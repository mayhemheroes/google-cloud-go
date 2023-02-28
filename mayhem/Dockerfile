FROM golang:1.17.13 as gofuzzbuilder
ARG project='google-cloud-go'
ARG fuzzTargetPath='spanner/spansql'

ADD . ${project}
WORKDIR ${project}/${fuzzTargetPath}
RUN go get -u github.com/dvyukov/go-fuzz/go-fuzz@latest github.com/dvyukov/go-fuzz/go-fuzz-build@latest
RUN GO_ENABLED=0 go-fuzz-build -libfuzzer -o /parsequery-fuzz.a .

FROM silkeh/clang:latest as sanitizer
COPY --from=gofuzzbuilder /parsequery-fuzz.a parsequery-fuzz.a
RUN clang -fsanitize=fuzzer /parsequery-fuzz.a -o /parsequery-fuzz.libfuzzer

FROM ubuntu:20.04
COPY --from=sanitizer /parsequery-fuzz.libfuzzer /
