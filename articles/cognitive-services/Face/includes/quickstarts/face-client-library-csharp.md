---
title: Face .NET ügyféloldali kódtár – rövid útmutató
description: Ismerkedjen meg a .NET-hez készült Face ügyféloldali kódtár ezzel a rövid útmutatóval.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 08/17/2020
ms.author: pafarley
ms.openlocfilehash: 3e561400683d9d0679a51a761f97ec7ba8bf8ea9
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505876"
---
Ismerkedés a .NET-hez készült Face ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. A Face szolgáltatás hozzáférést biztosít a speciális algoritmusokhoz a képeken található emberi arcok észleléséhez és felismeréséhez.

A .NET-hez készült Face ügyféloldali kódtár a következőhöz használható:

* [Arcok felismerése a képeken](#detect-faces-in-an-image)
* [Hasonló arcok keresése](#find-similar-faces)
* [Személy csoport létrehozása és betanítása](#create-and-train-a-person-group)
* [Arc azonosítása](#identify-a-face)
* [Pillanatkép készítése az adatok áttelepítéséhez](#take-a-snapshot-for-data-migration)

[Dokumentáció](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face)  |  [Csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.5.0-preview.1)  |  [Példák](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Előfeltételek

* A [.net Core](https://dotnet.microsoft.com/download/dotnet-core)jelenlegi verziója.
* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" hozzon létre egy Face-erőforrást "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése**elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Face APIhoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.
* A kulcs és a végpont beszerzése után [hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a kulcs és a végpont URL-címéhez, illetve a nevet `FACE_SUBSCRIPTION_KEY` `FACE_ENDPOINT` .

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

Hozzon létre egy új .NET Core-alkalmazást az előnyben részesített szerkesztőben vagy az IDE-ben. 

A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `face-quickstart` . Ez a parancs egy egyszerű ""Helló világ!"alkalmazás" C#-projektet hoz létre egyetlen forrásfájlban: *program.cs*. 

```dotnetcli
dotnet new console -n face-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Az alkalmazást az alábbiakkal hozhatja létre:

```dotnetcli
dotnet build
```

A Build kimenete nem tartalmazhat figyelmeztetést vagy hibát. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

A projekt könyvtárában nyissa meg a *program.cs* fájlt az előnyben részesített szerkesztőben vagy az ide-ben. Adja hozzá a következő `using` irányelveket:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_using)]

Az alkalmazás `Main` metódusában hozzon létre változókat az erőforrás Azure-végpontja és kulcsa számára.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_mainvars)]

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Az alkalmazás könyvtárában telepítse a .NET-hez készült Face ügyféloldali kódtárat a következő paranccsal:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.5.0-preview.1
```

Ha a Visual Studio IDE-t használja, az ügyféloldali kódtár letölthető NuGet-csomagként érhető el.

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek kezelik a Face .NET ügyféloldali kódtár főbb funkcióit:

|Név|Leírás|
|---|---|
|[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | Ez az osztály a Face szolgáltatás használatára vonatkozó engedélyt jelöli, és minden arc funkcióhoz szüksége van rá. Ezt az előfizetési adatok alapján hozza létre, és más osztályok példányainak előállítására használja. |
|[FaceOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|Ez az osztály az emberi arcokkal elvégezhető alapvető észlelési és felismerési feladatokat kezeli. |
|[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|Ez az osztály a rendszerkép egyetlen arca által észlelt összes adatmennyiséget jelképezi. Ezzel az oldallal kapcsolatos részletes információkat kérhet le.|
|[FaceListOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|Ez az osztály kezeli a felhőben tárolt **FaceList** -szerkezeteket, amelyek az arcok válogatott készleteit tárolják. |
|[PersonGroupPersonExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| Ez az osztály kezeli a felhőalapú tárolt **személyek** szerkezetét, amelyek egyetlen személyhez tartozó arcok készletét tárolják.|
|[PersonGroupOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| Ez az osztály kezeli a felhőben tárolt **PersonGroup** -szerkezeteket, amelyek a különböző **személyre** kiterjedő objektumok készletét tárolják. |
|[ShapshotOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)|Ez az osztály kezeli a pillanatkép funkcióit. A használatával ideiglenesen mentheti az összes felhőalapú Arcfelismerés, és áttelepítheti az adott adatait egy új Azure-előfizetésbe. |

## <a name="code-examples"></a>Kódpéldák

Az alábbi kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a .NET-hez készült Face ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Arcok felismerése a képeken](#detect-faces-in-an-image)
* [Hasonló arcok keresése](#find-similar-faces)
* [Személy csoport létrehozása és betanítása](#create-and-train-a-person-group)
* [Arc azonosítása](#identify-a-face)
* [Pillanatkép készítése az adatok áttelepítéséhez](#take-a-snapshot-for-data-migration)


## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE]
> Ez a rövid útmutató azt feltételezi, hogy [létrehozott egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) az arc kulcsához és a végponthoz, és megnevezett `FACE_SUBSCRIPTION_KEY` és `FACE_ENDPOINT` .

Egy új metódusban hozza létre az ügyfelet a végponttal és a kulccsal. Hozzon létre egy **[ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials?view=azure-dotnet)** objektumot a kulccsal, és használja a végpontján egy **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** objektum létrehozásához.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_auth)]

Valószínűleg ezt a metódust szeretné hívni a `Main` metódusban.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_client)]

### <a name="declare-helper-fields"></a>Segítő mezők deklarálása

A következő mezők szükségesek a később hozzáadott arc-műveletekhez. Az osztály gyökérkönyvtárában adja meg a következő URL-karakterláncot. Ez az URL-cím egy minta lemezképek mappájára mutat.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_image_url)]

A különböző felismerési modellekre mutató karakterláncok definiálása. Később meg tudja határozni, hogy melyik felismerési modellt szeretné használni az Arcfelismerés során. A beállításokkal kapcsolatos információkért tekintse [meg a felismerési modell megadása](../../Face-API-How-to-Topics/specify-recognition-model.md) című témakört.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_models)]

## <a name="detect-faces-in-an-image"></a>Arcok felismerése a képeken

Adja hozzá a következő metódus hívását a **Main** metódushoz. A következő metódust kell megadnia. A végső észlelési művelet egy **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** objektumot, egy képurl-címet és egy felismerési modellt fog elkészíteni.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_call)]

### <a name="get-detected-face-objects"></a>Észlelt Arcfelismerés-objektumok

A kód következő blokkjában a metódus az `DetectFaceExtract` adott URL-címen található képek három képét észleli, és létrehoz egy listát a **[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** objektumokról a program memóriájában. A **[FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** értékek listája határozza meg a kinyerni kívánt szolgáltatásokat. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Észlelt Arcfelismerés

A metódus többi része `DetectFaceExtract` elemezi és kinyomtatja az összes észlelt arc attribútum-adatát. Minden attribútumot külön kell megadni az eredeti Arcfelismerés API-hívásban (a **[FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** listában). A következő kód dolgozza fel az összes attribútumot, de valószínűleg csak egy vagy néhányat kell használnia.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Hasonló arcok keresése

A következő kód egyetlen észlelt arcot (forrást) használ, és a találatok kereséséhez más arcok (TARGET) készletet keres. Ha egyezést talál, kinyomtatja az egyeztetett arc AZONOSÍTÓját a konzolon.

### <a name="detect-faces-for-comparison"></a>Arcok észlelése összehasonlításhoz

Először határozza meg a második Arcfelismerés módszert. A képek összevetése előtt fel kell ismernie az arcokat, és ez az észlelési módszer összehasonlítási műveletekre van optimalizálva. Nem kibontja a részletes Arcfelismerés-attribútumokat, például a fenti részben, és egy másik felismerési modellt használ.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Egyezések keresése

A következő metódus észleli a megcélzott lemezképekben található arcokat és egyetlen forrás képét. Ezután összehasonlítja őket, és megkeresi a forrás képéhez hasonló összes megcélzott képet.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Egyezések nyomtatása

A következő kód a megfeleltetés részleteit jeleníti meg a konzolon:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar_print)]

## <a name="identify-a-face"></a>Arc azonosítása

Az azonosítási művelet egy személy (vagy több személy) képét veszi fel, és megkeresi a képen látható összes arc identitását. Összehasonlítja az észlelt elemeket egy **PersonGroup**, egy olyan, a különböző **személy** objektumokat tartalmazó adatbázissal, amelyek az arc funkciói ismertek. Az azonosítási művelet végrehajtásához először létre kell hoznia és be kell tanítania egy **PersonGroup**

### <a name="create-and-train-a-person-group"></a>Személy csoport létrehozása és betanítása

A következő kód egy **PersonGroup** hoz létre hat különböző **személy** objektummal. Az egyes **személyeket** a képek egy halmazával társítja, majd az egyes személyeket az arc jellemzői alapján elismerik. A **személy** -és **PersonGroup** objektumok az ellenőrzési, azonosítási és csoportosítási műveletekben használatosak.

#### <a name="create-persongroup"></a>PersonGroup létrehozása

Deklaráljon egy karakterlánc-változót az osztály gyökérkönyvtárában, hogy az a létrehozandó **PERSONGROUP** azonosítóját jelöli.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_declare)]

Új metódusban adja hozzá a következő kódot. Ez a metódus az azonosítási műveletet fogja végrehajtani. A kód első blokkja a személyek nevét társítja a képekkel.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_files)]

Ezután adja hozzá a következő kódot egy **személy** objektum létrehozásához a szótárban, és adja hozzá a megfelelő rendszerképekből a Face (adatok) elemet. Minden **személy** objektum ugyanahhoz a **PersonGroup** van társítva egyedi azonosító sztringje alapján. Ne felejtse el átadni a változókat `client` , `url` és `RECOGNITION_MODEL1` ebbe a metódusba.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_create)]

#### <a name="train-persongroup"></a>PersonGroup betanítása

Miután kibontotta az adatokból a képeket, és azokat különböző **személy** objektumokra rendezi, be kell tanítania a **PersonGroup** , hogy azonosítsa az egyes **személy** objektumaihoz társított vizuális funkciókat. A következő kód meghívja az aszinkron **betanítási** módszert, és lekérdezi az eredményeket, kinyomtatja az állapotot a konzolra.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_train)]

Most már készen áll arra, hogy az ellenőrzési, azonosítási vagy csoportosítási műveletekben felhasználja ezt a **személy** csoportot és a hozzá tartozó **személyeket** .

### <a name="get-a-test-image"></a>Tesztelési rendszerkép beolvasása

Figyelje meg, hogy a [személy csoport létrehozásához és betanításához](#create-and-train-a-person-group) használt kód változót határoz meg `sourceImageFileName` . Ez a változó a forrás rendszerképének felel meg, &mdash; amely az azonosított személyeket tartalmazza.

### <a name="identify-faces"></a>Arcok azonosítása

A következő kód a forrás képét veszi fel, és létrehozza a rendszerképben észlelt összes arc listáját. Ezek az arcok lesznek azonosítva a **PersonGroup**.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify_sources)]

