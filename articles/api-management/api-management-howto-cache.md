---
title: "Gyorsítótárazás hozzáadása az Azure API Management teljesítményének javításához | Microsoft Docs"
description: "Megtudhatja, hogyan javíthat az API Management szolgáltatáshívások késleltetésén, sávszélesség-használatán és a webszolgáltatások terhelésén."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 59c595f0d5ce849f44c46fdb6cab0b44d35fffa0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Gyorsítótárazás hozzáadása az Azure API Management teljesítményének javításához
Az API Management műveleteit konfigurálni lehet a válaszok gyorsítótárazásához. A válaszok gyorsítótárazása jelentősen csökkentheti az API-k késleltetését, sávszélesség-használatát és a webszolgáltatások terhelését olyan adatok esetén, amelyek nem változnak gyakran.

Jelen útmutató ismerteti, hogyan adhatja hozzá az API-hoz a válaszok gyorsítótárazását, és hogyan konfigurálhat házirendeket a minta Echo API-műveletekhez. Ezt követően meghívhatja a műveletet a fejlesztői portálról a gyorsítótárazás működés közbeni ellenőrzéséhez.

> [!NOTE]
> További információ az elemeknek a házirend-kifejezések kulcsával történő gyorsítótárazásáról: [Egyéni gyorsítótárazás az Azure API Management szolgáltatásban](api-management-sample-cache-by-key.md).
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Mielőtt belekezdene az útmutató lépéseibe, rendelkeznie kell egy API Management szolgáltatáspéldánnyal, amelyhez konfigurálva van egy API és egy termék. Ha még nem hozott létre API Management szolgáltatáspéldányt, tekintse meg az [Ismerkedés az Azure API Management szolgáltatással][Get started with Azure API Management] oktatóanyag [API Management szolgáltatáspéldány létrehozása][Create an API Management service instance] című szakaszát.

## <a name="configure-caching"> </a>Művelet konfigurálása gyorsítótárazáshoz
Ebben a lépésben a minta Echo API **GET Resource (cached)** műveletének gyorsítótárazási beállításait fogja ellenőrizni.

> [!NOTE]
> Minden API Management szolgáltatáspéldányhoz előre konfigurálva van egy kipróbálható Echo API, amely segít megismerni az API Management szolgáltatást. További információkért lásd: [Ismerkedés az Azure API Management szolgáltatással][Get started with Azure API Management].
> 
> 

Első lépésként kattintson a **Közzétevő portál** elemre az API Management szolgáltatás Azure Portalján. Ezzel továbblép az API Management közzétevő portáljára.

![Közzétevő portál][api-management-management-console]

Kattintson a bal oldali **API Management** menü **API-k** elemére, majd kattintson az **Echo API** lehetőségre.

![Echo API][api-management-echo-api]

Kattintson a **Műveletek** lapra, majd kattintson a **Műveletek** lista **GET Resource (cached)** műveletére.

![Echo API műveletek][api-management-echo-api-operations]

Kattintson a **Gyorsítótárazás** lapra a művelet gyorsítótárazási beállításainak megtekintéséhez.

![Gyorsítótárazás lap][api-management-caching-tab]

Ha engedélyezni szeretné a gyorsítótárazást egy művelet számára, jelölje be az **Engedélyezés** jelölőnégyzetet. Ebben a példában engedélyezve van a gyorsítótárazás.

Minden művelet válasza egy kulccsal van ellátva a **Változtatás a lekérdezési karakterlánc paraméterei alapján** és **Változtatás a fejlécek alapján** mezőkben megadott értékek szerint. Ha gyorsítótárazni szeretné a többszöri válaszadást a lekérdezési karakterlánc paraméterei vagy a fejlécek alapján, ebben a két mezőben konfigurálhatja őket.

Az **Időtartam** megadja a gyorsítótárazott válaszok lejárati időközét. Ebben a példában az időköz **3600** másodperc, ami egy órának felel meg.

Ha a példa gyorsítótárazási konfigurációját használja, a **GET Resource (cached)** műveletre irányuló első kérés a háttérszolgáltatásból küld vissza választ. Ez a válasz gyorsítótárazva lesz, és egy kulccsal lesz ellátva a megadott fejlécek és lekérdezési karakterlánc paraméterek alapján. A művelet későbbi, egyező paraméterekkel rendelkező hívásai a gyorsítótárazott választ küldik vissza, egészen addig, amíg a gyorsítótárazás időköze le nem jár.

