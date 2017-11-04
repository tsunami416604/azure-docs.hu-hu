---
title: "Útmutató a virtuális gépek replikálása Azure Azure hálózat az Azure Site Recovery |} Microsoft Docs"
description: "Útmutató az Azure virtuális gépek replikálása a hálózat"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/31/2017
ms.author: sujayt
ms.openlocfilehash: 99c08a1efbc610959fb4ba824dcb0601efac5877
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="networking-guidance-for-replicating-azure-virtual-machines"></a>Útmutató az Azure virtuális gépek replikálása a hálózat

>[!NOTE]
> Az Azure virtuális gépek helyreállítási helyreplikálásának jelenleg előzetes verzió.

Ez a cikk részletezi az hálózati útmutatót az Azure Site Recovery, amikor Ön replikálódik, és az Azure virtuális gépek helyreállítani egy régió tartozik egy másik régióban. Az Azure Site Recovery követelményeiről kapcsolatban bővebben lásd: a [Előfeltételek](site-recovery-prereq.md) cikk.

## <a name="site-recovery-architecture"></a>Site Recovery architektúrájáról

A Site Recovery használatával egy egyszerű és egyszerűen futó alkalmazásokhoz az Azure virtuális gépek Azure-régió, egy másik, hogy a helyre, ha az elsődleges régióban a szüneteltetése replikálásához. További információ [Ez a forgatókönyv és a Site Recovery architektúrájáról](site-recovery-azure-to-azure-architecture.md).

## <a name="your-network-infrastructure"></a>A hálózati infrastruktúra

A következő ábra szemlélteti a tipikus Azure környezetben egy Azure virtuális gépeken futó alkalmazáshoz:

