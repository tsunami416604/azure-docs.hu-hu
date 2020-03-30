---
title: Szabályzatok az Azure API Managementben | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre, szerkesztthet és konfigurálhat házirendeket az API Management ben.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/29/2017
ms.author: apimpm
ms.openlocfilehash: c10939b50a66cd608d27a71f02d959fbc2380f59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70072306"
---
# <a name="policies-in-azure-api-management"></a>Az Azure API Management szabályzatai

Az Azure API Management (APIM) szabályzatok a rendszer hatékony képessége, amely lehetővé teszi a közzétevő számára az API működését a konfiguráción keresztül. A szabályzatok olyan utasítások gyűjteményei, amelyek egy API kérésére vagy válaszára egymás után kerülnek végrehajtásra. A népszerű kivonatok közé tartozik az XML-ről JSON-ra történő formátumátalakítás és a hívási sebesség korlátozása a fejlesztőtől érkező bejövő hívások számának korlátozása érdekében. Sokkal több szabályzat érhető el a dobozból.

A szabályzatok az átjárón belül kerülnek alkalmazásra, amely az API-fogyasztó és a felügyelt API között helyezkedik el. Az átjáró fogadja az összes kérelmet, és általában továbbítja azokat változatlanul az alapul szolgáló API-t. A házirend azonban módosításokat alkalmazhat mind a bejövő kérelemre, mind a kimenő válaszra.

A házirend-kifejezéseket attribútumértékekként vagy szövegértékekként lehet használni bármelyik API Management házirendben, hacsak a házirend másként nem rendelkezik. Néhány házirend, például a [Vezérlés folyamata][Control flow] és a [Változó beállítása][Set variable] házirend-kifejezéseken alapul. További információ: [Speciális szabályzatok][Advanced policies] és [Szabályzatkifejezések][Policy expressions].

## <a name="understanding-policy-configuration"></a><a name="sections"> </a>A házirend-konfiguráció ismertetése

A házirend-definíció egy egyszerű XML-dokumentum, amely a bejövő és kimenő utasítások sorozatát írja le. Az XML közvetlenül a definíciós ablakban szerkeszthető. A jobb oldali utasítások listája, valamint az aktuális hatókörre vonatkozó utasítások engedélyezése és kiemelése látható.

Ha egy engedélyezett utasításra kattint, a megfelelő XML-t a definíciós nézetben a kurzor helyén adja hozzá. 

> [!NOTE]
> Ha a hozzáadni kívánt házirend nincs engedélyezve, győződjön meg arról, hogy a házirend megfelelő hatókörében van. Minden házirend-utasítás bizonyos hatókörökben és házirendszakaszokban való használatra készült. A házirendházirend-szakaszok és -hatókörök áttekintéséhez tekintse meg az adott házirend **használati** szakaszát a [Házirend-hivatkozás ban.][Policy Reference]
> 
> 

`inbound`A konfiguráció a `backend`, `outbound`, `on-error`, és . A megadott házirendutasítások sorozata egy kérés és válasz érdekében végrehajtásra kerül.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is forwarded to 
         the backend service go here -->
  </backend>
  <outbound>
    <!-- statements to be applied to the response go here -->
  </outbound>
  <on-error>
    <!-- statements to be applied if there is an error condition go here -->
  </on-error>
</policies> 
```

Ha hiba történik a kérelem feldolgozása során, a `inbound` `backend`program `outbound` kihagyja a , vagy szakaszok ban fennmaradó lépéseket, és a végrehajtás a `on-error` szakaszban lévő utasításokra ugrik. Ha a házirend-kivonatokat a `on-error` szakaszba helyezi, a tulajdonság használatával áttekintheti a `context.LastError` hibát, a házirend segítségével megvizsgálhatja és testreszabhatja a `set-body` hibaválaszt, és beállíthatja, hogy mi történjen hiba esetén. Vannak hibakódok a beépített lépésekhez és a házirend-utasítások feldolgozása során előforduló hibákhoz. További információt a [Hibakezelés az API-kezelési házirendekben című témakörben talál.](/azure/api-management/api-management-error-handling-policies)

## <a name="how-to-configure-policies"></a><a name="scopes"> </a>Házirendek konfigurálása

A házirendek konfigurálásáról a [Házirendek beállítása és szerkesztése](set-edit-policies.md)című témakörben talál további információt.

## <a name="policy-reference"></a>Házirend hivatkozása

A házirend-utasítások és azok beállításainak teljes listáját a [Házirend-útmutatóban](api-management-policy-reference.md) találja.

## <a name="policy-samples"></a>Házirendminták

További kódpéldákért tekintse meg [a házirendmintákat.](policy-samples.md)

## <a name="examples"></a>Példák

### <a name="apply-policies-specified-at-different-scopes"></a>Különböző hatókörökben megadott házirendek alkalmazása

Ha globális szintű szabályzattal és API-hoz konfigurált házirenddel rendelkezik, akkor minden alkalommal, amikor az adott API-t használja, mindkét szabályzat lesz alkalmazva. Az API Management lehetővé teszi a kombinált házirendutasítások determinisztikus rendezését az alapelemen keresztül. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

A fenti példaházirend-definícióban az utasítás minden `cross-domain` magasabb házirend előtt végrehajtaná, amelyet a `find-and-replace` házirend követne. 

### <a name="restrict-incoming-requests"></a>Bejövő kérelmek korlátozása

Ha új utasítást szeretne hozzáadni a bejövő kérelmek megadott IP-címekre való `inbound` korlátozására, helyezze a kurzort az XML-elem tartalmába, és kattintson a **Hívó IP-címek korlátozása** utasításra.

![Korlátozási házirendek][policies-restrict]

Ez egy XML-kódrészletet `inbound` ad hozzá az elemhez, amely útmutatást ad az utasítás konfigurálásához.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Ha korlátozni szeretné a bejövő kérelmeket, és csak az 1.2.3.4-es IP-címről érkezőknek a kívánt adatokat szeretné elfogadni, módosítsa az XML-t az alábbiak szerint:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>További lépések

A házirendekkel kapcsolatos további információkért lásd:

+ [API-k átalakítása](transform-api.md)
+ [Házirend-útmutató](api-management-policy-reference.md) a házirend-utasítások és beállításaik teljes listájához
+ [Házirendminták](policy-samples.md)   

[Policy Reference]: api-management-policy-reference.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: api-management-howto-add-operations.md

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
