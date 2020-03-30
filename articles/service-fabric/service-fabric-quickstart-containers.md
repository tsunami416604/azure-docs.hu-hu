---
title: Windows-tárolóalkalmazás létrehozása az Azure Service Fabric szolgáltatásában
description: Ebben a gyors útmutatóban létrehozza az első saját, Windows-alapú tárolóalkalmazását az Azure Service Fabricban.
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 477d47fabc59c5718c449418f225d6a38838b270
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75466271"
---
# <a name="quickstart-deploy-windows-containers-to-service-fabric"></a>Rövid útmutató: Windows-tárolók üzembe helyezése a Service Fabricben

Az Azure Service Fabric egy elosztott rendszerplatform, amely skálázható és megbízható mikroszolgáltatások és tárolók üzembe helyezésére és kezelésére szolgál.

A meglévő alkalmazások Service Fabric-fürtökön lévő Windows-tárolókban való futtatásához nem szükséges módosítania az alkalmazást. Ez a rövid útmutató bemutatja, hogyan helyezheti üzembe a Docker-tárolók előre összeállított rendszerképeit egy Service Fabric-alkalmazásban. Ha végzett, egy Windows Server Core 2016 Server és IIS tárolója lesz. Ez a rövid útmutató egy Windows-tároló központi telepítését ismerteti. Olvassa el [ezt a rövid útmutatót](service-fabric-quickstart-containers-linux.md) egy Linux-tároló üzembe helyezéséhez.

![Az IIS alapértelmezett webhelye][iis-default]

Ennek a rövid útmutatónak a segítségével megtanulhatja az alábbiakat:

