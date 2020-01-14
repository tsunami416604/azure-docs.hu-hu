---
title: Az Azure-hoz való kapcsolódás hibáinak elhárítása az Azure-ban Azure Site Recovery
description: Hibák és problémák elhárítása az Azure-beli virtuális gépek vész-helyreállításhoz való replikálásakor
services: site-recovery
author: carmonmills
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/05/2019
ms.author: carmonm
ms.openlocfilehash: 41b5203c328243c9ef7cc74e6b9771c677a54c7c
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933404"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Az Azure – Azure virtuálisgép-hálózat csatlakozási problémáinak elhárítása

Ez a cikk a hálózati kapcsolattal kapcsolatos gyakori problémákat ismerteti, amikor az Azure-beli virtuális gépeket egy régióból egy másik régióba replikálja és helyreállítja. A hálózati követelményekkel kapcsolatos további információkért tekintse [meg az Azure-beli virtuális gépek replikálásának kapcsolódási követelményeit](azure-to-azure-about-networking.md)ismertető témakört.

Ahhoz, hogy Site Recovery replikáció működjön, az adott URL-címekhez vagy IP-tartományokhoz kimenő kapcsolat szükséges a virtuális gépről. Ha a virtuális gép tűzfal mögött van, vagy hálózati biztonsági csoport (NSG) szabályok használatával vezérli a kimenő kapcsolatot, akkor előfordulhat, hogy ezek egyike a probléma.

**URL-cím** | **Részletek**  
--- | ---
*.blob.core.windows.net | Kötelező megadni, hogy az adatok a virtuális gépről származó forrás régióban lévő cache Storage-fiókba írhatók legyenek. Ha ismeri a virtuális gépekhez tartozó összes gyorsítótárbeli Storage-fiókot, engedélyezheti a-a megadott Storage-fiók URL-címeinek (például cache1.blob.core.windows.net és cache2.blob.core.windows.net) listázását *. blob.core.windows.net
login.microsoftonline.com | Az engedélyezéshez és a hitelesítéshez szükséges a Site Recovery szolgáltatás URL-címeihez.
*.hypervrecoverymanager.windowsazure.com | Szükséges, hogy a Site Recovery szolgáltatás kommunikációja a virtuális gépről is megtörténjen. Ha a tűzfal proxyja támogatja az IP-címeket, használhatja a megfelelő "Site Recovery IP-címet".
*.servicebus.windows.net | Szükséges, hogy a Site Recovery monitorozási és diagnosztikai adatok a virtuális gépről is írhatók legyenek. Ha a tűzfal proxyja támogatja az IP-címeket, használhatja a megfelelő "Site Recovery figyelési IP-címet".

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Kimenő kapcsolat Site Recovery URL-címekhez vagy IP-tartományokhoz (hibakód: 151037 vagy 151072)

## <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>1. probléma: nem sikerült regisztrálni az Azure-beli virtuális gépet Site Recovery (151195) </br>
- **Lehetséges ok** </br>
  - A DNS-feloldási hiba miatt nem lehet kapcsolódni Site Recovery végpontokhoz.
  - Ez gyakrabban fordul elő ismételt védelem esetén, amikor feladatátvételt végzett a virtuális gépen, de a DNS-kiszolgáló nem érhető el a vészhelyreállítási régióból.

