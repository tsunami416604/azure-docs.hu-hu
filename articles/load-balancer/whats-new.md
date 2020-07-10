---
title: A Azure Load Balancer újdonságai
description: Ismerje meg a Azure Load Balancer újdonságait, például a legújabb kibocsátási megjegyzéseket, ismert problémákat, hibajavításokat, elavult funkciókat és a közelgő változásokat.
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: overview
ms.date: 07/07/2020
ms.author: anavin
ms.openlocfilehash: 4725aaaf7a2c1e0aa49ea8dbe046b720727a5d54
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86148050"
---
# <a name="whats-new-in-azure-load-balancer"></a>A Azure Load Balancer újdonságai

A Azure Load Balancer rendszeresen frissül. Maradjon naprakész a legújabb bejelentésekkel. Ez a cikk a következő információkat tartalmazza:

- A legújabb kiadások
- Ismert problémák
- Hibajavítások
- Elavult funkció (ha van ilyen)

Itt megtalálhatja a legújabb Azure Load Balancer frissítéseket, és feliratkozhat [ide](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer)az RSS-hírcsatornára.

## <a name="new-features"></a>Új funkciók

|Szolgáltatás  |Leírás  |Hozzáadás dátuma  |
|---------|---------|---------|
| Az IP-alapú háttér-készlet felügyeletének támogatása (előzetes verzió) | Azure Load Balancer támogatja az erőforrások hozzáadását és eltávolítását egy háttérbeli készletből IPv4-vagy IPv6-címek használatával. Ez lehetővé teszi a tárolók, a virtuális gépek és a Load Balancerhoz társított virtuálisgép-méretezési csoportok egyszerű kezelését. Azt is lehetővé teszi, hogy az IP-címek a háttérbeli készlet részeként legyenek fenntartva a társított erőforrások létrehozása előtt. | 2020. július |
| A Azure Monitor használatával Azure Load Balancer a bepillantást | A hálózatok Azure Monitorának részeként az ügyfelek mostantól rendelkeznek topológiai térképekkel az összes Load Balancer-konfigurációhoz és az állapot irányítópulthoz az Azure Portal metrikákkal előre konfigurált standard Load Balancerekhez. [Első lépések és további információ](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | 2020. június |
| IPv6-támogatás a Azure Load Balancerhoz (általánosan elérhető) | Az Azure Load Balancer előtérben az IPv6-címek is megadhatók. Itt megtudhatja, hogyan [hozhat létre Dual stack-alkalmazást](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) |2020. április|
| A TCP alaphelyzetbe állítása üresjárati időkorláton (általánosan elérhető)| A TCP-visszaállítók használatával kiszámítható alkalmazások viselkedése hozható létre. [További információ](load-balancer-tcp-reset.md)| 2020. február |

## <a name="next-steps"></a>További lépések

További információ a Azure Load Balancerről: [Mi az Azure Load Balancer?](load-balancer-overview.md) és [Gyakori kérdések](load-balancer-faqs.md).