---
title: 'Oktatóanyag: Mérsékelt e-kereskedelmi termékképek - Tartalommoderátor'
titleSuffix: Azure Cognitive Services
description: Ez az oktatóanyag bemutatja, hogyan állíthat be egy alkalmazást a termékképek elemzéséhez és besorolásához megadott címkékkel (az Azure Computer Vision és a Custom Vision használatával). Címke kifogásolható képeket kell tovább vizsgálni (az Azure Content Moderator használatával).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 5e74eda9e30c536c0eba4e847019344c87e10cce
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774340"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Oktatóanyag: Mérsékelt e-kereskedelmi termékképek az Azure-tartalommoderátorral

Ebben az oktatóanyagban megtudhatja, hogyan használhatja az Azure Cognitive Servicest, beleértve a tartalommoderátort is, az e-kereskedelmi forgatókönyvek termékképeinek besorolásához és moderálására. A Computer Vision és a Custom Vision segítségével címkéket (címkéket) alkalmazhat a képekre, majd létrehoz egy csapatértékelést, amely egyesíti a Tartalommoderátor gépi tanuláson alapuló technológiáit az emberi felülvizsgálati csapatokkal, hogy intelligens moderálási rendszert biztosítson.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Regisztráljon a tartalommoderátorra, és hozzon létre egy felülvizsgálati csapatot.
> * A Content Moderator Image API-jának használata a lehetséges felnőtteknek szóló és kényes tartalom keresésére.
> * A Computer Vision szolgáltatással hírességek tartalmait (vagy más, a Computer-Vision által kimutatható címkéket) kereshet.
> * A Custom Vision szolgáltatás segítségével keresse meg a jelzők, játékok és tollak (vagy más egyéni címkék) jelenlétét.
> * Mutassa be a kombinált vizsgálati eredményeket az emberi felülvizsgálat és a végső döntéshozatal.

A teljes mintakód a [Minták e-kereskedelmi katalógus moderálástárházban](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) érhető el a GitHubon.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

- A Content Moderator előfizetői azonosítója. Kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) című részben található utasításokat, hogy előiratkozzon a Tartalommoderátor szolgáltatásra, és bekésezse a kulcsot.
- A Computer Vision előfizetési kulcs (ugyanazokat az utasításokat, mint fent).
- A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/)bármely kiadása.
- A Custom Vision osztályozó által használt címkék (ebben az esetben játékok, tollak és amerikai jelzők) képei.

## <a name="create-a-review-team"></a>Felülvizsgálati csoport létrehozása

