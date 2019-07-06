---
title: 'Oktatóanyag: Mérsékelt e-kereskedelmi Termékképek - Content Moderator'
titlesuffix: Azure Cognitive Services
description: Állítsa be egy alkalmazás elemzésére és Termékképek besorolása, a megadott címkék (Azure Computer Vision és a Custom Vision segítségével). A címke nem kívánatos képek tovább felül (az Azure Content Moderator használatával).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: ec17f9f0206ef639bd47d694880c064a012ea1cf
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604192"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Oktatóanyag: Termékképek mérsékelt e-kereskedelem az Azure Content Moderator

Ebben az oktatóanyagban elsajátíthatja az Azure Cognitive Services, beleértve a Content Moderator használatát besorolásához és mérsékelt Termékképek egy e-kereskedelmi forgatókönyvben. Fogja használni a Computer Vision és egyéni vizuális címkéjét szeretné alkalmazni (címkék) képeket, és ezután létrehozhat egy csapat tekintse át, amely egyesíti a Content Moderator machine learning-alapú technológiák emberi vizsgálóeszközt csapataival együttműködve biztosít olyan intelligens moderálás.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * A Content Moderator regisztráljon, és tekintse át a csoport létrehozása.
> * A Content Moderator Image API-jának használata a lehetséges felnőtteknek szóló és kényes tartalom keresésére.
> * A Computer Vision service segítségével ellenőrizze a hírességek tartalom (vagy más számítógép-Látástechnológia – cserélhető eszközként észlelhetőnek címkék).
> * A Custom Vision service segítségével jelzők, a játékok, és tollak (vagy más egyéni címkéket) meglétének ellenőrzése.
> * Jelenleg az emberi ellenőrző és végső döntéshozatal kombinált vizsgálat eredményeit.

A teljes minta kódja megtalálható a [e-kereskedelmi katalógusok moderálása minták](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) tárházban a Githubon.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

- A Content Moderator előfizetői azonosítója. Kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a Content Moderator szolgáltatás és a kulcs beszerzése.
- A Computer Vision előfizetési kulcs (a fenti ugyanezen utasításokat).
- A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/) bármely kiadása.
- Képkészlet egyes címkékhez, amelyek a Custom Vision osztályozó fogja használni (ez megkülönbözteti a kis játékok, tollak és jelzők USA).

## <a name="create-a-review-team"></a>Tekintse át a csoport létrehozása

