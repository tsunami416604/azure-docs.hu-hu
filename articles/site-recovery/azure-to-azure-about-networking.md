---
title: Az Azure Site Recovery segítségével Azure az Azure-bA vész-helyreállítási alkalmazás hálózatkezelési funkcióiról |} Microsoft Docs
description: Azure Site Recovery segítségével Azure virtuális gépek replikálását hálózatkezelés áttekintése.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/17/2018
ms.author: sujayt
ms.openlocfilehash: f318f98479caed8efb4a3705939cb9ac0dd5b237
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="about-networking-in-azure-to-azure-replication"></a>Az Azure az Azure-bA replikációs alkalmazás hálózatkezelési funkcióiról

>[!NOTE]
> Az Azure virtuális gépek helyreállítási helyreplikálásának jelenleg előzetes verzió.

Ez a cikk hálózati útmutatást biztosít, amikor Ön replikálásához és helyreállítása Azure virtuális gépek egy régióban egy másik használatával [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Előkészületek

Ismerje meg, hogy a Site Recovery biztosítja a vész-helyreállítási [ebben a forgatókönyvben](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Hálózati infrastruktúra

A következő ábra szemlélteti, általában az Azure környezetben, Azure virtuális gépeken futó alkalmazások:

![ügyfél-környezet](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Használata Azure expressroute-on vagy VPN-kapcsolat a helyszíni hálózatból az Azure-ba, a környezet néz ki:

![ügyfél-környezet](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Általában hálózatok védelmét tűzfalak és a hálózati biztonsági csoportokkal (NSG-k) használatával. Tűzfalak URL-cím vagy IP-alapú engedélyezett segítségével szabályozhatja a hálózati kapcsolatot. Az NSG-k adja meg a szabályokat, amelyek az IP-címtartományok segítségével szabályozhatja a hálózati kapcsolatot.

>[!IMPORTANT]
> A vezérlő hálózati kapcsolatot egy hitelesített proxykiszolgálót segítségével a Site Recovery által nem támogatott, és nem engedélyezhető.


## <a name="outbound-connectivity-for-urls"></a>Kimenő kapcsolat URL-címek esetén

Egy URL-alapú tűzfal proxy segítségével szabályozhatja a kimenő kapcsolat, ha engedélyezi a Site Recovery URL:


**URL-cím** | **Részletek**  
--- | ---
*.blob.core.windows.net | Szükséges, hogy az is lehet adatokat írni a forrás régióban gyorsítótár tárfiók a virtuális gépről.
login.microsoftonline.com | A Site Recovery szolgáltatás URL-címek a hitelesítési és engedélyezési szükséges.
*.hypervrecoverymanager.windowsazure.com | Szükséges, hogy a Site Recovery szolgáltatás kommunikációja akkor fordulhat elő, a virtuális gépről.
*.servicebus.windows.net | Szükséges, hogy a Site Recovery figyelése és diagnosztikai adatokat a virtuális gépről is beírhatók.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Kimenő kapcsolat az IP-címtartományokhoz

Az IP-alapú tűzfal proxy vagy az NSG-szabályok kimenő kapcsolat ellenőrzésére használnak, ha ezek IP-címtartományok engedélyezni kell.

- Összes IP-címtartományokat, amelyek megfelelnek a storage-fiókok forrás régióban
    - Kell létrehoznia egy [tárolási szolgáltatás címke](../virtual-network/security-overview.md#service-tags) NSG-szabály a forrás régió alapján.
    - Szeretné engedélyezni a ezeknél a címeknél, így az is lehet adatokat írni a gyorsítótár tárfiók a virtuális gépről.
- Az összes IP-címtartományokat, amelyek megfelelnek az Office 365 [hitelesítés és identitás IP V4 végpontok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).
    - Ha új cím ad hozzá az Office 365-tartomány a jövőben, akkor kell új NSG-szabályok létrehozásához.
- Webhely-helyreállítási szolgáltatási végpont IP-címek. Ezek érhetők el egy [XML-fájl](https://aka.ms/site-recovery-public-ips) és a célhely függ.
-  Is [töltse le és használja ezt a parancsfájlt](https://aka.ms/nsg-rule-script), az NSG-t a szükséges szabályok automatikus létrehozásához.
- Azt javasoljuk, hogy a szükséges NSG-szabályok létrehozása egy NSG-teszt, és ellenőrizze, hogy nincs probléma a termelési NSG-t a szabályok létrehozása előtt.


Site Recovery IP-címtartományok a következők:

   **Target** | **A helyreállítási hely IP** |  **A Site Recovery IP figyelése**
   --- | --- | ---
   Kelet-Ázsia | 52.175.17.132 | 13.94.47.61
   Délkelet-Ázsia | 52.187.58.193 | 13.76.179.223
   Közép-India | 52.172.187.37 | 104.211.98.185
   Dél-India | 52.172.46.220 | 104.211.224.190
   USA északi középső régiója | 23.96.195.247 | 168.62.249.226
   Észak-Európa | 40.69.212.238 | 52.169.18.8
   Nyugat-Európa | 52.166.13.64 | 40.68.93.145
   USA keleti régiója | 13.82.88.226 | 104.45.147.24
   USA nyugati régiója | 40.83.179.48 | 104.40.26.199
   USA déli középső régiója | 13.84.148.14 | 104.210.146.250
   USA középső régiója | 40.69.144.231 | 52.165.34.144
   USA 2. keleti régiója | 52.184.158.163 | 40.79.44.59
   Kelet-Japán | 52.185.150.140 | 138.91.1.105
   Nyugat-Japán | 52.175.146.69 | 138.91.17.38
   Dél-Brazília | 191.234.185.172 | 23.97.97.36
   Kelet-Ausztrália | 104.210.113.114 | 191.239.64.144
   Délkelet-Ausztrália | 13.70.159.158 | 191.239.160.45
   Közép-Kanada | 52.228.36.192 | 40.85.226.62
   Kelet-Kanada | 52.229.125.98 | 40.86.225.142
   USA nyugati középső régiója | 52.161.20.168 | 13.78.149.209
   USA nyugati régiója, 2. | 52.183.45.166 | 13.66.228.204
   Az Egyesült Királyság nyugati régiója | 51.141.3.203 | 51.141.14.113
   Az Egyesült Királyság déli régiója | 51.140.43.158 | 51.140.189.52
   Egyesült Királyság 2. déli régiója | 13.87.37.4| 13.87.34.139
   Egyesült Királyság északi régiója | 51.142.209.167 | 13.87.102.68
   Korea középső régiója | 52.231.28.253 | 52.231.32.85
   Korea déli régiója | 52.231.298.185 | 52.231.200.144




## <a name="example-nsg-configuration"></a>Példa NSG-konfiguráció

Ez a példa bemutatja a virtuális gépek replikálása az NSG-szabályok konfigurálása.

- NSG-szabályok segítségével szabályozhatja a kimenő kapcsolat, ha használja a "Kimenő HTTPS engedélyezése" szabályokat: 443-as port minden a szükséges IP-címtartományokhoz.
- A példa feltételezi, hogy a virtuális gép helyét "Amerikai keleti" és a cél elérési útja "Középső Régiójában".

### <a name="nsg-rules---east-us"></a>NSG-szabályok - USA keleti régiója

1. HTTPS (443) kimenő biztonsági szabály létrehozása "Storage.EastUS" az NSG-t az alábbi képernyőfelvételen látható módon.

      ![tároló-tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. Az összes IP-címtartományokat, amelyek megfelelnek az Office 365 kimenő HTTPS (443) szabályok létrehozása [hitelesítés és identitás IP V4 végpontok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).
3. HTTPS (443) kimenő szabályok létrehozása a hely helyreállítási IP-címek, amelyek megfelelnek a célhelyre:

   **Hely** | **Webhely-helyreállítási IP-cím** |  **Helyreállítási figyelő IP-címe**
    --- | --- | ---
   USA középső régiója | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>NSG-szabályok - USA középső RÉGIÓJA

Ezek a szabályok szükség, hogy a replikációs cél régióban engedélyezhető a forrás régió feladatátvételt követően a:

1. HTTPS (443) kimenő biztonsági szabály létrehozása az NSG "Storage.CentralUS".

2. Az összes IP-címtartományokat, amelyek megfelelnek az Office 365 kimenő HTTPS (443) szabályok létrehozása [hitelesítés és identitás IP V4 végpontok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).

3. Kimenő HTTPS (443) a szabályok létrehozása a hely helyreállítási IP-címek, amelyek megfelelnek a forrás helye:

   **Hely** | **Webhely-helyreállítási IP-cím** |  **Helyreállítási figyelő IP-címe**
    --- | --- | ---
   USA középső régiója | 13.82.88.226 | 104.45.147.24

## <a name="network-virtual-appliance-configuration"></a>Virtuális készülék hálózatkonfigurálás

Ha a virtuális gépek kimenő hálózati forgalmat a hálózati virtuális készülékek (NVAs) segítségével végzi, a készülék előfordulhat, hogy beolvasása szabályozva, ha a replikációs forgalom átmegy az NVA. Azt javasoljuk, hogy a hálózati szolgáltatás-végpont létrehozása a virtuális hálózat "Tárolás", hogy a replikációs forgalom nem halad az NVA számára.

### <a name="create-network-service-endpoint-for-storage"></a>Hálózati szolgáltatás-végpont létrehozása a tároláshoz
Létrehozhat egy hálózati végpontot a virtuális hálózat "Tárolás", hogy a replikálás forgalma nem hagynak Azure határ.

- Válassza ki az Azure virtuális hálózatot, majd kattintson a "Szolgáltatásvégpontok"

    ![Storage-végponthoz](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Kattintson a "Hozzáadása" és "Szolgáltatásvégpontok hozzáadása" lap megnyitása
- Válassza ki a "Microsoft.Storage" a "Service" és a szükséges alhálózatok "Alhálózatok" mező alatt, és kattintson a "Hozzáadás"

>[!NOTE]
>A storage-fiókokra, használja az automatikus rendszer-Helyreállítás nem korlátozhatja a virtuális hálózati hozzáférést. Engedélyezze a hozzáférést minden hálózatról

## <a name="expressroutevpn"></a>ExpressRoute/VPN

Ha egy ExpressRoute- vagy VPN-kapcsolat a helyszíni és az Azure-beli hely között, kövesse az ebben a szakaszban irányelveket.

### <a name="forced-tunneling"></a>Alagúthasználat kényszerítése

Általában, adja meg az alapértelmezett útvonalat (0.0.0.0/0), amely arra kényszeríti a helyszíni hely áramlása érdekében kimenő internetforgalom vagy. Ez nem ajánlott meg. A replikálás forgalma nem hagyja meg az Azure-határ.

Is [hozzon létre egy hálózati végpontot](#create-network-service-endpoint-for-storage) a virtuális hálózatot "Tároló" úgy, hogy a replikálás forgalma nem hagynak Azure határ.


### <a name="connectivity"></a>Kapcsolatok

Kövesse a kapcsolatok a cél helyét, és a helyszíni hely közötti:
- Ha az alkalmazásnak a helyszíni gépeket csatlakozni, vagy ha nincsenek a csatlakozó ügyfelek az alkalmazás a helyszíni keresztül VPN/ExpressRoute, győződjön meg arról, hogy legalább egy [pont-pont kapcsolat](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) a cél Azure-régió, és a helyszíni adatközpont között.

- Ha várhatóan sok forgalmat a cél Azure-régió, és a helyszíni adatközpont között, akkor hozzon létre egy másik [ExpressRoute-kapcsolat](../expressroute/expressroute-introduction.md) a cél Azure-régió, és a helyszíni adatközpont között.

- Ha meg szeretné őrizni az IP-címek a virtuális gépekhez, azok a feladatátvételt követően, ne a cél régió hely-hely vagy ExpressRoute-kapcsolat megszakad. Győződjön meg arról, hogy a forrás régió IP-címtartományok és a cél régió IP-címtartományai között nem tartomány csomópontjánál azt.

### <a name="expressroute-configuration"></a>Az ExpressRoute-konfiguráció
Kövesse az alábbi gyakorlati tanácsok az ExpressRoute-konfiguráció:

- A forrás- és a cél régiókban ExpressRoute-kapcsolatcsoportot létrehozni. Majd szüksége közötti kapcsolat létrehozásához:
    - A forrás virtuális hálózat és a helyszíni hálózatát, a forrás régióban ExpressRoute-kapcsolatcsoportot.
    - A cél virtuális hálózat és a helyszíni hálózatát, a cél régióban ExpressRoute-kapcsolatcsoportot.


- ExpressRoute standard részeként geopolitikai ugyanabban a régióban áramkör hozhat létre. Hozzon létre ExpressRoute-Kapcsolatcsoportok geopolitikai különböző régiókban, prémium szintű Azure ExpressRoute szükség, amely magában foglalja egy növekményes költséget. (Ha már használ ExpressRoute prémium, hogy ingyenesen.) További részletekért lásd: a [helyek ExpressRoute a dokumentum](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) és [ExpressRoute árképzési](https://azure.microsoft.com/pricing/details/expressroute/).

- Azt javasoljuk, hogy a forrás és cél régióban különböző IP-címtartományok használja. Az ExpressRoute-kapcsolatcsoport nem lehet kapcsolódni a két Azure virtuális hálózat az ugyanazon az IP-címtartományok egy időben.

- Hozzon létre virtuális hálózatok a azonos IP-címtartományok mindkét régiókban, és majd hozza létre az ExpressRoute-Kapcsolatcsoportok mindkét régióban. A feladatátadási esemény válassza le a kapcsolatcsoport a forrás virtuális hálózatról, és csatlakozzon a kapcsolatcsoport a cél virtuális hálózat.

 >[!IMPORTANT]
 > Ha az elsődleges régióban teljesen le, a leválasztás sikertelen lehet. Amely megakadályozza, hogy a cél virtuális hálózat első ExpressRoute-kapcsolatot.

## <a name="next-steps"></a>További lépések
A munkaterhelések számára a védelmének megkezdéséhez [Azure virtuális gépek replikálásához](site-recovery-azure-to-azure.md).
