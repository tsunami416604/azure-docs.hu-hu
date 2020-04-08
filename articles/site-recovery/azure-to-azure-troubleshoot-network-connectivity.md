---
title: Az Azure és az Azure vész-helyreállítási kapcsolatának hibaelhárítása az Azure Site Recovery szolgáltatással
description: Kapcsolódási problémák elhárítása az Azure virtuális gép vész-helyreállítási
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: 49d2d3d3e8829198a57aaf2feb40e89f105667bd
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804860"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Az Azure–Azure virtuális gép hálózati kapcsolatával kapcsolatos problémák elhárítása

Ez a cikk ismerteti a hálózati kapcsolattal kapcsolatos gyakori problémákat, amikor replikálja és helyreállítja az Azure virtuális gépek (VM) egyik régióból a másikba. A hálózati követelményekről az [Azure virtuális gépek replikálásának kapcsolódási követelményeiben](azure-to-azure-about-networking.md)talál további információt.

Ahhoz, hogy a Site Recovery replikációja működjön, a virtuális géptől adott URL-címekhez vagy IP-tartományokhoz kimenő kapcsolat szükséges. Ha a virtuális gép tűzfal mögött van, vagy hálózati biztonsági csoport (NSG) szabályokat használ a kimenő kapcsolatok szabályozására, előfordulhat, hogy az alábbi problémák egyikével szembesülhet.

| **Url** | **Részletek** |
| --- | --- |
| `*.blob.core.windows.net` | Szükséges, hogy az adatok at lehet írni a gyorsítótár-tárfiók a forrásrégióban a virtuális gépről. Ha ismeri a virtuális gépek gyorsítótár-tárfiókok, használhatja az engedélyezési lista az adott tárfiók URL-címek. Például, `cache1.blob.core.windows.net` `cache2.blob.core.windows.net` és `*.blob.core.windows.net`ahelyett, hogy . |
| `login.microsoftonline.com` | A Site Recovery szolgáltatás URL-címének engedélyezéséhez és hitelesítéséhez szükséges. |
| `*.hypervrecoverymanager.windowsazure.com` | Szükséges, hogy a Site Recovery szolgáltatás kommunikációja a virtuális gépről is megtörténhessen. A megfelelő _site recovery IP-címet akkor használhatja,_ ha a tűzfalproxy támogatja az IP-címeket. |
| `*.servicebus.windows.net` | Szükséges, hogy a site recovery figyelési és diagnosztikai adatokat lehet írni a virtuális gépről. A megfelelő _site recovery monitoring IP-címet használhatja,_ ha a tűzfalproxy támogatja az IP-címeket. |

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Kimenő kapcsolat a hely-helyreállítási URL-címekhez vagy IP-tartományokhoz (hibakód: 151037 vagy 151072)

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>1. probléma: Nem sikerült regisztrálni az Azure virtuális gépet a Site Recovery szolgáltatással (151195)

#### <a name="possible-cause"></a>Lehetséges ok

A dns-feloldási hiba miatt nem lehet kapcsolatot létesíteni a hely-helyreállítási végpontokkal. Ez a probléma gyakoribb az újravédelem során, ha a virtuális gép nem felelt meg, de a DNS-kiszolgáló nem érhető el a vész-helyreállítási (DR) régióban.

#### <a name="resolution"></a>Megoldás:

Ha egyéni DNS-t használ, győződjön meg arról, hogy a DNS-kiszolgáló elérhető a vész-helyreállítási régióból.

Annak ellenőrzése, hogy a virtuális gép egyéni DNS-beállítást használ-e:

1. Nyissa **meg a virtuális gépeket,** és válassza ki a virtuális gépet.
1. Nyissa meg a virtuális gépek **beállításait,** és válassza **a Hálózat lehetőséget.**
1. A **Virtuális hálózat/alhálózat alkalmazásban**válassza ki a hivatkozást a virtuális hálózat erőforráslapjának megnyitásához.
1. Nyissa meg a **Beállítások lapot,** és válassza a **DNS-kiszolgálók lehetőséget.**

Próbálja meg elérni a DNS-kiszolgálót a virtuális gépről. Ha a DNS-kiszolgáló nem érhető el, tegye elérhetővé a DNS-kiszolgáló feletti hiba, vagy a DR-hálózat és a DNS közötti helyvonal létrehozásával.

  :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-hiba":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>2. probléma: A hely-helyreállítási konfiguráció nem sikerült (151196)

> [!NOTE]
> Ha a virtuális gépek egy **szabványos** belső terheléselosztó mögött vannak, alapértelmezés szerint nem férnek hozzá `login.microsoftonline.com`az Office 365 IP-címhez, például . Módosítsa azt **alapszintű** belső terheléselosztó típusra, vagy hozzon létre kimenő hozzáférést a terheléselosztás és a kimenő szabályok konfigurálása a Standard Load Balancer ben az [Azure CLI használatával című cikkben](/azure/load-balancer/configure-load-balancer-outbound-cli)említettek szerint.

#### <a name="possible-cause"></a>Lehetséges ok

Az Office 365 hitelesítési és IP4-identitás-végpontjaihoz nem lehet kapcsolatot létesíteni.

#### <a name="resolution"></a>Megoldás:

