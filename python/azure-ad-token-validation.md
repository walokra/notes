## Azure AD JWT Token Validation

dependencies:

```
werkzeug = "==2.2.3"
requests = "==2.29.0"
pyjwt = "==2.6.0"
```

```python
import logging

from flask import views, request, current_app
from werkzeug.exceptions import InternalServerError, Forbidden, BadRequest, Unauthorized
import requests

import jwt
import json
from datetime import datetime

log = logging.getLogger(__name__)

def validate_and_decore_azure_ad_token(token):
    # Get token header
    token_header = jwt.get_unverified_header(token)

    client_id = "AZURE_CLIENT_ID"
    log.debug("client_id: %s" % client_id)

    if not client_id:
        log.error("Missing Azure AD configuration!")
        raise InternalServerError

    jwks = None
    try:
        # Retrieve the JWK from Azure AD, multi tenant
        jwks_url = "https://login.microsoftonline.com/common/discovery/keys"
        # OR if single tenant
        #jwks_url =  'https://login.microsoftonline.com/%s/discovery/v2.0/keys' % tenant_id
        jwks_response = requests.get(jwks_url)
        jwks = jwks_response.json()
    except Exception as e:
        log.error("Fetching JWKS from Azure failed! %s" % str(e))
        raise InternalServerError

    # Retrieve the public key from the JWK
    token_kid = token_header["kid"]
    jwk = None
    for key in jwks["keys"]:
        if key["kid"] == token_kid:
            jwk = key
            break

    if jwk is None:
        raise ValueError("Matching JWK not found in JWKS")

    # JWT token to validate and decode
    try:
        from cryptography.hazmat.primitives import serialization

        rsa_pem_key = jwt.algorithms.RSAAlgorithm.from_jwk(json.dumps(jwk))
        rsa_pem_key_bytes = rsa_pem_key.public_bytes(
            encoding=serialization.Encoding.PEM,
            format=serialization.PublicFormat.SubjectPublicKeyInfo,
        )

        # Validate and decoce token
        decoded_token = jwt.decode(
            token,
            key=rsa_pem_key_bytes,
            verify=True,
            algorithms=[token_header["alg"]],
            audience=[client_id],
        )

        # Optional - Validate issuer
        # issuer = 'https://login.microsoftonline.com/%s/v2.0' % tenant_id
        # decoded_token = jwt.decode(azure_ad_token, key=rsa_pem_key_bytes, verify=True, algorithms=[token_header['alg']], audience=[client_id], issuer=issuer)

        # Just decode without validation
        # decoded_token = jwt.decode(token, algorithms=[token_header['alg']], options={"verify_signature": False})
        # token_formatted = json.dumps(decoded_token, indent=2)
        # log.debug('%s' % token_formatted)
        email = None
        if "preferred_username" in decoded_token:
            email = decoded_token["preferred_username"]
        if "upn" in decoded_token:
            email = decoded_token["upn"]
        if "email" in decoded_token:
            email = decoded_token["email"]

        log.debug("uid: %s" % email)

        if email is None:
            log.error("email not found in token!")
            raise InternalServerError

        # JWK is valid and token is decoded successfully
        log.debug("%s" % decoded_token)
    except jwt.exceptions.PyJWTError as e:
        # JWK validation failed or token decoding failed
        log.error("JWK validation failed or token decoding failed: %s" % str(e))
        raise BadRequest
    except jwt.exceptions.InvalidTokenError:
        log.error("Invalid JWT: %s" % str(e))
        raise BadRequest

    # Token Expiry check
    tokenExpiry = datetime.utcfromtimestamp(decoded_token["exp"])
    now = datetime.utcnow()
    time_to_expiry = tokenExpiry - now

    if time_to_expiry.seconds < 0:
        log.error("Access Token Expired!")
        raise BadRequest

    minutesToExpiry = time_to_expiry.seconds / 60
    log.debug("Access Token Expires in: %s minutes" % str(minutesToExpiry))

    return decoded_token
```
