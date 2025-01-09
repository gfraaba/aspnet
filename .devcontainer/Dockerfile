# dotnet --list-sdks
# dotnet --info
# dotnet new mvc -o SampleAspNetApp
# cd SampleAspNetApp
# dotnet build
# dotnet dev-certs https --clean
# *NOTE: Run the following command to generate a self-signed certificate in the development environment (ONLY if mounting in the devcontainer.json file is not working)
# dotnet dev-certs https -ep /$HOME/.aspnet/https/aspnetapp.pfx -p password
# Update Program.cs to use HTTPS
# dotnet run
# Browse to https://localhost:5001
# Shut down the app by pressing Ctrl+C

# Use build arguments for flexibility
ARG DOTNET_SDK_VERSION="6.0"
ARG PWSH_VERSION="7.4.6"
ARG PLATFORM="linux/arm64"

FROM mcr.microsoft.com/dotnet/sdk:${DOTNET_SDK_VERSION}

# Install Pre-requisites
RUN apt-get update && apt-get install -y \
    curl \
    wget \
    apt-transport-https \
    software-properties-common

# Conditionally install PowerShell based on the platform
# Bug Fix: Had to declare the ARG PWSH_VERSION again here to use it in the RUN command!
ARG PWSH_VERSION
ARG PLATFORM
RUN if [ "$PLATFORM" = "linux/amd64" ]; then \
      wget -q "https://packages.microsoft.com/config/debian/12/packages-microsoft-prod.deb" \
      && dpkg -i packages-microsoft-prod.deb \
      && apt-get update \
      # && apt-get install -y powershell=${PWSH_VERSION}-1.deb \ # If a specific version is needed
      && apt-get install -y powershell \
      && apt-get clean \
      && rm -rf /var/lib/apt/lists/*; \
    elif [ "$PLATFORM" = "linux/arm64" ]; then \
        wget https://github.com/PowerShell/PowerShell/releases/download/v${PWSH_VERSION}/powershell-${PWSH_VERSION}-linux-arm64.tar.gz \
        && mkdir /usr/local/bin/pwsh${PWSH_VERSION} \
        && tar -xvf powershell-${PWSH_VERSION}-linux-arm64.tar.gz -C /usr/local/bin/pwsh${PWSH_VERSION} \
        && chmod +x /usr/local/bin/pwsh${PWSH_VERSION}/pwsh \
        && rm powershell-${PWSH_VERSION}-linux-arm64.tar.gz \
        && ls -la /usr/bin/pwsh \
        && sleep 10 \
        && rm /usr/bin/pwsh \
        && ln -s /usr/local/bin/pwsh${PWSH_VERSION}/pwsh /usr/bin/pwsh \
        # libicu is required for PowerShell; alternatively: export DOTNET_SYSTEM_GLOBALIZATION_INVARIANT=1
        # Yes, libunwind8 is required for .NET applications. It provides the necessary libraries for stack unwinding, which is essential for exception handling and debugging in .NET applications.
        && apt-get install -y libunwind8 libicu-dev \
        # Verify that PowerShell was installed correctly
        && echo "PWSH_VERSION: $PWSH_VERSION" \
        && pwsh --version || echo "PowerShell installation failed" \
        && echo "echoing PowerShell version: $(pwsh --version)" \
        && sleep 10; \
    else \
      echo "PowerShell is not supported on $PLATFORM" \
      && sleep 20 \
      && exit 1; \
    fi

