---
title: Azure Firewall FQDN-szűrés a hálózati szabályokban (előzetes verzió)
description: A Azure Firewall FQDN-szűrés használata a hálózati szabályokban
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/25/2020
ms.author: victorh
ms.openlocfilehash: 1a35d9c48dd46d5d220699589f4ed758d21feca8
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854281"
---
# <a name="use-fqdn-filtering-in-network-rules-preview"></a>FQDN-szűrés használata a hálózati szabályokban (előzetes verzió)

> [!IMPORTANT]
> A hálózati szabályok FQDN-szűrése jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A teljes tartománynév (FQDN) a gazdagép vagy az IP-cím (ek) tartománynevét jelöli. A hálózati szabályokban a Azure Firewall és a tűzfalszabályok DNS-feloldása alapján használhat teljes tartományneveket. Ez a funkció lehetővé teszi a kimenő forgalom szűrését bármely TCP/UDP protokollal (beleértve az NTP, az SSH, az RDP és egyebek). Engedélyeznie kell a DNS-proxyt a teljes tartománynevek használatára a hálózati szabályokban. További információ: [Azure Firewall DNS-beállítások (előzetes verzió)](dns-settings.md).

> [!NOTE]
> A kiépítés alapján a teljes tartománynevek szűrése nem támogatja a helyettesítő karaktereket.

## <a name="how-it-works"></a>Működés

Miután meghatározta, hogy a szervezet melyik DNS-kiszolgálóval rendelkezik (Azure DNS vagy a saját egyéni DNS), a Azure Firewall a kiválasztott DNS-kiszolgáló alapján lefordítja a teljes tartománynevet egy IP-cím (ek) ra. Ez a fordítás az alkalmazás-és a hálózati szabályok feldolgozására is vonatkozik.

Mi a különbség a tartománynevek alkalmazási szabályokban való használata között a hálózati szabályokhoz képest? 

- Az alkalmazási szabályokban a HTTP/S és az MSSQL esetében a teljes tartománynév-szűrés az alkalmazás szintű transzparens proxyn és a SNI fejlécen alapul. Így a két, azonos IP-címhez feloldott FQDN között képes érzékelni. Nem ez a helyzet a teljes tartománynév-szűréssel a hálózati szabályokban. Mindig használja az alkalmazás szabályait, ha lehetséges.
- Az alkalmazási szabályokban HTTP/S és MSSQL protokollt használhat a kiválasztott protokollokként. A hálózati szabályok területen bármilyen TCP/UDP protokollt használhat a célként megadott teljes tartománynévvel.

## <a name="next-steps"></a>További lépések

[Azure Firewall DNS-beállítások](dns-settings.md)
