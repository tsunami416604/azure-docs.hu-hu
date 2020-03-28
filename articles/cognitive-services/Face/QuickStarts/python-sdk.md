---
title: 'Rövid útmutató: Face-ügyfélkódtár pythonhoz'
description: Ez a rövid útmutató segít a Face ügyfélkódtár használatával a Python számára a hasonló felismerések, azonosítás, ellenőrzés és egyebek észleléséhez, megkereséséhez, azonosításához, ellenőrzéséhez és egyebekhez.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 7ac1b85ee44627990931cfc7b3a65f6f7a661b3f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76165814"
---
# <a name="quickstart-face-client-library-for-python"></a>Rövid útmutató: Face-ügyfélkódtár pythonhoz

Ismerkedés a Face ügyféltár python. Az alábbi lépésekkel telepítheti a csomagot, és kipróbálhatja az alapvető feladatok példakódját. A Face szolgáltatás hozzáférést biztosít a képeken lévő emberi arcok észlelésére és felismerésére szolgáló speciális algoritmusokhoz.

A Python Face ügyfélkönyvtárának használatával:

* Arcok felismerése a képeken
* Hasonló arcok keresése
* Személycsoport létrehozása és betanítása
* Arc azonosítása
* Arcok ellenőrzése
* Pillanatkép készítése az adatáttelepítésről

