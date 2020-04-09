---
title: 'Oktatóanyag: Címkekészítési projekt létrehozása a képbesoroláshoz'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan kezelheti a képek címkézési folyamatát, hogy azok többosztályos képbesorolási modellekben is használhatók legyenek.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.reviewer: ranku
ms.date: 04/02/2020
ms.openlocfilehash: fa33861d86ff8bee3e2a34fb3d93032ac6180880
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879720"
---
# <a name="tutorial-create-a-labeling-project-for-multi-class-image-classification"></a>Oktatóanyag: Címkekészítési projekt létrehozása többosztályos képbesoroláshoz 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ez az oktatóanyag bemutatja, hogyan kezelheti a gépi tanulási modellek hez adatokként használt lemezképek címkézési (más néven címkézési) folyamatát. Az Azure Machine Learning adatcímkézése nyilvános előzetes verzióban történik.

Ha azt szeretné, hogy a betanítása egy gépi tanulási modell a képek besorolásához, több száz vagy akár több ezer kép, amelyek helyesen címkézett.  Az Azure Machine Learning segítségével kezelheti a tartományi szakértőkből álló privát csapat ának előrehaladását, ahogy azok az adatok címkével látják el.
 
Ebben az oktatóanyagban macskák és kutyák képeit fogja használni.  Óta mindegyik kép van egyik egy macska vagy egy kutya, ez egy *multi-- osztály* címkézés tervez. A következőket fogja megtanulni:

> [!div class="checklist"]
>
> * Hozzon létre egy Azure-tárfiókot, és töltsön fel képeket a fiókba.
> * Hozzon létre egy Azure Machine Learning-munkaterületet.
> * Hozzon létre egy többosztályos képcímkézési projektet.
> * Címkézze fel az adatokat.  Ezt a feladatot Ön vagy a címkézők is végrehajthatják.
> * Az adatok áttekintésével és exportálásával fejezze be a projektet.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://aka.ms/AMLFree)

## <a name="create-a-workspace"></a>Munkaterület létrehozása

Az Azure Machine Learning-munkaterület egy alapvető erőforrás a felhőben, amely et a kísérletezés, a betanítás és a gépi tanulási modellek üzembe helyezése. Az Azure-előfizetést és az erőforráscsoportot egy könnyen felhasználható objektumhoz köti a szolgáltatásban.

Hozzon létre egy munkaterületet az Azure Portalon keresztül, egy webalapú konzol az Azure-erőforrások kezelésére.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="start-a-labeling-project"></a>Címkézési projekt indítása

Ezután kezelheti az adatcímkézési projektet az Azure Machine Learning stúdióban, egy konszolidált felületet, amely gépi tanulási eszközöket tartalmaz az adatelemzési forgatókönyvek végrehajtásához az adatelemzési szakemberek számára minden képzettségi szinten. A stúdió nem támogatott az Internet Explorer böngészőiben.

