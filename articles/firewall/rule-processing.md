---
title: Az Azure Firewall szabályfeldolgozási logikája
description: Azure Firewall rendelkezik NAT-szabályokkal, hálózati szabályokkal és alkalmazási szabályokkal. A szabályok feldolgozása a szabály típusa szerint történik.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2018
ms.author: victorh
ms.openlocfilehash: 0fc11221e0ff79d6e17b93282403792fc135c2a4
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166787"
---
# <a name="azure-firewall-rule-processing-logic"></a>Az Azure Firewall szabályfeldolgozási logikája
Azure Firewall rendelkezik NAT-szabályokkal, hálózati szabályokkal és alkalmazási szabályokkal. A szabályok feldolgozása a szabály típusa szerint történik.


## <a name="network-rules-and-applications-rules"></a>Hálózati szabályok és alkalmazások szabályai 
Először alkalmazza a hálózati szabályokat, majd az alkalmazás szabályait. A szabályok leállnak. Tehát ha a hálózati szabályok egyezést találnak, akkor a rendszer nem dolgozza fel az alkalmazás szabályait.  Ha egyik hálózati szabály sem érvényes, és ha a csomag protokollja HTTP/HTTPS, a csomag ezután az alkalmazásszabályok szerint is ki lesz értékelve. Ha még mindig nem található egyezés, akkor a rendszer kiértékeli a csomagot az infrastruktúra-szabálygyűjtemény alapján. És ha továbbra sincs egyezés, a tűzfal a csomagot alapértelmezés szerint elutasítja.

## <a name="nat-rules"></a>NAT-szabályok
A bejövő kapcsolat engedélyezhető a célként megadott hálózati címfordítás (DNAT) konfigurálásával az [oktatóanyag: a bejövő forgalom szűrése Azure Firewall DNAT a Azure Portal használatával](tutorial-firewall-dnat.md). A rendszer először alkalmazza a DNAT-szabályokat. Ha talál egyezést, egy implicit megfelelő hálózati szabályt ad hozzá a lefordított forgalom engedélyezéséhez. Ezt a viselkedést felülírhatja, ha explicit módon hozzáad egy hálózatiszabály-készletet, amely megtagadja azokat a szabályokat, amelyek a lefordított adatforgalomhoz tartoznak. Ezekhez a kapcsolatokhoz nem alkalmazhatók alkalmazási szabályok.


## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [helyezhet üzembe és konfigurálhat egy Azure Firewall](tutorial-firewall-deploy-portal.md).