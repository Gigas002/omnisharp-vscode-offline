# omnisharp-vscode-offline

[![Release](https://img.shields.io/github/release/Gigas002/omnisharp-vscode-offline.svg)](https://github.com/Gigas002/omnisharp-vscode-offline/releases/latest)

This repo contains releases of [omnisharp-vscode](https://github.com/OmniSharp/omnisharp-vscode) packages for offline installation. Download the latest release from releases page.

Built, using **WORKING** version of `Dockerfile` from this [issue](https://github.com/OmniSharp/omnisharp-vscode/issues/3954).

## How to build

Create `Dockerfile` with this content (*change all occurencies of 1.23.7 to your desired version*):

```dockerfile
FROM node:12.20.0-stretch as omnisharp-offline

RUN git clone --single-branch --branch v1.23.7 https://github.com/OmniSharp/omnisharp-vscode.git /app
WORKDIR /app

RUN npm i
RUN npm run compile
RUN npm run gulp vsix:offline:package

FROM alpine:latest
COPY --from=omnisharp-offline /app/csharp.1.23.7-linux-x86_64.vsix /tmp/csharp.1.23.7-linux-x86_64.vsix
COPY --from=omnisharp-offline /app/csharp.1.23.7-darwin-x86_64.vsix /tmp/csharp.1.23.7-darwin-x86_64.vsix
COPY --from=omnisharp-offline /app/csharp.1.23.7-win32-x86_64.vsix /tmp/csharp.1.23.7-win32-x86_64.vsix
```

Run docker:

```ps1
docker build .
docker images
docker run -it --rm -v D:/build/temp:/temp $image sh
```

Where `$image` is your built image's id. Then in interactive mode copy packages:

```sh
cp /tmp/* /temp
```

In the end, you'll have three ready packages in `D:/build/temp` directory.
