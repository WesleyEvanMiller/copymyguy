# copymyguy
```
# Use an official Ubuntu as a parent image
FROM ubuntu:latest

# Set environment variables to suppress interactive prompts during installation
ENV DEBIAN_FRONTEND=noninteractive

# Install necessary packages
RUN apt-get update && \
    apt-get install -y \
    ssh \
    x11-apps \
    xauth \
    xorg \
    xfce4 \
    xfce4-terminal \
    wget \
    gconf2 \
    gconf-service \
    gvfs-bin \
    libgtk2.0-0 \
    libnotify4 \
    libxtst6 \
    libnss3 \
    python3 \
    libxss1 \
    libasound2 \
    libcanberra-gtk-module \
    libunwind8 \
    libxkbfile1 \
    libexpat1 \
    libudev1 \
    sudo

# Download and install GitKraken (adjust the URL if needed)
RUN wget https://release.gitkraken.com/linux/gitkraken-amd64.deb && \
    dpkg -i gitkraken-amd64.deb && \
    rm gitkraken-amd64.deb

# Clean up after installation
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Create a user for SSH access (replace 'your_username' and 'your_password' with your desired values)
RUN useradd -m -s /bin/bash -p $(echo "your_password" | openssl passwd -1 -stdin) your_username && \
    usermod -aG sudo your_username

# Allow SSH login
RUN mkdir -p /run/sshd

# Start SSH server on port 22
EXPOSE 22

# Start the GUI environment when the container starts
CMD ["startxfce4"]

```

```
docker run -it --rm -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix -p 2222:22 --name my-gui-container gui-container
```

```
# Use the official Ubuntu 18.04 (Bionic Beaver) as a parent image
FROM ubuntu:18.04

# Set environment variables to suppress interactive prompts during installation
ENV DEBIAN_FRONTEND=noninteractive

# Update the package list and install necessary packages
RUN apt-get update && \
    apt-get install -y \
    ssh \
    x11-apps \
    xauth \
    xorg \
    xfce4 \
    xfce4-terminal \
    wget \
    gconf2 \
    gconf-service \
    gvfs-bin \
    libgtk2.0-0 \
    libnotify4 \
    libxtst6 \
    libnss3 \
    python3 \
    libxss1 \
    libasound2 \
    libcanberra-gtk-module \
    libunwind8 \
    libxkbfile1 \
    libexpat1 \
    libudev1 \
    sudo \
    libgnome-keyring0 \
    libsecret-1-0 \
    libsecret-1-dev \
    desktop-file-utils \
    mime-support

# Download and install GitKraken (adjust the URL if needed)
RUN wget https://release.gitkraken.com/linux/gitkraken-amd64.deb && \
    dpkg -i gitkraken-amd64.deb && \
    rm gitkraken-amd64.deb

# Clean up after installation
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Create a user for SSH access (replace 'your_username' and 'your_password' with your desired values)
RUN useradd -m -s /bin/bash -p $(echo "your_password" | openssl passwd -1 -stdin) your_username && \
    usermod -aG sudo your_username

# Allow SSH login
RUN mkdir -p /run/sshd

# Start SSH server on port 22
EXPOSE 22

# Start the GUI environment when the container starts
CMD ["startxfce4"]

```


```
# Use Ubuntu as the base image
FROM ubuntu:latest

# Set non-interactive mode for apt-get
ENV DEBIAN_FRONTEND=noninteractive

# Install necessary packages
RUN apt-get update && apt-get install -y \
    xfce4 \
    xfce4-terminal \
    xterm \
    novnc \
    x11vnc \
    && apt-get clean

# Set up a password for VNC access
RUN mkdir -p ~/.vnc && x11vnc -storepasswd your_password ~/.vnc/passwd

# Set up noVNC
RUN mkdir -p /opt/novnc \
    && ln -s /usr/share/novnc/vnc.html /opt/novnc/index.html

# Expose VNC and noVNC ports
EXPOSE 5900
EXPOSE 6080

# Start xfce4 and x11vnc
CMD ["startxfce4"]
```
```
# Use the official Ubuntu 20.04 base image
FROM ubuntu:20.04

# Install necessary packages
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y \
    xfce4 \
    xfce4-goodies \
    x11vnc \
    novnc \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Set the VNC password (change 'your_password' to your desired password)
RUN x11vnc -storepasswd your_password /etc/x11vnc.pass

# Create a startup script
RUN echo "#!/bin/sh" > /startup.sh && \
    echo "x11vnc -display :0 -rfbauth /etc/x11vnc.pass -forever -usepw -create" >> /startup.sh && \
    echo "Xvfb :0 -screen 0 1024x768x16 &" >> /startup.sh && \
    echo "DISPLAY=:0 startxfce4 &" >> /startup.sh && \
    echo "sleep 2" >> /startup.sh && \
    echo "websockify -D --web /usr/share/novnc/ --cert /etc/novnc-selfsigned.pem 6080 localhost:5900" >> /startup.sh && \
    chmod +x /startup.sh

# Expose ports for VNC and noVNC
EXPOSE 5900
EXPOSE 6080

# Start the startup script
CMD ["/startup.sh"]


```

