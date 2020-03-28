---
title: SOAP API importálása az Azure Portal használatával | Microsoft Docs
description: Megismerheti, hogyan importálhatja a SOAP API-t az API Managementtel.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: 359b90cc434dad04fc0296c54fcc762f3a75062d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74107660"
---
# <a name="import-soap-api"></a>SOAP API importálása

A cikk bemutatja, hogyan importálhatja egy SOAP API szabványos XML-ábrázolásását. A cikk az APIM API tesztelését is ismerteti.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * SOAP API importálása
> * Az API tesztelése az Azure Portalon
> * Az API tesztelése a fejlesztői portálon

## <a name="prerequisites"></a>Előfeltételek

A következő rövid útmutató befejezése: [Hozzon létre egy Azure API Management-példányt](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Háttér-API importálása és közzététele

1. Válassza ki az **API-kat** az **API MANAGEMENT** részben.
2. Az **Új API hozzáadása** listában válassza a **WSDL** elemet.

    ![Soap api](./media/import-soap-api/wsdl-api.png)
3. A **WSDL-specifikáció** mezőben adja meg azt az URL-címet, ahol a SOAP API található.
4. Alapértelmezés szerint a **SOAP típusú továbbítás** választógomb van kijelölve. Így az API SOAP-ként lesz elérhető. A fogyasztónak a SOAP-szabályokat kell használnia. Ha REST API-t szeretne használni, kövesse a [SOAP API importálását és REST-konvertálását](restify-soap-api.md) ismertető szakasz lépéseit.

    ![Továbbítás](./media/import-soap-api/pass-through.png)
5. Nyomja le a Tab billentyűt.

    A rendszer kitölti a következő mezőket a SOAP API-ból származó adatokkal: Megjelenített név, Név, Leírás.
6. Adja hozzá az API URL-cím utótagját. Az utótag lesz a név, amely azonosítja az API-t ebben az APIM-példányban. Egyedinek kell lennie az APIM-példányon belül.
9. Az API egy termékkel való társítással tehető közzé. Ebben az esetben az „*Unlimited*” terméket használjuk.  Ha közzé szeretné tenni az API-t, hogy elérhető legyen a fejlesztők számára, adja hozzá egy termékhez. Ezt megteheti az API létrehozása során, vagy később is.

    A termékek egy vagy több API társításai. Megadhatja az API-k számát, és a fejlesztői portálon elérhetővé teheti őket a fejlesztők számára. A fejlesztőknek elő kell fizetniük a termékre az API-k eléréséhez. Amikor előfizetnek, kapnak egy előfizetési kulcsot, amely a termék minden API-jához használható. Ha Ön hozta létre az APIM-példányt, akkor már eleve rendszergazdának számít, így alapértelmezés szerint minden termékre előfizetett.

    Alapértelmezés szerint az API Management minden példányához az alábbi két mintatermék jár:

    * **Kezdő**
    * **Korlátlan**   
10. Kattintson a **Létrehozás** gombra.

### <a name="test-the-new-api-in-the-administrative-portal"></a>Az új API tesztelése a felügyeleti portálon

A műveleteket meg lehet hívni közvetlenül a felügyeleti portálról, ami kényelmes módot biztosít az API műveleteinek megtekintésére és tesztelésére.  

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