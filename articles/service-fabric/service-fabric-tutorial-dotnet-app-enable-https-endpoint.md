---
title: HTTPS-végpont hozzáadása a Kestrel használatával egy Service Fabric-alkalmazáshoz az Azure-ban | Microsoft Docs
description: Ez az oktatóanyag azt ismerteti, hogyan adhat hozzá HTTPS-végpontot egy ASP.NET Core előtér-webszolgáltatáshoz a Kestrel használatával, és hogyan helyezheti üzembe az alkalmazást egy fürtön.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/17/2019
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 541d1473b21056e24c6b04b86414936a02b7d9d5
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54382579"
---
# <a name="tutorial-add-an-https-endpoint-to-an-aspnet-core-web-api-front-end-service-using-kestrel"></a>Oktatóanyag: HTTPS-végpont hozzáadása egy ASP.NET Core Web API kezelőfelületi szolgáltatás a Kestrel használatával

Ez az oktatóanyag egy sorozat harmadik része.  Az oktatóanyag során el fogja sajátítani a HTTPS engedélyezését a Service Fabricen futó ASP.NET Core-szolgáltatásban. Ha ezzel végzett, rendelkezni fog egy HTTPS-kompatibilis ASP.NET Core webes kezelőfelületes szavazóalkalmazással, amely a 443-as porton figyel. Ha nem szeretné manuálisan létrehozni a szavazóalkalmazást a [NET Service Fabric-alkalmazás létrehozása](service-fabric-tutorial-deploy-app-to-party-cluster.md) szakaszban, akkor [letöltheti a forráskódot](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) a kész alkalmazáshoz.

