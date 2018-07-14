---
title: Mi a Cloud Service-modell és csomag |} A Microsoft Docs
description: Ismerteti a felhőszolgáltatási modellnek (.csdef, .cscfg) és a csomag (.cspkg) az Azure-ban
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 4ce2feb5-0437-496c-98da-1fb6dcb7f59e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeconnoc
ms.openlocfilehash: 90d810916599db50249a3e2ec677046c5af42a09
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005849"
---
# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>Mi a Cloud Service-modell, és hogyan tegye Becsomagolhatja azt?
Egy felhőalapú szolgáltatás létrehozása az három összetevőt, a szolgáltatás definíciós *(.csdef)*, a szolgáltatás konfigurációs *(.cscfg)*, és a egy szolgáltatáscsomag *(.cspkg)*. Mindkét a **ServiceDefinition.csdef** és **ServiceConfig.cscfg** fájlok XML-alapú, és ismertetik a felhőalapú szolgáltatás, és hogyan van konfigurálva; szerkezete együttesen: a modell. A **ServicePackage.cspkg** egy zip-fájl, amely jön létre a **ServiceDefinition.csdef** , és többek között tartalmazza a szükséges bináris alapú függőségeket. Az Azure egy felhőalapú szolgáltatás létrehozása is a **ServicePackage.cspkg** és a **ServiceConfig.cscfg**.

Ha a felhőszolgáltatás már fut az Azure-ban, újrakonfigurálhatja az keresztül a **ServiceConfig.cscfg** fájlt, de a definíciója nem módosítható.

