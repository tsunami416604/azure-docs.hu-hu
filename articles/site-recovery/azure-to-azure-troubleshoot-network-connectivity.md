---
title: Az Azure-hoz való kapcsolódás hibáinak elhárítása az Azure-ban Azure Site Recovery
description: Az Azure-beli virtuális gép vész-helyreállítási kapcsolati problémáinak elhárítása
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: 9600f1cae61b59af5d026eb74f504658395a11ae
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87835884"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Az Azure – Azure virtuálisgép-hálózat csatlakozási problémáinak elhárítása

Ez a cikk a hálózati kapcsolattal kapcsolatos gyakori problémákat ismerteti, amikor Azure-beli virtuális gépeket (VM) replikál és helyreállít az egyik régióból a másikba. A hálózati követelményekkel kapcsolatos további információkért tekintse [meg az Azure-beli virtuális gépek replikálásának kapcsolódási követelményeit](azure-to-azure-about-networking.md)ismertető témakört.

Ahhoz, hogy Site Recovery replikáció működjön, az adott URL-címekhez vagy IP-tartományokhoz kimenő kapcsolat szükséges a virtuális gépről. Ha a virtuális gép tűzfal mögött van, vagy hálózati biztonsági csoport (NSG) szabályok használatával vezérli a kimenő kapcsolatot, akkor előfordulhat, hogy ezek egyike a probléma.

