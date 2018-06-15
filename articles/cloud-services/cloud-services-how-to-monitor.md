---
title: Azure-Felhőszolgáltatás figyelése |} Microsoft Docs
description: Ismerteti, mi az Azure-Felhőszolgáltatásban figyelése magában foglalja, és milyen beállítások egy része a rendszer.
services: cloud-services
documentationcenter: ''
author: thraka
manager: timlt
editor: ''
ms.assetid: ''
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: adegeo
ms.openlocfilehash: f3a3a1beb8540ee8ab0502379396c06ea505fb44
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2018
ms.locfileid: "29149906"
---
# <a name="introduction-to-cloud-service-monitoring"></a>Bevezetés a felhőalapú szolgáltatás figyelése

A felhőszolgáltatás fontos teljesítménymutatókat figyelheti. Minden felhőalapú szolgáltatás-szerepkör minimális adatokat gyűjt: CPU használati, a hálózati használati és a lemezkihasználtság lekéréséhez. A felhőszolgáltatás-e a `Microsoft.Azure.Diagnostics` szerepkör, akkor az a szerepkör alkalmazott bővítmény gyűjthet az adatok további pontok. A cikkben megismerkedhet az Azure Diagnostics Felhőszolgáltatásai számára.

Alapvető figyelési teljesítményszámláló-adatokat a szerepkörpéldányok mintát, és 3 perces időközönként gyűjti. A tárfiók nem tárolja az alapvető figyelési adatokat, és nem jelent többletköltséget vele társított.

Speciális figyelési további metrikák mintát és 5 perc, 1 óra és 12 óra időközönként gyűjti. Az összesített adatokat táblázatokban, a tárfiók tárolja, és 10 nap után véglegesen törlődnek. A használt tárfiók konfigurálható szerepkör; eltérő tárfiókokból különböző szerepkörök is használhatja. Ez a kapcsolati karakterláncot úgy van konfigurálva a [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) és [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) fájlokat.


## <a name="basic-monitoring"></a>Alapszintű figyelés

A bevezetés leírtaknak egy felhőalapú szolgáltatás automatikusan gyűjti az alapvető figyelési adatokat a gazdagép virtuális gépről. Ezen adatok tartalmazzák a Processzor-százalékos, hálózati vagy kikapcsolása és lemez olvasási/írási. Figyelési adatokat gyűjti össze a felhőalapú szolgáltatás, az Azure portál áttekintése és a metrikák oldalain automatikusan jelenik meg. 

Alapvető figyelését a tárfiók nem szükséges. 

![csempék figyelést alapvető felhőalapú szolgáltatás](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Speciális figyelés

Speciális figyelésére használatát foglalja magában a **Azure Diagnostics** bővítmény (és nem kötelezően az Application Insights SDK) a figyelni kívánt szerepkört. A diagnosztika kiterjesztést használ egy konfigurációs fájl (egy szerepkör) nevű **diagnostics.wadcfgx** a figyelt diagnosztika metrikák konfigurálásához. Az Azure diagnosztikai bővítmény gyűjt, és tárolja az adatokat egy Azure Storage-fiókot. Ezek a beállítások vannak konfigurálva a **.wadcfgx**, [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef), és [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) fájlokat. Ez azt jelenti, hogy van-e egy plusz költsége speciális figyelést.

Egyes szerepkörök létrehozását, a Visual Studio ad hozzá az Azure Diagnostics bővítmény azt. A diagnosztika bővítmény össze tudják gyűjteni a következő típusú információkat:

* Egyéni teljesítményszámlálói
* Alkalmazásnaplók
* Windows-Eseménynapló
* .NET eseményforrás
* IIS-naplók
* Jegyzékfájl alapú ETW
* Összeomlási memóriaképek
* Ügyfél hibanaplókat

> [!IMPORTANT]
> Ezeket az adatokat a tárfiók összesítve, amíg a portál does **nem** biztosít a Diagramadat natív módot. Erősen ajánlott, hogy egy másik szolgáltatást, mint az Application Insights az alkalmazásba integrálja.

## <a name="setup-diagnostics-extension"></a>A telepítő diagnosztika bővítmény

Első, ha nem rendelkezik egy **klasszikus** tárfiókot, [hozzon létre egyet](../storage/common/storage-create-storage-account.md#create-a-storage-account). Győződjön meg arról, hogy a storage-fiók jön létre a **klasszikus telepítési modell** megadott.

Ezután keresse meg a **(klasszikus) tárfiókot** erőforrás. Válassza ki **beállítások** > **hívóbetűk** , és másolja a **elsődleges kapcsolódási karakterlánc** érték. Ez az érték kell a felhőalapú szolgáltatáshoz. 

Meg kell változtatnia a speciális diagnosztika engedélyezése, két konfigurációs fájlt **ServiceDefinition.csdef** és **ServiceConfiguration.cscfg**.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef

Az a **ServiceDefinition.csdef** fájlt, adja hozzá egy új nevű beállításával `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` az egyes szerepkörökhöz, amely használja a speciális diagnosztika. A Visual Studio új projekt létrehozásakor ad hozzá a fájl ezt az értéket. Abban az esetben, ha hiányzik, most adhat hozzá. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Ez határozza meg, hogy egy új beállítás, amelyet hozzá kell adni minden **ServiceConfiguration.cscfg** fájlt. 

Valószínűleg van két **.cscfg** fájlok, nevű **ServiceConfiguration.cloud.cscfg** Azure, és olyan gyermektartománya történő üzembe helyezéshez **ServiceConfiguration.local.cscfg** helyi telepítés esetén a emulált környezetben használt. Nyissa meg és módosíthatja az egyes **.cscfg** fájlt. Egy nevű Alkalmazásbeállítás hozzáadása `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`. Állítsa be az értéket a **elsődleges kapcsolódási karakterlánc** a klasszikus tárfiók. Ha a helyi tároló a fejlesztői gépen használni kívánt, `UseDevelopmentStorage=true`.

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

Ha közzéteszi a Visual Studio Felhőszolgáltatás, lehetősége van a diagnosztikai adatok küldése az Application Insights lehetőséget. Ekkor az Application Insights Azure-erőforrás létrehozása, vagy elküldheti az adatokat egy meglévő Azure-erőforrás. A felhőalapú szolgáltatás rendelkezésre állási, a teljesítmény, a hibák és a használati Application Insights tudja figyelni. Egyéni diagramok felveheti az Application Insights részére, hogy az adatokat látja, hogy a legtöbb számít. Az Application Insights SDK használatával a felhőszolgáltatás-projekt szerepkör példány adatokat lehessen gyűjteni. Az Application Insights integrációjával kapcsolatos további információkért lásd: [Application Insights a Cloud Serviceshez](../application-insights/app-insights-cloudservices.md).

Vegye figyelembe, hogy közben az Application Insights segítségével a teljesítményszámlálók (és az egyéb beállítások) megadott, akkor csak a Windows Azure diagnosztikai bővítményével lekérni egy gazdagabb élmény azokat az Application Insights SDK integrálásával a munkavégző és a webes szerepkör.


## <a name="next-steps"></a>További lépések

- [További tudnivalók az Application Insights a Felhőszolgáltatásokkal.](../application-insights/app-insights-cloudservices.md)
- [Teljesítményszámlálók beállítása](diagnostics-performance-counters.md)

