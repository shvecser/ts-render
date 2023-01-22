FROM alpine:latest as tailscale
WORKDIR /tailscale
COPY . ./
ENV TSFILE=tailscale_1.34.1_amd64.tgz
RUN wget https://pkgs.tailscale.com/stable/${TSFILE} && \
  tar xzf ${TSFILE} --strip-components=1
COPY . ./

FROM alpine:latest
RUN apk update && apk add ca-certificates && rm -rf /var/cache/apk/*
RUN mkdir -p /var/run/tailscale
RUN mkdir -p /var/cache/tailscale
RUN mkdir -p /var/lib/tailscale

FROM alpine:latest AS base
RUN apk add --no-cache --update nodejs-current tini
WORKDIR /ts-render


COPY --chown=root ./start.sh ./start.sh
COPY ./tailscale-proxy.mjs /ts-render/tailscale-proxy.mjs
COPY --from=tailscale /tailscale/tailscaled /ts-render/tailscaled
COPY --from=tailscale /tailscale/tailscale /ts-render/tailscale

RUN chmod +x start.sh

EXPOSE 32456

USER root
CMD ["/ts-render/start.sh"]
