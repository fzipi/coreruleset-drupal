FROM debian:stable-slim as source

ARG CRS_VERSION

RUN set -eux; \
  \
  apt-get update; \
  apt-get install -y --no-install-recommends \
  ca-certificates \
  git; \
  mkdir /opt/drupal; \
  git clone https://git.drupalcode.org/project/drupal.git --branch ${CRS_VERSION} --depth 1 /opt/owasp-crs

FROM owasp/modsecurity-crs:apache

# Update just in case
COPY --from=source /opt/owasp-crs /opt/owasp-crs

COPY ./crs-setup.conf /opt/owasp-crs/crs-setup.conf

