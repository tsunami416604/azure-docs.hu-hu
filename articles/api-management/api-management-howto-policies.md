---
title: Az Azure API Management házirendek |} A Microsoft Docs
description: Ismerje meg, hogyan létrehozása, szerkesztése és az API Management házirendek konfigurálása.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: apimpm
ms.openlocfilehash: 99f756b5415811b3d4c2ee0167f98b31c905df1a
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793674"
---
# <a name="policies-in-azure-api-management"></a>Az Azure API Management házirendek

Az Azure API Management (APIM), a szabályzatok olyan egy hatékony képesség, a rendszer, amely lehetővé teszi a közzétevő, az API konfigurálással változtathatja meg. Szabályzatok olyan kérelmei végrehajtott utasítások gyűjteményei, vagy az API-válaszban. A népszerű utasítások közé tartozik a formátumátalakítás XML-ről JSON-ná, és a hívások sebességének korlátozása a fejlesztőktől érkező hívások mennyiségének korlátozásához. Számos további házirendeket beépített érhetők el.

A szabályzatokkal belül az átjáró, amely az API-ügyfél és a felügyelt API között helyezkedik el. Az átjáró összes kérelmet kap, és általában továbbítja őket a mögöttes API változatlan. Egy házirend azonban módosításokat a bejövő kérelem és a kimenő válaszok is alkalmazhatja.

A házirend-kifejezéseket attribútumértékekként vagy szövegértékekként lehet használni bármelyik API Management házirendben, hacsak a házirend másként nem rendelkezik. Néhány házirend, például a [átvitelvezérlés] [ Control flow] és [változó beállítása] [ Set variable] házirend-kifejezések alapul. További információkért lásd: [speciális szabályzatok] [ Advanced policies] és [házirend-kifejezések][Policy expressions].

## <a name="sections"> </a>A szabályzatkonfiguráció ismertetése

A szabályzat-definíció egy egyszerű XML-dokumentum, amely leírja a bejövő és kimenő utasítások sorozata. Az XML-fájl a definíció ablakban közvetlenül szerkesztheti. Utasítások listáját a jobb és az aktuális hatókörben érvényes utasításokat engedélyezve van, és vannak kiemelve.

Egy engedélyezett utasítás kattintva hozzáadja a megfelelő XML a kurzor a definíció nézetben a helyen. 

> [!NOTE]
> Ha a szabályzatot, amely a hozzáadni kívánt nincs engedélyezve, győződjön meg arról, hogy az adott házirendnek megfelelő hatókörben vannak-e. Minden egyes házirendutasítás az egyes hatókörök és a házirend-szakaszok használatra készült. Tekintse át a szabályzat szakaszok és a egy házirend hatókörök, ellenőrizze a **használati** , hogy a házirend a következő szakaszban a [házirend-referencia][Policy Reference].
> 
> 

A konfigurációs van felosztva `inbound`, `backend`, `outbound`, és `on-error`. A megadott házirend-utasítások, amely a kérelem és válasz sorrendben hajtja végre.

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

Kérelem feldolgozása során hiba történik, ha minden fennmaradó lépéseit a `inbound`, `backend`, vagy `outbound` szakaszok kimarad, és végrehajtási parancsfájlblokkokban lévő utasítások ugrik a `on-error` szakaszban. Úgy, hogy a házirend-utasítások a `on-error` szakaszt áttekintheti a hiba használatával a `context.LastError` tulajdonság, nézze meg, és testre szabhatja a hiba válasz használatával a `set-body` szabályzatot, és konfigurálhatja, mi történik, ha hiba lép fel. Nincsenek hibakódok beépített lépéseket és a házirend-utasítások feldolgozása során előforduló hibákat. További információkért lásd: [hibakezelés az API Management házirendek](/azure/api-management/api-management-error-handling-policies).

## <a name="scopes"> </a>Szabályzatok konfigurálása

Házirendek konfigurálásáról további információért lásd: [állítsa be, vagy szerkesztheti a szabályzatokat](set-edit-policies.md).

## <a name="policy-reference"></a>Házirend-referencia

Tekintse meg a [házirend-referencia](api-management-policy-reference.md) házirend-utasítások és a beállítások teljes listáját.

## <a name="policy-samples"></a>A házirend-minták

Lásd: [házirend minták](policy-samples.md) kód további példákat.

## <a name="examples"></a>Példák

### <a name="apply-policies-specified-at-different-scopes"></a>A megadott különféle hatókörökben szabályzatok alkalmazása

Ha egy szabályzatot a globális szinten és a egy olyan API-hoz konfigurált szabályzatot, majd, amikor az adott API használható mindkét házirend lépnek érvénybe. Az API Management lehetővé teszi, hogy determinisztikus rendezéshez egyesített szabályzat utasítások segítségével a alapszintű elemet. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

A fenti példa szabályzatdefinícióban a `cross-domain` utasítás futtatása előtt követi, amely viszont magasabb szabályzatok a `find-and-replace` házirend. 

### <a name="restrict-incoming-requests"></a>Korlátozza a bejövő kérések

Adjon hozzá egy új utasítást a bejövő kérések korlátozhatja a megadott IP-címek, helyezze a kurzort a tartalmát csak belül a `inbound` XML-elemet, és kattintson a **korlátozása hívó IP-címek** utasítást.

![A szoftverkorlátozó házirendek][policies-restrict]

Ez egy XML-részletet, hozzáadja a `inbound` elem, amely útmutatást nyújt az utasítás konfigurálása.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Korlátozza a bejövő kéréseket, és fogadja el a következőképpen módosítása 1.2.3.4 IP-címek csak azokat az XML-fájl:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>További lépések

Házirendek használata további információkért lásd:

+ [API-k átalakítása](transform-api.md)
+ [Házirend-referencia](api-management-policy-reference.md) házirend-utasítások és a beállítások teljes listáját
+ [A házirend-minták](policy-samples.md)   

[Policy Reference]: api-management-policy-reference.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: api-management-howto-add-operations.md

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
