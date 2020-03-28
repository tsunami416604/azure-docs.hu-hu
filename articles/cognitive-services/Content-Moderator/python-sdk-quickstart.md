---
title: 'Rövid útmutató: Tartalommoderátor ügyféltár pythonhoz'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan kezdheti el az Azure Cognitive Services tartalommoderátor ügyféltár pythonhoz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 31cdc9663283b580acc10e7ac8d5a77f9036a7a8
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772355"
---
# <a name="quickstart-content-moderator-client-library-for-python"></a>Rövid útmutató: Tartalommoderátor ügyféltár pythonhoz

Első lépések a Tartalommoderátor python-alapú ügyféltárral. Az alábbi lépésekkel telepítheti a csomagot, és kipróbálhatja az alapvető feladatok példakódját. A tartalommoderátor egy kognitív szolgáltatás, amely ellenőrzi a szöveg-, kép- és videotartalmakat olyan anyagok esetében, amelyek potenciálisan sértőek, kockázatosak vagy más módon nemkívánatosak. Ha ilyen anyagot talál, a szolgáltatás megfelelő címkéket (jelölőket) alkalmaz a tartalomra. Az alkalmazás ezután kezelheti a megjelölt tartalmakat, így betarthatja az előírásokat, és fenntarthatja a felhasználók számára kívánt környezetet.

Használja a Python tartalommoderátor ügyfélkönyvtárát a következőkhöz:

