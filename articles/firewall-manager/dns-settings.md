---
title: Azure Firewall házirend DNS-beállításai (előzetes verzió)
description: Azure Firewall házirendeket konfigurálhat a DNS-kiszolgáló és a DNS-proxy beállításaival.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: bf189e4c9853d9f2ff6e8495423f4f36f14f41d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85611798"
---
# <a name="azure-firewall-policy-dns-settings-preview"></a>Azure Firewall házirend DNS-beállításai (előzetes verzió)

> [!IMPORTANT]
> Azure Firewall DNS-beállítások jelenleg nyilvános előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Beállíthat egy egyéni DNS-kiszolgálót, és engedélyezheti a DNS-proxyt Azure Firewall házirendekhez. Ezeket a beállításokat a **DNS-beállítások** lapon konfigurálhatja a tűzfal telepítésekor vagy később is.

## <a name="dns-servers"></a>DNS-kiszolgálók

A DNS-kiszolgáló karbantartja és feloldja a tartományneveket az IP-címekre. Alapértelmezés szerint a Azure Firewall Azure DNS használ a névfeloldáshoz. A **DNS-kiszolgáló** beállítással konfigurálhatja a saját DNS-kiszolgálókat Azure Firewall névfeloldáshoz. Egyetlen vagy több kiszolgálót is beállíthat.

### <a name="configure-custom-dns-servers"></a>Egyéni DNS-kiszolgálók konfigurálása

1. Válassza ki a tűzfal házirendjét.
2. A **Beállítások**területen válassza a **DNS-beállítások**elemet.
3. A **DNS-kiszolgálók**területen beírhatja vagy hozzáadhat olyan meglévő DNS-kiszolgálókat, amelyek korábban meg lettek adva a Virtual Network.
4. Kattintson a **Mentés** gombra.
5. A tűzfal most a névfeloldáshoz a megadott DNS-kiszolgáló (k) re irányítja a DNS-forgalmat.

## <a name="dns-proxy-preview"></a>DNS-proxy (előzetes verzió)

Beállíthatja, hogy a Azure Firewall DNS-proxyként működjön. A DNS-proxy közvetítőként működik az ügyfél virtuális gépei által a DNS-kiszolgálóra irányuló DNS-kérelmek esetében. Ha egyéni DNS-kiszolgálót konfigurál, engedélyeznie kell a DNS-proxyt, hogy elkerülje a DNS-feloldás eltérését, és engedélyezze a teljes tartománynevek szűrését a hálózati szabályokban.

Ha nem engedélyezi a DNS-proxyt, előfordulhat, hogy az ügyféltől érkező DNS-kérések egy másik időpontban utaznak a DNS-kiszolgálóra, vagy egy másik választ adnak vissza a tűzfalhoz képest. A DNS-proxy Azure Firewall az ügyfelek kéréseinek elérési útjában az inkonzisztencia elkerülése érdekében.

A DNS-proxy konfigurálásához három lépés szükséges:
1. Engedélyezze a DNS-proxyt Azure Firewall DNS-beállításokban.
2. Igény szerint konfigurálhatja az egyéni DNS-kiszolgálót, vagy használhatja a megadott alapértelmezett értéket.
3. Végül konfigurálnia kell a Azure Firewall magánhálózati IP-címét egyéni DNS-címként a virtuális hálózat DNS-kiszolgálójának beállításaiban. Ez biztosítja, hogy a DNS-forgalom Azure Firewallre legyen irányítva.

### <a name="configure-dns-proxy-preview"></a>DNS-proxy konfigurálása (előzetes verzió)

A DNS-proxy konfigurálásához konfigurálnia kell a virtuális hálózati DNS-kiszolgálók beállítást a tűzfal magánhálózati IP-címének használatára. Ezután engedélyezze a DNS-proxyt Azure Firewall házirend **DNS-beállításaiban**.

#### <a name="configure-virtual-network-dns-servers"></a>Virtuális hálózati DNS-kiszolgálók konfigurálása

1. Válassza ki azt a virtuális hálózatot, ahol a DNS-forgalom a Azure Firewall keresztül lesz átirányítva.
2. A **Beállítások**területen válassza a **DNS-kiszolgálók**elemet.
3. Válassza az **Egyéni** lehetőséget a **DNS-kiszolgálók**területen.
4. Adja meg a tűzfal magánhálózati IP-címét.
5. Kattintson a **Mentés** gombra.

#### <a name="enable-dns-proxy-preview"></a>DNS-proxy engedélyezése (előzetes verzió)

1. Válassza ki a Azure Firewall szabályzatot.
2. A **Beállítások**területen válassza a **DNS-beállítások**elemet.
3. Alapértelmezés szerint a **DNS-proxy** le van tiltva. Ha engedélyezve van, a tűzfal figyeli a 53-es portot, és továbbítja a DNS-kéréseket a konfigurált DNS-kiszolgálókra.
4. Tekintse át a **DNS-kiszolgálók** konfigurációját, és győződjön meg arról, hogy a beállítások megfelelőek-e a környezetéhez.
5. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

[FQDN-szűrés a hálózati szabályokban](fqdn-filtering-network-rules.md)