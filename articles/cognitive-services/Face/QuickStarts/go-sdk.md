---
title: 'Rövid útmutató: Face client library for Go | Microsoft dokumentumok'
description: Ismerkedés a Face-ügyféltár go.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 2db40150167a8f16242b2feb15b77820fa1970a9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76941429"
---
# <a name="quickstart-face-client-library-for-go"></a>Rövid útmutató: Face-ügyfélkódtár az ugráshoz

Ismerkedés a Face-ügyféltár go. Az alábbi lépésekkel telepítheti a műsortárat, és kipróbálhatja az alapvető feladatokra vonatkozó példáinkat. A Face szolgáltatás hozzáférést biztosít a képeken lévő emberi arcok észlelésére és felismerésére szolgáló speciális algoritmusokhoz.

Használja a Face service ügyfélkönyvtárát az Ugrás hoz:

* [Arcok felismerése a képeken](#detect-faces-in-an-image)
* [Hasonló arcok keresése](#find-similar-faces)
* [Személycsoport létrehozása és betanítása](#create-and-train-a-person-group)
* [Arc azonosítása](#identify-a-face)
* [Pillanatkép készítése az adatáttelepítésről](#take-a-snapshot-for-data-migration)

[Referenciadokumentáció](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face) | [Könyvtár forráskódJának](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face) | [SDK letöltése](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Go](https://golang.org/dl/) legújabb verziója

## <a name="set-up"></a>Beállítás

### <a name="create-a-face-azure-resource"></a>Face Azure-erőforrás létrehozása 

Kezdje el használni a Face szolgáltatást egy Azure-erőforrás létrehozásával. Válassza ki az Önnek megfelelő erőforrástípust:

* [Próbaverziós erőforrás](https://azure.microsoft.com/try/cognitive-services/#decision) (nincs szükség Azure-előfizetésre): 
    * Hét napig érvényes, ingyen. A regisztráció után egy próbakulcs és egy végpont lesz elérhető az [Azure webhelyén.](https://azure.microsoft.com/try/cognitive-services/my-apis/) 
    * Ez egy nagyszerű lehetőség, ha szeretné kipróbálni face szolgáltatás, de nem rendelkezik Azure-előfizetéssel.
* [Face szolgáltatáserőforrás:](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)
    * Az Azure Portalon keresztül érhető el, amíg nem törli az erőforrást.
    * Az ingyenes tarifacsomag használatával próbálja ki a szolgáltatást, és frissítsen később egy fizetett szintre éles környezetben.
* [Többszervizes erőforrás:](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)
    * Az Azure Portalon keresztül érhető el, amíg nem törli az erőforrást.  
    * Használja ugyanazt a kulcsot és végpontot az alkalmazásokhoz, több Cognitive Services-ben.

### <a name="create-an-environment-variable"></a>Környezeti változó létrehozása

>[!NOTE]
> július 1-je után létrehozott nem próbaerőforrások végpontjai az alábbi egyéni altartomány-formátumot használják. További információt és a regionális végpontok teljes listáját a [Cognitive Services egyéni altartománynevei című témakörben talál.](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains) 

A létrehozott erőforrás kulcsának és végpontjának használatával hozzon létre két környezeti változót a hitelesítéshez:
* `FACE_SUBSCRIPTION_KEY`- A kérések hitelesítéséhez.
* `FACE_ENDPOINT`- Az erőforrás-végpont API-kérések küldéséhez. Így fog kinézni: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Használja az operációs rendszerre vonatkozó utasításokat.
<!-- replace the below endpoint and key examples -->
#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx FACE_SUBSCRIPTION_KEY <replace-with-your-product-name-key>
setx FACE_ENDPOINT <replace-with-your-product-name-endpoint>
```

A környezeti változó hozzáadása után indítsa újra a konzolablakot.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

A környezeti változó hozzáadását követően futtassa a `source ~/.bashrc` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

#### <a name="macos"></a>[Macos](#tab/unix)

A program `.bash_profile`szerkesztése és a környezeti változó hozzáadása:

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

A környezeti változó hozzáadását követően futtassa a `source .bash_profile` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.
***

### <a name="create-a-go-project-directory"></a>Go projektkönyvtár létrehozása

Egy konzolablakban (cmd, PowerShell, Terminál, Bash) hozzon létre `my-app`egy új munkaterületet a Go projekthez, amelynek neve , és keresse meg azt.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

A munkaterület három mappát tartalmaz:

* **src** - Ez a könyvtár forráskódot és csomagokat tartalmaz. A paranccsal `go get` telepített csomagok ebben a mappában lesznek.
* **pkg** - Ez a könyvtár tartalmazza a lefordított Go csomag objektumokat. Ezek a fájlok `.a` mind rendelkeznek kiterjesztéssel.
* **bin** - Ez a könyvtár a futtatáskor `go install`létrehozott bináris végrehajtható fájlokat fogja tartalmazni.

> [!TIP]
> A Go-munkaterület szerkezetéről a [Go nyelvi dokumentációjában](https://golang.org/doc/code.html#Workspaces)olvashat bővebben. Ez az útmutató `$GOPATH` a `$GOROOT`beállítással és a beállításával kapcsolatos információkat tartalmazza.

### <a name="install-the-client-library-for-go"></a>Az ügyféltár telepítése az Ugráshoz

Ezután telepítse az ügyféltárat az Ugráshoz:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

vagy ha dep-t használ, a társzalag-futtatáson belül:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>Go-alkalmazás létrehozása

Ezután hozzon létre egy fájlt `sample-app.go`a **src** könyvtárban, amelynek neve:

```bash
cd src
touch sample-app.go
```

Nyissa `sample-app.go` meg a kívánt IDE vagy szövegszerkesztőben. Ezután adja hozzá a csomag nevét, és importálja a következő könyvtárakat:

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

Ezután elkezdi hozzáadni a kódot a különböző Face-szolgáltatás műveletek végrehajtásához.

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek kezelik a Face service Go SDK néhány főbb funkcióját.

|Név|Leírás|
|---|---|
|[Alapügyfél](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | Ez az osztály a Face szolgáltatás használatára vonatkozó engedélyt jelöli, és minden Face funkcióhoz szüksége van rá. Az előfizetési adatokkal azonnal létrehozhatja azt, és más osztályok példányainak létrehozásához használhatja őket. |
|[Ügyfél](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|Ez az osztály kezeli az emberi arcokkal elvégezhető alapvető észlelési és felismerési feladatokat. |
|[DetectedFace (DetectedFace)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|Ez az osztály a kép egyetlen arcáról észlelt összes adatot jelöli. Segítségével részletes információkat kérhet az arcról.|
|[ListClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|Ez az osztály kezeli a felhőben tárolt **FaceList** konstrukciók, amely tárolja a válogatott lapok. |
|[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| Ez az osztály kezeli a felhőben tárolt **személy** konstrukciók, amely tárolja a lapok, amelyek egyetlen személyhez tartozik.|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| Ez az osztály kezeli a felhőben tárolt **PersonGroup** konstrukciókat, amelyek válogatott **személyobjektumokkészletét** tárolják. |
|[SnapshotClient (Pillanatfelvétel-ügyfél)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|Ez az osztály kezeli a Pillanatkép funkciót. Segítségével ideiglenesen mentheti az összes felhőalapú Face-adatát, és áttelepítheti ezeket az adatokat egy új Azure-előfizetésbe. |

## <a name="code-examples"></a>Kódpéldák

Ezek a kódminták bemutatják, hogyan végezhet el alapvető feladatokat a Face Service-ügyfélkódtár go hoz használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Arcok felismerése a képeken](#detect-faces-in-an-image)
* [Hasonló arcok keresése](#find-similar-faces)
* [Személycsoport létrehozása és betanítása](#create-and-train-a-person-group)
* [Arc azonosítása](#identify-a-face)
* [Pillanatkép készítése az adatáttelepítésről](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE] 
> Ez a rövid útmutató feltételezi, hogy [környezeti változókat hozott](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) `FACE_SUBSCRIPTION_KEY` létre `FACE_ENDPOINT` a Face kulcshoz és a végponthoz, amelyeket elnevezett és megfelelően.

Hozzon létre egy **fő** függvényt, és adja hozzá a következő kódot, hogy példányosítani egy ügyfél a végpont és a kulcs. Hozzon létre egy **[CognitiveServicesAuthorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** objektumot a kulccsal, és használja azt a végpontot egy **[ügyfélobjektum](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** létrehozásához. Ez a kód egy környezeti objektumot is létrehoz, amely az ügyfélobjektumok létrehozásához szükséges. Azt is meghatározza egy távoli hely, ahol néhány, a minta képek ebben a rövid útmutató található.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>Arcok felismerése a képeken

Adja hozzá a következő kódot a **fő** módszerhez. Ez a kód egy távoli mintaképet határoz meg, és meghatározza, hogy mely arcjellemzőket kell kivonni a lemezképből. Azt is meghatározza, hogy melyik AI-modellt használja az észlelt lap(ok) adatainak kinyeréséhez. Ezekről a beállításokról a [Felismerési modell megadása](../Face-API-How-to-Topics/specify-recognition-model.md) című témakörben talál tájékoztatást. Végül a **[DetectWithURL](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** metódus elvégez egy arcfelismerő műveletet a képen, és menti az eredményeket a programmemóriába.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Észlelt arcadatok megjelenítése

A következő kódblokk az **[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** objektumok tömbjének első elemét veszi fel, és kinyomtatja annak attribútumait a konzolra. Ha több arccal rendelkező képet használt, akkor inkább a tömbön keresztül kell végighaladnia.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>Hasonló arcok keresése

A következő kód egyetlen észlelt arcot (forrást) vesz igénybe, és más arcok (cél) egy csoportjában keres egyezést. Ha egyezést talál, kinyomtatja az egyező arc azonosítóját a konzolra.

### <a name="detect-faces-for-comparison"></a>Arcok észlelése összehasonlításhoz

Először mentse nannem a képek [lapjainak észlelése](#detect-faces-in-an-image) szakaszban észlelt arcra mutató hivatkozást. Ez az arc lesz a forrás.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

Ezután adja meg a következő kódot egy másik képen lévő lapok észleléséhez. Ezek az arcok lesznek a célpontok.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>Találat keresése

A következő kód a **[FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** metódust használja a forráslapnak megfelelő összes célarc megkereséséhez.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>Találatok nyomtatása

A következő kód kinyomtatja az egyezés részleteit a konzolra.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="create-and-train-a-person-group"></a>Személycsoport létrehozása és betanítása

Ebben a forgatókönyvben való átlépéshez a következő képeket kell https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/imagesmentenie a projekt gyökérkönyvtárába: .

Ez a képcsoport három egyarcú képet tartalmaz, amelyek három különböző embernek felelnek meg. A kód három **PersonGroup Person** objektumot határoz meg, `woman` `man`és `child`társítja őket a programmal kezdődő képfájlokhoz, és a hoz.

### <a name="create-persongroup"></a>Személycsoport létrehozása

Miután letöltötte a képeket, adja hozzá a következő kódot a **fő** módszer aljához. Ez a kód hitelesíti a **[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** objektumot, majd egy új **PersonGroup**csoport definiálására használja.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>Személyek létrehozása

A következő kódblokk hitelesíti a **[PersonGroupPersonClient ügyfelet,](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** és három új **PersonGroup Person** objektum definiálására használja. Ezek az objektumok egyenként egyetlen személyt képviselnek a képkészletben.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>Lapok hozzárendelése személyekhez

A következő kód a képeket előtagja szerint rendezi, felismeri az arcokat, és a képfájl neve alapján hozzárendeli az arcokat az egyes **PersonGroup Person** objektumhoz.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

### <a name="train-persongroup"></a>Betanítási személycsoport

Miután hozzárendelte az arcokat, betanítja a **PersonGroup csoportot,** hogy azonosíthassa az egyes személyobjektumaihoz társított vizuális funkciókat. **Person** A következő kód meghívja az aszinkron **vonat** metódust, és lekérdezi az eredményt, és kinyomtatja az állapotot a konzolra.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

## <a name="identify-a-face"></a>Arc azonosítása

A következő kód több arccal rendelkező képet vesz fel, és a képen lévő egyes személyek identitásának megkeresésére keresi a képet. Összehasonlítja az egyes észlelt arc egy **PersonGroup**, egy adatbázis a különböző **személy** objektumok, amelyek arcvonásai ismertek.

> [!IMPORTANT]
> A példa futtatásához először futtatnia kell a kódot a [Létrehozás és a személycsoport betanítása mezőben.](#create-and-train-a-person-group)

### <a name="get-a-test-image"></a>Tesztkép beszereznie

A következő kód a projekt gyökerében egy _képteszt-kép-személy-group.jpg_ fájlt keres, és betölti azt a program memóriájába. Ezt a képet ugyanabban a tárházban találhatja meg, mint https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/imagesa Személy létrehozása és [betanítása csoportban](#create-and-train-a-person-group)használt képeket: .

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>Forráslapok észlelése a tesztképen

A következő kódblokk nem rendes arcfelismerés a teszt képet letölteni az összes arcok, és mentse őket egy tömb.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces"></a>Arcok azonosítása

A **[Identify](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** metódus az észlelt lapok tömbjét veszi fel, és összehasonlítja azadott **PersonGroup csoporttal** (a korábbi szakaszban definiálva és betanítva). Ha egy észlelt arcot össze tud egyeztetni a csoportban lévő **személlyel,** menti az eredményt.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

Ez a kód részletes egyezési eredményeket nyomtat a konzolra.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


## <a name="verify-faces"></a>Arcok ellenőrzése

Az Ellenőrzés művelet egy arcazonosítót és egy másik arcazonosítót vagy egy **Személy** objektumot vesz fel, és meghatározza, hogy ugyanahhoz a személyhez tartoznak-e.

A következő kód két forráskép arcait észleli, majd mindegyiket a célképből észlelt arccal szemben ellenőrzi.

### <a name="get-test-images"></a>Tesztképek beszereznie

A következő kódblokkok deklarálják azokat a változókat, amelyek az ellenőrzési művelet cél- és forrásképeire mutatnak.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>Arcok észlelése ellenőrzésre

A következő kód észleli az arcokat a forrás- és a célképekben, és menti őket változókba.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>Az ellenőrzés eredményeinek beszerezni

A következő kód összehasonlítja az egyes forrásképeket a célképpel, és kinyomtat egy üzenetet, amely jelzi, hogy ugyanahhoz a személyhez tartoznak-e.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]


## <a name="take-a-snapshot-for-data-migration"></a>Pillanatkép készítése az adatáttelepítésről

A Pillanatképek funkció lehetővé teszi, hogy a mentett arcadatokat, például egy betanított **PersonGroupot**egy másik Azure Cognitive Services Face-előfizetésbe helyezze át. Ezt a funkciót akkor használhatja, ha például létrehozott egy **PersonGroup-objektumot** egy ingyenes próba-előfizetéssel, és most át szeretné telepíteni egy fizetős előfizetésbe. Tekintse meg az [arcadatok áttelepítése](../Face-API-How-to-Topics/how-to-migrate-face-data.md) című témakört a Pillanatképek funkció átfogó áttekintéséhez.

Ebben a példában áttelepíti a [Személycsoport létrehozása és betanítása csoportban](#create-and-train-a-person-group)létrehozott **személycsoportot.** Ezt a szakaszt először befejezheti, vagy használhatja a saját Face adatkonstrukció(i)t.

### <a name="set-up-target-subscription"></a>Cél-előfizetés beállítása

Először is egy második Azure-előfizetéssel kell rendelkeznie egy Face-erőforrással; ehhez ismételje meg a [Beállítás](#set-up) szakasz lépéseit. 

Ezután hozza létre a következő változókat a **fő** módszer tetején. Új környezeti változókat is létre kell hoznia az Azure-fiók előfizetési azonosítójához, valamint az új (cél) fiók kulcsához, végpontjához és előfizetési azonosítóhoz.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_target_client)]

Ezután helyezze az előfizetés-azonosító értékét egy tömbbe a következő lépésekhez.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_id)]

### <a name="authenticate-target-client"></a>Célügyfél hitelesítése

A parancsfájl későbbi részében mentse az eredeti ügyfélobjektumot forrásügyfélként, majd hitelesítse az új ügyfélobjektumot a cél-előfizetéshez. 

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_auth)]

### <a name="take-a-snapshot"></a>Pillanatkép készítése

A következő lépés a **[Felvétel](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Take)** című pillanatfelvétel készítése, amely az eredeti előfizetés arcadatait egy ideiglenes felhőbe menti. Ez a módszer egy azonosítót ad vissza, amelyet a művelet állapotának lekérdezéséhez használ.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_take)]

Ezután kérdezze le az azonosítót, amíg a művelet be nem fejeződik.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_query)]

### <a name="apply-the-snapshot"></a>A pillanatkép alkalmazása

Az **[Alkalmaz](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Apply)** művelet segítségével írja be az újonnan feltöltött arcadatokat a cél-előfizetésbe. Ez a módszer azonosítót is ad vissza.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply)]

Ismét kérdezze le az azonosítót, amíg a művelet befejeződik.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply_query)]

Miután elvégezte ezeket a lépéseket, az új (cél) előfizetésből elérheti az arcadat-konstrukciókat.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa a `go run [arguments]` Go alkalmazást az alkalmazáskönyvtárból származó paranccsal.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, hogy tisztítsák meg, és távolítsa el a Cognitive Services-előfizetés, törölheti az erőforrás vagy erőforráscsoport. Az erőforráscsoport törlése a hozzá társított egyéb erőforrásokat is törli.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Ha létrehozott egy **PersonGroup csoportot** ebben a rövid útmutatóban, és törölni szeretné, hívja meg a **[Törlés](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** metódust. Ha ebben a rövid útmutatóban a Pillanatkép szolgáltatás használatával telepítette át az adatokat, törölnie kell a cél-előfizetésbe mentett **PersonGroup csoportot** is.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja a Face-kódtár a Go alapfeladatok elvégzéséhez. Ezután tekintse meg a referenciadokumentációt, ha többet szeretne megtudni a könyvtárról.

> [!div class="nextstepaction"]
> [Face API-hivatkozás (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [Mi az a Face szolgáltatás?](../overview.md)
* A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go)
