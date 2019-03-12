---
title: 'Gyors útmutató: A LUIS-portál alkalmazás üzembe helyezése'
titleSuffix: Language Understanding - Azure Cognitive Services
description: Miután az alkalmazás készen áll a utterance (kifejezés) előrejelzéseket térjen vissza egy ügyfélalkalmazás, például csevegőrobotot, kell az előrejelzési végpont szeretné telepíteni az alkalmazást. Ebben a rövid útmutatóban megismerheti az alkalmazás központi előrejelzési végponti erőforrás létrehozása, az erőforrás hozzárendelése az alkalmazáshoz, az alkalmazás képzés és az alkalmazás közzététele.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 9c6ce826569b9c198e747a7977de3d346b1d3230
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57783136"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Gyors útmutató: A LUIS-portál alkalmazás telepítése

Miután az alkalmazás készen áll a utterance (kifejezés) előrejelzéseket térjen vissza egy ügyfélalkalmazás, például csevegőrobotot, kell az előrejelzési végpont szeretné telepíteni az alkalmazást. 

Ebben a rövid útmutatóban megismerheti az alkalmazás központi előrejelzési végponti erőforrás létrehozása, az erőforrás hozzárendelése az alkalmazáshoz, az alkalmazás képzés és az alkalmazás közzététele. 

## <a name="prerequisites"></a>Előfeltételek

