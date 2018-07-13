---
title: Azure Cloud Services – NetworkConfiguration séma |} A Microsoft Docs
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
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: ed071d1da30a598eef830b4485c246ffae09c950
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39004114"
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Azure Cloud Services – Config NetworkConfiguration séma

A `NetworkConfiguration` elem a szolgáltatás konfigurációs fájlja a virtuális hálózat és a DNS-értékeket adja meg. Ezek a beállítások megadása nem kötelező, a cloud services.

Használhatja a következő erőforrás, ha többet szeretne megtudni a virtuális hálózatok és a kapcsolódó sémák:

- [Cloud Service (klasszikus) konfigurációs séma](schema-cscfg-file.md)
- [Cloud Service (klasszikus) definíciós séma](schema-csdef-file.md)
- [Hozzon létre egy virtuális hálózat (klasszikus)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="networkconfiguration-element"></a>NetworkConfiguration elemet
A következő példa bemutatja a `NetworkConfiguration` elem és az alárendelt elemei.

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
| AccessControl | Választható. Adja meg a végpontokra hozzáférésére vonatkozó szabályok egy felhőalapú szolgáltatás. Egy karakterláncot határozza meg a hozzáférés-vezérlési neve `name` attribútum. A `AccessControl` egy vagy több elemet tartalmaz `Rule` elemeket. Egynél több `AccessControl` elem lehet definiálni.|
| Szabály | Választható. Meghatározza a megadott alhálózati IP-címtartományt a végrehajtandó műveletet. A szabály a sorrend határozza meg egy karakterláncértéket a a `order` attribútum. A szabály a kisebb szám a annál magasabb a prioritás. Például szabályokat a rendelési szám 100, 200-as és 300 sikerült adható meg. A sorszám: 100-vel rendelkező szabályhoz élvez a szabályt, amely rendelkezik egy 200 sorrendjét.<br /><br /> Egy karakterláncot határozza meg a műveletet a szabály a `action` attribútum. Lehetséges értékek:<br /><br /> -   `permit` – Itt adhatja meg, hogy csak a megadott alhálózat címtartományából csomagok kommunikál a végpontot.<br />-   `deny` – Itt adhatja meg, hogy a végpont a megadott alhálózati tartományban való hozzáférés megtagadva.<br /><br /> Egy karakterláncot határozza meg az alhálózat IP-címtartományt a szabály által érintett a `remoteSubnet` attribútum. Egy karakterláncot határozza meg a szabály leírása a `description` attribútum.|
| EndpointAcl | Választható. Adja meg a hozzáférés-vezérlési szabályok egy végpontnak hozzárendelését. A szerepkör, amely tartalmazza a végpont nevét határozza meg egy karakterláncot a `role` attribútum. Egy karakterláncot határozza meg a végpont neve a `endpoint` attribútum. Készletét neve `AccessControl` keres a karakterláncban meghatározott szabályokat, amelyek a alkalmazni kell a végpont a `accessControl` attribútum. Egynél több `EndpointAcl` elemek lehet definiálni.|
| DNS-kiszolgáló | Választható. Megadja a DNS-kiszolgáló beállításait. DNS-kiszolgálók nélkül a virtuális hálózat beállításait lehet megadni. Egy karakterláncot határozza meg a DNS-kiszolgáló nevét a `name` attribútum. Egy karakterláncot határozza meg a DNS-kiszolgáló IP-címét a `IPAddress` attribútum. Az IP-cím érvényes IPv4-címnek kell lennie.|
| VirtualNetworkSite | Választható. Adja meg a használni kívánt központi telepítése a felhőszolgáltatás virtuális hálózati telephely neve. Ez a beállítás nem hoz létre egy virtuális hálózati telephelyhez. Egy helyet, amely korábban már definiálva van a hálózati fájlt a virtuális hálózat hivatkozik. Cloud service csak lehet egy virtuális hálózat tagja. Ha nem adja meg ezt a beállítást, a felhőszolgáltatás nem lesznek telepítve olyan virtuális hálózatot. A virtuális hálózati telephely nevét határozza meg egy karakterláncot a `name` attribútum.|
| InstanceAddress | Választható. A társítás az alhálózathoz szerepkör vagy alhálózatokat megadja a virtuális hálózatban. Ha hozzárendel egy példány címre a szerepkör nevét, megadhatja az alhálózatok, akit be kíván társítani ehhez a szerepkörhöz. A `InstanceAddress` alhálózatok elemet tartalmaz. Egy karakterláncot határozza meg, amely az alhálózatra vagy alhálózatok társítva van a szerepkör nevét a `roleName` attribútum.|
| Alhálózat | Választható. Adja meg az alhálózatot, amelyet felel meg az alhálózat neve, a hálózat konfigurációs fájljában. Egy karakterláncot határozza meg az alhálózat neve a `name` attribútum.|
| Fenntartott IP-cím | Választható. Itt adhatja meg a fenntartott IP-cím, amely a központi telepítés társítva kell lennie. Fenntartott IP-cím létrehozása a fenntartott IP-cím létrehozásához kell használnia. Minden üzembe helyezés egy cloud service-ben is társítható egy fenntartott IP-cím. Egy karakterláncot határozza meg a fenntartott IP-cím nevére a `name` attribútum.|

## <a name="see-also"></a>Lásd még:
[Cloud Service (klasszikus) konfigurációs séma](schema-cscfg-file.md)