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
```
Thank you for your responses. Based on your requirements:

- A strong presence in the Microsoft ecosystem with some open-source tools.
- Aiming for a hybrid, eventually multi-cloud approach, with no focus on on-premises.
- No strong preference for specific vendor integration.
- A container-first approach with a need for strong Kubernetes support.
- Flexible budget, but cost-effectiveness is valued.
- Importance placed on scalability, performance, and high-level security.
- Preference for simplicity in management, but capable of handling complexity.
- Desire for flexibility to transition to AKS in the future.
- Need for solid vendor support, a sizable community, and a platform that is future-proof.
- No specific additional feature requirements.

**Recommendation**: Azure Arc seems to be the most suitable option for your needs. It aligns well with your Microsoft-centric infrastructure and supports a hybrid and multi-cloud approach. Azure Arc offers robust Kubernetes management, aligning with your container-first strategy. It provides the flexibility to transition to AKS, which is part of your future plan. Additionally, Azure Arc's integration capabilities, security standards, and scalability align with your requirements. Its management simplicity, combined with the potential for cost savings and strong vendor support, also fit your criteria. 

While Google Anthos and Red Hat OpenShift are strong contenders, especially in Kubernetes management and open-source integration, Azure Arc's alignment with your current Microsoft ecosystem and future AKS plans give it an edge in your specific case.
```
```
When meeting with vendors of Azure Arc, Google Anthos, Red Hat OpenShift, or Azure Stack HCI, it’s important to ask targeted questions that will help you understand how their offerings align with your specific needs. Here are some key questions to consider:

1. **Integration with Existing Infrastructure**:
   - How will your solution integrate with our existing Microsoft-based infrastructure and open-source tools?
   - Can you provide examples or case studies of similar integrations you've done?

2. **Transition to a Hybrid/Multi-Cloud Environment**:
   - What features and tools do you offer to support a transition to a hybrid and eventually multi-cloud environment?
   - How does your solution handle workload portability across different cloud environments?

3. **Container and Kubernetes Management**:
   - Can you elaborate on your Kubernetes management capabilities, especially for a container-first strategy?
   - How do you ensure high availability and scalability for containerized applications?

4. **Cost and ROI**:
   - What is the pricing model for your solution? Are there any cost-saving features?
   - Can you provide a cost-benefit analysis or ROI examples from other clients in the financial sector?

5. **Security and Compliance**:
   - How do you ensure security and compliance, particularly for financial firms with stringent requirements?
   - What security certifications and compliance standards does your solution meet?

6. **Ease of Management**:
   - How user-friendly is your management interface, especially for complex environments?
   - Do you offer automation and self-service capabilities?

7. **Vendor Lock-in and Flexibility**:
   - How easy is it to migrate to or from your platform, especially if we plan to move to AKS in the future?
   - What is your approach to vendor lock-in?

8. **Support and Community**:
   - What kind of technical support do you offer? Can you describe your SLAs?
   - How large and active is your user community?

9. **Future-Proofing**:
   - How do you handle updates and innovation? 
   - What's your roadmap for the next few years?

10. **Specific Features and Scenarios**:
    - Are there any additional features or capabilities that you think would be particularly beneficial for our organization?
    - How does your solution perform in real-world scenarios similar to ours?

These questions will help you gain a deeper understanding of how each vendor’s solution aligns with your organization's specific requirements, and how they can support your current and future cloud and container strategies.
```
```
Deploying a Kubernetes (K8s) cluster on Microsoft Azure Stack HCI involves several steps, leveraging Azure Stack HCI's capabilities and integration with Azure services. As of my last update in April 2023, here's a general guide on how to deploy a Kubernetes cluster on Azure Stack HCI:

### Prerequisites

1. **Azure Stack HCI Environment**: Ensure you have an operational Azure Stack HCI environment. This includes the necessary hardware and the Azure Stack HCI operating system installed.

2. **Windows Admin Center**: Install and configure Windows Admin Center, as it is the primary tool for managing Azure Stack HCI.

3. **Azure Subscription**: An active Azure subscription is required for some aspects of integration and management.

4. **Network Setup**: Proper network configuration in your Azure Stack HCI to support Kubernetes, including considerations for load balancing, node communication, and external access.

### Deployment Steps

1. **Set Up Azure Kubernetes Service (AKS) on Azure Stack HCI**:
    - AKS on Azure Stack HCI is a on-premises implementation of Azure's Kubernetes service.
    - In Windows Admin Center, go to the Azure Stack HCI cluster connection, and under "Kubernetes", select "Add Kubernetes cluster".
    - Follow the wizard to set up AKS on your Azure Stack HCI. This process includes specifying the cluster size, networking details, and other configuration parameters.

2. **Create the Kubernetes Cluster**:
    - Once AKS on Azure Stack HCI is set up, you can create your Kubernetes cluster.
    - Specify the cluster configuration such as the number of nodes, size, and the Kubernetes version.
    - Initiate the deployment. The Azure Stack HCI will handle the provisioning of Kubernetes nodes and the necessary networking components.

3. **Configure Kubernetes Networking**:
    - Configure networking for the Kubernetes cluster, which may include setting up a load balancer, network policies, and ingress controllers.

4. **Integrate with Azure Arc (Optional)**:
    - For enhanced management and governance, you can integrate your on-premises Kubernetes cluster with Azure Arc.
    - This allows you to manage your Azure Stack HCI Kubernetes cluster as if it were running in Azure, using Azure’s management tools and services.

5. **Deploy Applications**:
    - Once your Kubernetes cluster is up and running, you can deploy containerized applications.
    - Use Kubernetes manifests or Helm charts to deploy and manage your applications on the cluster.

6. **Monitoring and Management**:
    - Utilize tools like Azure Monitor, Azure Security Center, and Windows Admin Center for monitoring and managing the health, performance, and security of your Kubernetes cluster.

### Post-Deployment

- Regularly update and patch your Kubernetes environment to ensure security and performance.
- Monitor the cluster and applications using the integrated tools and Azure services.
- Consider implementing backup and disaster recovery strategies for your Kubernetes workloads.

### Note

The specific steps and configurations can vary based on the version of Azure Stack HCI, AKS, and the specific requirements of your environment. Always refer to the latest Microsoft documentation for Azure Stack HCI and AKS for detailed guidance and best practices.
```
```
https://petri.com/what-is-azure-stack-hci/
```