[Referenciadokumentáció](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python) | [könyvtár forráskódcsomag](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face) | [(PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/) | [minták](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Beállítása

### <a name="create-a-face-azure-resource"></a>Face Azure-erőforrás létrehozása

Az Azure Cognitive Servicest olyan Azure-erőforrások képviselik, amelyekre előfizet. Hozzon létre egy erőforrást a Face az [Azure Portalon](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) a helyi gépen. További lehetőségek:

* Kap egy [próbakulcs](https://azure.microsoft.com/try/cognitive-services/#decision) érvényes részére hét napok részére szabad. A regisztráció után elérhető lesz az [Azure webhelyén.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Az erőforrás megtekintése az [Azure Portalon](https://portal.azure.com/)

Miután bekért egy kulcsot a próba-előfizetésből vagy erőforrásból, [hozzon létre egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a kulcshoz, amelynek neve . `FACE_SUBSCRIPTION_KEY`
 
### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Hozzon létre&mdash;például egy új Python-parancsfájlt*quickstart-file.py.* Ezután nyissa meg a kívánt szerkesztőben vagy IDE-ben, és importálja a következő könyvtárakat.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

Ezután hozzon létre változókat az erőforrás Azure-végpontjának és kulcsának. Előfordulhat, hogy módosítania kell a végpont`westus`első részét ( ) az előfizetésének megfelelően.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!NOTE]
> Ha az alkalmazás elindítása után hozta létre a környezeti változót, be kell zárnia, majd újra meg kell nyitnia a változót futtató szerkesztőt, IDE-t vagy rendszerhéjat.

### <a name="install-the-client-library"></a>Az ügyféltár telepítése

Az ügyfélkönyvtár a következő kkel telepíthető:

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek a Face Python SDK néhány főbb funkcióját kezelik.

|Név|Leírás|
|---|---|
|[FaceClient (FaceClient)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) | Ez az osztály a Face szolgáltatás használatára vonatkozó engedélyt jelöli, és minden Face funkcióhoz szüksége van rá. Az előfizetési adatokkal azonnal létrehozhatja azt, és más osztályok példányainak létrehozásához használhatja őket. |
|[FaceOperations (FaceOperations)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python)|Ez az osztály kezeli az emberi arcokkal elvégezhető alapvető észlelési és felismerési feladatokat. |
|[DetectedFace (DetectedFace)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)|Ez az osztály a kép egyetlen arcáról észlelt összes adatot jelöli. Segítségével részletes információkat kérhet az arcról.|
|[FaceListOperations (FaceListOperations)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations?view=azure-python)|Ez az osztály kezeli a felhőben tárolt **FaceList** konstrukciók, amely tárolja a válogatott lapok. |
|[PersonGroupPersonOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations?view=azure-python)| Ez az osztály kezeli a felhőben tárolt **személy** konstrukciók, amely tárolja a lapok, amelyek egyetlen személyhez tartozik.|
|[PersonGroupOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations?view=azure-python)| Ez az osztály kezeli a felhőben tárolt **PersonGroup** konstrukciókat, amelyek válogatott **személyobjektumokkészletét** tárolják. |
|[ShapshotOperations (ShapshotOperations)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations?view=azure-python)|Ez az osztály kezeli a Pillanatkép funkciót; segítségével ideiglenesen mentheti az összes felhőalapú arcadatát, és áttelepítheti ezeket az adatokat egy új Azure-előfizetésbe. |

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan kell elvégezni a következő feladatokat a Face client library for Python használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Arcok felismerése a képeken](#detect-faces-in-an-image)
* [Hasonló arcok keresése](#find-similar-faces)
* [Személycsoport létrehozása és betanítása](#create-and-train-a-person-group)
* [Arc azonosítása](#identify-a-face)
* [Arcok ellenőrzése](#verify-faces)
* [Pillanatkép készítése az adatáttelepítésről](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE]
> Ez a rövid útmutató feltételezi, hogy létrehozott egy környezeti `FACE_SUBSCRIPTION_KEY` [változót](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) a Face kulcshoz, melynek neve .

Ügyfél példányosítsa meg a végpontot és a kulcsot. Hozzon létre egy [CognitiveServicesCredentials objektumot](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) a kulccsal, és használja azt a végponttal egy [FaceClient-objektum](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) létrehozásához.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Arcok felismerése a képeken

A következő kód észleli a távoli lemezkép arcát. Kinyomtatja az észlelt arc azonosítóját a konzolra, és a program memóriájában is tárolja. Ezután észleli az arcokat egy több emberrel rendelkező képen, és kinyomtatja az azonosítójukat a konzolra is. A detect_with_url [metódus](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-) paramétereinek módosításával különböző adatokat adhat vissza az egyes [DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python) objektumokhoz.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

Tekintse meg a mintakódot a [GitHubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py) további észlelési forgatókönyvek.

### <a name="display-and-frame-faces"></a>Lapok megjelenítése és kerete

A következő kód a megadott képet adja ki a kijelzőre, és téglalapokat rajzol az arcok köré az DetectedFace.faceRectangle tulajdonság használatával.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![Egy fiatal nő egy piros téglalap húzott az arc körül](../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>Hasonló arcok keresése

A következő kód egyetlen észlelt arcot vesz igénybe, és más lapok at keres egyezések kereséséhez. Ha egyezést talál, kinyomtatja az egyező lap téglalapkoordinátáit a konzolra. 

### <a name="find-matches"></a>Találat keresése

Először futtassa a kódot a fenti szakaszban ([Arcok észlelése a képen](#detect-faces-in-an-image)) egy hivatkozás egyetlen lapra történő mentéséhez. Ezután futtassa a következő kódot, hogy a csoportkép több arcára mutató hivatkozásokat kapjon.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Ezután adja hozzá a következő kódblokkot a csoport első arcának példányainak megkereséséhez. Tekintse meg a [find_similar](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) módszerrel megtudhatja, hogyan módosíthatja ezt a viselkedést.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>Találatok nyomtatása

A következő kód dal nyomtathatja ki az egyezés részleteit a konzolra.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>Személycsoport létrehozása és betanítása

A következő kód három különböző **személyobjektummal** rendelkező **PersonGroup csoportot** hoz létre. Minden **egyes személyt** egy példaképhez társít, majd kiképzi, hogy képes legyen felismerni az egyes személyeket. 

### <a name="create-persongroup"></a>Személycsoport létrehozása

Ebben a forgatókönyvben való átlépéshez a következő képeket kell https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/imagesmentenie a projekt gyökérkönyvtárába: .

Ez a csoport a képek három készlet arcképek megfelelő három különböző emberek. A kód három Személy objektumot határoz **meg,** `woman`és `man`társítja őket a programmal kezdődő képfájlokhoz és `child`a hoz.

Miután beállította a képeket, definiáljon egy címkét a parancsfájl tetején a létrehozandó **PersonGroup** objektumhoz.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

Ezután adja hozzá a következő kódot a parancsfájl aljához. Ez a kód létrehoz egy **PersonGroup** és három **személy** objektumot.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Lapok hozzárendelése személyekhez

A következő kód a képeket előtaguk szerint rendezi, felismeri az arcokat, és hozzárendeli az arcokat az egyes **Személyek** objektumokhoz.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

### <a name="train-persongroup"></a>Betanítási személycsoport

Miután hozzárendelte az arcokat, be kell képeznie a **PersonGroup csoportot,** hogy azonosítani tudja az egyes személyobjektumaihoz társított vizuális jellemzőket. **Person** A következő kód meghívja az aszinkron **vonat** metódust, és lekérdezi az eredményt, és kinyomtatja az állapotot a konzolra.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

## <a name="identify-a-face"></a>Arc azonosítása

A következő kód több arccal rendelkező képet vesz fel, és a képen lévő egyes személyek identitásának megkeresésére keresi a képet. Összehasonlítja az egyes észlelt arc egy **PersonGroup**, egy adatbázis a különböző **személy** objektumok, amelyek arcvonásai ismertek.

> [!IMPORTANT]
> A példa futtatásához először futtatnia kell a kódot a [Létrehozás és a személycsoport betanítása mezőben.](#create-and-train-a-person-group)

### <a name="get-a-test-image"></a>Tesztkép beszereznie

A következő kód a projekt gyökerében egy _képteszt-kép-személy-group.jpg_ fájlt keres, és észleli a képen lévő arcokat. Ezt a képet a **PersonGroup-kezeléshez** https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/imageshasznált képekkel találja: .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Arcok azonosítása

Az **azonosítási** módszer az észlelt arcok tömbjét veszi fel, és összehasonlítja őket egy **persongroup csoporttal.** Ha egy észlelt arcot egy **személlyel**egyeztetni tud, menti az eredményt. Ez a kód részletes egyezési eredményeket nyomtat a konzolra.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="verify-faces"></a>Arcok ellenőrzése

Az Ellenőrzés művelet egy arcazonosítót és egy másik arcazonosítót vagy egy **Személy** objektumot vesz fel, és meghatározza, hogy ugyanahhoz a személyhez tartoznak-e.

A következő kód két forráskép arcait észleli, majd a célképből észlelt arccal szemben ellenőrzi azokat.

### <a name="get-test-images"></a>Tesztképek beszereznie

A következő kódblokkok deklarálják azokat a változókat, amelyek az ellenőrzési művelet forrás- és célképeire mutatnak.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_baseurl)]

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_photos)]

### <a name="detect-faces-for-verification"></a>Arcok észlelése ellenőrzésre

A következő kód észleli az arcokat a forrás- és a célképekben, és menti őket változókba.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_detect)]

### <a name="get-verification-results"></a>Az ellenőrzés eredményeinek beszerezni

A következő kód összehasonlítja az egyes forrásképeket a célképpel, és kinyomtat egy üzenetet, amely jelzi, hogy ugyanahhoz a személyhez tartoznak-e.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify)]

## <a name="take-a-snapshot-for-data-migration"></a>Pillanatkép készítése az adatáttelepítésről

A Pillanatképek funkció lehetővé teszi, hogy a mentett arcadatokat, például egy betanított **PersonGroupot**egy másik Azure Cognitive Services Face-előfizetésbe helyezze át. Ezt a funkciót akkor érdemes használnia, ha például létrehozott egy **PersonGroup** objektumot egy ingyenes próba-előfizetéssel, és most át szeretné telepíteni egy fizetős előfizetésbe. Tekintse meg az [arcadatok áttelepítése](../Face-API-How-to-Topics/how-to-migrate-face-data.md) című témakört a Pillanatképek funkció átfogó áttekintéséhez.

Ebben a példában áttelepíti a [Személycsoport létrehozása és betanítása csoportban](#create-and-train-a-person-group)létrehozott **személycsoportot.** Ezt a szakaszt először befejezheti, vagy használhatja a saját Face adatkonstrukció(i)t.

### <a name="set-up-target-subscription"></a>Cél-előfizetés beállítása

Először is egy második Azure-előfizetéssel kell rendelkeznie egy Face-erőforrással; ezt a Beállítás szakaszban ismertetett lépések végrehajtásával teheti [meg.](#setting-up) 

Ezután hozza létre a következő változókat a parancsfájl tetején. Új környezeti változókat is létre kell hoznia az Azure-fiók előfizetési azonosítójához, valamint az új (cél) fiók kulcsához, végpontjához és előfizetési azonosítóhoz. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshotvars)]

### <a name="authenticate-target-client"></a>Célügyfél hitelesítése

A parancsfájl későbbi részében mentse az aktuális ügyfélobjektumot forrásügyfélként, majd hitelesítse az új ügyfélobjektumot a cél-előfizetéshez. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_auth)]

### <a name="use-a-snapshot"></a>Pillanatkép használata

A pillanatkép-műveletek többi része egy aszinkron függvényen belül történik. 

1. Az első lépés a pillanatkép **készítése,** amely az eredeti előfizetés arcadatait egy ideiglenes felhőbe menti. Ez a módszer egy azonosítót ad vissza, amelyet a művelet állapotának lekérdezéséhez használ.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_take)]

