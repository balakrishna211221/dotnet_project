ARG REPO=mcr.microsoft.com/dotnet/aspnet
FROM $REPO:3.1.19-cbl-mariner1.0

ENV \
    # Unset ASPNETCORE_URLS from aspnet base image
    ASPNETCORE_URLS= \
    # Do not generate certificate
    DOTNET_GENERATE_ASPNET_CERTIFICATE=false \
    # Enable correct mode for dotnet watch (only mode supported in a container)
    DOTNET_USE_POLLING_FILE_WATCHER=true \
    # Skip extraction of XML docs - generally not useful within an image/container - helps performance
    NUGET_XMLDOC_MODE=skip \
    # PowerShell telemetry for docker image usage
    POWERSHELL_DISTRIBUTION_CHANNEL=PSDocker-DotnetCoreSDK-CBL-Mariner-1.0

# Install .NET Core SDK
RUN dotnet_sdk_version=3.1.413 \
    dotnet_version=3.1.19 \
    aspnet_version=3.1.19 \
    && curl -SL --output dotnet.rpm https://dotnetcli.azureedge.net/dotnet/Sdk/$dotnet_sdk_version/dotnet-sdk-$dotnet_sdk_version-x64.rpm \
    && dotnet_sha512='4c2ccc9cf5a8aba0d1efae62c7874e95c08937abb8b09c443e928901cddba8a3af069d7b740bfd7d8ad28eb1722d305853e71f569aedf25df2b6dbc6afdf7a39' \
    && echo "$dotnet_sha512  dotnet.rpm" | sha512sum -c - \
    \
    && curl -SL --output apphost.rpm https://dotnetcli.azureedge.net/dotnet/Runtime/$dotnet_version/dotnet-apphost-pack-$dotnet_version-x64.rpm \
    && dotnet_sha512='7b825212fc82289ef0347baa01339c58fe489cc930e37e2e26eee26efca24406117d613235da561742320a60ede89fda65e8099bfe39e09c94b722fc1bff163d' \
    && echo "$dotnet_sha512  apphost.rpm" | sha512sum -c - \
    \
    && curl -SL --output targeting-pack.rpm https://dotnetcli.azureedge.net/dotnet/Runtime/3.1.0/dotnet-targeting-pack-3.1.0-x64.rpm \
    && dotnet_sha512='9111ef4a0083c1fbd969f5adfca8f7b0a2e90eada94771c68ca4d00d34756fb280de153ecce4ad3da4b16698b7add4848f5b6ec24b89a144c2a119a42fb27c85' \
    && echo "$dotnet_sha512  targeting-pack.rpm" | sha512sum -c - \
    \
    && curl -SL --output aspnetcore-targeting-pack.rpm https://dotnetcli.azureedge.net/dotnet/aspnetcore/Runtime/3.1.0/aspnetcore-targeting-pack-3.1.0.rpm \
    && dotnet_sha512='fdab7122896ff3602d8a25c0b283996973ef0cf4f4d0f9eba429d3a93b2441ebe0e0d0b499b97bb28c43cfb386501cc617ca7ed7c949dd3e45c61db0d5879fd4' \
    && echo "$dotnet_sha512  aspnetcore-targeting-pack.rpm" | sha512sum -c - \
    \
    && curl -SL --output netstandard-targeting-pack.rpm https://dotnetcli.azureedge.net/dotnet/Runtime/3.1.0/netstandard-targeting-pack-2.1.0-x64.rpm \
    && dotnet_sha512='fab41a86b9182b276992795247868c093890c6b3d5739376374a302430229624944998e054de0ff99bddd9459fc9543636df1ebd5392db069ae953ac17ea2880' \
    && echo "$dotnet_sha512  netstandard-targeting-pack.rpm" | sha512sum -c - \
    \
    && rpm --install dotnet.rpm apphost.rpm targeting-pack.rpm aspnetcore-targeting-pack.rpm netstandard-targeting-pack.rpm \
    && rm dotnet.rpm apphost.rpm targeting-pack.rpm aspnetcore-targeting-pack.rpm netstandard-targeting-pack.rpm \
    # Trigger first run experience by running arbitrary cmd
    && dotnet help

# Install PowerShell global tool
RUN powershell_version=7.0.7 \
    && curl -SL --output PowerShell.Linux.x64.$powershell_version.nupkg https://pwshtool.blob.core.windows.net/tool/$powershell_version/PowerShell.Linux.x64.$powershell_version.nupkg \
    && powershell_sha512='ebcd8a57b1e19c2b8ab099c2b5ff4206640c5711f75a71ec2cf3493db8c00264b8b42af8589430fc9f14a9065f49528efb230e412253d21cc5ed7c94bea659f1' \
    && echo "$powershell_sha512  PowerShell.Linux.x64.$powershell_version.nupkg" | sha512sum -c - \
    && mkdir -p /usr/share/powershell \
    && dotnet tool install --add-source / --tool-path /usr/share/powershell --version $powershell_version PowerShell.Linux.x64 \
    && dotnet nuget locals all --clear \
    && rm PowerShell.Linux.x64.$powershell_version.nupkg \
    && ln -s /usr/share/powershell/pwsh /usr/bin/pwsh \
    && chmod 755 /usr/share/powershell/pwsh \
    # To reduce image size, remove the copy nupkg that nuget keeps.
    && find /usr/share/powershell -print | grep -i '.*[.]nupkg$' | xargs rm
