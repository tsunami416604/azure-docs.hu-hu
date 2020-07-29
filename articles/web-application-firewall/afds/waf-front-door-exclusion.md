---
title: Webalkalmazási tűzfal kizárási listája az Azure bejárati ajtóban – Azure Portal
description: Ez a cikk információt nyújt a kizárási listáról az Azure-ban a Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/25/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 6ed382e88700e4ecd7f8de20a2c8da7ed3c13566
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77925929"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Webalkalmazási tűzfal (WAF) a bejárati ajtó szolgáltatás kizárási listájával 

Előfordulhat, hogy a webalkalmazási tűzfal (WAF) blokkolni kívánja az alkalmazás számára engedélyezni kívánt kérelmet. A Active Directory például beszúrja a hitelesítéshez használt jogkivonatokat. Ezek a tokenek olyan speciális karaktereket tartalmazhatnak, amelyek a WAF-szabályokból hamis pozitív riasztást indíthatnak. A WAF kizárási listája lehetővé teszi bizonyos WAF kiértékelését.  A kizárási lista a [PowserShell](https://docs.microsoft.com/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0), az [Azure CLI](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add), a [REST API](https://docs.microsoft.com/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)vagy a Azure Portal használatával konfigurálható. A következő példa a Azure Portal konfigurációját mutatja be. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Kizárási listák konfigurálása a Azure Portal használatával
A **kizárások kezelése** a WAF-portálról a **felügyelt szabályok** területen érhető el

![Kizárások ](../media/waf-front-door-exclusion/exclusion1.png)
 ![ kezelése exclusion_add kezelése](../media/waf-front-door-exclusion/exclusion2.png)

 Példa kizárási listára: ![ exclusion_define kezelése](../media/waf-front-door-exclusion/exclusion3.png)

Ez a példa a *felhasználói* fejléc mezőben lévő értéket zárja ki. Egy érvényes kérelem tartalmazhatja az SQL-injektálási szabályt kiváltó karakterláncot tartalmazó *felhasználói* mezőt is. Ebben az esetben kizárhatja a *felhasználói* paramétereket, így a WAF-szabály nem értékel semmit a mezőben.

A következő attribútumok adhatók hozzá a kizárási listához név szerint. A rendszer nem értékeli ki a használt mezők értékeit a WAF-szabályok alapján, de a nevük kiértékelése megtörténik. A kizárási listán el kell távolítani a mező értékének ellenőrzését.

* Kérelem fejlécének neve
* Kérelem cookie-neve
* Lekérdezési karakterlánc-argumentumok neve
* Kérelem törzse post argumentumok neve

Megadhat egy pontos kérelem fejlécét, törzsét, cookie-t vagy lekérdezési karakterlánc-attribútumát.  Másik lehetőségként megadhatja a részleges egyezéseket is. A támogatott egyeztetési feltételek a következő operátorok:

- **Egyenlő**: ez az operátor pontos egyezést használ. Például egy **bearerToken**nevű fejléc kiválasztásához használja az Equals operátort a választó **bearerToken**.
- A következővel **kezdődik**: ez az operátor megegyezik a megadott választó értékkel kezdődő összes mezővel.
- **Végződik**: ez az operátor a megadott választó értékkel végződő összes kérelem mezőre illeszkedik.
- **Tartalmazza**: ez az operátor megfelel az összes olyan kérelem mezőnek, amely tartalmazza a megadott választó értéket.
- **Egyenlő**: ez az operátor megfelel az összes kérelem mezőnek. * a választó értéke.

A fejléc és a cookie neve nem megkülönbözteti a kis-és nagybetűket.

Kizárási listát alkalmazhat a felügyelt szabálykészlet összes szabályára, egy adott szabálykészlet szabályaira vagy egyetlen, az előző példában látható szabályra. 

## <a name="next-steps"></a>További lépések

A WAF beállításainak konfigurálása után megtudhatja, hogyan tekintheti meg a WAF-naplókat. További információ: a [bejárati ajtó diagnosztikája](../afds/waf-front-door-monitor.md).
