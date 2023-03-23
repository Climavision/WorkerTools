FROM octopusdeploy/tentacle:6.3.417

#ARG Aws_Cli_Version=2.0.60
#ARG Aws_Iam_Authenticator_Version=0.5.3
#ARG Aws_Powershell_Version=4.1.2
# Get Azure_Cli_Version from https://github.com/actions/runner-images/blob/main/images/linux/Ubuntu2204-Readme.md
ARG Azure_Cli_Version=2.46.0\*
# Get Azure_Powershell_Version from https://github.com/actions/runner-images/blob/main/images/linux/Ubuntu2204-Readme.md
ARG Azure_Powershell_Version=9.3.0
# Get Dotnet_Sdk_Version from https://learn.microsoft.com/en-us/dotnet/core/install/linux-debian
ARG Dotnet_Sdk_Version=6.0
#ARG Ecs_Cli_Version=1.20.0
#ARG Eks_Cli_Version=0.25.0
#ARG Google_Cloud_Cli_Version=339.0.0-0
# Get Helm_Version from https://github.com/helm/helm/releases
ARG Helm_Version=v3.11.2
ARG Java_Jdk_Version=11.0.17+8-1ubuntu2~18.04
ARG Kubectl_Version=1.25.4-00
# Get Octopus_Cli_Version from https://github.com/OctopusDeploy/OctopusCLI/blob/main/CHANGELOG.md
ARG Octopus_Cli_Version=9.1.7
# Get Octopus_Client_Version from https://www.nuget.org/packages/Octopus.Client
ARG Octopus_Client_Version=14.3.516
# Get Powershell_Version from https://packages.microsoft.com/repos/microsoft-debian-bullseye-prod/dists/bullseye/main/binary-amd64/Packages
ARG Powershell_Version=7.3.3-1.deb
# Get Terraform_Version from https://github.com/hashicorp/terraform/releases
ARG Terraform_Version=1.4.2
# Get Umoci_Version from https://github.com/opencontainers/umoci/releases
ARG Umoci_Version=0.4.7

# get `wget` & software-properties-common
# https://learn.microsoft.com/en-us/powershell/scripting/install/install-debian?view=powershell-7.3
RUN apt-get update && \
    apt-get install -y wget unzip curl gnupg apt-transport-https

# get powershell for debian 11
RUN wget --quiet -O - https://packages.microsoft.com/keys/microsoft.asc | apt-key add -  && \
    sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-debian-bullseye-prod bullseye main" > /etc/apt/sources.list.d/microsoft.list' && \
    apt-get update && \
    apt-get install -y powershell=${Powershell_Version}

# Get Octo CLI
# https://octopus.com/downloads/octopuscli#linux
RUN apt-get update && \
    apt-get install -y --no-install-recommends gnupg curl ca-certificates apt-transport-https && \
    curl -sSfL https://apt.octopus.com/public.key | apt-key add - && \
    sh -c "echo deb https://apt.octopus.com/ stable main > /etc/apt/sources.list.d/octopus.com.list" && \
    apt-get update && \
    apt-get install -y octopuscli=${Octopus_Cli_Version}

