---
title: "Az API-k védelme az Azure API Management szolgáltatással | Microsoft Docs"
description: "Megtudhatja, hogyan védheti meg az API-kat kvótákkal és szabályozási (sebességhatároló) házirendekkel."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 450dc368-d005-401d-ae64-3e1a2229b12f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: apimpm
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: 9dba928b78c11213d4b0098986561b09678444eb
ms.contentlocale: hu-hu
ms.lasthandoff: 07/27/2017

---
# <a name="protect-your-api-with-rate-limits-using-azure-api-management"></a>Az API-k védelme sebességkorlátokkal az Azure API Management használatával
Ez az útmutató ismerteti, milyen könnyen adhat védelmet a háttérrendszerben futó API-khoz az Azure API Management szolgáltatásban a sebességkorlát- és kvótaházirendek konfigurálásával.

Ebben az oktatóanyagban létre fog hozni egy „Ingyenes próbaverzió” API-terméket, amely lehetővé teszi a fejlesztők számára, hogy percenként 10 hívást, hetente pedig legfeljebb 200 hívást indítsanak az API felé a [Hívások sebességének korlátozása előfizetésenként](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) és a [Használati kvóta beállítása előfizetésenként](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota) házirendek használatával. Ezután közzéteszi az API-t és teszteli a sebességkorlátozási házirendet.

A [rate-limit-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) és a [quota-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) házirendeket használó speciálisabb szabályozási forgatókönyvekért tekintse meg a [Speciális kérésszabályzás az Azure API Management szolgáltatással](api-management-sample-flexible-throttling.md) szakaszt.

## <a name="create-product"> </a>Termék létrehozása
Ebben a lépésben létrehoz egy Ingyenes próbaverzió terméket, amely nem igényel jóváhagyott előfizetést.

> [!NOTE]
> Ha már konfigurált egy terméket, és azt szeretné használni az oktatóanyaghoz, továbbléphet [A hívások sebességének korlátozása és a kvótaházirendek konfigurálása][Configure call rate limit and quota policies] című lépésre, és onnan már az Ingyenes próbaverzió termék helyett a saját termékével követheti az oktatóanyagot.
> 
> 

Első lépésként kattintson a **Közzétevő portál** elemre az API Management szolgáltatás Azure Portalján.

![Közzétevő portál][api-management-management-console]

> Ha még nem hozott létre API Management szolgáltatáspéldányt, tekintse meg [Az első API kezelése az Azure API Management szolgáltatásban][Manage your first API in Azure API Management] oktatóanyag [API Management szolgáltatáspéldány létrehozása][Create an API Management service instance] című szakaszát.
> 
> 

Kattintson a bal oldali **API Management** menü **Termékek** lehetőségére a **Termékek** oldal megjelenítéséhez.

![Termék hozzáadása][api-management-add-product]

Kattintson a **Termék hozzáadása** lehetőségre az **Új termék hozzáadása** párbeszédpanel megjelenítéséhez.

![Új termék hozzáadása][api-management-new-product-window]

A **Cím** mezőbe írja be az **Ingyenes próbaverzió** kifejezést.

A **Leírás** mezőbe írja be az alábbi szöveget: **Az előfizetők percenként 10 hívást indíthatnak, hetente pedig akár 200 hívást, ami után meg lesz tagadva a hozzáférés.**

Az API Management termékei védettek vagy nyitottak lehetnek. A védett termékeket csak az előfizetők használhatják. A nyitott termékeket előfizetés nélkül is lehet használni. Győződjön meg arról, hogy az **Előfizetés szükséges** lehetőség ki van választva, ha egy előfizetést igénylő védett terméket kíván létrehozni. Ez az alapértelmezett beállítás.

Ha szeretné, hogy a termékre történő előfizetési kísérleteket egy rendszergazda ellenőrizze, és elfogadja vagy elutasítsa azt, válassza ki a **Előfizetés jóváhagyása szükséges** lehetőséget. Ha a jelölőnégyzet nincs bejelölve, az előfizetési kísérletek automatikusan el lesznek fogadva. Ebben a példában az előfizetések jóváhagyása automatikus, ezért ne jelölje be a jelölőnégyzetet.

Ha engedélyezni szeretné a fejlesztői fiókok számára, hogy többször is előfizethessenek egy új termékre, jelölje be a **Több egyidejű előfizetés engedélyezése** jelölőnégyzetet. Ez az oktatóanyag nem használ több egyidejű előfizetést, ezért ne jelölje ezt be.

