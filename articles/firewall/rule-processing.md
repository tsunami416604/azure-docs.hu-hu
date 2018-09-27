---
title: Az Azure tűzfal szabály feldolgozási logika
description: További információ a feldolgozó logika Azure Firewall-rule
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/27/2018
ms.author: victorh
ms.openlocfilehash: 542682037a932a2e3b08c71b38b64b2694ad40f3
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228370"
---
# <a name="azure-firewall-rule-processing-logic"></a>Az Azure tűzfal szabály feldolgozási logika
Azure tűzfal NAT szabályokat, a hálózati szabályok és alkalmazások szabályokat tartalmaz. A szabályok feldolgozása a szabály típusa szerint történik.


## <a name="network-rules-and-applications-rules"></a>Hálózati és az alkalmazások szabályok 
A hálózati szabályok alkalmazott első, majd az alkalmazás szabályok vonatkoznak. A szabályok is leáll. Tehát ha van egyezés található a hálózati szabályok, majd alkalmazás szabályok feldolgozása nem.  Ha nem egyezik meg az hálózati szabályt, és ha a csomag protokollja HTTP/HTTPS, a csomagot Ezután kiértékeli az alkalmazás szabályok. Ha még mindig nem talál egyezést, majd az infrastruktúra-szabályok gyűjteményéhez képest értékeli ki a csomagot. Ha még nem szerepel, akkor a csomagot a rendszer megtagadja alapértelmezés szerint.

## <a name="nat-rules"></a>NAT-szabályok
Bejövő kapcsolatok konfigurálása a cél hálózati cím fordítása (DNAT) leírtak szerint engedélyezhető [oktatóanyag: az Azure portal segítségével Azure tűzfal DNAT bejövő forgalom szűrése](tutorial-firewall-dnat.md). DNAT szabályokat alkalmazza a rendszer. Ha van egyezés, egy implicit megfelelő hálózati forgalmat engedélyező szabállyal a lefordított egészül ki. Megtagadási szabályokkal, amelyek megfelelnek a lefordított forgalmat egy hálózati szabálygyűjtemény explicit módon hozzáadásával felül lehet bírálni ezt a viselkedést. Nincs alkalmazás szabályokat akkor alkalmazza, ezek a kapcsolatok számára.


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [üzembe helyezése és konfigurálása az Azure-tűzfalak](tutorial-firewall-deploy-portal.md).