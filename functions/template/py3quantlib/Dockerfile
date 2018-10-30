FROM lballabio/quantlib-python3:1.14-bionic

# Allows you to add additional packages via build-arg
ARG ADDITIONAL_PACKAGE

# Alternatively use ADD https:// (which will not be cached by Docker builder)
RUN apt-get update \
 && DEBIAN_FRONTEND=noninteractive apt-get install -y curl python3-pip ${ADDITIONAL_PACKAGE} \
 && echo "Pulling watchdog binary from Github." \
 && curl -sSL https://github.com/openfaas/faas/releases/download/0.9.6/fwatchdog > /usr/bin/fwatchdog \
 && chmod +x /usr/bin/fwatchdog \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*


# Add non root user
RUN groupadd -r app && useradd -r -g app app

WORKDIR /home/app/

COPY index.py           .
COPY requirements.txt   .

RUN chown -R app /home/app

USER app
ENV PATH=$PATH:/home/app/.local/bin
RUN pip3 install --user -r requirements.txt

RUN mkdir -p function
RUN touch ./function/__init__.py

WORKDIR /home/app/function/
COPY function/requirements.txt	.
RUN pip3 install --user -r requirements.txt

WORKDIR /home/app/

USER root
COPY function           function
RUN chown -R app:app ./
USER app

ENV fprocess="python3 index.py"
EXPOSE 8080

HEALTHCHECK --interval=3s CMD [ -e /tmp/.lock ] || exit 1

CMD ["fwatchdog"]
