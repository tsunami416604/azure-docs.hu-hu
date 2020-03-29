---
title: Webalkalmazás-tűzfal kizárási listák az Azure Bejárati ajtajában – Azure portal
description: Ez a cikk az Azure Front on Azure Portal kizárási listák konfigurációjával kapcsolatos információkat tartalmaz.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/25/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 6ed382e88700e4ecd7f8de20a2c8da7ed3c13566
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925929"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Webalkalmazás-tűzfal (WAF) a Bejárati ajtó szolgáltatás kizárási listáival 

Előfordulhat, hogy a webalkalmazás-tűzfal (WAF) blokkolja az alkalmazás engedélyezésére vonatkozó kéréseket. Az Active Directory például beilleszti a hitelesítéshez használt tokeneket. Ezek a tokenek tartalmazhatnak speciális karaktereket, amelyek hamis pozitív eredményt válthatnak ki a WAF-szabályokból. A WAF kizárási listák lehetővé teszik bizonyos kérésattribútumok kihagyását a WAF-kiértékelésből.  A kizárási lista konfigurálható [a PowserShell,](https://docs.microsoft.com/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0) [az Azure CLI](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add), [a Rest API](https://docs.microsoft.com/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)vagy az Azure Portal használatával. A következő példa az Azure Portal konfigurációját mutatja be. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Kizárási listák konfigurálása az Azure Portal használatával
**A kizárások kezelése** a WAF portálról érhető el **a Felügyelt szabályok** alatt

![Kizárás](../media/waf-front-door-exclusion/exclusion1.png)
![kezelése exclusion_add kezelése](../media/waf-front-door-exclusion/exclusion2.png)

 Példa kizárási ![listára: Exclusion_define kezelése](../media/waf-front-door-exclusion/exclusion3.png)

Ez a példa kizárja a *felhasználói* fejlécmező értékét. Az érvényes kérelem tartalmazhatja azt a *felhasználói* mezőt, amely egy SQL-injektálási szabályt kiváltó karakterláncot tartalmaz. Ebben az esetben kizárhatja a *felhasználói* paramétert, hogy a WAF-szabály ne számítson ki semmit a mezőben.

A kizárási listákhoz név szerint a következő attribútumok adhatók hozzá. A használt mezők értékeit a rendszer nem a WAF-szabályok alapján értékeli ki, de a nevüket kiértékeli. A kizárási listák megszüntetik a terület értékének ellenőrzését.

* Kérelemfejléc neve
* Cookie-név kérése
* Lekérdezési karakterlánc args neve
* Törzsbejegyzés-bejegyzés e-oszlop nevének kérése

Megadhatja a pontos kérelem fejlécét, törzsét, cookie-ját vagy lekérdezési karakterlánc-attribútumegyezést.  Vagy megadhatja a részleges egyezéseket is. A következő operátorok a támogatott egyezési feltételek:

- **Egyenlő :** Ez az operátor pontos egyezéshez használatos. Például egy **bearerToken**nevű fejléc kiválasztásához használja az egyenlő operátort, a választókészlettel **pedig bearerToken.**
- **A következővel kezdődik:** Ez az operátor a megadott választóértékkel kezdődő összes mezőt egyezteti.
- **Végződés:** Ez az operátor a megadott választóértékkel végződő összes kérelemmezőnek megfelel.
- **A következőket tartalmazza:** Ez az operátor megfelel a megadott választóértéket tartalmazó összes kérelemmezőnek.
- **Egyenlő bármely**: Ez az operátor megfelel az összes kérelem mezőnek. * a választó érték.

A fejléc- és cookie-nevek nem tartalmaznak kis- és nagybetűket.

A kizárási listát alkalmazhatja a kezelt szabálykészlet összes szabályára, egy adott szabálycsoport szabályaira vagy egyetlen szabályra az előző példában látható módon. 

## <a name="next-steps"></a>További lépések

A WAF-beállítások konfigurálása után olvassa el a WAF-naplók megtekintésének módját. További információ: [Front Door diagnostics](../afds/waf-front-door-monitor.md).
