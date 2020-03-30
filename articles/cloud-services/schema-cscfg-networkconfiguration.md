---
title: Azure Cloud Services NetworkConfiguration Séma | Microsoft dokumentumok
description: Információ a szolgáltatáskonfigurációs fájl Hálózatikonfigurációelem gyermekelemeiről, amely a virtuális hálózat és a DNS értékeit adja meg.
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 28
author: tgore03
ms.author: tagore
ms.openlocfilehash: 695ba3acfd5af8797de6e6f7454e493d7863627c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529288"
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Az Azure Cloud Services konfigurációs hálózatikonfigurációs sémája

A `NetworkConfiguration` szolgáltatáskonfigurációs fájl eleme a virtuális hálózat és a DNS értékeit adja meg. Ezek a beállítások nem kötelezőek a felhőszolgáltatások esetében.

A virtuális hálózatokról és a kapcsolódó sémákról a következő erőforrás segítségével többet is megtudhat:

- [Felhőszolgáltatás (klasszikus) konfigurációs séma](schema-cscfg-file.md)
- [Felhőszolgáltatás (klasszikus) definícióséma](schema-csdef-file.md)
- [Virtuális hálózat létrehozása (klasszikus)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="networkconfiguration-element"></a>Hálózati konfigurációs elem
A következő példa `NetworkConfiguration` az elemet és annak gyermekelemeit mutatja be.

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
    <VirtualNetworkSite name="Group <RG-VNet> <VNet-name>"/>
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

Az alábbi táblázat az `NetworkConfiguration` elem gyermekelemeit ismerteti.

| Elem       | Leírás |
| ------------- | ----------- |
| AccessControl | Választható. A felhőszolgáltatás végpontjaihoz való hozzáférés szabályai. A hozzáférés-vezérlés nevét egy `name` attribútum karakterlánca határozza meg. Az `AccessControl` elem egy `Rule` vagy több elemet tartalmaz. Több `AccessControl` elem is definiálható.|
| Szabály | Választható. Megadja azt a műveletet, amelyet az IP-címek adott alhálózati tartományában végre kell hozni. A szabály sorrendjét az `order` attribútum karakterláncértéke határozza meg. Minél alacsonyabb a szabályszám, annál magasabb a prioritás. A szabályok at például 100, 200 és 300-as rendelésszámmal lehet megadni. A 100-as rendelési számú szabály elsőbbséget élvez a 200-as sorrenddel rendelkező szabállyal szemben.<br /><br /> A szabály műveletét az `action` attribútum karakterlánca határozza meg. Lehetséges értékek:<br /><br /> -   `permit`– Itt adhatja meg, hogy csak a megadott alhálózati tartományból származó csomagok kommunikálhassanak a végponttal.<br />-   `deny`– Megadja, hogy a rendszer megtagadja a hozzáférést a megadott alhálózati tartomány végpontjaihoz.<br /><br /> A szabály által érintett IP-címek alhálózati tartományát az `remoteSubnet` attribútum karakterlánca határozza meg. A szabály leírását az `description` attribútum karakterlánca határozza meg.|
| VégpontAcl | Választható. A hozzáférés-vezérlési szabályok végponthoz rendelését adja meg. A végpontot tartalmazó szerepkör nevét az `role` attribútum karakterlánca határozza meg. A végpont nevét az `endpoint` attribútum karakterlánca határozza meg. A végpontra alkalmazandó `AccessControl` szabálykészlet neve az `accessControl` attribútum karakterláncában van definiálva. Több `EndpointAcl` elem is definiálható.|
| DnsServer | Választható. A DNS-kiszolgáló beállításait adja meg. A virtuális hálózat nélküli DNS-kiszolgálók beállításait megadhatja. A DNS-kiszolgáló nevét az `name` attribútum karakterlánca határozza meg. A DNS-kiszolgáló IP-címét az `IPAddress` attribútum karakterlánca határozza meg. Az IP-címnek érvényes IPv4-címnek kell lennie.|
| VirtualNetworkSite | Választható. Megadja annak a virtuális hálózati helynek a nevét, amelyben a felhőszolgáltatást telepíteni szeretné. Ez a beállítás nem hoz létre virtuális hálózati helyet. Olyan helyre hivatkozik, amelyet korábban a virtuális hálózat hálózati fájljában definiáltak. Egy felhőszolgáltatás csak egy virtuális hálózat tagja lehet. Ha nem adja meg ezt a beállítást, a felhőszolgáltatás nem lesz telepítve a virtuális hálózatra. A virtuális hálózati hely nevét az `name` attribútum karakterlánca határozza meg.|
| InstanceAddress (Példánycíme) | Választható. Egy szerepkör társítását adja meg a virtuális hálózat alhálózatához vagy alhálózataihoz. Amikor egy szerepkörnevet társít egy példánycímhez, megadhatja azokat az alhálózatokat, amelyekhez a szerepkört társítani szeretné. Az `InstanceAddress` alhálózati elemet tartalmaz. Az alhálózathoz vagy alhálózatokhoz társított szerepkör nevét az `roleName` attribútum karakterlánca határozza meg.|
| Alhálózat | Választható. Megadja azt az alhálózatot, amely megfelel a hálózati konfigurációs fájlalhálózati nevének. Az alhálózat nevét az `name` attribútum karakterlánca határozza meg.|
| ReservedIP (fenntartott ip) | Választható. Megadja a központi telepítéshez társítandó fenntartott IP-címet. A fenntartott IP-cím létrehozásához a Fenntartott IP-cím létrehozása kell, hogy legyen. A felhőszolgáltatásban minden egyes központi telepítés egy fenntartott IP-címhez társítható. A fenntartott IP-cím nevét az `name` attribútum karakterlánca határozza meg.|

## <a name="see-also"></a>Lásd még:
[Felhőszolgáltatás (klasszikus) konfigurációs séma](schema-cscfg-file.md)