```
# Use the official Ubuntu as the base image
FROM ubuntu:latest

# Install necessary packages
RUN apt-get update && apt-get install -y \
    xfce4 \
    xfce4-terminal \
    xterm \
    xfonts-base \
    novnc \
    websockify \
    supervisor \
    curl \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Expose ports for NoVNC (6080) and SSH (22)
EXPOSE 6080

# Set up NoVNC
ENV DISPLAY=:0 \
    VNC_PORT=5900 \
    NOVNC_PORT=6080 \
    VNC_PASSWORD=password

# Set up supervisor to run the necessary services
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Download noVNC web client
RUN mkdir -p /opt/noVNC/utils/websockify && \
    curl -Lo- https://github.com/novnc/noVNC/archive/v1.2.0.tar.gz | tar xz --strip 1 -C /opt/noVNC && \
    curl -Lo- https://github.com/novnc/websockify/archive/v0.10.0.tar.gz | tar xz --strip 1 -C /opt/noVNC/utils/websockify && \
    chmod +x -R /opt/noVNC/utils

# Start supervisor when the container starts
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]
```

```
[supervisord]
nodaemon=true

[program:xfce4]
command=startxfce4
autostart=true
autorestart=true

[program:websockify]
command=/opt/noVNC/utils/websockify/run 6080 localhost:5900 --web /opt/noVNC --wrap-mode=ignore
autostart=true
autorestart=true
```

```
{
  "name": "Ubuntu with GUI and NoVNC",
  "dockerFile": "Dockerfile",
  "service": "ubuntu-novnc-gui",
  "extensions": [
    "ms-vscode-remote.remote-containers"
  ],
  "forwardPorts": [6080],
  "overrideCommand": false,
  "settings": {
    "terminal.integrated.shell.linux": "/bin/bash"
  },
  "postCreateCommand": "docker run -d -p 6080:6080 -v /dev/shm:/dev/shm ubuntu-novnc-gui",
  "workspaceFolder": "/workspace"
}
```
```
# Start the NoVNC and Websockify servers (add this to the end of the Dockerfile)
RUN mkdir -p /opt/noVNC/utils/websockify && \
    curl -Lo- https://github.com/novnc/noVNC/archive/v1.2.0.tar.gz | tar xz --strip 1 -C /opt/noVNC && \
    curl -Lo- https://github.com/novnc/websockify/archive/v0.10.0.tar.gz | tar xz --strip 1 -C /opt/noVNC/utils/websockify && \
    chmod +x -R /opt/noVNC/utils
```
```
# Use the official Ubuntu 20.04 as the base image
FROM ubuntu:20.04

# Set environment variables to prevent interactive prompts during installation
ENV DEBIAN_FRONTEND=noninteractive

# Update the package lists and install dependencies
RUN apt-get update && \
    apt-get install -y \
    wget \
    libx11-xcb1 \
    libxtst6 \
    libnss3 \
    libxss1 \
    libasound2 \
    libatk1.0-0 \
    libatk-bridge2.0-0 \
    libxkbcommon-x11-0 \
    libgtk-3-0 \
    libgdk-pixbuf2.0-0 \
    libgbm1 \
    libnotify4 \
    libgconf-2-4 \
    libxss1 \
    libasound2 \
    libxtst6 \
    libx11-xcb1 \
    libgtk2.0-0 \
    libxkbfile1 \
    libsecret-1-0 \
    libnss3 \
    libxss1 \
    libx11-6 \
    libxext6 \
    libxrender1 \
    libxtst6 \
    libegl1-mesa \
    libgl1-mesa-glx \
    libegl1-mesa-drivers \
    libegl1 \
    libegl-mesa0 \
    libxrandr2 \
    xdg-utils \
    libxcomposite1 \
    libcap2

# Download and install GitKraken
RUN wget https://release.gitkraken.com/linux/gitkraken-amd64.deb && \
    dpkg -i gitkraken-amd64.deb && \
    apt-get install -f -y && \
    rm gitkraken-amd64.deb

# Cleanup to reduce the image size
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Set the entry point for GitKraken
ENTRYPOINT ["gitkraken"]

# Expose a port if GitKraken requires it (check GitKraken documentation)
# EXPOSE 8080

# Run GitKraken as a non-root user (recommended for security, but may require additional configuration)
# Replace '1000' with your user's UID and '1000' with your user's GID
# USER 1000:1000
```

