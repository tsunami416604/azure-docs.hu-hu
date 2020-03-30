---
title: Az Azure Firewall szabályfeldolgozási logikája
description: További információ az Azure Firewall szabályfeldolgozási logikájáról
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 74e58c316651a1604984ac14c70a3a65d46d6d9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73518202"
---
# <a name="azure-firewall-rule-processing-logic"></a>Az Azure Firewall szabályfeldolgozási logikája

Az Azure Firewall rendelkezik NAT-szabályokkal, hálózati szabályokkal és alkalmazásszabályokkal. A szabályok feldolgozása a szabálytípusnak megfelelően.

## <a name="network-rules-and-applications-rules"></a>Hálózati szabályok és alkalmazások szabályai

Először a hálózati szabályokat, majd az alkalmazási szabályokat alkalmazza a rendszer. A szabályok megszűnnek. Tehát ha egyezést talál a hálózati szabályokban, akkor az alkalmazásszabályok feldolgozása nem történik meg.  Ha egyik hálózati szabály sem érvényes, és ha a csomag protokollja HTTP/HTTPS, a csomag ezután az alkalmazásszabályok szerint is ki lesz értékelve. Ha még mindig nem talál egyezést, akkor a csomag kiértékelése az infrastruktúraszabály-gyűjtemény alapján történik. És ha továbbra sincs egyezés, a tűzfal a csomagot alapértelmezés szerint elutasítja.

## <a name="nat-rules"></a>NAT-szabályok

A bejövő kapcsolat az oktatóanyagban leírtak szerint a Célhálózati címfordítás (DNAT) konfigurálásával [engedélyezhető: Szűrje a bejövő forgalmat az Azure Firewall DNST szolgáltatással az Azure Portalon](../firewall/tutorial-firewall-dnat.md)keresztül. A DNST-szabályok at alkalmaznak először. Ha egyezést talál, a fordítást engedélyező implicit megfelelő hálózati szabály hozzáadódik. Ezt a viselkedést felülírhatja, ha explicit módon hozzáad egy hálózatiszabály-készletet, amely megtagadja azokat a szabályokat, amelyek a lefordított adatforgalomhoz tartoznak. Ezekre a kapcsolatokra nem vonatkozik alkalmazási szabályok.

## <a name="inherited-rules"></a>Örökölt szabályok

A szülőházirendből örökölt hálózati szabálygyűjtemények mindig elsőbbséget élveznek az új házirend részeként definiált hálózati szabálygyűjtemények felett. Ugyanez a logika vonatkozik az alkalmazásszabály-gyűjteményekre is. A hálózati szabálygyűjtemények feldolgozása azonban mindig az alkalmazásszabály-gyűjtemények előtt van feldolgozva, az öröklődéstől függetlenül.

Alapértelmezés szerint a szabályzat örökli a szülő házirend fenyegetésintelligencia-mód. Ezt felülbírálhatja, ha a fenyegetésfelderítési módot egy másik értékre állítja a házirend-beállítások lapon. Csak szigorúbb értékkel lehet felülbírálni. Ha például a szülőházirend *beállítása csak riasztás,* beállíthatja ezt a helyi házirendet *riasztásra és megtagadásra,* de nem kapcsolhatja ki.

## <a name="next-steps"></a>További lépések

- [További információ az Azure Firewall Manager előzetes verziójáról](overview.md)