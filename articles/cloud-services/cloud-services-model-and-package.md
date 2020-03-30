---
title: Mi az a Cloud Service modell és csomag | Microsoft dokumentumok
description: A felhőszolgáltatás modelljét (.csdef, .cscfg) és csomagot (.cspkg) ismerteti az Azure-ban
services: cloud-services
author: tanmaygore
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: 32603f4ab33e020245861e5dc66d2ade545fa627
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247487"
---
# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>Mi a Cloud Service-modell, és hogyan csomagolhatom?
A felhőszolgáltatás három összetevőből, a *szolgáltatásdefinícióból (.csdef)*, a szolgáltatás config *(.cscfg)* és egy szolgáltatáscsomagból *(.cspkg)* jön létre. Mind a **ServiceDefinition.csdef,** mind a **ServiceConfig.cscfg** fájlok XML-alapúak, és ismertetik a felhőszolgáltatás szerkezetét és konfigurálásának módját; együttesen nevezik a modellt. A **ServicePackage.cspkg** egy zip fájl, amely a **ServiceDefinition.csdef** fájlból jön létre, és többek között tartalmazza az összes szükséges bináris alapú függőséget. Az Azure felhőszolgáltatást hoz létre mind a **ServicePackage.cspkg,** mind a **ServiceConfig.cscfg**fájlból.

Miután a felhőszolgáltatás fut az Azure-ban, újrakonfigurálhatja a **ServiceConfig.cscfg** fájlon keresztül, de nem módosíthatja a definíciót.