```
# Use Ubuntu as the base image
FROM ubuntu:latest

# Set non-interactive mode for package installations
ENV DEBIAN_FRONTEND=noninteractive

# Install necessary dependencies for running a graphical environment and VNC
RUN apt-get update && apt-get install -y \
    xorg xserver-xorg-video-dummy xinit \
    xfce4 xfce4-terminal xfce4-taskmanager \
    tightvncserver novnc websockify supervisor \
    wget curl

# Download and install Lens
RUN wget https://github.com/lensapp/lens/releases/download/{VERSION}/Lens-{VERSION}.linux.AppImage && \
    chmod +x Lens-{VERSION}.linux.AppImage && \
    mv Lens-{VERSION}.linux.AppImage /usr/local/bin/lens

# Configure VNC server
RUN mkdir ~/.vnc && \
    echo "startxfce4 &" > ~/.vnc/xstartup && \
    chmod +x ~/.vnc/xstartup

# Expose VNC port
EXPOSE 5901

# Expose noVNC web interface port
EXPOSE 6080

# Create a supervisord configuration
RUN echo "[supervisord]\n\
nodaemon=true\n\
\n\
[program:xvfb]\n\
command=Xvfb :1 -screen 0 1024x768x16\n\
\n\
[program:x11vnc]\n\
command=x11vnc -display :1 -rfbport 5901 -noxrecord -noxfixes -noxdamage -forever\n\
\n\
[program:novnc]\n\
command=/usr/share/novnc/utils/launch.sh --vnc localhost:5901 --listen 6080\n\
autostart=true\n\
autorestart=true\n\
redirect_stderr=true" > /etc/supervisor/conf.d/supervisord.conf

# Start supervisord
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]
```
```
# Use the official Ubuntu 20.04 base image
FROM ubuntu:20.04

# Set non-interactive mode during package installation
ENV DEBIAN_FRONTEND=noninteractive

# Install required packages
RUN apt-get update && \
    apt-get install -y xfce4 xfce4-goodies x11vnc xvfb wget unzip novnc git libgtk2.0-0 libnotify4 libgconf-2-4 libnss3 libxss1 libasound2 && \
    apt-get clean

# Download noVNC
RUN mkdir -p /opt/novnc && \
    wget -qO- https://github.com/novnc/noVNC/archive/v1.2.0.tar.gz | tar xz --strip 1 -C /opt/novnc

# Install GitKraken
RUN wget -qO- https://release.gitkraken.com/linux/gitkraken-amd64.deb -O /tmp/gitkraken.deb && \
    dpkg -i /tmp/gitkraken.deb && \
    apt-get install -f -y && \
    rm /tmp/gitkraken.deb

# Expose port for NoVNC (6080) and SSH (22 if needed)
EXPOSE 6080

# Start NoVNC and Xfce desktop
CMD ["/bin/bash", "-c", "Xvfb :1 -screen 0 1920x1080x16 & x11vnc -display :1 -nopw -listen 0.0.0.0 -forever & /opt/novnc/utils/launch.sh --vnc localhost:5900 --listen 6080"]
```


