---
title: Függvényalkalmazás importálása API-ként az Azure Portal használatával | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan lehet az API Management (APIM) használatával függvényalkalmazásokat API-ként importálni.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/15/2018
ms.author: apimpm
ms.openlocfilehash: 0ee83446bb08e66c7f325bdd5585b8cc0484a74e
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090928"
---
# <a name="import-a-function-app-as-an-api"></a>Függvényalkalmazás importálása API-ként

A cikk bemutatja, hogyan importálhat egy függvényalkalmazást API-ként. A cikk az APIM API tesztelését is ismerteti.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Függvényalkalmazás importálása API-ként
> * Az API tesztelése az Azure Portalon
> * Az API tesztelése a fejlesztői portálon

## <a name="prerequisites"></a>Előfeltételek

+ Végezze el a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md)
+ Győződjön meg arról, hogy az előfizetése tartalmaz egy Azure-függvényalkalmazást. További információkért lásd a [függvényalkalmazás létrehozását](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) ismertető szakaszt.
+ Az Azure-függvényalkalmazás [OpenAPI-definíciójának létrehozása](../azure-functions/functions-openapi-definition.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Háttérrendszeri API importálása és közzététele

1. Válassza ki az **API-kat** az **API MANAGEMENT** részben.
2. Válasszon ki egy **függvényalkalmazást** az **Új API hozzáadása** listából.

    ![Függvényalkalmazás](./media/import-function-app-as-api/function-app-api.png)
3. Kattintson a **Tallózás** gombra az előfizetésben foglalt függvényalkalmazások listájának megtekintéséhez.
4. Válassza ki az alkalmazást. Az APIM megkeresi a kiválasztott alkalmazáshoz társuló Swaggert, majd beszerzi és importálja azt. 
5. Adja hozzá az API URL-cím utótagját. Az utótag lesz a név, amely azonosítja az API-t ebben az APIM-példányban. Egyedinek kell lennie az APIM-példányon belül.
6. Az API egy termékkel való társítással tehető közzé. Ebben az esetben az „*Unlimited*” terméket használjuk.  Ha közzé szeretné tenni az API-t, hogy elérhető legyen a fejlesztők számára, adja hozzá egy termékhez. Ezt megteheti az API létrehozása során, vagy később is.

    A termékek egy vagy több API társításai. Megadhatja az API-k számát, és a fejlesztői portálon elérhetővé teheti őket a fejlesztők számára. A fejlesztőknek elő kell fizetniük a termékre az API-k eléréséhez. Amikor előfizetnek, kapnak egy előfizetési kulcsot, amely a termék minden API-jához használható. Ha Ön hozta létre az APIM-példányt, akkor már eleve rendszergazdának számít, így alapértelmezés szerint minden termékre előfizetett.

    Alapértelmezés szerint az API Management minden példányához az alábbi két mintatermék jár:

    * **Kezdő**
    * **Korlátlan**   
7. Kattintson a **Létrehozás** gombra.

## <a name="populate-azure-functions-keys-in-azure-api-management"></a>Azure Functions-kulcsok feltöltése az Azure API Managementben

Ha az importált Azure Functionst kulcsok védik, akkor az Azure API Management automatikusan létrehoz hozzájuk **névvel ellátott értékeket**, de nem tölti fel titkos kulcsokkal a bejegyzéseket. Minden bejegyzés esetében el kell végeznie az alábbi lépéseket.  

1. Lépjen az API Management-példány **Névvel ellátott értékek** lapjára.
2. Kattintson egy bejegyzésre, majd az oldalsáv **Érték megjelenítése** elemére.

    ![Névvel ellátott értékek](./media/import-function-app-as-api/apim-named-values.png)

3. Ha a tartalom a(z) *{Azure-függvény neve} kódjához* hasonlít, lépjen az importált az Azure Functions-alkalmazásra, és keresse meg az Azure-függvényt.
4. Lépjen a kívánt Azure-függvény **Kezelés** szakaszára, és másolja ki a megfelelő kulcsot az Azure-függvény hitelesítési módszere alapján.

    ![Függvényalkalmazás](./media/import-function-app-as-api/azure-functions-app-keys.png)

5. Illessze be a **Névvel ellátott értékek** mezőből származó kulcsot a szövegmezőbe, majd kattintson a **Mentés** gombra.

    ![Függvényalkalmazás](./media/import-function-app-as-api/apim-named-values-2.png)

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Az új APIM API tesztelése az Azure Portalon

A műveleteket meg lehet hívni közvetlenül az Azure Portalról, ami kényelmes módot biztosít az API műveleteinek megtekintésére és tesztelésére.  

1. Válassza ki az előző lépésben létrehozott API-t.
2. Kattintson a **Teszt** fülre.
3. Válasszon ki egy műveletet.

    Az oldalon megjelennek a lekérdezési paraméterek és a fejlécek mezői. Az ehhez az API-hoz társított termék előfizetői azonosítójának egyik fejléce „Ocp-Apim-Subscription-Key” értékű. Ha Ön hozta létre az APIM-példányt, akkor már eleve rendszergazdának számít, így a kulcsot automatikusan kitölti a rendszer. 
1. Kattintson a **Küldés** gombra.

    A háttér a **200 OK** üzenetet és néhány adatot küld válaszként.

## <a name="call-operation"></a>Művelet meghívása a fejlesztői portálról

A **fejlesztői portálról** is meghívhat műveleteket az API-k teszteléséhez. 

1. Válassza ki a „Háttérrendszeri API importálása és közzététele” című lépésben létrehozott API-t.
2. Nyomja meg a **Fejlesztői portál** gombot.

    Megnyílik a Fejlesztői portál webhely.
3. Jelölje ki a létrehozott **API**-t.
4. Kattintson a tesztelni kívánt műveletre.
5. Kattintson a **Kipróbálás** gombra.
6. Kattintson a **Küldés** gombra.
    
    A művelet meghívása után a fejlesztői portál megjeleníti a **Válasz állapota**, a **Válasz fejlécei** és a **Válasz tartalma** minden információját.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Közzétett API átalakítása és védelme](transform-api.md)