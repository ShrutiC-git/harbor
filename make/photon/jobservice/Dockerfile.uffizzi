ARG harbor_base_image_version
ARG harbor_base_namespace
FROM ${harbor_base_namespace}/harbor-jobservice-base:${harbor_base_image_version}

COPY ./make/photon/common/install_cert.sh /harbor/
COPY ./make/photon/jobservice/entrypoint.sh /harbor/
# COPY ./make/photon/jobservice/harbor_jobservice /harbor/


RUN chown -R harbor:harbor /etc/pki/tls/certs \
    && chown -R harbor:harbor /harbor/ \
    && chmod u+x /harbor/entrypoint.sh \
    && chmod u+x /harbor/install_cert.sh
    # && chmod u+x /harbor/harbor_jobservice

WORKDIR /harbor/

USER harbor

VOLUME ["/var/log/jobs/", "/var/scandata_exports"]

# HEALTHCHECK CMD curl --fail -s http://localhost:8083/api/v1/stats || curl -sk --fail --key /etc/harbor/ssl/job_service.key --cert /etc/harbor/ssl/job_service.crt https://localhost:8443/api/v1/stats || exit 1

# ENTRYPOINT ["/harbor/entrypoint.sh"]

FROM golang:1.19-alpine
RUN apk add build-base
RUN mkdir /home/harbor
WORKDIR /home/harbor
COPY ../../../config/jobservice/config.yml .
COPY ../../../src/jobservice/main.go .
RUN go env -w GO111MODULE=on
RUN go mod init jobservice
RUN go mod tidy -compat=1.18
RUN chmod +x .
RUN go build -o main .
CMD ["./main","-c", "config.yml"]
