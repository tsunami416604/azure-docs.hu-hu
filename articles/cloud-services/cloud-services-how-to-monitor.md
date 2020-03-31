---
title: Azure felhőszolgáltatás figyelése | Microsoft dokumentumok
description: Bemutatja, hogy az Azure Cloud-szolgáltatás milyen figyelési lehetőséggel jár, és milyen lehetőségei vannak.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 01/29/2018
ms.author: tagore
ms.openlocfilehash: 61c794ba03934ae1828ba310f3f776bfb61b652b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273097"
---
# <a name="introduction-to-cloud-service-monitoring"></a>Bevezetés a felhőszolgáltatás figyelésének bevezetésébe

Bármely felhőszolgáltatás fő teljesítménymutatói figyelheti. Minden felhőszolgáltatási szerepkör minimális adatokat gyűjt: processzorhasználat, hálózati használat és lemezkihasználtság. Ha a felhőszolgáltatás `Microsoft.Azure.Diagnostics` a bővítmény egy szerepkörre van alkalmazva, a szerepkör további adatpontokat gyűjthet. Ez a cikk bemutatja az Azure Diagnostics for Cloud Services című témakört.

Az alapvető figyelés, a teljesítményszámláló-adatok a szerepkörpéldányok mintavételezése és gyűjtése 3 perces időközönként. Ez az alapvető figyelési adatok nem tárolják a tárfiókban, és nincs további költségek hez kapcsolódó.

A speciális figyelés, további metrikák mintavételezése és gyűjtése időközönként 5 perc, 1 óra és 12 óra időközönként. Az összesített adatok egy tárfiókban, táblákban tárolódnak, és 10 nap után törlődnek. A használt tárfiókot szerepkör konfigurálja; különböző tárfiókokat használhat a különböző szerepkörökhöz. Ez a [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) és [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) fájlokban lévő kapcsolati karakterlánccal van konfigurálva.


## <a name="basic-monitoring"></a>Alapvető ellenőrzés

Ahogy azt a bevezetés, a felhőszolgáltatás automatikusan összegyűjti az alapvető figyelési adatokat a gazdagép virtuális gép. Ezek az adatok magukban foglaljak a processzor százalékos arányát, a hálózat be- és kiírását, valamint a lemez olvasását/írását. Az összegyűjtött figyelési adatok automatikusan megjelennek a felhőszolgáltatás áttekintő és metrikaoldalain az Azure Portalon. 

Az alapszintű figyeléshez nincs szükség tárfiókra. 