| **Név**                  | **Kereskedelmi**                               | **Államigazgatás**                                 | **Leírás** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Storage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`              | Kötelező megadni, hogy az adatok a virtuális gépről származó forrás régióban lévő cache Storage-fiókba írhatók legyenek. Ha ismeri a virtuális gépekhez tartozó összes gyorsítótár-tárolási fiókot, használhat egy engedélyezési listát az adott Storage-fiók URL-címeihez. Például a `cache1.blob.core.windows.net` és `cache2.blob.core.windows.net` a helyett `*.blob.core.windows.net` . |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Az engedélyezéshez és a hitelesítéshez szükséges a Site Recovery szolgáltatás URL-címeihez. |
| Replikáció               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Szükséges, hogy a Site Recovery szolgáltatás kommunikációja a virtuális gépről is megtörténjen. A megfelelő _site Recovery IP-címet_ használhatja, ha a tűzfal proxyja támogatja az IP-címeket. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Szükséges, hogy a Site Recovery monitorozási és diagnosztikai adatok a virtuális gépről is írhatók legyenek. Ha a tűzfal proxyja támogatja az IP-címeket, használhatja a megfelelő _site Recovery figyelési IP-címet_ . |

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Kimenő kapcsolat Site Recovery URL-címekhez vagy IP-tartományokhoz (hibakód: 151037 vagy 151072)

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>1. probléma: nem sikerült regisztrálni az Azure-beli virtuális gépet Site Recovery (151195)

#### <a name="possible-cause"></a>Lehetséges ok

A tartománynévrendszer (DNS) feloldási hibája miatt nem lehet kapcsolódni Site Recovery végpontokhoz. Ez a probléma gyakoribb a virtuális gép feladatátvétele során, de a DNS-kiszolgáló nem érhető el a vész-helyreállítási (DR) régióból.

#### <a name="resolution"></a>Feloldás

Ha egyéni DNS-t használ, győződjön meg arról, hogy a DNS-kiszolgáló elérhető a vész-helyreállítási régióból.

Annak ellenőrzését, hogy a virtuális gép használ-e egyéni DNS-beállítást:

1. Nyissa meg a **virtuális gépeket** , és válassza ki a virtuális gépet.
1. Navigáljon a virtuális gépek **beállításaihoz** , és válassza a **hálózatkezelés**lehetőséget.
1. A **virtuális hálózat/alhálózat**területen válassza a virtuális hálózat erőforrás-lapjának megnyitására szolgáló hivatkozást.
1. Lépjen a **Beállítások** és a **DNS-kiszolgálók**elemre.

Próbálja meg elérni a DNS-kiszolgálót a virtuális gépről. Ha a DNS-kiszolgáló nem érhető el, tegye elérhetővé a DNS-kiszolgáló meghibásodása vagy a hely létrehozása a DR hálózat és a DNS között.

  :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com – hiba":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>2. probléma: Site Recovery konfiguráció nem sikerült (151196)

> [!NOTE]
> Ha a virtuális gépek **szabványos** belső terheléselosztó mögött vannak, alapértelmezés szerint nem férnek hozzá az Office 365 IP-címeihez, például a következőhöz: `login.microsoftonline.com` . Módosítsa az **alapszintű** belső terheléselosztó típusára, vagy hozzon létre kimenő hozzáférést a [standard Load Balancer terheléselosztás és kimenő szabályok konfigurálása az Azure CLI használatával](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard#create-outbound-rule-configuration)című cikkben említettek szerint.

#### <a name="possible-cause"></a>Lehetséges ok

Nem hozhatók összefüggésbe az Office 365-hitelesítés és az Identity IP4-végpontok.

#### <a name="resolution"></a>Feloldás

- A Azure Site Recovery hozzáférést igényel az Office 365 IP-tartományokhoz a hitelesítéshez.
- Ha az Azure hálózati biztonsági csoport (NSG) szabályait/tűzfal proxyját használja a kimenő hálózati kapcsolat vezérléséhez a virtuális gépen, ügyeljen arra, hogy az Office 365 IP-tartományokhoz való kommunikációt engedélyezze. Hozzon létre egy [Azure Active Directory (Azure ad) Service tag](../virtual-network/security-overview.md#service-tags) -alapú NSG-szabályt, amely lehetővé teszi az Azure ad-nek megfelelő összes IP-cím elérését.
- Ha a jövőben új címeket adnak hozzá az Azure AD-hoz, létre kell hoznia új NSG-szabályokat.

### <a name="example-nsg-configuration"></a>Példa NSG-konfigurációra

Ez a példa bemutatja, hogyan konfigurálhatja a virtuális gépek NSG-szabályait a replikáláshoz.

- Ha NSG szabályokat használ a kimenő kapcsolatok vezérlésére, a **https-kimenő szabályok engedélyezése** a 443-as portra az összes szükséges IP-címtartományok esetében.
- A példa azt feltételezi, hogy a virtuális gép forrásának helye az **USA keleti** régiója, a célhely pedig az **USA középső**régiója.

#### <a name="nsg-rules---east-us"></a>NSG-szabályok – USA keleti régiója

1. Hozzon létre egy HTTPS-alapú kimenő biztonsági szabályt a NSG, ahogy az alábbi képernyőfelvételen is látható. Ez a példa a **célként megadott szolgáltatási címkét**használja: _Storage. EastUS_ és a **célport tartománya**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/storage-tag.png" alt-text="Storage-tag":::

1. Hozzon létre egy HTTPS-alapú kimenő biztonsági szabályt a NSG, ahogy az alábbi képernyőfelvételen is látható. Ez a példa a **célként megadott szolgáltatási címkét**használja: a _AzureActiveDirectory_ és a **célport tartományait**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/aad-tag.png" alt-text="HRE – címke":::

1. Hozzon létre HTTPS-portot 443 kimenő szabályok a célhelynek megfelelő Site Recovery IP-címekhez:

   | Hely | Site Recovery IP-cím | Site Recovery figyelési IP-cím |
   | --- | --- | --- |
   | USA középső régiója | 40.69.144.231 | 52.165.34.144 |

#### <a name="nsg-rules---central-us"></a>NSG-szabályok – USA középső régiója

Ebben a példában ezek a NSG szabályok szükségesek ahhoz, hogy a replikáció engedélyezhető legyen a célként megadott régióban a feladatátvételt követően:

1. Hozzon létre egy HTTPS kimenő biztonsági szabályt a _Storage. CentralUS_:

   - **Célhely szolgáltatás címkéje**: _Storage. CentralUS_
   - **Célport tartományai**: _443_

1. Hozzon létre egy HTTPS-alapú kimenő biztonsági szabályt a _AzureActiveDirectory_.

   - **Rendeltetési szolgáltatás címkéje**: _AzureActiveDirectory_
   - **Célport tartományai**: _443_

1. Hozzon létre HTTPS-portot 443 kimenő szabályok a forrás helyének megfelelő Site Recovery IP-címekhez:

   | Hely | Site Recovery IP-cím | Site Recovery figyelési IP-cím |
   | --- | --- | --- |
   | USA keleti régiója | 13.82.88.226 | 104.45.147.24 |

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>3. probléma: Site Recovery konfiguráció nem sikerült (151197)

#### <a name="possible-cause"></a>Lehetséges ok

Nem lehet kapcsolódni Azure Site Recovery szolgáltatási végpontokhoz.

#### <a name="resolution"></a>Feloldás

Az Azure Site Recoverynek hozzá kell férnie a [Site Recovery IP-címtartományaihoz](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags), a régiónak megfelelően. Győződjön meg arról, hogy a szükséges IP-címtartományok elérhetők a virtuális gépről.

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>4. probléma: az Azure – Azure replikáció sikertelen volt, ha a hálózati forgalom a helyszíni proxykiszolgálón keresztül halad (151072)

#### <a name="possible-cause"></a>Lehetséges ok

Az egyéni proxybeállítások érvénytelenek, és a Azure Site Recovery mobilitási szolgáltatás ügynöke nem ismeri fel automatikusan a proxybeállításokat az Internet Explorerben (IE).

#### <a name="resolution"></a>Feloldás

1. A mobilitási szolgáltatás ügynöke észleli a proxybeállításokat az IE-ből a Windows és `/etc/environment` Linux rendszeren.
1. Ha inkább a proxyt szeretné beállítani Azure Site Recovery mobilitási szolgáltatáshoz, a proxy részleteit a _ProxyInfo. conf fájlban_ találja a következő helyen:

   - **Linux**:`/usr/local/InMage/config/`
   - **Windows**:`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. A _ProxyInfo. conf_ _fájlnak_ a következő ini formátumúnak kell lennie:

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Azure Site Recovery mobilitási szolgáltatás ügynöke csak a nem **hitelesített proxykat**támogatja.

### <a name="fix-the-problem"></a>A probléma javítása

[A szükséges URL-címek](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) vagy a [szükséges IP-tartományok](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)engedélyezéséhez kövesse a [hálózatkezelési útmutató dokumentum](./azure-to-azure-about-networking.md)lépéseit.

## <a name="next-steps"></a>További lépések

[Azure-beli virtuális gépek replikálása másik Azure-régióba](azure-to-azure-how-to-enable-replication.md)