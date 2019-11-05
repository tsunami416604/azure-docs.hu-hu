---
title: Az Azure Firewall szabályfeldolgozási logikája
description: Tudnivalók a Azure Firewall szabályok feldolgozásának logikáról
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 74e58c316651a1604984ac14c70a3a65d46d6d9f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518202"
---
# <a name="azure-firewall-rule-processing-logic"></a>Az Azure Firewall szabályfeldolgozási logikája

Azure Firewall rendelkezik NAT-szabályokkal, hálózati szabályokkal és alkalmazási szabályokkal. A szabályok feldolgozása a szabály típusa szerint történik.

## <a name="network-rules-and-applications-rules"></a>Hálózati szabályok és alkalmazások szabályai

Először alkalmazza a hálózati szabályokat, majd az alkalmazás szabályait. A szabályok leállnak. Tehát ha a hálózati szabályok egyezést találnak, akkor a rendszer nem dolgozza fel az alkalmazás szabályait.  Ha egyik hálózati szabály sem érvényes, és ha a csomag protokollja HTTP/HTTPS, a csomag ezután az alkalmazásszabályok szerint is ki lesz értékelve. Ha még mindig nem található egyezés, akkor a rendszer kiértékeli a csomagot az infrastruktúra-szabálygyűjtemény alapján. És ha továbbra sincs egyezés, a tűzfal a csomagot alapértelmezés szerint elutasítja.

## <a name="nat-rules"></a>NAT-szabályok

A bejövő kapcsolat engedélyezhető a célként megadott hálózati címfordítás (DNAT) konfigurálásával az [oktatóanyag: a bejövő forgalom szűrése Azure Firewall DNAT a Azure Portal használatával](../firewall/tutorial-firewall-dnat.md). A rendszer először alkalmazza a DNAT-szabályokat. Ha talál egyezést, egy implicit megfelelő hálózati szabályt ad hozzá a lefordított forgalom engedélyezéséhez. Ezt a viselkedést felülírhatja, ha explicit módon hozzáad egy hálózatiszabály-készletet, amely megtagadja azokat a szabályokat, amelyek a lefordított adatforgalomhoz tartoznak. Ezekhez a kapcsolatokhoz nem alkalmazhatók alkalmazási szabályok.

## <a name="inherited-rules"></a>Örökölt szabályok

A szülő házirendből örökölt hálózati szabályok gyűjteményeit mindig rangsorolja a rendszer az új szabályzat részeként definiált hálózati szabályok gyűjteményei felett. Ugyanez a logika vonatkozik az alkalmazási szabályok gyűjteményére is. A hálózati szabályok gyűjteményeit azonban a rendszer mindig feldolgozza az alkalmazási szabályok gyűjtése előtt, az örökléstől függetlenül.

Alapértelmezés szerint a házirend örökli a szülő házirend fenyegetésének intelligencia üzemmódját. Ezt felülbírálhatja úgy, hogy a fenyegetési intelligencia módot egy másik értékre állítja be a házirend-beállítások lapon. Csak szigorúbb értékkel lehet felülbírálni. Ha például a szülői házirend *csak riasztásra*van beállítva, a helyi házirendet beállíthatja *riasztásra és megtagadásre*, de nem kapcsolhatja ki.

## <a name="next-steps"></a>További lépések

- [További információ a Azure Firewall Manager előzetes verziójáról](overview.md)