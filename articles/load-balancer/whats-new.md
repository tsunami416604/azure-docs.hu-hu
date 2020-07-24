---
title: A Azure Load Balancer újdonságai
description: Ismerje meg a Azure Load Balancer újdonságait, például a legújabb kibocsátási megjegyzéseket, ismert problémákat, hibajavításokat, elavult funkciókat és a közelgő változásokat.
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: overview
ms.date: 07/07/2020
ms.author: anavin
ms.openlocfilehash: 8b44dc230dbee1b29b9889a1b81e35ebe25f6b97
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87078684"
---
# <a name="whats-new-in-azure-load-balancer"></a>A Azure Load Balancer újdonságai

A Azure Load Balancer rendszeresen frissül. Maradjon naprakész a legújabb bejelentésekkel. Ez a cikk a következő információkat tartalmazza:

- A legújabb kiadások
- Ismert problémák
- Hibajavítások
- Elavult funkció (ha van ilyen)

Itt megtalálhatja a legújabb Azure Load Balancer frissítéseket, és feliratkozhat [ide](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer)az RSS-hírcsatornára.

## <a name="recent-releases"></a>Legutóbbi kiadások

| Típus |Név |Leírás  |Hozzáadás dátuma  |
| ------ |---------|---------|---------|
| Funkció | Az IP-alapú háttér-készlet felügyeletének támogatása (előzetes verzió) | Azure Load Balancer támogatja az erőforrások hozzáadását és eltávolítását egy háttérbeli készletből IPv4-vagy IPv6-címek használatával. Ez lehetővé teszi a tárolók, a virtuális gépek és a Load Balancerhoz társított virtuálisgép-méretezési csoportok egyszerű kezelését. Azt is lehetővé teszi, hogy az IP-címek a háttérbeli készlet részeként legyenek fenntartva a társított erőforrások létrehozása előtt. További információt [itt](backend-pool-management.md) talál|2020. július |
| Funkció| A Azure Monitor használatával Azure Load Balancer a bepillantást | A hálózatok Azure Monitorának részeként az ügyfelek mostantól rendelkeznek topológiai térképekkel az összes Load Balancer-konfigurációhoz és az állapot irányítópulthoz az Azure Portal metrikákkal előre konfigurált standard Load Balancerekhez. [Első lépések és további információ](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | 2020. június |
| Érvényesítés | A HA-portok érvényesítésének hozzáadása | Az érvényesítés hozzá lett adva annak biztosításához, hogy ha a lebegőpontos IP-cím engedélyezve van, akkor a HA portra vonatkozó szabályok és a nem szükséges portszabályok nem konfigurálhatók. Korábban ez a konfiguráció áthalad, de nem a kívánt módon működik. Nem történt változás a funkcionalitásban. További információt [itt](load-balancer-ha-ports-overview.md#limitations) talál| 2020. június |
| Funkció| IPv6-támogatás a Azure Load Balancerhoz (általánosan elérhető) | Az Azure Load Balancer előtérben az IPv6-címek is megadhatók. Itt megtudhatja, hogyan [hozhat létre Dual stack-alkalmazást](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) |2020. április|
| Funkció| A TCP alaphelyzetbe állítása üresjárati időkorláton (általánosan elérhető)| A TCP-visszaállítók használatával kiszámítható alkalmazások viselkedése hozható létre. [További információ](load-balancer-tcp-reset.md)| 2020. február |

## <a name="next-steps"></a>További lépések

További információ a Azure Load Balancerről: [Mi az Azure Load Balancer?](load-balancer-overview.md) és [Gyakori kérdések](load-balancer-faqs.md).