A sorozat harmadik részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * HTTPS-végpont meghatározása a szolgáltatásban
> * A Kestrel konfigurálása HTTPS használatára
> * Az SSL-tanúsítvány telepítése a távoli fürtcsomópontokon
> * Hálózatiszolgáltatás-hozzáférés hozzáadása a tanúsítvány titkos kulcsához
> * A 443-as port megnyitása az Azure Load Balancerben
> * Az alkalmazás üzembe helyezése egy távoli fürtön

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [.NET Service Fabric-alkalmazás létrehozása](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Az alkalmazás üzembe helyezése egy távoli fürtön](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * HTTPS-végpont hozzáadása ASP.NET Core kezelőfelületi szolgáltatáshoz
> * [A CI/CD konfigurálása az Azure Pipelines használatával](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Figyelés és diagnosztika beállítása az alkalmazáshoz](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Telepítse a Visual Studio 2017](https://www.visualstudio.com/) 15.5-ös vagy újabb verzióját az **Azure-fejlesztési** és az **ASP.NET- és webfejlesztési** számítási feladattal.
* [A Service Fabric SDK telepítése](service-fabric-get-started.md)

## <a name="obtain-a-certificate-or-create-a-self-signed-development-certificate"></a>Tanúsítvány beszerzése vagy egy önaláírt fejlesztési tanúsítvány létrehozása

Éles alkalmazásokhoz használjon [hitelesítésszolgáltatói (CA-)](https://wikipedia.org/wiki/Certificate_authority) tanúsítványt. Fejlesztési és tesztelési célok érdekében létrehozhat és használhat önaláírt tanúsítványt. A Service Fabric SDK biztosítja a *CertSetup.ps1* szkriptet, amely létrehozza az önaláírt tanúsítványt, és importálja azt a `Cert:\LocalMachine\My` tanúsítványtárolóba. Nyisson meg egy parancssort rendszergazdaként, és a következő paranccsal hozzon létre egy tanúsítványt a tulajdonos "CN = mytestcert":

```powershell
PS C:\program files\microsoft sdks\service fabric\clustersetup\secure> .\CertSetup.ps1 -Install -CertSubjectName CN=mytestcert
```

Ha már rendelkezik a tanúsítvány PFX kiterjesztésű fájljával, a következő lekérdezés futtatásával importálhatja a tanúsítványt a `Cert:\LocalMachine\My` tanúsítványtárolóba:

```powershell

PS C:\mycertificates> Import-PfxCertificate -FilePath .\mysslcertificate.pfx -CertStoreLocation Cert:\LocalMachine\My -Password (ConvertTo-SecureString "!Passw0rd321" -AsPlainText -Force)


   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

## <a name="define-an-https-endpoint-in-the-service-manifest"></a>HTTPS-végpont meghatározása a szolgáltatásjegyzékben

Indítsa el a Visual Studiót **rendszergazdaként**, és nyissa meg a Választási megoldást. A Megoldáskezelőben nyissa meg a következőt: *VotingWeb/PackageRoot/ServiceManifest.xml*. A szolgáltatásjegyzék meghatározza a szolgáltatásvégpontokat.  Keresse meg a **Végpontok** szakaszt, és szerkessze a meglévő ServiceEndpoint végpontot.  Módosítsa a nevet VégpontHttps névre, és állítsa be a protokollt *https*, a típust *Bemenet*, a portot pedig *443* értékre.  Mentse a módosításokat.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="configure-kestrel-to-use-https"></a>A Kestrel konfigurálása HTTPS használatára

A Megoldáskezelőben nyissa meg a következő fájlt: *VotingWeb/VotingWeb.cs*.  A Kestrel konfigurálása HTTPS használatára és tanúsítvány megkeresésére a `Cert:\LocalMachine\My` tárolóban. Adja hozzá a következő using utasításokat:

```csharp
using System.Net;
using Microsoft.Extensions.Configuration;
using System.Security.Cryptography.X509Certificates;
```

Frissítse a `ServiceInstanceListener` elemet úgy, hogy az új *VégpontHttps* végpontot használja, és a 443-as porton figyeljen. Ha Kestrel-kiszolgáló használatára konfigurálja a webgazdát, a Kestrelt minden hálózati adapteren az IPv6-címek figyelésére kell konfigurálni: `opt.Listen(IPAddress.IPv6Any, port, listenOptions => {...}`.

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

Adja hozzá a következő metódust, hogy a Kestrel megtalálja a tanúsítványt a `Cert:\LocalMachine\My` tárolóban a tárgy segítségével.  

Cserélje le "&lt;saját_cn_értéke&gt;" a "mytestcert", ha az előző PowerShell-paranccsal önaláírt tanúsítványt létrehozni, vagy a tanúsítványt használja.

```csharp
private X509Certificate2 GetCertificateFromStore()
{
    var store = new X509Store(StoreName.My, StoreLocation.LocalMachine);
    try
    {
        store.Open(OpenFlags.ReadOnly);
        var certCollection = store.Certificates;
        var currentCerts = certCollection.Find(X509FindType.FindBySubjectDistinguishedName, "CN=<your_CN_value>", false);
        return currentCerts.Count == 0 ? null : currentCerts[0];
    }
    finally
    {
        store.Close();
    }
}
```

## <a name="give-network-service-access-to-the-certificates-private-key"></a>Hálózatiszolgáltatás-hozzáférés hozzáadása a tanúsítvány titkos kulcsához

Előzőleg importálta a tanúsítványt a fejlesztői számítógép `Cert:\LocalMachine\My` tárolójába.  Explicit módon biztosítania kell a hozzáférést a szolgáltatást futtató fióknak (HÁLÓZATI SZOLGÁLTATÁS, alapértelmezés szerint) a tanúsítvány titkos kulcsához. Ezt megteheti manuálisan is (a certlm.msc eszköz használatával), de javasolt inkább automatikusan futtatni egy PowerShell-szkriptet. Ehhez [konfiguráljon egy indítási szkriptet](service-fabric-run-script-at-service-startup.md) a szolgáltatásjegyzék **SetupEntryPoint** pontjában.

### <a name="configure-the-service-setup-entry-point"></a>Szolgáltatásbeállítás belépési pontjának konfigurálása

A Megoldáskezelőben nyissa meg a következőt: *VotingWeb/PackageRoot/ServiceManifest.xml*.  A **CodePackage** szakaszban adja hozzá a **SetupEntryPoint** csomópontot, majd az **ExeHost** csomópontot.  Az **ExeHost** elemben állítsa be a **Program** értékét Setup.bat értékre, a **WorkingFolder** értékét pedig CodePackage értékre.  A VotingWeb szolgáltatás elindulásakor a rendszer futtatja a Setup.bat szkriptet a CodePackage mappában, még a VotingWeb.exe elindulása előtt.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Setup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>

    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

### <a name="add-the-batch-and-powershell-setup-scripts"></a>A köteg és a PowerShell beállítási szkriptjeinek hozzáadása

A PowerShell a **SetupEntryPoint** pontból való futtatásához futtathatja a PowerShell.exe fájlt egy kötegfájlban, amely egy PowerShell-fájlra mutat. Először adja hozzá a kötegfájlt a szolgáltatásprojekthez.  A Megoldáskezelőben kattintson a jobb gombbal a **VotingWeb** pontra, és válassza az **Add**->**New Item** (Hozzáadás, Új elem) pontot, majd adjon hozzá egy új fájlt Setup.bat névvel.  Szerkessze a *Setup.bat* fájlt, és adja hozzá az alábbi parancsot:

```bat
powershell.exe -ExecutionPolicy Bypass -Command ".\SetCertAccess.ps1"
```

Módosítsa a *Setup.bat* fájltulajdonságokat a **Copy to Output Directory** (Másolás a kimeneti könyvtárba) elem Copy if newer (Másolás, ha újabb) értékre állításához.

![Fájltulajdonságok beállítása][image1]

A Megoldáskezelőben kattintson a jobb gombbal a **VotingWeb** pontra, és válassza az **Add**->**New Item** (Hozzáadás, Új elem) pontot, majd adjon hozzá egy új fájlt SetCertAccess.ps1 névvel.  Szerkessze a *SetCertAccess.ps1* fájlt, és adja hozzá az alábbi szkriptet:

```powershell
$subject="mytestcert"
$userGroup="NETWORK SERVICE"

Write-Host "Checking permissions to certificate $subject.." -ForegroundColor DarkCyan

$cert = (gci Cert:\LocalMachine\My\ | where { $_.Subject.Contains($subject) })[-1]

if ($cert -eq $null)
{
    $message="Certificate with subject:"+$subject+" does not exist at Cert:\LocalMachine\My\"
    Write-Host $message -ForegroundColor Red
    exit 1;
}elseif($cert.HasPrivateKey -eq $false){
    $message="Certificate with subject:"+$subject+" does not have a private key"
    Write-Host $message -ForegroundColor Red
    exit 1;
}else
{
    $keyName=$cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName

    $keyPath = "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys\"
    $fullPath=$keyPath+$keyName
    $acl=(Get-Item $fullPath).GetAccessControl('Access')


    $hasPermissionsAlready = ($acl.Access | where {$_.IdentityReference.Value.Contains($userGroup.ToUpperInvariant()) -and $_.FileSystemRights -eq [System.Security.AccessControl.FileSystemRights]::FullControl}).Count -eq 1

    if ($hasPermissionsAlready){
        Write-Host "Account $userGroup already has permissions to certificate '$subject'." -ForegroundColor Green
        return $false;
    } else {
        Write-Host "Need add permissions to '$subject' certificate..." -ForegroundColor DarkYellow

        $permission=$userGroup,"Full","Allow"
        $accessRule=new-object System.Security.AccessControl.FileSystemAccessRule $permission
        $acl.AddAccessRule($accessRule)
        Set-Acl $fullPath $acl

        Write-Output "Permissions were added"

        return $true;
    }
}

```

Módosítsa a *SetCertAccess.ps1* fájltulajdonságokat a **Copy to Output Directory** (Másolás a kimeneti könyvtárba) elem Copy if newer (Másolás, ha újabb) értékre állításához.

### <a name="run-the-setup-script-as-a-local-administrator"></a>Beállítási szkript futtatása helyi rendszergazdaként

Alapértelmezés szerint a szolgáltatásbeállítás belépési pontjának végrehajtható fájlja ugyanolyan hitelesítő adatok alatt fut, mint a Service Fabric (általában a Hálózati szolgáltatási fiók). A *SetCertAccess.ps1* rendszergazdai jogosultságokat igényel. Az alkalmazásjegyzékben módosíthatja a biztonsági engedélyeket az indítási szkript futtatásához, egy helyi rendszergazdai fiókkal.

A Solution Explorerben (Megoldáskezelő) nyissa meg a *Voting/ApplicationPackageRoot/ApplicationManifest.xml* fájlt. Először hozzon létre egy **Rendszerbiztonsági tagok** szakaszt, és adjon hozzá új felhasználót (például SetupAdminUser). Adja hozzá a SetupAdminUser felhasználói fiókot a rendszergazdák rendszercsoportjához.
Ezután a VotingWebPkg **ServiceManifestImport** szakaszban konfigurálja a **RunAsPolicy** elemet, hogy alkalmazza a SetupAdminUser rendszerbiztonsági tagot a beállítási belépési pontra. Ez a szabályzat tájékoztatja a Service Fabricet, hogy a Setup.bat fájl SetupAdminUser-ként fut (rendszergazdai jogosultságokkal).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="run-the-application-locally"></a>Az alkalmazás helyi futtatása

A Megoldáskezelőben válassza ki a **Szavazó**alkalmazást, és állítsa be az **Alkalmazás URL-címe** tulajdonságot a következőre: https://localhost:443.

Mentse a fájlokat és nyomja le az F5 billentyűt az alkalmazás helyi futtatásához.  Az alkalmazás üzembe helyezése után a webböngésző a [https://localhost:443](https://localhost:443) címet nyitja meg. Ha önaláírt tanúsítványt használ, látni fog egy figyelmeztetést, amely szerint a számítógépe nem bízik az adott webhely biztonságában.  Tovább a weblapra.

![Szavazóalkalmazás][image2]

## <a name="install-certificate-on-cluster-nodes"></a>Tanúsítvány telepítése fürtcsomópontokon

Az Azure-ban az alkalmazás üzembe helyezése előtt telepítse a tanúsítványt a `Cert:\LocalMachine\My` tárolójában, a távoli fürt összes csomópontján.  Szolgáltatások áthelyezheti a fürt más csomópontjaira.  Ha az előtér-webszolgáltatás elindul a fürtcsomóponton, az indítási szkript megkeresi a tanúsítványt, és konfigurálja a hozzáférési engedélyeket.

Először exportálja a tanúsítványt egy PFX-fájlba. Nyissa meg a certlm.msc alkalmazást, majd keresse meg a **Személyes**>**tanúsítványok** elemet.  Kattintson a jobb gombbal a *mytestcert* tanúsítványra, és válassza **feladatok**>**exportálása**.

![Tanúsítvány exportálása][image4]

Az exportálási varázslóban válassza az **Igen, a titkos kulcs exportálását választom** lehetőséget, majd a Személyes információcsere (PFX) formátumot.  Exportálja a fájlt a *C:\Users\sfuser\votingappcert.pfx* helyre.

Ezután telepítse a tanúsítványt a távoli fürtön az [Add-AzureRmServiceFabricApplicationCertificate](/powershell/module/azurerm.servicefabric/Add-AzureRmServiceFabricApplicationCertificate) parancsmag segítségével.

> [!Warning]
> Az önaláírt tanúsítvány elegendő alkalmazások fejlesztéséhez és teszteléséhez. Éles alkalmazásokhoz használja a [hitelesítésszolgáltatói (CA-)](https://wikipedia.org/wiki/Certificate_authority) tanúsítványt az önaláírt tanúsítvány helyett.

```powershell
Connect-AzureRmAccount

$vaultname="sftestvault"
$certname="VotingAppPFX"
$certpw="!Password321#"
$groupname="voting_RG"
$clustername = "votinghttps"
$ExistingPfxFilePath="C:\Users\sfuser\votingappcert.pfx"

$appcertpwd = ConvertTo-SecureString -String $certpw -AsPlainText -Force

Write-Host "Reading pfx file from $ExistingPfxFilePath"
$cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2 $ExistingPfxFilePath, $certpw

$bytes = [System.IO.File]::ReadAllBytes($ExistingPfxFilePath)
$base64 = [System.Convert]::ToBase64String($bytes)

$jsonBlob = @{
   data = $base64
   dataType = 'pfx'
   password = $certpw
   } | ConvertTo-Json

$contentbytes = [System.Text.Encoding]::UTF8.GetBytes($jsonBlob)
$content = [System.Convert]::ToBase64String($contentbytes)

$secretValue = ConvertTo-SecureString -String $content -AsPlainText -Force

# Upload the certificate to the key vault as a secret
Write-Host "Writing secret to $certname in vault $vaultname"
$secret = Set-AzureKeyVaultSecret -VaultName $vaultname -Name $certname -SecretValue $secretValue

# Add a certificate to all the VMs in the cluster.
Add-AzureRmServiceFabricApplicationCertificate -ResourceGroupName $groupname -Name $clustername -SecretIdentifier $secret.Id -Verbose
```

## <a name="open-port-443-in-the-azure-load-balancer"></a>A 443-as port megnyitása az Azure Load Balancerben

Nyissa meg a 443-as portot a Load Balancerben, ha még nem tette meg.

```powershell
$probename = "AppPortProbe6"
$rulename="AppPortLBRule6"
$RGname="voting_RG"
$port=443

# Get the load balancer resource
$resource = Get-AzureRmResource | Where {$_.ResourceGroupName –eq $RGname -and $_.ResourceType -eq "Microsoft.Network/loadBalancers"}
$slb = Get-AzureRmLoadBalancer -Name $resource.Name -ResourceGroupName $RGname

# Add a new probe configuration to the load balancer
$slb | Add-AzureRmLoadBalancerProbeConfig -Name $probename -Protocol Tcp -Port $port -IntervalInSeconds 15 -ProbeCount 2

# Add rule configuration to the load balancer
$probe = Get-AzureRmLoadBalancerProbeConfig -Name $probename -LoadBalancer $slb
$slb | Add-AzureRmLoadBalancerRuleConfig -Name $rulename -BackendAddressPool $slb.BackendAddressPools[0] -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -Probe $probe -Protocol Tcp -FrontendPort $port -BackendPort $port

# Set the goal state for the load balancer
$slb | Set-AzureRmLoadBalancer
```

## <a name="deploy-the-application-to-azure"></a>Az alkalmazás központi telepítése az Azure-ban

Mentsen minden fájlt, váltson Hibakeresésről Kiadásra, majd nyomja le az F6 billentyűt az újraépítéshez.  A Megoldáskezelőben kattintson a jobb gombbal a **Szavazás** elemre, majd válassza a **Közzététel** lehetőséget. Válassza ki az [Alkalmazás üzembe helyezése egy fürtön](service-fabric-tutorial-deploy-app-to-party-cluster.md) területen létrehozott fürt kapcsolati végpontját, vagy válasszon ki egy másik fürtöt.  Kattintson a **Közzététel** gombra az alkalmazás közzétételéhez a távoli fürtön.

Az alkalmazás üzembe helyezése után nyisson meg egy webböngészőt, majd keresse meg a következő elemet: [https://mycluster.region.cloudapp.azure.com:443](https://mycluster.region.cloudapp.azure.com:443) (frissítse az URL-címet a fürtjéhez tartozó kapcsolati végponttal). Ha önaláírt tanúsítványt használ, látni fog egy figyelmeztetést, amely szerint a számítógépe nem bízik az adott webhely biztonságában.  Tovább a weblapra.

![Szavazóalkalmazás][image3]

## <a name="next-steps"></a>További lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:

> [!div class="checklist"]
> * HTTPS-végpont meghatározása a szolgáltatásban
> * A Kestrel konfigurálása HTTPS használatára
> * Az SSL-tanúsítvány telepítése a távoli fürtcsomópontokon
> * Hálózatiszolgáltatás-hozzáférés hozzáadása a tanúsítvány titkos kulcsához
> * A 443-as port megnyitása az Azure Load Balancerben
> * Az alkalmazás üzembe helyezése egy távoli fürtön

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [A CI/CD konfigurálása az Azure Pipelines használatával](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)

[image1]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/SetupBatProperties.png
[image2]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/VotingAppLocal.png
[image3]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/VotingAppAzure.png
[image4]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/ExportCert.png