Tekintse meg a [próbálja ki a Content Moderator, a weben](quick-start.md) regisztrálni létrehozásával kapcsolatos útmutatást a rövid útmutatóban a [Content Moderator, tekintse át az eszköz](https://contentmoderator.cognitive.microsoft.com/) , és tekintse át a csoport létrehozása. Jegyezze fel a **Csapatazonosító** érték a **hitelesítő adatok** lapot.

## <a name="create-custom-moderation-tags"></a>Egyéni moderálás címkék létrehozása

Ezután hozzon létre egyéni címkéket a felülvizsgálati eszközben (lásd a [címkék](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) . Ha ez a folyamat segítségre van szüksége a cikk). Ebben az esetben hozzáadjuk a következő címkékkel: **hírességek**, **USA**, **jelző**, **kisfia**, és **toll**. Címkék mindegyikével nem kell lennie a Computer Vision cserélhető eszközként észlelhetőnek kategóriák (például **hírességek**); egyéni címkéket adhat hozzá, mindaddig, amíg a Custom Vision osztályozó által igénybe vett észre később a vonat.

![Egyéni címkék konfigurálása](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. A Visual Studióban nyissa meg az új projekt párbeszédpanel. Bontsa ki a **telepített**, majd **Visual C#** , majd **Console app (.NET Framework)** .
1. Adjon nevet az alkalmazásnak **EcommerceModeration**, majd kattintson a **OK**.
1. Ha egy meglévő megoldás adja hozzá a projekthez, jelölje ki a projektet az egyetlen kezdőprojektként.

Ez az oktatóanyag kiemeli a kódot, amely a projekt központi, de a kódsor nem foglalkozik. Másolja ki a teljes tartalmának _Program.cs_ a mintaprojektet a ([e-kereskedelmi katalógusok moderálása minták](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)), a _Program.cs_ fájlt az új projekt. Ezt követően lépésenként az alábbi szakaszok további információt a projekt működését, és hogyan használható a saját maga.

## <a name="define-api-keys-and-endpoints"></a>API-kulcsok és a végpontok megadása

Ebben az oktatóanyagban három a cognitive services; Ezért van szükség három tartozó kulcsok és API-végpontokon. Tekintse meg a következő mezőket a **Program** osztály:

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

Frissíteni kell a `___Key` mezőket az értékeket az előfizetési kulcsok (fog kapni a `CustomVisionKey` később), és előfordulhat, hogy módosítania a `___Uri` mezők, így a megfelelő régióban azonosítók tartalmaznak. Töltse ki a `YOURTEAMID` része a `ReviewUri` azonosítójú, a korábban létrehozott csapatától mezőt. Utolsó része lesz kitölti a `CustomVisionUri` később mezőben.

## <a name="primary-method-calls"></a>Elsődleges metódust hívja

Tekintse meg az alábbi kódot a **fő** metódussal, amely végighalad a kép URL-címek listáját. Azt elemzi az egyes rendszerképek és a három különböző szolgáltatások, az alkalmazott címkék rögzíti a **ReviewTags** tömböt, és majd létrehoz egy az emberi moderátorok a képek küld a Content Moderator tekintse át eszköz. Ezek a módszerek az alábbi szakaszokban található felderíti. Ha szeretné, szabályozhatja, tekintse át, melyik lemezképek küldött használatával a **ReviewTags** tömböt, ellenőrizze, hogy mely címkéket alkalmazott feltételes utasításban.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>EvaluateAdultRacy metódus

Tekintse meg a **EvaluateAdultRacy** metódus az a **Program** osztály. Ez a módszer paraméterekként egy kép URL-címe és a egy tömb, kulcs-érték párok vesz igénybe. A felnőtt és Racy pontszámok annak a lemezképnek beolvasásához meghívja a Content Moderator Image API (REST használatával). Ha a pontszám a nagyobb, mint a 0,4 (a tartományt a 0 és 1 között), azt a megfelelő értéket állít be a **ReviewTags** a tárolótömb **igaz**.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecomputervisiontags-method"></a>EvaluateComputerVisionTags metódus

A következő metódust vesz igénybe egy kép URL-címe és a Computer Vision előfizetési információk és elemzi a kép hírességek meglétének. Ha egy vagy több hírességek talál, azt a megfelelő értéket állít be a **ReviewTags** a tárolótömb **igaz**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>EvaluateCustomVisionTags metódus

Ezután tekintse meg a **EvaluateCustomVisionTags** metódussal, amely a tényleges termékek osztályozza&mdash;ebben az esetben megőrzendő tartalomként jelöli meg, a játékok, és tollak. Kövesse az utasításokat a [besorolás létrehozása](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) útmutató, amellyel hozhat létre saját egyéni rendszerkép osztályozó és jelzőket, a játékok, és tollak (vagy bármilyen, az egyéni címkék választott) észlelése a képeken. A lemezképeket is használhatja a **minta-lemezképek** mappában található a [GitHub-adattárat](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) gyorsan betanításához néhány ebben a példában kategóriája.

![Egyéni vizuális weblapját a betanító kép tollak, a játékok és jelzők](images/tutorial-ecommerce-custom-vision.PNG)

Után az osztályozó által igénybe vett már betanított, az előrejelzési és előrejelzési végponti URL-cím beszerzése (lásd: [URL-cím és az előrejelzési kulcs lekérése](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key) segítséget lekérése őket), és rendelje hozzá ezeket az értékeket a `CustomVisionKey` és `CustomVisionUri` mezők, illetve. A metódus az osztályozó által igénybe vett lekérdezéséhez ezeket az értékeket használja. Az osztályozó által igénybe vett talál a kép egy vagy több egyéni címkével, ha ez a módszer beállítja a megfelelő értékeket a **ReviewTags** a tárolótömb **igaz**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Felülvizsgálati eszköz felülvizsgálatának létrehozása

Az előző szakaszokban megismerte, hogyan az alkalmazás megvizsgálja a bejövő képeket a felnőtt és szexuális tartalom (Content Moderator), hírességek (a Computer Vision) és számos más objektumok (Custom Vision). Ezután tekintse meg a **CreateReview** metódussal, amely feltölti a lemezképeket az összes alkalmazott címkétől (átadott, _metaadatok_), a Content Moderator felülvizsgálati eszközben.

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

A képek felülvizsgálati lapján fog megjelenni a [Content Moderator felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/).

![A Content Moderator felülvizsgálati eszköz számos olyan rendszerkép található, és a kiemelt címkétől képernyőképe](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Teszt rendszerképek listájának elküldése

Amint láthatja, hogy az a **fő** metódus, a program "C:Test" címtár keres egy _Urls.txt_ kép URL-címek listáját tartalmazó fájl. Ez a fájl és könyvtár létrehozása, vagy módosítsa az elérési utat a szöveg fájlra mutasson. Ezután töltse fel az URL-címeket, a képek, tesztelni szeretné a fájlt.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>A program futtatása

Ha a fenti lépéseket követte, a program kell feldolgozni az egyes rendszerképek (lekérdezés mindhárom szolgáltatást a megfelelő címkék) és majd feltölti a lemezképeket az eszközcímke-információ a Content Moderator felülvizsgálati eszközben.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban beállíthatja egy programot Termékképek elemzési, terméktípus címkézése őket, és lehetővé teszi egy csapatától kapcsolatos tartalom-jóváhagyás felelős döntéseket hozhat. Ezután további információ a képmoderálás részleteit.

> [!div class="nextstepaction"]
> [Moderált rendszerképek felülvizsgálata](./review-tool-user-guide/review-moderated-images.md)
