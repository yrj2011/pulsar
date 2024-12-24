FROM pgvector/pgvector:pg17 AS build

RUN apt-get update \
    && apt-get install -y --no-install-recommends --no-install-suggests \
       bison \
       build-essential \
       flex \
       postgresql-server-dev-17

COPY . /age

WORKDIR /age

RUN make && make install


# Final stage: Create a final image by copying the files created in the build stage
FROM pgvector/pgvector:pg17

RUN apt-get update \
    && apt-get install -y --no-install-recommends --no-install-suggests \
    locales

RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen \
    && locale-gen \
    && update-locale LANG=en_US.UTF-8

ENV LANG=en_US.UTF-8
ENV LC_COLLATE=en_US.UTF-8
ENV LC_CTYPE=en_US.UTF-8

COPY --from=build /usr/lib/postgresql/17/lib/age.so /usr/lib/postgresql/16/lib/
COPY --from=build /usr/share/postgresql/17/extension/age--1.5.0.sql /usr/share/postgresql/16/extension/
COPY --from=build /usr/share/postgresql/17/extension/age.control /usr/share/postgresql/16/extension/
COPY docker/docker-entrypoint-initdb.d/00-create-extension-age.sql /docker-entrypoint-initdb.d/00-create-extension-age.sql

CMD ["postgres", "-c", "shared_preload_libraries=age"]
