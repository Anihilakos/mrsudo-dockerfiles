FROM kalilinux/kali-rolling:latest

LABEL website="https://github.com/iphoneintosh/kali-docker"
LABEL description="Kali Linux with XFCE Desktop via VNC and noVNC in browser."

# Install kali packages

ARG KALI_METAPACKAGE=core
ENV DEBIAN_FRONTEND noninteractive
RUN apt-get update
RUN apt-get -y upgrade
RUN apt-get -y install kali-linux-default
RUN apt-get clean

# Install kali desktop

ARG KALI_DESKTOP=xfce
RUN apt-get -y install kali-desktop-${KALI_DESKTOP}
RUN apt-get -y install tightvncserver dbus dbus-x11 novnc net-tools nano net-tools python3-psycopg2 python3-apt inetutils-ping python3-pip
RUN pip3 install requests
RUN pip3 install click
RUN pip3 install pyyaml

ENV USER root

ENV VNCEXPOSE 0
ENV VNCPORT 5900
EXPOSE 22
ENV VNCPWD changeme
ENV VNCDISPLAY 1920x1080
ENV VNCDEPTH 16

ENV NOVNCPORT 8080

# Install custom packages
# TODO: You can add your own packages here
# Install vagrant

ENV TZ=America/Mexico_City
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt-get update -y

RUN apt-get install -y --no-install-recommends ssh sudo

RUN useradd --create-home -s /bin/bash vagrant
RUN echo -n 'vagrant:vagrant' | chpasswd
RUN echo 'vagrant ALL = NOPASSWD: ALL' > /etc/sudoers.d/vagrant
RUN chmod 440 /etc/sudoers.d/vagrant
RUN mkdir -p /home/vagrant/.ssh
RUN chmod 700 /home/vagrant/.ssh
#RUN echo "ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEA6NF8iallvQVp22WDkTkyrtvp9eWW6A8YVr+kz4TjGYe7gHzIw+niNltGEFHzD8+v1I2YJ6oXevct1YeS0o9HZyN1Q9qgCgzUFtdOKLv6IedplqoPkcmF0aYet2PkEDo3MlTBckFXPITAMzF8dJSIFo9D8HfdOV0IAdx4O7PtixWKn5y2hMNG0zQPyUecp4pzC6kivAIhyfHilFR61RGL+GPXQ2MWZWFYbAGjyiYJnAmCP3NOTd0jMZEnDkbUvxhMmBYSdETk1rRgm+R4LOzFUGaHqHDLKLX+FIPKcF96hrucXzcWyLbIbEgE98OHlnVYCzRdK8jlqm8tehUc9c9WhQ==" > /home/vagrant/.ssh/authorized_keys
RUN echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC0Q/9dx+JQLQ+HonlH0Dj8w3UEhmqqSUnSzokuW1esDccU/ag5ymbAm83aXs5D9a6f06Jbv5G0Hc7rtPxWYCX2aGfnb1A5RCxlvuFalpUnHzrpzZeQjy+Q1TUjgd0lHbrLAyzZShXPlaMyhXpIHUXOpN2tWsMFYwz3vo6RJ7ZsdNio57mEG3kQPjcEZPkE3Gs/dQsCLEfT+UfE0146blVocR4nkZyWRIYMgbuU95vLni7M+GL2OmZWIpPJDrmY03a8+iGK3Ad361jPRgfLt6z3DOjRi1+QRTrhePq4kV/uSb5nr6bKfTq1wk64jof7kjTH/T5o5kJshTWWMwH1tPkGmkpBTGFK7rSfdV3nWaJ4jhFeE5BpnKmuUeXoj9yaKeG9VrK5NmTWkSfBnPI76sxI4OLp1Q8uISBwvAupnyRaMY5tvWl++vgBNknoUaSjknphr6Tvv/eT0ZXmMdqECsGWWarIrF8CkDQUyxsDjwtj6MsV/JhCKkxRpMNZ4B6epkk= root@da64c7755a0b" > /home/vagrant/.ssh/authorized_keys
RUN chmod 600 /home/vagrant/.ssh/authorized_keys
RUN chown -R vagrant:vagrant /home/vagrant/.ssh
RUN sed -i -e 's/Defaults.*requiretty/#&/' /etc/sudoers
RUN sed -i -e 's/\(UsePAM \)yes/\1 no/' /etc/ssh/sshd_config
RUN mkdir /var/run/sshd
RUN apt-get -y install openssh-client

# Entrypoint
COPY fullchain.pem /etc/ssl/certs/novnc_cert.pem
COPY privkey.pem /etc/ssl/private/novnc_key.pem

COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
CMD ["service", "ssh", "start"]
ENTRYPOINT [ "/entrypoint.sh" ]