Miután az összes értéket megadta, kattintson a **Mentés** gombra a termék létrehozásához.

![Hozzáadott termék][api-management-product-added]

Alapértelmezés szerint a **Rendszergazdák** csoport tagjai látják az új termékeket. Hozzá fogjuk adni a **Fejlesztők** csoportot. Kattintson az **Ingyenes próbaverzió** lehetőségre, majd kattintson a **Láthatóság** lapra.

> Az API Management szolgáltatásban csoportok használatával szabályozható a fejlesztők hozzáférése a termékhez. A csoportok számára a termékek biztosítanak láthatóságot, a fejlesztők pedig megtekinthetik a csoportjuk számára látható termékeket és előfizethetnek rájuk. További információkért lásd: [Csoportok létrehozása és használata az Azure API Management szolgáltatásban][How to create and use groups in Azure API Management].
> 
> 

![Fejlesztői csoport hozzáadása][api-management-add-developers-group]

Jelölje be a **Fejlesztők** jelölőnégyzetet, majd kattintson a **Mentés** gombra.

## <a name="add-api"> </a>API hozzáadása a termékhez
Az oktatóanyag ezen lépésében hozzáadjuk az Echo API-t az új Ingyenes próbaverzió termékhez.

> Minden API Management szolgáltatáspéldányhoz előre konfigurálva van egy kipróbálható Echo API, amely segít megismerni az API Management szolgáltatást. További információkért lásd: [Az első API kezelése az Azure API Management szolgáltatásban][Manage your first API in Azure API Management].
> 
> 

Kattintson a bal oldali **API Management** menü **Termékek** elemére, majd kattintson az **Ingyenes próbaverzió** lehetőségre a termék konfigurálásához.

![Termék konfigurálása][api-management-configure-product]

Kattintson az **API hozzáadása termékhez** lehetőségre.

![API hozzáadása termékhez][api-management-add-api]

Válassza ki az **Echo API** elemet, majd kattintson a **Mentés** gombra.

![Echo API hozzáadása][api-management-add-echo-api]

## <a name="policies"> </a>A hívások sebességkorlátozása és a kvótaházirendek konfigurálása
A sebességkorlátokat és a kvótákat a házirendszerkesztőben lehet konfigurálni. A két szabályzat, amelyet ebben az oktatóanyagban hozzáadunk a [Hívások számának korlátozása előfizetésenként](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) és a [Használati kvóta beállítása előfizetésenként](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota). Ezeket a szabályzatokat termékszinten kell alkalmazni.

Kattintson a bal oldali **API Management** menü alatt található**Házirendek** lehetőségre. A **Termék** listán kattintson az **Ingyenes próbaverzió** lehetőségre.

![Termékházirend][api-management-product-policy]

Kattintson a **Házirend hozzáadása** lehetőségre a házirendsablon importálásához, hogy elkezdje létrehozni a sebességkorlát- és kvótaházirendeket.

![Házirend hozzáadása][api-management-add-policy]

A sebességkorlát- és kvótaházirendek bejövő házirendek, ezért vigye a kurzort az „inbound” elemre.

![Házirendszerkesztő][api-management-policy-editor-inbound]

szabályzat Görgessen végig a szabályzatok listáján, és keresse meg a **Hívások számának korlátozása előfizetésenként** szabályzatbejegyzést.

![Házirend-utasítások][api-management-limit-policies]

Miután a kurzort odavitte az **inbound** házirend elemre, kattintson a **Hívások számának korlátozása előfizetésenként** melletti nyílra a házirendsablon beillesztéséhez.

```xml
<rate-limit calls="number" renewal-period="seconds">
<api name="name" calls="number">
<operation name="name" calls="number" />
</api>
</rate-limit>
```

Ahogy a kódrészletből is látható, ezzel a szabályzattal korlátozások állíthatók be a termék API-jaira és műveleteire vonatkozóan. Ebben az oktatóanyagban nem használjuk ezt a képességet, ezért törölje az **api** és az **operation** elemet a **rate-limit** elemből, hogy csak a külső **rate-limit** elem maradjon az alábbi példában látható módon.

```xml
<rate-limit calls="number" renewal-period="seconds">
</rate-limit>
```