![ügyfél-környezet](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Ha az Azure expressroute-on vagy egy helyszíni hálózatból Azure VPN-kapcsolatot használ, a környezet néz ki:

![ügyfél-környezet](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Általában az ügyfelek védelme a tűzfalak és/vagy a hálózati biztonsági csoportokkal (NSG-k) használó hálózatokon. A tűzfalak használható URL-cím vagy IP-alapú engedélyezett hálózati kapcsolat ellenőrzése. Az NSG-k engedélyezési szabály IP-címtartományok segítségével vezérelheti, hálózati kapcsolat.

>[!IMPORTANT]
> Ha egy hitelesített proxykiszolgálót segítségével szabályozhatja a hálózati kapcsolatot, nem támogatott, és a Site Recovery nem engedélyezhető.

Az alábbi szakaszok ismertetik a hálózati kimenő kapcsolat változtatásokat, hogy a virtuális gépek az Azure Site Recovery replikációjának működéséhez szükséges.

## <a name="outbound-connectivity-for-azure-site-recovery-urls"></a>Kimenő kapcsolódás az Azure Site Recovery URL-címek

Ha bármilyen URL-alapú tűzfal proxy segítségével szabályozhatja a kimenő kapcsolat, mindenképp érdemes engedélyezett Azure Site Recovery szolgáltatás URL-címeket az alábbi szükséges:


**URL-CÍME** | **Cél**  
--- | ---
*.blob.core.windows.net | Szükséges, hogy az is lehet adatokat írni a forrás régióban gyorsítótár tárfiók a virtuális gépről.
login.microsoftonline.com | A Site Recovery szolgáltatás URL-címek a hitelesítési és engedélyezési szükséges.
*.hypervrecoverymanager.windowsazure.com | Szükséges, hogy a Site Recovery szolgáltatás kommunikációja akkor fordulhat elő, a virtuális gépről.
*. servicebus.windows.net | Szükséges, hogy a Site Recovery figyelése és diagnosztikai adatokat a virtuális gépről is beírhatók.

## <a name="outbound-connectivity-for-azure-site-recovery-ip-ranges"></a>Kimenő kapcsolódás az Azure Site Recovery IP-címtartományok

>[!NOTE]
> Automatikusan létrehozni a szükséges NSG-szabályok a hálózati biztonsági csoport, akkor [töltse le és használja ezt a parancsfájlt](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702).

>[!IMPORTANT]
> * Azt javasoljuk, hogy a szükséges NSG-szabályok létrehozása a tesztelési hálózati biztonsági csoport, és ellenőrizze, hogy nincs probléma a szabályok a termelési hálózati biztonsági csoport létrehozása előtt.
> * A szükséges számú NSG-szabályok létrehozása, győződjön meg arról, hogy az előfizetés szerepel az engedélyezési listán. Az előfizetés az NSG-szabály korlát növeléséhez forduljon a támogatási szolgálathoz.

Ha bármely IP-alapú tűzfal proxy vagy az NSG-szabályok segítségével szabályozhatja a kimenő kapcsolat végzi, a következő IP-címtartományok kell szerepel az engedélyezési listán, attól függően, hogy a virtuális gépek a forrás és cél helyét:

- Az összes IP-címtartományok felel meg a forráshelyét. (Letöltheti a [IP-címtartományok](https://www.microsoft.com/download/confirmation.aspx?id=41653).) Engedélyezett szükség, így az is lehet adatokat írni a gyorsítótár tárfiók a virtuális gépről.

- Az összes IP-címtartományokat, amelyek megfelelnek az Office 365 [hitelesítés és identitás IP V4 végpontok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).

    >[!NOTE]
    > Ha új IP-címek a jövőben Office 365 IP-címtartományok hozzáadják, akkor kell új NSG-szabályok létrehozása.

- Hely helyreállítási szolgáltatás végpontjának IP-címek ([rendelkezésre álló XML-fájlban](https://aka.ms/site-recovery-public-ips)), a célhely függ:

   **Célhely** | **Site Recovery szolgáltatás IP-címek** |  **A Site Recovery IP figyelése**
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

## <a name="sample-nsg-configuration"></a>Mintakonfiguráció NSG
Ez a szakasz ismerteti az NSG-szabályok konfigurálása, hogy a Site Recovery replikációs virtuális gépen használható. NSG-szabályok segítségével szabályozhatja a kimenő kapcsolat, ha a "Kimenő HTTPS engedélyezése" szabályok használata az összes szükséges IP-címtartományok.

>[!Note]
> Automatikusan létrehozni a szükséges NSG-szabályok a hálózati biztonsági csoport, akkor [töltse le és használja ezt a parancsfájlt](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702).

Például ha a virtuális gép forrás elérési útja "Amerikai keleti" és a replikációs célhelye "Középső Régiójában", kövesse a következő két szakasz utasításait.

>[!IMPORTANT]
> * Azt javasoljuk, hogy a szükséges NSG-szabályok létrehozása a tesztelési hálózati biztonsági csoport, és ellenőrizze, hogy nincs probléma a szabályok a termelési hálózati biztonsági csoport létrehozása előtt.
> * A szükséges számú NSG-szabályok létrehozása, győződjön meg arról, hogy az előfizetés szerepel az engedélyezési listán. Az előfizetés az NSG-szabály korlát növeléséhez forduljon a támogatási szolgálathoz.

### <a name="nsg-rules-on-the-east-us-network-security-group"></a>USA keleti régiója hálózati biztonsági csoport az NSG-szabályok

* Létrehozhat szabályokat, amelyek megfelelnek [keleti USA IP-címtartományok](https://www.microsoft.com/download/confirmation.aspx?id=41653). Erre azért szükség, így az is lehet adatokat írni a gyorsítótár tárfiók a virtuális gépről.

* Létrehozhat szabályokat, amelyek megfelelnek az Office 365 összes IP-címtartományokhoz [hitelesítés és identitás IP V4 végpontok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).

* Hozzon létre szabályokat, amelyek megfelelnek a célhelyre:

   **Hely** | **Site Recovery szolgáltatás IP-címek** |  **A Site Recovery IP figyelése**
    --- | --- | ---
   USA középső régiója | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules-on-the-central-us-network-security-group"></a>USA középső RÉGIÓJA hálózati biztonsági csoport az NSG-szabályok

Ezek a szabályok szükség, hogy a replikációs cél régióban engedélyezhető a forrás régió feladatátvételt követően a:

* Szabályok, amelyek megfelelnek [központi USA IP-címtartományok](https://www.microsoft.com/download/confirmation.aspx?id=41653). Ezek szükségesek, így az is lehet adatokat írni a gyorsítótár tárfiók a virtuális gépről.

* Az összes IP-címtartományokat, amelyek megfelelnek az Office 365 szolgáltatásra vonatkozó szabályok [hitelesítés és identitás IP V4 végpontok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).

* Olyan szabályokat, hogy a forrás helye:

   **Hely** | **Site Recovery szolgáltatás IP-címek** |  **A Site Recovery IP figyelése**
    --- | --- | ---
   USA keleti régiója | 13.82.88.226 | 104.45.147.24


## <a name="guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration"></a>Meglévő Azure--helyszíni ExpressRoute és a VPN-konfiguráció vonatkozó irányelvek

Ha egy ExpressRoute- vagy VPN-kapcsolatot a helyszíni és a forráshely az Azure-ban, kövesse az ebben a szakaszban az útmutatást.

### <a name="forced-tunneling-configuration"></a>A kényszerített bújtatás konfigurálása

Egy közös felhasználói konfigurálása egy alapértelmezett útvonalat (0.0.0.0/0), amely arra kényszeríti a kimenő internetforgalom keresztül a helyszíni hely. Ez nem ajánlott meg. A replikációs forgalom és a Site Recovery szolgáltatás kommunikációja nem hagyja az Azure-határ. A megoldás, hogy vegye fel a felhasználó által definiált útvonalak (udr-EK) a [ezek IP-címtartományok](#outbound-connectivity-for-azure-site-recovery-ip-ranges) , hogy a replikációs forgalmat a helyszíni nem használható.

### <a name="connectivity-between-the-target-and-on-premises-location"></a>A cél- és a helyszíni hely közötti kapcsolat

Kövesse a kapcsolatok a cél helyét, és a helyszíni hely közötti:
- Ha az alkalmazásnak a helyszíni gépeket csatlakozni, vagy ha nincsenek a csatlakozó ügyfelek az alkalmazás a helyszíni keresztül VPN/ExpressRoute, győződjön meg arról, hogy legalább egy [pont-pont kapcsolat](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) a cél Azure-régió, és a helyszíni adatközpont között.

- Ha várhatóan sok forgalmat a cél Azure-régió, és a helyszíni adatközpont között, akkor hozzon létre egy másik [ExpressRoute-kapcsolat](../expressroute/expressroute-introduction.md) a cél Azure-régió, és a helyszíni adatközpont között.

- Ha meg szeretné őrizni az IP-címek a virtuális gépekhez, azok a feladatátvételt követően, ne a cél régió hely-hely vagy ExpressRoute-kapcsolat megszakad. Győződjön meg arról, hogy a forrás régió IP-címtartományok és a cél régió IP-címtartományai között nem tartomány csomópontjánál azt.

### <a name="best-practices-for-expressroute-configuration"></a>Ajánlott eljárások az ExpressRoute-konfiguráció
Kövesse az alábbi gyakorlati tanácsok az ExpressRoute-konfiguráció:

- ExpressRoute-kapcsolatcsoportot létrehozni a forrás- és a cél régiókban kell. Majd szüksége közötti kapcsolat létrehozásához:
  - A forrás virtuális hálózat és az ExpressRoute-kapcsolatcsoportot.
  - A cél virtuális hálózat és az ExpressRoute-kapcsolatcsoportot.

- ExpressRoute standard részeként geopolitikai ugyanabban a régióban áramkör hozhat létre. Hozzon létre ExpressRoute-Kapcsolatcsoportok geopolitikai különböző régiókban, prémium szintű Azure ExpressRoute szükség, amely magában foglalja egy növekményes költséget. (Ha már használ ExpressRoute prémium, hogy ingyenesen.) További részletekért lásd: a [helyek ExpressRoute a dokumentum](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) és [ExpressRoute árképzési](https://azure.microsoft.com/pricing/details/expressroute/).

- Azt javasoljuk, hogy a forrás és cél régióban különböző IP-címtartományok használja. Az ExpressRoute-kapcsolatcsoport nem lehet kapcsolódni a két Azure virtuális hálózat az ugyanazon az IP-címtartományok egy időben.

- Hozzon létre virtuális hálózatok a azonos IP-címtartományok mindkét régiókban, és majd hozza létre az ExpressRoute-Kapcsolatcsoportok mindkét régióban. A feladatátadási esemény válassza le a kapcsolatcsoport a forrás virtuális hálózatról, és csatlakozzon a kapcsolatcsoport a cél virtuális hálózat.

 >[!IMPORTANT]
 > Ha az elsődleges régióban teljesen le, a leválasztás sikertelen lehet. Amely megakadályozza, hogy a cél virtuális hálózat első ExpressRoute-kapcsolatot.

## <a name="next-steps"></a>Következő lépések
A munkaterhelések számára a védelmének megkezdéséhez [Azure virtuális gépek replikálásához](site-recovery-azure-to-azure.md).
