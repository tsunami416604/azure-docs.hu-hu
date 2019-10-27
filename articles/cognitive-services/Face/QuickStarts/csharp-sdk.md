---
title: 'Rövid útmutató: a .NET-hez készült ügyféloldali kódtár | Microsoft Docs'
description: Ismerkedés a .NET-hez készült Face ügyféloldali kódtáraval.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/20/2019
ms.author: pafarley
ms.openlocfilehash: 4f06e423c6dcc561ef8e51c33f24cd9f88a681b5
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935888"
---
# <a name="quickstart-face-client-library-for-net"></a>Gyors útmutató: Face ügyféloldali kódtár a .NET-hez

Ismerkedés a .NET-hez készült Face ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. A Face API szolgáltatás lehetővé teszi a speciális algoritmusok elérését a képeken található emberi arcok észleléséhez és felismeréséhez.

A .NET-hez készült Face ügyféloldali kódtár a következőhöz használható:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Képeken lévő arcok észlelése](#detect-faces-in-an-image)
* [Hasonló arcok keresése](#find-similar-faces)
* [Személy csoport létrehozása és betanítása](#create-and-train-a-person-group)
* [Arc azonosítása](#identify-a-face)
* [Pillanatkép készítése az adatok áttelepítéséhez](#take-a-snapshot-for-data-migration)

[Dokumentáció](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)  | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face)  | [csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.5.0-preview.1)  | [minták](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [.net Core](https://dotnet.microsoft.com/download/dotnet-core)jelenlegi verziója.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-face-azure-resource"></a>Face Azure-erőforrás létrehozása

Az Azure Cognitive Services a-ra előfizetett Azure-erőforrások képviselik. Hozzon létre egy erőforrást a helyi gépen a [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure parancssori](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) felület használatával. További lehetőségek:

* A [próbaverziós kulcs](https://azure.microsoft.com/try/cognitive-services/#decision) ingyenes hét napig érvényes. A regisztráció után elérhető lesz az [Azure webhelyén](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Tekintse meg az erőforrást a [Azure Portalon](https://portal.azure.com/).

Miután megszerezte a kulcsot a próbaverziós előfizetésből vagy erőforrásból, [hozzon létre egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a kulcs-és végpont URL-címéhez, amelynek neve `FACE_SUBSCRIPTION_KEY` és `FACE_ENDPOINT`.

### <a name="create-a-new-c-application"></a>Új C# alkalmazás létrehozása

Hozzon létre egy új .NET Core-alkalmazást az előnyben részesített szerkesztőben vagy az IDE-ben. 

Egy konzolablak (például cmd, PowerShell vagy bash) használatával hozzon létre egy új, `face-quickstart`nevű Console-alkalmazást a `dotnet new` paranccsal. Ez a parancs egy egyszerű ""Helló világ!"alkalmazás" C# projektet hoz létre egyetlen forrásfájlban: *program.cs*. 

```console
dotnet new console -n face-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Az alkalmazást az alábbiakkal hozhatja létre:

```console
dotnet build
```

A Build kimenete nem tartalmazhat figyelmeztetést vagy hibát. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

A projekt könyvtárában nyissa meg a *program.cs* fájlt az előnyben részesített szerkesztőben vagy az ide-ben. Adja hozzá a következő `using` irányelveket:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_using)]

Az alkalmazás `Main` metódusában hozzon létre változókat az erőforrás Azure-végpontja és kulcsa számára.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_mainvars)]

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Az alkalmazás könyvtárában telepítse a .NET-hez készült Face ügyféloldali kódtárat a következő paranccsal:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.5.0-preview.1
```

Ha a Visual Studio IDE-t használja, az ügyféloldali kódtár letölthető NuGet-csomagként érhető el.

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek kezelik a Face .NET SDK főbb funkcióit:

|Név|Leírás|
|---|---|
|[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | Ez az osztály a Face szolgáltatás használatára vonatkozó engedélyt jelöli, és minden arc funkcióhoz szüksége van rá. Ezt az előfizetési adatok alapján hozza létre, és más osztályok példányainak előállítására használja. |
|[FaceOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|Ez az osztály az emberi arcokkal elvégezhető alapvető észlelési és felismerési feladatokat kezeli. |
|[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|Ez az osztály a rendszerkép egyetlen arca által észlelt összes adatmennyiséget jelképezi. Ezzel az oldallal kapcsolatos részletes információkat kérhet le.|
|[FaceListOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|Ez az osztály kezeli a felhőben tárolt **FaceList** -szerkezeteket, amelyek az arcok válogatott készleteit tárolják. |
|[PersonGroupPersonExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| Ez az osztály kezeli a felhőalapú tárolt **személyek** szerkezetét, amelyek egyetlen személyhez tartozó arcok készletét tárolják.|
|[PersonGroupOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| Ez az osztály kezeli a felhőben tárolt **PersonGroup** -szerkezeteket, amelyek a különböző **személyre** kiterjedő objektumok készletét tárolják. |
|[ShapshotOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)|Ez az osztály kezeli a pillanatkép funkcióit. A használatával ideiglenesen mentheti az összes felhőalapú Arcfelismerés, és áttelepítheti az adott adatait egy új Azure-előfizetésbe. |

## <a name="code-examples"></a>Példák a kódokra

Az alábbi kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a .NET-hez készült Face ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Képeken lévő arcok észlelése](#detect-faces-in-an-image)
* [Hasonló arcok keresése](#find-similar-faces)
* [Személy csoport létrehozása és betanítása](#create-and-train-a-person-group)
* [Arc azonosítása](#identify-a-face)
* [Pillanatkép készítése az adatok áttelepítéséhez](#take-a-snapshot-for-data-migration)


## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE]
> Ez a rövid útmutató azt feltételezi, hogy [létrehozott egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) az arc kulcsához és a végponthoz, amelynek neve `FACE_SUBSCRIPTION_KEY` és `FACE_ENDPOINT`.

Egy új metódusban hozza létre az ügyfelet a végponttal és a kulccsal. Hozzon létre egy [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) objektumot a kulccsal, és használja a végpontján egy [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) objektum létrehozásához.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_auth)]

Valószínűleg ezt a metódust szeretné hívni a `Main` metódusban.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_client)]

## <a name="detect-faces-in-an-image"></a>Arcok felismerése a képeken

Az osztály gyökérkönyvtárában adja meg a következő URL-karakterláncot. Ez az URL-cím egy minta lemezképek halmazára mutat.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

Opcionálisan kiválaszthatja, hogy melyik AI-modellt használja az észlelt arc (ok) ből származó adatok kinyeréséhez. A beállításokkal kapcsolatos információkért tekintse [meg a felismerési modell megadása](../Face-API-How-to-Topics/specify-recognition-model.md) című témakört.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

A végső észlelési művelet egy [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) objektumot, egy képurl-címet és egy felismerési modellt fog elkészíteni.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_call)]

Ebben az esetben a `DetectFaceExtract` metódus az adott URL-címen található képek három képét észleli, és a [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) objektumok listáját hozza létre a program memóriájában. A [FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) értékek listája határozza meg a kinyerni kívánt szolgáltatásokat. Tekintse meg a [githubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/Face/Program.cs) található mintakód a segítő kódot, amely intuitív módon tudja kinyomtatni ezeket az információt.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect)]

## <a name="find-similar-faces"></a>Hasonló arcok keresése

A következő kód egyetlen észlelt arcot (forrást) használ, és a találatok kereséséhez más arcok (TARGET) készletet keres. Ha egyezést talál, kinyomtatja az egyeztetett arc AZONOSÍTÓját a konzolon.

### <a name="detect-faces-for-comparison"></a>Arcok észlelése összehasonlításhoz

Először határozza meg a második Arcfelismerés módszert. A képek összevetése előtt fel kell ismernie az arcokat, és ez az észlelési módszer összehasonlítási műveletekre van optimalizálva. Nem kibontja a részletes Arcfelismerés-attribútumokat, például a fenti részben, és egy másik felismerési modellt használ.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Egyezések keresése

A következő metódus észleli a megcélzott lemezképekben található arcokat és egyetlen forrás képét. Ezután összehasonlítja őket, és megkeresi a forrás képéhez hasonló összes megcélzott képet.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Egyezések nyomtatása

A következő kód a megfeleltetés részleteit jeleníti meg a konzolon.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar_print)]

## <a name="create-and-train-a-person-group"></a>Személy csoport létrehozása és betanítása

A következő kód egy **PersonGroup** hoz létre hat különböző **személy** objektummal. Az egyes **személyeket** a képek egy halmazával társítja, majd az egyes személyeket az arc jellemzői alapján elismerik. A **személy** -és **PersonGroup** objektumok az ellenőrzési, azonosítási és csoportosítási műveletekben használatosak.

Ha még nem tette meg, adja meg a következő URL-karakterláncot az osztály gyökerénél. Ez egy minta lemezképek halmazára mutat.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

A szakasz későbbi részében az adatok az arcokból való kinyerésére szolgáló felismerési modell fog megjelenni, és az alábbi kódrészlet az elérhető modellekre mutató hivatkozásokat hoz létre. A felismerési modellekkel kapcsolatos információkért tekintse [meg a felismerési modell megadása](../Face-API-How-to-Topics/specify-recognition-model.md) című témakört.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

### <a name="create-persongroup"></a>PersonGroup létrehozása

Deklaráljon egy karakterlánc-változót az osztály gyökérkönyvtárában, hogy az a létrehozandó **PERSONGROUP** azonosítóját jelöli.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_declare)]

Új metódusban adja hozzá a következő kódot. Ez a kód a személyek nevét társítja a képekkel.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_files)]

Ezután adja hozzá a következő kódot egy **személy** objektum létrehozásához a szótárban, és adja hozzá a megfelelő rendszerképekből a Face (adatok) elemet. Minden **személy** objektum ugyanahhoz a **PersonGroup** van társítva egyedi azonosító sztringje alapján. Ne felejtse el átadni a változókat `client`, `url`és `RECOGNITION_MODEL1` ebbe a metódusba.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_create)]

### <a name="train-persongroup"></a>PersonGroup betanítása

Miután kibontotta az adatokból a képeket, és azokat különböző **személy** objektumokra rendezi, be kell tanítania a **PersonGroup** , hogy azonosítsa az egyes **személy** objektumaihoz társított vizuális funkciókat. A következő kód meghívja az aszinkron **betanítási** módszert, és lekérdezi az eredményeket, kinyomtatja az állapotot a konzolra.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_train)]