Az Ingyenes próbaverzió termékben a maximálisan engedélyezett hívások száma percenként 10, ezért írjon be **10**-et a **calls** attribútum értékéhez, és **60**-at a **renewal-period** attribútum értékéhez.

```xml
<rate-limit calls="10" renewal-period="60">
</rate-limit>
```

A **Használati kvóta beállítása előfizetésenként** szabályzat konfigurálásához helyezze az egérmutatót az újonnan hozzáadott **rate-limit** elem alá az **inbound** elemen belül, majd keresse meg és kattintson a **Használati kvóta beállítása előfizetésenként** szabályzat bal oldalán lévő nyílra.

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
<api name="name" calls="number" bandwidth="kilobytes">
<operation name="name" calls="number" bandwidth="kilobytes" />
</api>
</quota>
```

A **Használati kvóta beállítása előfizetésenként** szabályzathoz hasonlóan a **Használati kvóta beállítása előfizetésenként** szabályzattal korlátokat adhatunk meg a termék API-jaira és műveleteire vonatkozóan. Ebben az oktatóanyagban nem használjuk ezt a képességet, ezért törölje az **api** és az **operation** elemet a **quota** elemből az alábbi példában látható módon.

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
</quota>
```

A kvóták alapulhatnak az időközönként indított hívások számán, a sávszélességen, vagy mindkettőn. Ebben az oktatóanyagban nem a sávszélesség alapján szabályozunk, ezért törölje a **bandwidth** attribútumot.

```xml
<quota calls="number" renewal-period="seconds">
</quota>
```

Az Ingyenes próbaverzió termékben a kvóta 200 hívás hetente. Adja meg a **200**-at a **calls** attribútum értékeként, majd adja meg a **604800**-at a **renewal-period** attribútum értékeként.

```xml
<quota calls="200" renewal-period="604800">
</quota>
```

> A házirendidőközök másodpercekben vannak megadva. A heti időköz kiszámításához szorozza össze a napok számát (7) a nap óráinak számával (24), az óra perceinek számával (60) és a perc másodperceinek számával (60): 7 * 24 * 60 * 60 = 604 800.
> 
> 

A házirendnek a konfigurálás után meg kell egyeznie az alábbi példával.

```xml
<policies>
    <inbound>
        <rate-limit calls="10" renewal-period="60">
        </rate-limit>
        <quota calls="200" renewal-period="604800">
        </quota>
        <base />

</inbound>
<outbound>

    <base />

    </outbound>
</policies>
```

Miután konfigurálta a kívánt házirendeket, kattintson a **Mentés** gombra.

![Házirend mentése][api-management-policy-save]

## <a name="publish-product"> </a>A termék közzététele
Most, hogy hozzáadta az API-kat és konfigurálta a házirendeket, közzé kell tenni a terméket, hogy a fejlesztők használhassák. Kattintson a bal oldali **API Management** menü **Termékek** elemére, majd kattintson az **Ingyenes próbaverzió** lehetőségre a termék konfigurálásához.

![Termék konfigurálása][api-management-configure-product]

Kattintson a **Közzététel** elemre, majd kattintson az **Igen, közzéteszem** lehetőségre a megerősítéshez.

![Termék közzététele][api-management-publish-product]

## <a name="subscribe-account"> </a>Előfizetés a termékre egy fejlesztői fiók nevében
Most, hogy a termék közzé lett téve, a fejlesztők elő tudnak rá fizetni és használni tudják.

> Az API Management példányok rendszergazdái automatikusan előfizetnek az összes termékre. Az oktatóanyag ezen lépésében elő fogunk fizetni az egyik nem rendszergazdai fejlesztői fiókkal az Ingyenes próbaverzió termékre. Ha a fejlesztői fiókja a rendszergazdai szerepkör része, követheti a lépés utasításait, akkor is, ha már előfizetett.
> 
> 

Kattintson a bal oldali **API Management** menü **Felhasználók** lehetőségére, majd kattintson a fejlesztői fiókja nevére. Ebben a példában a **Clayton Gragg** fejlesztői fiókot használjuk.

![Fejlesztő konfigurálása][api-management-configure-developer]

Kattintson az **Előfizetés hozzáadása** lehetőségre.

![Előfizetés hozzáadása][api-management-add-subscription-menu]

Válassza ki az **Ingyenes próbaverzió** elemet, majd kattintson az **Előfizetés** lehetőségre.

![Előfizetés hozzáadása][api-management-add-subscription]