```
Wesley Miller
Wesley Miller  11:21 AM
Redhat Openshift Question
Can Openshift build and manage AKS clusters or only ARO clusters?
Matt Hargrave sent the following message at 11:23 AM
View Matt’s profileMatt Hargrave
Matt Hargrave  11:23 AM
ACM (Advanced Cluster Management) can manage AKS clusters but it can not build them
Wesley Miller sent the following messages at 11:24 AM
View Wesley’s profileWesley Miller
Wesley Miller  11:24 AM
is that something you plan to add some day?
Also are there additional limitations or is it fully featured management like it was an ARO cluster
Matt Hargrave sent the following messages at 11:24 AM
View Matt’s profileMatt Hargrave
Matt Hargrave  11:24 AM
I am not sure I have seen that on the roadmap.  Let me ask someone who is more in the know
View Matt’s profileMatt Hargrave
Matt Hargrave  11:25 AM
ACM uses native K8 APIs, so it should be able to do most anything.
Wesley Miller sent the following message at 11:26 AM
View Wesley’s profileWesley Miller
Wesley Miller  11:26 AM
and I mean like an AKS and ARO cluster can be managed easily and similarly
Matt Hargrave sent the following message at 11:26 AM
View Matt’s profileMatt Hargrave
Matt Hargrave  11:26 AM
yes
Wesley Miller sent the following message at 11:26 AM
View Wesley’s profileWesley Miller
Wesley Miller  11:26 AM
I guess what I mean to say is. Does Openshift as a whole work better with ARO than AKS from a management perspective
Matt Hargrave sent the following messages at 11:26 AM
View Matt’s profileMatt Hargrave
Matt Hargrave  11:26 AM
ARO is Openshift
ACM (Advanced Cluster Management) is an add-on product that can be used to manage Openshift and other Kuberentes clusters
https://access.redhat.com/articles/6968787

Red Hat Advanced Cluster Management for Kubernetes 2.6 Support Matrix - Red Hat Customer Portal
access.redhat.com • 2 min read
That is a matrix of supported features for the various hypervisor's k8 products/services
Wesley Miller sent the following message at 11:32 AM
View Wesley’s profileWesley Miller
Wesley Miller  11:32 AM
Yeah I knew ARO was Azure Redhat Openshift. Okay let me look at some of this before I ask anything else.
Matt Hargrave sent the following messages at 11:35 AM
View Matt’s profileMatt Hargrave
Matt Hargrave  11:35 AM
No worries.  I can happily connect you with the General Dynamics account team if that would be helpful
View Matt’s profileMatt Hargrave
Matt Hargrave  11:36 AM
So looking at that matrix and after speaking with one of our experts.  It looks like ACM can't build the managed services at all, even the Openshift ones (ARO, ROSA, etc).  You can of course import them once they are built however
Wesley Miller sent the following message at 11:43 AM
View Wesley’s profileWesley Miller
Wesley Miller  11:43 AM
I think that was all I needed I appreciate the help!
Matt Hargrave sent the following message at 11:43 AM
View Matt’s profileMatt Hargrave
Matt Hargrave  11:43 AM
no problem.  Out of curiosity how did you you end up landing on me?  Happy to help just surprised since we don't  seem to have any common connections
Wesley Miller sent the following messages at 11:44 AM
View Wesley’s profileWesley Miller
Wesley Miller  11:44 AM
Ha I think my linkedin is out of date. We were on the edward jones call together yesterday.
or at least you were one of the invited individuals, but I thought I remembered you
Matt Hargrave sent the following message at 11:45 AM
View Matt’s profileMatt Hargrave
Matt Hargrave  11:45 AM
Oooh right! yeah it shows you with General Dynamics
Wesley Miller sent the following message at 11:46 AM
View Wesley’s profileWesley Miller
Wesley Miller  11:46 AM
Yeah I don't use LinkedIn really at all. I got the premium trial just to ask a couple things directly. Its hard this week to get a hold of people at EJ.
Matt Hargrave sent the following message at 11:46 AM
View Matt’s profileMatt Hargrave
Matt Hargrave  11:46 AM
yeah my pervious job before Red Hat I started 2 weeks before Christmas.  It was a slow start

You can also email me at: matt.hargrave@redhat.com
Wesley Miller sent the following message at 11:47 AM
View Wesley’s profileWesley Miller
Wesley Miller  11:47 AM
I was confused on which tool we were completely evaluating because of how everyone was wording things here. Again most of my experience is managed k8s before I joined EJ a couple months ago
Matt Hargrave sent the following message at 11:48 AM
View Matt’s profileMatt Hargrave
Matt Hargrave  11:48 AM
Yeah happy to try to clear up some things.
Wesley Miller sent the following messages at 11:49 AM
View Wesley’s profileWesley Miller
Wesley Miller  11:49 AM
So I don't know much about Openshift and ACM yet but I am trying to research it. Some of the docs had me feeling like it doesn't create AKS (which you confirmed) and it made me worried that it doesn't fully manage managed k8s like it can Openshift
AKS clusters^
Matt Hargrave sent the following message at 11:52 AM
View Matt’s profileMatt Hargrave
Matt Hargrave  11:52 AM
Yeah that page should provide those details.  From a quick glance it looks like ACM only gives you about 80-90% of the feature set with non-openshift clusters
Wesley Miller sent the following message at 11:53 AM
View Wesley’s profileWesley Miller
Wesley Miller  11:53 AM
I will review that thoroughly if there is any other docs that might be nice to read let me know
Matt Hargrave sent the following message at 11:53 AM
View Matt’s profileMatt Hargrave
Matt Hargrave  11:53 AM
Hit me up if you want to dive into anything else.  Happy to get a call with just the technical folks and leave off the sales guys
Wesley Miller sent the following message at 11:54 AM
View Wesley’s profileWesley Miller
Wesley Miller  11:54 AM
Great yeah. I would prefer to maybe get some knowledge on my own first and they probably wouldnt like me reaching out individually. I get that vibe at least.
Matt Hargrave sent the following messages at 11:57 AM
View Matt’s profileMatt Hargrave
Matt Hargrave  11:57 AM
No worries.  Ill see if I can find any other interesting documentation and send it your way.
View Matt’s profileMatt Hargrave
Matt Hargrave  11:58 AM
https://developers.redhat.com/
This is probably one of the best technical resources.  It has less "sales fluff" than the marketing material

Red Hat Developer Homepage | Red Hat Developer
developers.redhat.com • 1 min read
you can even spin up openshift sandboxes and labs
Wesley Miller sent the following message at 12:01 PM
View Wesley’s profileWesley Miller
Wesley Miller  12:01 PM
so the creation and management of these clusters if they arent on prem will have to be on us rather than ACM
Matt Hargrave sent the following messages at 12:02 PM
View Matt’s profileMatt Hargrave
Matt Hargrave  12:02 PM
just the creation/destruction/upgrade
for the managed services
You can create/destroy/upgrade Openshift clusters in Azure using ACM that are non managed service.  IE it would use the IaaS to build the systems that would run Openshift
View Matt’s profileMatt Hargrave
Matt Hargrave  12:12 PM
Just to try to clear everything up
ACM can not create/upgrade/destroy any public cloud managed service. Those can be imported after the fact and managed

The only limitation for non Openshift K8s imported into ACM appear to only be around a few Operator management features
TODAY
Wesley Miller sent the following message at 3:12 AM
View Wesley’s profileWesley Miller
Wesley Miller  3:12 AM
Okay so a couple more thoughts: ACM is the tool that is installed on an OpenShift cluster (and has to be an OpenShift cluster), but if it was never used to manage an OpenShift cluster and only other the variants of Kubernetes (Core, AKS, etc.) we wouldn’t notice any lack of features? (Not including the provisioning non-managed openshift cluster feature) I would say and you’d probably agree though that embracing Opensource completely would be in our best interest if we take this route.
Matt Hargrave sent the following message at 9:37 AM
View Matt’s profileMatt Hargrave
Matt Hargrave  9:37 AM
So for the first part yes ACM is only supported on Openshift itself.  So if I understand what you are saying, is if you never run Openshift would it make more sense to run ACM's upstream?

So that gets into the value of a subscription, or really why would someone buy from Red Hat instead of running the community version.  Bear with me, this is kind of along winded discussion

Opensource is great, but its kind of wild west.  Also while the code itself is free, the building and maintaining aspect of it isn't, that requires resources (mostly human).  So from a business aspect you have to decide if you want to focus on maintaining the underlying platform and tools or business value. 

If something breaks when running the community version you are at the mercy of the community to try to solve that problem.  If its a bug in the code there is no guarantee its fixed, at least not in a timely matter.  Also things like security patches are not backported so you always will have to upgrade to get those which could be problematic on the environment.   For large organizations like Edward Jones that is a lot of risk to take on.  So the value from a subscription gives you support with SLAs from the people who actually build the software.  We build it and make sure its QAed and patches are backported.
Wesley Miller sent the following messages at 11:57 AM
View Wesley’s profileWesley Miller
Wesley Miller  11:57 AM
Those points I agree with. I think a better way to maybe clarify what I said is, if we never run openshift (for some odd hypothetical reason), does ACM still feel full featured and work as well as the opposite hypothetical scenario of only running Openshift (on-prem, in the cloud, etc.)

To help illustrate, as I could be using poor terminology, we might not be able to buy into Openshift initially. We will have on-prem k8s core and AKS clusters. Both may have to be imported into ACM for a time rather than creating an Openshift version and we may never be able to adopt ARO for the cloud side. I know from our previous discussions that any K8s can be managed fine, but what will our experience be if ACM is optimized for Openshift.(Edited)

View Wesley’s profileWesley Miller
Wesley Miller  12:01 PM
“ACM is optimized for OpenShift” may be conjecture because that’s only what I’ve heard people say, but logically it would make sense for it to have been designed that way.
Matt Hargrave sent the following message at 12:04 PM
View Matt’s profileMatt Hargrave
Matt Hargrave  12:04 PM
so ACM is only certified to run on Openshift, but I think in that scenario you might not actually have to pay for Openshift  because the ACM subs would cover the underlying OCP
Wesley Miller sent the following message at 12:05 PM
View Wesley’s profileWesley Miller
Wesley Miller  12:05 PM
I meant to add excluding the one Openshift cluster that ACM is running on^
Matt Hargrave sent the following message at 12:06 PM
View Matt’s profileMatt Hargrave
Matt Hargrave  12:06 PM
right.  In which ACM would do most things for all the other K8s clusters.  According to that matrix only a few features most of which are around operator governance are not supported
Wesley Miller sent the following message at 12:06 PM
View Wesley’s profileWesley Miller
Wesley Miller  12:06 PM
Okay cool. Yeah from a cost perspective I’m not too concerned (Edited)

Matt Hargrave sent the following message at 12:06 PM
View Matt’s profileMatt Hargrave
Matt Hargrave  12:06 PM
of course using something like ArgoCD (which is what Openshift uses for gitops) you could probably cover those gap
Wesley Miller sent the following message at 12:08 PM
View Wesley’s profileWesley Miller
Wesley Miller  12:08 PM
Ah okay yeah. Hopefully, this wasn’t a waste of your time of course, but it’s helpful to know given how long it takes EJ to migrate or change clusters types(Edited)

Matt Hargrave sent the following message at 12:09 PM
View Matt’s profileMatt Hargrave
Matt Hargrave  12:09 PM
Not at all man, my entire job is to help you guys out on the technical stuff
Wesley Miller sent the following messages at 12:12 PM
View Wesley’s profileWesley Miller
Wesley Miller  12:12 PM
My ideal situation in regards to adapting the Redhat ecosystem would be to use OpenShift for everything on-prem, stick with AKS in the cloud, and have that all managed by ACM. I just know we need to have ACM set up a couple months before we can effectively shift to Openshift for our other clusters
So my questions just come from that perspective. Anyway that’s all I had for now. I’m going to dive into governance a bit and see how that works
I really appreciate the information
Matt Hargrave sent the following messages at 12:14 PM
View Matt’s profileMatt Hargrave
Matt Hargrave  12:14 PM
Yeah that could work.  The only downside is you that it might be tricky to move workload between the two due to how the two platforms are built
Which may not be a use case that EJ needs anyways
Wesley Miller sent the following message at 12:15 PM
View Wesley’s profileWesley Miller
Wesley Miller  12:15 PM
Which two platforms AKS and Openshift?
Matt Hargrave sent the following messages at 12:16 PM
View Matt’s profileMatt Hargrave
Matt Hargrave  12:16 PM
yeah, so the advantage of Openshift on prem and in the cloud is you could move your workloads between those two really easily the experience and the tooling would all be the same
Makes stuff like DR really easy
Wesley Miller sent the following messages at 12:16 PM
View Wesley’s profileWesley Miller
Wesley Miller  12:16 PM
So like using ARO would allow us to do that?
Move workloads that is
Matt Hargrave sent the following messages at 12:17 PM
View Matt’s profileMatt Hargrave
Matt Hargrave  12:17 PM
absolutely.  Or if you didn't want to run ARO and wanted to deploy Openshift with IaaS on Azure you could do that too
You could even span the same app across both and then use global load balancers
Wesley Miller sent the following messages at 12:18 PM
View Wesley’s profileWesley Miller
Wesley Miller  12:18 PM
That’s cool but these are things only possible if everything was Openshift based. What other things like this exist if we fully embrace it?
Might be a reason to try to use ARO idk if IaaS is the right move but it might be
Matt Hargrave sent the following message at 12:19 PM
View Matt’s profileMatt Hargrave
Matt Hargrave  12:19 PM
No, you could still do that in theory with any K8s, but the more different it is the harder it is.  It would be a much more complex architecture to develop the app for
Wesley Miller sent the following message at 12:19 PM
View Wesley’s profileWesley Miller
Wesley Miller  12:19 PM
I meant like possible for ACM to facilitate
Matt Hargrave sent the following message at 12:19 PM
View Matt’s profileMatt Hargrave
Matt Hargrave  12:19 PM
Hard maybe on that one.
Wesley Miller sent the following message at 12:21 PM
View Wesley’s profileWesley Miller
Wesley Miller  12:21 PM
I guess to help clarify for me is. Does ACM facilitate some of those or is it just a consideration given we have different platforms
Matt Hargrave sent the following messages at 12:21 PM
View Matt’s profileMatt Hargrave
Matt Hargrave  12:21 PM
I won't say its not possible, but I think it would be more difficult than it would be worth.  You would need to invest a lot of resources to make it work that you might as well just use ARO
ACM does help facilitate that
Wesley Miller sent the following message at 12:21 PM
View Wesley’s profileWesley Miller
Wesley Miller  12:21 PM
Awesome, this is great information.
Matt Hargrave sent the following messages at 12:22 PM
View Matt’s profileMatt Hargrave
Matt Hargrave  12:22 PM
Yeah, I would probably need to get Daniel involved to really answer some of that
View Matt’s profileMatt Hargrave
Matt Hargrave  12:23 PM
He was at a customer before he came to Red Hat and built their global routing solution for doing something similar so he has really strong knowledge around that kind of thing
Wesley Miller sent the following message at 12:27 PM
View Wesley’s profileWesley Miller
Wesley Miller  12:27 PM
Maybe it’s worth capturing this, because it seems like an important question to have an answer for. If you don’t mind I would like to save some of this conversation in my notes
Matt Hargrave sent the following message at 12:28 PM
View Matt’s profileMatt Hargrave
Matt Hargrave  12:28 PM
Absolutely
Seen by Matt Hargrave at 12:28 PM.
Wesley Miller sent the following messages at 7:25 PM
View Wesley’s profileWesley Miller
Wesley Miller  7:25 PM
I really appreciate setting me straight on things.
```
```
https://access.redhat.com/articles/6968787
```

```
kubectl get pods --all-namespaces | awk '$4=="CrashLoopBackOff" {print $1, $2}' | while read ns pod; do
    echo "$(kubectl describe pod $pod -n $ns | grep -m 1 'Reason' | awk '{print $2}') - Namespace: $ns, Pod: $pod";
done | sort | uniq -c
```

```
kubectl get pods --all-namespaces | awk '$4=="CrashLoopBackOff" {print $1, $2}' | while read ns pod; do
    kubectl describe pod $pod -n $ns | awk '/Events:/{flag=1;next}/^$/{flag=0}flag' | grep 'Reason:' | awk -F'Reason:' '{print $2}' | awk -F' ' '{print $1}';
done | sort | uniq -c | sort -nr
```
