---
title: OpenAPI-specifikáció importálása az Azure Portal használatával | Microsoft Docs
description: Ez a cikk ismerteti, hogyan importálhat OpenAPI-specifikációt az API Managementtel.
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
ms.openlocfilehash: b1602af59cd46b4bbcefa6f1df3cd84c0d342b71
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52959699"
---
# <a name="import-an-openapi-specification"></a>OpenAPI-specifikáció importálása

Ez a cikk bemutatja, hogyan importálható egy „OpenAPI-specifikációjú” háttérrendszeri API, amely a https://conferenceapi.azurewebsites.net?format=json helyen található. Ezt a háttérrendszeri API-t a Microsoft biztosítja, és az Azure-ban üzemel. A cikk az APIM API tesztelését is ismerteti.

> [!IMPORTANT]
> Az OpenAPI-importálásról ebben a [dokumentumban](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/) talál fontos információt és cikkeket.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * „OpenAPI-specifikációjú” háttérrendszeri API importálása
> * Az API tesztelése az Azure Portalon
> * Az API tesztelése a fejlesztői portálon

## <a name="prerequisites"></a>Előfeltételek

Végezze el a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Háttérrendszeri API importálása és közzététele

1. Válassza ki az **API-kat** az **API MANAGEMENT** részben.
2. Az **Új API hozzáadása** listáról válassza az **OpenAPI-specifikáció** lehetőséget.
    ![OpenAPI-specifikáció](./media/import-api-from-oas/oas-api.png)
3. Adja meg a megfelelő beállításokat. Minden API-értéket beállíthat a létrehozás során. Másik lehetőségként néhányukat később is megadhatja a **Beállítások** lapon. <br/> Ha lenyomja a **Tab** billentyűt, a rendszer feltölti némelyik (vagy az összes) mezőt a háttérszolgáltatásból származó megadott információval.

    ![API létrehozása](./media/api-management-get-started/create-api.png)

    |Beállítás|Érték|Leírás|
    |---|---|---|
    |**OpenAPI-specifikáció**|https://conferenceapi.azurewebsites.net?format=json|Az API-t alkalmazó szolgáltatásra hivatkozik. Az API Management erre a címre továbbítja a kérelmeket.|
    |**Megjelenített név**|*Demo Conference API*|Ha a szolgáltatás URL-címének beírása után lenyomja a Tab billentyűt, az APIM kitölti ezt a mezőt a JSON-fájl tartalma alapján. <br/>Ez a név a fejlesztői portálon jelenik meg.|
    |**Name (Név)**|*demo-conference-api*|Egyedi nevet ad meg az API-nak. <br/>Ha a szolgáltatás URL-címének beírása után lenyomja a Tab billentyűt, az APIM kitölti ezt a mezőt a JSON-fájl tartalma alapján.|
    |**Leírás**|Adjon meg egy opcionális leírást az API-hoz.|Ha a szolgáltatás URL-címének beírása után lenyomja a Tab billentyűt, az APIM kitölti ezt a mezőt a JSON-fájl tartalma alapján.|
    |**API URL-címének utótagja**|*conference*|Az utótag az API Management szolgáltatás kiindulási URL-címéhez van hozzáfűzve. Az API Management az API-kat az utótag alapján különbözteti meg, ezért az utótagnak egy adott közzétevő minden API-ja esetében egyedinek kell lennie.|
    |**URL-séma**|*HTTPS*|Meghatározza, mely protokollok használatával lehet hozzáférni az API-hoz. |
    |**Termékek**|*Korlátlan*| Az API egy termékkel való társítással tehető közzé. Ha hozzá kívánja adni az új API-t egy termékhez, írja be a termék nevét. Ez a lépés többször is megismételhető, így az az API több termékhez is hozzáadható.<br/>A termékek egy vagy több API társításai. Megadhatja az API-k számát, és a fejlesztői portálon elérhetővé teheti őket a fejlesztők számára. A fejlesztőknek elő kell fizetniük a termékre az API-k eléréséhez. Amikor előfizetnek, kapnak egy előfizetési kulcsot, amely a termék minden API-jához használható. Ha Ön hozta létre az APIM-példányt, akkor már eleve rendszergazdának számít, így alapértelmezés szerint minden termékre előfizetett.<br/> Alapértelmezés szerint az API Management minden példányához az alábbi két mintatermék jár: **Starter** és **Unlimited**. |

4. Kattintson a **Létrehozás** gombra.

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Az új APIM API tesztelése az Azure Portalon

A műveleteket meg lehet hívni közvetlenül az Azure Portalról, ami kényelmes módot biztosít az API műveleteinek megtekintésére és tesztelésére.

![API tesztelése](./media/api-management-get-started/01-import-first-api-01.png)

1. Válassza ki az előző lépésben létrehozott API-t.
2. Kattintson a **Teszt** fülre.
3. Kattintson a **GetSpeakers** elemre.

    A lapon megjelennek a lekérdezési paraméterek mezői, de ebben az esetben nincs ilyen. A lapon a fejlécek mezői is megjelennek. Az ehhez az API-hoz társított termék előfizetői azonosítójának egyik fejléce „Ocp-Apim-Subscription-Key” értékű. Ha Ön hozta létre az APIM-példányt, akkor már eleve rendszergazdának számít, így a kulcsot automatikusan kitölti a rendszer.
4. Kattintson a **Küldés** gombra.

    A háttér a **200 OK** üzenetet és néhány adatot küld válaszként.

## <a name="call-operation"> </a>Művelet meghívása a fejlesztői portálról

A **fejlesztői portálról** is meghívhat műveleteket az API-k teszteléséhez.

1. Válassza ki a „Háttérrendszeri API importálása és közzététele” című lépésben létrehozott API-t.
2. Nyomja meg a **Fejlesztői portál** gombot.

    ![Tesztelés a fejlesztői portálon](./media/api-management-get-started/developer-portal.png)

    Megnyílik a Fejlesztői portál webhely.
3. Válassza ki az **API** lehetőséget.
4. Válassza a **Demo Conference API** lehetőséget.
5. Kattintson a **GetSpeakers** elemre.

    A lapon megjelennek a lekérdezési paraméterek mezői, de ebben az esetben nincs ilyen. A lapon a fejlécek mezői is megjelennek. Az ehhez az API-hoz társított termék előfizetői azonosítójának egyik fejléce „Ocp-Apim-Subscription-Key” értékű. Ha Ön hozta létre az APIM-példányt, akkor már eleve rendszergazdának számít, így a kulcsot automatikusan kitölti a rendszer.
6. Kattintson a **Kipróbálás** gombra.
7. Kattintson a **Küldés** gombra.

    A művelet meghívása után a fejlesztői portál megjeleníti a **Válasz állapota**, a **Válasz fejlécei** és a **Válasz tartalma** minden információját.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Közzétett API átalakítása és védelme](transform-api.md)
