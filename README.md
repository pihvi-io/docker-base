# Docker base images

This repository includes base images which contain small fixes we want to use in our docker containers.

## Debian image

```bash
$ docker pull pihvio/base:debian
```

### Features
* [S6 overlay] for running multiple processes
* Automatically setups the timezone from $TZ env variable
* Doesn't send the audit signals from `su` ( Fixes: https://github.com/moby/moby/issues/5899 )

## Maintainers
[Onni Hakala](https://github.com/onnimonni)

## License
MIT
