FROM debian:12

# Install dependencies
RUN apt update && apt install -y \
    openssh-server \
    lynis \
    fail2ban \
    sudo \
    nano \
    rsyslog && \
    mkdir -p /run/sshd /root/.ssh

# Create admin user
RUN useradd -m -s /bin/bash admin && \
    echo "admin:RaNdOmPaSsWoRd" | chpasswd

# Add root SSH key
COPY id_ed25519.pub /root/.ssh/authorized_keys
RUN chmod 700 /root/.ssh && chmod 600 /root/.ssh/authorized_keys

# MOTD
RUN cat << 'EOF' > /etc/motd
          __   _,--="=--,_   __
         /  \."    .-.    "./  \
        /  ,/  _   : :   _  \/` \
        \  `| /o\  :_:  /o\ |\__/
         `-'| :="~` _ `~"=: |
            \`     (_)     `/
     .-"-.   \      |      /   .-"-.
.---{     }--|  /,.-'-.,\  |--{     }---.
 )  (_)_)_)  \_/`~-===-~`\_/  (_(_(_)  (
(    ACCÈS STRICTEMENT RÉSERVÉ         )
 )     ACTIVITÉS SURVEILLÉES           (
(  TOUTE ACTION NON AUTORISÉE SERA     )
 )      BLOQUÉE ET SIGNALÉE            (
'---------------------------------------'
EOF

# SSH config
RUN sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config && \
    sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config && \
    sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin prohibit-password/' /etc/ssh/sshd_config && \
    sed -i 's/#LogLevel INFO/LogLevel VERBOSE/' /etc/ssh/sshd_config

# Fix rsyslog inside Docker
RUN sed -i 's/^\$ModLoad imklog/#$ModLoad imklog/' /etc/rsyslog.conf && \
    sed -i 's/^\$KLogPermitNonKernelFacility/#$KLogPermitNonKernelFacility/' /etc/rsyslog.conf

# Create auth.log
RUN touch /var/log/auth.log && chmod 644 /var/log/auth.log

# Fail2ban jail config
RUN cat << 'EOF' > /etc/fail2ban/jail.local
[sshd]
enabled = true
port = ssh
logpath = /var/log/auth.log
backend = auto
maxretry = 3
findtime = 300
bantime = 600
EOF

# Generate SSH host keys
RUN ssh-keygen -A

# Start rsyslog, fail2ban and sshd (NO systemd)
CMD rsyslogd && \
    fail2ban-client -x start && \
    /usr/sbin/sshd -D