# Install Octopus Client
# https://octopus.com/docs/octopus-rest-api/octopus.client
RUN pwsh -c 'Install-Package -Force Octopus.Client -MaximumVersion "'${Octopus_Client_Version}'" -source https://www.nuget.org/api/v2 -SkipDependencies' && \
    octopusClientPackagePath=$(pwsh -c '(Get-Item ((Get-Package Octopus.Client).source)).Directory.FullName') && \
    cp -r $octopusClientPackagePath/lib/netstandard2.0/* .

# Get AWS Powershell core modules
# https://docs.aws.amazon.com/powershell/latest/userguide/pstools-getting-set-up-linux-mac.html
#RUN pwsh -c 'Install-Module -Force -Name AWSPowerShell.NetCore -AllowClobber -Scope AllUsers -MaximumVersion "'${Aws_Powershell_Version}'"'

# Get AZ Powershell core modules
# https://docs.microsoft.com/en-us/powershell/azure/install-az-ps?view=azps-3.6.1
RUN pwsh -c 'Install-Module -Force -Name Az -AllowClobber -Scope AllUsers -MaximumVersion "'${Azure_Powershell_Version}'"'

# Get Helm3
RUN wget --quiet -O - https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get | bash -s -- -v ${Helm_Version}

# Get .NET SDK 6.0
# https://docs.microsoft.com/en-us/dotnet/core/install/linux-package-manager-ubuntu-1804
RUN wget -q https://packages.microsoft.com/config/debian/11/packages-microsoft-prod.deb -O packages-microsoft-prod.deb && \
    dpkg -i packages-microsoft-prod.deb && \
    rm packages-microsoft-prod.deb && \
    apt-get update && \
    apt-get install -y dotnet-sdk-${Dotnet_Sdk_Version}

# Get JDK
# https://www.digitalocean.com/community/tutorials/how-to-install-java-with-apt-on-ubuntu-18-04
# https://packages.ubuntu.com/bionic/openjdk-11-dbg
# RUN apt-get install -y openjdk-11-jdk-headless=${Java_Jdk_Version}
RUN apt-get install -y default-jdk

# Install common Java tools
RUN apt-get install -y maven gradle

# Get Azure CLI
# https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-apt?view=azure-cli-latest
RUN wget --quiet -O - https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor | tee /etc/apt/trusted.gpg.d/microsoft.asc.gpg > /dev/null && \
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ bullseye main" | tee /etc/apt/sources.list.d/azure-cli.list && \
    apt-get update && \
    apt-get install -y azure-cli=${Azure_Cli_Version}

# Get NodeJS
# https://websiteforstudents.com/how-to-install-node-js-10-11-12-on-ubuntu-16-04-18-04-via-apt-and-snap/\
RUN wget --quiet -O - https://deb.nodesource.com/setup_14.x | bash && \
    apt-get install -y nodejs

# Get Kubectl
# https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-using-native-package-management
RUN wget --quiet -O - https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -  && \
    echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | tee -a /etc/apt/sources.list.d/kubernetes.list && \
    apt-get update && apt-get install -y kubectl=${Kubectl_Version}

# Get Terraform
# https://computingforgeeks.com/how-to-install-terraform-on-ubuntu-centos-7/
RUN wget https://releases.hashicorp.com/terraform/${Terraform_Version}/terraform_${Terraform_Version}_linux_amd64.zip && \
    unzip terraform_${Terraform_Version}_linux_amd64.zip && \
    mv terraform /usr/local/bin

# Install Google Cloud CLI
# https://cloud.google.com/sdk/docs/downloads-apt-get
#RUN echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | tee -a /etc/apt/sources.list.d/google-cloud-sdk.list && \
#    apt-get install -y ca-certificates gnupg && \
#    wget -q -O - https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key --keyring /usr/share/keyrings/cloud.google.gpg add - && \
#    apt-get update && apt-get install -y google-cloud-sdk=${Google_Cloud_Cli_Version}

# Get python3 & groff
RUN apt-get install -y python3-pip groff

# Install python2
# RUN apt-get install -y python-minimal

# Get AWS CLI
# https://docs.aws.amazon.com/cli/latest/userguide/install-linux.html#install-linux-awscli
#RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64-${Aws_Cli_Version}.zip" -o "awscliv2.zip" && \
#    unzip awscliv2.zip && \
#    ./aws/install && \
#    rm awscliv2.zip && \
#    rm -rf ./aws

# Get EKS CLI
# https://github.com/weaveworks/eksctl
#RUN curl --silent --location "https://github.com/weaveworks/eksctl/releases/download/${Eks_Cli_Version}/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp && \
#    mv /tmp/eksctl /usr/local/bin

# Get ECS CLI
# https://github.com/aws/amazon-ecs-cli
#RUN curl --silent --location "https://amazon-ecs-cli.s3.amazonaws.com/ecs-cli-linux-amd64-v${Ecs_Cli_Version}" -o /usr/local/bin/ecs-cli && \
#    chmod +x /usr/local/bin/ecs-cli

# Get AWS IAM Authenticator
# https://docs.aws.amazon.com/eks/latest/userguide/install-aws-iam-authenticator.html
#RUN curl --silent --location https://github.com/kubernetes-sigs/aws-iam-authenticator/releases/download/v${Aws_Iam_Authenticator_Version}/aws-iam-authenticator_${Aws_Iam_Authenticator_Version}_linux_amd64 -o /usr/local/bin/aws-iam-authenticator && \
#    chmod +x /usr/local/bin/aws-iam-authenticator

# Get the Istio CLI
# https://istio.io/docs/ops/diagnostic-tools/istioctl/
RUN curl -sL https://istio.io/downloadIstioctl | sh - && \
    mv /root/.istioctl/bin/istioctl /usr/local/bin/istioctl && \
    rm -rf /root/.istioctl

# Get the Linkerd CLI
# https://linkerd.io/2/getting-started/
RUN curl -sL https://run.linkerd.io/install | sh && \
    cp /root/.linkerd2/bin/linkerd /usr/local/bin && \
    rm -rf /root/.linkerd2

# Get tools for working with Docker images without the Docker daemon
# https://github.com/openSUSE/umoci
RUN curl --silent --location https://github.com/opencontainers/umoci/releases/download/v${Umoci_Version}/umoci.amd64 -o /usr/local/bin/umoci && \
    chmod +x /usr/local/bin/umoci

# Get common utilities for scripting
# https://mikefarah.gitbook.io/yq/
# https://augeas.net/
RUN apt-get update && apt-get install -y openssh-client rsync git augeas-tools

# Skopeo
# https://github.com/containers/skopeo/blob/master/install.md
#RUN sh -c "echo 'deb http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_18.04/ /' > /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list" && \
#    wget -nv https://download.opensuse.org/repositories/devel:kubic:libcontainers:stable/xUbuntu_18.04/Release.key -O- | apt-key add - && \
#    apt-get update && apt-get install -y skopeo