* [Mérsékelt szöveg](#moderate-text)
* [Egyéni kifejezéslista használata](#use-a-custom-terms-list)
* [Mérsékelt képek](#moderate-images)
* [Egyéni képlista használata](#use-a-custom-image-list)
* [Vélemény létrehozása](#create-a-review)

[Referenciadokumentáció](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/contentmoderator?view=azure-python) | [könyvtár forráskódcsomag](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator) | [(PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/) | [minták](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Beállítása

### <a name="create-a-content-moderator-azure-resource"></a>Tartalommoderátor Azure-erőforrás létrehozása

Az Azure Cognitive Servicest olyan Azure-erőforrások képviselik, amelyekre előfizet. Hozzon létre egy erőforrást a tartalommoderátor számára az [Azure Portalon](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI-n](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) keresztül a helyi gépen. További lehetőségek:

* Kap egy [próbakulcs](https://azure.microsoft.com/try/cognitive-services/#decision) érvényes részére hét napok részére szabad. A regisztráció után elérhető lesz az [Azure webhelyén.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Az erőforrás megtekintése az [Azure Portalon](https://portal.azure.com/)

Miután lekért egy kulcsot a próba-előfizetés vagy erőforrás, [hozzon létre környezeti változók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) at a kulcs és a végpont URL-cím, nevű, `CONTENT_MODERATOR_SUBSCRIPTION_KEY` illetve. `CONTENT_MODERATOR_ENDPOINT`
 
### <a name="create-a-python-script"></a>Python-parancsfájl létrehozása

Hozzon létre egy új Python-parancsfájlt, és nyissa meg a kívánt szerkesztőben vagy IDE-ben. Ezután adja `import` hozzá a következő állításokat a fájl tetejéhez.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imports)]

Ezután hozzon létre változókat az erőforrás végponti helyéhez és a kulcshoz környezeti változóként. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_vars)]

> [!NOTE]
> Ha az alkalmazás elindítása után hozta létre a környezeti változókat, be kell zárnia, majd újra meg kell nyitnia a változók eléréséhez futtató szerkesztőt, IDE-t vagy rendszerhéjat.

### <a name="install-the-client-library"></a>Az ügyféltár telepítése

A Tartalommoderátor ügyféltár a következő paranccsal telepíthető:

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

## <a name="object-model"></a>Objektummodell

A következő osztályok a Tartalommoderátor Python SDK néhány főbb funkcióját kezelik.

|Név|Leírás|
|---|---|
|[ContentModerator-ügyfél](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python)|Ez az osztály szükséges a Tartalommoderátor összes funkciójához. Az előfizetési adatokkal azonnal létrehozhatja azt, és más osztályok példányainak létrehozásához használhatja őket.|
|[ImageModerationOperations (ImageModerationOperations)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python)|Ez az osztály biztosítja a felnőtteknek szánt tartalmak, személyes adatok vagy emberi arcok képeinek elemzéséhez szükséges funkciókat.|
|[TextModerationOperations (Szövegmoderációs műveletek)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)|Ez az osztály biztosítja a nyelvi, káromkodási, hiba- és személyes adatok szövegének elemzéséhez szükséges funkciókat.|
[VéleményekÜzemeltetés](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python)|Ez az osztály a véleményezési API-k funkcióit biztosítja, beleértve a munkahelyek létrehozásának módszereit, az egyéni munkafolyamatokat és az emberi véleményeket.|

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan kell elvégezni a következő feladatokat a Python tartalommoderátor ügyféltárával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Mérsékelt szöveg](#moderate-text)
* [Egyéni kifejezéslista használata](#use-a-custom-terms-list)
* [Mérsékelt képek](#moderate-images)
* [Egyéni képlista használata](#use-a-custom-image-list)
* [Vélemény létrehozása](#create-a-review)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE]
> Ez a rövid útmutató feltételezi, hogy [környezeti változókat hozott létre](../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) a tartalommoderátor kulcsához és végpontjához.

Ügyfél példányosítsa meg a végpontot és a kulcsot. Hozzon létre egy [CognitiveServicesCredentials objektumot](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) a kulccsal, és használja azt a végponttal [egy ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python) objektum létrehozásához.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_client)]

## <a name="moderate-text"></a>Mérsékelt szöveg

A következő kód egy tartalommoderátor-ügyfél segítségével elemzi a szövegtörzset, és kinyomtatja az eredményeket a konzolra. Először hozzon létre egy **text_files/** mappát a projekt gyökerében, és adjon hozzá egy *content_moderator_text_moderation.txt* fájlt. Adja hozzá saját szövegét a fájlhoz, vagy használja a következő mintaszöveget:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 2065550111
```

Hivatkozás hozzáadása az új mappához.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

Ezután adja hozzá a következő kódot a Python-parancsfájlhoz.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textmod)]

## <a name="use-a-custom-terms-list"></a>Egyéni kifejezéslista használata

Az alábbi kód bemutatja, hogyan kezelhető a szövegmoderáláshoz egyéni kifejezések listája. A [ListManagementTermListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations?view=azure-python) osztály segítségével létrehozhat egy kifejezéslistát, kezelheti az egyes kifejezéseket, és más szövegrészeket is megvizsgálhat vele.

### <a name="get-sample-text"></a>Mintaszöveg beszereznie

A minta használatához létre kell hoznia egy **text_files/** mappát a projekt gyökerében, és hozzá kell adnia egy *content_moderator_term_list.txt* fájlt. Ennek a fájlnak olyan szerves szöveget kell tartalmaznia, amelyet összevetanek a kifejezések listájával. A következő mintaszöveget használhatja:

```
This text contains the terms "term1" and "term2".
```

Ha még nem definiált hivatkozást a mappához, adjon hozzá hivatkozást a mappához.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

### <a name="create-a-list"></a>Lista létrehozása

Adja hozzá a következő kódot a Python-parancsfájlhoz egy egyéni kifejezéslista létrehozásához és az azonosítóérték mentéséhez.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_create)]

### <a name="define-list-details"></a>Listarészleteinek meghatározása

A lista azonosítójával szerkesztheti a nevét és a leírását.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_details)]

### <a name="add-a-term-to-the-list"></a>Kifejezés hozzáadása a listához

A következő kód `"term1"` hozzáadja a feltételeket és `"term2"` a listát.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_add)]

### <a name="get-all-terms-in-the-list"></a>Az összes kifejezés beszereznie a listában

A listaazonosító segítségével a lista összes feltételét visszaadhatja.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_getterms)]

### <a name="refresh-the-list-index"></a>A listaindex frissítése

Amikor kifejezéseket ad hozzá vagy távolít el a listából, a frissített lista használata előtt frissítenie kell az indexet.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_refresh)]

### <a name="screen-text-against-the-list"></a>Képernyőszöveg a listához

Az egyéni kifejezések listájának fő funkciója a szövegtörzs összehasonlítása a listával, és annak kiderítése, hogy vannak-e egyező kifejezések. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_screen)]

### <a name="remove-a-term-from-a-list"></a>Kifejezés eltávolítása a listából

A következő kód eltávolítja a kifejezést `"term1"` a listából.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_remove)]

### <a name="remove-all-terms-from-a-list"></a>Az összes kifejezés eltávolítása a listából

A következő kód dal törölheti az összes kifejezés listáját.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_removeall)]

### <a name="delete-list"></a>Lista törlése

Az alábbi kód segítségével törölheti az egyéni kifejezések listáját.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_deletelist)]

## <a name="moderate-images"></a>Mérsékelt képek

A következő kód egy tartalommoderátor-ügyfelet, valamint egy [ImageModerationOperations objektumot](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python) használ a felnőtteknek és pikáns tartalmaknak a képek elemzésére.

### <a name="get-images"></a>Képek bekérése

Adjon meg hivatkozást néhány elemzandó képre.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemodvars)]

Ezután adja hozzá a következő kódot a képeken keresztül. A többi kód ebben a szakaszban megy ebbe a hurokba.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Felnőtt/pikáns tartalom ellenőrzése

A következő kód ellenőrzi a képet a megadott URL-címen a felnőtt vagy pikáns tartalom, és kinyomtatja az eredményeket a konzolra. A [képmoderálási útmutatóban](./image-moderation-api.md) további információt talál arról, hogy mit jelentenek ezek a kifejezések.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_ar)]

### <a name="check-for-visible-text"></a>Látható szöveg ellenőrzése

A következő kód ellenőrzi a képen a látható szöveg tartalmát, és kinyomtatja az eredményeket a konzolra.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Arcok ellenőrzése

A következő kód ellenőrzi a képet az emberi arcok és a nyomatok eredményeket a konzolra.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_face)]

## <a name="use-a-custom-image-list"></a>Egyéni képlista használata

A következő kód bemutatja, hogyan kezelheti a képek egyéni listáját a képmoderáláshoz. Ez a funkció akkor hasznos, ha a platform gyakran kap példányokat az azonos képkészlet, amelyet ki szeretne szűrni. Az adott képek listájának karbantartásával javíthatja a teljesítményt. A [ListManagementImageListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementimagelistsoperations?view=azure-python) osztály lehetővé teszi egy képlista létrehozását, a listán szereplő egyes képek kezelését és a többi kép összehasonlítását.

Hozza létre a következő szövegváltozókat az ebben a forgatókönyvben használt képURL-ek tárolásához.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelistvars)]

> [!NOTE]
> Ez nem maga a megfelelő lista, hanem a kód `add images` szakaszában megjelenő képek informális listája.


### <a name="create-an-image-list"></a>Képlista létrehozása

Adja hozzá a következő kódot egy képlista létrehozásához, és mentse a hivatkozást az azonosítójára.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_create)]

### <a name="add-images-to-a-list"></a>Képek hozzáadása listához

A következő kód az összes képet hozzáadja a listához.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_add)]

A add_images **segítő** függvénydefiniálása a parancsfájl más részein.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_addhelper)]

### <a name="get-images-in-list"></a>Képek bekerülése a listában

A következő kód a listában szereplő összes kép nevét nyomtatja ki.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getimages)]

### <a name="update-list-details"></a>Lista részleteinek frissítése

A listaazonosító segítségével frissítheti a lista nevét és leírását.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_updatedetails)]

### <a name="get-list-details"></a>Lista részleteinek beszereznie

A lista aktuális adatainak nyomtatásához használja az alábbi kódot.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getdetails)]

### <a name="refresh-the-list-index"></a>A listaindex frissítése

A képek hozzáadása vagy eltávolítása után frissítenie kell a listaindexet, mielőtt más képek et is megszűrhetne vele.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_refresh)]

### <a name="match-images-against-the-list"></a>Képek egyeztetése a listával

A képlisták fő funkciója az új képek összehasonlítása és az egyezések megtekintése.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_match)]

### <a name="remove-an-image-from-the-list"></a>Kép eltávolítása a listából

A következő kód eltávolít egy elemet a listából. Ebben az esetben ez egy olyan kép, amely nem felel meg a listakategóriának.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_remove)]

### <a name="remove-all-images-from-a-list"></a>Az összes kép eltávolítása a listából

A következő kód dalával törölheti a képlistát.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_removeall)]

### <a name="delete-the-image-list"></a>A képlista törlése

Adott képlista törléséhez használja a következő kódot.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_delete)]

## <a name="create-a-review"></a>Vélemény létrehozása

A Tartalommoderátor Python SDK segítségével tartalmakat táplálhat be a [Véleményezés eszközbe,](https://contentmoderator.cognitive.microsoft.com) hogy az emberi moderátorok áttekinthessék azt. Ha többet szeretne megtudni a Véleményezés eszközről, olvassa el a [Felülvizsgálati eszköz fogalmi útmutatóját.](./review-tool-user-guide/human-in-the-loop.md)

A következő kód a [ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python) osztály segítségével hozzon létre egy felülvizsgálatot, kérje le az azonosítóját, és ellenőrizze annak részleteit, miután megkapta az emberi bevitelt a Felülvizsgálati eszköz webportálján keresztül.

### <a name="get-review-credentials"></a>Hitelesítő adatok áttekintése

Először jelentkezzen be a Véleményezés eszközbe, és olvassa be a csapat nevét. Ezután rendelje hozzá a kód megfelelő változójához. Szükség esetén beállíthat egy visszahívási végpontot, hogy megkapja a frissítéseket az ellenőrzés tevékenységéről.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_vars)]

### <a name="create-an-image-review"></a>Képellenőrzés létrehozása

Adja hozzá a következő kódot az adott kép URL-címének létrehozásához és közzétételéhez. A kód menti a hivatkozást az ellenőrzés azonosítójára. 
[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_create)]

### <a name="get-review-details"></a>Véleményezési részletek beszerezni

Az adott értékelés részleteinek ellenőrzéséhez használja a következő kódot. Miután létrehozta az értékelést, saját kezűleg is megnyissa a Véleményezés eszközt, és használhatja a tartalmat. Ennek módjáról a [Vélemények útmutatójában](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images)talál további információt. Ha végzett, újra futtathatja ezt a kódot, és lekéri az ellenőrzési folyamat eredményeit.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_getdetails)]

Ha ebben a forgatókönyvben visszahívási végpontot használt, az alábbi formátumú eseményt kell kapnia:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az `python` alkalmazást a parancssegítségével a rövid útmutató fájlban.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, hogy tisztítsák meg, és távolítsa el a Cognitive Services-előfizetés, törölheti az erőforrás vagy erőforráscsoport. Az erőforráscsoport törlése a hozzá társított egyéb erőforrásokat is törli.

* [Portál](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja a Tartalommoderátor Python-függvénytárat moderálási feladatok elvégzésére. Ezután tudjon meg többet a képek vagy más médiatartalmak moderálásáról egy koncepcionális útmutató elolvasásával.

> [!div class="nextstepaction"]
>[Képmoderálási fogalmak](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Mi az az Azure Content Moderator?](./overview.md)
* A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py)