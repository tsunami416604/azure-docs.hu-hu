---
title: "Hogyan hozhat létre, és a termék közzététele az Azure API Management"
description: "Ismerje meg, hogyan hozhat létre és termékek közzététele az Azure API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 31de55cb-9384-490b-a2f2-6dfcf83da764
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 2cf905967f26de97613ff7d5fc495c9223e11390
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-create-and-publish-a-product-in-azure-api-management"></a>Hogyan hozhat létre, és a termék közzététele az Azure API Management
Az Azure API Management a termék egy vagy több API-k, valamint a memóriahasználati kvóta és a használati feltételeket tartalmaz. Miután közzétette a termék, a fejlesztők a termék előfizetni és a termék API-k használatának megkezdéséhez. A témakör való termék létrehozása, az API-k hozzáadása, és közzéteheti azt a fejlesztők számára.

## <a name="create-product"></a>Termék létrehozása
Műveletek felvétele, illetve egy API-t, a közzétevő portálon konfigurálva. A közzétevő portál eléréséhez kattintson **Publisher portal** az Azure portálon az API Management szolgáltatás.

![Közzétevő portál][api-management-management-console]

> Ha még nem hozott létre API Management szolgáltatáspéldányt, tekintse meg az [Ismerkedés az Azure API Management szolgáltatással][Get started with Azure API Management] oktatóanyag [API Management szolgáltatáspéldány létrehozása][Create an API Management service instance] című szakaszát.
> 
> 

Kattintson a **termékek** megjelenítéséhez a bal oldali menüben a **termékek** lapon, majd kattintson **termék hozzáadása**.

![Termékek][api-management-products]

![Új termék][api-management-add-new-product]

Adjon meg egy leíró nevet a termékből a **neve** mező, és a termék leírása a **leírás** mező.

Az API Management termékek lehet **nyitott** vagy **védett**. A védett termékeket csak előfizetők használhatják, míg a nyílt termékeket előfizetés nélkül is lehet használni. Ellenőrizze **előfizetés szükséges** előfizetést igénylő védett termék létrehozásához. Ez az alapértelmezett beállítás.

Ellenőrizze **előfizetés jóváhagyás szükséges** Ha azt szeretné, hogy tekintse át és fogadja el vagy utasítsa el a termék előfizetés megkísérli a rendszergazda. Ha a jelölőnégyzet nincs bejelölve, a előfizetés kísérletek lesz automatikusan jóváhagyta. Az előfizetések további információkért lásd: [megtekintheti a termék-előfizetők][View subscribers to a product].

Engedélyezze a fejlesztői fiókok többször előfizetni a terméket, ellenőrizze a **több előfizetés engedélyezése** jelölőnégyzetet. Ha a négyzet nincs bejelölve, minden developer-fiók csak egy alkalommal a termék kérhet le.

![Korlátlan több előfizetéssel][api-management-unlimited-multiple-subscriptions]

Több egyidejű előfizetések számának korlátozása érdekében ellenőrizze a **egyidejű előfizetések számának korlátozása** jelölje be a jelölőnégyzetet, és írja be az előfizetési határértéket. A következő példában egyidejű előfizetések korlátozódnak négy fejlesztői fiókonként.

![Négy több előfizetéssel][api-management-four-multiple-subscriptions]

Minden új termék beállítások konfigurálása után kattintson **mentése** az új termék létrehozásához.

![Termékek][api-management-products-page]

> Alapértelmezés szerint új termékek közzé nem tett, és csak a **rendszergazdák** csoport.
> 
> 

A termék konfigurálásához kattintson a termék nevére a a **termékek** fülre.

## <a name="add-apis"></a>API-k hozzáadása egy termékre
A **termékek** lapon négy hivatkozása a konfigurációhoz: **összegzés**, **beállítások**, **látható**, és  **Előfizetők**. A **összegzés** lapon látható, ahol akkor is vegye fel az API-k és közzétételét és a termék.

![Összefoglalás][api-management-new-product-summary]

A termék közzététele előtt kell hozzáadnia egy vagy több API-k. Ehhez kattintson **API vegye fel a termék**.

![API-k hozzáadása][api-management-add-apis-to-product]

Válassza ki a kívánt API-kat, és kattintson a **mentése**.

