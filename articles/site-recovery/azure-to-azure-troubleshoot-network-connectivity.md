---
title: Az Azure és az Azure vész-helyreállítási kapcsolatának hibaelhárítása az Azure Site Recovery szolgáltatással
description: Kapcsolódási problémák elhárítása az Azure virtuális gép vész-helyreállítási
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 08/05/2019
ms.openlocfilehash: b082e1aca094dcb335a7268e4c116376d756fd3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292026"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Az Azure–Azure virtuális gép hálózati kapcsolatával kapcsolatos problémák elhárítása

Ez a cikk ismerteti a hálózati kapcsolattal kapcsolatos gyakori problémákat, amikor replikálja és helyreállítja az Azure virtuális gépeket az egyik régióból a másikba. A hálózati követelményekről az [Azure virtuális gépek replikálásának kapcsolódási követelményeiben](azure-to-azure-about-networking.md)talál további információt.

Ahhoz, hogy a Site Recovery replikációja működjön, a virtuális géptől adott URL-címekhez vagy IP-tartományokhoz kimenő kapcsolat szükséges. Ha a virtuális gép tűzfal mögött van, vagy hálózati biztonsági csoport (NSG) szabályokat használ a kimenő kapcsolatok szabályozására, előfordulhat, hogy az alábbi problémák egyikével szembesülhet.

**Url** | **Részletek**  
--- | ---
*.blob.core.windows.net | Szükséges, hogy az adatok at lehet írni a gyorsítótár-tárfiók a forrásrégióban a virtuális gépről. Ha ismeri a virtuális gépek összes gyorsítótár-tárfiókját, a *.blob.core.windows.net helyett engedélyezheti az adott tárfiók URL-címeit (például cache1.blob.core.windows.net és cache2.blob.core.windows.net).
login.microsoftonline.com | A Site Recovery szolgáltatás URL-címének engedélyezéséhez és hitelesítéséhez szükséges.
*.hypervrecoverymanager.windowsazure.com | Szükséges, hogy a Site Recovery szolgáltatás kommunikációja a virtuális gépről is megtörténhessen. A megfelelő "Site Recovery IP" protokollt akkor használhatja, ha a tűzfalproxy támogatja az IP-címeket.
*.servicebus.windows.net | Szükséges, hogy a site recovery figyelési és diagnosztikai adatokat lehet írni a virtuális gépről. A megfelelő "Site Recovery Monitoring IP" protokollt akkor használhatja, ha a tűzfalproxy támogatja az IP-címeket.

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Kimenő kapcsolat a hely-helyreállítási URL-címekhez vagy IP-tartományokhoz (hibakód: 151037 vagy 151072)

## <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a><a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>1. probléma: Nem sikerült regisztrálni az Azure virtuális gépet a Site Recovery szolgáltatással (151195) </br>
- **Lehetséges ok** </br>
  - Dns-feloldási hiba miatt nem lehet kapcsolatot létesíteni a hely-helyreállítási végpontokkal.
  - Ez gyakrabban fordul elő ismételt védelem esetén, amikor feladatátvételt végzett a virtuális gépen, de a DNS-kiszolgáló nem érhető el a vészhelyreállítási régióból.