```
# Use the official Ubuntu 20.04 image as the base image
FROM ubuntu:20.04

# Set environment variables to avoid interactive prompts during installation
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=UTC

# Update the package list and install dependencies
RUN apt-get update && apt-get install -y \
    git \
    python3-venv \
    python3-pip \
    wget \
    tightvncserver \
    xvfb \
    fluxbox \
    x11vnc \
    novnc \
    supervisor \
    && apt-get clean

# Clone the MuhammedKlakan/openlens repository
RUN git clone https://github.com/MuhammedKlakan/openlens.git /opt/openlens

# Expose ports for noVNC and VNC server
EXPOSE 8080
EXPOSE 5901

# Configure the VNC server (set a password)
RUN mkdir -p ~/.vnc && echo "password" | vncpasswd -f > ~/.vnc/passwd && chmod 600 ~/.vnc/passwd

# Create a startup script for VNC
RUN echo "#!/bin/bash" > /opt/startup.sh && \
    echo "x11vnc -display :1 -usepw -forever &" >> /opt/startup.sh && \
    echo "/usr/bin/novnc --listen 8080 --vnc localhost:5901 &" >> /opt/startup.sh && \
    chmod +x /opt/startup.sh

# Start the VNC server and noVNC on container startup
CMD ["/opt/startup.sh"]
```
```
# Use an Alpine Linux base image
FROM alpine:latest

# Update package repositories and install required packages
RUN apk update && apk upgrade
RUN apk add emacs-nox git

# Install Magit using the Emacs package manager
RUN emacs --batch --eval "(require 'package)" --eval "(add-to-list 'package-archives '(\"melpa\" . \"https://melpa.org/packages/\") t)" --eval "(package-initialize)" --eval "(package-refresh-contents)" --eval "(package-install 'magit)"

# Clean up unnecessary files
RUN rm -rf /var/cache/apk/*

# Start Emacs with Magit when the container runs
CMD ["emacs", "--eval", "(magit-status)"]

```

```
# Use an Ubuntu base image
FROM ubuntu:latest

# Update package repositories and install required packages
RUN apt-get update && apt-get install -y emacs-nox git

# Install Magit using the Emacs package manager
RUN emacs --batch --eval "(require 'package)" --eval "(add-to-list 'package-archives '(\"melpa\" . \"https://melpa.org/packages/\") t)" --eval "(package-initialize)" --eval "(package-refresh-contents)" --eval "(package-install 'magit)"

# Clean up after package installation
RUN apt-get clean

# Start Emacs with Magit when the container runs
CMD ["emacs", "--eval", "(magit-status)"]

```

```
# Use an Ubuntu base image
FROM ubuntu:latest

# Update package repositories and install required packages
RUN apt-get update && apt-get upgrade -y
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y emacs-nox git build-essential

# Clone the Magit GitHub repository
RUN git clone https://github.com/magit/magit /root/magit

# Build and install Magit
WORKDIR /root/magit
RUN make
RUN make install

# Clean up unnecessary files
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /root/magit

# Start Emacs with Magit when the container runs
CMD ["emacs", "--eval", "(magit-status)"]

```
```
;; Set your preferred package repositories (Melpa and Melpa Stable)
(require 'package)
(setq package-archives '(("melpa" . "https://melpa.org/packages/")
                         ("melpa-stable" . "https://stable.melpa.org/packages/"))
      package-archive-priorities '(("melpa" . 10)
                                   ("melpa-stable" . 5)))

;; Initialize the package system
(package-initialize)

;; Ensure use-package is installed (a popular package for package management)
(unless (package-installed-p 'use-package)
  (package-refresh-contents)
  (package-install 'use-package))
(require 'use-package)

;; Set up proxy settings for HTTP and HTTPS
;; Replace 'proxy_server_address' and 'proxy_server_port' with your proxy server information
(setq url-proxy-services
      '(("http" . "http://proxy_server_address:proxy_server_port")
        ("https" . "http://proxy_server_address:proxy_server_port")))

;; Define no_proxy exceptions for local and private network addresses and .devjones.com domains
(setq url-proxy-services
      '(("no_proxy" . "127.0.0.1\\|localhost\\|192.168.0.0/16\\|.devjones.com")))

;; Add any other custom settings and configurations below this line

;; Example: Set your default font
;; (when (display-graphic-p)
;;   (set-face-attribute 'default nil :font "Source Code Pro-12"))

;; Example: Enable line numbers
;; (global-linum-mode t)

;; Example: Configure a theme (install the theme first)
;; (use-package dracula-theme
;;   :ensure t
;;   :config
;;   (load-theme 'dracula t))

;; Your custom configurations go here

;; Don't forget to save your `init.el` file after making changes.
```

