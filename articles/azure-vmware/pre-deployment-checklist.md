---
title: Azure VMware-megoldás előtti üzembe helyezési ellenőrzőlista
description: Ezt a feladatlistát az üzembe helyezés előtti tervezési folyamat részeként használhatja.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 51480f72d6de6b1f49aa753e006c4683bbd48cc7
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91579767"
---
# <a name="azure-vmware-solution-pre-deployment-checklist"></a>Azure VMware-megoldás előtti üzembe helyezési ellenőrzőlista
Ezt az előtelepítési ellenőrzőlistát a [tervezési fázisban](production-ready-deployment-steps.md)fogja használni.

## <a name="success-criteria"></a>Sikerességi feltételek
Határozza meg a futtatni kívánt teszteket és a várt eredményt.

| Alapvető információk szükségesek | Válasz |
| ----------- | ------------- |
| SDDC üzembe helyezése | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Virtuális hálózat létrehozása | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Ugrási mező létrehozása | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Virtuális hálózati átjáró létrehozása | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| ExpressRoute-Global Reach összekötése | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| Bejelentkezés a NSX-kezelőbe és a vCenter | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



| Elsődleges információ szükséges | Válasz |
| --------| --------------|
| DHCP-kiszolgáló létrehozása | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Hálózati szegmensek létrehozása | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Skálázás (csomópontok hozzáadása vagy törlése) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| VMware HCX üzembe helyezése | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Virtuális gépek (VM-EK) létrehozása | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
| Internet engedélyezése | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| VIRTUÁLIS gépek migrálása a helyszínről a saját felhőbe | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



| További információk szükségesek | Válasz |
| --------| --------------|
| VM-Pillanatképek műveletei | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| A NSX-T Load Balancer üzembe helyezése | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Azure-integráció<br><ul><li>Megosztott tartalom könyvtára</li><li>Biztonsági integráció</li><li>ISO feltöltése</li><li>Létrehozás ISO-ből</li><li>Azure Backup</li></ul> | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Mikro-szegmentálás | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Útválasztás | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |




## <a name="azure-vmware-solution-information"></a>Azure VMware-megoldás adatai

#### <a name="azure-subscription"></a>Azure-előfizetés
Adja meg az előfizetés nevét és az előfizetés-azonosítót az Azure VMware-megoldáshoz. Az előfizetés lehet új vagy meglévő előfizetés. Ne használjon fejlesztési/tesztelési előfizetést.

