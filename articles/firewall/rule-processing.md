---
title: Az Azure tűzfal szabály feldolgozási logika
description: További információ a feldolgozó logika Azure Firewall-rule
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/27/2018
ms.author: victorh
ms.openlocfilehash: 12d86793c0d75413559aad77c558c4adb7ac91af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64681590"
---
# <a name="azure-firewall-rule-processing-logic"></a>Az Azure tűzfal szabály feldolgozási logika
Azure tűzfal NAT szabályokat, a hálózati szabályok és alkalmazások szabályokat tartalmaz. A szabályok feldolgozása a szabály típusa szerint történik.


## <a name="network-rules-and-applications-rules"></a>Hálózati és az alkalmazások szabályok 
A hálózati szabályok alkalmazott első, majd az alkalmazás szabályok vonatkoznak. A szabályok is leáll. Tehát ha van egyezés található a hálózati szabályok, majd alkalmazás szabályok feldolgozása nem.  Ha egyik hálózati szabály sem érvényes, és ha a csomag protokollja HTTP/HTTPS, a csomag ezután az alkalmazásszabályok szerint is ki lesz értékelve. Ha még mindig nem talál egyezést, majd az infrastruktúra-szabályok gyűjteményéhez képest értékeli ki a csomagot. És ha továbbra sincs egyezés, a tűzfal a csomagot alapértelmezés szerint elutasítja.

## <a name="nat-rules"></a>NAT-szabályok
Bejövő kapcsolatok konfigurálása a cél hálózati cím fordítása (DNAT) leírtak szerint engedélyezhető [oktatóanyag: Bejövő forgalom szűrése az Azure portal segítségével Azure tűzfal DNAT](tutorial-firewall-dnat.md). DNAT szabályokat alkalmazza a rendszer. Ha van egyezés, egy implicit megfelelő hálózati forgalmat engedélyező szabállyal a lefordított egészül ki. Ezt a viselkedést felülírhatja, ha explicit módon hozzáad egy hálózatiszabály-készletet, amely megtagadja azokat a szabályokat, amelyek a lefordított adatforgalomhoz tartoznak. Nincs alkalmazás szabályokat akkor alkalmazza, ezek a kapcsolatok számára.


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [üzembe helyezése és konfigurálása az Azure-tűzfalak](tutorial-firewall-deploy-portal.md).