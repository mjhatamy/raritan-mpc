FROM x11vnc/desktop

LABEL "vendor"="Marands Inc"
LABEL version="1.0"
LABEL author="Jacob Hatami <mjhatamy@gmail.com>"


ENV USER_ID=1000 \
    GROUP_ID=1000 \
    APP_NAME="Multi-Platform Client 7.0.3.5.62" \
    WIDTH=1280 \
    HEIGHT=720 \
    EDGE=0

RUN apt-get update -y && apt-get upgrade -y && \
    apt-get install net-tools wget unzip -y

RUN cd /tmp && \
    wget https://github.com/jachinte/oracle-jdk-1.6.0_23/raw/master/jdk-6u23-linux-x64.bin && \
    ls -la && \
    chmod a+x jdk-6u23-linux-x64.bin && \
    mkdir -p /usr/java && \
    ./jdk-6u23-linux-x64.bin && \
    mv jdk1.6.0_23 /usr/java/latest && \
    rm -f jdk-6u23-linux-x64.bin
ENV PATH="/usr/java/latest/bin:${PATH}"
ENV JAVA_HOME="/usr/java/latest"

# INSTALL RARITAN CLIENT
COPY autoinstall.xml /tmp/autoinstall.xml
RUN cd /tmp && \
    curl -o mpc-installer.MPC_7.0.3.5.62.jar.zip \
            https://marands.io/files/raritan/mpc-installer.MPC_7.0.3.5.62.jar.zip && \
    unzip mpc-installer.MPC_7.0.3.5.62.jar.zip && \
    java -jar "mpc-installer.MPC_7.0.3.5.62.jar" autoinstall.xml && \
    rm -rf /tmp/mpc-installer.MPC_* /tmp/autoinstall.xml /var/lib/apt/lists/*


RUN mkdir /app
COPY --chown=${USER_ID}:${GROUP_ID} ./app/* /app/.
RUN chown -R ${USER_ID}:${GROUP_ID} /app
ENV PATH "/app:$PATH"
ENV HOME /home/ubuntu

# # Copy X app start script to right location
RUN chmod 555 /app/startapp.sh /app/watchapp.sh /app/rc.local && \
    mv /app/rc.local /etc/rc.local && \
    echo "/usr/java/latest" | tee /path.conf && \
    chown ubuntu:ubuntu /path.conf && \
     #ln -s /config/.java /nobody && \
    ln -s /config/ApplicationSettings.xml ${HOME}/ApplicationSettings.xml && \
    ln -s /config/KeyboardMacros.xml ${HOME}/KeyboardMacros.xml && \
    ln -s /config/DeviceTree.xml ${HOME}/DeviceTree.xml

USER ubuntu
RUN echo $HOME
COPY --chown=1000:1000 ssh/id_rsa ${HOME}/.ssh/id_rsa
COPY --chown=1000:1000 ssh/id_rsa.pub ${HOME}/.ssh/id_rsa.pub
COPY ssh/authorized_keys ${HOME}/.ssh/authorized_keys
#COPY config/vncpass ${HOME}/.vnc/passwd0
RUN echo "/app/startapp.sh" | tee -a ${HOME}/.profile

USER root
ENV PATH="/app:$PATH"
RUN echo $PATH
RUN echo ${JAVA_HOME}
RUN echo export JAVA_HOME=${JAVA_HOME} | tee -a /etc/environment
RUN echo "export PATH='$PATH:$JAVA_HOME/bin'" | tee -a /etc/environment
# Place whater volumes and ports you want exposed here:
VOLUME ["/config"]
EXPOSE 3389 8080 8081 5900
#CMD ["/sbin/my_init"]
