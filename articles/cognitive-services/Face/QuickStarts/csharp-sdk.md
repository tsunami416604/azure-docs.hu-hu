---
title: 'Rövid útmutató: Face ügyféltár a .NET-hez'
description: Ezzel a rövid útmutatóval ismerkedje meg a .NET Face ügyféltárával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 89ef221fce9aed3f9e2c948e89933b8650bb4b4b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769828"
---
# <a name="quickstart-face-client-library-for-net"></a>Rövid útmutató: Face ügyféltár a .NET-hez

Ismerkedés a .NET Face ügyféltárával. Az alábbi lépésekkel telepítheti a csomagot, és kipróbálhatja az alapvető feladatok példakódját. A Face szolgáltatás hozzáférést biztosít a képeken lévő emberi arcok észlelésére és felismerésére szolgáló speciális algoritmusokhoz.

A .NET Face ügyféltár segítségével:

* [Arcok felismerése a képeken](#detect-faces-in-an-image)
* [Hasonló arcok keresése](#find-similar-faces)
* [Személycsoport létrehozása és betanítása](#create-and-train-a-person-group)
* [Arc azonosítása](#identify-a-face)
* [Pillanatkép készítése az adatáttelepítésről](#take-a-snapshot-for-data-migration)

[Referenciadokumentációkönyvtár](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet) | [forráskódjának](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | [csomagja (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.5.0-preview.1) | [mintái](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)aktuális verziója .

## <a name="setting-up"></a>Beállítása

### <a name="create-a-face-azure-resource"></a>Face Azure-erőforrás létrehozása

Az Azure Cognitive Servicest olyan Azure-erőforrások képviselik, amelyekre előfizet. Hozzon létre egy erőforrást a Face az [Azure Portalon](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) a helyi gépen. Ön is kap egy [próbakulcs](https://azure.microsoft.com/try/cognitive-services/#decision) érvényes hét napig ingyen. A regisztráció után elérhető lesz az [Azure webhelyén.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  

Miután lekért egy kulcsot a próba-előfizetés vagy erőforrás, [hozzon létre környezeti változók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) at a kulcs és a végpont URL-cím, nevű, `FACE_SUBSCRIPTION_KEY` illetve. `FACE_ENDPOINT`

### <a name="create-a-new-c-application"></a>Új C# alkalmazás létrehozása

Hozzon létre egy új .NET Core alkalmazást a kívánt szerkesztőben vagy IDE-ben. 

Egy konzolablakban (például cmd, PowerShell vagy `dotnet new` Bash) a paranccsal `face-quickstart`hozhat létre egy új konzolalkalmazást a .. Ez a parancs egy egyszerű "Hello World" C# projektet hoz létre egyetlen forrásfájllal: *Program.cs*. 

```dotnetcli
dotnet new console -n face-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazásmappára. Az alkalmazást a következőkkel hozhatja létre:

```dotnetcli
dotnet build
```

A build kimenetnem tartalmazhat figyelmeztetéseket vagy hibákat. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

A projekt könyvtárából nyissa meg a *Program.cs* fájlt a kívánt szerkesztőben vagy IDE-ben. Adja hozzá `using` a következő irányelveket:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_using)]

Az alkalmazás metódusában `Main` hozzon létre változókat az erőforrás Azure-végpontjának és kulcsának.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_mainvars)]

### <a name="install-the-client-library"></a>Az ügyféltár telepítése

Az alkalmazáskönyvtáron belül telepítse a Face client library for .NET programot a következő paranccsal:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.5.0-preview.1
```

Ha a Visual Studio IDE-t használja, az ügyfélkönyvtár letölthető NuGet csomagként érhető el.

## <a name="object-model"></a>Objektummodell

A face .NET SDK néhány főbb jellemzőjét a következő osztályok és felületek kezelik:

|Name (Név)|Leírás|
|---|---|
|[FaceClient (FaceClient)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | Ez az osztály a Face szolgáltatás használatára vonatkozó engedélyt jelöli, és minden Face funkcióhoz szüksége van rá. Az előfizetési adatokkal azonnal létrehozhatja azt, és más osztályok példányainak létrehozásához használhatja őket. |
|[FaceOperations (FaceOperations)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|Ez az osztály kezeli az emberi arcokkal elvégezhető alapvető észlelési és felismerési feladatokat. |
|[DetectedFace (DetectedFace)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|Ez az osztály a kép egyetlen arcáról észlelt összes adatot jelöli. Segítségével részletes információkat kérhet az arcról.|
|[FaceListOperations (FaceListOperations)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|Ez az osztály kezeli a felhőben tárolt **FaceList** konstrukciók, amely tárolja a válogatott lapok. |
|[PersonGroupPersonExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| Ez az osztály kezeli a felhőben tárolt **személy** konstrukciók, amely tárolja a lapok, amelyek egyetlen személyhez tartozik.|
|[PersonGroupOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| Ez az osztály kezeli a felhőben tárolt **PersonGroup** konstrukciókat, amelyek válogatott **személyobjektumokkészletét** tárolják. |
|[ShapshotOperations (ShapshotOperations)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)|Ez az osztály kezeli a Pillanatkép funkciót. Segítségével ideiglenesen mentheti az összes felhőalapú Face-adatát, és áttelepítheti ezeket az adatokat egy új Azure-előfizetésbe. |

## <a name="code-examples"></a>Kódpéldák

Az alábbi kódrészletek megmutatják, hogyan kell elvégezni a következő feladatokat a .NET Face ügyféltárával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Arcok felismerése a képeken](#detect-faces-in-an-image)
* [Hasonló arcok keresése](#find-similar-faces)
* [Személycsoport létrehozása és betanítása](#create-and-train-a-person-group)
* [Arc azonosítása](#identify-a-face)
* [Pillanatkép készítése az adatáttelepítésről](#take-a-snapshot-for-data-migration)


## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE]
> Ez a rövid útmutató feltételezi, hogy [környezeti változókat hozott](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) `FACE_SUBSCRIPTION_KEY` létre `FACE_ENDPOINT`a Face kulcshoz és a végponthoz, amelynek neve és.

Egy új módszer, példányosítja az ügyfél a végpont és a kulcs. Hozzon létre egy **[ApiKeyServiceClients objektumot](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials?view=azure-dotnet)** a kulccsal, és használja azt a végponttal egy **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** objektum létrehozásához.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_auth)]

Ezt a módszert valószínűleg meg `Main` szeretné hívni a metódusban.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_client)]

### <a name="declare-helper-fields"></a>Segítő mezők deklarálása

A következő mezőkre van szükség a később hozzáadni kívánt Arc műveletek közül többhez. Az osztály gyökerében adja meg a következő URL-karakterláncot. Ez az URL-cím mintaképek mappájára mutat.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_image_url)]

Definiáljon karakterláncokat, amelyek a különböző felismerési modelltípusokra mutatnak. Később megadhatja, hogy melyik felismerési modellt szeretné használni az arcfelismeréshez. Ezekről a beállításokról a [Felismerési modell megadása](../Face-API-How-to-Topics/specify-recognition-model.md) című témakörben talál tájékoztatást.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_models)]

## <a name="detect-faces-in-an-image"></a>Arcok felismerése a képeken

Adja hozzá a következő metódushívás a **fő** módszer. Legközelebb definiálja a módszert. A végső észlelési művelet egy **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** objektumot, egy kép URL-címét és egy felismerési modellt vesz igénybe.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_call)]

### <a name="get-detected-face-objects"></a>Észlelt arcobjektumok

A következő kódblokkban `DetectFaceExtract` a módszer három kép arcát észleli az adott URL-címen, és létrehozza az **[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** objektumok listáját a program memóriájában. A **[FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** értékek listája határozza meg, hogy mely szolgáltatásokat kell kibontani. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Észlelt arcadatok megjelenítése

A `DetectFaceExtract` metódus többi része elemzi és kinyomtatja az attribútumadatokat az egyes észlelt arcokhoz. Minden attribútumot külön kell megadni az eredeti arcfelismerő API-hívásban (a **[FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** listában). A következő kód feldolgoz minden attribútumot, de valószínűleg csak egyet vagy néhányat kell használnia.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Hasonló arcok keresése

A következő kód egyetlen észlelt arcot (forrást) vesz igénybe, és más arcok (cél) egy csoportjában keres egyezést. Ha egyezést talál, kinyomtatja az egyező arc azonosítóját a konzolra.

### <a name="detect-faces-for-comparison"></a>Arcok észlelése összehasonlításhoz

Először definiáljon egy második arcfelismerési módszert. Az összehasonlítás előtt észlelnie kell a képeken lévő arcokat, és ez az észlelési módszer az összehasonlítási műveletekre van optimalizálva. Nem bontja ki a részletes arcattribútumokat, mint a fenti szakaszban, és más felismerési modellt használ.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Találat keresése

A következő módszer észleli az arcokat a célképek készletében és egyetlen forrásképen. Ezután összehasonlítja őket, és megkeresi az összes olyan célképet, amely hasonlít a forrásképhez.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Találatok nyomtatása

A következő kód kinyomtatja az egyezés részleteit a konzolra:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar_print)]

## <a name="identify-a-face"></a>Arc azonosítása

Az Azonosítás művelet egy személy (vagy több személy) képét veszi fel, és a képen lévő egyes arcok identitását keresi. Összehasonlítja az egyes észlelt arc egy **PersonGroup**, egy adatbázis a különböző **személy** objektumok, amelyek arcvonásai ismertek. Az Azonosítás művelet elvégzéséhez először létre kell hoznia és be kell képeznie **egy**

### <a name="create-and-train-a-person-group"></a>Személycsoport létrehozása és betanítása

A következő kód létrehoz egy **PersonGroup** hat különböző **személy** objektumok. Minden **egyes személyt** egy példaképhez társít, majd kiképzi, hogy minden egyes személyt az arcvonásai alapján ismerjen fel. **A Személy** és **a PersonGroup** objektumok az Ellenőrzés, azonosítás és csoport műveletekben használatosak.

#### <a name="create-persongroup"></a>Személycsoport létrehozása

Deklaráljon egy karakterlánc-változót az osztály gyökerében, amely a létrehozandó **Személycsoport** azonosítóját jelöli.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_declare)]

Új módszerrel adja hozzá a következő kódot. Ez a módszer végrehajtja az Azonosítás műveletet. Az első kódblokk a személyek nevét társítja a példaképeikkel.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_files)]

Ezután adja hozzá a következő kódot, hogy hozzon létre egy **személy** objektumot minden egyes személy számára a szótárban, és adja hozzá az arcadatokat a megfelelő képekből. Minden **személy** objektum ugyanahhoz a **PersonGrouphoz** van társítva az egyedi azonosítókarakterláncán keresztül. Ne felejtse el `client` `url`átadni `RECOGNITION_MODEL1` a változókat , és adja át ezt a módszert.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_create)]

#### <a name="train-persongroup"></a>Betanítási személycsoport

Miután kivette a képadatokat a képekből, és különböző **személyobjektumokba** rendezte azokat, be kell tanítania a **PersonGroup ot,** hogy azonosítsa az egyes személyobjektumaihoz társított vizuális funkciókat. **Person** A következő kód meghívja az aszinkron **vonat** metódust, és lekérdezi az eredményeket, és kinyomtatja az állapotot a konzolra.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_train)]

Ez **a személycsoport** és a hozzá tartozó **Személy** objektumok készen állnak az Ellenőrzés, az Azonosítás vagy a Csoport műveletekben való használatra.

### <a name="get-a-test-image"></a>Tesztkép beszereznie

Figyelje meg, hogy egy [személycsoport létrehozása és betanítása](#create-and-train-a-person-group) kódja változót `sourceImageFileName`határoz meg. Ez a változó annak&mdash;a forrásképnek felel meg, amelyazonosítja az azonosításra alkalmas személyeket.

### <a name="identify-faces"></a>Arcok azonosítása

A következő kód veszi a forrásképet, és létrehoz egy listát a képen észlelt összes arcról. Ezek azok az arcok, amelyeket a **PersonGroup**ellen azonosítanak.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify_sources)]