```
Courses designed to prepare you for the AZ-104 Microsoft Azure Administrator exam typically cover a comprehensive range of topics that align with the exam objectives. These courses aim to provide both theoretical knowledge and practical skills required to pass the exam and perform effectively as an Azure Administrator.

Here's an overview of what these courses generally cover, corresponding to the key domains of the AZ-104 exam:

1. **Manage Azure Identities and Governance (15-20%)**:
    - Understanding Azure Active Directory (AD), managing Azure AD objects (users, groups, and devices).
    - Implementing and managing hybrid identities.
    - Using role-based access control (RBAC) to assign permissions.
    - Managing subscriptions, governance policies, and role assignments.

2. **Implement and Manage Storage (10-15%)**:
    - Securing storage accounts.
    - Managing storage (Blob Storage, File Shares, and storage synchronization).
    - Configuring Azure files and implementing Azure backup for various workloads.

3. **Deploy and Manage Azure Compute Resources (25-30%)**:
    - Creating and configuring Azure Virtual Machines (VMs) for scalability and high availability.
    - Automating deployment and configuration of VMs using Azure Resource Manager templates.
    - Creating and managing containers in Azure (using Azure Kubernetes Service (AKS) or Azure Container Instances).
    - Configuring and managing Azure Web Apps.

4. **Configure and Manage Virtual Networking (30-35%)**:
    - Implementing and managing virtual networks, including network security groups and application security groups.
    - Configuring private and public DNS zones.
    - Implementing secure access (VPNs and ExpressRoute).
    - Configuring load balancers.
    - Monitoring and troubleshooting network-related issues.
    - Integrating on-premises networks with Azure virtual networks.

5. **Monitor and Back Up Azure Resources (10-15%)**:
    - Using Azure Monitor for monitoring and reporting.
    - Implementing backup and recovery solutions in Azure.
    - Using tools like Azure Backup and Azure Site Recovery.

### Types of Courses and Resources:

- **Online Self-Paced Courses**: These include video lectures, reading materials, labs, and quizzes, offered by platforms like Microsoft Learn, Udemy, Coursera, Pluralsight, and others.
- **Instructor-Led Training**: Offered by Microsoft or authorized training partners, these courses are conducted by certified trainers and often include interactive sessions, hands-on labs, and direct instructor support.
- **Books and Study Guides**: Various publishers offer study guides and books that cover the AZ-104 exam objectives in detail.
- **Practice Tests and Labs**: These are crucial for understanding the exam format and practicing real-world scenarios. Many courses include practice tests and lab exercises.

### Additional Tips:

- **Hands-On Experience**: Theoretical knowledge is important, but practical experience is crucial. Try to spend time on the Azure portal, experimenting with different services.
- **Stay Updated**: Azure services are constantly evolving, so make sure your learning materials are up-to-date with the latest Azure features and exam requirements.
- **Microsoft Documentation**: Leveraging Microsoft's own documentation can be highly beneficial as it's the most up-to-date source of information about Azure services.

Always check the latest exam outline from the official Microsoft certification page, as the exam objectives might change over time.
```
```
kubectl get pods --all-namespaces -o go-template='{{range .items}}{{.metadata.name}} {{.metadata.namespace}} {{.metadata.creationTimestamp}}{{"\n"}}{{end}}' | awk -v date="$(date -u +'%Y-%m-%dT%H:%M:%SZ' --date='1 hour ago')" '$3 > date {print $1 " in namespace " $2}'
```
```
The AZ-900: Microsoft Azure Fundamentals exam is an entry-level certification intended for individuals who are just beginning to work with cloud-based solutions and services or are new to Azure. Courses designed to prepare for the AZ-900 exam typically cover foundational level knowledge of cloud services and how those services are provided with Microsoft Azure.

The AZ-900 exam covers several key areas:

1. **Describe Cloud Concepts (20-25%)**:
    - Understanding the benefits and considerations of using cloud services.
    - Differentiating between categories of cloud services (IaaS, PaaS, SaaS).
    - Understanding the differences between types of cloud computing (public, private, hybrid).

2. **Describe Core Azure Services (15-20%)**:
    - Core Azure architectural components (regions, resource groups, Azure Resource Manager, Virtual Networks, etc.).
    - Core Azure services and products (Azure Virtual Machines, Azure Web Apps, Azure SQL Database, Azure Blob Storage, etc.).
    - Azure solutions like Internet of Things (IoT), Big Data, Machine Learning, and Serverless.

3. **Describe Core Solutions and Management Tools on Azure (10-15%)**:
    - Types of Azure management tools and technologies (Azure PowerShell, Azure CLI, Azure Portal, Azure Advisor).
    - Azure solutions for database management, compute, networking, storage, etc.

4. **Describe General Security and Network Security Features (10-15%)**:
    - Azure Security Center, Azure Secure Score.
    - Identity services (Azure Active Directory, Multi-Factor Authentication).
    - Network security features (Network Security Groups, Application Security Groups, Azure Firewall, etc.).

5. **Describe Identity, Governance, Privacy, and Compliance Features (20-25%)**:
    - Identity services including Azure AD.
    - Governance features like resource locks, tags, Azure Policy, Azure Blueprints.
    - Privacy and compliance resources (Trust Center, Azure compliance documentation).

6. **Describe Azure Cost Management and Service Level Agreements (10-15%)**:
    - Planning and managing costs (Azure Pricing Calculator, Total Cost of Ownership Calculator, Azure Cost Management).
    - Azure Service Level Agreements (SLAs) and service lifecycles.

### Types of Courses and Learning Materials:

- **Online Self-Paced Courses**: These are available on platforms like Microsoft Learn, Coursera, Udemy, Pluralsight, and LinkedIn Learning. They often include video lectures, interactive quizzes, and sometimes hands-on labs.
- **Instructor-Led Training**: Microsoft and its partners offer instructor-led training courses that can be taken in a classroom setting or online.
- **Books and Study Guides**: Various publishers offer study guides for the AZ-900 exam. These can be a good supplement to online courses.
- **Microsoft Documentation and Resources**: Microsoft provides extensive documentation and learning paths on their own website, which are invaluable resources for understanding Azure services.
- **Practice Exams**: These can help familiarize you with the exam format and types of questions you can expect.

### Additional Tips:

- **Understand the Basics**: Make sure you understand basic cloud concepts and how they are implemented in Azure.
- **Practical Experience**: Even for a fundamental exam, having hands-on experience with the Azure portal and services can be very helpful.
- **Stay Updated**: The cloud landscape changes rapidly, so ensure your study material is up to date.

Remember, the AZ-900 is a fundamentals exam, so the focus is on understanding basic concepts and services in Azure rather than deep technical expertise. It's an excellent starting point for anyone looking to work with Azure or cloud services in general.
```
```
Certainly, a comparison between the AZ-900 (Microsoft Azure Fundamentals) and AZ-104 (Microsoft Azure Administrator) exams can be presented in a table format. The comparison will focus on the target audience, exam content, difficulty level, and the primary focus of each exam.

| Criteria               | AZ-900: Azure Fundamentals                          | AZ-104: Azure Administrator                         |
|------------------------|----------------------------------------------------|----------------------------------------------------|
| **Target Audience**    | Beginners to Azure, non-technical roles, those new to cloud computing | IT professionals with some Azure experience, those responsible for implementing, managing, and monitoring an organization’s Azure environment |
| **Focus**              | Basic understanding of cloud concepts, core Azure services, security, privacy, compliance, and trust, Azure pricing, SLAs, and lifecycle | Managing Azure identities and governance, implementing and managing storage, deploying and managing Azure compute resources, configuring and managing virtual networking, monitoring and backup of Azure resources |
| **Content Depth**      | Foundational, broad overview of cloud concepts and Azure services | Deep dive into specific Azure administration tasks |
| **Prerequisites**      | None, introductory level | Some knowledge and experience in Azure, understanding of core Azure services, workloads, security, and governance |
| **Difficulty Level**   | Entry-level, more conceptual | Intermediate, requires practical experience in Azure administration |
| **Ideal For**          | Those seeking to validate foundational level knowledge of cloud services and Azure | Azure administrators, system admins, network admins, and IT professionals with a focus on Azure |
| **Exam Format**        | Multiple-choice, scenario-based questions, and case studies | Multiple-choice, multiple response, scenario-based questions, and case studies |
| **Primary Objective**  | To understand cloud concepts, core Azure services, security, privacy, pricing, and support | To manage Azure identities, governance, storage, compute, and virtual networks effectively |
| **Practical Component**| Not focused on hands-on experience | Strong emphasis on practical, hands-on experience and knowledge |

This table highlights the key differences between the two exams. The AZ-900 is an entry-level exam suitable for those new to Azure or cloud technology in general, focusing on basic concepts and knowledge. In contrast, the AZ-104 is more advanced, targeting IT professionals who actively manage and administer Azure cloud services and infrastructure.
```