Most már készen áll arra, hogy az ellenőrzési, azonosítási vagy csoportosítási műveletekben felhasználja ezt a **személy** csoportot és a hozzá tartozó **személyeket** .

## <a name="identify-a-face"></a>Arc azonosítása

Az azonosítási művelet egy személy (vagy több személy) képét veszi fel, és megkeresi a képen látható összes arc identitását. Összehasonlítja az észlelt elemeket egy **PersonGroup**, egy olyan, a különböző **személy** objektumokat tartalmazó adatbázissal, amelyek az arc funkciói ismertek.

> [!IMPORTANT]
> A példa futtatásához először futtatnia kell a kódot [egy személy csoport létrehozása és betanítása csoportban](#create-and-train-a-person-group). Az ebben a szakaszban használt változók&mdash;`client`, `url`és `RECOGNITION_MODEL1`&mdash;itt is elérhetőnek kell lenniük.

### <a name="get-a-test-image"></a>Tesztelési rendszerkép beolvasása

Figyelje meg, hogy a [személy csoport létrehozásához és betanításához](#create-and-train-a-person-group) használt kód `sourceImageFileName`változót határoz meg. Ez a változó megfelel a forrás rendszerképének&mdash;az azonosított személyeket tartalmazó rendszerképnek.

### <a name="identify-faces"></a>Arcok azonosítása

A következő kód a forrás képét veszi fel, és létrehozza a rendszerképben észlelt összes arc listáját. Ezek az arcok lesznek azonosítva a **PersonGroup**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify_sources)]

