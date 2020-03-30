---
title: Webalkalmazás-tűzfal-kérelmek méretkorlátai és kizárási listái az Azure Application Gateway alkalmazásban – Azure portal
description: Ez a cikk a webalkalmazás-tűzfal kérelem méretkorlátairól és kizárási listák konfigurációjáról tartalmaz információt az Azure Portalalkalmazás-átjáróban.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 7244788bbc7431c7f26363b2852babb72d5697e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526790"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Webalkalmazás-tűzfal kérelemméret-korlátés kizárási listák

Az Azure Application Gateway webalkalmazás-tűzfal (WAF) védelmet nyújt a webalkalmazások számára. Ez a cikk a WAF-kérelmek méretkorlátait és a kizárási listák konfigurációját ismerteti. Ezek a beállítások az alkalmazásátjáróhoz társított WAF-szabályzatban találhatók. Ha többet szeretne megtudni a WAF-szabályzatokról, olvassa el [az Azure Web Application Firewall on Azure Application Gateway](ag-overview.md) és a Create Web Application Firewall [házirendek létrehozása az alkalmazásátjáróhoz című témakört.](create-waf-policy-ag.md)

## <a name="waf-exclusion-lists"></a>WAF kizárási listák

![Kérelem méretkorlátai](../media/application-gateway-waf-configuration/waf-policy.png)

A WAF kizárási listák lehetővé teszik bizonyos kérésattribútumok kihagyását a WAF-kiértékelésből. Gyakori példa erre a hitelesítéshez vagy jelszómezőkhöz használt Active Directory beszúrt tokenek. Ezek az attribútumok hajlamosak olyan speciális karaktereket, amelyek hamis pozitív eredményt válthatnak ki a WAF-szabályokból. Miután egy attribútumot hozzáadtak a WAF-kizárási listához, azt egyetlen konfigurált és aktív WAF-szabály sem veszi figyelembe. A kizárási listák hatóköre globális.

A kizárási listákhoz név szerint a következő attribútumok adhatók hozzá. A kiválasztott mező értékeit a rendszer nem értékeli ki a WAF-szabályok alapján, de a nevük még mindig fennáll (lásd az alábbi 1. példát, a User-Agent fejléc értéke ki van zárva a WAF-kiértékelésből). A kizárási listák megszüntetik a terület értékének ellenőrzését.

* Kérelemfejlécek
* Cookie-k kérése
* A kérelem attribútumneve (args) kizárási elemként adható meg, például:

   * Űrlapmező neve
   * XML-entitás
   * JSON entitás
   * URL-lekérdezési karakterlánc-argok

Megadhatja a pontos kérelem fejlécét, törzsét, cookie-ját vagy lekérdezési karakterlánc-attribútumegyezést.  Vagy megadhatja a részleges egyezéseket is. A kizárási szabályok globális hatályúak, és minden oldalra és szabályra vonatkoznak.

A támogatott egyezési feltételek operátorai a következők:

- **Egyenlő :** Ez az operátor pontos egyezéshez használatos. Például a **bearerToken**nevű fejléc kiválasztásához használja az egyenlő operátort, a választókészlettel **pedig a bearerToken.**
- **A következővel kezdődik:** Ez az operátor a megadott választóértékkel kezdődő összes mezőt egyezteti.
- **Végződés:** Ez az operátor a megadott választóértékkel végződő összes kérelemmezőnek megfelel.
- **A következőket tartalmazza:** Ez az operátor megfelel a megadott választóértéket tartalmazó összes kérelemmezőnek.
- **Egyenlő bármely**: Ez az operátor megfelel az összes kérelem mezőnek. * lesz a választó értéke.

Minden esetben az egyeztetés nem érzékeny a kis- és nagybetűk megkülönböztetésére, és a reguláris kifejezés nem engedélyezett választóként.

> [!NOTE]
> További információt és hibaelhárítási segítséget a [WAF hibaelhárítása](web-application-firewall-troubleshoot.md)című témakörben talál.

### <a name="examples"></a>Példák

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az alábbi példák a kizárások alkalmazását mutatják be.

