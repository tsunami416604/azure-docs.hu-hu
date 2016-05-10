<properties urlDisplayName="Enable remote debugging with continuous delivery" pageTitle="Enable remote debugging with continuous delivery" metaKeywords="" description="Learn how to enable remote debugging when using continuous delivery to deploy to Azure." metaCanonical="" services="cloud-services,virtual machines" documentationCenter=".NET" title="Enable remote debugging when using continuous delivery to publish to Azure" authors="kempb" solutions="" manager="douge" editor="" />

<tags ms.service="multiple" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="kempb"></tags>

# Enable remote debugging when using continuous delivery to publish to Azure

You can enable remote debugging in Azure when you use [continuous delivery][continuous delivery] to publish to Azure by following these steps.

In this topic:

[Enabling remote debugging for cloud services][Enabling remote debugging for cloud services]

[Enabling remote debugging for virtual machines][Enabling remote debugging for virtual machines]

## <a name="cloudservice"></a>Enabling remote debugging for cloud services

1.  On the build agent, set up the initial environment for Azure as outlined in [Command-Line Build for Azure][Command-Line Build for Azure].
2.  Because the remote debug runtime (msvsmon.exe) is required for the package, install the [Remote Tools for Visual Studio 2013][Remote Tools for Visual Studio 2013] (or the [Remote Tools for Visual Studio 2012 Update 4][Remote Tools for Visual Studio 2012 Update 4] if you’re using Visual Studio 2012). As an alternative, you can copy the remote debug binaries from a system that has Visual Studio installed.
3.  Create a certificate as outlined in [Create a Service Certificate for Azure][Create a Service Certificate for Azure]. Keep the .pfx and RDP certificate thumbprint and upload the certificate to the target cloud service.
4.  Use the following options in the MSBuild command line to build and package with remote debug enabled. (Update the paths for your system and project files.)

    /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="C:\\Remote Debugger\\x64\\";RemoteDebuggerConnectorCertificateThumbprint="56D7D1B25B472268E332F7FC0C87286458BFB6B2";RemoteDebuggerConnectorVersion="2.4" "C:\\Users\\yourusername\\Documents\\visual studio 2013\\Projects\\WindowsAzure1\\WindowsAzure1.sln"

5.  Publish to the target cloud service by using the package and .cscfg file generated in the previous step.
6.  Import the certificate (.pfx file) to the machine that has Visual Studio with Azure SDK 2.4 installed.

## <a name="virtualmachine"></a>Enabling remote debugging for virtual machines

1.  Create an Azure virtual machine. See [Create a Virtual Machine Running Windows Server][Create a Virtual Machine Running Windows Server] or [Creating Azure Virtual Machines in Visual Studio][Creating Azure Virtual Machines in Visual Studio].
2.  On the [Azure portal page][Azure portal page], view the virtual machine dashboard to see the virtual machine’s “RDP Certificate Thumbprint”. This is used for the ServerThumbprint value in the extension configuration.
3.  Create a client certificate as outlined in [Create a Service Certificate for Azure][Create a Service Certificate for Azure] (keep the .pfx and RDP certificate thumbprint).
4.  Install [Azure Powershell][Azure Powershell] (version 0.7.4 or later) from the Microsoft Download Center.
5.  Run the following script to enable the RemoteDebug extension. Replace the personal data with your own, such as your subscription name, service name, and thumbprint. (NOTE: This script is configured for Visual Studio 2013. If you’re using Visual Studio 2012, use "RemoteDebugVS2013" for ReferenceName and ExtensionName.)

        Add-AzureAccount

        Select-AzureSubscription "My Microsoft Subscription"

        $vm = Get-AzureVM -ServiceName "mytestvm1" -Name "mytestvm1"

        $endpoints = @(
        ,@{Name="RDConnVS2013"; PublicPort=30400; PrivatePort=30398}
        ,@{Name="RDFwdrVS2013"; PublicPort=31400; PrivatePort=31398}
        )

        foreach($endpoint in $endpoints)
        {
        Add-AzureEndpoint -VM $vm -Name $endpoint.Name -Protocol tcp -PublicPort $endpoint.PublicPort -LocalPort $endpoint.PrivatePort
        }

        $referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2013"
        $publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug"
        $extensionName = "RemoteDebugVS2013"
        $version = "1.*"
        $publicConfiguration = "true56D7D1B25B472268E332F7FC0C87286458BFB6B2E7DCB00CB916C468CC3228261D6E4EE45C8ED3C63039831398"

        $vm | Set-AzureVMExtension `
        -ReferenceName $referenceName `
        -Publisher $publisher `
        -ExtensionName $extensionName `
        -Version $version `
        -PublicConfiguration $publicConfiguration

        foreach($extension in $vm.VM.ResourceExtensionReferences)
        {   
        if(($extension.ReferenceName -eq $referenceName) `
        -and ($extension.Publisher -eq $publisher) `
        -and ($extension.Name -eq $extensionName) `
        -and ($extension.Version -eq $version))
        {
        $extension.ResourceExtensionParameterValues[0].Key = 'config.txt'
        break
        }
        }

        $vm | Update-AzureVM 

6.  Import the certificate (.pfx) to the machine that has Visual Studio with Azure SDK for .NET 2.4 installed.

  [continuous delivery]: http://azure.microsoft.com/en-us/documentation/articles/cloud-services-dotnet-continuous-delivery/
  [Enabling remote debugging for cloud services]: #cloudservice
  [Enabling remote debugging for virtual machines]: #virtualmachine
  [Command-Line Build for Azure]: http://msdn.microsoft.com/en-us/library/hh535755.aspx
  [Remote Tools for Visual Studio 2013]: http://www.microsoft.com/en-us/download/details.aspx?id=40781
  [Remote Tools for Visual Studio 2012 Update 4]: http://www.microsoft.com/en-us/download/details.aspx?id=38184
  [Create a Service Certificate for Azure]: http://msdn.microsoft.com/library/azure/gg432987.aspx
  [Create a Virtual Machine Running Windows Server]: http://azure.microsoft.com/en-us/documentation/articles/virtual-machines-windows-tutorial/
  [Creating Azure Virtual Machines in Visual Studio]: http://msdn.microsoft.com/en-us/library/azure/dn569263.aspx
  [Azure portal page]: http://go.microsoft.com/fwlink/p/?LinkID=269851
  [Azure Powershell]: http://go.microsoft.com/?linkid=9811175&clcid=0x409
