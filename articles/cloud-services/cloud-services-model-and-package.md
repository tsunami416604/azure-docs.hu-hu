---
title: "Egy felhőalapú szolgáltatás modell és a csomag |} Microsoft Docs"
description: "Ismerteti a felhő szolgáltatásmodell (.csdef, .cscfg) és a csomagba (.cspkg) az Azure-ban"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 4ce2feb5-0437-496c-98da-1fb6dcb7f59e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: b7210c944e2f99aacdc2f554409552007286c5da
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>Mi a felhőalapú szolgáltatás modell, és hogyan tegye csomag azt?
Egy felhőalapú szolgáltatás létrehozása az három összetevővel, a szolgáltatás definíciós *(.csdef)*, a szolgáltatás konfigurációs *(.cscfg)*, és a szolgáltatáscsomagot *(.cspkg)*. Mindkét a **ServiceDefinition.csdef** és **ServiceConfig.cscfg** fájlok XML-alapú, és ismerteti a felhőalapú szolgáltatás, és hogyan van konfigurálva; szerkezete együttesen: a modell. A **ServicePackage.cspkg** egy zip-fájl, amely jönnek létre a **ServiceDefinition.csdef** , és többek között tartalmazza az összes szükséges bináris alapú függőség. Az Azure létrehoz egy felhőalapú szolgáltatás is a **ServicePackage.cspkg** és a **ServiceConfig.cscfg**.

Ha a felhőalapú szolgáltatás fut az Azure-ban, újrakonfigurálhatja azt a a **ServiceConfig.cscfg** fájlt, de a definíciója nem módosítható.