- **Felbontás**
   - Ha egyéni DNS-t használ, győződjön meg arról, hogy a DNS-kiszolgáló elérhető a vész-helyreállítási régióból. Ellenőrizze, hogy van-e egyéni DNS a virtuális géphez > vész-helyreállítási hálózati > DNS-kiszolgálókon. Próbálja meg elérni a DNS-kiszolgálót a virtuális gépről. Ha nem érhető el, tegye elérhetővé a DNS-kiszolgáló meghibásodása vagy a hely létrehozása a DR hálózat és a DNS között.

    ![com – hiba](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


## <a name="issue-2-site-recovery-configuration-failed-151196"></a>2\. probléma: Site Recovery konfiguráció nem sikerült (151196)

> [!NOTE]
> Ha a virtuális gépek a **standard** belső terheléselosztó mögött vannak, akkor alapértelmezés szerint nem férnek hozzá a O365 IP-címekhez (azaz login.microsoftonline.com). Módosítsa az **alapszintű** belső terheléselosztó típusára, vagy hozzon létre kimenő hozzáférést a [cikkben](https://aka.ms/lboutboundrulescli)említettek szerint.

- **Lehetséges ok** </br>
  - Nem lehet kapcsolódni az Office 365-hitelesítéshez és az Identity IP4-végpontokhoz.

- **Felbontás**
  - Azure Site Recovery szükséges hozzáférést az Office 365 IP-címek tartományához a hitelesítéshez.
    Ha az Azure hálózati biztonsági csoport (NSG) szabályai/tűzfal proxyja segítségével vezérli a kimenő hálózati kapcsolatot a virtuális gépen, győződjön meg arról, hogy engedélyezi a kommunikációt a O365 tartományok. Hozzon létre egy [Azure Active Directory (Azure ad) Service tag](../virtual-network/security-overview.md#service-tags) -alapú NSG-szabályt, amely lehetővé teszi az Azure ad-nek megfelelő összes IP-cím elérését
      - Ha a jövőben új címeket adnak hozzá az Azure AD-hoz, létre kell hoznia új NSG-szabályokat.

### <a name="example-nsg-configuration"></a>Példa NSG-konfigurációra

Ez a példa bemutatja, hogyan konfigurálhatja a virtuális gépek NSG-szabályait a replikáláshoz.

- Ha NSG szabályokat használ a kimenő kapcsolatok vezérlésére, használja a "HTTPS kimenő" szabályokat a 443-as portra a szükséges IP-címtartományok esetében.
- A példa azt feltételezi, hogy a virtuális gép forrása "az USA keleti régiója", a célhely pedig az "USA középső régiója".

### <a name="nsg-rules---east-us"></a>NSG-szabályok – USA keleti régiója

1. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a "Storage. EastUS" számára a NSG, ahogy az alábbi képernyőképen is látható.

      ![Storage-tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a NSG "AzureActiveDirectory" számára, ahogy az alábbi képernyőképen is látható.

      ![HRE – címke](./media/azure-to-azure-about-networking/aad-tag.png)

3. Kimenő HTTPS (443) szabályok létrehozása a célhelynek megfelelő Site Recovery IP-címekhez:

   **Hely** | **Site Recovery IP-cím** |  **Site Recovery figyelési IP-cím**
    --- | --- | ---
   USA középső régiója | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>NSG-szabályok – USA középső régiója

Ezek a szabályok azért szükségesek, hogy a replikáció engedélyezhető legyen a célként megadott régióból a feladatátvételt követően:

1. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a "Storage. CentralUS" számára a NSG.

2. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a "AzureActiveDirectory" számára a NSG.

3. Kimenő HTTPS (443) szabályok létrehozása a forrás helyének megfelelő Site Recovery IP-címekhez:

   **Hely** | **Site Recovery IP-cím** |  **Site Recovery figyelési IP-cím**
    --- | --- | ---
   USA középső régiója | 13.82.88.226 | 104.45.147.24
## <a name="issue-3-site-recovery-configuration-failed-151197"></a>3\. probléma: Site Recovery konfiguráció nem sikerült (151197)
- **Lehetséges ok** </br>
  - Nem lehet kapcsolódni Azure Site Recovery szolgáltatási végpontokhoz.

- **Felbontás**
  - Az Azure Site Recoverynek hozzá kell férnie a [Site Recovery IP-címtartományaihoz](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges), a régiónak megfelelően. Gondoskodjon arról, hogy a szükséges IP-címtartományok elérhetők legyenek a virtuális gépről.


## <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>4\. probléma: a A2A replikációja meghiúsult, ha a hálózati forgalom a helyszíni proxykiszolgálón halad át (151072)
- **Lehetséges ok** </br>
  - Az egyéni proxybeállítások érvénytelenek, és Azure Site Recovery mobilitási szolgáltatás ügynöke nem tudta automatikusan felderíteni a proxybeállításokat az IE-ből


- **Felbontás**
  1. A mobilitási szolgáltatás ügynöke észleli a proxybeállításokat az IE-ből a Windows és a Linux rendszeren futó/etc/Environment.
  2. Ha inkább a proxyt szeretné beállítani Azure Site Recovery mobilitási szolgáltatáshoz, a proxy részleteit a ProxyInfo. conf fájlban találja a következő helyen:</br>
     - ``/usr/local/InMage/config/`` ***Linuxon***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` ***Windows*** rendszeren
  3. A ProxyInfo.conf a proxybeállításokat a következő INI-formátumban tárolja.</br>
                *proxy*</br>
                *Címe =http://1.2.3.4*</br>
                *Port = 567*</br>
  4. Azure Site Recovery mobilitási szolgáltatás ügynöke csak a nem ***hitelesített proxykat***támogatja.

### <a name="fix-the-problem"></a>A probléma javítása
[A szükséges URL-címek](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) vagy a [szükséges IP-tartományok](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)engedélyezéséhez kövesse a [hálózatkezelési útmutató dokumentum](site-recovery-azure-to-azure-networking-guidance.md)lépéseit.


## <a name="next-steps"></a>Következő lépések
[Azure-alapú virtuális gépek replikálása](site-recovery-replicate-azure-to-azure.md)
