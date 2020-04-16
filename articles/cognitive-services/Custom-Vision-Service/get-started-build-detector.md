---
title: Rövid útmutató Objektumdetektor létrehozása – Egyéni látásszolgáltatás
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban megtudhatja, hogyan használhatja a Custom Vision webhelyet egy lemezkép-besorolási modell létrehozásához.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: ba121400368f37c4a562a9c34e209c59d15b173c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404112"
---
# <a name="quickstart-how-to-build-an-object-detector-with-custom-vision"></a>Rövid útmutató: Objektumdetektor létrehozása egyéni látással

Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre objektumdetektort a Custom Vision webhelyen keresztül. Miután létrehozegy detektormodellt, használhatja a Custom Vision szolgáltatást az objektumészleléshez.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

- Az érzékelő modell betanításához készült képkészlet. A GitHubon használhatja a [mintaképek](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision/images) készletét. Vagy kiválaszthatja saját képeit az alábbi tippek segítségével.

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Egyéni Vision-erőforrások létrehozása az Azure Portalon

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Új projekt létrehozása

A webböngészőben keresse meg a [Custom Vision weblapot,](https://customvision.ai) és válassza __a Bejelentkezés__lehetőséget. Jelentkezzen be ugyanazzal a fiókkal, amelyet az Azure Portalon használt.

![A bejelentkezési oldal képe](./media/browser-home.png)


1. Az első projekt létrehozásához válassza az **Új projekt**lehetőséget. Megjelenik **az Új projekt létrehozása** párbeszédpanel.

    ![Az új projekt párbeszédpanelen a név, a leírás és a tartományok mezői vannak.](./media/get-started-build-detector/new-project.png)

1. Adja meg a projekt nevét és leírását. Ezután válasszon egy erőforráscsoportot. Ha a bejelentkezett fiók egy Azure-fiókhoz van társítva, az erőforráscsoport legördülő menü megjeleníti az összes Azure-erőforráscsoportok, amelyek tartalmazzák a Custom Vision Service Resource. 

   > [!NOTE]
   > Ha nem áll rendelkezésre erőforráscsoport, ellenőrizze, hogy ugyanazzal a fiókkal jelentkezett-e be [customvision.ai,](https://customvision.ai) mint amelyet az [Azure Portalra](https://portal.azure.com/)való bejelentkezéshez használt. Azt is ellenőrizze, hogy ugyanazt a "Címt" választotta-e az egyéni látásportálon, mint az Azure portalon, ahol az egyéni látási erőforrások találhatók. Mindkét oldalon kiválaszthatja a könyvtárat a képernyő jobb felső sarkában található legördülő fiók menüből. 

1. Válassza az __Objektumészlelés__ lehetőséget a __Projekttípusok csoportban.__

1. Ezután válassza ki az elérhető tartományok egyikét. Minden tartomány az alábbi táblázatban leírtak szerint optimalizálja az érzékelőt bizonyos típusú képekhez. Ön képes lesz arra, hogy módosítsa a domain később, ha szeretné.

    |Domain|Cél|
    |---|---|
    |__Általános__| Objektumészlelési feladatok széles körére optimalizálva. Ha a többi tartomány egyike sem megfelelő, vagy nem biztos abban, hogy melyik tartományt válassza, válassza az Általános tartományt. |
    |__Embléma__|Márkalogók keresésére optimalizálva a képeken.|
    |__Tömörített tartományok__| A mobileszközökön történő valós idejű objektumészlelés korlátaira optimalizálva. A kompakt tartományok által létrehozott modellek exportálhatók helyi futtatásra.|

1. Végül válassza a __Projekt létrehozása lehetőséget.__

## <a name="choose-training-images"></a>Képzési képek kiválasztása

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

Ebben a részben feltöltési és manuális címke képeket, hogy segítsen a vonat az érzékelő. 

1. Képek hozzáadásához kattintson a __Képek hozzáadása__ gombra, majd a __Helyi fájlok tallózása parancsra.__ A képek feltöltéséhez válassza a __Megnyitás__ lehetőséget.

    ![A képek hozzáadása vezérlő a bal felső sarokban, alul középen pedig gombként jelenik meg.](./media/get-started-build-detector/add-images.png)

1. A feltöltött képek a felhasználói felület **Címkézetlen** szakaszában jelennek meg. A következő lépés az, hogy manuálisan tag az objektumokat, amelyeket meg szeretne tanulni az érzékelő felismerni. Kattintson az első képre a címkézési párbeszédpanel ablakának megnyitásához. 

    ![Feltöltött képek a Címkézetlen szakaszban](./media/get-started-build-detector/images-untagged.png)

1. Kattintson a kép objektuma köré, és húzzon egy téglalapot. Ezután írjon be egy **+** új címkenevet a gombbal, vagy jelöljön ki egy meglévő címkét a legördülő listából. Nagyon fontos, hogy a felcímkézni kívánt objektum(ok) minden példányát megcímkézze, mert az érzékelő a címkézetlen háttérterületet használja negatív példaként a betanítássorán. Ha végzett a címkézéssel, kattintson a jobb oldali nyílra a címkék mentéséhez, és lépjen tovább a következő képre.

    ![Objektum címkézése téglalap alakú kijelöléssel](./media/get-started-build-detector/image-tagging.png)

Egy másik képkészlet feltöltéséhez térjen vissza a szakasz tetejére, és ismételje meg a lépéseket.

## <a name="train-the-detector"></a>Az érzékelő betanítása

Az érzékelő modell betanításához válassza a **Vonat** gombot. Az érzékelő az összes aktuális képet és azok címkéit egy olyan modell létrehozásához használja, amely azonosítja az egyes címkézett objektumokat.

![A betanítás gomb a weblap fejlécének eszköztárának jobb felső részén](./media/getting-started-build-a-classifier/train01.png)

A képzési folyamat csak néhány percet vesz igénybe. Ez alatt az idő alatt a betanítási folyamattal kapcsolatos információk a **Teljesítmény** lapon jelennek meg.

![A főszakaszban található oktatópárbeszédpanellel rendelkező böngészőablak](./media/get-started-build-detector/training.png)

## <a name="evaluate-the-detector"></a>Az érzékelő értékelése

A betanítás befejezése után a modell teljesítményét kiszámítja és megjeleníti. A Custom Vision szolgáltatás a betanításra beküldött képek alapján kiszámítja a pontosságot, a visszahívást és az átlagos átlagos pontosságot. A precizitás és a visszahívás a detektor hatékonyságának két különböző mérése:

- **A pontosság** az azonosított osztályozások helyes hányadát jelzi. Például, ha a modell azonosított 100 kép, mint a kutyák, és 99 közülük valójában a kutyák, akkor a pontosság lenne 99%.
- **A Visszahívás** a tényleges osztályozások helyesen azonosított hányadát jelzi. Például, ha valóban 100 kép az alma, és a modell azonosított 80 alma, a visszahívás lenne 80%.

![Az edzéseredmények az általános pontosságot és visszahívást, valamint az átlagos átlagos pontosságot mutatják.](./media/get-started-build-detector/trained-performance.png)

### <a name="probability-threshold"></a>Valószínűségi határérték

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Képzési ismétlések kezelése

Minden alkalommal, amikor betanítja az érzékelőt, új _iterációt_ hoz létre a saját frissített teljesítménymutatóival. Az összes ismétlést megtekintheti a **Teljesítmény** lap bal oldali ablaktáblájában. A bal oldali ablaktáblában található a **Törlés** gomb is, amellyel törölheti az ismétlést, ha az elavult. Az iteráció törlésekor törli az egyedileg társított képeket.

Lásd: [A modell használata az előrejelzési API-val](./use-prediction-api.md) a betanított modellek programozott elérésének megismerése című témakört.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre és taníthat be egy objektumérzékelő-modellt a Custom Vision webhely használatával. Ezután további információt kaphat a modell fejlesztésének iteratív folyamatáról.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](test-your-model.md)