#### <a name="example-1"></a>1. példa

Ebben a példában ki szeretné zárni a user-agent fejlécet. A felhasználói ügynök kérelemfejléce tartalmaz egy jellemző karakterláncot, amely lehetővé teszi, hogy a hálózati protokoll társai azonosítsák a kérelmező szoftverfelhasználói ügynök alkalmazástípusát, operációs rendszerét, szoftvergyártóját vagy szoftververzióját. További információ: [User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent).

A fejléc kiértékelésének letiltása számos oka lehet. Lehet, hogy egy karakterlánc, hogy a WAF látja, és feltételezi, hogy rosszindulatú. Például a klasszikus SQL támadás "x=x" egy karakterláncban. Bizonyos esetekben ez lehet a jogszerű forgalom. Ezért előfordulhat, hogy ki kell zárnia ezt a fejlécet a WAF-kiértékelésből.

A következő Azure PowerShell-parancsmag kizárja a felhasználói ügynök fejlécét a kiértékelésből:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```
#### <a name="example-2"></a>2. példa

Ez a példa kizárja a *felhasználói* paraméterben szereplő értéket, amely az URL-címen keresztül a kérelemben átkerül. Tegyük fel például, hogy a felhasználói mező ben gyakori, hogy a WAF által rosszindulatú tartalomként megtekintett karakterláncot tartalmaz, ezért blokkolja azt.  Ebben az esetben kizárhatja a felhasználói paramétert, hogy a WAF ne értékeljen ki semmit a mezőben.

A következő Azure PowerShell-parancsmag kizárja a felhasználói paramétert a kiértékelésből:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "StartsWith" `
   -Selector "user"
```
Tehát, ha `http://www.contoso.com/?user%281%29=fdafdasfda` az URL-t átadják a WAF, akkor nem értékeli a string **fdafdasfda**, de továbbra is értékeli a paraméter nevét **user%281%29**. 

## <a name="waf-request-size-limits"></a>WAF-kérelem méretkorlátai



A WebApplication Firewall lehetővé teszi a kérelmek méretkorlátainak konfigurálását az alsó és felső határokon belül. A következő két méretkorlát-konfiguráció áll rendelkezésre:

- A kérelem törzsének maximális mérete mező kilobájtban van megadva, és a fájlfeltöltések kivételével szabályozza a kérelem teljes méretkorlátját. Ez a mező legalább 1 KB és 128 KB maximális érték között lehet. A kérelemtörzs alapértelmezett értéke 128 KB.
- A fájlfeltöltési korlát mező MB-ban van megadva, és a maximálisan megengedett fájlfeltöltési méretet szabályozza. Ez a mező minimális értéke 1 MB és a következő maximumok lehetnek:

   - 100 MB a v1 közepes WAF átjárók hoz
   - 500 MB a v1 nagy WAF átjárók hoz
   - 750 MB a v2 WAF átjárókhoz 

 A fájlfeltöltési korlát alapértelmezett értéke 100 MB.

A WAF egy konfigurálható gombot is kínál a kéréskaros vizsgálat be- vagy kikapcsolására. Alapértelmezés szerint a kérelem törzs ellenőrzése engedélyezve van. Ha a kérelem törzs ellenőrzése ki van kapcsolva, waf nem értékeli ki a HTTP-üzenet törzse tartalmát. Ilyen esetekben a WAF továbbra is kényszeríti a WAF-szabályokat a fejléceken, a cookie-kon és az URI-kon. Ha a kérelem törzs ellenőrzése ki van kapcsolva, majd a kérelem törzsméret mező nem alkalmazható, és nem állítható be. A kérelemtörzs ellenőrzésének kikapcsolása lehetővé teszi a 128 KB-nál nagyobb üzenetek WAF-nak történő küldését, de az üzenettörzset nem ellenőrzi a biztonsági rések szempontjából.

## <a name="next-steps"></a>További lépések

A WAF-beállítások konfigurálása után megtudhatja, hogyan tekintheti meg a WAF-naplókat. További információt az [Application Gateway diagnosztikája című témakörben talál.](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)
