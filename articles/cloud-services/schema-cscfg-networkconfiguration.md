---
title: Azure Cloud Services NetworkConfiguration séma | Microsoft Docs
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 28
author: tgore03
ms.author: tagore
ms.openlocfilehash: cacc8b1f2909965594fdf0d841963e792acf648c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75385424"
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Azure Cloud Services konfiguráció NetworkConfiguration sémája

A szolgáltatás konfigurációs fájljának `NetworkConfiguration` eleme a Virtual Network és a DNS-értékeket adja meg. Ezek a beállítások a Cloud Services esetében nem kötelezőek.

A virtuális hálózatokkal és a kapcsolódó sémákkal kapcsolatos további tudnivalókért a következő erőforrással tájékozódhat:

- [Cloud Service (klasszikus) konfigurációs séma](schema-cscfg-file.md)
- [Cloud Service (klasszikus) definíciós séma](schema-csdef-file.md)
- [Virtual Network létrehozása (klasszikus)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="networkconfiguration-element"></a>NetworkConfiguration elem
Az alábbi példa a `NetworkConfiguration` elemet és annak alárendelt elemeit mutatja be.

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

Az alábbi táblázat a `NetworkConfiguration` elem alárendelt elemeit ismerteti.

| Elem       | Leírás |
| ------------- | ----------- |
| AccessControl | Választható. Meghatározza a felhőben lévő végpontokhoz való hozzáférés szabályait. A hozzáférés-vezérlés nevét a `name` attribútum karakterlánca határozza meg. A `AccessControl` elem egy vagy több `Rule` elemet tartalmaz. Egynél több `AccessControl` elem is definiálható.|
| Szabály | Választható. Meghatározza azt a műveletet, amelyet az IP-címek megadott alhálózati tartománya esetében kell végrehajtani. A szabály sorrendjét a `order` attribútumhoz tartozó karakterlánc-érték határozza meg. Minél alacsonyabb a szabály száma, annál magasabb a prioritás. Például a szabályok megadhatók a 100, 200 és 300 sorszámokkal. A 100-as sorszámú szabály elsőbbséget élvez a 200-os sorrendtel rendelkező szabállyal szemben.<br /><br /> A szabályhoz tartozó műveletet a `action` attribútum karakterlánca határozza meg. Lehetséges értékek:<br /><br /> -   `permit` – azt adja meg, hogy csak a megadott alhálózati tartományból származó csomagok kommunikáljanak a végponttal.<br />-   `deny` – azt adja meg, hogy a rendszer megtagadja a hozzáférést a megadott alhálózati tartomány végpontjai számára.<br /><br /> A szabály által érintett IP-címek alhálózatának tartományát a `remoteSubnet` attribútum karakterlánca határozza meg. A szabály leírását a `description` attribútum karakterlánca határozza meg.|
| EndpointAcl | Választható. Meghatározza a hozzáférés-vezérlési szabályok hozzárendelését egy végponthoz. A végpontot tartalmazó szerepkör nevét a `role` attribútum karakterlánca határozza meg. A végpont nevét a `endpoint` attribútum karakterlánca határozza meg. A végpontra alkalmazni kívánt `AccessControl`-szabályok csoportjának neve a `accessControl` attribútum karakterláncában van definiálva. Egynél több `EndpointAcl` elemet is meghatározhat.|
| DNS | Választható. A DNS-kiszolgáló beállításait adja meg. A DNS-kiszolgálók beállításait Virtual Network nélkül is megadhatja. A DNS-kiszolgáló nevét a `name` attribútum karakterlánca határozza meg. A DNS-kiszolgáló IP-címét a `IPAddress` attribútum karakterlánca határozza meg. Az IP-címnek érvényes IPv4-címnek kell lennie.|
| VirtualNetworkSite | Választható. Annak a Virtual Network-helynek a nevét adja meg, amelyben telepíteni szeretné a felhőalapú szolgáltatást. Ez a beállítás nem hoz létre Virtual Network helyet. Egy olyan webhelyre hivatkozik, amely korábban meg van határozva a Virtual Network hálózati fájljában. A felhőalapú szolgáltatás csak egy Virtual Network tagja lehet. Ha nem megadja ezt a beállítást, a rendszer nem telepíti a Cloud Service-t egy Virtual Network. A Virtual Network hely nevét a `name` attribútumhoz tartozó karakterlánc határozza meg.|
| InstanceAddress | Választható. Meghatározza egy szerepkör társítását egy alhálózathoz vagy alhálózatokhoz a Virtual Networkban. Amikor egy szerepkör-nevet társít egy példány címéhez, megadhatja azokat az alhálózatokat, amelyekhez társítani szeretné ezt a szerepkört. A `InstanceAddress` alhálózatok elemet tartalmaz. Az alhálózathoz vagy alhálózatokhoz társított szerepkör nevét a `roleName` attribútum karakterlánca határozza meg.|
| Alhálózat | Választható. Meghatározza azt az alhálózatot, amely megfelel az alhálózat nevének a hálózati konfigurációs fájlban. Az alhálózat nevét a `name` attribútum karakterlánca határozza meg.|
| Foglalt IP | Választható. Meghatározza azt a fenntartott IP-címet, amelyet hozzá kell rendelni a központi telepítéshez. A fenntartott IP-cím létrehozásához a Create Fenntartott IP címet kell használnia. A felhőalapú szolgáltatásokban minden üzembe helyezés egy fenntartott IP-címmel társítható. A fenntartott IP-cím nevét a `name` attribútum karakterlánca határozza meg.|

## <a name="see-also"></a>Lásd még:
[Cloud Service (klasszikus) konfigurációs séma](schema-cscfg-file.md)
