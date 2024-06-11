```
# Install curl if it is not already installed
apk add --no-cache curl ca-certificates

# Download the glibc APK packages from the sgerrand repository with curl and -k to skip certificate validation
curl -k -o /etc/apk/keys/sgerrand.rsa.pub https://alpine-pkgs.sgerrand.com/sgerrand.rsa.pub

# Download the specific version of glibc (for example, 2.28-r0) with curl and -k
curl -k -L -o glibc-2.28-r0.apk https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.28-r0/glibc-2.28-r0.apk
curl -k -L -o glibc-bin-2.28-r0.apk https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.28-r0/glibc-bin-2.28-r0.apk

# Install the downloaded packages
apk add --no-cache glibc-2.28-r0.apk glibc-bin-2.28-r0.apk

# Clean up the downloaded packages
rm -f glibc-2.28-r0.apk glibc-bin-2.28-r0.apk

# Verify the installation
/usr/glibc-compat/bin/ldd --version

```