![alapvető felhőszolgáltatás-figyelési csempék](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Speciális figyelés

A speciális figyelés magában foglalja az **Azure Diagnostics** bővítmény (és adott esetben az Application Insights SDK) használatát a figyelni kívánt szerepkörön. A diagnosztikai bővítmény egy **diagnostics.wadcfgx** nevű konfigurációs fájlt (szerepkörenként) használ a figyelt diagnosztikai metrikák konfigurálásához. Az Azure Diagnostic bővítmény adatokat gyűjt és tárol egy Azure Storage-fiókban. Ezek a beállítások a **.wadcfgx**, [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef)és [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) fájlokban vannak konfigurálva. Ez azt jelenti, hogy a fejlett figyeléshez további költségek merülnek fel.

Az egyes szerepkörök létrehozásakor a Visual Studio hozzáadja az Azure Diagnostics bővítményt. Ez a diagnosztikai bővítmény a következő típusú információkat gyűjtheti:

* Egyéni teljesítményszámlálók
* Alkalmazásnaplók
* Windows-eseménynaplók
* .NET eseményforrás
* IIS-naplók
* Jegyzékalapú ETW
* összeomlási memóriaképek,
* Vevői hibanaplók

> [!IMPORTANT]
> Bár az összes ilyen adatok a tárfiókba összesítve vannak, a portál **nem** biztosít natív módot az adatok ábrázolására. Erősen ajánlott, hogy egy másik szolgáltatás, például az Application Insights integrálása az alkalmazásba.

## <a name="setup-diagnostics-extension"></a>Beállítási diagnosztikai bővítmény

Először is, ha nem rendelkezik **klasszikus** tárfiókkal, [hozzon létre egyet.](../storage/common/storage-account-create.md) Győződjön meg arról, hogy a tárfiók a **klasszikus üzembe helyezési modell** vel van létrehozva.

Ezután keresse meg a **Storage-fiók (klasszikus)** erőforrás. Válassza a **Beállítások** > **elérése gombokat,** és másolja az **Elsődleges kapcsolat karakterláncának** értékét. Szüksége van erre az értékre a felhőszolgáltatáshoz. 

A speciális diagnosztika engedélyezéséhez két konfigurációs fájlt kell módosítania, a **ServiceDefinition.csdef** és **a ServiceConfiguration.cscfg**.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef

A **ServiceDefinition.csdef** fájlban adjon hozzá `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` egy új, speciális diagnosztikát használó szerepkörhöz elnevezett beállítást. A Visual Studio új projekt létrehozásakor hozzáadja ezt az értéket a fájlhoz. Abban az esetben, ha hiányzik, most felveheti. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Ez egy új beállítást határoz meg, amelyet minden **ServiceConfiguration.cscfg** fájlhoz hozzá kell adni. 

Valószínűleg két **.cscfg** fájllal rendelkezik, amelyek közül az **egyiket ServiceConfiguration.cloud.cscfg** néven helyezi üzembe az Azure-ba, és egy **Nevű ServiceConfiguration.local.cscfg** fájlt, amelyet az emulált környezetben helyi telepítésekhez használnak. Nyissa meg és módosítsa az egyes **.cscfg** fájlokat. Adjon hozzá `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`egy nevű beállítást . Állítsa be az értéket a klasszikus tárfiók **elsődleges kapcsolati karakterláncára.** Ha a fejlesztői gépen szeretné használni a `UseDevelopmentStorage=true`helyi tárolót, használja a használatát.

```xml
<ServiceConfiguration serviceName="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="WorkerRoleWithSBQueue1">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=mystorage;AccountKey=KWwkdfmskOIS240jnBOeeXVGHT9QgKS4kIQ3wWVKzOYkfjdsjfkjdsaf+sddfwwfw+sdffsdafda/w==" />
      
      <!-- or use the local development machine for storage
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
      -->
```

## <a name="use-application-insights"></a>Az Application Insights használata

Amikor közzéteszi a Felhőszolgáltatást a Visual Studio-ból, lehetősége van a diagnosztikai adatok elküldésére az Application Insightsba. Létrehozhatja az Application Insights Azure-erőforrást abban az időben, vagy elküldheti az adatokat egy meglévő Azure-erőforrásnak. A felhőszolgáltatás az Application Insights által figyelhető a rendelkezésre állás, a teljesítmény, a hibák és a használat. Egyéni diagramok adhatók hozzá az Application Insightshoz, így láthatja a legfontosabb adatokat. Szerepkörpéldány-adatok gyűjthetők az Application Insights SDK használatával a felhőszolgáltatás-projekt. Az Application Insights integrálásáról az Application Insights és a Cloud Services című [témakörben](../azure-monitor/app/cloudservices.md)talál további információt.

Vegye figyelembe, hogy bár az Application Insights segítségével megjelenítheti a Windows Azure Diagnosztikai bővítményen keresztül megadott teljesítményszámlálókat (és a többi beállítást), csak gazdagabb élményt kaphat az Application Insights SDK integrálásával a dolgozói és webes szerepkörök.


## <a name="next-steps"></a>További lépések

- [További információ az Application Insights szolgáltatásról a Felhőszolgáltatásokkal](../azure-monitor/app/cloudservices.md)
- [Teljesítményszámlálók beállítása](diagnostics-performance-counters.md)




