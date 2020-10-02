---
title: Oktatóanyag – termék létrehozása és közzététele az Azure-ban API Management
description: Ebben az oktatóanyagban egy terméket hoz létre és tesz közzé az Azure API Managementban. A közzététel után a fejlesztők elkezdhetik használni a termék API-jait.
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: apimpm
ms.openlocfilehash: 2f298f240d8aa7a38b42a8c78ee3c90fe3423d10
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91630566"
---
# <a name="tutorial-create-and-publish-a-product"></a>Oktatóanyag: termék létrehozása és közzététele  

Az Azure API Management egy [*termék*](api-management-terminology.md#term-definitions) tartalmaz egy vagy több API-t, valamint egy használati kvótát és a használati feltételeket. Egy termék közzététele után a fejlesztők előfizethetnek a termékre, és megkezdhetik a termék API-jainak használatát.  

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Termékek létrehozása és közzététele
> * API hozzáadása a termékhez

:::image type="content" source="media/api-management-howto-add-products/added-product.png" alt-text="API Management termékek a portálon":::


## <a name="prerequisites"></a>Előfeltételek

+ Az [Azure API Management terminológiájának](api-management-terminology.md) ismerete.
+ Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
+ Végezze el a következő oktatóanyagot is: [Az első API importálása és közzététele](import-and-publish.md).

## <a name="create-and-publish-a-product"></a>Termékek létrehozása és közzététele

1. Jelentkezzen be a Azure Portalba, és navigáljon a API Management-példányhoz.
1. A bal oldali navigációs sávon válassza a **termékek**  >  **+ Hozzáadás**lehetőséget.
1.  A **termék hozzáadása** ablakban adja meg az alábbi táblázatban leírt értékeket a termék létrehozásához.

    :::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-01.png" alt-text="API Management termékek a portálon":::

    | Név                     | Leírás                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Megjelenített név             | A név, ahogy szeretné, hogy megjelenjen a [fejlesztői portálon](api-management-howto-developer-portal.md).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Leírás              | Adja meg a termékkel kapcsolatos információkat, például a célját, az API-kat, amelyek hozzáférést biztosítanak a szolgáltatáshoz és egyéb részletekhez.                                                                                                                                               |
    | Állapot                    | Válassza a **közzétett** elemet, ha közzé szeretné tenni a terméket. Mielőtt meghívhatná egy termék API-jait, közzé kell tenni a terméket. Alapértelmezés szerint az új termékek nincsenek közzétéve, és csak a  **rendszergazdák** csoport számára láthatók.                                                                                      |
    | Előfizetés szükséges    | Válassza ki, hogy a termék használatához szükség van-e a felhasználó előfizetésére.                                                                                                                                                                                                                                   |
    | Jóváhagyást igényel        | Jelölje be, ha azt szeretné, hogy a rendszergazda áttekintse és elfogadja vagy elutasítja az előfizetési kísérleteket a termékre. Ha nincs bejelölve, a rendszer automatikusan jóváhagyja az előfizetési kísérleteket.                                                                                                                         |
    | Előfizetések számának korlátozása | Opcionálisan korlátozhatja több egyidejű előfizetés számát.                                                                                                                                                                                                                                |
    | Jogi feltételek              | Megadhatja a termék használati feltételeit, amelyeket az előfizetőknek el kell fogadniuk, hogy használni tudják a terméket.                                                                                                                                                                                                             |
    | API-k                     | Válasszon ki egy vagy több API-t. A termék létrehozása után API-kat is hozzáadhat. További információ: API-k [hozzáadása egy termékhez](#add-apis-to-a-product) a cikk későbbi részében. |

3. Válassza a **Létrehozás** lehetőséget az új termék létrehozásához.

### <a name="add-more-configurations"></a>További konfigurációk hozzáadása

A mentés után folytassa a termék konfigurálását. A API Management-példányban válassza ki a terméket a **termékek** ablakban. Hozzáadás vagy frissítés:


|Elem   |Leírás  |
|---------|---------|
|Beállítások     |    Termék metaadatai és állapota     |
|API-k     |  A termékhez társított API-k       |
|[Házirendek](api-management-howto-policies.md)     |  A termék API-kra alkalmazott házirendek      |
|Hozzáférés-vezérlés     |  A termék láthatósága a fejlesztők és a vendégek számára       |
|[Előfizetések](api-management-subscriptions.md)    |    Termék előfizetői     |

## <a name="add-apis-to-a-product"></a>API-k hozzáadása termékekhez

A termékek egy vagy több API társításai. Megadhatja az API-k számát, és a fejlesztői portálon elérhetővé teheti őket a fejlesztők számára. A termék létrehozása során hozzáadhat egy vagy több meglévő API-t. A termékhez később is hozzáadhat API-kat a Products **Settings** (termékek beállításai) lapon, vagy LÉTREHOZHAT egy API-t.

A fejlesztőknek elő kell fizetniük a termékre az API-k eléréséhez. Amikor előfizetnek, kapnak egy előfizetési kulcsot, amely a termék minden API-jához használható. Ha Ön hozta létre az APIM-példányt, akkor már eleve rendszergazdának számít, így alapértelmezés szerint minden termékre előfizetett.

### <a name="add-an-api-to-an-existing-product"></a>API hozzáadása meglévő termékhez


1. A API Management példány bal oldali navigációs sávján válassza a **termékek**elemet.
1. Válasszon ki egy terméket, majd válassza az **API-kat**.
1. Válassza a **+ Hozzáadás** lehetőséget.
1. Válasszon ki egy vagy több API-t, majd **válassza a elemet**.

:::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-02.png" alt-text="API Management termékek a portálon":::

> [!TIP]
> Létrehozhat vagy frissíthet egy felhasználó előfizetését egyéni előfizetési kulcsokkal rendelkező termékre egy [REST API](/rest/api/apimanagement/2019-12-01/subscription/createorupdate) vagy egy PowerShell-paranccsal.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Termékek létrehozása és közzététele
> * API hozzáadása a termékhez

Folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Üres API és API-válaszok utánzásának létrehozása](mock-api-responses.md)