## <a name="what-would-you-like-to-know-more-about"></a>Mit szeretne tudni a több?
* Az ismertetés szeretnék a [ServiceDefinition.csdef](#csdef) és [ServiceConfig.cscfg](#cscfg) fájlokat.
* Arról, hogy már ismeri, adja meg [néhány példa](#next-steps) a Mi lehet konfigurálni.
* Létrehozása a [ServicePackage.cspkg](#cspkg).
* Visual Studio használok, és szeretnék...
  * [Felhőalapú szolgáltatás létrehozása][vs_create]
  * [Egy meglévő felhőszolgáltatáshoz újrakonfigurálása][vs_reconfigure]
  * [Egy Felhőszolgáltatás-projekt telepítése][vs_deploy]
  * [Távoli asztali kapcsolatot a felhő példánya.][remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
A **ServiceDefinition.csdef** fájl határozza meg a beállításokat, amelyek segítségével az Azure-felhőszolgáltatás konfigurálása. A [Azure szolgáltatás definíciós séma (.csdef fájl)](https://msdn.microsoft.com/library/azure/ee758711.aspx) az engedélyezett formátum biztosít a szolgáltatásdefiníciós fájlban. A következő példa bemutatja a beállítások a webes és feldolgozói szerepkörök adható meg:

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

Olvassa el a [szolgáltatás definíciós séma](https://msdn.microsoft.com/library/azure/ee758711.aspx) kra az itt használt XML-séma, azonban itt van néhány elem gyors magyarázata:

**Webhelyek**  
A webhelyekhez vagy webes alkalmazásokhoz, amelyek az IIS7 definícióját tartalmazza.

**InputEndpoints**  
A felhőalapú szolgáltatással való kapcsolatfelvételre használt vonatkozó definíciókat tartalmazza.

**InternalEndpoints**  
Végpontok kommunikálnak egymással a szerepkörpéldányok által használt definíciókat tartalmazza.

**ConfigurationSettings**  
Egy adott szerepkör szolgáltatások beállítás definícióit tartalmazza.

**Tanúsítványok**  
A tanúsítványok, a szerepkör szükséges definíciókat tartalmazza. Az előző példakódban egy Azure Connect konfigurálásához használt tanúsítvány látható.

**LocalResources**  
A helyi tároló-erőforrások definícióját tartalmazza. A helyi tároló egyik erőforrásához egy fenntartott könyvtárat a virtuális gép szerepkör példánya fut. a fájlrendszerben.

**Importálása**  
Az importált modulok kapcsolatos definíciókat tartalmazza. Az előző példakódban a modulok a távoli asztali kapcsolat és az Azure Connect jeleníti meg.

**Indítása**  
A szerepkör indításakor futtatott feladatokat tartalmazza. A feladatok .cmd vagy végrehajtható fájlban vannak definiálva.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration.cscfg
A beállítások a felhőszolgáltatás levő értékek alapján határozza meg a **ServiceConfiguration.cscfg** fájlt. Megadja, hogy telepíteni szeretné az egyes szerepkörökhöz, a fájlban található példányok száma. A konfigurációs beállítások keresése a szolgáltatásdefiníciós fájlban meghatározott értékek hozzáadódnak a szolgáltatás konfigurációs fájljában. Bármely a felhőszolgáltatáshoz társított felügyeleti tanúsítványok ujjlenyomatait is bekerülnek a fájlt. A [Azure szolgáltatás konfigurációs séma (.cscfg fájl)](https://msdn.microsoft.com/library/azure/ee758710.aspx) az engedélyezett formátum biztosít a szolgáltatás konfigurációs fájljában.

A szolgáltatás konfigurációs fájlja nem az alkalmazás együtt van csomagolva, de tölt fel az Azure-bA külön fájlt, és a felhőalapú szolgáltatás konfigurálására szolgál. A felhőalapú szolgáltatás ismételt üzembe helyezésével feltöltheti egy új szolgáltatás konfigurációs fájljában. A felhőalapú szolgáltatáshoz a konfigurációs értékeket módosíthatja a felhőalapú szolgáltatás futása közben. A következő példa bemutatja a konfigurációs beállítások a webes és feldolgozói szerepkörök adható meg:

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

Olvassa el a [szolgáltatás konfigurációs séma](https://msdn.microsoft.com/library/azure/ee758710.aspx) átláthatóbbá az itt használt XML-séma, azonban itt van egy gyors magyarázat elemek:

**Példányok**  
Konfigurálja a futó a szerepkör példányainak számát. Ha szeretné megakadályozni a felhőalapú szolgáltatás frissítéskor esetlegesen elérhetetlenné válik, ajánlott telepíteni a webalkalmazás számára is elérhető szerepkörök egynél több példánya. Egynél több példány telepítésével tartja vannak a irányelveinek be a [Azure számítási szolgáltatás szolgáltatói szerződésben (SLA)](http://azure.microsoft.com/support/legal/sla/), amely biztosítja, hogy az 99,95 % külső kapcsolatot internetre szerepkörökhöz, ha két vagy több szerepkörpéldányt beállítani a szolgáltatás telepítésére.

**ConfigurationSettings**  
A futó példányát tekintve szerepkör beállításait konfigurálja. Neve a `<Setting>` elemet meg kell egyeznie a beállításdefiníciókra a szolgáltatásdefiníciós fájlban.

**Tanúsítványok**  
Konfigurálja a szolgáltatás által használt tanúsítványok. Az előző példakódban a tanúsítványt a távelérési modul definiálást mutatja be. Értékét a *ujjlenyomat* attribútumot meg kell a tanúsítvány ujjlenyomatának használatával.

<p/>

> [!NOTE]
> A tanúsítvány ujjlenyomatát is hozzáadhatók a konfigurációs fájl egy szövegszerkesztőben. Vagy vehetők fel az értéket a **tanúsítványok** lapján a **tulajdonságok** a Visual Studio szerepkör oldalán.
> 
> 

## <a name="defining-ports-for-role-instances"></a>A szerepkörpéldányokért portok meghatározása
Azure lehetővé teszi, hogy csak egy belépési pont webes szerepkört. Ami azt jelenti, hogy az összes forgalom egy IP-címen keresztül történik. Konfigurálhatja a webhelyek számára, hogy ossza meg a port konfigurálása az állomásfejléc át tudja irányítani a kérelem a megfelelő helyre. Az alkalmazások jól ismert port az IP-cím figyelésére is konfigurálhatja.

Az alábbi minta egy webes szerepkör egy webhely és a webes alkalmazás mutatja be. A webhely 80-as porton az alapértelmezett belépési helyként van konfigurálva, és a webes alkalmazások úgy, hogy egy másodlagos állomásfejléc "mail.mysite.cloudapp.net" is nevezett kérelmeket fogadjon.

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
Frissítheti a felhőalapú szolgáltatás konfigurációját az Azure, a szolgáltatás offline állapotba helyezése nélkül futása közben. Módosítsa a konfigurációs adatokat, vagy töltsön fel egy új konfigurációs fájlt, vagy módosítsa a konfigurációs fájlt helyben elemre, és alkalmazza azt a futó szolgáltatás. A következő módosításokat létesíthető a szolgáltatás konfigurációját:

* **A konfigurációs beállítások értékének módosítása**  
  Ha az konfigurációs módosítások beállítást, a szerepkör példánya is dönt, hogy a módosítás alkalmazására, amíg a példány online állapotban, vagy a példány szabályosan újrahasznosítása, és közben a példányt a módosítás alkalmazására offline állapotban van.
* **A szolgáltatás topológiát szerepkörpéldányt beállítani módosítása**  
  Topológia változtatások nincsenek hatással a futó példányát, kivéve ahol távolít el egy példányát. Összes többi példányt általában nem kell újrahasznosítását; azonban ha szeretné, újrahasznosítja szerepkörpéldányokat topológiamódosítás válaszként.
* **A tanúsítvány ujjlenyomata módosítása**  
  A tanúsítvány csak akkor frissíthető, ha a szerepkörpéldány offline állapotban. Ha egy tanúsítvány hozzáadott, törölték, vagy módosítható, amíg a szerepkör példánya online állapotban, Azure szabályosan bontja a példány kapcsolatot frissítése a tanúsítványt, és azt újra online állapotba kerüljön a módosítás befejezése után.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Konfigurációs módosítások szolgáltatás futásidejű események kezelése
A [Azure futásidejű kódtár](https://msdn.microsoft.com/library/azure/mt419365.aspx) magában foglalja a [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.aspx) névtér, amely osztályok nyújt az Azure környezetbe szerepkörről való interakció. A [roleenvironment-et](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) osztály határozza meg a következő események előállított előtt és után a konfiguráció módosítása:

* **[Módosítása](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx) esemény**  
  Ez akkor fordul elő, a megadott példánya egy szerepkört, amelyen a szerepkörpéldányok le, ha szükséges a konfigurációs módosítás alkalmazása előtt.
* **[Módosított](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changed.aspx) esemény**  
  Egy megadott szerepkör-példányhoz a konfigurációs módosítás alkalmazása után következik be.

> [!NOTE]
> Tanúsítvány módosítások mindig a szerepkör példánya offline állapotba, mert azok emelje a RoleEnvironment.Changing vagy RoleEnvironment.Changed események.
> 
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
Az Azure-ban felhőszolgáltatásként alkalmazás központi telepítése, elő kell készítenie az alkalmazás a megfelelő formátumban. Használhatja a **CSPack** parancssori eszköz (telepített a [Azure SDK](https://azure.microsoft.com/downloads/)) a csomag fájl létrehozása a Visual Studio helyett.

**CSPack** határozható meg a csomag tartalma a szolgáltatásdefiníciós fájlban, és a szolgáltatás konfigurációs fájlja a tartalmát használja. **CSPack** hoz létre, amely feltöltheti az Azure használatával alkalmazás csomagfájlját (.cspkg) a [Azure-portálon](cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Alapértelmezés szerint a csomag neve `[ServiceDefinitionFileName].cspkg`, de megadhat egy másik nevet a használatával a `/out` lehetőség a **CSPack**.

**CSPack** itt található:  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> (Windowson) CSPack.exe érhető futtatásával a **Microsoft Azure parancssori** az SDK-val telepített helyi.  
> 
> Futtassa a CSPack.exe programot önmagában dokumentációjában az összes lehetséges kapcsolók és parancsok megjelenítéséhez.
> 
> 

<p />

> [!TIP]
> A felhőalapú szolgáltatás a helyi Futtatás a **Microsoft Azure Compute Emulator**, használja a **/copyonly** lehetőséget. Ez a funkció egy directory elrendezést, ahonnan azok is futtatható a compute emulator az alkalmazás a bináris fájlokat másolja.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>A csomag egy felhőalapú szolgáltatás példaparancs
Az alábbi példa létrehoz egy alkalmazáscsomagot, amely a webes szerepkör adatait tartalmazza. A parancs a szolgáltatásdefiníciós fájlt használja, a könyvtár, hol találhatók a bináris fájlokat, és a csomag fájl nevét határozza meg.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

Ha az alkalmazás egy webes és feldolgozói szerepkörök is tartalmaz, a következő parancsot használja:

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /out:[OutputFileName]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]
```

Ha a változóit az alábbiak szerint:

| Változó | Érték |
| --- | --- |
| \[Könyvtárnév\] |A gyökérkönyvtárban a projekt, amely tartalmazza a .csdef fájl az Azure-projekthez tartozó alkönyvtárat. |
| \[ServiceDefinition\] |A szolgáltatásdefiníciós fájl neve. Alapértelmezés szerint a fájl neve ServiceDefinition.csdef. |
| \[Kimenetifajlneve\] |A létrehozott fájl nevét. Általában ez értéke az alkalmazás nevét. Ha nincs fájl neve meg van adva, az alkalmazáscsomag elemként jön létre \[ApplicationName\].cspkg. |
| \[RoleName\] |A szerepkör a szolgáltatásdefiníciós fájlban meghatározott neve. |
| \[RoleBinariesDirectory] |A szerepkör bináris fájljainak helyét. |
| \[VirtualPath\] |A szolgáltatásdefiníció helyek szakaszában meghatározott minden egyes virtuális elérési fizikai könyvtárak. |
| \[PhysicalPath\] |A szolgáltatásdefiníció hely csomópontjában definiált minden egyes virtuális elérési tartalma fizikai könyvtárak. |
| \[RoleAssemblyName\] |A szerepkör bináris fájl nevét. |

## <a name="next-steps"></a>Következő lépések
I vagyok létrehozni egy cloud service-csomag, és szeretnék...

* [Távoli asztal beállítása a felhő példánya.][remotedesktop]
* [Egy Felhőszolgáltatás-projekt telepítése][deploy]

Visual Studio használok, és szeretnék...

* [Új felhőalapú szolgáltatás létrehozása][vs_create]
* [Egy meglévő felhőszolgáltatáshoz újrakonfigurálása][vs_reconfigure]
* [Egy Felhőszolgáltatás-projekt telepítése][vs_deploy]
* [Távoli asztal beállítása a felhő példánya.][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop-new-portal.md
[vs_remote]: ../vs-azure-tools-remote-desktop-roles.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md
