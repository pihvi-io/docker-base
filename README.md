# Docker base images

This repository includes base images which contain small fixes we want to use in our docker containers.

## Debian image

```bash
$ docker pull pihvio/base:debian
```

### Features
* Doesn't send the audit signals from `su` ( Fixes: https://github.com/moby/moby/issues/5899 )

## Maintainers
[Onni Hakala](https://github.com/onnimonni)

## License
MIT