| Szükséges információk  | Válasz |
| ------------| ------------- |
| Előfizetés és azonosító | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Előfizetés nagyvállalati szerződéssel | ☐ Igen &nbsp; &nbsp; ☐  |
| Erőforráscsoport neve | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Hely | ☒ USA keleti régiója |
| Azure-rendszergazda<br><br>Adja meg a rendszergazda nevét és elérhetőségét<br>a szolgáltatás a piactéren való engedélyezéséhez van hozzárendelve.<br>A közreműködő a minimálisan szükséges szerepkör <br>[regisztrálja az Azure VMware megoldás erőforrás-szolgáltatóját](tutorial-create-private-cloud.md#register-the-resource-provider). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="on-premises-vmware-information"></a>Helyszíni VMware-információk

| Szükséges információk  | Válasz |
| ------------| --------------|
| vSphere verziója | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| vCenter verziója | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| vCenter-rendszergazda | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| L2-bővítmény<br><br>Ha az L2-hálózatok kiterjesztése VMware HCX,<br>adja meg a kiterjeszteni kívánt helyszíni hálózatot. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| vSwitch<br><br>Adja meg az egész környezetben használt vSwitch típusát. | ☐ Standard &nbsp; &nbsp; ☐ Distributed<br><br>Standard használata esetén a VMware HCX nem érhető el. |
| DNS és DHCP<br><br>Megfelelő DNS-és DHCP-infrastruktúrára van szükség. <br>A beépített DHCP-szolgáltatás használata javasolt <br>Helyi DHCP-kiszolgáló NSX vagy használata a privát felhőben <br>a DHCP-forgalom továbbítása helyett a <br>WAN vissza a helyszíni környezetbe. További információk: <br>Tekintse [meg, hogyan hozhat létre és kezelhet DHCP-t az Azure VMware megoldásban](manage-dhcp.md). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |


 

## <a name="networking---azure-vmware-solution-infrastructure"></a>Hálózatkezelés – Azure VMware-megoldási infrastruktúra 
A szükséges adatigény az Azure VMware megoldás hálózatkezelési igényeinek kielégítése az üzembe helyezés és a kezdeti hálózati tesztelés érdekében. 

| Szükséges információk | Válasz |
| ----------- | ------------- |
|Azure VMware-megoldás CIDR<br><br>A vSphere gazdagépek, a vSAN és a felügyelet számára szükséges <br>hálózatok az Azure VMware megoldásban. További információ <br>információ: [Útválasztás és alhálózat szempontjai](tutorial-network-checklist.md#routing-and-subnet-considerations). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Azure VMware-megoldás munkaterhelés-CIDR (nem kötelező)<br><br>Az Azure VMware-ben használni kívánt hálózat kiosztása<br>Megoldás a kezdeti teszteléshez. Virtual machines (Virtuális gépek)<br>a hálózati kapcsolat ellenőrzéséhez lesz telepítve <br>Azure VMware-megoldásról/-ről. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="networking---connect-azure-vmware-solution-to-azure-virtual-network"></a>Hálózatkezelés – Azure VMware-megoldás összekötése az Azure Virtual Network
Az Azure VMware Solution-fürthöz való csatlakozást követően szükséges adatmennyiség az Azure-ban az Azure VMware megoldás szolgáltatás részét képező ExpressRoute áramkörön keresztül kapcsolódhat az Azure-hoz.

| Szükséges információk | Válasz |
| ------------------ | ------------- |
| Jump Box – Windows Server | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Virtuális hálózat létrehozása | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Átjáró-alhálózat létrehozása | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Virtuális hálózati átjáró létrehozása<br><br>További információ: [create The Virtual Network Gateway (virtuális hálózati átjáró létrehozása](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway)). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |


 

## <a name="networking---connect-azure-vmware-solution-to-an-on-premises-datacenter"></a>Hálózatkezelés – Azure VMware-megoldás összekapcsolása helyszíni adatközponttal

| Szükséges információk | Válasz |
| ------------------ | ------------- |
| ExpressRoute peering CIDR<br><br>A `/29` CIDR. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| ExpressRoute engedélyezési kulcs és erőforrás-azonosító<br><br>Adja meg az engedélyezési kulcsot és az erőforrás-azonosítót, <br>az aktuális ExpressRoute létrehozva <br>a helyszíni adatközponthoz csatlakozó áramkör.  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Az alapértelmezett útvonal iránya<br><br>A virtuális gépeket az Azure VMware megoldásban kell <br>az Internet elérése az Azure VMware-megoldáson keresztül <br>az interneten, vagy térjen vissza a ExpressRoute áramkörre <br>a helyszínen az alapértelmezett útvonalat? | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| A szolgáltatással való kommunikációhoz szükséges hálózati portok<br><br>További információ: [szükséges hálózati portok](tutorial-network-checklist.md#required-network-ports). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="networking---vmware-hcx"></a>Hálózatkezelés – VMware HCX

| Szükséges információk | Válasz |
| ------------------ | ------------- |
| Hálózati portok<br><br>Ha van tűzfal, győződjön meg arról, hogy a szükséges hálózati portok <br>a helyszíni és az Azure VMware megoldás között nyílik meg. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| DNS<br><br>További információ a DNS konfigurálásáról: <br>Lásd: [hálózatkezelés – Azure VMware megoldás-infrastruktúra](#networking---azure-vmware-solution-infrastructure). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| VMware HCX CIDRs<br><br>Két `/29` CIDR-blokkra van szükség, amelyek a következőhöz használatosak: <br>a VMware HCX infrastruktúra összetevői a helyszínen.  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |

