---
title: Azure Functions-alkalmazás importálása API-ként az Azure Portal használatával | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan lehet az Azure API Management használatával Azure Functions-alkalmazásokat API-ként importálni.
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
ms.openlocfilehash: 670fa58de7155028b0f72f1f819b9f269e07b9eb
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239052"
---
# <a name="import-an-azure-functions-app-as-an-api"></a>Azure Functions-alkalmazás importálása API-ként

A cikk bemutatja, hogyan importálhat egy Azure Functions-alkalmazást API-ként. A cikk az Azure API Management API tesztelését is ismerteti.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Functions-alkalmazás importálása API-ként
> * Az API tesztelése az Azure Portalon
> * Az API tesztelése a fejlesztői portálon

## <a name="prerequisites"></a>Előfeltételek

+ Tekintse át az [Azure API Management-példány létrehozását](get-started-create-service-instance.md) ismertető rövid útmutatót.
+ Győződjön meg arról, hogy az előfizetése tartalmaz egy Azure Functions-alkalmazást. További információkért lásd a [Functions-alkalmazás létrehozását](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) ismertető szakaszt.
+ Az Azure Functions-alkalmazás [OpenAPI-definíciójának létrehozása](../azure-functions/functions-openapi-definition.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"></a>Háttérrendszerbeli API importálása és közzététele

1. Az **API MANAGEMENT** részben válassza ki az **API-kat**.
2. Az **Új API hozzáadása** listából válassza ki a **Functions-alkalmazást**.

    ![Functions-alkalmazás](./media/import-function-app-as-api/function-app-api.png)
3. Kattintson a **Tallózás** gombra az előfizetésben található Functions-alkalmazások listájának megtekintéséhez.
4. Válassza ki az alkalmazást. Az API Management megkeresi a kiválasztott alkalmazáshoz társuló Swaggert, és beszerzi, majd importálja azt. 
5. Adja hozzá az API URL-cím utótagját. Az utótag lesz a név, amely azonosítja az API-t ebben az API Management-példányban. Az utótagnak egyedinek kell lennie az API Management-példányon belül.
6. Az API egy termékkel való társítással tehető közzé. Ebben az esetben az **Unlimited** terméket használjuk. Ha közzé szeretné tenni az API-t, hogy elérhető legyen a fejlesztők számára, adja hozzá egy termékhez. Az API-t hozzáadhatja egy termékhez az API létrehozásakor, illetve később is.

    A termékek egy vagy több API társításai. Több API-t is megadhat, és a fejlesztői portálon elérhetővé teheti őket a fejlesztők számára. A fejlesztőknek elő kell fizetniük a termékre az API-k eléréséhez. Amikor előfizetnek, a fejlesztők kapnak egy előfizetési kulcsot, amely a termék minden API-jához használható. Ha Ön hozta létre az API Management-példányt, akkor rendszergazdának számít. A rendszergazdák alapértelmezés szerint minden termékre elő vannak fizetve.

    Alapértelmezés szerint az API Management minden példányához az alábbi két mintatermék jár:

    * **Kezdő**
    * **Korlátlan**   
7. Kattintson a **Létrehozás** gombra.

## <a name="populate-azure-functions-keys-in-azure-api-management"></a>Azure Functions-kulcsok feltöltése az Azure API Managementben

Ha az importált Azure Functions-alkalmazásokat kulcsok védik, akkor az Azure API Management automatikusan létrehoz hozzájuk *névvel ellátott értékeket*. Az API Management nem tölti fel titkos kulcsokkal a bejegyzéseket. Hajtsa végre mindegyik bejegyzésre vonatkozóan a következő lépéseket:  

1. Lépjen az API Management-példány **Névvel ellátott értékek** lapjára.
2. Kattintson egy bejegyzésre, majd az oldalsáv **Érték megjelenítése** elemére.

    ![Névvel ellátott értékek](./media/import-function-app-as-api/apim-named-values.png)

3. Ha az **Érték** mezőben megjelenő szöveg hasonlít az **\<Azure Functions-név\> kódjához**, lépjen a **Functions-alkalmazások**, majd a **Functions** részre.
4. Válassza ki a **Kezelés** lehetőséget, és másolja ki az alkalmazás hitelesítési metódusának megfelelő kulcsot.

    ![Functions-alkalmazás – Kulcsok másolása](./media/import-function-app-as-api/azure-functions-app-keys.png)

5. Illessze be a kulcsot az **Érték** mezőbe, majd válassza a **Mentés** lehetőséget.

    ![Functions-alkalmazás – Kulcsértékek beillesztése](./media/import-function-app-as-api/apim-named-values-2.png)

## <a name="test-the-new-api-management-api-in-the-azure-portal"></a>Az új API Management API tesztelése az Azure Portalon

Az Azure Portalról közvetlenül meghívhat műveleteket. Az Azure Portalon kényelmesen megtekintheti és tesztelheti az API-k műveleteit.  

1. Válassza ki az előző szakaszban létrehozott API-t.
2. Kattintson a **Teszt** fülre.
3. Válasszon ki egy művelet.

    Az oldalon megjelennek a lekérdezési paraméterek és a fejlécek mezői. Az egyik fejléc az **Ocp-Apim-Subscription-Key**, amely az ehhez az API-hoz társított termék előfizetési kulcsára vonatkozik. Ha Ön hozta létre az API Management-példányt, akkor már eleve rendszergazdának számít, így a kulcsot automatikusan kitölti a rendszer. 
4. Kattintson a **Küldés** gombra.

    A háttérrendszer a **200 OK** üzenetet és néhány adatot küld válaszként.

## <a name="call-operation"></a>Művelet meghívása a fejlesztői portálról

A műveletek meghívhatók a fejlesztői portálról is az API-k teszteléséhez. 

1. Válassza ki a [Háttérrendszerbeli API importálása és közzététele](#create-api) című lépésben létrehozott API-t.
2. Válassza ki a **fejlesztői portált**.

    Megnyílik a fejlesztői portál webhelye.
3. Jelölje ki a létrehozott **API**-t.
4. Válassza ki a tesztelni kívánt műveletet.
5. Kattintson a **Kipróbálás** lehetőségre.
6. Kattintson a **Küldés** gombra.
    
    A művelet meghívása után a fejlesztői portál megjeleníti a **Válasz állapota**, a **Válasz fejlécei** és a **Válasz tartalma** minden információját.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Közzétett API átalakítása és védelme](transform-api.md)