---
title: "Házirendek az Azure API Management |} Microsoft Docs"
description: "Megtudhatja, hogyan létrehozása, szerkesztése és API-felügyeleti szabályzatok konfigurálására."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: apimpm
ms.openlocfilehash: 315e4bd7372416800373f98ecb5d8b1eb440e134
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="policies-in-azure-api-management"></a>Házirendek az Azure API Management

Az Azure API Management (APIM), a házirendek olyan egy hatékony képesség, a rendszer, amelyek lehetővé teszik a közzétevőt úgy, hogy az API-t konfigurálással működésének módosításához. Házirendek olyan utasításokat, amelyek egymás után, ha a kérés végrehajtása gyűjteménye vagy egy API-t adott válaszokat. Népszerű utasítások JSON az XML-formátumú átalakítás tartalmazza, és hívja meg egy fejlesztő a bejövő hívások mennyiségének korlátozásához sebességével. Számos további házirendeket nem érhetők el.

Házirendek az átjárón, amely az API-ügyfél és a felügyelt API között helyezkedik el belül érvényesek. Az átjáró összes kéréseket fogad, és általában továbbítja őket a mögöttes API változatlan. Azonban egy házirend módosításokat is alkalmazhatja a bejövő kérelem és a kimenő válasz.

A házirend-kifejezéseket attribútumértékekként vagy szövegértékekként lehet használni bármelyik API Management házirendben, hacsak a házirend másként nem rendelkezik. Egyes házirendek, például a [folyamatot szabályozhatja] [ Control flow] és [Set változó] [ Set variable] házirendek a házirend-kifejezések alapulnak. További információkért lásd: [házirendek speciális] [ Advanced policies] és [házirend-kifejezések][Policy expressions].

## <a name="sections"></a>Ismertetése házirend konfigurálása

A házirend-definíció egy egyszerű XML-dokumentumban, amely leírja a bejövő és kimenő utasítás sorozata. Az XML-fájl közvetlenül a definíció ablakban szerkeszthető. Egy utasítás listája jobbra, és az aktuális hatókör vonatkozó utasítások engedélyezve van, és a kijelölt.

Egy engedélyezett utasítás gombra kattintva adja hozzá a megfelelő XML a kurzor a definíció nézetben a helyen. 

> [!NOTE]
> Ha hozzá szeretne adni a házirend nincs engedélyezve, ellenőrizze, hogy az adott házirendnek megfelelő hatókörben. Minden egyes házirend-utasítás az egyes hatókörök és házirend szakaszok használatra szolgál. Tekintse át a házirend szakaszok és egy házirend hatókörök, ellenőrizze a **használati** a házirendhez tartozó szakasz a [szabályzataihoz][Policy Reference].
> 
> 

A konfigurációs oszlik `inbound`, `backend`, `outbound`, és `on-error`. A megadott házirend-utasításoknál, amely végrehajtja a ahhoz, hogy egy kérés és válasz.

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

Ha a kérelem feldolgozása során hiba történt, a többi szükséges lépések a `inbound`, `backend`, vagy `outbound` szakaszok kimarad, és végrehajtási lévő utasítások ugrik a `on-error` szakasz. Úgy, hogy a házirend-utasításoknál a `on-error` tekintse át a hiba a szakasz a `context.LastError` tulajdonság, nézze meg, és testre szabhatja a hiba válasz használatával a `set-body` házirend, és konfigurálása, mi történik, ha a hiba akkor fordul elő. Nincsenek hibakódok beépített lépéseket és a házirend-utasításoknál feldolgozása során előforduló hibákat. További információkért lásd: [hiba történt az API-felügyeleti házirendek kezelése](https://msdn.microsoft.com/library/azure/mt629506.aspx).

## <a name="scopes"></a>Házirendek konfigurálása

Házirendek konfigurálásával kapcsolatos további információkért lásd: [beállítása vagy szerkesztheti a szabályzatokat](set-edit-policies.md).

## <a name="policy-reference"></a>Házirend-hivatkozás

Tekintse meg a [szabályzataihoz](api-management-policy-reference.md) házirend-utasításoknál és a beállítások teljes listáját.

## <a name="policy-samples"></a>Házirend-minták

Lásd: [házirend minták](policy-samples.md) kód további példákat.

## <a name="examples"></a>Példák

### <a name="appliy-policies-specified-at-different-scopes"></a>Különböző hatóköröket megadott Appliy házirendek

Ha a globális szinten és az API-k konfigurált házirendek egy házirendet, majd, hogy adott API-t igénybe mindkét házirendeket a rendszer alkalmazza. API-kezelés lehetővé teszi, hogy az Alap elem keresztül kombinált házirend kimutatások determinisztikus rendezéshez. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

A fenti példa házirend-definíció a `cross-domain` utasítás futtatása előtt minden magasabb házirendet, amely viszont követnie a `find-and-replace` házirend. 

### <a name="restrict-incoming-requests"></a>Korlátozhatja a bejövő kérelmeket

A megadott IP-címekre korlátozhatja a bejövő kérelmeket egy új utasítás hozzáadása, a kurzort belülre tartalmát a `inbound` XML-elemet, és kattintson a **korlátozása hívó IP-címek** utasítás.

![A szoftverkorlátozó házirendek][policies-restrict]

Ez egy XML-részletet, hogy hozzáadja a `inbound` elem, amely az utasítás konfigurálásához nyújt útmutatást.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Korlátozza a bejövő kéréseket, és fogadja csak azokat az IP-címről 1.2.3.4 módosítsa az XML-fájl az alábbiak szerint:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

## <a name="next-steps"></a>Következő lépések

Házirendek használata további információkért lásd:

+ [Átalakítás API-k](transform-api.md)
+ [Házirend-hivatkozás](api-management-policy-reference.md) házirend-utasításoknál és a beállítások teljes listáját
+ [Házirend-minták](policy-samples.md)   

[Policy Reference]: api-management-policy-reference.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: api-management-howto-add-operations.md

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
