FROM gitlab/gitlab-runner:ubuntu-v14.7.0

# Grundlegende Dinge
RUN apt-get update && apt-get install -yq \
	software-properties-common \
	rsync

# PHP Repo
RUN add-apt-repository ppa:ondrej/php -y

# Yarn
RUN curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list

# NodeJS
RUN curl -sL https://deb.nodesource.com/setup_12.x | bash -

# Weitere Module installieren
RUN apt-get update && apt-get install -yq \
	rsync \
	nodejs \
	yarn \
	zip \
	unzip \
	php8.0-cli \
	php8.0-zip

# Composer für PHP-Projekte
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
	&& php composer-setup.php \
	&& php -r "unlink('composer-setup.php');" \
	&& mv composer.phar /usr/local/bin/composer

# Docker, um Images zu erstellen
RUN apt-get update && apt-get install -yq apt-transport-https ca-certificates curl gnupg lsb-release
RUN curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
RUN echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
RUN apt-get update && apt-get install -yq docker-ce docker-ce-cli containerd.io -y

# Runner user hinzufügen
RUN usermod -aG docker gitlab-runner

# SSH Keys hinzufügen
COPY keys/id_rsa /home/gitlab-runner/.ssh/id_rsa
COPY keys/id_rsa.pub /home/gitlab-runner/.ssh/id_rsa.pub

# Docker muss einen anderen Storage-Driver benutzen, sonst geht es nicht
COPY daemon.json /etc/docker/daemon.json

# Eigenen Entrypoint erstellen
COPY entrypoint /
