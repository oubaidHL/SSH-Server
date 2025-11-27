FROM debian:12

# Tools
RUN apt update && apt install -y \
    openssh-client

# Create SSH folder
RUN mkdir -p /root/.ssh

# Copy private + public keys
COPY id_ed25519 /root/.ssh/id_ed25519
COPY id_ed25519.pub /root/.ssh/id_ed25519.pub


# Fix permissions
RUN chmod 700 /root/.ssh && chmod 600 /root/.ssh/id_ed25519

CMD ["bash"]
