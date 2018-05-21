---
title: Azure Cloud Services – NetworkConfiguration séma |} Microsoft Docs
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: c1b94a9e-46e8-4a18-ac99-343c94b1d4bd
caps.latest.revision: 28
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: ebe81b2e4dea347eb22b173ff1e9baf1ee6bb75d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Azure Cloud Services – Config NetworkConfiguration séma

A `NetworkConfiguration` elem a szolgáltatás konfigurációs fájl határozza meg a virtuális hálózat és a DNS-értékek. Ezek a beállítások opcionálisak felhőszolgáltatásai számára.

A következő erőforrás tudhat meg többet a virtuális hálózatok és a kapcsolódó sémák használhatja:

- [Cloud Service (klasszikus) konfigurációs séma](schema-cscfg-file.md)
- [Cloud Service (klasszikus) Definition séma](schema-csdef-file.md)
- [Hozzon létre egy virtuális hálózatot (klasszikus)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="networkconfiguration-element"></a>NetworkConfiguration elemet
Az alábbi példa azt mutatja meg a `NetworkConfiguration` elem és gyermekelemeinek.

```xml
<ServiceConfiguration>
  <NetworkConfiguration>
    <AccessControls>
      <AccessControl name="aclName1">
        <Rule order="<rule-order>" action="<rule-action>" remoteSubnet="<subnet-address>" description="rule-description"/>
      </AccessControl>
    </AccessControls>
    <EndpointAcls>
      <EndpointAcl role="<role-name>" endpoint="<endpoint-name>" accessControl="<acl-name>"/>
    </EndpointAcls>
    <Dns>
      <DnsServers>
        <DnsServer name="<server-name>" IPAddress="<server-address>" />
      </DnsServers>
    </Dns>
    <VirtualNetworkSite name="<site-name>"/>
    <AddressAssignments>
      <InstanceAddress roleName="<role-name>">
        <Subnets>
          <Subnet name="<subnet-name>"/>
        </Subnets>
      </InstanceAddress>
      <ReservedIPs>
        <ReservedIP name="<reserved-ip-name>"/>
      </ReservedIPs>
    </AddressAssignments>
  </NetworkConfiguration>
</ServiceConfiguration>
```

A következő táblázat ismerteti az alárendelt elemei a `NetworkConfiguration` elemet.

| Elem       | Leírás |
| ------------- | ----------- |
| AccessControl | Választható. Adja meg a végpontokkal való hozzáférésére vonatkozó szabályok egy felhőalapú szolgáltatás. Egy karakterláncot határozza meg a hozzáférés-vezérlő neve `name` attribútum. A `AccessControl` egy vagy több elemet tartalmaz `Rule` elemek. Egynél több `AccessControl` elem adható meg.|
| Szabály | Választható. Meghatározza a megadott alhálózati az IP-címek végrehajtandó műveletet. A karakterlánc-érték határozza meg a szabály sorrendjét a `order` attribútum. Minél alacsonyabb a szabály number annál magasabb a prioritás. Például szabályok rendelési szám 100, 200-as és 300 sikerült lehet megadni. A szabály 100 számú élvez szabályt a 200-as sorrendhez.<br /><br /> Egy karakterláncot határozza meg a műveletet a szabály a `action` attribútum. Lehetséges értékek:<br /><br /> -   `permit` – Adja meg, hogy csak a megadott alhálózati tartományból csomagok kommunikál a végpont.<br />-   `deny` – Adja meg, hogy a hozzáférés megtagadva a végpontok a megadott alhálózati tartományba.<br /><br /> Egy karakterláncot határozza meg az alhálózat, IP-címek tartománya, hogy a szabály által érintett a `remoteSubnet` attribútum. A leírást a szabályhoz határozza meg egy karakterláncot a `description` attribútum.|
| EndpointAcl | Választható. Adja meg a hozzáférés-vezérlési szabályok a végpont-hozzárendelés. A szerepkör, amely tartalmazza a végpont nevét határozza meg egy karakterláncot a `role` attribútum. Egy karakterláncot határozza meg a végpont neve a `endpoint` attribútum. A készlet nevét `AccessControl` alkalmazni kell a végpont szabályok határozzák meg egy karakterláncot a `accessControl` attribútum. Egynél több `EndpointAcl` elem adható meg.|
| DNS-kiszolgáló | Választható. Megadja a DNS-kiszolgáló beállításait. Megadhatja a DNS-kiszolgálók nélkül a virtuális hálózati beállításait. Egy karakterláncot határozza meg a DNS-kiszolgáló nevét a `name` attribútum. A DNS-kiszolgáló IP-címét határozza meg egy karakterláncot a `IPAddress` attribútum. Az IP-cím érvényes IPv4-címnek kell lennie.|
| VirtualNetworkSite | Választható. Megadja a virtuális hálózati hely szülőhelyének szánt központi telepítése a felhőalapú szolgáltatás nevét. Ez a beállítás nem hoz létre egy virtuális hálózati hely. Egy helyet, amely korábban már definiálva van a hálózati fájlt a virtuális hálózat hivatkozik. Egy felhőalapú szolgáltatás csak egy virtuális hálózati tagja lehet. Ha nem adja meg ezt a beállítást, a felhőalapú szolgáltatás nem telepíti a virtuális hálózathoz. Egy karakterláncot határozza meg a virtuális hálózati telephely neve a `name` attribútum.|
| InstanceAddress | Választható. Adja meg a virtuális hálózati alhálózat szerepkör hozzárendelését vagy alhálózatokat. Amikor hozzárendel egy példány címet szerepkör nevét, amelybe a szerepkörhöz tartozó alhálózatok is megadhat. A `InstanceAddress` alhálózatok elemet tartalmaz. Egy karakterláncot határozza meg az alhálózatot vagy alhálózat társított szerepkör nevét a `roleName` attribútum.|
| Alhálózat | Választható. Adja meg az alhálózatot, amely megfelel az alhálózat neve, a hálózati konfigurációs fájlban. Egy karakterláncot határozza meg az alhálózat neve a `name` attribútum.|
| Foglalt IP-cím | Választható. Adja meg a fenntartott IP-cím, amely a központi telepítés társítva kell lennie. Fenntartott IP-cím létrehozása a fenntartott IP-cím létrehozásához kell használnia. Minden központi telepítési felhőszolgáltatásban társítható egy fenntartott IP-cím. Egy karakterláncot határozza meg a fenntartott IP-cím neve a `name` attribútum.|

## <a name="see-also"></a>Lásd még:
[Cloud Service (klasszikus) konfigurációs séma](schema-cscfg-file.md)