# ---------------------------------------------
# Overleaf Community Edition (overleaf/overleaf)
# zevin version
# ---------------------------------------------

FROM sharelatex/sharelatex-base:latest

ENV baseDir .


# Install app settings files
# --------------------------
ADD ${baseDir}/settings.coffee /etc/sharelatex/settings.coffee
ENV SHARELATEX_CONFIG /etc/sharelatex/settings.coffee


# Checkout Overleaf Community Edition repo
# ----------------------------------------
RUN git clone https://github.com/kingzevin/overleaf.git \
	--depth 1 /var/www/sharelatex


# Install dependencies needed to run configuration scripts
# --------------------------------------------------------
ADD ${baseDir}/package.json /var/www/package.json
ADD ${baseDir}/git-revision.js /var/www/git-revision.js
RUN cd /var/www && npm install


# Replace overleaf/config/services.js with the list of available 
# services in Overleaf Community Edition
# --------------------------------------------------------------
ADD ${baseDir}/services.js /var/www/sharelatex/config/services.js

# zevin: copy services

ADD ${baseDir}/real-time /var/www/sharelatex/real-time
ADD ${baseDir}/document-updater /var/www/sharelatex/document-updater
ADD ${baseDir}/filestore /var/www/sharelatex/filestore
ADD ${baseDir}/track-changes /var/www/sharelatex/track-changes
ADD ${baseDir}/web /var/www/sharelatex/web
ADD ${baseDir}/docstore /var/www/sharelatex/docstore
ADD ${baseDir}/chat /var/www/sharelatex/chat
ADD ${baseDir}/tags /var/www/sharelatex/tags
ADD ${baseDir}/spelling /var/www/sharelatex/spelling
ADD ${baseDir}/contacts /var/www/sharelatex/contacts
ADD ${baseDir}/notifications /var/www/sharelatex/notifications
# ADD ${baseDir}/clsi /var/www/sharelatex/clsi


# Checkout services
# -----------------
RUN cd /var/www/sharelatex && \
	npm install && grunt install;


# install and compile services
# ----------------------------
RUN bash -c 'cd /var/www/sharelatex && source ./bin/install-services'
RUN bash -c 'cd /var/www/sharelatex && source ./bin/compile-services'


# Links CLSI sycntex to its default location
# ------------------------------------------
RUN ln -s /var/www/sharelatex/clsi/bin/synctex /opt/synctex


# Change application ownership to www-data
# ----------------------------------------
RUN	chown -R www-data:www-data /var/www/sharelatex;


# Copy runit service startup scripts to its location
# --------------------------------------------------
ADD ${baseDir}/runit /etc/service


# Configure nginx
# ---------------
RUN rm /etc/nginx/sites-enabled/default
ADD ${baseDir}/nginx/nginx.conf /etc/nginx/nginx.conf
ADD ${baseDir}/nginx/sharelatex.conf /etc/nginx/sites-enabled/sharelatex.conf


# Configure log rotation
# ----------------------
ADD ${baseDir}/logrotate/sharelatex /etc/logrotate.d/sharelatex


# Copy Phusion Image startup scripts to its location
# --------------------------------------------------
COPY ${baseDir}/init_scripts/ /etc/my_init.d/


# Stores the version installed for each service
# ---------------------------------------------
RUN cd /var/www && node git-revision > revisions.txt


EXPOSE 80

WORKDIR /

ENTRYPOINT ["/sbin/my_init"]

