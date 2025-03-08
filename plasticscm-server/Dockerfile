# This installs plasticscm-server-netcore manually and not through the package manager.
# This allows us to run 'plasticd' manually and not through systemd as this is not available inside Docker.

FROM ubuntu:latest
LABEL maintainer="matthew@sygan.gd"

ARG DEBIAN_FRONTEND=noninteractive

# Update package list and install required tools
RUN apt-get update && apt-get install -y wget gnupg2 vim apt-transport-https \
    # Install all dependecies
    libc6 libgcc1 libgssapi-krb5-2 libstdc++6 zlib1g sudo liblttng-ust1 libssl3 libkrb5-3 libicu70 && \
    # Clean up temporary files
    rm -rf /var/log/* && \
    rm -rf /var/lib/apt/lists/*

ARG VERSION

# Register the plastic scm repositiory
RUN wget -qO - "https://www.plasticscm.com/plasticrepo/stable/ubuntu/Release.key" | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/plastic.gpg
RUN echo "deb https://www.plasticscm.com/plasticrepo/stable/ubuntu/ ./" > /etc/apt/sources.list.d/plasticscm-stable.list
RUN sudo apt-get update

# Install plastic scm server manually
RUN wget -qO "/plasticscm.tar.gz" "https://s3.eu-west-2.amazonaws.com/plastic-releases/releases/$VERSION/plasticscm/linux/PlasticSCM-$VERSION-linux-x64-server.tar.gz"
RUN mkdir -p "/opt/plasticscm5/"
RUN tar -xf "/plasticscm.tar.gz" -C "/opt/plasticscm5/"
RUN chmod u+x "/opt/plasticscm5/server/plasticd"

# Clean up temporary files
RUN rm "/plasticscm.tar.gz"
RUN rm -rf /var/log/*
RUN rm -rf /var/lib/apt/lists/*

# TCP port
EXPOSE 8087
# SSL port
EXPOSE 8088
# Webadmin TCP port (localhost only)
EXPOSE 7178
# Webadmin SSL port
EXPOSE 7179

VOLUME /db/jet
VOLUME /logs
# Contains the configurations that will be linked to the server through plastic.sh
VOLUME /conf

COPY "./jet.conf" "/opt/plasticscm5/server/jet.conf"

COPY "./plastic.sh" "/plastic.sh"

ENTRYPOINT [ "/plastic.sh" ]