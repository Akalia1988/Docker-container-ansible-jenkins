# https://blog.docker.com/2019/07/intro-guide-to-dockerfile-best-practices/

ARG PYTHON_VERSION=3.7.4-slim
FROM python:${PYTHON_VERSION}
LABEL python-version=${PYTHON_VERSION}

# Install git for ansible-galaxy role installs from git repositories
RUN apt update && apt install --no-install-recommends -y git && rm -rf /var/lib/apt/lists*

# Create Non-Root User/Group for Process
# https://medium.com/@mccode/processes-in-containers-should-not-run-as-root-2feae3f0df3b

ARG USER=ansible
ARG UID=1000
ARG GID=1000
RUN addgroup --system --gid ${GID} ${USER} && adduser --system --uid ${UID} --gid ${GID} ${USER}

# Install Ansible (via python package manager)
ARG ANSIBLE_VERSION=2.8.5
RUN pip install --no-cache-dir ansible==${ANSIBLE_VERSION}
LABEL ansible-version=${ANSIBLE_VERSION}

# Change to Non-Root $user
USER ${USER}
WORKDIR /home/${USER}

# Copy Target
COPY target/ /home/${USER}/

ENV ANSIBLE_VAULT_PASSWORD_FILE=/home/${USER}/vault-env

# Install PIP dependencies (non-root) for base image
RUN pip install --user --no-cache-dir -r requirements.txt && rm requirements.txt

# Copy Repository into Container (ON BUILD)
ONBUILD COPY --chown=ansible:ansible . .

# Install Ansible Role/PIP Dependencies (On BUILD) for specific repository
ONBUILD RUN echo "Installing Dependencies" &&\
			if test -f "requirements.txt" ; \
				then pip install --user --no-cache-dir -r requirements.txt ; \
			fi

CMD [ "/bin/bash" ]