A következő kódrészlet meghívja a **IdentifyAsync** műveletet, és kiírja az eredményeket a-konzolra. Itt a szolgáltatás megkísérli a forrás-rendszerképből származó összes arc egyeztetését az adott **PersonGroup**lévő **személynek** . Ez kizárja az azonosítási módszert.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>Pillanatkép készítése az adatok áttelepítéséhez

A pillanatképek funkció lehetővé teszi a mentett Arcfelismerés, például a betanított **PersonGroup**áthelyezését egy másik Azure Cognitive Services Face-előfizetésbe. Érdemes lehet ezt a funkciót használni, ha például egy ingyenes előfizetés használatával létrehozott egy **PersonGroup** objektumot, és szeretné áttelepíteni egy fizetős előfizetésre. A pillanatképek funkció áttekintését lásd: [az arc-információk migrálása](../../Face-API-How-to-Topics/how-to-migrate-face-data.md) .

Ebben a példában a [személy csoport létrehozása és betanítása](#create-and-train-a-person-group)során létrehozott **PersonGroup** kell áttelepítenie. Először hajtsa végre az adott szakaszt, vagy hozzon létre egy saját Face adatszerkezetet az áttelepítéshez.

### <a name="set-up-target-subscription"></a>Cél-előfizetés beállítása

Először is rendelkeznie kell egy másik Azure-előfizetéssel, egy Face erőforrással. ezt a [beállítás](#setting-up) szakasz lépéseit követve teheti meg. 

Ezután adja meg a következő változókat a `Main` program metódusában. Új környezeti változókat kell létrehoznia az Azure-fiók előfizetés-AZONOSÍTÓJÁRA, valamint az új (cél) fiók kulcsát, végpontját és előfizetési AZONOSÍTÓját. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_vars)]

