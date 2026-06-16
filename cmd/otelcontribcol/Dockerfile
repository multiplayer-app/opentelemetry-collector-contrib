FROM alpine:latest@sha256:28bd5fe8b56d1bd048e5babf5b10710ebe0bae67db86916198a6eec434943f8b AS prep
RUN apk --update add ca-certificates busybox-static curl
RUN mkdir -p /staging/usr/bin && \
    cp /usr/bin/curl /staging/usr/bin/curl && \
    ldd /usr/bin/curl | grep -oE '(/[^ ]+\.so[^ ]*)' | sort -u | while read lib; do \
        mkdir -p "/staging$(dirname "$lib")"; \
        cp "$lib" "/staging$lib"; \
    done

FROM scratch

ARG USER_UID=10001
ARG USER_GID=10001
USER ${USER_UID}:${USER_GID}

COPY --from=prep /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/ca-certificates.crt
COPY --from=prep /bin/busybox.static /bin/sh
COPY --from=prep /staging/ /
COPY otelcontribcol /
EXPOSE 4317 55680 55679
ENTRYPOINT ["/otelcontribcol"]
CMD ["--config", "/etc/otel/config.yaml"]
