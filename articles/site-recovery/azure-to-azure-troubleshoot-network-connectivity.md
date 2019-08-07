---
title: Az Azure – Azure hálózati kapcsolati problémák és hibák Azure Site Recovery hibaelhárítása | Microsoft Docs
description: Hibák és problémák elhárítása az Azure-beli virtuális gépek vész-helyreállításhoz való replikálásakor
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/05/2019
ms.author: asgang
ms.openlocfilehash: 5ed57c93f73eb9a9e972a683f1068692a5963e54
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816964"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Az Azure – Azure virtuálisgép-hálózat csatlakozási problémáinak elhárítása

Ez a cikk a hálózati kapcsolattal kapcsolatos gyakori problémákat ismerteti, amikor az Azure-beli virtuális gépeket egy régióból egy másik régióba replikálja és helyreállítja. A hálózati követelményekkel kapcsolatos további információkért tekintse [meg az Azure-beli virtuális gépek replikálásának kapcsolódási követelményeit](azure-to-azure-about-networking.md)ismertető témakört.

A Site Recovery replikációja, a kimenő kapcsolat az adott URL-címek vagy IP-címtartományok szükség a virtuális gépről. Ha a virtuális gép tűzfal mögött található, vagy használja a hálózati biztonsági csoport (NSG) szabályai kimenő kapcsolat szabályozásához, előfordulhat, hogy között ezek a problémák egyike.

**URL-cím** | **Részletek**  
--- | ---
*.blob.core.windows.net | Kötelező megadni, hogy az adatok a virtuális gépről származó forrás régióban lévő cache Storage-fiókba írhatók legyenek. Ha ismeri a virtuális gépekhez tartozó összes gyorsítótárbeli Storage-fiókot, engedélyezheti a-a megadott Storage-fiók URL-címeinek (például cache1.blob.core.windows.net és cache2.blob.core.windows.net) listázását *. blob.core.windows.net
login.microsoftonline.com | Az engedélyezéshez és a hitelesítéshez szükséges a Site Recovery szolgáltatás URL-címeihez.
*.hypervrecoverymanager.windowsazure.com | Szükséges, hogy a Site Recovery szolgáltatás kommunikációja a virtuális gépről is megtörténjen. Ha a tűzfal proxyja támogatja az IP-címeket, használhatja a megfelelő "Site Recovery IP-címet".
*.servicebus.windows.net | Szükséges, hogy a Site Recovery monitorozási és diagnosztikai adatok a virtuális gépről is írhatók legyenek. Ha a tűzfal proxyja támogatja az IP-címeket, használhatja a megfelelő "Site Recovery figyelési IP-címet".

# <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>A Site Recovery URL-címek vagy IP-címtartományokat (hibakód: 151037 vagy 151072) kimenő kapcsolatok

## <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>1. probléma: Nem sikerült regisztrálni az Azure-beli virtuális gépet Site Recovery (151195) </br>
- **Lehetséges ok** </br>
  - A DNS-feloldási hiba miatt nem lehet kapcsolódni Site Recovery végpontokhoz.
  - Ez gyakran látható ismételt védelem során a virtuális gép feladatátadása, de a DNS-kiszolgáló nem érhető el a DR régióban.

