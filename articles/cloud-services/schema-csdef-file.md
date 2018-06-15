---
title: Azure Cloud Services – Definition séma (.csdef fájl) |} Microsoft Docs
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
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: d5e086a952e18e477177634e5c197c27d4a5cc5f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359016"
---
# <a name="azure-cloud-services-definition-schema-csdef-file"></a>Azure Cloud Services Definition séma (.csdef fájl)
A szolgáltatásdefiníciós fájl határozza meg a szolgáltatásmodellt az alkalmazáshoz. A fájl egy felhőalapú szolgáltatás számára elérhetők lesznek a szerepkörök definícióit tartalmazza, adja meg a végpontok, és megállapítja a szolgáltatás konfigurációs beállításait. Konfigurációs beállítás értéke a konfigurációs fájlban, szerint a [Felhőszolgáltatás (klasszikus) konfigurációs séma](http://msdn.microsoft.com/library/b1ae68cd-cc95-48cb-a4a4-da91dc708a35).

Alapértelmezés szerint a Azure Diagnostics-konfigurációs fájl van telepítve a `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` könyvtár. Cserélje le `<version>` a telepített verzióját az [Azure SDK](http://www.windowsazure.com/develop/downloads/).

Az alapértelmezett kiterjesztése a szolgáltatásdefiníciós fájl .csdef.

## <a name="basic-service-definition-schema"></a>Alapszintű service definition séma
A szolgáltatásdefiníciós fájlban tartalmaznia kell egy `ServiceDefinition` elemet. A szolgáltatásdefiníció tartalmaznia kell legalább egy szerepkör (`WebRole` vagy `WorkerRole`) elemet. Legfeljebb 25 szerepköröket, meghatározott egyetlen meghatározást tartalmazhat, és kombinálhatja a felhasználóiszerepkör-típusokhoz. A szolgáltatásdefiníció is tartalmazza az opcionális `NetworkTrafficRules` elem, amelynek mely szerepkörök korlátozza a megadott belső végpontokkal való kommunikáció is. A szolgáltatásdefiníció is tartalmazza az opcionális `LoadBalancerProbes` elem, amely tartalmazza a felhasználói állapotának mintavételt végpontok definiálva.

A szolgáltatásdefiníciós fájlban alapvető formátuma a következő.

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
A következő témakörök ismertetik a sémában:

- [LoadBalancerProbe séma](schema-csdef-loadbalancerprobe.md)
- [WebRole séma](schema-csdef-webrole.md)
- [WorkerRole séma](schema-csdef-workerrole.md)
- [NetworkTrafficRules séma](schema-csdef-networktrafficrules.md)

##  <a name="ServiceDefinition"></a> ServiceDefinition elem
A `ServiceDefinition` eleme a legfelső szintű elemének a szolgáltatásdefiníciós fájlban.

A következő táblázat ismerteti az attribútumai a `ServiceDefinition` elemet.

| Attribútum               | Leírás |
| ----------------------- | ----------- |
| név                    |Kötelező. A szolgáltatás nevét. A név a szolgáltatásfiók belül egyedinek kell lennie.|
| topologyChangeDiscovery | Választható. Határozza meg a topológia módosítási értesítést. Lehetséges értékek:<br /><br /> -   `Blast` -Küld a frissítés a lehető leghamarabb összes szerepkörpéldányt. Ha a beállítást választja, a szerepkör tudja kezelni a topológia a frissítés újraindítás nélkül kell lennie.<br />-   `UpgradeDomainWalk` – A frissítést küld minden szerepkörpéldányt soros módon az előző példány sikeresen fogadták el a frissítés után.|
| sémaverzióval           | Választható. Határozza meg a szolgáltatás definíciós séma verzióját. A sémaverzió lehetővé teszi, hogy válassza ki a megfelelő SDK eszközöket a sémaérvényesítés használandó, ha az SDK több verziója telepítve van a Visual Studio egymás mellett.|
| upgradeDomainCount      | Választható. A frissítési tartományok között, amelyek ezt a szolgáltatást a szerepkörök foglal le azt. A szolgáltatás telepítésekor szerepkörpéldányokat kiosztott frissítési tartományokhoz. További információkért lásd: [felhő szerepkör-szolgáltatás vagy a központi telepítési](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment).<br /><br /> Legfeljebb 20 frissítési tartományt is megadhat. Ha nincs megadva, a frissítési tartományok száma alapértelmezett érték 5.|