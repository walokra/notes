# PHP and Static Analysis

## PHPStan - PHP Static Analysis Tool

```shell
docker run -it --rm -v $(pwd):/project -w /project jakzal/phpqa:php7.4-alpine phpstan analyse --level 1 src
```

## PHPDepend

<http://pdepend.org/documentation/getting-started.html>

```shell
./vendor/bin/pdepend --summary-xml=./summary.xml --jdepend-chart=./jdepend.svg --overview-pyramid=./pyramid.svg ./src
```

## PHPMD: PHP Mess Detector

<https://phpmd.org/rules/index.html>
<https://hub.docker.com/r/jakzal/phpqa/>
<https://github.com/jakzal/toolbox>

Docker container

```shell
docker pull jakzal/phpqa
docker run -it --rm -v $(pwd):/project -w /project jakzal/phpqa \
    phpmd src html cleancode,codesize,controversial,design,naming,unusedcode --reportfile phpmd.html
```
