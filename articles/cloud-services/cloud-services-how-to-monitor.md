---
title: Az Azure Cloud Services monitorozása |} A Microsoft Docs
description: Leírja, mi az Azure Cloud Service figyelése magában foglalja, és milyen beállítások egy része a rendszer.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: ''
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: jeconnoc
ms.openlocfilehash: 844fef9a87c1db06c6415c59d4be26caf928382b
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53789467"
---
# <a name="introduction-to-cloud-service-monitoring"></a>Bevezetés a felhőalapú szolgáltatás figyelése

Minden olyan felhőalapú szolgáltatás fő teljesítménymutatói követheti nyomon. Minden felhőszolgáltatási szerepkör minimális adatokat gyűjti össze: CPU-használat, a hálózati forgalom és a lemezkihasználtság. Ha a felhőszolgáltatáshoz a `Microsoft.Azure.Diagnostics` bővítmény egy szerepkörhöz, a szerepkör a alkalmazni az adatok további pontok képes összegyűjteni. Ez a cikk az Azure Diagnostics bevezetést nyújt a Cloud Services.

Alapszintű figyelési szerepkör példányai a teljesítményszámlálók adatainak mintavételezés és összegyűjtött 3 perces időközönként. Ez a alapvető figyelési adatok nincs tárolva a tárfiók, és azt további költségek nélkül kapcsolódik.

Speciális monitorozás, a további metrikákat mintát venni, és 5 perc, 1 óra, és 12 óra időközönként gyűjtött. Az összesített adatok a storage-fiókban, táblák, tárolja, és a 10 nap után törlődnek. A használt tárfiók szerepkör; van konfigurálva. különböző szerepkörök különböző tárfiókokban is használhat. Ez a kapcsolati karakterlánc van konfigurálva a [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) és [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) fájlokat.


## <a name="basic-monitoring"></a>Alapszintű figyelés

A bevezetés leírtaknak egy felhőalapú szolgáltatás automatikusan gyűjt alapvető figyelési adatok a gazdagép virtuális gépről. A Processzor-százalékos aránya, a bejövő és kimenő hálózati és lemez olvasási/írási szerepel. Figyelési adatokat gyűjti össze a felhőalapú szolgáltatás, az Azure Portal áttekintés és metrikák oldalak automatikusan jelenik meg. 

Alapszintű figyelés, a storage-fiók nem igényel. 

![Alapszintű felhőszolgáltatás csempék figyelése](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Speciális figyelés

Speciális monitorozás használatát foglalja magában a **Azure Diagnostics** bővítmény (és opcionálisan az Application Insights SDK-t) a figyelni kívánt szerepkört. A diagnosztikai bővítményt használ egy konfigurációs fájl (szerepkörönként) nevű **diagnostics.wadcfgx** a figyelt diagnosztikai metrikák konfigurálása. Az Azure diagnosztikai bővítmény gyűjt, és tárolja az adatokat egy Azure Storage-fiókot. Ezek a beállítások vannak konfigurálva az a **.wadcfgx**, [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef), és [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) fájlokat. Ez azt jelenti, hogy nincs-e egy plusz költséggel speciális figyelés.

Egyes szerepkörök létrehozása a Visual Studio hozzáadja az Azure Diagnostics bővítmény azt. Ez a diagnosztikai bővítmény az alábbi típusú információkat gyűjthet:

* Egyéni teljesítményszámlálók
* Alkalmazásnaplók
* Windows-eseménynaplók,
* .NET-esemény forrása
* IIS-naplók
* Jegyzékalapú ETW
* összeomlási memóriaképek,
* Ügyfél-hibanaplók

> [!IMPORTANT]
> Ezeket az adatokat összesített értéket jelenít meg a tárfiókba, míg a portálon nem **nem** natív módon az adatokat diagram. Azt javasoljuk, hogy egy másik szolgáltatást, például az Application Insights, az alkalmazásba integrálja.

## <a name="setup-diagnostics-extension"></a>A telepítő a diagnosztikai bővítmény

Első, ha nem rendelkezik egy **klasszikus** tárfiók [hozzon létre egyet](../storage/common/storage-quickstart-create-account.md). Ellenőrizze, hogy a tárfiók létrejön a **klasszikus üzemi modellt** megadott.

Ezután keresse meg a **tárfiók (klasszikus)** erőforrás. Válassza ki **beállítások** > **hozzáférési kulcsok** , és másolja a **elsődleges kapcsolati karakterlánc** értéket. Ez az érték a felhőszolgáltatáshoz van szükség. 

Módosítania kell engedélyezni kell, a speciális diagnosztikai két konfigurációs fájl **ServiceDefinition.csdef** és **ServiceConfiguration.cscfg**.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef

Az a **ServiceDefinition.csdef** fájlt, adjon hozzá egy új beállítás nevesített `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` minden szerepkör, amely használja a speciális diagnosztikát. A Visual Studio új projekt létrehozásakor a fájlt hozzá ezt az értéket. Abban az esetben, ha ez hiányzik, akkor most már hozzáadhatja. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Ez határozza meg, hogy hozzá kell adni minden új beállítás **ServiceConfiguration.cscfg** fájlt. 

Valószínűleg már kettővel fog rendelkezni **.cscfg** fájlok, az egyik nevű **ServiceConfiguration.cloud.cscfg** üzembe helyezéséhez az Azure és a egy nevű **ServiceConfiguration.local.cscfg** helyi telepítés esetén az emulált környezetben használt. Nyissa meg és módosíthatja az egyes **.cscfg** fájlt. Adjon hozzá egy beállítást nevű `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`. Állítsa az értékét a **elsődleges kapcsolati karakterlánc** a klasszikus tárfiók. Ha szeretné használni a helyi tároló a fejlesztői gépen, `UseDevelopmentStorage=true`.

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

Amikor közzéteszi a Felhőszolgáltatás a Visual Studióból, akkor a diagnosztikai adatok küldése az Application Insights lehetőséget kapnak. Ekkor az Application Insights az Azure-erőforrás létrehozásához, vagy egy meglévő Azure-erőforrás elküldi az adatokat. A felhőszolgáltatás rendelkezésre állási, teljesítmény, hibák és használatát az Application Insights is figyeli. Egyéni diagramok is hozzáadhatók az Application Insights, hogy az adatokat láthatja, hogy a legtöbb számít. Szerepkör-példány adatait a felhőszolgáltatási projektet az Application Insights SDK használatával gyűjthetők össze. Hogyan integrálható az Application Insights további információkért lásd: [Application Insights a Cloud Services](../azure-monitor/app/cloudservices.md).

Vegye figyelembe, hogy míg az Application Insights segítségével a teljesítményszámlálók (és a többi beállítás) megadott keresztül a Windows Azure Diagnostics bővítményt, akkor csak sokoldalúbb felhasználói élményben lekérése az Application Insights SDK való integrálásával a feldolgozó és a webes szerepkör.


## <a name="next-steps"></a>További lépések

- [Ismerje meg az Application Insights a Cloud Services](../azure-monitor/app/cloudservices.md)
- [Állítsa be a teljesítményszámlálók](diagnostics-performance-counters.md)