## <a name="caching-policies"> </a>A gyorsítótárazási házirendek áttekintése
Ebben a lépésben a minta Echo API **GET Resource (cached)** műveletének gyorsítótárazási beállításait ellenőrzi.

Amikor a **Gyorsítótárazás** lapon konfigurálják egy művelet gyorsítótárazási beállításait, a rendszer gyorsítótárazási házirendeket ad művelethez. Ezeket a házirendeket a házirendszerkesztőben lehet megtekinteni és szerkeszteni.

Kattintson a bal oldali **API Management** menü **Házirendek** elemére, majd válassza ki az **Echo API / GET Resource (cached)** lehetőséget a **Művelet** legördülő listából.

![Házirend-hatókör művelet][api-management-operation-dropdown]

Ez megjeleníti a művelet házirendjeit a házirendszerkesztőben.

![API Management házirendszerkesztő][api-management-policy-editor]

A művelet házirend-definíciójába beletartoznak azok a házirendek is, amelyek az előző lépésben a **Gyorsítótárazás** lappal ellenőrzött gyorsítótárazási konfigurációt definiálják.

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
            <vary-by-header>Accept</vary-by-header>
            <vary-by-header>Accept-Charset</vary-by-header>
        </cache-lookup>
        <rewrite-uri template="/resource" />
    </inbound>
    <outbound>
        <base />
        <cache-store caching-mode="cache-on" duration="3600" />
    </outbound>
</policies>
```

> [!NOTE]
> A gyorsítótárazási házirendek házirendszerkesztőben végzett módosításai meg fognak jelenni a művelet **Gyorsítótárazás** oldalán, és fordítva.
> 
> 

## <a name="test-operation"> </a>Művelet meghívása és a gyorsítótárazás tesztelése
A gyorsítótárazás működés közbeni megtekintéséhez meghívhatjuk a műveletet a fejlesztői portálról. Kattintson a **Fejlesztői portál** lehetőségre a jobb felső menüben.

![Fejlesztői portál][api-management-developer-portal-menu]

Kattintson az **API-k** elemre a felső menüben, majd válassza az **Echo API** lehetőséget.

![Echo API][api-management-apis-echo-api]

> Ha csak egy API van konfigurálva, vagy csak egy API látható a fiókja számára, és rákattint az API-k elemre, az közvetlenül az API-hoz tartozó művelethez fogja vinni.
> 
> 

Válassza ki a **GET Resource (cached)** műveletet, majd kattintson a **Konzol megnyitása** lehetőségre.

![Konzol megnyitása][api-management-open-console]

A konzol lehetővé teszi, hogy műveleteket hívjon meg közvetlenül a fejlesztői portálról.

![Konzol][api-management-console]

Tartsa meg a **param1** és a **param2** alapértelmezett értékeit.

Válassza ki a kívánt kulcsot a **subscription-key** legördülő listából. Ha a fiókja csak egyetlen előfizetéssel rendelkezik, akkor az alapból ki lesz választva.

Írja be a **sampleheader:value1** kifejezést a **Kérésfejlécek** szövegmezőbe.

Kattintson a **HTTP Get** lehetőségre, és jegyezze fel a válaszfejléceket.

Írja be a **sampleheader:value2** kifejezést a **Kérésfejlécek** szövegmezőbe, majd kattintson a **HTTP Get** lehetőségre.

Vegye figyelembe, hogy a **sampleheader** értéke a válaszban is **value1**. Próbáljon ki különböző értékeket, és jegyezze fel az első visszaküldött hívás gyorsítótárazott válaszát.

Írja be a **25** értéket a **param2** mezőbe, majd kattintson a **HTTP Get** lehetőségre.

Vegye figyelembe, hogy a **sampleheader** értéke a válaszban most már **value2**. Mivel a művelet eredményei egy kulccsal vannak ellátva a lekérdezési karakterlánc alapján, a rendszer nem küldte vissza az előző gyorsítótárazott választ.

## <a name="next-steps"> </a>Következő lépések
* További információt a gyorsítótárazási házirendekről az [API Management házirend-referencia][API Management policy reference] oktatóanyag [Gyorsítótárazási házirendek][Caching policies] szakaszában talál.
* További információ az elemeknek a házirend-kifejezések kulcsával történő gyorsítótárazásáról: [Egyéni gyorsítótárazás az Azure API Management szolgáltatásban](api-management-sample-cache-by-key.md).

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: api-management-get-started.md

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
