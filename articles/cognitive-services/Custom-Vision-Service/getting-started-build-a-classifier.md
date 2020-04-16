---
title: Rövid útmutató Osztályozó létrehozása – Egyéni vision szolgáltatás
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
ms.openlocfilehash: 56bdaa324420bf274e7cda8ac1c6506e4bc9ad21
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404058"
---
# <a name="quickstart-how-to-build-a-classifier-with-custom-vision"></a>Rövid útmutató: Osztályozó készítése egyéni látással

Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre osztályozót a Custom Vision webhelyen keresztül. Miután létrehoz egy osztályozó modellt, használhatja a Custom Vision szolgáltatást a képbesoroláshoz.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

- Képek készlete, amelyekkel betaníthatja az osztályozót. Az alábbiakban tippeket talál a képek kiválasztásához.

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Egyéni Vision-erőforrások létrehozása az Azure Portalon

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Új projekt létrehozása

A webböngészőben keresse meg a [Custom Vision weblapot,](https://customvision.ai) és válassza __a Bejelentkezés__lehetőséget. Jelentkezzen be ugyanazzal a fiókkal, amelyet az Azure Portalon használt.

![A bejelentkezési oldal képe](./media/browser-home.png)


1. Az első projekt létrehozásához válassza az **Új projekt**lehetőséget. Megjelenik **az Új projekt létrehozása** párbeszédpanel.

    ![Az új projekt párbeszédpanelen a név, a leírás és a tartományok mezői vannak.](./media/getting-started-build-a-classifier/new-project.png)

1. Adja meg a projekt nevét és leírását. Ezután válasszon egy erőforráscsoportot. Ha a bejelentkezett fiók egy Azure-fiókhoz van társítva, az erőforráscsoport legördülő menü megjeleníti az összes Azure-erőforráscsoportok, amelyek tartalmazzák a Custom Vision Service Resource. 

   > [!NOTE]
   > Ha nem áll rendelkezésre erőforráscsoport, ellenőrizze, hogy ugyanazzal a fiókkal jelentkezett-e be [customvision.ai,](https://customvision.ai) mint amelyet az [Azure Portalra](https://portal.azure.com/)való bejelentkezéshez használt. Azt is ellenőrizze, hogy ugyanazt a "Címt" választotta-e az egyéni látásportálon, mint az Azure portalon, ahol az egyéni látási erőforrások találhatók. Mindkét oldalon kiválaszthatja a könyvtárat a képernyő jobb felső sarkában található legördülő fiók menüből. 

1. Válassza a __Besorolás__ lehetőséget a __Projekttípusok csoportban.__ Ezután __a Besorolási típusok csoportban__válassza a **Többcímkét** vagy **a Többosztályos**lehetőséget a használati esettől függően. A többcímkés besorolás tetszőleges számú címkét alkalmaz egy képre (nulla vagy több), míg a többosztályos besorolás a képeket egyetlen kategóriákba rendezi (minden beküldött kép a legvalószínűbb címkébe lesz rendezve). A besorolás típusát később módosíthatja, ha szeretné.

1. Ezután válassza ki az elérhető tartományok egyikét. Minden tartomány az alábbi táblázatban leírtak szerint optimalizálja az osztályozót bizonyos típusú képekhez. Ön képes lesz arra, hogy módosítsa a domain később, ha szeretné.

    |Domain|Cél|
    |---|---|
    |__Általános__| Képbesorolási feladatok széles körére optimalizálva. Ha a többi tartomány egyike sem megfelelő, vagy nem biztos abban, hogy melyik tartományt válassza, válassza az Általános tartományt. |
    |__Élelmiszer__|Az ételek fényképeire optimalizálva, ahogy az éttermi menüben látni fogja őket. Ha az egyes gyümölcsökről vagy zöldségekről készült fényképeket szeretné osztályozni, használja az Élelmiszer domaint.|
    |__Tereptárgyak__|Felismerhető tereptárgyakra optimalizálva, természetes és mesterséges. Ez a domain akkor működik a legjobban, ha a mérföldkő jól látható a fényképen. Ez a domain akkor is működik, ha a mérföldkő kissé akadályozza az emberek előtte.|
    |__Kiskereskedelem__|Bevásárlókatalógusban vagy vásárlási webhelyen található képekre optimalizálva. Ha azt szeretnénk, nagy pontosságú osztályozása között ruhák, nadrágok, és ingek, használja ezt a tartományt.|
    |__Tömörített tartományok__| A mobileszközökön történő valós idejű besorolás korlátaira optimalizálva. A kompakt tartományok által létrehozott modellek exportálhatók helyi futtatásra.|

1. Végül válassza a __Projekt létrehozása lehetőséget.__

## <a name="choose-training-images"></a>Képzési képek kiválasztása

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

Ebben a szakaszban képeket tölthet fel és címkézhet manuálisan az osztályozó betanításához. 

1. Képek hozzáadásához kattintson a __Képek hozzáadása__ gombra, majd a __Helyi fájlok tallózása parancsra.__ A __Megnyitás gombra__ a címkézésre való ugráshoz válassza a Megnyitás gombot. A címkekiválasztása a feltöltendő képek teljes csoportjára lesz alkalmazva, így könnyebb a képeket külön csoportokba feltölteni a kívánt címkék nek megfelelően. Az egyes képek címkéit a feltöltésük után is módosíthatja.

    ![A képek hozzáadása vezérlő a bal felső sarokban, alul középen pedig gombként jelenik meg.](./media/getting-started-build-a-classifier/add-images01.png)


1. Címke létrehozásához írja be a szöveget a __Saját címkék__ mezőbe, és nyomja le az Enter billentyűt. Ha a címke már létezik, az megjelenik egy legördülő menüben. Többcímkétes projektekben több címkét is hozzáadhat a képekhez, de egy többosztályos projektben csak egyet. A képek feltöltésének befejezéséhez használja a __[szám] fájlok feltöltése__ gombot. 

    ![A címke és a feltöltési oldal képe](./media/getting-started-build-a-classifier/add-images03.png)

1. A képek feltöltése után válassza a __Kész__ lehetőséget.

    ![A folyamatjelző sáv az összes befejezett feladatot mutatja.](./media/getting-started-build-a-classifier/add-images04.png)

Egy másik képkészlet feltöltéséhez térjen vissza a szakasz tetejére, és ismételje meg a lépéseket.

## <a name="train-the-classifier"></a>Az osztályozó betanítása

Az osztályozó betanításához válassza a **Vonat** gombot. Az osztályozó az összes aktuális lemezképet egy olyan modell létrehozásához használja, amely azonosítja az egyes címke vizuális tulajdonságait.

![A betanítás gomb a weblap fejlécének eszköztárának jobb felső részén](./media/getting-started-build-a-classifier/train01.png)

A képzési folyamat csak néhány percet vesz igénybe. Ez alatt az idő alatt a betanítási folyamattal kapcsolatos információk a **Teljesítmény** lapon jelennek meg.

![A főszakaszban található oktatópárbeszédpanellel rendelkező böngészőablak](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>Az osztályozó kiértékelése

A betanítás befejezése után a modell teljesítményét a rendszer megbecsüli és megjeleníti. A Custom Vision Service a betanításra beküldött képek alapján számítja ki a pontosságot és a visszahívást a [k-szeres keresztérvényesítés](https://en.wikipedia.org/wiki/Cross-validation_(statistics))nevű folyamat segítségével. A precizitás és a visszahívás az osztályozó hatékonyságának két különböző mérése:

- **A pontosság** az azonosított osztályozások helyes hányadát jelzi. Például, ha a modell azonosított 100 kép, mint a kutyák, és 99 közülük valójában a kutyák, akkor a pontosság lenne 99%.
- **A Visszahívás** a tényleges osztályozások helyesen azonosított hányadát jelzi. Például, ha valóban 100 kép az alma, és a modell azonosított 80 alma, a visszahívás lenne 80%.

![Az edzéseredmények az osztályozó minden egyes címkéjének általános pontosságát és visszahívását mutatják.](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>Valószínűségi határérték

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Képzési ismétlések kezelése

Minden alkalommal, amikor betanítja az osztályozót, új _iterációt_ hoz létre a saját frissített teljesítménymutatóival. Az összes ismétlést megtekintheti a **Teljesítmény** lap bal oldali ablaktáblájában. A **Törlés** gomb is megtalálható, amellyel törölheti az iterációt, ha az elavult. Az iteráció törlésekor törli az egyedileg társított képeket.

Lásd: [A modell használata az előrejelzési API-val](./use-prediction-api.md) a betanított modellek programozott elérésének megismerése című témakört.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre és taníthat be egy képbesorolási modellt a Custom Vision webhely használatával. Ezután további információt kaphat a modell fejlesztésének iteratív folyamatáról.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](test-your-model.md)

