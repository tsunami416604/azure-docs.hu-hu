---
title: Az első API importálása és közzététele az Azure API Management szolgáltatásban | Microsoft Docs
description: Megismerheti, hogyan importálhatja és teheti közzé az első API-t az API Management szolgáltatással.
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
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 4173c0b26b2d176549d3a89cc6fdfa928b6cca5b
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52963844"
---
# <a name="import-and-publish-your-first-api"></a>Az első API importálása és közzététele 

Ez az oktatóanyag bemutatja a https://conferenceapi.azurewebsites.net?format=json helyen található „OpenAPI-specifikációjú” háttérrendszeri API importálását. Ezt a háttérrendszeri API-t a Microsoft biztosítja, és az Azure-ban üzemel. 

A háttérrendszeri API API Management (APIM) szolgáltatásba történő importálása után az APIM API lesz a háttérrendszeri API homlokzata. A háttérrendszeri API importálásakor a forrás-API és az APIM API megegyezik. Az APIM lehetővé teszi a homlokzat igény szerinti testreszabását a háttérrendszeri API módosítása nélkül. További információ: [Az API átalakítása és védelme](transform-api.md). 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az első API importálása
> * Az API tesztelése az Azure Portalon
> * Az API tesztelése a fejlesztői portálon

![Új API](./media/api-management-get-started/created-api.png)

## <a name="prerequisites"></a>Előfeltételek

+ Az [Azure API Management terminológiájának](api-management-terminology.md) ismerete.
+ Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Háttérrendszeri API importálása és közzététele

Ez a szakasz bemutatja az OpenAPI-specifikációjú háttérrendszeri API importálását és közzétételét.
 
1. Válassza ki az **API-kat** az **API MANAGEMENT** részben.
2. A listáról válassza az **OpenAPI-specifikáció** lehetőséget.

    ![API létrehozása](./media/api-management-get-started/create-api.png)

    Az API értékeit a létrehozás során vagy később is megadhatja, a **Beállítások** lapon. A piros csillag a mező mellett azt jelzi, hogy a mező kitöltése kötelező.

    Használja az alábbi táblázat értékeit az első API létrehozásához.

    | Beállítás                   | Érték                                              | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    |---------------------------|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **OpenAPI-specifikáció** | https://conferenceapi.azurewebsites.net?format=json | Az API-t alkalmazó szolgáltatásra hivatkozik. Az API Management erre a címre továbbítja a kérelmeket.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
    | **Megjelenített név**          | *Demo Conference API*                              | Ha a szolgáltatás URL-címének beírása után lenyomja a Tab billentyűt, az APIM kitölti ezt a mezőt a JSON-fájl tartalma alapján. <br/>Ez a név a fejlesztői portálon jelenik meg.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
    | **Name (Név)**                  | *demo-conference-api*                              | Egyedi nevet ad meg az API-nak. <br/>Ha a szolgáltatás URL-címének beírása után lenyomja a Tab billentyűt, az APIM kitölti ezt a mezőt a JSON-fájl tartalma alapján.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
    | **Leírás**           | Adjon meg egy opcionális leírást az API-hoz.        | Ha a szolgáltatás URL-címének beírása után lenyomja a Tab billentyűt, az APIM kitölti ezt a mezőt a JSON-fájl tartalma alapján.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
    | **URL-séma**            | *HTTPS*                                            | Meghatározza, mely protokollok használatával lehet hozzáférni az API-hoz.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | **API URL-címének utótagja**        | *conference*                                       | Az utótag az API Management szolgáltatás kiindulási URL-címéhez van hozzáfűzve. Az API Management az API-kat az utótag alapján különbözteti meg, ezért az utótagnak egy adott közzétevő minden API-ja esetében egyedinek kell lennie.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
    | **Termékek**              | *Korlátlan*                                        | A termékek egy vagy több API társításai. Megadhatja az API-k számát egy termékben, és a fejlesztői portálon elérhetővé teheti őket a fejlesztők számára. <br/>Az API egy termékkel (ebben a példában *Unlimited*) való társítással tehető közzé. Az új API termékhez történő hozzáadásához adja meg a termék nevét (ezt később is megteheti a **Beállítások** lapon). Ez a lépés többször is megismételhető, így az az API több termékhez is hozzáadható.<br/>Ahhoz, hogy a fejlesztők hozzáférhessenek az API-hoz, elő kell fizetniük a termékre. Amikor előfizetnek, kapnak egy előfizetési kulcsot, amely a termék minden API-jához használható. <br/> Ha Ön hozta létre az APIM-példányt, akkor már eleve rendszergazdának számít, így minden termékre előfizetett.<br/> Alapértelmezés szerint az API Management minden példányához az alábbi két mintatermék jár: **Starter** és **Unlimited**. |
    | Új verziót készít az API-ról?         |                                                    | További információ a verziókezelésről: [Az API több verziójának közzététele](api-management-get-started-publish-versions.md)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |

    >[!NOTE]
    > Az API közzétételéhez társítania kell azt egy termékkel. Ezt megteheti a **Beállítások lapon**.

3. Kattintson a **Létrehozás** gombra.

> [!TIP]
> Ha problémát tapasztal saját API-definíciójának importálása során, [tekintse meg az ismert problémák és korlátozások listáját](api-management-api-import-restrictions.md).

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Az új APIM API tesztelése az Azure Portalon

![API-leképezés tesztelése](./media/api-management-get-started/01-import-first-api-01.png)

A műveleteket meg lehet hívni közvetlenül az Azure Portalról, ami kényelmes módot biztosít az API műveleteinek megtekintésére és tesztelésére.

1. Válassza ki az előző lépésben létrehozott API-t (az **API-k** lapról).
2. Kattintson a **Teszt** fülre.
3. Kattintson a **GetSpeakers** elemre. A lapon megjelennek a lekérdezési paraméterek mezői (ilyenek ebben az esetben nincsenek), valamint a fejlécek. Az ehhez az API-hoz társított termék előfizetői azonosítójának egyik fejléce „Ocp-Apim-Subscription-Key” értékű. A kulcsot automatikusan kitölti a rendszer.
4. Kattintson a **Küldés** gombra.

    A háttér a **200 OK** üzenetet és néhány adatot küld válaszként.

## <a name="call-operation"></a>Művelet meghívása a fejlesztői portálról

Műveletek is meghívhatók a **fejlesztői portálról** az API-k teszteléséhez.

1. Lépjen a **fejlesztői portálra**.

    ![Fejlesztői portál](./media/api-management-get-started/developer-portal.png)

2. Kattintson az **API-k**, majd a **Demo Conference API**, majd a **GetSpeakers** lehetőségre.

    A lapon megjelennek a lekérdezési paraméterek mezői (ilyenek ebben az esetben nincsenek), valamint a fejlécek. Az ehhez az API-hoz társított termék előfizetői azonosítójának egyik fejléce „Ocp-Apim-Subscription-Key” értékű. Ha Ön hozta létre az APIM-példányt, akkor már eleve rendszergazdának számít, így a kulcsot automatikusan kitölti a rendszer.

3. Kattintson a **Kipróbálás** gombra.
4. Kattintson a **Küldés** gombra.

    A művelet meghívása után a fejlesztői portál megjeleníti a válaszokat.  

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az első API importálása
> * Az API tesztelése az Azure Portalon
> * Az API tesztelése a fejlesztői portálon

Folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Termékek létrehozása és közzététele](api-management-howto-add-products.md)
