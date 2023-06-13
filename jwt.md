# JWT

## Validation

- sign => ES256, also faster than RS256
- alg field is whitelisted
- encrypt if necessary
- iss and aud fields are validated to be acceptable
- nbf (not-before) field

### Test Tokens

```
import { generateKeyPairSync } from 'crypto';
import { sign, SignOptions } from 'jsonwebtoken';

const generateKeyPair = () =>
    generateKeyPairSync('rsa', {
        modulusLength: 4096,
        privateKeyEncoding: {
            format: 'pem',
            type: 'pkcs8',
        },
        publicKeyEncoding: {
            format: 'pem',
            type: 'spki',
        },
    });

const { privateKey, publicKey } = generateKeyPair();

const generateTestToken = (claims = {}, options?: SignOptions) => {
    const signOptions: SignOptions = { ...options };

    signOptions.algorithm = 'RS256';

    if (!('keyid' in signOptions)) {
        signOptions.keyid = 'test-key-id';
    }

    if (!(claims as any).exp) {
        signOptions.expiresIn = signOptions.expiresIn || '1h';
    }

    signOptions.issuer = signOptions.issuer || 'test-pool';

    return sign(
        {
            ...claims,
        },
        privateKey,
        signOptions
    );
};

export { generateTestToken, publicKey };

```
