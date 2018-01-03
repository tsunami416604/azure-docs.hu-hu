---
title: "Hogyan hozhat létre, és a termék közzététele az Azure API Management"
description: "Ismerje meg, hogyan hozhat létre és termékek közzététele az Azure API Management."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: e6b11145506780f9a08799c4c9daf55ba17b366d
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2017
---
# <a name="create-and-publish-a-product"></a>Hozzon létre, és a termék közzététele  

Az Azure API Management a termék egy vagy több API-k, valamint a memóriahasználati kvóta és a használati feltételeket tartalmaz. Miután közzétette a termék, a fejlesztők a termék előfizetni és a termék API-k használatának megkezdéséhez.  

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre, és a termék közzététele
> * API hozzáadása a termékhez

![a hozzáadott termék](media/api-management-howto-add-products/added-product.png)

## <a name="prerequisites"></a>Előfeltételek

+ Fejezze be a következő gyorsindítási: [hozzon létre egy Azure API Management példányt](get-started-create-service-instance.md).
+ Is, végezze el a következő oktatóanyagot: [importálása és az első API-t közzétenni](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-and-publish-a-product"></a>Hozzon létre, és a termék közzététele

1. Kattintson a **termékek** megjelenítéséhez a bal oldali menüben a **termékek** lap.
2. Kattintson a **+ termék**.

    ![a hozzáadott termék](media/api-management-howto-add-products/add-product.png)

    A termék hozzáadásakor kell megadnia a következő információkat: 

    |Name (Név)|Leírás|
    |---|---|
    |Megjelenített név|A neve, ahogyan azt szeretné, hogy jelennek meg a **fejlesztői portálján**.|
    |Name (Név)|A termék egy leíró nevet.|
    |Leírás|A **leírás** mező lehetővé teszi, hogy adja meg a termék, például a célja, hozzáférést biztosít az API-k és más hasznos információkat nyújt tájékoztatást.|
    |Állapot|Nyomja le az **közzétett** ha közzéteszi a terméket. A termék API-k hívása előtt közzé kell tenni a terméket. Alapértelmezés szerint új termékek közzé nem tett, és csak a **rendszergazdák** csoport.|
    |Jóváhagyást igényel|Ellenőrizze **előfizetés jóváhagyás szükséges** Ha azt szeretné, hogy tekintse át és fogadja el vagy utasítsa el a termék előfizetés megkísérli a rendszergazda. Ha a jelölőnégyzet nincs bejelölve, az előfizetés kísérletek, automatikusan jóváhagyta. |
    |Előfizetések számának korlátja|Több egyidejű előfizetések számának korlátozásához adja meg az előfizetési határértéket. |
    |Jogi feltételek|A termék, mely előfizetők a termék használatához el kell fogadnia a használati feltételeket is megadhat.|
    |API-k|A termékeket társítását, egy vagy több API-k. Számos olyan API-k, és a fejlesztői portálon keresztül a fejlesztők számára biztosíthat számukra. <br/> A termék létrehozása során egy meglévő API-t is hozzáadhat. Később, a termék, a termékek származhatnak is hozzáadhat az API-k **beállítások** lapon vagy az API-k létrehozásakor.|<br/>A fejlesztők a termék az API eléréséhez először elő kell fizetnie. Fizet elő, amikor azok beolvasása, amely a termék API-k ideális előfizetés kulcsa.<br/> A APIM példányt hozott létre, ha rendszergazdaként jelentkezett már, így minden egyes termék előfizetett alapértelmezés szerint.|

3. Kattintson a **létrehozása** az új termék létrehozásához.

### <a name="add-more-configurations"></a>Adja hozzá a további konfigurációk

Folytathatja a termék konfigurálása után mentse kiválasztásával a **beállítások** fülre. 

Megtekintéséhez vagy felvételéhez előfizetők számára a terméket a **előfizetések** fülre.

Állítsa be a látható-e a termék a fejlesztők számára, vagy a Vendég a **hozzáférés-vezérlés** fülre.

## <a name="add-apis"></a>API-k hozzáadása egy termékre

A termékeket társítását, egy vagy több API-k. Számos olyan API-k, és a fejlesztői portálon keresztül a fejlesztők számára biztosíthat számukra. A termék létrehozása során egy meglévő API-t is hozzáadhat. Később, a termék, a termékek származhatnak is hozzáadhat az API-k **beállítások** lapon vagy az API-k létrehozásakor.

A fejlesztők a termék az API eléréséhez először elő kell fizetnie. Fizet elő, amikor azok beolvasása, amely a termék API-k ideális előfizetés kulcsa. A APIM példányt hozott létre, ha rendszergazdaként jelentkezett már, így minden egyes termék előfizetett alapértelmezés szerint.

### <a name="add-an-api-to-an-existing-product"></a>Az API-k hozzáadása egy meglévő megoldás

1. Termék kiválasztása.
2. Kattintson az API-k fülre.
3. Kattintson a **+ API**.
4. Válasszon egy API-t, és kattintson a **létrehozása**.

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Using-Products/player]
> 
> 

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Hozzon létre, és a termék közzététele
> * API hozzáadása a termékhez

Előzetes következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Hozzon létre üres API és API-válaszok mock](mock-api-responses.md)
