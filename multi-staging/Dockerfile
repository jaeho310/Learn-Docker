ARG BUILD_IMAGE=golang:alpine3.10
ARG BASE_IMAGE=alpine:3.10

FROM ${BUILD_IMAGE} AS build

ADD ./main.go /temp/

WORKDIR /temp

RUN go build -o myApp main.go

FROM ${BASE_IMAGE}

COPY --from=build /temp /temp

WORKDIR /temp

CMD "./myApp"