---
title: Azure Cloud Services definíciós séma (. csdef fájl) | Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 42
author: georgewallace
ms.author: gwallace
ms.openlocfilehash: b832723fdf773ff06c0b03b9aa80f542279cd309
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360629"
---
# <a name="azure-cloud-services-definition-schema-csdef-file"></a>Azure Cloud Services definíciós séma (. csdef fájl)
A szolgáltatás definíciós fájlja határozza meg az alkalmazás szolgáltatási modelljét. A fájl tartalmazza a felhőalapú szolgáltatás számára elérhető szerepkörök definícióit, meghatározza a szolgáltatási végpontokat, és létrehozza a szolgáltatás konfigurációs beállításait. A konfigurációs beállítás értékei a szolgáltatás konfigurációs fájljában vannak beállítva, a [Cloud Service (klasszikus) konfigurációs séma](/previous-versions/azure/reference/ee758710(v=azure.100))szerint.

Alapértelmezés szerint a rendszer a Azure Diagnostics konfigurációs sémafájl fájlját telepíti a `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` könyvtárba. Cserélje `<version>` le az helyére az [Azure SDK](https://www.windowsazure.com/develop/downloads/)telepített verzióját.

A szolgáltatás definíciós fájljának alapértelmezett kiterjesztése. csdef.

## <a name="basic-service-definition-schema"></a>Alapszintű szolgáltatás-definíciós séma
A szolgáltatás definíciós fájljának tartalmaznia `ServiceDefinition` kell egy elemet. A szolgáltatási definíciónak tartalmaznia kell legalább egy szerepkör (`WebRole` vagy `WorkerRole`) elemet. Akár 25, egyetlen definícióban definiált szerepkört is tartalmazhat, és a szerepkörök típusait vegyesen is elvégezheti. A szolgáltatás definíciója tartalmazza azt a `NetworkTrafficRules` választható elemet is, amely korlátozza, hogy mely szerepkörök tudnak kommunikálni a megadott belső végpontokkal. A szolgáltatás definíciója tartalmazza a választható `LoadBalancerProbes` elemet is, amely tartalmazza a végpontok ügyfél által meghatározott állapotának mintavételét.

A szolgáltatás definíciós fájljának alapszintű formátuma a következő.

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

## <a name="schema-definitions"></a>Séma-definíciók
A következő témakörök ismertetik a sémát:

- [LoadBalancerProbe séma](schema-csdef-loadbalancerprobe.md)
- [WebRole séma](schema-csdef-webrole.md)
- [WorkerRole séma](schema-csdef-workerrole.md)
- [NetworkTrafficRules séma](schema-csdef-networktrafficrules.md)

##  <a name="ServiceDefinition"></a>ServiceDefinition elem
Az `ServiceDefinition` elem a szolgáltatás definíciós fájljának legfelső szintű eleme.

Az alábbi táblázat az `ServiceDefinition` elem attribútumait ismerteti.

| Attribútum               | Leírás |
| ----------------------- | ----------- |
| name                    |Kötelező. A szolgáltatás neve. A névnek egyedinek kell lennie a szolgáltatásfiók keretén belül.|
| topologyChangeDiscovery | Választható. Meghatározza a topológia változási értesítésének típusát. Lehetséges értékek a következők:<br /><br /> -   `Blast`– A frissítést a lehető leghamarabb elküldi az összes szerepkör-példánynak. Ha a beállítás lehetőséget választja, a szerepkörnek újra kell tudnia kezelni a topológia frissítését.<br />-   `UpgradeDomainWalk`– Az egyes szerepkör-példányok frissítését szekvenciális módon küldi el, miután az előző példány sikeresen elfogadta a frissítést.|
| sémaverzióval           | Választható. Megadja a szolgáltatás definíciós sémájának verzióját. A séma verziója lehetővé teszi a Visual Studio számára, hogy kiválassza a séma-ellenőrzéshez használandó megfelelő SDK-eszközöket, ha az SDK több verziója is telepítve van egymás mellett.|
| upgradeDomainCount      | Választható. Meghatározza, hogy hány frissítési tartomány van lefoglalva a szolgáltatás szerepkörei között. A szerepkör-példányok egy frissítési tartományhoz vannak lefoglalva, ha a szolgáltatás telepítve van. További információ: [Cloud Service-szerepkör vagy-telepítés frissítése](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment), [a virtuális gépek elérhetőség kezelése](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) és [a felhőalapú szolgáltatási modell](https://docs.microsoft.com/azure/cloud-services/cloud-services-model-and-package).<br /><br /> Akár 20 frissítési tartományt is megadhat. Ha nincs megadva, a frissítési tartományok alapértelmezett száma 5.|