1. Ezután kérdezze le az azonosítót, amíg a művelet be nem fejeződik.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait)]

    Ez a kód `wait_for_operation` használja a funkciót, amelyet külön kell definiálni:

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_waitforop)]

1. Lépjen vissza az aszinkron függvényhez. Az **alkalmazási** művelettel arcadatokat írhat a cél-előfizetésbe. Ez a módszer azonosítót is ad vissza.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_apply)]

1. Ismét használja `wait_for_operation` a függvényt az azonosító lekérdezéséhez, amíg a művelet befejeződik.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait2)]

Miután elvégezte ezeket a lépéseket, az új (cél) előfizetésből hozzáférhet az arcadat-konstrukcióihoz.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az `python` alkalmazást a parancssegítségével a rövid útmutató fájlban.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, hogy tisztítsák meg, és távolítsa el a Cognitive Services-előfizetés, törölheti az erőforrás vagy erőforráscsoport. Az erőforráscsoport törlése a hozzá társított egyéb erőforrásokat is törli.

* [Portál](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)

Ha létrehozott egy **PersonGroup csoportot** ebben a rövid útmutatóban, és törölni szeretné, futtassa a következő kódot a parancsfájlban:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

Ha ebben a rövid útmutatóban a Pillanatkép szolgáltatás használatával telepítette át az adatokat, törölnie kell a cél-előfizetésbe mentett **PersonGroup csoportot** is.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletetargetgroup)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja a Face-kódtárat a Pythonhoz alapfeladatok elvégzéséhez. Ezután tekintse meg a referenciadokumentációt, ha többet szeretne megtudni a könyvtárról.

> [!div class="nextstepaction"]
> [Face API-hivatkozás (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)

* [Mi az a Face szolgáltatás?](../overview.md)
* A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py)