A következő kódrészlet meghívja az azonosítási műveletet, és kiírja az eredményeket a-konzolra. Itt a szolgáltatás megkísérli a forrás-rendszerképből származó összes arc egyeztetését az adott **PersonGroup**lévő **személynek** .

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>Pillanatkép készítése az adatok áttelepítéséhez

A pillanatképek funkció lehetővé teszi a mentett Arcfelismerés, például a betanított **PersonGroup**áthelyezését egy másik Azure Cognitive Services Face-előfizetésbe. Érdemes lehet ezt a funkciót használni, ha például egy ingyenes próbaverziós előfizetéssel létrehozott egy **PersonGroup** objektumot, és szeretné áttelepíteni egy fizetős előfizetésre. A pillanatképek funkció áttekintését lásd: [az arc-információk migrálása](../Face-API-How-to-Topics/how-to-migrate-face-data.md) .

Ebben a példában a [személy csoport létrehozása és betanítása](#create-and-train-a-person-group)során létrehozott **PersonGroup** kell áttelepítenie. Először hajtsa végre az adott szakaszt, vagy hozzon létre egy saját Face adatszerkezetet az áttelepítéshez.

### <a name="set-up-target-subscription"></a>Cél-előfizetés beállítása

Először is rendelkeznie kell egy másik Azure-előfizetéssel, egy Face erőforrással. ezt a [beállítás](#setting-up) szakasz lépéseit követve teheti meg. 

Ezután adja meg a következő változókat a program `Main` metódusában. Új környezeti változókat kell létrehoznia az Azure-fiók előfizetés-AZONOSÍTÓJÁRA, valamint az új (cél) fiók kulcsát, végpontját és előfizetési AZONOSÍTÓját. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

Ebben a példában deklaráljon egy változót a célként megadott **PERSONGROUP** azonosítójának&mdash;az új előfizetéshez tartozó objektumra, amelyet az adatai másolásához fog másolni.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

### <a name="authenticate-target-client"></a>A célként megadott ügyfél hitelesítése

Ezután adja hozzá a kódot a másodlagos arc-előfizetés hitelesítéséhez.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_client)]

