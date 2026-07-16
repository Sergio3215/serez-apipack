# serez-apipack

Deploy tool for [Serez-Code](https://serezcode.org) APIs: packages a
[serez-http](https://serezcode.org/docs/serez-http) project into a **self-contained Docker
image** — the host machine only needs Docker, not Serez-Code. Written in pure `.sz`.

## Install & use

```powershell
sz install serez-apipack
```

From the root of your serez-http project (where `serez.json` lives):

```sh
# Build a Docker image tagged name:version (read from serez.json)
sz run apipack

# Override port or tag (options are key=value, no dashes)
sz run apipack port=8080 tag=my-api:latest
```

Then run it anywhere with Docker:

```sh
docker run -p 3000:3000 my-api:1.0.0
```

## Options

| Option     | Default        | Description |
|------------|----------------|-------------|
| `project=` | `.`            | Path to the project directory (where `serez.json` is) |
| `port=`    | `3000`         | Port exposed by the container |
| `tag=`     | `name:version` | Docker image tag (from `serez.json` if omitted) |

## How it works

serez-apipack reads your `serez.json` (`name`, `version`, `main`), generates a `Dockerfile` in
the project directory and runs `docker build`. The image is a **multi-stage build**: stage 1
compiles `sz` from source with Rust; stage 2 is a lean Ubuntu with just the `sz` binary, your
project and its dependencies (`sz install` runs inside the image).

Serez-Code binds its server to `127.0.0.1`, so the image includes **socat** listening on
`0.0.0.0` and forwarding traffic to the app — Docker port mapping works without touching the
language core.

serez-apipack is a **deploy-time tool only** — you never import it in your app code. For local
development just run `sz index.sz` directly.

## Documentation

- **[serez-apipack reference](https://serezcode.org/docs/serez-apipack)** — options, the
  generated Dockerfile and `serez.json` fields, on the Serez-Code site.
- **[Build a REST API](https://serezcode.org/guides/rest-api)** — full tutorial: develop with
  serez-http, deploy with serez-apipack.