## <a name="what-would-you-like-to-know-more-about"></a>Mit szeretne többet tudni?
* Szeretnék többet tudni a [ServiceDefinition.csdef](#csdef) és [ServiceConfig.cscfg](#cscfg) fájlokat.
* Arról, hogy már ismeri, adja meg [néhány példa](#next-steps) a mi is konfigurálható.
* Szeretnék létrehozni a [ServicePackage.cspkg](#cspkg).
* A Visual Studio használok, és szeretném...
  * [Felhőszolgáltatás létrehozása][vs_create]
  * [Egy meglévő felhőszolgáltatáshoz újrakonfigurálása][vs_reconfigure]
  * [A Felhőszolgáltatás-projekt üzembe helyezése][vs_deploy]
  * [Felhőszolgáltatás-példányok távoli asztal][remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
A **ServiceDefinition.csdef** fájl adja meg a felhőszolgáltatás konfigurálása az Azure által használt beállításokat. A [Azure szolgáltatásdefiníciós sémában (.csdef fájl)](https://msdn.microsoft.com/library/azure/ee758711.aspx) biztosít az engedélyezett formátum a szolgáltatásdefiníciós fájlban. Az alábbi példa bemutatja a beállításokat, amelyek a webes és feldolgozói szerepkörök:

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

Olvassa el a [szolgáltatásdefiníciós sémában](https://msdn.microsoft.com/library/azure/ee758711.aspx) jobban megérteni az itt használt XML-séma, azonban a következő néhány elemét rövid leírását:

**Helyek**  
Az IIS7 szolgáltatásban üzemeltetett webhelyek vagy webalkalmazások számára vonatkozó definíciókat tartalmazza.

**InputEndpoints**  
A felhőalapú szolgáltatáshoz való használt ügyfélvégpontokhoz vonatkozó definíciókat tartalmazza.

**InternalEndpoints**  
Végpontok kommunikálnak egymással a szerepkörpéldányok által használt definíciókat tartalmazza.

**ConfigurationSettings**  
Egy adott szerepkör funkciók beállítás definícióit tartalmazza.

**Tanúsítványok**  
A tanúsítványok, a szerepkör szükséges definíciókat tartalmazza. Az előző példakód bemutatja egy tanúsítvány, amely Azure Connect konfigurációjának.

**LocalResources**  
Helyi tároló-erőforrások vonatkozó definíciókat tartalmazza. Helyi tároló egyik erőforrásához az egy fenntartott könyvtár a virtuális gép szerepkör-példány fut, amelyben a fájlrendszerben.

**Import**  
Importált modulok vonatkozó definíciókat tartalmazza. Az előző példakód bemutatja a modulok a távoli asztali kapcsolat, és csatlakozzon az Azure.

**Indítás**  
A szerepkör indításakor futtatott feladatokat tartalmazza. A feladatok egy .cmd vagy egy végrehajtható fájl vannak definiálva.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration.cscfg
A beállításokat a felhőszolgáltatás konfigurációja határozza meg az értékeket a **ServiceConfiguration.cscfg** fájlt. Megadhatja, hogy telepíteni szeretné a fájlban minden szerepkör-példányok száma. Az értékeket a szolgáltatásdefiníciós fájlban meghatározott konfigurációs beállításainak kerülnek a szolgáltatás konfigurációs fájlja. Bármely a felhőszolgáltatáshoz társított felügyeleti tanúsítványok ujjlenyomatait is bekerülnek a fájlt. A [Azure szolgáltatás konfigurációs sémáját (.cscfg fájl)](https://msdn.microsoft.com/library/azure/ee758710.aspx) az engedélyezett formátum biztosít egy konfigurációs fájlban.

A szolgáltatás konfigurációs fájlja nem az alkalmazás együtt van csomagolva, de egy külön fájlként az Azure-bA feltöltött és a felhőalapú szolgáltatás konfigurálására szolgál. Új szolgáltatáskonfigurációs fájlt tölthet fel a felhőalapú szolgáltatás újbóli telepítése nélkül. A konfigurációs értékeket a felhőszolgáltatás számára is módosítható, a felhőalapú szolgáltatás futása közben. Az alábbi példa bemutatja a konfigurációs beállításokat, amelyek a webes és feldolgozói szerepkörök:

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

Olvassa el a [szolgáltatás konfigurációs sémáját](https://msdn.microsoft.com/library/azure/ee758710.aspx) jobb megértéséhez, az itt használt XML-séma, azonban itt van egy rövid magyarázatot elemek:

**példányok**  
Konfigurálja a futó a szerepkör példányainak számát. Megakadályozni a felhőszolgáltatás frissítések során esetleg elérhetetlenné válik, javasoljuk, hogy telepít-e a webalkalmazás felé néző szerepkörök több példánya. Több példány telepítésével tartja vannak az irányelveket a [Azure számítási szolgáltatás szolgáltatói szerződés (SLA)](http://azure.microsoft.com/support/legal/sla/), amely garantálja, hogy a 99,95 %-os külső kapcsolatokat az Internet felé néző szerepkörök, amikor két vagy több szerepkör példányai üzembe helyezett szolgáltatáshoz.

**ConfigurationSettings**  
A futó példányát tekintve szerepkör beállításait konfigurálja. Neve a `<Setting>` elemet meg kell egyeznie a beállítás definíciókat a szolgáltatásdefiníciós fájlban.

**Tanúsítványok**  
A szolgáltatás által használt tanúsítványt konfigurálja. Az előző példakód bemutatja, hogyan határozza meg a tanúsítványt a távelérési modul. Értékét a *ujjlenyomat* attribútum használatára kell állítani a tanúsítvány ujjlenyomatát.

<p/>

> [!NOTE]
> A tanúsítvány ujjlenyomatát a konfigurációs fájlt egy szövegszerkesztőben használatával lehet hozzáadni. Vagy az érték is hozzáadhat a **tanúsítványok** lapján a **tulajdonságok** lap a szerepkör a Visual Studióban.
> 
> 

## <a name="defining-ports-for-role-instances"></a>Szerepkörpéldányok portok meghatározása
Az Azure lehetővé teszi, hogy csak egy belépési pont webes szerepkört. Ami azt jelenti, hogy minden forgalom egyetlen IP-címen keresztül történik. Beállíthatja, hogy a webhelyek megosztani egy portot az állomásfejléc irányítja a kérést a megfelelő helyre való konfigurálásával. Beállíthatja, hogy az alkalmazások, az IP-címet a jól ismert portot figyeli.

A következő minta bemutatja a konfiguráció egy webes szerepkör egy webhelyet és egy webes alkalmazással. A webhely alapértelmezett bejegyzés helye a 80-as porton van konfigurálva, és a webes alkalmazások kéréseket fogadni egy másik állomást fejlécet, amelynek a neve "mail.mysite.cloudapp.net" vannak konfigurálva.

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
      <Binding name="mail" endpointName="HttpIn" hostheader="mail.mysite.cloudapp.net" />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>Egy szerepkör konfigurációjának módosítása
A felhőszolgáltatás konfigurációjának futás közben az Azure-ban, a szolgáltatás offline állapotba helyezése nélkül frissítheti. Módosíthatja a konfigurációs adatokat, vagy töltsön fel egy új konfigurációs fájlt, vagy a konfigurációs fájlok helyi szerkesztése elemre, és alkalmazza azt a futó szolgáltatás. A következő módosításokat módosíthatók, a szolgáltatás konfigurációját:

* **Az értékek a konfigurációs beállításainak módosítása**  
  Amikor egy konfigurációs beállítás a módosításokat, egy szerepkörpéldány lehet váltani, a módosítás alkalmazására, amíg a példányok online állapotban, vagy szabályosan újrahasznosítása a példány, és alkalmazza a módosítást, miközben a példány offline állapotban van.
* **A szolgáltatás-topológia a szerepkörpéldányok módosítása**  
  Topológia módosítása nem érinti a futó példányát, kivéve, ha egy példány eltávolítása folyamatban van. Az összes többi példányok általában nincs szükségük újraindítására; azonban választhat topológia változásakor a szerepkörpéldányok újraindítása.
* **A tanúsítvány-ujjlenyomat módosítása**  
  A tanúsítvány csak akkor frissíthető, ha egy szerepkörpéldány offline állapotban. Ha a tanúsítvány hozzáadása, törlése, vagy módosítható, amíg egy szerepkörpéldány online állapotban, az Azure szabályosan vesz igénybe a példány offline frissítse a tanúsítványt, és ismét online állapotba a módosítás befejezése után.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Konfigurációs módosítások szolgáltatás futtatókörnyezeti események kezelése
A [Azure-futtatókörnyezeti kódtárának](https://msdn.microsoft.com/library/azure/mt419365.aspx) magában foglalja a [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.aspx) névtér, amely osztályokat biztosít az Azure-környezetet egy szerepkörből való interakcióhoz. A [RoleEnvironment](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) osztály határozza meg a következő események előállított előtt és után a konfiguráció módosítása:

* **[Módosítása](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx) esemény**  
  Ez akkor fordul elő, a konfiguráció módosításának egy szerepkört, és szükség esetén a szerepkörpéldányok forgalommegugrást így megadott példányra alkalmazása előtt.
* **[Módosított](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changed.aspx) esemény**  
  Akkor következik be, miután a konfiguráció módosításának alkalmazott egy megadott szerepkör-példány.

> [!NOTE]
> Tanúsítvány módosítások mindig offline állapotba helyezése szerepkör példányai, mivel azok nem a RoleEnvironment.Changing vagy RoleEnvironment.Changed eseményeket hoz létre a.
> 
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
Alkalmazás üzembe helyezése felhőalapú szolgáltatásként az Azure-ban, először az alkalmazás a megfelelő formátumban kell készítenie. Használhatja a **CSPack** parancssori eszköz (telepített a [Azure SDK](https://azure.microsoft.com/downloads/)) az alkalmazáscsomag-fájl létrehozása a Visual Studio helyett.

**CSPack** meghatározásához a csomag tartalmát a szolgáltatásdefiníciós fájlban és a szolgáltatás konfigurációs fájlja a tartalmát használja. **CSPack** állít elő, az alkalmazás csomagfájlját (.cspkg) használatával az Azure-bA feltöltött is a [az Azure portal](cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Alapértelmezés szerint a csomag neve `[ServiceDefinitionFileName].cspkg`, de megadhat egy másik nevet a használatával a `/out` lehetőség a **CSPack**.

**CSPack** található:  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> Futtatásával CSPack.exe (windows) rendszeren érhető el a **a Microsoft Azure-parancssort** hivatkozást, amely az SDK-val települ.  
> 
> Futtassa a CSPack.exe programot önmagában kapcsolatos lehetséges kapcsolók és parancsok dokumentációjában talál.
> 
> 

<p />

> [!TIP]
> A felhőszolgáltatások futtatása helyben a **a Microsoft Azure Compute Emulator**, használja a **/copyonly** lehetőséget. Ezt a beállítást, amelyről futtathatók a compute emulatorban directory elrendezés az alkalmazás a bináris fájlokat másolja át.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>A példában szereplő parancs egy felhőszolgáltatás csomagolása
A következő példában létrehozunk egy alkalmazáscsomagot, amely tartalmazza a webes szerepkör adatait. A parancs megadja a szolgáltatásdefiníciós fájlt szeretne használni, a könyvtárat, amelyben megtalálható a bináris fájlokat, és az alkalmazáscsomag-fájl nevét.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

Ha az alkalmazás webes szerepkör és a egy feldolgozói szerepkörben is tartalmaz, használja a következő parancsot:

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /out:[OutputFileName]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]
```

Ha a változók meghatározása a következő:

| Változó | Érték |
| --- | --- |
| \[DirectoryName\] |A projekt gyökérkönyvtárában, amely tartalmazza a .csdef fájl az Azure-projekt beszüntetve. |
| \[ServiceDefinition\] |A szolgáltatásdefiníciós fájl neve. Alapértelmezés szerint ez a fájl neve ServiceDefinition.csdef. |
| \[OutputFileName\] |A létrehozott alkalmazáscsomag-fájl neve. Általában ez van beállítva az alkalmazás nevére. Ha nem fájlt ad meg nevet, az alkalmazáscsomag jön létre \[ApplicationName\].cspkg. |
| \[RoleName\] |A szerepkör a szolgáltatásdefiníciós fájlban meghatározott neve. |
| \[RoleBinariesDirectory] |A szerepkör bináris fájljainak helyét. |
| \[VirtualPath\] |Minden egyes virtuális elérési út a szolgáltatás definíciós helyek szakaszában meghatározott fizikai könyvtárak. |
| \[PhysicalPath\] |A tartalom minden egyes virtuális elérési út a szolgáltatás definíciós hely csomópontján definiált fizikai könyvtárak. |
| \[RoleAssemblyName\] |A szerepkör a bináris fájl neve. |

## <a name="next-steps"></a>További lépések
Felhőszolgáltatás-csomagok hozok létre, és szeretném...

* [Felhőszolgáltatás-példányok távoli asztal beállítása][remotedesktop]
* [A Felhőszolgáltatás-projekt üzembe helyezése][deploy]

A Visual Studio használok, és szeretném...

* [Új felhőszolgáltatás hozható létre][vs_create]
* [Egy meglévő felhőszolgáltatáshoz újrakonfigurálása][vs_reconfigure]
* [A Felhőszolgáltatás-projekt üzembe helyezése][vs_deploy]
* [Felhőszolgáltatás-példányok távoli asztal beállítása][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop-new-portal.md
[vs_remote]: cloud-services-role-enable-remote-desktop-visual-studio.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md
