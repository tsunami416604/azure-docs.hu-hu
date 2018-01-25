---
title: "Azure Service Fabric-tárolóalkalmazás létrehozása | Microsoft Docs"
description: "Hozza létre első saját, Windows-alapú tárolóalkalmazását az Azure Service Fabricban.  Egy Python-alkalmazással elkészíthet egy Docker-rendszerképet, amelyet leküldéssel továbbíthat egy tárolóregisztrációs adatbázisba, majd összeállíthat és üzembe helyezhet egy Service Fabric-tárolóalkalmazást."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/19/2018
ms.author: ryanwi
ms.openlocfilehash: cc616c05f7f0579653b793f2c364782f4a35d7f9
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/20/2018
---
# <a name="create-your-first-service-fabric-container-application-on-windows"></a>Az első Service Fabric-tárolóalkalmazás létrehozása Windows rendszeren
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

A meglévő alkalmazások Service Fabric-fürtökön lévő Windows-tárolókban való futtatásához nem szükséges módosítania az alkalmazást. Ez a cikk ismerteti a Python [Flask](http://flask.pocoo.org/)-webalkalmazást tartalmazó Docker-rendszerképek létrehozását, illetve egy Service Fabric-fürtön való üzembe helyezését.  Emellett meg is fogja osztani a tárolóalapú alkalmazást az [Azure Container Registry](/azure/container-registry/) használatával.  A cikk feltételezi, hogy rendelkezik a Docker használatára vonatkozó alapvető ismeretekkel. A Docker megismeréséhez olvassa el a [Docker áttekintő ismertetését](https://docs.docker.com/engine/understanding-docker/).

## <a name="prerequisites"></a>Előfeltételek
Egy fejlesztői számítógép, amelyen a következők futnak:
* Visual Studio 2015 vagy Visual Studio 2017.
* [Service Fabric SDK és -eszközök](service-fabric-get-started.md).
*  Windows rendszerhez készült Docker.  [A Docker CE for Windows (stable) letöltése](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Miután telepítette és elindította a Dockert, kattintson a jobb gombbal a tálca ikonjára, és válassza a **Switch to Windows containers** (Váltás Windows-tárolókra) lehetőséget. Ez szükséges ahhoz, hogy Windows-alapú Docker-rendszerképeket tudjon futtatni.

Egy Windows-fürt legalább három, Windows Server 2016 rendszerű, a Containerst futtató csomóponttal. Ehhez [hozzon létre egy fürtöt](service-fabric-cluster-creation-via-portal.md) vagy [próbálja ki ingyen a Service Fabricot](https://aka.ms/tryservicefabric).

Egy Azure Container Registry-beállításjegyzék – ehhez [hozzon létre egy tároló-beállításjegyzéket](../container-registry/container-registry-get-started-portal.md) Azure-előfizetésében.

> [!NOTE]
> A tárolók üzembe helyezése Service Fabric-fürtökön a Windows 10-ben vagy Docker CE-t futtató fürtökön nem támogatott. Ez az útmutató helyi ellenőrzéseket végez a Docker-motor használatával a Windows 10 rendszeren, majd végül egy Azure-beli, a Docker CE-t futtató Windows Server-fürtön helyezi üzembe a tárolószolgáltatásokat. 
>   

> [!NOTE]
> A Service Fabric 6.1-es verziója előzetes verzióban támogatja a Windows Server 1709-es verzióját. A nyílt hálózatkezelés és a Service Fabric DNS-szolgáltatás nem működik a Windows Server 1709-es verziójával. 
> 

## <a name="define-the-docker-container"></a>A Docker-tároló definiálása
Állítson össze egy rendszerképet a Docker Hubban található [Python-rendszerkép](https://hub.docker.com/_/python/) alapján.

Definiálja a Docker-tárolót egy Docker-fájlban. A Docker-fájl tartalmazza a környezet tárolón belüli beállítására, a futtatni kívánt alkalmazás betöltésére és a portok hozzárendelésére vonatkozó utasításokat. A Docker-fájl a `docker build` parancs bemenete, amely a rendszerképet létrehozza.

Hozzon létre egy üres könyvtárat és a *Docker-fájlt* (fájlkiterjesztés nélkül). Adja hozzá a következőket a *Docker-fájlhoz*, és mentse a módosításokat:

```
# Use an official Python runtime as a base image
FROM python:2.7-windowsservercore

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

További információkért olvassa el a [Docker-fájlra vonatkozó referenciákat](https://docs.docker.com/engine/reference/builder/).

## <a name="create-a-simple-web-application"></a>Egyszerű webalkalmazás létrehozása
Hozzon létre egy olyan Flask-webalkalmazást, amely a 80-as portot figyeli, és a „Hello World!” szöveget adja vissza.  Ugyanebben a könyvtárban hozza létre a *requirements.txt* fájlt.  Adja hozzá a következőket, és mentse a módosításokat:
```
Flask
```

Hozza létre az *app.py* fájlt, és adja hozzá a következőket:

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():

    return 'Hello World!'

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

<a id="Build-Containers"></a>
## <a name="build-the-image"></a>Rendszerkép létrehozása
Futtassa a(z) `docker build` parancsot a webalkalmazást futtató rendszerkép létrehozásához. Nyisson meg egy PowerShell-ablakot, és lépjen a Docker-fájlt tartalmazó könyvtárra. Futtassa az alábbi parancsot:

```
docker build -t helloworldapp .
```

Ez a parancs létrehozza az új rendszerképet a Docker-fájlban foglalt utasítások alapján, és elnevezi (-t címkézés) a rendszerképet „helloworldapp”-nak. A rendszerképek készítése során a rendszer lekéri az alaprendszerképet a Docker Hubból, és létrehoz egy olyan új rendszerképet, amelyben az alkalmazás hozzá van adva az alaprendszerképhez.  

Miután az összeállító parancs lefutott, futtassa a `docker images` parancsot az új rendszerkép információinak megtekintéséhez:

```
$ docker images

REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              8ce25f5d6a79        2 minutes ago       10.4 GB
```

## <a name="run-the-application-locally"></a>Az alkalmazás helyi futtatása
Ellenőrizze helyben a rendszerkép működését, mielőtt leküldené azt a tároló-beállításjegyzékbe.  

Futtassa az alkalmazást:

```
docker run -d --name my-web-site helloworldapp
```

A *name* nevet ad a futtató tárolónak (a tárolóazonosító helyett).

Miután a tároló elindult, keresse meg az IP-címét, hogy böngészőből is el tudja érni a futó tárolót:
```
docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
```

Csatlakozzon a futó tárolóhoz.  Nyisson meg egy webböngészőt, majd a visszaadott IP-címet, például „http://172.31.194.61”. A „Hello World!” címsornak kell megjelennie a böngészőben.

A tároló leállításához futtassa a következő parancsot:

```
docker stop my-web-site
```

Törölje a tárolót a fejlesztői gépről:

```
docker rm my-web-site
```

<a id="Push-Containers"></a>
## <a name="push-the-image-to-the-container-registry"></a>A rendszerkép leküldése a tároló-beállításjegyzékbe
Miután ellenőrizte, hogy a tároló fut-e a fejlesztői gépen, küldje le a rendszerképet a beállításjegyzékébe az Azure Container Registryben.

Futtassa a(z) ``docker login`` parancsot a tároló-beállításjegyzékbe való bejelentkezéshez a [beállításjegyzékhez tartozó hitelesítő adataival](../container-registry/container-registry-authentication.md).

Az alábbi példában a rendszer egy Azure Active Directory [egyszerű szolgáltatás](../active-directory/active-directory-application-objects.md) azonosítóját és jelszavát adja át. Például lehet, hogy hozzárendelt egy egyszerű szolgáltatást a beállításjegyzékhez egy automatizálási forgatókönyvhöz. Vagy bejelentkezhet a beállításjegyzékhez tartozó felhasználónevével és jelszavával.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

A következő parancs létrehoz egy címkét vagy aliast a rendszerképről a beállításjegyzékre mutató teljes elérési úttal. Az alábbi példa a rendszerképet a ```samples``` névtérben helyezi el, hogy ne legyen zsúfolt a beállításjegyzék gyökere.

```
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Küldje le a rendszerképet tároló-beállításjegyzékbe:

```
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="create-the-containerized-service-in-visual-studio"></a>A tárolóalapú szolgáltatás létrehozása a Visual Studióban
A Service Fabric SDK és -eszközök egy szolgáltatássablont biztosítanak, amellyel tárolóalapú alkalmazást hozhat létre.

1. Indítsa el a Visual Studiót.  Válassza a **File** (Fájl) > **New** (Új) > **Project** (Projekt) lehetőséget.
2. Válassza a **Service Fabric application** (Service Fabric-alkalmazás) lehetőséget, nevezze el „MyFirstContainer” néven, és kattintson az **OK** gombra.
3. Az **szolgáltatássablonok** listájában válassza a **Tároló** elemet.
4. Az **Image Name** (Rendszerkép neve) mezőben adja meg a „myregistry.azurecr.io/samples/helloworldapp” rendszerképet, amelyet leküldött a tároló-beállításjegyzékbe.
5. Nevezze el a szolgáltatást, és kattintson az **OK** gombra.

## <a name="configure-communication"></a>A kommunikáció konfigurálása
A tárolóalapú szolgáltatáshoz szükség van egy kommunikációs végpontra. Adja hozzá a protokollt, a portot és a típust tartalmazó `Endpoint` elemet a servicemanifest.xml fájlhoz. Ebben a cikkben a tárolóalapú szolgáltatás a 8081-es portot figyeli.  Ez a példa egy rögzített 8081-es portot használ erre a célra.  Ha nincs megadva port, a rendszer egy véletlenszerű portot választ az alkalmazás porttartományából.  

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
  </Endpoints>
</Resources>
```

Egy végpont megadásával a Service Fabric közzéteszi a végpontot az elnevezési szolgáltatásban.  A fürtben futó más szolgáltatások feloldhatják ezt a tárolót.  Tárolók közötti kommunikációt is folytathat a [fordított proxyval](service-fabric-reverseproxy.md).  A kommunikációhoz környezeti változókként adja meg a fordított proxy HTTP-figyelő portját és azon szolgáltatások nevét, amelyekkel kommunikálni kíván.

## <a name="configure-and-set-environment-variables"></a>Környezeti változók konfigurálása és beállítása
A szolgáltatásjegyzékben minden kódcsomaghoz megadhatók környezeti változók. Ez a funkció az összes szolgáltatáshoz elérhető attól függetlenül, hogy tárolókként, folyamatokként vagy vendég futtatható fájlokként vannak-e üzembe helyezve. A környezeti változó értékeit felülbírálhatja az alkalmazásjegyzékben, vagy az üzembe helyezés alatt megadhatja őket alkalmazásparaméterekként.

A következő szolgáltatásjegyzékbeli XML-kódrészlet arra mutat be egy példát, hogyan adhat meg környezeti változókat egy kódcsomaghoz:
```xml
<CodePackage Name="Code" Version="1.0.0">
  ...
  <EnvironmentVariables>
    <EnvironmentVariable Name="HttpGatewayPort" Value=""/>    
  </EnvironmentVariables>
</CodePackage>
```

Ezek a környezeti változók bírálhatók felül az alkalmazásjegyzékben:

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
  </EnvironmentOverrides>
  ...
</ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping-and-container-to-container-discovery"></a>Tárolóport–gazdagépport hozzárendelés és tároló–tároló felderítés konfigurálása
Konfiguráljon egy gazdagépportot a tárolóval való kommunikációhoz. A portkötés a gazdagép egyik portjához rendeli hozzá a szolgáltatás által figyelt tárolóportot. Adjon hozzá egy `PortBinding` elemet az ApplicationManifest.xml fájl `ContainerHostPolicies` eleméhez.  Ebben a cikkben a `ContainerPort` értéke 80 (a tároló a 80-as portot használja a Docker-fájlban foglalt beállítások szerint), az `EndpointRef` pedig „Guest1TypeEndpoint” (a szolgáltatásjegyzékben korábban definiált végpont).  A szolgáltatáshoz a 8081-es porton beérkező kérések a tárolón a 80-as portra vannak leképezve.

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="configure-container-registry-authentication"></a>Tárolóregisztrációs adatbázis hitelesítésének konfigurálása
A tárolóregisztrációs adatbázis hitelesítésének konfigurálásához adja a hozzá a `RepositoryCredentials` elemet az ApplicationManifest.xml fájl `ContainerHostPolicies` eleméhez. Adja meg a myregistry.azurecr.io tárolóregisztrációs adatbázis fiókját és jelszavát, hogy a szolgáltatás le tudja tölteni a tároló rendszerképét az adattárból.

```xml
<Policies>
    <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
    </ContainerHostPolicies>
</Policies>
```

Javasoljuk, hogy az adattár jelszavát egy olyan titkosítási tanúsítvánnyal titkosítsa, amely a fürt minden csomópontján üzembe van helyezve. Amikor a Service Fabric üzembe helyezi a szervizcsomagot a fürtön, a titkosítási tanúsítvánnyal fejti vissza a titkosított szöveget.  Az Invoke-ServiceFabricEncryptText parancsmaggal hozhat létre titkosított szöveget a jelszóhoz, amelyet a rendszer hozzáad az ApplicationManifest.xml fájlhoz.

A következő szkript létrehoz egy új önaláírt tanúsítványt, és exportálja egy PFX-fájlba.  A rendszer egy meglévő kulcstárolóba importálja a tanúsítványt, majd üzembe helyezi a Service Fabric-fürtön.

```powershell
# Variables.
$certpwd = ConvertTo-SecureString -String "Pa$$word321!" -Force -AsPlainText
$filepath = "C:\MyCertificates\dataenciphermentcert.pfx"
$subjectname = "dataencipherment"
$vaultname = "mykeyvault"
$certificateName = "dataenciphermentcert"
$groupname="myclustergroup"
$clustername = "mycluster"

$subscriptionId = "subscription ID"

Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId

# Create a self signed cert, export to PFX file.
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject $subjectname -Provider 'Microsoft Enhanced Cryptographic Provider v1.0' `
| Export-PfxCertificate -FilePath $filepath -Password $certpwd

# Import the certificate to an existing key vault.  The key vault must be enabled for deployment.
$cer = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certificateName -FilePath $filepath -Password $certpwd

Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $groupname -EnabledForDeployment

# Add the certificate to all the VMs in the cluster.
Add-AzureRmServiceFabricApplicationCertificate -ResourceGroupName $groupname -Name $clustername -SecretIdentifier $cer.SecretId
```
Titkosítsa a jelszót az [Invoke-ServiceFabricEncryptText](/powershell/module/servicefabric/Invoke-ServiceFabricEncryptText?view=azureservicefabricps) parancsmaggal.

```powershell
$text = "=P==/==/=8=/=+u4lyOB=+=nWzEeRfF="
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint $cer.Thumbprint -Text $text -StoreLocation Local -StoreName My
```

Cserélje le a jelszót az [Invoke-ServiceFabricEncryptText](/powershell/module/servicefabric/Invoke-ServiceFabricEncryptText?view=azureservicefabricps) parancsmag által visszaadott titkosított szövegre, és állítsa a `PasswordEncrypted` tulajdonságot „true” (igaz) értékre.

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
    <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="configure-isolation-mode"></a>Az elkülönítési mód konfigurálása
A Windows a tárolók két elkülönítési módját támogatja: a folyamatalapú és a Hyper-V módot. Folyamatelkülönítési módban az ugyanazon a gazdagépen futó összes tároló ugyanazt a kernelt használja, mint a gazdagép. Hyper-V elkülönítési módban az egyes Hyper-V tárolók és a tároló gazdagép kernelei elkülönülnek. Az elkülönítési mód az alkalmazásjegyzék-fájl `ContainerHostPolicies` elemében van meghatározva. A megadható elkülönítési módok a következők: `process`, `hyperv` és `default`. Az elkülönítési mód alapértelmezett értéke a Windows Server-gazdagépeken `process`, a Windows 10-gazdagépeken pedig `hyperv`. A következő kódrészlet azt mutatja be, hogyan van határozható meg az elkülönítési mód az alkalmazásjegyzék-fájlban.

```xml
<ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
```
   > [!NOTE]
   > A HyperV elkülönítési módja az Azure beágyazott virtualizálástámogatással rendelkező Ev3 és Dv3 SKU-ján érhető el. 
   >
   >

## <a name="configure-resource-governance"></a>Az erőforrás-szabályozás konfigurálása
Az [erőforrás-szabályozás](service-fabric-resource-governance.md) korlátozza a tároló által a gazdagépen használható erőforrásokat. Az alkalmazásjegyzékben megadott `ResourceGovernancePolicy` elemmel határozhatók meg erőforráskorlátok a szolgáltatások kódcsomagjaihoz. A következő erőforrásokhoz állíthatók be erőforráskorlátok: Memory, MemorySwap, CpuShares (CPU relatív súlya), MemoryReservationInMB, BlkioWeight (BlockIO relatív súlya).  Ebben a példában a Guest1Pkg szolgáltatáscsomag egy magot kap a fürtcsomópontokon, amelyekre el van helyezve.  A memóriakorlátok abszolútak, ezért a kódcsomag 1024 MB memóriára van korlátozva (és ugyanennyi a gyenge garanciás foglalás). A kódcsomagok (tárolók vagy folyamatok) nem tudnak ennél a korlátnál több memóriát lefoglalni, és ennek megkísérlése memóriahiány miatti kivételt eredményez. Az erőforráskorlát érvényesítéséhez a szolgáltatáscsomagokban lévő minden kódcsomaghoz memóriakorlátokat kell meghatároznia.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <Policies>
    <ServicePackageResourceGovernancePolicy CpuCores="1"/>
    <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
  </Policies>
</ServiceManifestImport>
```
## <a name="configure-docker-healthcheck"></a>Docker HEALTHCHECK konfigurálása 

A 6.1-es verzióval kezdődően a Service Fabric automatikusan integrálja a [docker HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck) eseményeket a rendszerállapot-jelentésbe. Ez azt jelenti, hogy ha a tárolón engedélyezett a **HEALTHCHECK**, a Service Fabric jelenti az állapotát, valahányszor a tároló állapota módosul a Docker jelentése szerint. Egy **OK** állapotjelentés jelenik meg a [Service Fabric Explorerben](service-fabric-visualizing-your-cluster.md), amikor a *health_status* értéke *healthy* (megfelelő), és egy **WARNING** jelenik meg, ha a *health_status* értéke *unhealthy* (nem megfelelő). A tároló állapotának monitorozása céljából ténylegesen elvégzett ellenőrzésre mutató **HEALTHCHECK** utasításnak szerepelnie kell a tárolórendszerkép létrehozásához használt **dockerfile** fájlban. 

![HealthCheckHealthy][3]

![HealthCheckUnealthyApp][4]

![HealthCheckUnhealthyDsp][5]

A **HEALTHCHECK** viselkedését konfigurálhatja az egyes tárolókhoz, ha megadja a **HealthConfig** beállításait a **ContainerHostPolicies** részeként az ApplicationManifest fájlban.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="ContainerServicePkg" ServiceManifestVersion="2.0.0" />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <HealthConfig IncludeDockerHealthStatusInSystemHealthReport="true" RestartContainerOnUnhealthyDockerHealthStatus="false" />
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
```
Alapértelmezés szerint az *IncludeDockerHealthStatusInSystemHealthReport* beállítása **true**, és a *RestartContainerOnUnhealthyDockerHealthStatus* beállítása **false**. Ha a *RestartContainerOnUnhealthyDockerHealthStatus* beállítása **true**, egy újra és újra nem megfelelő állapotúnak jelentett tároló újraindul (lehetőleg más csomópontokon).

Ha az egész Service Fabric-fürthöz le szeretné tiltani a **HEALTHCHECK** integrációját, az [EnableDockerHealthCheckIntegration](service-fabric-cluster-fabric-settings.md) elemet **false** értékre kell állítania.

## <a name="deploy-the-container-application"></a>A tárolóalkalmazás üzembe helyezése
Mentse az összes módosítást, és hozza létre az alkalmazást. Az alkalmazás közzétételéhez kattintson a jobb gombbal a **MyFirstContainer** elemre a Megoldáskezelőben, és válassza a **Közzététel** lehetőséget.

A **Kapcsolati végpont** területen adja meg a fürt kezelési végpontját,  például a „containercluster.westus2.cloudapp.azure.com:19000” végpontot. Az ügyfél csatlakozási végpontját a fürt Áttekintés paneljén találja az [Azure Portalon](https://portal.azure.com).

Kattintson a **Publish** (Közzététel) gombra.

A [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) egy webalapú eszköz az alkalmazások és csomópontok vizsgálatához és kezeléséhez a Service Fabric-fürtökben. Nyisson meg egy böngészőt, lépjen a http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/ helyre, és folytassa az alkalmazás üzembe helyezését.  Az alkalmazás üzembe lesz helyezve, azonban hibaállapotban van, amíg a rendszerkép le nem töltődik a fürtcsomópontokra (ez a rendszerkép méretétől függően némi időt vehet igénybe): ![Hiba][1]

Az alkalmazás akkor kész, amikor ```Ready``` állapotba kerül: ![Kész][2]

Nyisson meg egy böngészőt, majd navigáljon a http://containercluster.westus2.cloudapp.azure.com:8081 helyre. A „Hello World!” címsornak kell megjelennie a böngészőben.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása
A fürt futtatása költségekkel jár, ezért érdemes lehet [törölni a fürtöt](service-fabric-cluster-delete.md).  A [nyilvános fürtök](https://try.servicefabric.azure.com/) néhány óra múlva automatikusan törlődnek.

Miután leküldte a rendszerképet a tároló-beállításjegyzékbe, törölheti a helyi rendszerképet a fejlesztői számítógépről:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="specify-os-build-version-specific-container-images"></a>Specifikus tárolórendszerképek megadása az operációs rendszer buildverziója alapján 

A Windows Server-tárolók (folyamatelkülönítési mód) esetleg nem kompatibilisek az operációs rendszer újabb verzióival. Például, a Windows Server 2016-tal létrehozott Windows Server-tárolók nem működnek a Windows Server 1709-es verziójában. Ezért amikor a fürtcsomópontokat a legújabb verzióra frissíti, az operációs rendszer korábbi verzióival létrehozott tárolószolgáltatások esetleg nem működnek majd. Annak érdekében, hogy ezt a futtatókörnyezet 6.1-es vagy újabb verzióiban meggátolja, a Service Fabricban több operációsrendszer-lemezképet lehet tárolóként megadni, és felcímkézni azokat az operációs rendszer buildverzióival (ennek lekéréséhez futtassa a `winver` parancsot egy Windows-parancssorban).  Először javasolt frissíteni az alkalmazásjegyzékeket, és rendszerverziónként külön rendszerkép-felülbírálásokat megadni, mielőtt frissítené az operációs rendszer verzióját a csomópontokon. A következő kódrészlet azt mutatja be, hogyan adható meg több tároló-rendszerkép az **ApplicationManifest.xml** alkalmazásjegyzék-fájlban:


```xml
<ContainerHostPolicies> 
         <ImageOverrides> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1701" Os="14393" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1709" Os="16299" /> 
         </ImageOverrides> 
     </ContainerHostPolicies> 
```
A Windows Server 2016 buildverziója 14393, a 1709-es verzió buildverziója 16299. A szolgáltatásjegyzék továbbra is csak egy rendszerképet ad meg mindegyik tárolószolgáltatáshoz, ahogy az alábbi kódrészletben is látható:

```xml
<ContainerHost>
    <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName> 
</ContainerHost>
```

   > [!NOTE]
   > Az operációs rendszer buildverzióját címkéző szolgáltatás csak a Windowson futó Service Fabric esetében érhető el
   >

Ha a virtuális gép mögöttes operációs rendszerének buildverziója 16299 (1709-es verzió), a Service Fabric az ehhez Windows Server verzióhoz tartozó tárolórendszerképet választja ki.  Ha a felcímkézett tárolórendszerképek mellett egy fel nem címkézett tárolórendszerkép is található az alkalmazásjegyzékben, a Service Fabric a fel nem címkézett rendszerképet az összes verzióban használható rendszerképként kezeli. Javasoljuk, hogy explicit módon címkézze fel a tárolórendszerképeket.


## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Példa teljes Service Fabric-alkalmazásra és szolgáltatásjegyzékre
Itt találja a jelen cikkben használt teljes szolgáltatás- és alkalmazásjegyzéket.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->    
    <EnvironmentVariables>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
    </EnvironmentVariables>

  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to
           listen. Please note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentOverrides CodePackageRef="FrontendService.Code">
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentOverrides>
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this
         application type is created. You can also create one or more instances of service type using the
         ServiceFabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="configure-time-interval-before-container-is-force-terminated"></a>A tároló kényszerített leállítását megelőző időköz beállítása

Konfigurálhat egy időintervallumot a futtatókörnyezet számára, ezzel megadva, hogy az mennyit várjon a tároló eltávolítása előtt, miután megkezdődött a szolgáltatás törlése (vagy másik csomópontba áthelyezése). Az időintervallum konfigurálásával a `docker stop <time in seconds>` parancsot küldi a tárolónak.   További információ: [docker stop](https://docs.docker.com/engine/reference/commandline/stop/). A várakozási időköz a `Hosting` szakaszban van meghatározva. Az alábbi fürtjegyzék kódrészlete azt mutatja be, hogyan adható meg a várakozási időköz:

```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "ContainerDeactivationTimeout",
                "value" : "10"
          },
          ...
        ]
}
```
Az alapértelmezett időintervallum 10 másodperc. Mivel ez egy dinamikus konfiguráció, a csak konfigurációs frissítés a fürtön frissíti az időkorlátot. 


## <a name="configure-the-runtime-to-remove-unused-container-images"></a>Futtatókörnyezet konfigurálása a nem használt tárolórendszerképek eltávolításához

A Service Fabric-fürtöt úgy is konfigurálhatja, hogy eltávolítsa a nem használt tárolórendszerképeket a csomópontról. Ez a konfiguráció lehetővé teszi a lemezterület visszanyerését, ha túl sok tárolórendszerkép található a csomóponton.  A funkció engedélyezéséhez frissítse a fürtjegyzék `Hosting` szakaszát az alábbi kódrészletben látható módon: 


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "PruneContainerImages",
                "value": "True"
          },
          {
                "name": "ContainerImagesToSkip",
                "value": "microsoft/windowsservercore|microsoft/nanoserver|microsoft/dotnet-frameworku|..."
          }
          ...
          }
        ]
} 
```

A `ContainerImagesToSkip` paraméternél megadhatja azokat a rendszerképeket, amelyeket nem szabad törölni. 


## <a name="configure-container-image-download-time"></a>Tárolórendszerkép letöltési idejének konfigurálása

Alapértelmezés szerint a Service Fabric futásideje 20 percet jelöl ki a tárolórendszerképek letöltésére és kicsomagolására, és ez a tárolórendszerképek többségénél elegendő is. Nagyobb rendszerképek esetében, vagy ha a hálózati kapcsolat lassú, szükséges lehet növelni a rendszerkép letöltésének és kibontásának megszakításáig rendelkezésre álló időtartamot. Ez a **ContainerImageDownloadTimeout** attribútum segítségével állítható be a fürtjegyzék **Üzemeltetés** szakaszában az alábbi kódrészletben látható módon:

```json
{
"name": "Hosting",
        "parameters": [
          {
              "name": " ContainerImageDownloadTimeout ",
              "value": "1200"
          }
]
}
```


## <a name="set-container-retention-policy"></a>Tárolómegőrzési szabályzat megadása

A tárolóindítási hibák diagnosztizálásának elősegítése céljából a Service Fabric (6.1-es vagy újabb verzió esetén) támogatja a megszakadt működésű vagy el sem induló tárolók megőrzését. Ez a szabályzat az **ApplicationManifest.xml** fájlban állítható be az alábbi kódrészletben látható módon:

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

A **ContainersRetentionCount** beállítása megadja a hiba esetén megőrzendő tárolók számát. Ha negatív érték van megadva, a rendszer minden olyan tárolót megőriz, amelyen hiba jelentkezik. Ha a **ContainersRetentionCount** attribútum nincs megadva, a rendszer nem őriz meg tárolókat. A **ContainersRetentionCount** attribútum az Alkalmazásparamétereket is támogatja, így a felhasználók különböző értékeket adhatnak meg a tesztelési és az éles fürtökön. Ezen funkciók használatakor javasolt elhelyezésre vonatkozó korlátozások használata, hogy a tárolószolgáltatás egy adott csomóponton maradjon, és a rendszer ne helyezze át más csomópontokra. Az ezzel a funkcióval megőrzött tárolókat manuálisan kell eltávolítani.


## <a name="next-steps"></a>További lépések
* További információk a [tárolók futtatásáról a Service Fabricban](service-fabric-containers-overview.md).
* Tekintse meg a [.NET-alkalmazás üzembe helyezését](service-fabric-host-app-in-a-container.md) ismertető oktatóanyagot.
* További információk a Service Fabric [alkalmazásainak élettartamáról](service-fabric-application-lifecycle.md).
* Tekintse meg [a Service Fabric-tárolók mintakódjait](https://github.com/Azure-Samples/service-fabric-containers) a GitHubon.

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[4]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[5]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