* Docker-rendszerképtároló becsomagolása
* A kommunikáció konfigurálása
* Service Fabric-alkalmazás felépítése és becsomagolása
* A tárolóalkalmazás üzembe helyezése az Azure-on


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés (létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
* Egy fejlesztői számítógép, amelyen a következők futnak:
  * Visual Studio 2019 vagy Windows 2019.
  * [Service Fabric SDK és eszközök](service-fabric-get-started.md).

## <a name="package-a-docker-image-container-with-visual-studio"></a>Docker-rendszerképtároló becsomagolása a Visual Studióval

A Service Fabric SDK és -eszközök egy szolgáltatássablont biztosítanak, amelynek segítségével a tároló üzembe helyezhető egy Service Fabric-fürtben.

Indítsa el a Visual Studiót „rendszergazdaként”.  Válassza **az** > **Új** > **fájl projekt lehetőséget.**

Válassza a **Service Fabric-alkalmazás**lehetőséget, legyen a neve "MyFirstContainer", majd kattintson a **Létrehozás gombra.**

A **Hosted Containers and Applications** (Üzemeltetett tárolók és alkalmazások) sablonokból válassza a **Container** (Tároló) elemet.

A **Lemezkép neve**mezőbe írja be a "mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016" kifejezést, a Windows Server Core Server és az [IIS alaplemezképet.](https://hub.docker.com/_/microsoft-windows-servercore-iis)

Konfigurálja úgy a tárolóport–gazdagépport leképezést, hogy a szolgáltatáshoz a 80-as porton beérkező kérések a tárolón a 80-as portra legyenek leképezve.  Állítsa a **Tárolóport** beállítást 80, a **Gazdaport** beállítást pedig szintén 80 értékre.  

Nevezze el a szolgáltatást „MyContainerService” néven, majd kattintson az **OK** gombra.

![Új szolgáltatás párbeszédpanel][new-service]

## <a name="specify-the-os-build-for-your-container-image"></a>Az operációs rendszer buildjének megadása a tárolórendszerképhez

Előfordulhat, hogy a Windows Server egy bizonyos verziójával épített tárolók a Windows Server egy más verzióját futtató gazdagépen nem működnek. A Windows Server 1709-es verziójával készített tárolók például nem futnak Windows Server 2016 rendszert futtató állomásokon. További tudnivalókat a [Windows Server tároló operációs rendszerének és a gazdagép operációs rendszerének kompatibilitását ismertető cikket](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility). 

A Service Fabric-futtatókörnyezet 6.1-es vagy újabb verziójával több operációsrendszer-képet is megadhat tárolónként, és mindegyik rendszerképet megcímkézheti azon operációs rendszer buildverziójával, amelybe az üzembe helyezése történik. Így meggyőződhet arról, hogy az alkalmazása különböző verziójú Windows operációs rendszereket futtató gazdagépeken is működik. További tudnivalókat a [specifikus tárolórendszerképek operációs rendszer buildje alapján végzett megadását ismertető cikkben talál](service-fabric-get-started-containers.md#specify-os-build-specific-container-images). 

A Microsoft különböző rendszerképeket tesz közzé a Windows Server különböző verzióira épülő IIS-verziókhoz. Ahhoz, hogy a Service Fabric biztosan azon Windows Server verziójával kompatibilis tárolót telepítsen, amely azon fürtcsomópontokon fut ahová az alkalmazását is telepíti, az *ApplicationManifest.xml* fájlhoz adja hozzá a következő sorokat. A Windows Server 2016 buildverziója 14393, a 1709-es verzió buildverziója 16299.

```xml
    <ContainerHostPolicies CodePackageRef="Code"> 
      <ImageOverrides> 
        ...
          <Image Name="mcr.microsoft.com/windows/servercore/iis:windowsservercore-1803" /> 
          <Image Name= "mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016" Os="14393" /> 
          <Image Name="mcr.microsoft.com/windows/servercore/iis:windowsservercore-1709" Os="16299" /> 
      </ImageOverrides> 
    </ContainerHostPolicies> 
```

A szolgáltatásjegyzék továbbra is csak egy rendszerképet ad meg a nanoserverhez, `mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016`.

Szintén az *ApplicationManifest.xml* fájlban módosítsa **a PasswordEncrypted titkosítást** **hamis**ra. A fiók és a jelszó üres a Nyilvános tárolórendszerkép, amely a Docker Hub, ezért kikapcsoljuk a titkosítást, mert egy üres jelszó titkosítása létrehoz egy build hiba.

```xml
<RepositoryCredentials AccountName="" Password="" PasswordEncrypted="false" />
```

## <a name="create-a-cluster"></a>Fürt létrehozása

A következő mintaparancsfájl egy Öt csomópontos Service Fabric-fürtöt hoz létre X.509-es tanúsítvánnyal. A parancs létrehoz egy önaláírt tanúsítványt, és feltölti azt egy új kulcstartóba. A rendszer emellett a tanúsítványt egy helyi könyvtárba is átmásolja. A fürt létrehozásáról a Parancsfájl használatával a [Service Fabric-fürt létrehozása](scripts/service-fabric-powershell-create-secure-cluster-cert.md)című részben olvashat bővebben.

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell-útmutatóban](/powershell/azure/overview)található utasítások alapján.

A következő parancsfájl futtatása előtt `Connect-AzAccount` a PowerShell futtatása az Azure-ral való kapcsolat létrehozásához.

Másolja a következő parancsfájlt a vágólapra, és nyissa meg a **Windows PowerShell ISE ablakot.**  Illessze be a tartalmat az üres Untitled1.ps1 ablakba. Ezután adja meg a parancsfájl `subscriptionId`változóinak `adminuser` `adminpwd`értékeit: , `certpwd`, `certfolder`, , , és így tovább.  A parancsfájl futtatása előtt léteznie kell a megadott könyvtárnak. `certfolder`

[!code-powershell[main](../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Create a Service Fabric cluster")]

Miután megadta a változók értékeit, nyomja le az **F5** billentyűt a parancsfájl futtatásához.

A parancsfájl futtatása és a fürt `ClusterEndpoint` létrehozása után keresse meg a kimenetben. Példa:

```powershell
...
ClusterEndpoint : https://southcentralus.servicefabric.azure.com/runtime/clusters/b76e757d-0b97-4037-a184-9046a7c818c0
```

### <a name="install-the-certificate-for-the-cluster"></a>A fürt tanúsítványának telepítése

Most telepítjük a PFX-et a *CurrentUser\My* tanúsítványtárolóba. A PFX-fájl a fenti PowerShell-parancsfájl `certfolder` környezeti változójával megadott könyvtárban lesz.

Módosítsa a könyvtárat, majd futtassa a következő PowerShell parancsot, és `certfolder` cserélje le a könyvtárban található `certpwd` PFX-fájl nevét és a változóban megadott jelszót. Ebben a példában az aktuális könyvtár a PowerShell-parancsfájlban a `certfolder` változó által megadott könyvtárra van beállítva. Onnan a `Import-PfxCertificate` parancs fut:

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\mysfclustergroup20190130193456.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString Password#1234 -AsPlainText -Force)
```

A parancs visszaadja az ujjlenyomatot:

```powershell
  ...
  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
0AC30A2FA770BEF566226CFCF75A6515D73FC686  CN=mysfcluster.SouthCentralUS.cloudapp.azure.com
```

Ne feledje a következő lépés ujjlenyomatértékét.

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>Alkalmazás üzembe helyezése az Azure-ban a Visual Studio használatával

Az alkalmazást a létrehozása után telepítheti a fürtben, közvetlenül a Visual Studióból.

A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a **MyFirstContainer** elemre, majd kattintson a **Publish** (Közzététel) parancsra. Ekkor megjelenik a Publish (Közzététel) párbeszédpanel.

Másolja a **CN=** értéket követő tartalmat a `Import-PfxCertificate` PowerShell ablakába, amikor a fenti parancsot futtatta, és adja hozzá a portot. `19000` Például: `mysfcluster.SouthCentralUS.cloudapp.azure.com:19000`. Másolja a **Kapcsolatvégpont** mezőbe. Ne feledje ezt az értéket, mert szüksége lesz rá egy későbbi lépésben.

Kattintson az **Advanced Connection Parameters** (Speciális kapcsolati paraméterek) elemre, és ellenőrizze a kapcsolati paraméterek információit.  *A FindValue* és *a ServerCertThumbprint* értékeknek meg kell `Import-PfxCertificate` egyezniük az előző lépésben futtatott tanúsítvány ujjlenyomatával.

![Publish (Közzététel) párbeszédpanel](./media/service-fabric-quickstart-containers/publish-app.png)

Kattintson a **Publish** (Közzététel) gombra.

A fürtben szereplő minden alkalmazásnak egyedi névvel kell rendelkeznie. Névütközés esetén nevezze át a Visual Studio-projektet, és telepítse újra.

Nyisson meg egy böngészőt, és keresse meg az előző lépésben a **Kapcsolatvégpont** mezőbe helyezett címet. Azt is megteheti, hogy beilleszti előre a `http://` sémaazonosítót, vagy hozzáfűzheti a `:80` portot az URL-címhez. Például: http:\//mysfcluster.SouthCentralUS.cloudapp.azure.com:80.

 Ekkor az IIS alapértelmezett webhelyének kell megjelennie: ![Az IIS alapértelmezett webhelye][iis-default]

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A fürt futása közben továbbra is felszámítja a díjakat. Fontolja meg [a fürt törlését.](service-fabric-cluster-delete.md)

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

* Docker-rendszerképtároló becsomagolása
* A kommunikáció konfigurálása
* Service Fabric-alkalmazás felépítése és becsomagolása
* A tárolóalkalmazás üzembe helyezése az Azure-on

Ha bővebb információra van szüksége a Windows-tárolók Service Fabricben való használatával kapcsolatban, lépjen tovább a Windows-tárolóalkalmazásokról szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Tárolóalkalmazás létrehozása Windowshoz](./service-fabric-host-app-in-a-container.md)

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
[new-service]: ./media/service-fabric-quickstart-containers/NewService.png