A következő kódrészlet meghívja a **IdentifyAsync** műveletet, és kinyomtatja az eredményeket a konzolra. Itt a szolgáltatás megkísérli a forráskép minden arcának egyeztetését az adott **személycsoportban**lévő **személlyel.** Ezzel bezárja az Azonosítás imitátrát.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>Pillanatkép készítése az adatáttelepítésről

A Pillanatképek funkció lehetővé teszi, hogy a mentett Face-adatokat, például egy betanított **PersonGroupot**áthelyezi egy másik Azure Cognitive Services Face-előfizetésbe. Ezt a funkciót akkor érdemes használnia, ha például létrehozott egy **PersonGroup** objektumot egy ingyenes próba-előfizetéssel, és át szeretné telepíteni egy fizetős előfizetésbe. A Pillanatképek funkció áttekintése az [arcadatok áttelepítése](../Face-API-How-to-Topics/how-to-migrate-face-data.md) című témakörben található.

Ebben a példában áttelepíti a [Személycsoport létrehozása és betanítása csoportban](#create-and-train-a-person-group)létrehozott **személycsoportot.** Ezt a szakaszt először befejezheti, vagy létrehozhatja saját Face-adatkonstrukcióit az áttelepítéshez.

### <a name="set-up-target-subscription"></a>Cél-előfizetés beállítása

Először is egy második Azure-előfizetéssel kell rendelkeznie egy Face-erőforrással; ezt a Beállítás szakaszban ismertetett lépések végrehajtásával teheti [meg.](#setting-up) 

Ezután adja meg a `Main` következő változókat a program metódusában. Új környezeti változókat kell létrehoznia az Azure-fiók előfizetési azonosítójához, valamint az új (cél) fiók kulcsához, végpontjához és előfizetési azonosítóhoz. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_vars)]