- Az Azure Site Recovery hitelesítéshez hozzáférést igényel az Office 365 IP-tartományaihoz.
- Ha az Azure Network biztonsági csoport (NSG) szabályokat/tűzfalproxyt használja a virtuális gép kimenő hálózati kapcsolatának szabályozásához, győződjön meg arról, hogy engedélyezi az Office 365 IP-tartományaiközötti kommunikációt. Hozzon létre egy [Azure Active Directory (Azure AD) szolgáltatáscímke](/azure/virtual-network/security-overview#service-tags) alapú NSG-szabályt, amely lehetővé teszi a hozzáférést az Azure AD-nek megfelelő összes IP-címhez.
- Ha a jövőben új címeket ad hozzá az Azure AD-hez, új NSG-szabályokat kell létrehoznia.

### <a name="example-nsg-configuration"></a>Példa NSG-konfigurációra

Ez a példa bemutatja, hogyan konfigurálhatja az NSG-szabályok at a virtuális gép replikálására.

- Ha NSG-szabályokat használ a kimenő kapcsolatok vezérléséhez, használja **a HTTPS kimenő** szabályok engedélyezése a 443-as porthoz az összes szükséges IP-címtartományhoz.
- A példa feltételezi, hogy a virtuális gép forráshelye **USA keleti régiója,** a célhely pedig **USA középső**régiója.

#### <a name="nsg-rules---east-us"></a>NSG szabályok - USA keleti része

1. Hozzon létre egy HTTPS kimenő biztonsági szabályt az NSG-hez az alábbi képernyőképen látható módon. Ez a példa a **Cél szolgáltatáscímkét**használja: _Storage.EastUS_ és **Destination port tartományok**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/storage-tag.png" alt-text="tároló címke":::

1. Hozzon létre egy HTTPS kimenő biztonsági szabályt az NSG-hez az alábbi képernyőképen látható módon. Ez a példa a **Cél szolgáltatáscímkét**használja: _Az AzureActiveDirectory_ és **a Célport tartományok**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/aad-tag.png" alt-text="aad-tag":::

1. Https-port 443 kimenő szabályokat hozhat létre a hely-helyreállítási IP-khez, amelyek megfelelnek a célhelynek:

   | **Helyen** | **Hely-helyreállítási IP-cím** |  **Helyhelyreállítása figyelésének IP-címe** |
   | --- | --- | --- |
   | USA középső régiója | 40.69.144.231 | 52.165.34.144 |

#### <a name="nsg-rules---central-us"></a>NSG-szabályok - USA középső régiója

Ebben a példában ezek az NSG-szabályok szükségesek, hogy a replikáció engedélyezhető legyen a célrégióból a forrásrégióba a feladatátvétel után:

1. HTTPS kimenő biztonsági szabály létrehozása a _Storage.CentralUS számára:_

   - **Célszolgáltatás címkéje**: _Storage.CentralUS_
   - **Célport-tartományok**: _443_

1. Https kimenő biztonsági szabály létrehozása az _AzureActiveDirectory számára._

   - **Célszolgáltatás-címke**: _AzureActiveDirectory_
   - **Célport-tartományok**: _443_

1. Https-port 443 kimenő szabályokat hozhat létre a hely-helyreállítási IP-khez, amelyek megfelelnek a forráshelynek:

   |**Helyen** | **Hely-helyreállítási IP-cím** |  **Helyhelyreállítása figyelésének IP-címe** |
   | --- | --- | --- |
   | USA keleti régiója | 13.82.88.226 | 104.45.147.24 |

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>3. probléma: A hely-helyreállítási konfiguráció nem sikerült (151197)

#### <a name="possible-cause"></a>Lehetséges ok

A kapcsolat nem lehet létrehozni az Azure Site Recovery szolgáltatás végpontjaihoz.

#### <a name="resolution"></a>Megoldás:

Az Azure Site Recoverynek hozzá kell férnie a [Site Recovery IP-címtartományaihoz](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags), a régiónak megfelelően. Győződjön meg arról, hogy a szükséges IP-tartományok érhetők el a virtuális gépről.

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>4. probléma: Az Azure-ból Azure-ba irányuló replikáció sikertelen volt, amikor a hálózati forgalom a helyszíni proxykiszolgálón keresztül ment (151072)

#### <a name="possible-cause"></a>Lehetséges ok

Az egyéni proxybeállítások érvénytelenek, és az Azure Site Recovery Mobility szolgáltatásügynök nem észlelte automatikusan az Internet Explorer (IE) proxybeállításait.

#### <a name="resolution"></a>Megoldás:

1. A Mobility service agent észleli a proxy `/etc/environment` beállításokat ie Windows és Linux rendszeren.
1. Ha csak az Azure Site Recovery Mobility szolgáltatásproxyt szeretné beállítani, a proxy részleteit a _ProxyInfo.conf_ webhelyen adja meg a következő helyen:

   - **Linux**:`/usr/local/InMage/config/`
   - **Windows**:`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. A _ProxyInfo.conf_ proxybeállításainak a következő INI formátumban kell _rendelkeznie:_

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

1. Az Azure Site Recovery Mobility service agent csak **a nem hitelesített proxykat**támogatja.

### <a name="fix-the-problem"></a>A probléma javítása

A [szükséges URL-címek](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) vagy a [szükséges IP-tartományok](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)engedélyezéséhez kövesse a [hálózati útmutató dokumentum lépéseit](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="next-steps"></a>További lépések

[Azure-alapú virtuális gépek replikálása](site-recovery-replicate-azure-to-azure.md)
