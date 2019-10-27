FROM debian:stretch-slim
MAINTAINER Odoo S.A. <info@odoo.com>

# Generate locale C.UTF-8 for postgres and general locale data
ENV LANG C.UTF-8

# Use backports to avoid install some libs with pip
RUN echo 'deb http://deb.debian.org/debian stretch-backports main' > /etc/apt/sources.list.d/backports.list

# Install some deps, lessc and less-plugin-clean-css, and wkhtmltopdf
RUN set -x; \
        apt-get update \
        && apt-get install -y --no-install-recommends \
            ca-certificates \
            curl \
            dirmngr \
            fonts-noto-cjk \
            gnupg \
            libssl1.0-dev \
            node-less \
            python3-num2words \
            python3-pip \
            python3-phonenumbers \
            python3-pyldap \
            python3-qrcode \
            python3-renderpm \
            python3-setuptools \
            python3-vobject \
            python3-watchdog \
            python3-xlwt \
            xz-utils \
        && curl -o wkhtmltox.deb -sSL https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.5/wkhtmltox_0.12.5-1.stretch_amd64.deb \
        && echo '7e35a63f9db14f93ec7feeb0fce76b30c08f2057 wkhtmltox.deb' | sha1sum -c - \
        && apt-get install -y --no-install-recommends ./wkhtmltox.deb \
        && rm -rf /var/lib/apt/lists/* wkhtmltox.deb

# install latest postgresql-client
RUN set -x; \
        echo 'deb http://apt.postgresql.org/pub/repos/apt/ stretch-pgdg main' > etc/apt/sources.list.d/pgdg.list \
        && export GNUPGHOME="$(mktemp -d)" \
        && repokey='B97B0AFCAA1A47F044F244A07FCC7D46ACCC4CF8' \
        && gpg --batch --keyserver keyserver.ubuntu.com --recv-keys "${repokey}" \
        && gpg --batch --armor --export "${repokey}" > /etc/apt/trusted.gpg.d/pgdg.gpg.asc \
        && gpgconf --kill all \
        && rm -rf "$GNUPGHOME" \
        && apt-get update  \
        && apt-get install -y postgresql-client \
        && rm -rf /var/lib/apt/lists/*

# Install rtlcss (on Debian stretch)
RUN set -x;\
    echo "deb http://deb.nodesource.com/node_8.x stretch main" > /etc/apt/sources.list.d/nodesource.list \
    && export GNUPGHOME="$(mktemp -d)" \
    && repokey='9FD3B784BC1C6FC31A8A0A1C1655A0AB68576280' \
    && gpg --batch --keyserver keyserver.ubuntu.com --recv-keys "${repokey}" \
    && gpg --batch --armor --export "${repokey}" > /etc/apt/trusted.gpg.d/nodejs.gpg.asc \
    && gpgconf --kill all \
    && rm -rf "$GNUPGHOME" \
    && apt-get update \
    && apt-get install -y nodejs \
    && npm install -g rtlcss \
    && rm -rf /var/lib/apt/lists/*

# Install Odoo
ENV ODOO_VERSION 12.0
ARG ODOO_RELEASE=20191022
ARG ODOO_SHA=31cdde4a9f6cd9d2cbf6af45a5d301e0a0907148
RUN set -x; \
        curl -o odoo.deb -sSL http://nightly.odoo.com/${ODOO_VERSION}/nightly/deb/odoo_${ODOO_VERSION}.${ODOO_RELEASE}_all.deb \
        && echo "${ODOO_SHA} odoo.deb" | sha1sum -c - \
        && dpkg --force-depends -i odoo.deb \
        && apt-get update \
        && apt-get -y install -f --no-install-recommends \
        && rm -rf /var/lib/apt/lists/* odoo.deb

# Copy entrypoint script and Odoo configuration file
COPY ./entrypoint.sh /
COPY ./odoo.conf /etc/odoo/
RUN chown odoo /etc/odoo/odoo.conf

# Mount /var/lib/odoo to allow restoring filestore and /mnt/extra-addons for users addons
RUN mkdir -p /mnt/extra-addons \
        && chown -R odoo /mnt/extra-addons
        && curl -o account-analytic.zip  -SL https://github.com/OCA/account-analytic/archive/12.0.zip \
        && unzip /mnt/extra-addons/account-analytic.zip
        && curl -o account-closing.zip  -SL https://github.com/OCA/account-closing/archive/12.0.zip \
&& unzip /mnt/extra-addons/account-closing.zip
&& curl -o account-financial-reporting.zip  -SL https://github.com/OCA/account-financial-reporting/archive/12.0.zip \
&& unzip /mnt/extra-addons/account-financial-reporting.zip
&& curl -o account-financial-tools.zip  -SL https://github.com/OCA/account-financial-tools/archive/12.0.zip \
&& unzip /mnt/extra-addons/account-financial-tools.zip
&& curl -o account-invoicing.zip  -SL https://github.com/OCA/account-invoicing/archive/12.0.zip \
&& unzip /mnt/extra-addons/account-invoicing.zip
&& curl -o account-payment.zip  -SL https://github.com/OCA/account-payment/archive/12.0.zip \
&& unzip /mnt/extra-addons/account-payment.zip
&& curl -o bank-payment.zip  -SL https://github.com/OCA/bank-payment/archive/12.0.zip \
&& unzip /mnt/extra-addons/bank-payment.zip
&& curl -o bank-statement-import.zip  -SL https://github.com/OCA/bank-statement-import/archive/12.0.zip \
&& unzip /mnt/extra-addons/bank-statement-import.zip
&& curl -o commission.zip  -SL https://github.com/OCA/commission/archive/12.0.zip \
&& unzip /mnt/extra-addons/commission.zip
&& curl -o community-data-files.zip  -SL https://github.com/OCA/community-data-files/archive/12.0.zip \
&& unzip /mnt/extra-addons/community-data-files.zip
&& curl -o contract.zip  -SL https://github.com/OCA/contract/archive/12.0.zip \
&& unzip /mnt/extra-addons/contract.zip
&& curl -o crm.zip  -SL https://github.com/OCA/crm/archive/12.0.zip \
&& unzip /mnt/extra-addons/crm.zip
&& curl -o event.zip  -SL https://github.com/OCA/event/archive/12.0.zip \
&& unzip /mnt/extra-addons/event.zip
&& curl -o hr.zip  -SL https://github.com/OCA/hr/archive/12.0.zip \
&& unzip /mnt/extra-addons/hr.zip
&& curl -o timesheet.zip  -SL https://github.com/OCA/timesheet/archive/12.0.zip \
&& unzip /mnt/extra-addons/timesheet.zip
&& curl -o knowledge.zip  -SL https://github.com/OCA/knowledge/archive/12.0.zip \
&& unzip /mnt/extra-addons/knowledge.zip
&& curl -o l10n-spain.zip  -SL https://github.com/OCA/l10n-spain/archive/12.0.zip \
&& unzip /mnt/extra-addons/l10n-spain.zip
&& curl -o manufacture.zip  -SL https://github.com/OCA/manufacture/archive/12.0.zip \
&& unzip /mnt/extra-addons/manufacture.zip
&& curl -o mis-builder.zip  -SL https://github.com/OCA/mis-builder/archive/12.0.zip \
&& unzip /mnt/extra-addons/mis-builder.zip
&& curl -o partner-contact.zip  -SL https://github.com/OCA/partner-contact/archive/12.0.zip \
&& unzip /mnt/extra-addons/partner-contact.zip
&& curl -o pos.zip  -SL https://github.com/OCA/pos/archive/12.0.zip \
&& unzip /mnt/extra-addons/pos.zip
&& curl -o product-attribute.zip  -SL https://github.com/OCA/product-attribute/archive/12.0.zip \
&& unzip /mnt/extra-addons/product-attribute.zip
&& curl -o product-variant.zip  -SL https://github.com/OCA/product-variant/archive/12.0.zip \
&& unzip /mnt/extra-addons/product-variant.zip
&& curl -o project.zip  -SL https://github.com/OCA/project/archive/12.0.zip \
&& unzip /mnt/extra-addons/project.zip
&& curl -o purchase-workflow.zip  -SL https://github.com/OCA/purchase-workflow/archive/12.0.zip \
&& unzip /mnt/extra-addons/purchase-workflow.zip
&& curl -o queue.zip  -SL https://github.com/OCA/queue/archive/12.0.zip \
&& unzip /mnt/extra-addons/queue.zip
&& curl -o reporting-engine.zip  -SL https://github.com/OCA/reporting-engine/archive/12.0.zip \
&& unzip /mnt/extra-addons/reporting-engine.zip
&& curl -o sale-workflow.zip  -SL https://github.com/OCA/sale-workflow/archive/12.0.zip \
&& unzip /mnt/extra-addons/sale-workflow.zip
&& curl -o server-brand.zip  -SL https://github.com/OCA/server-brand/archive/12.0.zip \
&& unzip /mnt/extra-addons/server-brand.zip
&& curl -o server-tools.zip  -SL https://github.com/OCA/server-tools/archive/12.0.zip \
&& unzip /mnt/extra-addons/server-tools.zip
&& curl -o server-ux.zip  -SL https://github.com/OCA/server-ux/archive/12.0.zip \
&& unzip /mnt/extra-addons/server-ux.zip
&& curl -o social  -SL https://github.com/OCA/social/archive/12.0.zip \
&& unzip /mnt/extra-addons/social
&& curl -o stock-logistics-warehouse.zip  -SL https://github.com/OCA/stock-logistics-warehouse/archive/12.0.zip \
&& unzip /mnt/extra-addons/stock-logistics-warehouse.zip
&& curl -o stock-logistics-workflow.zip  -SL https://github.com/OCA/stock-logistics-workflow/archive/12.0.zip \
&& unzip /mnt/extra-addons/stock-logistics-workflow.zip
&& curl -o web.zip  -SL https://github.com/OCA/web/archive/12.0.zip \
&& unzip /mnt/extra-addons/web.zip
&& curl -o website.zip  -SL https://github.com/OCA/website/archive/12.0.zip \
&& unzip /mnt/extra-addons/website.zip

VOLUME ["/var/lib/odoo", "/mnt/extra-addons"]

# Expose Odoo services
EXPOSE 8069 8071

# Set the default config file
ENV ODOO_RC /etc/odoo/odoo.conf

COPY wait-for-psql.py /usr/local/bin/wait-for-psql.py

# Set default user when running the container
USER odoo

ENTRYPOINT ["/entrypoint.sh"]
CMD ["odoo"]