- **Felbontás**
   - Ha egyéni DNS-t használ, győződjön meg arról, hogy a DNS-kiszolgáló elérhető a vész-helyreállítási régióból. Ha egy egyéni DNS nyissa meg a virtuális gép rendelkezik-e > vész-helyreállítási hálózat > DNS-kiszolgálók. Próbálja ki a virtuális gépről a DNS-kiszolgáló eléréséhez. Ha nem érhető el, tegye elérhetővé a DNS-kiszolgáló meghibásodása vagy a hely létrehozása a DR hálózat és a DNS között.

    ![COM-hiba](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


## <a name="issue-2-site-recovery-configuration-failed-151196"></a>2\. probléma: Site Recovery konfiguráció nem sikerült (151196)

> [!NOTE]
> Ha a virtuális gépek a **standard** belső terheléselosztó mögött vannak, akkor alapértelmezés szerint nem férnek hozzá a O365 IP-címekhez (azaz login.microsoftonline.com). Módosítsa az alapszintű belső terheléselosztó típusára, vagy hozzon létre kimenő hozzáférést a [cikkben](https://aka.ms/lboutboundrulescli)említettek szerint.

- **Lehetséges ok** </br>
  - Az Office 365 portál és identitás IP4 végpontok nem létesíthető kapcsolat.

- **Felbontás**
  - Az Azure Site Recovery-hitelesítéshez szükséges hozzáférés az Office 365 IP-címek tartományát.
    Ha az Azure hálózati biztonsági csoport (NSG) szabályai, illetve a tűzfal proxy segítségével szabályozza a kimenő hálózati kapcsolatokra a virtuális Gépen, győződjön meg arról, Office 365 IP-tartományokkal való kommunikáció engedélyezése. Hozzon létre egy [Azure Active Directory (Azure ad) Service tag](../virtual-network/security-overview.md#service-tags) -alapú NSG-szabályt, amely lehetővé teszi az Azure ad-nek megfelelő összes IP-cím elérését
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

   **Location** | **Site Recovery IP-cím** |  **Site Recovery figyelési IP-cím**
    --- | --- | ---
   USA középső régiója | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>NSG-szabályok – USA középső régiója

Ezek a szabályok azért szükségesek, hogy a replikáció engedélyezhető legyen a célként megadott régióból a feladatátvételt követően:

1. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a "Storage. CentralUS" számára a NSG.

2. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a "AzureActiveDirectory" számára a NSG.

3. Kimenő HTTPS (443) szabályok létrehozása a forrás helyének megfelelő Site Recovery IP-címekhez:

   **Location** | **Site Recovery IP-cím** |  **Site Recovery figyelési IP-cím**
    --- | --- | ---
   USA középső régiója | 13.82.88.226 | 104.45.147.24
## <a name="issue-3-site-recovery-configuration-failed-151197"></a>3\. probléma: Site Recovery konfiguráció nem sikerült (151197)
- **Lehetséges ok** </br>
  - Nem lehet kapcsolatot az Azure Site Recovery szolgáltatási végpontjait.

- **Felbontás**
  - Az Azure Site Recovery szükséges hozzáférést [Site Recovery IP-címtartományok](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) a régiójától függően. Győződjön meg arról, hogy a szükséges ip-címtartományok érhetők el a virtuális gép.


## <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>4\. probléma: A A2A replikációja meghiúsult, ha a hálózati forgalom a helyszíni proxykiszolgálón halad át (151072)
- **Lehetséges ok** </br>
  - Az egyéni proxybeállítások érvénytelenek, és Azure Site Recovery mobilitási szolgáltatás ügynöke nem tudta automatikusan felderíteni a proxybeállításokat az IE-ből


- **Felbontás**
  1. A mobilitási szolgáltatás ügynökének a proxybeállításokat az Internet Explorer a Windows és Linux rendszeren /etc/environment észleli.
  2. Ha inkább a proxyt szeretné beállítani Azure Site Recovery mobilitási szolgáltatáshoz, a proxy részleteit a ProxyInfo. conf fájlban találja a következő helyen:</br>
     - ``/usr/local/InMage/config/`` a ***Linux***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` a ***Windows***
  3. A ProxyInfo.conf kell a proxybeállításokat a következő INI-formátumban.</br>
                *[proxy]*</br>
                *Cím =http://1.2.3.4*</br>
                *Port = 567*</br>
  4. Azure Site Recovery mobilitási szolgáltatás ügynöke csak a nem ***hitelesített proxykat***támogatja.

### <a name="fix-the-problem"></a>A probléma megoldása
[A szükséges URL-címek](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) vagy a [szükséges IP-tartományok](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)engedélyezéséhez kövesse a [hálózatkezelési útmutató dokumentum](site-recovery-azure-to-azure-networking-guidance.md)lépéseit.


## <a name="next-steps"></a>További lépések
[Azure-alapú virtuális gépek replikálása](site-recovery-replicate-azure-to-azure.md)