## <a name="add-description"></a>Leíró adatokat hozzáadni a termék
A **beállítások** lapon adhatja meg a termék, például a célja, hozzáférést biztosít az API-k és más hasznos információk részletes információkat tartalmaznak. A tartalom célja a fejlesztők számára, amely hívja az API-t kell lesz, és egyszerű szöveg- vagy HTML-kódot is beírhatók.

![A termék beállításai][api-management-product-settings]

Ellenőrizze **előfizetés szükséges** védett termék, amely használható, vagy törölje a jelölőnégyzet bejelölésével hozzon létre egy megnyitott terméket, amely a előfizetés nélkül meghívható előfizetését igényli létrehozásához.

Válassza ki **előfizetés jóváhagyás szükséges** Ha manuálisan jóváhagyja az összes termék előfizetési kérelmek. Alapértelmezés szerint az összes termék előfizetések automatikusan kapnak.

Engedélyezze a fejlesztői fiókok többször előfizetni a terméket, ellenőrizze a **több előfizetés engedélyezése** jelölje be a jelölőnégyzetet, és opcionálisan adja meg a határértéket. Ha a négyzet nincs bejelölve, minden developer-fiók csak egy alkalommal a termék kérhet le.

Nem kötelező kitölteni a **használati feltételek** mezőt, amely leírja a termék, mely előfizetők a termék használatához el kell fogadnia a használati feltételeket.

## <a name="publish-product"></a>Termék közzététele
A termék API-k hívása előtt közzé kell tenni a terméket. A a **összegzés** termék lapra, majd **közzététel**, és kattintson a **Igen, azt közzé** megerősítéséhez. A korábban közzétett termék titkos kattintson **Közzététel megszüntetése**.

![Termék közzététele][api-management-publish-product]

## <a name="make-visible"></a>a fejlesztők számára láthatóvá termék
A **látható** lap lehetővé teszi annak meghatározását, mely szerepkörök képesek a termék megjelenik a fejlesztői portálján, és a termék előfizetni.

![A termék látható][api-management-product-visiblity]

Letiltása és engedélyezése látható-e a termék a fejlesztők számára az egy csoport, ellenőrizze, vagy törölje a jelet az a csoport melletti jelölőnégyzetet, majd kattintson **mentése**.

> További információkért lásd: [létrehozásáról és csoportoknak a segítségével az Azure API Management fejlesztői fiókok kezelése][How to create and use groups to manage developer accounts in Azure API Management].
> 
> 

## <a name="view-subscribers"></a>Termék-előfizetők megtekintése
A **előfizetők** lap felsorolja a fejlesztők számára a termék előfizetett. A részletek és minden fejlesztői beállításait a a fejlesztői nevére kattintva is megtekinthetők. Ebben a példában nincs fejlesztők előfizetett még a termék.

![Fejlesztők][api-management-developer-list]

## <a name="next-steps"></a>Következő lépések
Miután a kívánt API-k hozzáadásával, és a termék közzétett, a fejlesztők előfizetni a termék és az API-k hívására megkezdéséhez. Lásd: mutatja be, ezeket a cikkeket, valamint a speciális termék konfigurációs [hogyan létrehozása, és speciális termékbeállítások konfigurálása az Azure API Management][How create and configure advanced product settings in Azure API Management].

Termékek kezelésével kapcsolatos további információkért tekintse meg a következő videó.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Using-Products/player]
> 
> 

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[View subscribers to a product]: #view-subscribers
[Next steps]: #next-steps

[api-management-management-console]: ./media/api-management-howto-add-products/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-add-products/api-management-add-product.png
[api-management-add-new-product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
[api-management-unlimited-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-unlimited-multiple-subscriptions.png
[api-management-four-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-four-multiple-subscriptions.png
[api-management-products-page]: ./media/api-management-howto-add-products/api-management-products-page.png
[api-management-new-product-summary]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
[api-management-add-apis-to-product]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
[api-management-product-settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
[api-management-publish-product]: ./media/api-management-howto-add-products/api-management-publish-product.png
[api-management-product-visiblity]: ./media/api-management-howto-add-products/api-management-product-visibility.png
[api-management-developer-list]: ./media/api-management-howto-add-products/api-management-developer-list.png



[api-management-products]: ./media/api-management-howto-add-products/api-management-products.png
[api-management-]: ./media/api-management-howto-add-products/
[api-management-]: ./media/api-management-howto-add-products/


[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps
[How to create and use groups to manage developer accounts in Azure API Management]: api-management-howto-create-groups.md
[How create and configure advanced product settings in Azure API Management]: api-management-howto-product-with-rules.md 