> [!NOTE]
> Ebben az oktatóanyagban az Ingyenes próbaverzió terméknél nincs engedélyezve a több egyidejű előfizetés. Ha engedélyezve lenne, a rendszer megkérné, hogy nevezze el az előfizetést az alábbi példában látható módon.
> 
> 

![Előfizetés hozzáadása][api-management-add-subscription-multiple]

Miután az **Előfizetés** lehetőségre kattint, a termék megjelenik a felhasználó számára az **Előfizetés** listán.

![Előfizetés hozzáadva][api-management-subscription-added]

## <a name="test-rate-limit"> </a>Művelet meghívása és a sebességkorlátozás tesztelése
Most, hogy konfigurálta és közzétette az Ingyenes próbaverzió terméket, meghívhatunk néhány műveletet, és tesztelhetjük a sebességkorlát-házirendet.
Váltson át a fejlesztői portálra a jobb felső menü **Fejlesztői portál** lehetőségére kattintva.

![Fejlesztői portál][api-management-developer-portal-menu]

Kattintson az **API-k** elemre a felső menüben, majd kattintson az **Echo API** lehetőségre.

![Fejlesztői portál][api-management-developer-portal-api-menu]

Kattintson a **GET Resource** elemre, majd kattintson a **Kipróbálom** gombra.

![Konzol megnyitása][api-management-open-console]

Tartsa meg az alapértelmezett paraméterértékeket, majd válassza ki az Ingyenes próbaverzió termék előfizetői azonosítóját.

![Előfizetői azonosító][api-management-select-key]

> [!NOTE]
> Ha több előfizetéssel rendelkezik, győződjön meg arról, hogy az **Ingyenes próbaverzió** azonosítóját választja, különben az előző lépésekben konfigurált házirendek nem lesznek érvényben.
> 
> 

Kattintson a **Küldés** gombra, majd tekintse meg a választ. Figyelje meg, hogy a **Válaszállapot****200 OK**.

![A művelet eredményei][api-management-http-get-results]

Kattintson a **Küldés** gombra egy olyan értékkel, amely meghaladja a 10 hívás percenként sebességkorláti házirendet. Ha túllépi a sebességkorlát-házirend értékét, a rendszer a **429 Too Many Requests** válaszállapotot küldi vissza.

![A művelet eredményei][api-management-http-get-429]

A **Válasz tartalma** jelzi a hátralévő időközt, amely leteltével az újrapróbálkozások sikeresek lesznek.

Ha a 10 hívás percenként sebességkorlátozási házirend van érvényben, a későbbi hívások sikertelenek lesznek, amíg el nem telik 60 másodperc a sebességkorlát átlépése előtti 10 sikeres termékhívás első hívását követően. Ebben a példában a hátralévő időköz 54 másodperc.

## <a name="next-steps"> </a>Következő lépések
* A sebességkorlátok és a kvóták beállításáról az alábbi videó kínál egy bemutatót.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]
> 
> 

[api-management-management-console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
[api-management-new-product-window]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
[api-management-product-added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
[api-management-add-policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
[api-management-policy-editor-inbound]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
[api-management-limit-policies]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
[api-management-policy-save]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
[api-management-configure-product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
[api-management-add-api]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
[api-management-add-echo-api]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
[api-management-developer-portal-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
[api-management-publish-product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
[api-management-configure-developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
[api-management-add-subscription-menu]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
[api-management-add-subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
[api-management-developer-portal-api-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
[api-management-open-console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-product-with-rules/api-management-http-get.png
[api-management-http-get-results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
[api-management-http-get-429]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
[api-management-product-policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
[api-management-add-developers-group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
[api-management-select-key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
[api-management-subscription-added]: ./media/api-management-howto-product-with-rules/api-management-subscription-added.png
[api-management-add-subscription-multiple]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-multiple.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Manage your first API in Azure API Management]: api-management-get-started.md
[How to create and use groups in Azure API Management]: api-management-howto-create-groups.md
[View subscribers to a product]: api-management-howto-add-products.md#view-subscribers
[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps

[Create a product]: #create-product
[Configure call rate limit and quota policies]: #policies
[Add an API to the product]: #add-api
[Publish the product]: #publish-product
[Subscribe a developer account to the product]: #subscribe-account
[Call an operation and test the rate limit]: #test-rate-limit

[Limit call rate]: https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate
[Set usage quota]: https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota

