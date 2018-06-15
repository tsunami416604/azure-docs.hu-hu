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
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: 1962a4aac8e2d15caf4ec33998da1985d3b8a9af
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33934790"
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
+ Győződjön meg arról, hogy az előfizetése tartalmaz egy függvényalkalmazást. További információkért lásd a [függvényalkalmazás létrehozását](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) ismertető szakaszt.

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