* [Azure-előfizetés](https://azure.microsoft.com/free).
* Végezze el a [előző portal rövid útmutatójának](get-started-portal-build-app.md) vagy [letöltése és importálása az alkalmazás](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json). 


## <a name="create-endpoint-resource"></a>Végponti erőforrás létrehozása

Az előrejelzési végponti erőforrás jön létre az Azure Portalon. Ehhez az erőforráshoz csak használandó végpont előrejelzési lekérdezések. Ne használja ezt az erőforrást módosítások az alkalmazás szerzői műveletekhez részben. 

1. Jelentkezzen be a  **[az Azure portal](https://ms.portal.azure.com/)**. 

1. Válassza ki a zöld **+** jelentkezzen be a felső bal oldali panelen, és keresse meg `Cognitive Services` a Marketplace-en, majd válassza ki azt. 

1. Az előfizetés konfigurálása a következő beállításokkal:

    |Beállítás|Érték|Cél|
    |--|--|--|
    |Name (Név)|`my-cognitive-service-resource`|Az Azure-erőforrás neve. Ha az erőforrás rendel az alkalmazáshoz, a LUIS portálon kell ezt a nevet.|
    |Előfizetés|Az Ön előfizetése|Válasszon ki egy a a fiókjához társított előfizetéseket.|
    |Hely|**USA nyugati régiója**|Az azure-régióban ehhez az erőforráshoz.|
    |Tarifacsomag|**S0**|Az alapértelmezett tarifacsomag ehhez az erőforráshoz.|
    |Erőforráscsoport|`my-cognitive-service-resource-group`|Hozzon létre egy új erőforráscsoportot, a cognitive Services-szolgáltatás-erőforrások. Amikor elkészült, az erőforrások, törölheti az erőforráscsoportot törölni az előfizetést. | 

    ![Az Azure API kiválasztása](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png) 

1. Válassza ki **létrehozás** az Azure-erőforrás létrehozásához. 

    A következő szakaszban megismerheti, hogyan az új erőforrás csatlakozni a LUIS-alkalmazások, a LUIS-portálon. 

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Az erőforráskulcs hozzárendelése a LUIS-alkalmazás a LUIS-portálon

Minden alkalommal, amikor egy új erőforrást hoz létre a LUIS, akkor hozzá kell rendelni az erőforrás a LUIS-alkalmazás. Ha van hozzárendelve, nem kell újra el ezt a lépést, ha nem hoz létre egy új erőforrást. Létrehozhat egy új erőforrást, bontsa ki a régiók, az alkalmazás vagy egy előrejelzési lekérdezések nagyobb számának támogatásához. 

1. Jelentkezzen be a [LUIS portál](https://www.luis.ai), válassza ki a **myEnglishApp** alkalmazást a listáról.

1. Válassza ki **kezelés** a jobb felső menüben, majd válassza ki **kulcsokat és a végpontok**.

1. A LUIS hozzáadásához válassza ki a **erőforrás hozzárendelése +**.

    [![Rendelje hozzá egy erőforrást az alkalmazáshoz](./media/get-started-portal-deploy-app/assign-resource-button.png)](./media/get-started-portal-deploy-app/assign-resource-button.png#lightbox)

1. Válassza ki a bérlő, az előfizetés és az erőforráscsoport nevét, majd válassza ki **erőforrás hozzárendelése**. 

    ![Rendelje hozzá egy erőforrást az alkalmazáshoz](./media/get-started-portal-deploy-app/assign-resource.png)

1. Keresse meg az új sort a táblázatban, és másolja a végpont URL-címe. Helyesen kialakítani, hogy egy HTTP GET kérés előrejelzési az intelligens HANGFELISMERÉSI API-végponthoz. 

## <a name="train-and-publish-the-app"></a>Az alkalmazás betanítása és közzététele 

A vonat kell minden alkalommal, amikor készen áll teszteléséhez. Tegyen közzé az alkalmazást, ha azt szeretné, hogy a jelenleg betanított verziót, hogy az ügyfélalkalmazások számára elérhetőnek lennie az előrejelzési végpont modul. 

1. Ha az alkalmazás kellő, jelölje be **Train** a felső menüben jobb.

1. Válassza ki **közzététel** a felső menüben jobb. Fogadja el az alapértelmezett környezet beállításokat, és válassza ki **közzététel**.

1. Amikor a zöld, sikeres értesítési sáv jelenik meg a böngészőablakban tetején, válassza ki a **tekintse meg a végpontok listáját** hivatkozásra. 

    ![Alkalmazás közzététele sikerült értesítési sáv böngészőben](./media/get-started-portal-deploy-app/successfully-published-notification.png)

1. Ezzel továbblép a **kulcsokat és a végpont beállításait** lap. A lista hozzárendelt erőforrások és a kapcsolódó végpont URL-címek van, az oldal alján. 

1. Válassza ki a végponthoz társított URL-címet az új erőforrás neve. Győződjön meg arról, hogy helyesen kialakítani URL-címet egy webböngészőben megnyílik egy **első** az előrejelzési végpont modul irányuló kérelem. 

1. A `q=` az URL-cím végén található rövid a **lekérdezés** , és ahol a felhasználó utterance (kifejezés) a rendszer hozzáfűzi a GET-kérés. Miután a `q=`, adja meg az ugyanazon felhasználói utterance (kifejezés) használt a korábbi rövid útmutató végén:

    ```Is there a form named hrf-234098```

    A böngésző választ kap az ügyfélalkalmazás azonos JSON:

    ```JSON
    {
    "query": "Is there a form named hrf-234098",
    "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9768753
    },
    "intents": [
        {
        "intent": "FindForm",
        "score": 0.9768753
        },
        {
        "intent": "None",
        "score": 0.0216071066
        }
    ],
    "entities": [
        {
        "entity": "hrf-234098",
        "type": "Human Resources Form Number",
        "startIndex": 22,
        "endIndex": 31
        }
      ]
    }
    ```

    Ez a válasz biztosít további információkat, mint az alapértelmezett **teszt** ablaktáblán az előző oktatóanyagban. Ha meg szeretné tekinteni a ezen a teszt ablaktábla ugyanazon szintjét, közzé kell tenni az alkalmazást. Ezt követően a teszt panelt, válassza **hasonlítsa össze a közzétett**. Használat **megjelenítése JSON-nézet** a téve az azonos JSON tekintheti meg az előző lépésben ablaktáblán. Ez lehetővé teszi, hogy az aktuális alkalmazás éppen dolgozik, és az alkalmazást, amelyet közzé van-e a végpont összehasonlítása.

    [![A jelenleg szerkesztett és alkalmazás közzétett verziójának összehasonlítása](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Amikor elkészült, az ebben a rövid, válassza ki a **saját alkalmazások** a felső navigációs menüjében. Ezután válassza ki a bal oldali jelölőnégyzetet az alkalmazást a listából, és válassza ki **törlése** a környezet eszköztáron az eszközlista feletti. 

[![Alkalmazás törlése a saját alkalmazások listája](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Közös szándékok és entitások azonosítása](/luis-tutorial-prebuilt-intents-entities.md)