- **Resolution** (Osztás)
   - Ha egyéni DNS-t használ, győződjön meg arról, hogy a DNS-kiszolgáló elérhető a Vész-helyreállítási régióból. Annak ellenőrzéséhez, hogy van-e egyéni DNS-e, látogasson el a virtuális gép> vész-helyreállítási hálózat> DNS-kiszolgálók. Próbálja meg elérni a DNS-kiszolgálót a virtuális gépről. Ha nem érhető el, tegye elérhetővé a DNS-kiszolgáló feletti hiba, vagy a DR-hálózat és a DNS közötti helyvonal létrehozásával.

    ![com-hiba](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


## <a name="issue-2-site-recovery-configuration-failed-151196"></a>2. probléma: A hely-helyreállítási konfiguráció nem sikerült (151196)

> [!NOTE]
> Ha a virtuális gépek a **standard** belső terheléselosztó mögött vannak, alapértelmezés szerint nem férnek hozzá az O365 IP-címekhez (azaz login.microsoftonline.com). Módosítsa **alapszintű** belső terheléselosztó típusra, vagy hozzon létre kimenő hozzáférést a [cikkben](https://aka.ms/lboutboundrulescli)említettek szerint.

- **Lehetséges ok** </br>
  - Nem lehet kapcsolatot létesíteni az Office 365 hitelesítési és IP4-identitás-végpontjaival.

- **Resolution** (Osztás)
  - Az Azure Site Recovery hozzáféréssel rendelkezik az Office 365 IP-kiszolgálóihoz a hitelesítéshez.
    Ha az Azure Network biztonsági csoport (NSG) szabályok/tűzfal proxy a kimenő hálózati kapcsolat a virtuális gép, győződjön meg arról, hogy engedélyezi a kommunikációt O365 IPranges. Hozzon létre egy [Azure Active Directory (Azure AD) szolgáltatáscímke](../virtual-network/security-overview.md#service-tags) alapú NSG-szabályt, amely lehetővé teszi az Azure AD-nek megfelelő összes IP-cím hez való hozzáférést
      - Ha a jövőben új címeket ad hozzá az Azure AD-hez, új NSG-szabályokat kell létrehoznia.

### <a name="example-nsg-configuration"></a>Példa NSG-konfigurációra

Ez a példa bemutatja, hogyan konfigurálhatja az NSG-szabályok at a virtuális gép replikálására.

- Ha NSG-szabályokat használ a kimenő kapcsolatok vezérléséhez, használja a "HTTPS kimenő kimenő" szabályok port:443 az összes szükséges IP-címtartományok.
- A példa feltételezi, hogy a virtuális gép forráshelye "USA keleti régiója", a célhely pedig "USA középső régiója".

### <a name="nsg-rules---east-us"></a>NSG szabályok - USA keleti része

1. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a "Storage.EastUS" számára az NSG-n, ahogy az az alábbi képernyőképen látható.

      ![tároló címke](./media/azure-to-azure-about-networking/storage-tag.png)

2. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt az "AzureActiveDirectory" számára az NSG-n, ahogy az az alábbi képernyőképen látható.

      ![aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. Kimenő HTTPS (443) szabályok létrehozása a hely-helyreállítási IP-khez, amelyek megfelelnek a célhelynek:

   **Helyen** | **Hely-helyreállítási IP-cím** |  **Helyhelyreállítása figyelésének IP-címe**
    --- | --- | ---
   USA középső régiója | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>NSG-szabályok - USA középső régiója

Ezekre a szabályokra azért van szükség, hogy a replikáció engedélyezhető legyen a célrégióból a forrásrégióba a feladatátvétel után:

1. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a "Storage.CentralUS" számára az NSG-n.

2. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt az "AzureActiveDirectory" számára az NSG-n.

3. Kimenő HTTPS (443) szabályok létrehozása a hely-helyreállítási IP-khez, amelyek megfelelnek a forráshelynek:

   **Helyen** | **Hely-helyreállítási IP-cím** |  **Helyhelyreállítása figyelésének IP-címe**
    --- | --- | ---
   USA középső régiója | 13.82.88.226 | 104.45.147.24
## <a name="issue-3-site-recovery-configuration-failed-151197"></a>3. probléma: A hely-helyreállítási konfiguráció nem sikerült (151197)
- **Lehetséges ok** </br>
  - Nem hozható létre kapcsolat az Azure Site Recovery szolgáltatás végpontjaihoz.

- **Resolution** (Osztás)
  - Az Azure Site Recoverynek hozzá kell férnie a [Site Recovery IP-címtartományaihoz](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-using-service-tags), a régiónak megfelelően. Gondoskodjon arról, hogy a szükséges IP-címtartományok elérhetők legyenek a virtuális gépről.


## <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>4. probléma: Az A2A-replikáció nem sikerült, amikor a hálózati forgalom a helyszíni proxykiszolgálón keresztül ment (151072)
- **Lehetséges ok** </br>
  - Az egyéni proxybeállítások érvénytelenek, és az Azure Site Recovery Mobility Service ügynök nem észlelte automatikusan az IE proxybeállításait


- **Resolution** (Osztás)
  1. A Mobility Service agent észleli az IE proxybeállításait Windows rendszeren és /etc/environment Linuxon.
  2. Ha csak az Azure Site Recovery Mobility Service proxyját szeretné beállítani, a proxy részleteit a ProxyInfo.conf webhelyen adja meg a következő helyen:</br>
     - ``/usr/local/InMage/config/``***Linuxon***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config``***Windows*** rendszeren
  3. A ProxyInfo.conf a proxybeállításokat a következő INI-formátumban tárolja.</br>
                *[proxy]*</br>
                *Cím =http://1.2.3.4*</br>
                *Port=567*</br>
  4. Az Azure Site Recovery Mobility Service ügynök csak nem ***hitelesített proxykat***támogat.

### <a name="fix-the-problem"></a>A probléma javítása
A [szükséges URL-címek](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) vagy a [szükséges IP-tartományok](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)engedélyezéséhez kövesse a [hálózati útmutató dokumentum lépéseit](site-recovery-azure-to-azure-networking-guidance.md).


## <a name="next-steps"></a>További lépések
[Azure-alapú virtuális gépek replikálása](site-recovery-replicate-azure-to-azure.md)