## <a name="what-would-you-like-to-know-more-about"></a>Miről szeretnél többet tudni?
* Szeretnék többet megtudni a [ServiceDefinition.csdef](#csdef) és [ServiceConfig.cscfg](#cscfg) fájlokról.
* Én már tudom, hogy, adj [néhány példát,](#next-steps) hogy mit tudok beállítani.
* Szeretném létrehozni a [ServicePackage.cspkg](#cspkg).
* A Visual Studio alkalmazást használom, és szeretnék...
  * [Felhőszolgáltatás létrehozása][vs_create]
  * [Meglévő felhőszolgáltatás újrakonfigurálása][vs_reconfigure]
  * [Felhőszolgáltatás-projekt telepítése][vs_deploy]
  * [Távoli asztal egy felhőszolgáltatás-példányba][remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
A **ServiceDefinition.csdef** fájl megadja azokat a beállításokat, amelyeket az Azure egy felhőszolgáltatás konfigurálásához használ. Az [Azure Service Definition Schema (.csdef fájl)](/previous-versions/azure/reference/ee758711(v=azure.100)) biztosítja a szolgáltatásdefiníciós fájl megengedett formátumát. A következő példa a webes és a feldolgozói szerepkörökhöz definiálható beállításokat mutatja be:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

Az itt használt XML-séma jobb megértését a [szolgáltatásdefiníciós sémában](/previous-versions/azure/reference/ee758711(v=azure.100)) olvashatja, de itt van néhány elem gyors magyarázata:

**Webhelyek**  
Az IIS7-ben tárolt webhelyek vagy webalkalmazások definícióit tartalmazza.

**Bemeneti végpontok**  
A felhőszolgáltatással való kapcsolatfelvételhez használt végpontok definícióit tartalmazza.

**Belső végpontok**  
A szerepkörpéldányok által egymással való kommunikációra használt végpontok definícióit tartalmazza.

**ConfigurationSettings (Konfigurációs beállítások)**  
Egy adott szerepkör szolgáltatásainak beállításdefinícióit tartalmazza.

**Tanúsítványok**  
A szerepkörhöz szükséges tanúsítványok definícióit tartalmazza. Az előző kód példa egy tanúsítványt mutat be, amely az Azure Connect konfigurációjához használatos.

**Helyi források**  
A helyi tárolási erőforrások definícióit tartalmazza. A helyi tárolási erőforrás annak a virtuális gépnek a fájlrendszerének fenntartott könyvtára, amelyben egy szerepkör példánya fut.

**Behozatal**  
Az importált modulok definícióit tartalmazza. Az előző kód példa a távoli asztali kapcsolat és az Azure Connect moduljait mutatja be.

**Indítás**  
A szerepkör indításakor futtatott feladatokat tartalmazza. A feladatok at .cmd vagy végrehajtható fájlban definiálják.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration.cscfg
A felhőszolgáltatás beállításainak konfigurációját a **ServiceConfiguration.cscfg** fájl értékei határozzák meg. Megadhatja, hogy hány példányt szeretne telepíteni a fájl egyes szerepköreihez. A szolgáltatásdefiníciós fájlban megadott konfigurációs beállítások értékei hozzáadódnak a szolgáltatáskonfigurációs fájlhoz. A felhőszolgáltatáshoz társított felügyeleti tanúsítványok ujjlenyomatai is hozzáadódnak a fájlhoz. Az [Azure Service Configuration Schema (.cscfg fájl)](/previous-versions/azure/reference/ee758710(v=azure.100)) biztosítja a szolgáltatás konfigurációs fájl megengedett formátumát.

A szolgáltatás konfigurációs fájl nincs becsomagolva az alkalmazással, de külön fájlként töltődik fel az Azure-ba, és a felhőszolgáltatás konfigurálására szolgál. Új szolgáltatáskonfigurációs fájlt tölthet fel a felhőszolgáltatás újratelepítése nélkül. A felhőszolgáltatás konfigurációs értékei a felhőszolgáltatás futása közben módosíthatók. A következő példa a webes és feldolgozói szerepkörökhöz definiálható konfigurációs beállításokat mutatja be:

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Az itt használt XML-séma jobb megértéséhez tekintse meg a [szolgáltatáskonfigurációs sémát,](/previous-versions/azure/reference/ee758710(v=azure.100)) azonban itt van egy gyors magyarázat az elemekről:

**példányszám**  
A szerepkör futó példányainak számát adja meg. Annak érdekében, hogy a felhőszolgáltatás ne váljon elérhetetlenné a frissítések során, ajánlott egynél több példányt telepíteni a webes szerepkörökből. Több példány telepítésével betartják az [Azure Compute szolgáltatásiszint-szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/)irányelveit, amelyek 99,95%-os külső kapcsolatot biztosítanak az internetre néző szerepkörök számára, ha két vagy több szerepkörpéldány van telepítve egy szolgáltatáshoz.

**ConfigurationSettings (Konfigurációs beállítások)**  
Egy szerepkör futó példányainak beállításait adja meg. Az elemek `<Setting>` nevének meg kell egyeznie a szolgáltatásdefiníciós fájlban lévő beállításdefiníciókkal.

**Tanúsítványok**  
A szolgáltatás által használt tanúsítványok konfigurálása. Az előző kódpélda bemutatja, hogyan definiálható a RemoteAccess modul tanúsítványa. Az *ujjlenyomat-attribútum* értékét a használni hozandó tanúsítvány ujjlenyomatára kell állítani.

<p/>

> [!NOTE]
> A tanúsítvány ujjlenyomata szövegszerkesztővel adható hozzá a konfigurációs fájlhoz. Vagy az érték hozzáadható a Visual Studio szerepkör **Tulajdonságok** lapjának **Tanúsítványok** lapján.
> 
> 

## <a name="defining-ports-for-role-instances"></a>Szerepkörpéldányok portjainak meghatározása
Az Azure csak egy belépési pontot engedélyez egy webes szerepkörhöz. Ami azt jelenti, hogy minden forgalom egy IP-címen keresztül történik. A webhelyek et úgy konfigurálhatja, hogy megosszák a portot, ha úgy állítja be a gazdafejlécet, hogy a kérést a megfelelő helyre irányítsa. Az alkalmazásokat úgy is beállíthatja, hogy az IP-cím jól ismert portjait hallgassák.

A következő minta egy webszerepkör és egy webalkalmazás konfigurációját mutatja be. A webhely a 80-as port alapértelmezett bejegyzéshelyeként van konfigurálva, a webalkalmazások pedig úgy vannak beállítva, hogy kéréseket fogadjanak egy "mail.mysite.cloudapp.net" nevű másodlagos állomásfejléctől.

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" port="80" />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" hostHeader="mail.mysite.cloudapp.net" />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>Szerepkör konfigurációjának módosítása
Frissítheti a felhőszolgáltatás konfigurációját az Azure-ban való futtatás a szolgáltatás offline állapotba helyezése nélkül. A konfigurációs adatok módosításához feltölthet egy új konfigurációs fájlt, vagy szerkesztheti a konfigurációs fájlt, és alkalmazhatja azt a futó szolgáltatásra. A szolgáltatás konfigurációjának következő módosításai hajthatók végre:

* **A konfigurációs beállítások értékeinek módosítása**  
  Amikor egy konfigurációs beállítás megváltozik, a szerepkörpéldány úgy is választhat, hogy alkalmazza a módosítást, amíg a példány online állapotban van, vagy a példány tetszése alatt, és alkalmazza a módosítást, amíg a példány offline állapotban van.
* **A szerepkörpéldányok szolgáltatástopológiájának módosítása**  
  A topológia módosításai nincsenek hatással a futó példányokra, kivéve, ha egy példány eltávolításra kerül. Az összes többi példányt általában nem kell újrahasznosítani; azonban dönthet úgy, hogy újrahasznosítja a szerepkörpéldányokat a topológia változására válaszul.
* **A tanúsítvány ujjlenyomatának módosítása**  
  Csak akkor frissíthet tanúsítványt, ha egy szerepkörpéldány offline állapotban van. Ha egy tanúsítványt hozzáadnak, törölnek vagy módosítanak, miközben egy szerepkörpéldány online állapotban van, az Azure kecsesen offline állapotba helyezi a példányt a tanúsítvány frissítéséhez, és a módosítás befejezése után újra online állapotba hozza.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Konfigurációs módosítások kezelése szolgáltatásfutásidejű eseményekkel
Az [Azure Runtime Library](/previous-versions/azure/reference/mt419365(v=azure.100)) tartalmazza a [Microsoft.WindowsAzure.ServiceRuntime](/previous-versions/azure/reference/ee741722(v=azure.100)) névteret, amely osztályokat biztosít az Azure-környezetben egy szerepkörből való interakcióhoz. A [RoleEnvironment](/previous-versions/azure/reference/ee773173(v=azure.100)) osztály a következő eseményeket határozza meg, amelyek a konfiguráció módosítása előtt és után jelennek meg:

* **[Esemény módosítása](/previous-versions/azure/reference/ee758134(v=azure.100))**  
  Ez akkor fordul elő, mielőtt a konfigurációs módosítás egy szerepkör egy adott példányára vonatkozik, így szükség esetén lehetősége van a szerepkörpéldányok leállítására.
* **[Módosított](/previous-versions/azure/reference/ee758129(v=azure.100)) esemény**  
  Akkor következik be, ha a konfigurációs módosítás egy szerepkör megadott példányára van alkalmazva.

> [!NOTE]
> Mivel a tanúsítvány módosításai mindig offline állapotba helyezik egy szerepkör példányait, nem emelik ki a RoleEnvironment.Changing vagy a RoleEnvironment.Changed eseményeket.
> 
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
> [!NOTE]
> A maximálisan telepíthető csomagméret 600 MB

Egy alkalmazás üzembe helyezéséhez felhőszolgáltatásként az Azure-ban, először az alkalmazás megfelelő formátumban kell csomagolnia. A **CSPack** parancssori eszközzel (az [Azure SDK-val](https://azure.microsoft.com/downloads/)telepítve) létrehozhatja a csomagfájlt a Visual Studio alternatívájaként.

**A CSPack** a szolgáltatásdefiníciós fájl és a szolgáltatáskonfigurációs fájl tartalmát használja a csomag tartalmának meghatározásához. **A CSPack** létrehoz egy alkalmazáscsomag-fájlt (.cspkg), amelyet az Azure portalon keresztül tölthet fel az [Azure-ba.](cloud-services-how-to-create-deploy-portal.md#create-and-deploy) Alapértelmezés szerint a csomag `[ServiceDefinitionFileName].cspkg`neve , de a `/out` **CSPack**lehetőséggel eltérő nevet is megadhat.

**A CSPack** a  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> A CSPack.exe (windows) az SDK-val telepített **Microsoft Azure parancssori** parancsikon futtatásával érhető el.  
> 
> Futtassa a CSPack.exe programot önmagában, hogy megtekintse az összes lehetséges kapcsolóés parancs dokumentációját.
> 
> 

<p />

> [!TIP]
> Futtassa a felhőszolgáltatást helyileg a **Microsoft Azure Compute Emulator,** használja a **/copyonly** opciót. Ez a beállítás az alkalmazás bináris fájljait egy könyvtárelrendezésbe másolja, amelyből futtathatók a számítási emulátorban.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>Példa felhőszolgáltatás csomagolására
A következő példa létrehoz egy alkalmazáscsomagot, amely egy webes szerepkör adatait tartalmazza. A parancs megadja a használandó szolgáltatásdefiníciós fájlt, azt a könyvtárat, ahol bináris fájlok találhatók, valamint a csomagfájl nevét.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

Ha az alkalmazás webes és feldolgozói szerepkört is tartalmaz, a következő parancs használatos:

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /out:[OutputFileName]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]
```

Amennyiben a változók meghatározása a következő:

| Változó | Érték |
| --- | --- |
| \[Könyvtárnév\] |Az Azure-projekt .csdef fájlját tartalmazó gyökérprojekt-könyvtár alkönyvtára. |
| \[Szolgáltatásdefiníció\] |A szolgáltatásdefiníciós fájl neve. Alapértelmezés szerint ez a fájl neve ServiceDefinition.csdef. |
| \[OutputFileName\] |A létrehozott csomagfájl neve. Ez általában az alkalmazás nevére van beállítva. Ha nincs megadva fájlnév, az alkalmazáscsomag \[\]ApplicationName .cspkg néven jön létre. |
| \[RoleName\] |A szerepkör neve a szolgáltatásdefiníciós fájlban meghatározottak szerint. |
| \[RoleBinariesDirectory] |A szerepkör bináris fájljainak helye. |
| \[VirtualPath\] |A szolgáltatásdefiníció Helyek szakaszában meghatározott minden egyes virtuális útvonal fizikai könyvtárai. |
| \[PhysicalPath\] |A szolgáltatásdefiníció helycsomópontjában definiált minden egyes virtuális elérési út tartalmának fizikai könyvtárai. |
| \[RoleAssemblyName\] |A szerepkör bináris fájljának neve. |

## <a name="next-steps"></a>További lépések
Egy felhőalapú szolgáltatási csomagot hozok létre, és szeretnék...

* [Távoli asztal telepítése felhőszolgáltatás-példányhoz][remotedesktop]
* [Felhőszolgáltatás-projekt telepítése][deploy]

A Visual Studio alkalmazást használom, és szeretnék...

* [Új felhőszolgáltatás létrehozása][vs_create]
* [Meglévő felhőszolgáltatás újrakonfigurálása][vs_reconfigure]
* [Felhőszolgáltatás-projekt telepítése][vs_deploy]
* [Távoli asztal telepítése felhőszolgáltatás-példányhoz][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop-new-portal.md
[vs_remote]: cloud-services-role-enable-remote-desktop-visual-studio.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md



