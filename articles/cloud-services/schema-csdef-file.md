---
title: Azure Cloud Services – definíciós séma (.csdef fájl) |} A Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: b7735dbf-8e91-4d1b-89f7-2f17e9302469
caps.latest.revision: 42
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: df2f7c1bf99c13779e5720e15d8d669aa4f945c0
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39002749"
---
# <a name="azure-cloud-services-definition-schema-csdef-file"></a>Az Azure Cloud Services – definíciós séma (.csdef fájl)
A szolgáltatásdefiníciós fájl határozza meg, hogy a modell egy alkalmazáshoz. A fájl a felhőszolgáltatás számára elérhető szerepköröket definícióit tartalmazza, meghatározza a szolgáltatásvégpontokat, és hozza létre a szolgáltatás konfigurációs beállításait. Konfigurációs beállítás értéke a konfigurációs fájlban, leírtak szerint a [Felhőszolgáltatás (klasszikus) konfigurációs séma](http://msdn.microsoft.com/library/b1ae68cd-cc95-48cb-a4a4-da91dc708a35).

Alapértelmezés szerint a fájl az Azure Diagnostics konfigurációs van telepítve a `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` könyvtár. Cserélje le `<version>` telepített verziójának a [Azure SDK](http://www.windowsazure.com/develop/downloads/).

Az alapértelmezett kiterjesztése a szolgáltatásdefiníciós fájl .csdef esetében.

## <a name="basic-service-definition-schema"></a>Alapszintű szolgáltatásdefiníciós sémában
A szolgáltatásdefiníciós fájlt tartalmaznia kell egy `ServiceDefinition` elemet. A szolgáltatásdefiníció tartalmaznia kell legalább egy szerepkört (`WebRole` vagy `WorkerRole`) elemet. Tartalmazhat, egyetlen-definícióban meghatározott legfeljebb 25 szerepkörök és szerepkör-típusok vegyesen is. A szolgáltatásdefiníció is tartalmaz a választható `NetworkTrafficRules` elem, amely korlátozza, mely szerepkörök megadott belső végpontjaihoz kommunikálhatnak. A szolgáltatásdefiníció is tartalmaz a választható `LoadBalancerProbes` elem, amely tartalmazza az ügyfél állapotadat-mintavételek végpontok definiálva.

A szolgáltatásdefiníciós fájlt alapvető formátuma a következő.

```xml
<ServiceDefinition name="<service-name>" topologyChangeDiscovery="<change-type>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" upgradeDomainCount="<number-of-upgrade-domains>" schemaVersion="<version>">
  
  <LoadBalancerProbes>
         …
  </LoadBalancerProbes>
  
  <WebRole …>
         …
  </WebRole>
  
  <WorkerRole …>
         …
  </WorkerRole>
  
  <NetworkTrafficRules>
         …
  </NetworkTrafficRules>

</ServiceDefinition>
```

## <a name="schema-definitions"></a>Sémadefiníciók
Az alábbi témakörök ismertetik a séma:

- [LoadBalancerProbe séma](schema-csdef-loadbalancerprobe.md)
- [WebRole séma](schema-csdef-webrole.md)
- [WorkerRole séma](schema-csdef-workerrole.md)
- [NetworkTrafficRules séma](schema-csdef-networktrafficrules.md)

##  <a name="ServiceDefinition"></a> A szolgáltatásdefiníció elem
A `ServiceDefinition` elem a szolgáltatásdefiníciós fájlt a legfelső szintű elemének.

A következő táblázat ismerteti az attribútumai a `ServiceDefinition` elemet.

| Attribútum               | Leírás |
| ----------------------- | ----------- |
| név                    |Kötelező. A szolgáltatás neve. A név a szolgáltatás fiókja egyedinek kell lennie.|
| topologyChangeDiscovery | Választható. Határozza meg topológia módosítási értesítést. Lehetséges értékek:<br /><br /> -   `Blast` -Küld a frissítést minél hamarabb összes szerepkörpéldányra. Ha a beállítást választja, a szerepkör tudja kezelni a topológia a frissítés újraindítás nélkül kell lennie.<br />-   `UpgradeDomainWalk` – A frissítést küld minden szerepkörpéldány soros módon a korábbi példánya sikeresen fogadta el a frissítés után.|
| sémaverzióval           | Választható. Határozza meg a szolgáltatásdefiníciós sémában verzióját. A séma verziója lehetővé teszi, hogy válassza ki a megfelelő SDK-eszközök séma érvényesítése használandó, ha az SDK egynél több verziója van telepítve a Visual Studio egymás mellett.|
| upgradeDomainCount      | Választható. A frissítési tartományok között, amelyek a szolgáltatás szerepkörök lefoglalásának számát adja meg. Szerepkör példányai vannak lefoglalva a frissítési tartományokhoz, a szolgáltatás telepítésekor. További információkért lásd: [felhőszolgáltatási szerepkör vagy telepítés frissítése](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment).<br /><br /> Akár 20 frissítési tartományt is megadhat. Ha nincs megadva, az alapértelmezett frissítési tartományok száma az 5.|