---
title: Termék létrehozása és közzététele az Azure API Managementben
description: Megismerheti, hogyan hozhat létre és tehet közzé termékeket az Azure API Managementben.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 08/10/2018
ms.author: apimpm
ms.openlocfilehash: 4f5097f8555d0345add7de8a0f648190bd4e1e00
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2018
ms.locfileid: "41918752"
---
# <a name="create-and-publish-a-product"></a>Termékek létrehozása és közzététele  

Az Azure API Management szolgáltatásban a termék egy vagy több API-t, valamint a használati kvótát és a használati feltételeket tartalmazza. Egy termék közzététele után a fejlesztők előfizethetnek a termékre, és megkezdhetik a termék API-jainak használatát.  

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Termékek létrehozása és közzététele
> * API hozzáadása a termékhez

![termék hozzáadása](media/api-management-howto-add-products/added-product.png)

## <a name="prerequisites"></a>Előfeltételek

+ Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
+ Végezze el a következő oktatóanyagot is: [Az első API importálása és közzététele](import-and-publish.md).

## <a name="create-and-publish-a-product"></a>Termékek létrehozása és közzététele

1. Kattintson a bal oldali menü **Termékek** lehetőségére a **Termékek** oldal megjelenítéséhez.
2. Kattintson a **+Hozzáadás** gombra.

    ![hozzáadott termék](media/api-management-howto-add-products/add-product.png)

    Termék hozzáadásakor meg kell adnia a következő információkat: 

    | Name (Név)                     | Leírás                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Megjelenített név             | A név, ahogyan a **fejlesztői portálon** meg szeretné jeleníteni.                                                                                                                                                                                                                                                        |
    | Name (Név)                     | A termék leíró neve.                                                                                                                                                                                                                                                                                      |
    | Leírás              | A **Leírás** mezőben részletes információkat adhat meg a termékről, például a funkcióját vagy az API-kat, amelyekhez hozzáférést nyújt.                                                                                                                                               |
    | Állapot                    | Kattintson a **Közzétett** elemre, ha közzé szeretné tenni a terméket. Mielőtt meghívhatná egy termék API-jait, közzé kell tenni a terméket. Alapértelmezés szerint az új termékek nincsenek közzétéve, és csak a **Rendszergazdák** csoport tagjai számára láthatók.                                                                                      |
    | Előfizetés szükséges    | Jelölje be az **Előfizetés szükséges** jelölőnégyzetet, ha egy felhasználónak elő kell fizetnie a termék használatára.                                                                                                                                                                                                                                   |
    | Jóváhagyást igényel        | Jelölje be a **Jóváhagyást igényel** jelölőnégyzetet, ha azt szeretné, hogy egy rendszergazda döntsön a termék előfizetésére vonatkozó kérelmek elfogadásáról vagy elutasításáról. Ha a jelölőnégyzet nincs bejelölve, a rendszer az előfizetési kísérleteket automatikusan elfogadja.                                                                                                                         |
    | Előfizetések számának korlátozása | Az egyidejű előfizetések számának korlátozásához adjon meg egy határértéket.                                                                                                                                                                                                                                |
    | Jogi feltételek              | Megadhatja a termék használati feltételeit, amelyeket az előfizetőknek el kell fogadniuk, hogy használni tudják a terméket.                                                                                                                                                                                                             |
    | API-k                     | A termékek egy vagy több API társításai. Megadhatja az API-k számát, és a fejlesztői portálon elérhetővé teheti őket a fejlesztők számára. <br/> A termék létrehozása során hozzáadhat meglévő API-kat, de később is hozzáadhat API-t a termékhez a Termékek **Beállítások** lapján vagy az API-k létrehozásakor. |

3. Az új termék létrehozásához kattintson a **Létrehozás** gombra.

### <a name="add-more-configurations"></a>További konfigurációk hozzáadása

A mentés után is folytathatja a termék konfigurálását a **Beállítások** lapon. 

A termék előfizetőit az **Előfizetések** lapon tekintheti meg, ahol új előfizetőket is megadhat.

A **Hozzáférés-vezérlés** lapon azt adhatja meg, hogy egy termék látható legyen-e a fejlesztők vagy a vendégek számára.

## <a name="add-apis"> </a>API-k hozzáadása termékekhez

A termékek egy vagy több API társításai. Megadhatja az API-k számát, és a fejlesztői portálon elérhetővé teheti őket a fejlesztők számára. A termék létrehozása során hozzáadhat meglévő API-kat, de később is hozzáadhat API-t a termékhez a Termékek **Beállítások** lapján vagy az API-k létrehozásakor.

A fejlesztőknek elő kell fizetniük a termékre az API-k eléréséhez. Amikor előfizetnek, kapnak egy előfizetési kulcsot, amely a termék minden API-jához használható. Ha Ön hozta létre az APIM-példányt, akkor már eleve rendszergazdának számít, így alapértelmezés szerint minden termékre előfizetett.

### <a name="add-an-api-to-an-existing-product"></a>API hozzáadása meglévő termékhez

![termék hozzáadása, API](media/api-management-howto-add-products/add-product-api.png)

1. Válasszon ki egy terméket a **Termékek** lapon.
2. Lépjen az **API-k** lapra.
3. Kattintson a **+Hozzáadás** gombra.
4. Válasszon egy API-t, és kattintson a **Kiválasztás** gombra.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Termékek létrehozása és közzététele
> * API hozzáadása a termékhez

Folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Üres API és API-válaszok utánzásának létrehozása](mock-api-responses.md)