Ebben a példában deklaráljon egy változót a **célszemélycsoport**&mdash;azonosítójára, amelyaz új előfizetéshez tartozó objektumot, amelybe az adatokat másolni fogja.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_vars)]

### <a name="authenticate-target-client"></a>Célügyfél hitelesítése

Ezután adja hozzá a kódot a másodlagos Face-előfizetés hitelesítéséhez.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_client)]

### <a name="use-a-snapshot"></a>Pillanatkép használata

A pillanatkép-műveletek többi részének aszinkron metóduson belül kell zajlania. 

1. Az első lépés a pillanatkép **készítése,** amely az eredeti előfizetés arcadatait egy ideiglenes felhőbe menti. Ez a módszer egy azonosítót ad vissza, amelyet a művelet állapotának lekérdezéséhez használ.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_take)]

1. Ezután kérdezze le az azonosítót, amíg a művelet be nem fejeződik.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_take_wait)]

1. Ezután az **alkalmazási** művelettel írja be az arcadatokat a cél-előfizetésbe. Ez a módszer azonosító értéket is ad vissza.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_apply)]

1. Ismét kérdezze le az új azonosítót, amíg a művelet be nem fejeződik.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_apply)]

1. Végül fejezze be a try/catch blokkot, és fejezze be a módszert.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_trycatch)]

