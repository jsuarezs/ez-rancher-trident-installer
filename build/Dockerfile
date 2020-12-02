FROM quay.io/operator-framework/ansible-operator:v0.18.1

ENV KUBECTL_VERSION=v1.18.3
ENV TRIDENTCTL_VERSION=20.10.0

USER root

RUN yum install -y curl && \
  curl -Lo /bin/kubectl https://storage.googleapis.com/kubernetes-release/release/${KUBECTL_VERSION}/bin/linux/amd64/kubectl && \
  chmod +x /bin/kubectl

RUN curl -LO https://github.com/NetApp/trident/releases/download/v${TRIDENTCTL_VERSION}/trident-installer-${TRIDENTCTL_VERSION}.tar.gz && \
    tar -xvzf trident-installer-${TRIDENTCTL_VERSION}.tar.gz && \
    rm trident-installer-${TRIDENTCTL_VERSION}.tar.gz && \
    mv /trident-installer/tridentctl /bin/tridentctl && \
    chmod +x /bin/tridentctl

USER ansible-operator
COPY requirements.yml ${HOME}/requirements.yml
RUN ansible-galaxy collection install -r ${HOME}/requirements.yml \
 && chmod -R ug+rwx ${HOME}/.ansible

COPY watches.yaml ${HOME}/watches.yaml

COPY roles/ ${HOME}/roles/
