Phala deploy dockerfiles
====

This repo contains dockerfiles for deployment

## Usage

### PhalaNode

#### Build

`docker build --build-arg PHALA_GIT_TAG=master -f node.Dockerfile -t phala-node:prod .`

> `--no-cache` for a clean build

#### Run

`docker run -ti --name phala-node -d -e NODE_NAME=my-phala-node -p 9615:9615 -p 9933:9933 -p 9944:9944 -p 30333:30333 -v $(pwd)/data:/root/data phala-node:prod`

### PRuntime

#### Build

`docker build --build-arg PHALA_GIT_TAG=master --build-arg SGX_MODE=HW --build-arg IAS_SPID=SPID_FROM_INTEL_PORTAL --build-arg IAS_API_KEY=API_KEY_FROM_INTEL_PORTAL --build-arg IAS_ENV=DEV_OR_PROD --build-arg SGX_SIGN_KEY_URL=URL_TO_FETCH_SIGN_KEY --build-arg SGX_ENCLAVE_CONFIG_URL=URL_TO_FETCH_ENCLAVE_CONFIG -f pruntime.Dockerfile -t phala-pruntime:prod .`

> For security reason, non-official build can not be registered to Phala chain network.

#### Run

`docker run -ti --name phala-pruntime -d -p 8000:8000 -v $(pwd)/data:/root/data phala-pruntime:prod`

### PHost

#### Build

`docker build --build-arg PHALA_GIT_TAG=master -f phost.Dockerfile -t phala-phost:prod .`

#### Run

`docker run -ti --name phala-phost -d -e PRUNTIME_ENDPOINT="http://YOUR_IP:8000" -e PHALA_NODE_WS_ENDPOINT="ws://YOUR_IP:9944" -e MNEMONIC="YOUR_MNEMONIC" -e EXTRA_OPTS="-r" phala-phost:prod`

Note:

Remember start PHost after PhalaNode and Phost started to accept connections.

About `EXTRA_OPTS`:

- `-r` means enable remote attestation flow, this must be set if you wanna join the public chain network
- `--heartbeat-interval 5` to control how many blocks that PHost send a heartbeat to the chain, default is `5`
- `--fetch-heartbeat-from-buffer` is an advance control that PHost won't send heartbeat unless PRuntime generated one (`POST /ping` to PRuntime)

## Cheatsheets

### Start & stop a container

Start

`docker start phala-node`

Safe stop

`docker stop phala-node`

Force stop

`docker kill phala-node`

### Remove a container

`docker rm phala-node`

### Show outputs

`docker attach --sig-proxy=false phala-node`

### Run shell

`docker exec -it phala-node bash`

### Clean up

`docker image prune`

## Build, Push, Pull to GitHub

`docker build --build-arg PHALA_GIT_TAG=master -f node.Dockerfile -t docker.pkg.github.com/phala-network/phala-docker/phala-node:VERSION .`

`docker push docker.pkg.github.com/phala-network/phala-docker/phala-node:VERSION`

`docker pull docker.pkg.github.com/phala-network/phala-docker/phala-node:TAG_NAME`

## References

- Proxy and other Systemd relates configurations <https://docs.docker.com/config/daemon/systemd/>
- Manage Docker as a non-root user (avoid `sudo`) <https://docs.docker.com/engine/install/linux-postinstall/>

## License

This project is licensed under the terms of the MIT license.