Ezen a ponton az új **PersonGroup-objektumnak** ugyanazokat az adatokat kell mutatnia, mint az eredetinek, és elérhetőnek kell lennie az új (cél) Azure Face-előfizetésből.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást az `dotnet run` alkalmazáskönyvtárból a paranccsal.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, hogy tisztítsák meg, és távolítsa el a Cognitive Services-előfizetés, törölheti az erőforrás vagy erőforráscsoport. Az erőforráscsoport törlése a hozzá társított egyéb erőforrásokat is törli.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Ha ebben a rövid útmutatóban létrehozott egy **PersonGroup csoportot,** és törölni szeretné, futtassa a következő kódot a programban:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_delete)]

Adja meg a törlési módszert a következő kóddal:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_deletepersongroup)]

Továbbá ha ebben a rövid útmutatóban a Pillanatkép szolgáltatás használatával telepítette át az adatokat, törölnie kell a cél-előfizetésbe mentett **PersonGroup-ot** is.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_target_persongroup_delete)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja a Face könyvtárat a .NET-hez alapfeladatok elvégzésére. Ezután tekintse meg a referenciadokumentációt, ha többet szeretne megtudni a könyvtárról.

> [!div class="nextstepaction"]
> [Face API-hivatkozás (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [Mi az a Face szolgáltatás?](../overview.md)
* A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs)
