---
title: "Házirendek az Azure API Management |} Microsoft Docs"
description: "Megtudhatja, hogyan létrehozása, szerkesztése és API-felügyeleti szabályzatok konfigurálására."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 537e5caf-708b-430e-a83f-72b70af28aa9
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 7c1f235343074ec11c635097f2b094a10f3fe781
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="policies-in-azure-api-management"></a>Házirendek az Azure API Management
Az Azure API Management a házirendek olyan egy hatékony képesség, a rendszer, amelyek lehetővé teszik a közzétevőt úgy, hogy az API-t konfigurálással működésének módosításához. Házirendek olyan utasításokat, amelyek egymás után, ha a kérés végrehajtása gyűjteménye vagy egy API-t adott válaszokat. Népszerű utasítások JSON az XML-formátumú átalakítás tartalmazza, és hívja meg egy fejlesztő a bejövő hívások mennyiségének korlátozásához sebességével. Számos további házirendeket nem érhetők el.

Tekintse meg a [szabályzataihoz] [ Policy Reference] házirend-utasításoknál és a beállítások teljes listáját.

Házirendek az átjárón, amely az API-ügyfél és a felügyelt API között helyezkedik el belül érvényesek. Az átjáró összes kéréseket fogad, és általában továbbítja őket a mögöttes API változatlan. Azonban egy házirend módosításokat is alkalmazhatja a bejövő kérelem és a kimenő válasz.

A házirend-kifejezéseket attribútumértékekként vagy szövegértékekként lehet használni bármelyik API Management házirendben, hacsak a házirend másként nem rendelkezik. Egyes házirendek, például a [folyamatot szabályozhatja] [ Control flow] és [Set változó] [ Set variable] házirendek a házirend-kifejezések alapulnak. További információkért lásd: [házirendek speciális] [ Advanced policies] és [házirend-kifejezések][Policy expressions].

## <a name="scopes"></a>Házirendek konfigurálása
Házirendek úgy is konfigurálhatók, globálisan vagy a hatókörben egy [termék][Product], [API] [ API] vagy [művelet] [Operation]. Konfigurálja a házirendet, navigáljon a házirendek szerkesztő a közzétevő portálon.

![Házirendek menü][policies-menu]

A házirendek szerkesztő három fő részből áll: a házirend-hatókörnek (felső), a házirend-definíció, ahol házirendek szerkesztése (bal oldali) és a kimutatások listában (jobbra):

![Házirendek szerkesztő][policies-editor]

Egy házirend konfigurálásának a megkezdéséhez ki kell választania a hatókör, ahol a házirendet alkalmazni kell. Az alábbi képernyőképen a **alapszintű** termék van kiválasztva. Vegye figyelembe, hogy a házirend neve melletti négyzet szimbólum azt jelzi, hogy egy házirend már alkalmazva van ezen a szinten.

![Hatókör][policies-scope]

Óta egy házirend már telepítve van, a definíció nézetben jelenik meg a konfigurációt.

![Konfigurálás][policies-configure]

A házirend csak olvasható jelenik meg először. Kattintson a definíció szerkesztéséhez a **házirend konfigurálása** művelet.

![Szerkesztés][policies-edit]

A házirend-definíció egy egyszerű XML-dokumentumban, amely leírja a bejövő és kimenő utasítás sorozata. Az XML-fájl közvetlenül a definíció ablakban szerkeszthető. Egy utasítás listája jobbra, és az aktuális hatókör vonatkozó utasítások engedélyezve van, és a kijelölt; ahogy ezt a **korlát hívás arány** utasítás a fenti képernyőfelvételen látható.

Egy engedélyezett utasítás gombra kattintva adja hozzá a megfelelő XML a kurzor a definíció nézetben a helyen. 

> [!NOTE]
> Ha hozzá szeretne adni a házirend nincs engedélyezve, ellenőrizze, hogy az adott házirendnek megfelelő hatókörben. Minden egyes házirend-utasítás az egyes hatókörök és házirend szakaszok használatra szolgál. Tekintse át a házirend szakaszok és egy házirend hatókörök, ellenőrizze a **használati** a házirendhez tartozó szakasz a [szabályzataihoz][Policy Reference].
> 
> 

A házirend-utasításoknál teljes listáját és a beállítások érhetők el a [szabályzataihoz][Policy Reference].

Például a megadott IP-címek korlátozása a bejövő kérelmeket egy új utasítás hozzáadása, vigye a kurzort, belülre tartalmát a `inbound` XML-elemet, és kattintson a **korlátozása hívó IP-címek** utasítás.

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

![Mentés][policies-save]

Amikor végzett a kimutatások a házirend konfigurálása, kattintson **mentése** és a módosítások propagálódik az API Management átjáró azonnal.

## <a name="sections"></a>Ismertetése házirend konfigurálása
A házirend utasításokat, amelyek ahhoz, hogy egy kérés- és a végrehajtása több. A konfigurációs oszlik megfelelően `inbound`, `backend`, `outbound`, és `on-error` látható módon a következő konfigurációs szakasz.

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

Mivel a házirendek (globális, termék, api és művelet) különböző szinteken adható meg a konfigurációs módot biztosít a megadhatja a sorrendet, amelyben a házirend-definíció utasítások tekintetében a szülő házirend hajtható végre. 

Házirend hatókörök a következő sorrendben értékeli ki a rendszer.

1. Globális hatókörű
2. A termék hatókör
3. API-hatókör
4. Műveleti hatókör

Azokat a utasítási kiértékelése az elhelyezkedését a `base` elem, ha telepítve. Általános házirend nem szülője házirend és a használatával a `<base>` az elem nincs hatása.

Például, ha a globális szinten és az API-k konfigurált házirendek egy házirendet, majd, hogy adott API-t igénybe mindkét házirendeket a rendszer alkalmazza. API-kezelés lehetővé teszi, hogy az Alap elem keresztül kombinált házirend kimutatások determinisztikus rendezéshez. 

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

A házirendek a Helyicsoportházirend-szerkesztő a jelenlegi hatókörben megtekintéséhez kattintson **számítsa ki újra a kijelölt hatókör hatékony házirend**.

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő videót a házirend-kifejezések.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

[Policy Reference]: api-management-policy-reference.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md#add-apis 
[Operation]: api-management-howto-add-operations.md

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-menu]: ./media/api-management-howto-policies/api-management-policies-menu.png
[policies-editor]: ./media/api-management-howto-policies/api-management-policies-editor.png
[policies-scope]: ./media/api-management-howto-policies/api-management-policies-scope.png
[policies-configure]: ./media/api-management-howto-policies/api-management-policies-configure.png
[policies-edit]: ./media/api-management-howto-policies/api-management-policies-edit.png
[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
[policies-save]: ./media/api-management-howto-policies/api-management-policies-save.png