Ebben a példában deklaráljon egy változót az **PersonGroup** &mdash; új előfizetéshez tartozó objektum PersonGroup azonosítójának, amelyet az adatai másolásához fog másolni.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_vars)]

### <a name="authenticate-target-client"></a>A célként megadott ügyfél hitelesítése

Ezután adja hozzá a kódot a másodlagos arc-előfizetés hitelesítéséhez.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_client)]

### <a name="use-a-snapshot"></a>Pillanatkép használata

A pillanatképek további műveleteinek egy aszinkron metóduson belül kell megvalósulnia. 

1. Első lépésként **készítse el a** pillanatképet, amely az eredeti előfizetése adatait egy ideiglenes Felhőbeli helyre menti. Ez a metódus egy azonosítót ad vissza, amelyet a művelet állapotának lekérdezéséhez használ.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_take)]

1. Ezután kérdezze le az azonosítót, amíg a művelet be nem fejeződik.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_take_wait)]

1. Ezután az **alkalmazás** művelet használatával írja a Face-adatait a cél előfizetésbe. Ez a metódus egy azonosító értéket is visszaad.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_apply)]

1. Ismét kérdezze le az új azonosítót, amíg a művelet be nem fejeződik.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_apply)]

1. Végül fejezze be a try/catch blokkot, és fejezze be a metódust.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_trycatch)]

Ezen a ponton az új **PersonGroup** -objektumnak ugyanazokkal az adatokkal kell rendelkeznie, mint az eredetinek, és elérhetőnek kell lennie az új (cél) Azure Face-előfizetésből.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást az alkalmazás könyvtárából a `dotnet run` paranccsal.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Ha ebben a rövid útmutatóban létrehozott egy **PersonGroup** , és törölni szeretné, futtassa a következő kódot a programban:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_delete)]

Adja meg a törlési módszert a következő kóddal:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_deletepersongroup)]

Ha ezen a rövid útmutatóban a pillanatkép-szolgáltatás használatával áttelepítette az adatáttelepítést, akkor a cél előfizetésbe mentett **PersonGroup** is törölnie kell.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_target_persongroup_delete)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja a .NET-hez készült Face Library-t a feladatok elvégzésére. Ezután tekintse át a dokumentációt, és ismerkedjen meg a könyvtárral.

> [!div class="nextstepaction"]
> [Face API-hivatkozás (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [Mi az a Face szolgáltatás?](../../overview.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs)található.
