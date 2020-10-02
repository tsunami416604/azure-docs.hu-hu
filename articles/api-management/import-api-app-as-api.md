---
title: API-alkalmazás importálása API-ként az Azure Portal használatával  | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan lehet az API Management (APIM) használatával API-alkalmazásokat API-ként importálni.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: 007598046ae64b390d44e61438b69bd26024b1c4
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91626887"
---
# <a name="import-an-api-app-as-an-api"></a>API-alkalmazás importálása API-ként

A cikk bemutatja, hogyan importálhat egy API-alkalmazást API-ként. A cikk az APIM API tesztelését is ismerteti.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * API-alkalmazás importálása API-ként
> * Az API tesztelése az Azure Portalon
> * Az API tesztelése a fejlesztői portálon

## <a name="prerequisites"></a>Előfeltételek

+ Hajtsa végre a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md)
+ Győződjön meg arról, hogy az előfizetése tartalmaz egy API-alkalmazást. További információ: [app Service dokumentáció](../app-service/index.yml)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Háttérbeli API importálása és közzététele

1. Navigáljon a API Management szolgáltatáshoz a Azure Portal, és válassza az **API-kat** a menüből.
2. Válasszon ki egy **API-alkalmazást** az **Új API hozzáadása** listából.

    ![API-alkalmazás](./media/import-api-app-as-api/api-app.png)
3. Kattintson a **Tallózás** gombra az előfizetésben foglalt API-alkalmazások listájának megtekintéséhez.
4. Válassza ki az alkalmazást. Az APIM megkeresi a kiválasztott alkalmazáshoz társuló Swaggert, majd beszerzi és importálja azt. 

    Amennyiben az APIM nem talál egy swaggert, „továbbítási” API-ként teszi közzé az API-t. 
5. Adja hozzá az API URL-cím utótagját. Az utótag lesz a név, amely azonosítja az API-t ebben az APIM-példányban. Egyedinek kell lennie az APIM-példányon belül.
6. Az API egy termékkel való társítással tehető közzé. Ebben az esetben az „*Unlimited*” terméket használjuk.  Ha közzé szeretné tenni az API-t, hogy elérhető legyen a fejlesztők számára, adja hozzá egy termékhez. Ezt megteheti az API létrehozása során, vagy később is.

    A termékek egy vagy több API társításai. Megadhatja az API-k számát, és a fejlesztői portálon elérhetővé teheti őket a fejlesztők számára. A fejlesztőknek elő kell fizetniük a termékre az API-k eléréséhez. Amikor előfizetnek, kapnak egy előfizetési kulcsot, amely a termék minden API-jához használható. Ha Ön hozta létre az APIM-példányt, akkor már eleve rendszergazdának számít, így alapértelmezés szerint minden termékre előfizetett.

    Alapértelmezés szerint az API Management minden példányához az alábbi két mintatermék jár:

    * **Kezdő**
    * **Korlátlan**   
7. Adja meg az egyéb API-beállításokat. Megadhatja az értékeket a létrehozás során, vagy később konfigurálhatja őket a **Beállítások** lapon. A beállításokat az [első API-oktatóanyag importálásával és közzétételével](import-and-publish.md#import-and-publish-a-backend-api) foglalkozó cikkben ismertetjük.
8. Kattintson a **Létrehozás** gombra.

## <a name="test-the-new-api-in-the-azure-portal"></a>Az új API tesztelése a Azure Portal

A műveleteket meg lehet hívni közvetlenül az Azure Portalról, ami kényelmes módot biztosít az API műveleteinek megtekintésére és tesztelésére.  

1. Válassza ki az előző lépésben létrehozott API-t.
2. Kattintson a **Teszt** fülre.
3. Válasszon ki egy műveletet.

    Az oldalon megjelennek a lekérdezési paraméterek és a fejlécek mezői. Az ehhez az API-hoz társított termék előfizetői azonosítójának egyik fejléce „Ocp-Apim-Subscription-Key” értékű. Ha Ön hozta létre az APIM-példányt, akkor már eleve rendszergazdának számít, így a kulcsot automatikusan kitölti a rendszer. 
1. Kattintson a **Küldés** gombra.

    A háttér a **200 OK** üzenetet és néhány adatot küld válaszként.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Közzétett API átalakítása és védelme](transform-api.md)