A [Tartalommoderátor kipróbálása a weben](quick-start.md) című rövid útmutatóban tájékán megtudhatja, hogyan regisztrálhat a [Tartalommoderátor-ellenőrző eszközre,](https://contentmoderator.cognitive.microsoft.com/) és hogyan hozhat létre felülvizsgálati csoportot. Vegye figyelembe a **Csapatazonosító** értékét a **Hitelesítő adatok** lapon.

## <a name="create-custom-moderation-tags"></a>Egyéni moderálási címkék létrehozása

Ezután hozzon létre egyéni címkéket a Véleményezés eszközben (ha segítségre van szüksége a [Címkék](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) cikkben). Ebben az esetben a következő címkéket adjuk hozzá: **híresség**, **USA**, **zászló**, **játék**és **toll.** Nem minden címke kell kimutatható kategóriák Computer Vision (mint **a híresség);** hozzáadhatja saját egyéni címkéit, feltéve, hogy betanítja a Custom Vision osztályozót, hogy később észlelje őket.

![Egyéni címkék konfigurálása](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. A Visual Studióban nyissa meg az Új projekt párbeszédpanelt. Bontsa **ki a Installed**elemet, majd a Visual **C#** lehetőséget, majd válassza **a Console app (.NET Framework)** lehetőséget.
1. Nevezze el az **alkalmazást EcommerceModeration**, majd kattintson **az OK gombra.**
1. Ha ezt a projektet egy meglévő megoldáshoz adja hozzá, válassza ki ezt a projektet egyetlen indítási projektként.

Ez az oktatóanyag kiemeli a projekt központi kódját, de nem fedi le az összes kódsort. Másolja a _Program.cs_ teljes tartalmát a mintaprojektből ([Minták e-kereskedelmi katalógus moderálása](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) az új projekt _Program.cs_ fájljába. Ezután lépjen végig a következő szakaszokon, és ismerje meg, hogyan működik a projekt, és hogyan használhatja saját maga.

## <a name="define-api-keys-and-endpoints"></a>API-kulcsok és végpontok definiálása

Ez az oktatóanyag három kognitív szolgáltatást használ; ezért három megfelelő kulcsot és API-végpontot igényel. A **Program** osztály következő mezői:

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

Frissítenie kell a `___Key` mezőket az előfizetési kulcsok értékeivel, `___Uri` és a mezőket a megfelelő végpontURL-ekre kell módosítania (később megkapja az Egyéni látás kulcsot és végpontot). Ezeket az értékeket az egyes Azure-erőforrások **rövid útmutatójában** találja. Töltse ki `YOURTEAMID` a `ReviewUri` mező nek a korábban létrehozott ellenőrző csapat azonosítójával. A `CustomVisionUri` mező utolsó részét később ki kell töltenie.

[!INCLUDE [subdomains note](../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="primary-method-calls"></a>Elsődleges metódushívások

Tekintse meg a következő kódot a **Fő** metódusban, amely végighalad a kép URL-ek listáján. Elemzi az egyes képeket a három különböző szolgáltatással, rögzíti az alkalmazott címkéket a **ReviewTags** tömbben, majd létrehozza az emberi moderátorok felülvizsgálatát a képek elküldésével a Tartalommoderátor ellenőrző eszköznek. Ezeket a módszereket a következő szakaszokban ismertetheti. Ha szeretné, szabályozhatja, hogy mely képeket küldje át a rendszer, a **ReviewTags** tömb feltételes utasításban, hogy ellenőrizze, mely címkéket alkalmazta.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>ÉrtékeljeAdultRacy módszer

Tekintse meg az **EvaluateAdultRacy** metódust a **Program** osztályban. Ez a módszer egy kép URL-címét és egy kulcs-érték párok tömbjét veszi paraméterekként. Meghívja a tartalommoderátor image API-ját (REST használatával), hogy megkapja a kép felnőtt és pikáns pontszámait. Ha bármelyik pontszáma nagyobb, mint 0,4 (a tartomány 0 és 1 között van), akkor a **Korrektok tömb** megfelelő értékét Igaz értékre **állítja.**

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecomputervisiontags-method"></a>EvaluateComputerVisionTags metódus

A következő módszer egy kép URL-címét és a Computer Vision előfizetési adatait, és elemzi a képet a hírességek jelenlétét. Ha egy vagy több híresség található, akkor a **Korrektorok** tömb megfelelő értékét Igaz értékre **állítja.**

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>EvaluateCustomVisionTags metódus

Ezután tekintse meg az **EvaluateCustomVisionTags** metódust,&mdash;amely ebben az esetben a tényleges termékeket sorolja be a jelzőket, játékokat és tollakat. Kövesse a [Hogyan hozhat létre egy osztályozó](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) útmutatót, hogy saját egyéni képosztályozót hozzon létre, és észlelje a jelzőket, játékokat és tollakat (vagy bármit, amit egyéni címkékként választott) a képekben. A [GitHub-tárházban](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) lévő **mintaképek** mappájában lévő képek segítségével gyorsan betaníthatja a példában szereplő kategóriák némelyikét.

![Egyéni Vision weboldal tollak, játékok és zászlók képzési képeivel](images/tutorial-ecommerce-custom-vision.PNG)

Miután betanította az osztályozót, kérje le az előrejelzési kulcsot és az előrejelzési végpont URL-címét (lásd: Az `CustomVisionKey` `CustomVisionUri` URL és [az előrejelzési kulcs beolvasása,](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key) ha segítségre van szüksége a beolvasásukhoz), és rendelje hozzá ezeket az értékeket a saját és a mezőkhöz. A metódus ezeket az értékeket használja az osztályozó lekérdezéséhez. Ha az osztályozó egy vagy több egyéni címkét talál a képen, ez a módszer a **Korrektorok** tömb megfelelő értékét igaz értékre **állítja.**

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Vélemények létrehozása a Véleményezés eszközhöz

Az előző szakaszokban azt vizsgálta, hogy az alkalmazás hogyan vizsgálja meg a beérkező képeket felnőtt és pikáns tartalom (Content Moderator), hírességek (Computer Vision) és számos egyéb objektum (Custom Vision) számára. Ezután tekintse meg a **CreateReview** metódust, amely feltölti a képeket az összes alkalmazott címkével _(metaadatként_átadva) a Tartalommoderátor ellenőrző eszközébe.

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

A képek a [Tartalommoderátor-ellenőrző eszköz](https://contentmoderator.cognitive.microsoft.com/)Véleményezés lapján jelennek meg.

![Képernyőkép a Tartalommoderátor-ellenőrző eszközről, amely több képet és azok kiemelt címkéit tartalmaz](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Tesztképek listájának beküldése

Mint látható a **Fő** módszer, ez a program keres egy "C:Test" könyvtár egy _Urls.txt_ fájlt, amely tartalmazza a kép URL-eket. Hozza létre ezt a fájlt és könyvtárat, vagy módosítsa az elérési utat úgy, hogy a szövegfájlra mutasson. Ezután feltöltheti ezt a fájlt a tesztelni kívánt képek URL-címével.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>A program futtatása

Ha a fenti lépések mindegyikét követte, a programnak fel kell dolgoznia az egyes képeket (mindhárom szolgáltatást le kell kérdeznie a megfelelő címkékért), majd fel kell töltenie a címkeinformációkat tartalmazó képeket a Tartalommoderátor ellenőrző eszközébe.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban beállíthat egy programot a termékképek elemzésére, terméktípus szerint címkézve, és lehetővé teszi, hogy a felülvizsgálati csoport megalapozott döntéseket hozzon a tartalom moderálásáról. Ezután tudjon meg többet a képmoderálás részleteiről.

> [!div class="nextstepaction"]
> [Moderált képek áttekintése](./review-tool-user-guide/review-moderated-images.md)