1. Jelentkezzen be az [Azure Machine Learning stúdióba.](https://ml.azure.com)

1. Válassza ki az előfizetést és a létrehozott munkaterületet.

### <a name="create-a-datastore"></a><a name="create-datastore"></a>Adattár létrehozása

Az Azure Machine Learning-adattárak a kapcsolati adatok, például az előfizetés-azonosító és a jogkivonat-engedélyezés tárolására szolgálnak. Itt egy adattár segítségével csatlakozhat a tárfiókhoz, amely az oktatóanyag lemezképeit tartalmazza.

1. A munkaterület bal oldalán válassza az **Adattárak**lehetőséget.

1. Válassza **a + Új adattár lehetőséget.**

1. Töltse ki az űrlapot az alábbi beállításokkal:

    Mező|Leírás 
    ---|---
    Adattár neve | Adjon nevet az adattárnak.  Itt használjuk **labeling_tutorial**.
    Adattár típusa | Válassza ki a tároló típusát.  Itt használjuk **az Azure Blob Storage,** a képek előnyben részesített tárolója.
    Fiókkiválasztási módszer | Válassza **a Manuális bevitel**lehetőséget.
    URL-cím | `https://azureopendatastorage.blob.core.windows.net/openimagescontainer`
    Hitelesítés típusa | Válassza a **SAS-jogkivonat lehetőséget.**
    Fiókkulcs | `?sv=2019-02-02&ss=bfqt&srt=sco&sp=rl&se=2025-03-25T04:51:17Z&st=2020-03-24T20:51:17Z&spr=https&sig=7D7SdkQidGT6pURQ9R4SUzWGxZ%2BHlNPCstoSRRVg8OY%3D`

1. Az adattár létrehozásához válassza a **Létrehozás** gombot.

### <a name="add-labelers-to-workspace"></a>Címkézők hozzáadása a munkaterülethez

Állítsa be úgy a munkaterületet, hogy az tartalmazza az összes olyan embert, aki a projektek adatait címkézi.  Később ezeket a címkézőket hozzáadja az adott címkézési projekthez.

1. A bal oldalon válassza az **Adatfeliratozás**lehetőséget.

1. A lap tetején válassza a **Címkézők**lehetőséget.

1. Válassza **a Címkecímke hozzáadása** lehetőséget a címkéző e-mail címének hozzáadásához.

1. Folytassa további címkézők hozzáadását, amíg el nem készült.

### <a name="create-a-labeling-project"></a>Címkézési projekt létrehozása

Most, hogy megvan a címkézők listája és a megcímkézni kívánt adatokhoz való hozzáférés, hozza létre a címkézési projektet.

1. A lap tetején válassza a **Projektek**lehetőséget.

1. Válassza **a + Projekt hozzáadása**lehetőséget.

    ![Projekt létrehozása](media/tutorial-labeling/create-project.png)

### <a name="project-details"></a>Projekt részletei

1. A **Projekt részletei** űrlaphoz használja a következő bemenetet:

    Mező|Leírás 
    ---|---
    Projektnév | Adjon nevet a projektnek.  Itt fogjuk használni **bemutató-macskák-n-kutyák**.
    Tevékenységtípus címkézése | Válassza **a Képbesorolás többosztályos lehetőséget.**  
    
    A projekt létrehozásának folytatásához válassza a **Tovább** gombot.

### <a name="select-or-create-a-dataset"></a>Adatkészlet kijelölése vagy létrehozása

1.   Az **Adatkészlet kiválasztása vagy létrehozása** űrlapon jelölje ki a második lehetőséget, **hozzon létre egy adatkészletet**, majd válassza ki az **adattárból mutató hivatkozást.**

1. Használja a következő bemenetet az **Adatkészlet létrehozása az adattárból űrlapon:**

    1. Az **alapvető info** űrlapon adjon hozzá egy nevet, itt fogjuk használni **a képeket az oktatóanyaghoz**.  Adjon hozzá leírást, ha szeretné.  Ezután válassza a **Tovább**gombot.
    1. Az **adattár kiválasztási** űrlapján a legördülő menüben válassza ki a **korábban létrehozott adattár ,** például **tutorial_images (Azure Blob Storage)**
    1. Ezután még mindig az **Adattár kiválasztási** űrlapján válassza a **Tallózás,** majd a **MultiClass - DogsCats**lehetőséget.  Válassza a **Mentés** lehetőséget a **/MultiClass - DogsCats** útvonal ként való használatához.
    1. Válassza a **Tovább** gombra a részletek megerősítéséhez, majd a **Létrehozás** lehetőséget az adatkészlet létrehozásához.
    1. Jelölje ki a listában az adatkészlet neve melletti kört, például **az oktatórendszerszámára**készült képeket.

1. A projekt létrehozásának folytatásához válassza a **Tovább** gombot.

### <a name="label-classes"></a>Címkeosztályok

1. A **Címkeosztályok** képernyőn írja be a címke nevét, majd a **+Címke hozzáadása lehetőséget** választva írja be a következő címkét.  Ehhez a projekthez a címkék **macska,** **kutya**és **bizonytalan**.

1. Válassza a **Tovább** gombot, ha az összes címkét hozzáadta.

### <a name="labeling-instructions"></a>Címkézési utasítások

1. A **Címkézési utasítások** képernyőn megadhat egy hivatkozást egy webhelyre, amely részletes utasításokat ad a címkézőknek.  Ez a bemutató üresen hagyja.

1. A tevékenység rövid leírását közvetlenül az űrlapon is hozzáadhatja.  Típus **címkézés bemutató - Macskák & Kutyák.**

1. Válassza a **Tovább lehetőséget.**

1. Az **ML-es segédcímkézési** űrlapon hagyja bejelölve a jelölőnégyzetet. Az ML-es rendszer által támogatott címkézéshez több adatra van szükség, mint amennyit ebben az oktatóanyagban használni fog.

1. Válassza a **Create project** (Projekt létrehozása) lehetőséget.

Ez a lap nem frissül automatikusan. Szünet után manuálisan frissítse a lapot, amíg a projekt állapota **Létrehozva**állapotra nem változik.

### <a name="add-labelers-to-your-project"></a>Címkézők hozzáadása a projekthez

Adja hozzá a címkézőket a projekthez.

1. Válassza ki a projekt nevét a projekt megnyitásához.  

1. A lap tetején válassza a **Csapatok**lehetőséget.

1. Válassza ki **a labeling_tutorial alapértelmezett csapat** hivatkozását.

1. Most **a Címkézők hozzárendelése** segítségével adja hozzá a projektben részt venni kívánt címkézőket. 

1. Válasszon a korábban létrehozott címkézők listájából.  Miután kiválasztotta az összes használni kívánt címkézőt, válassza **a Címkézők hozzárendelése** lehetőséget az alapértelmezett projektcsapathoz való hozzáadásához.

## <a name="start-labeling"></a>Címkézés megkezdése

Most már beállította az Azure-erőforrásokat, és konfigurált egy adatcímkézési projektet. Itt az ideje, hogy címkéket adjon az adatokhoz.

### <a name="notify-labelers"></a>Címkézők értesítése

Ha sok képet címkézni, remélhetőleg sok címkézője is van a feladat elvégzéséhez.  Most el kell küldenie nekik az utasításokat, hogy hozzáférhessenek az adatokhoz, és elkezdhessék a címkézést.

1. A [Machine Learning studióban](https://ml.azure.com)válassza az **Adatcímkézés** lehetőséget a bal oldalon a projekt megkereséséhez.  

1. Jelölje ki a projekt névhivatkozását.

1. A lap tetején válassza a **Részletek lehetőséget.**  Megjelenik a projekt összegzése.

    ![Projekt részletei](media/tutorial-labeling/project-details.png)

1. Másolja a **címkéző portál URL-hivatkozását** a címkézőknek való elküldéshez.

1. Most válassza **a Csapat** a tetején, hogy megtalálja a címkézés csapat.  

1. Jelölje ki a csoportnév hivatkozást.

1. A lap tetején válassza az **E-mail csapat** lehetőséget az e-mail indításához.  Illessze be az imént másolt címkézési portál URL-címét.  

Minden alkalommal, amikor egy címkéző megy a portál URL-címét, akkor jelenik meg több képet kell címkézni, amíg a várólista üres.  

### <a name="tag-the-images"></a>A képek címkézése

Az oktatóanyag ezen részében a szerepköröket a *projektrendszergazdáról* a *címkéző*szerepkörökre válthatja.  Használja a csapatnak küldött URL-címet.  Ez az URL-cím a projekt címkézési portáljára vezet.  Ha hozzáadott utasításokat, akkor látni őket itt, amikor megérkezik az oldalra.

1. A lap tetején válassza a **Tevékenységek** lehetőséget a címkézés megkezdéséhez.

1. Jelöljön ki egy miniatűr képet a jobb oldalon, hogy megjelenítse az egy menetben címkézni kívánt képek számát. A továbblépés előtt fel kell címkéznie ezeket a képeket. Csak akkor váltson elrendezéseket, ha friss, címkézetlen adatokat tartalmazó oldallal rendelkezik. Az elrendezésváltás törli az oldal folyamatban lévő címkézési munkáját.

1. Jelöljön ki egy vagy több képet, majd jelöljön ki egy címkét, amelyet a kijelölésre alkalmazni szeretne. A címke a kép alatt jelenik meg.  Továbbra is jelölje ki és címkézze fel az oldalon lévő összes képet.  Az összes megjelenített kép egyidejű kijelöléséhez jelölje be **az Összes kijelölése**jelölőnégyzetet. Jelöljön ki legalább egy képet a címke alkalmazásához.


    > [!TIP]
    > Az első kilenc címkét a billentyűzet számgombjai segítségével választhatja ki.

1. Miután az oldalon lévő összes kép meg van címkézve, válassza a **Küldés** lehetőséget a címkék elküldéséhez.

    ![Képek címkézése](media/tutorial-labeling/catsndogs.gif)

1. Miután elküldte az aktuális adatok címkéit, az Azure frissíti az oldalt egy új képkészlettel a munkavárólistából.

## <a name="complete-the-project"></a>A projekt befejezése

Most visszavált a szerepkörökre a *projekt rendszergazdájára* a címkézési projekthez.

Menedzserként érdemes lehet áttekintenia a címkéző munkáját.  

### <a name="review-labeled-data"></a>Címkézett adatok áttekintése

1. A [Machine Learning studióban](https://ml.azure.com)válassza az **Adatcímkézés** lehetőséget a bal oldalon a projekt megkereséséhez.  

1. Jelölje ki a projekt névhivatkozását.

1. Az irányítópult a projekt előrehaladását mutatja be.

1. A lap tetején válassza az **Adatok**lehetőséget.

1. A bal oldalon válassza a **Címkézett adatok** lehetőséget a címkézett képek megtekintéséhez.  

1. Ha nem ért egyet egy címkével, jelölje ki a képet, majd válassza az **Elutasítás** lehetőséget az oldal alján.  A címkék törlődnek, és a kép visszakerül a címkézetlen képek várólistájába.

### <a name="export-labeled-data"></a>Címkézett adatok exportálása

A Machine Learning-kísérletezés címkeadatait bármikor exportálhatja. A felhasználók gyakran többször exportálnak, és különböző modelleket tanítanak be, ahelyett, hogy megvárnák az összes kép címkézését.

A képcímkék [COCO formátumban](http://cocodataset.org/#format-data) vagy Azure Machine Learning-adatkészletként exportálhatók. Az adatkészlet formátuma megkönnyíti az Azure Machine Learning betanításához.  

1. A [Machine Learning studióban](https://ml.azure.com)válassza az **Adatcímkézés** lehetőséget a bal oldalon a projekt megkereséséhez.  

1. Jelölje ki a projekt névhivatkozását.

1. Válassza **az Exportálás** és az Exportálás Azure **ML-adatkészletként lehetőséget.** 

    Az exportálás állapota közvetlenül az **Exportálás** gomb alatt jelenik meg. 

1. A címkék sikeres exportálása után válassza az **Adatkészletek** lehetőséget a bal oldalon az eredmények megtekintéséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása


[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a képeket címkézte.  Most használja a címkézett adatokat:

> [!div class="nextstepaction"]
> [Gépi tanulási képfelismerő modell betanítása.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb)
