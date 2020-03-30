---
title: Azure Cloud Services-definíciós séma (.csdef fájl) | Microsoft dokumentumok
description: A szolgáltatásdefiníciós (.csdef) fájl egy alkalmazás szolgáltatásmodelljét határozza meg, amely a szolgáltatás elérhető szerepköreit, végpontjait és konfigurációs értékeit tartalmazza.
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 42
author: tgore03
ms.author: tagore
ms.openlocfilehash: dadb50bd0663f47e6a1bf3d58b5187c8b466964d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528370"
---
# <a name="azure-cloud-services-definition-schema-csdef-file"></a>Azure Cloud Services-definíciós séma (.csdef fájl)
A szolgáltatásdefiníciós fájl határozza meg egy alkalmazás szolgáltatásmodelljét. A fájl tartalmazza a felhőszolgáltatás számára elérhető szerepkörök definícióit, megadja a szolgáltatás végpontjait, és létrehozza a szolgáltatás konfigurációs beállításait. A konfigurációs beállítási értékek a szolgáltatás konfigurációs fájljában vannak beállítva, a [felhőszolgáltatás (klasszikus) konfigurációs sémája](/previous-versions/azure/reference/ee758710(v=azure.100))által leírtak szerint.

Alapértelmezés szerint az Azure Diagnostics konfigurációs sémafájl telepítve van a `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` könyvtárba. Cserélje `<version>` le az [Azure SDK telepített verziójára.](https://www.windowsazure.com/develop/downloads/)

A szolgáltatásdefiníciós fájl alapértelmezett kiterjesztése .csdef.

## <a name="basic-service-definition-schema"></a>Alapszintű szolgáltatásdefiníciós séma
A szolgáltatásdefiníciós fájlnak egy `ServiceDefinition` elemet kell tartalmaznia. A szolgáltatásdefiníciónak legalább egy`WebRole` szerepkör `WorkerRole`( vagy ) elemet kell tartalmaznia. Legfeljebb 25, egyetlen definícióban definiált szerepkört tartalmazhat, és a szerepkörtípusok keverhetők. A szolgáltatásdefiníció tartalmazza `NetworkTrafficRules` azt a választható elemet is, amely korlátozza, hogy mely szerepkörök kommunikálhatnak a megadott belső végpontokkal. A szolgáltatásdefiníció tartalmazza `LoadBalancerProbes` a választható elemet is, amely a végpontok vevő által definiált állapotmintait tartalmazza.

A szolgáltatásdefiníciós fájl alapformátuma a következő.

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
Az alábbi témakörök a sémát ismertetik:

- [LoadBalancerProbe-séma](schema-csdef-loadbalancerprobe.md)
- [WebRole-séma](schema-csdef-webrole.md)
- [WorkerRole-séma](schema-csdef-workerrole.md)
- [NetworkTrafficRules-séma](schema-csdef-networktrafficrules.md)

##  <a name="servicedefinition-element"></a><a name="ServiceDefinition"></a>Szolgáltatásdefiníciós elem
Az `ServiceDefinition` elem a szolgáltatásdefiníciós fájl legfelső szintű eleme.

Az alábbi táblázat az `ServiceDefinition` elem attribútumait ismerteti.

| Attribútum               | Leírás |
| ----------------------- | ----------- |
| név                    |Kötelező. A szolgáltatás neve. A névnek egyedinek kell lennie a szolgáltatásfiókban.|
| topológiaChangeDiscovery | Választható. Megadja a topológia módosítási értesítésének típusát. Lehetséges értékek:<br /><br /> -   `Blast`- Elküldi a frissítést a lehető leghamarabb az összes szerepkör példányok. Ha a lehetőséget választja, a szerepkörnek újraindulás nélkül is kezelnie kell a topológia frissítést.<br />-   `UpgradeDomainWalk`– A frissítést az előző példány sikeres elfogadása után egymás után egymás után elküldi az egyes szerepkörpéldányoknak.|
| sémaverzió           | Választható. A szolgáltatásdefiníciós séma verzióját adja meg. A sémaverzió lehetővé teszi a Visual Studio számára, hogy kiválassza a séma-ellenőrzéshez használandó megfelelő SDK-eszközöket, ha az SDK több verziója van telepítve egymás mellett.|
| upgradeDomainCount      | Választható. Megadja, hogy hány frissítési tartomány ban vannak lefoglalva a szolgáltatás szerepkörei. A szerepkörpéldányok a szolgáltatás telepítésekor egy frissítési tartományhoz vannak rendelve. További információ: [A felhőszolgáltatási szerepkör vagy üzembe helyezés frissítése](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment), A virtuális gépek [rendelkezésre állásának kezelése](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) és a Mi a [felhőszolgáltatási modell.](https://docs.microsoft.com/azure/cloud-services/cloud-services-model-and-package)<br /><br /> Legfeljebb 20 frissítési tartományt adhat meg. Ha nincs megadva, a frissítési tartományok alapértelmezett száma 5.|