### <a name="use-a-snapshot"></a>Pillanatkép használata

A pillanatképek további műveleteinek egy aszinkron metóduson belül kell megvalósulnia. 

1. Első lépésként **készítse el a** pillanatképet, amely az eredeti előfizetése adatait egy ideiglenes Felhőbeli helyre menti. Ez a metódus egy azonosítót ad vissza, amelyet a művelet állapotának lekérdezéséhez használ.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take)]

1. Ezután kérdezze le az azonosítót, amíg a művelet be nem fejeződik.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take_wait)]

1. Ezután az **alkalmazás** művelet használatával írja a Face-adatait a cél előfizetésbe. Ez a metódus egy azonosító értéket is visszaad.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. Ismét kérdezze le az új azonosítót, amíg a művelet be nem fejeződik.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. Végül fejezze be a try/catch blokkot, és fejezze be a metódust.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_trycatch)]

Ezen a ponton az új **PersonGroup** -objektumnak ugyanazokkal az adatokkal kell rendelkeznie, mint az eredetinek, és elérhetőnek kell lennie az új (cél) Azure Face-előfizetésből.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást az alkalmazás könyvtárából az `dotnet run` paranccsal.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Ha ebben a rövid útmutatóban létrehozott egy **PersonGroup** , és törölni szeretné, futtassa a következő kódot a programban:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_delete)]

Adja meg a törlési módszert a következő kóddal:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_deletepersongroup)]

Ha ezen a rövid útmutatóban a pillanatkép-szolgáltatás használatával áttelepítette az adatáttelepítést, akkor a cél előfizetésbe mentett **PersonGroup** is törölnie kell.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_target_persongroup_delete)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja a .NET-hez készült Face Library-t a feladatok elvégzésére. Ezután tekintse át a dokumentációt, és ismerkedjen meg a könyvtárral.

> [!div class="nextstepaction"]
> [Face API-hivatkozás (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [Mi a Face API?](../overview.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/Face/Program.cs)található.