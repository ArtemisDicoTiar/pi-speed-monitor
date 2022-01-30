FROM --platform=linux/arm64/v8 ubuntu
ENV DEBIAN_FRONTEND noninteractive
RUN apt-get update && apt-get -qq -y install curl
#RUN DEBIAN_FRONTEND=noninteractive apt-get install -y build-essential libssl-dev libffi-dev python3-dev python3-pip libsasl2-dev libldap2-dev libpq-dev git cmake wget
RUN apt-get install -y build-essential libssl-dev lsb-release \
    libffi-dev python3-dev python3-pip libsasl2-dev libldap2-dev libpq-dev git cmake wget

RUN wget https://apache.jfrog.io/artifactory/arrow/$(lsb_release --id --short | tr 'A-Z' 'a-z')/apache-arrow-apt-source-latest-$(lsb_release --codename --short).deb
RUN apt-get install -y -V ./apache-arrow-apt-source-latest-$(lsb_release --codename --short).deb
#RUN wget https://apache.bintray.com/arrow/ubuntu/apache-arrow-archive-keyring-latest-focal.deb
#RUN apt-get install ./apache-arrow-archive-keyring-latest-focal.deb

RUN apt-get update
RUN apt-get install -y libarrow-dev=1.0.1-1 libarrow-python-dev=1.0.1-1
RUN pip3 install --upgrade setuptools pip
RUN ARROW_HOME=/usr PYARROW_CMAKE_OPTIONS="-DARROW_ARMV8_ARCH=armv8-a" pip3 install psycopg2-binary apache-superset==1.0.1
RUN superset db upgrade
ENV FLASK_APP=superset
RUN superset fab create-admin \
    --username admin \
    --firstname admin \
    --lastname admin \
    --email admin@admin.com \
    --password admin
RUN superset init
COPY dashboards /dashboards
ARG DB_STRING
RUN superset set-database-uri -d speed_test -u $DB_STRING
RUN superset import-dashboards -p /dashboards
CMD superset run -h 0.0.0.0 -p 8088 --with-threads --reload --debugger

