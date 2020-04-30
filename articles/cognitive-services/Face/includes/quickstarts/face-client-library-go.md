---
title: Face go ügyféloldali kódtár – rövid útmutató
description: Ismerkedés a Face ügyféloldali függvénytárával a Go-ban.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: include
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: a09b79992f669c296fe0a674179fde5f38e8fc8a
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82149381"
---
Ismerkedés a Face ügyféloldali függvénytárával a Go-ban. Az alábbi lépéseket követve telepítheti a könyvtárat, és kipróbálhatja a példákat az alapszintű feladatokhoz. A Face szolgáltatás hozzáférést biztosít a speciális algoritmusokhoz a képeken található emberi arcok észleléséhez és felismeréséhez.

A következőhöz való ugráshoz használja a Face Service ügyféloldali függvénytárát:

* [Arcok felismerése a képeken](#detect-faces-in-an-image)
* [Hasonló arcok keresése](#find-similar-faces)
* [Személy csoport létrehozása és betanítása](#create-and-train-a-person-group)
* [Arc azonosítása](#identify-a-face)
* [Pillanatkép készítése az adatok áttelepítéséhez](#take-a-snapshot-for-data-migration)

[Útmutató a dokumentáció](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face) | [könyvtár forráskódjának](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face) | [SDK-letöltéséhez](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Go](https://golang.org/dl/) legújabb verziója

## <a name="set-up"></a>Beállítás

### <a name="create-a-face-azure-resource"></a>Face Azure-erőforrás létrehozása 

Kezdje el használni a Face szolgáltatást egy Azure-erőforrás létrehozásával. Válassza ki az Ön számára legmegfelelőbb erőforrás-típust:

* [Próbaverziós erőforrás](https://azure.microsoft.com/try/cognitive-services/#decision) (nincs szükség Azure-előfizetésre): 
    * Hét napig ingyenesen használható. A regisztrációt követően a próbaverziós kulcs és a végpont elérhető lesz az [Azure webhelyén](https://azure.microsoft.com/try/cognitive-services/my-apis/). 
    * Ez nagyszerű megoldás, ha szeretné kipróbálni a Face Service-t, de nem rendelkezik Azure-előfizetéssel.
* Egy [Face Service-erőforrás](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace):
    * A Azure Portalon keresztül érhető el, amíg nem törli az erőforrást.
    * Az ingyenes díjszabási csomaggal próbálja ki a szolgáltatást, és később frissítsen egy fizetős szintre az éles környezetben.
* [Több szolgáltatásból álló erőforrás](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne):
    * A Azure Portalon keresztül érhető el, amíg nem törli az erőforrást.  
    * Használja ugyanazt a kulcsot és végpontot az alkalmazásaihoz, több Cognitive Services között.

### <a name="create-an-environment-variable"></a>Környezeti változó létrehozása

>[!NOTE]
> Az 2019. július 1. után létrehozott, nem próbaverziós erőforrásokhoz használt végpontok az alább látható egyéni altartomány-formátumot használják. További információk és a regionális végpontok teljes listája: [Cognitive Services egyéni altartománynevei nevei](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Ha a kulcsot és a végpontot a létrehozott erőforrás alapján hozza létre, hozzon létre két környezeti változót a hitelesítéshez:
* `FACE_SUBSCRIPTION_KEY`– A kérések hitelesítéséhez szükséges erőforrás-kulcs.
* `FACE_ENDPOINT`– Az erőforrás-végpont API-kérelmek küldéséhez. A következőképpen fog kinézni: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Használja az operációs rendszerének utasításait.
<!-- replace the below endpoint and key examples -->
#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx FACE_SUBSCRIPTION_KEY <replace-with-your-product-name-key>
setx FACE_ENDPOINT <replace-with-your-product-name-endpoint>
```

A környezeti változó hozzáadása után indítsa újra a konzolablak ablakát.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

A környezeti változó hozzáadását követően futtassa a `source ~/.bashrc` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

#### <a name="macos"></a>[macOS](#tab/unix)

Szerkessze `.bash_profile`a t, és adja hozzá a környezeti változót:

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

A környezeti változó hozzáadását követően futtassa a `source .bash_profile` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.
***

### <a name="create-a-go-project-directory"></a>Go-projekt könyvtárának létrehozása

A konzol ablakban (cmd, PowerShell, Terminal, bash) hozzon létre egy új munkaterületet a go-projekt `my-app`számára, és keresse meg a nevet.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

A munkaterület három mappát fog tartalmazni:

* **src** – ez a könyvtár a forráskódot és a csomagokat fogja tartalmazni. A `go get` paranccsal telepített csomagok ebben a mappában lesznek.
* **pkg** – ez a könyvtár tartalmazni fogja a lefordított go Package objektumokat. Ezek a `.a` fájlok mindegyike rendelkezik bővítménnyel.
* **bin** – ez a könyvtár fogja tartalmazni a futtatásakor `go install`létrehozott bináris végrehajtható fájlokat.

> [!TIP]
> Ha többet szeretne megtudni a go-munkaterület struktúrájáról, tekintse meg a [Go Language dokumentációját](https://golang.org/doc/code.html#Workspaces). Ez az útmutató a és `$GOPATH` `$GOROOT`a beállításával kapcsolatos információkat tartalmaz.

### <a name="install-the-client-library-for-go"></a>Az ügyféloldali kódtár telepítése a Go-hoz

Ezután telepítse az ügyféloldali kódtárat a Go-hoz:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

vagy ha a DEP-t használja a tárházon belül, futtassa a következőket:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>Go-alkalmazás létrehozása

Következő lépésként hozzon létre egy **src** fájlt a ( `sample-app.go`z) nevű src könyvtárban:

```bash
cd src
touch sample-app.go
```

Nyissa meg `sample-app.go` az előnyben részesített ide-vagy szövegszerkesztőben. Ezután adja hozzá a csomag nevét, és importálja a következő könyvtárakat:

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

Ezt követően megkezdheti a kód hozzáadását a különböző Face Service-műveletek végrehajtásához.

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek kezelik a Face Service go ügyféloldali függvénytárának főbb funkcióit.

|Name (Név)|Leírás|
|---|---|
|[BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | Ez az osztály a Face szolgáltatás használatára vonatkozó engedélyt jelöli, és minden arc funkcióhoz szüksége van rá. Ezt az előfizetési adatok alapján hozza létre, és más osztályok példányainak előállítására használja. |
|[Ügyfél](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|Ez az osztály az emberi arcokkal elvégezhető alapvető észlelési és felismerési feladatokat kezeli. |
|[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|Ez az osztály a rendszerkép egyetlen arca által észlelt összes adatmennyiséget jelképezi. Ezzel az oldallal kapcsolatos részletes információkat kérhet le.|
|[ListClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|Ez az osztály kezeli a felhőben tárolt **FaceList** -szerkezeteket, amelyek az arcok válogatott készleteit tárolják. |
|[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| Ez az osztály kezeli a felhőalapú tárolt **személyek** szerkezetét, amelyek egyetlen személyhez tartozó arcok készletét tárolják.|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| Ez az osztály kezeli a felhőben tárolt **PersonGroup** -szerkezeteket, amelyek a különböző **személyre** kiterjedő objektumok készletét tárolják. |
|[SnapshotClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|Ez az osztály kezeli a pillanatkép funkcióit. A használatával ideiglenesen mentheti az összes felhőalapú Arcfelismerés, és áttelepítheti az adott adatait egy új Azure-előfizetésbe. |

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan végezheti el az alapszintű feladatokat a go szolgáltatás ügyféloldali kódtára használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Arcok felismerése a képeken](#detect-faces-in-an-image)
* [Hasonló arcok keresése](#find-similar-faces)
* [Személy csoport létrehozása és betanítása](#create-and-train-a-person-group)
* [Arc azonosítása](#identify-a-face)
* [Pillanatkép készítése az adatok áttelepítéséhez](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE] 
> Ez a rövid útmutató azt feltételezi, hogy [létrehozott egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) az arc kulcsához `FACE_ENDPOINT` és a végponthoz, illetve a nevet `FACE_SUBSCRIPTION_KEY` .

Hozzon létre egy **fő** függvényt, és adja hozzá a következő kódot egy ügyfél létrehozásához a végponttal és a kulccsal. Hozzon létre egy **[CognitiveServicesAuthorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** objektumot a kulccsal, és használja azt a végponttal egy **[ügyfél](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** -objektum létrehozásához. Ez a kód egy környezeti objektumot is létrehoz, amely az ügyfélalkalmazások létrehozásához szükséges. Emellett olyan távoli helyet is meghatároz, ahol a rövid útmutatóban szereplő néhány minta lemezkép található.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>Arcok felismerése a képeken

Adja hozzá a következő kódot a **Main** metódushoz. Ez a kód egy távoli mintaképet határoz meg, és meghatározza, hogy mely arc-funkciókat kell kinyerni a rendszerképből. Azt is meghatározza, hogy melyik AI-modellt kell használnia az észlelt arc (ok) ből származó adatok kinyeréséhez. A beállításokkal kapcsolatos információkért tekintse [meg a felismerési modell megadása](../../Face-API-How-to-Topics/specify-recognition-model.md) című témakört. Végül a **[DetectWithURL](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** metódus az Arcfelismerés műveletet hajtja végre a képen, és menti az eredményeket a program memóriájában.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Észlelt Arcfelismerés

A kód következő blokkja a **[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** objektumok tömb első elemét veszi fel, és kiírja az attribútumait a konzolra. Ha több képpel rendelkező rendszerképet használt, akkor inkább a tömbön keresztül kell megismételni a tömböt.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>Hasonló arcok keresése

A következő kód egyetlen észlelt arcot (forrást) használ, és a találatok kereséséhez más arcok (TARGET) készletet keres. Ha egyezést talál, kinyomtatja az egyeztetett arc AZONOSÍTÓját a konzolon.

### <a name="detect-faces-for-comparison"></a>Arcok észlelése összehasonlításhoz

Először mentse a képek szakaszban az [arcok észlelése](#detect-faces-in-an-image) során észlelt arcot. Ez az arc lesz a forrás.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

Ezután adja meg a következő kódot az arcok egy másik rendszerképben való észleléséhez. Ezek az arcok lesznek a célpontok.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>Egyezések keresése

A következő kód a **[FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** metódus használatával keresi meg az összes cél arcot, amely megfelel a forrás arcanek.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>Egyezések nyomtatása

A következő kód a megfeleltetés részleteit jeleníti meg a konzolon.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="create-and-train-a-person-group"></a>Személy csoport létrehozása és betanítása

Ennek a forgatókönyvnek a végrehajtásához a következő képeket kell mentenie a projekt gyökérkönyvtárában: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

A rendszerkép ezen csoportja három különböző személynek megfelelő egyoldalas rendszerképeket tartalmaz. A kód három PersonGroup- **személyt** határoz meg, és társítja azokat a (, `woman` `man`, és `child`) kezdetű képfájlokhoz.

### <a name="create-persongroup"></a>PersonGroup létrehozása

A lemezképek letöltése után adja hozzá a következő kódot a **Main** metódus aljához. Ez a kód hitelesít egy **[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** objektumot, majd egy új **PersonGroup**definiálásához használja.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>PersonGroup-személyek létrehozása

A kód következő blokkja hitelesít egy **[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** , és a használatával három új PersonGroup- **személyt** határoz meg. Ezek az objektumok a képek készletében egyetlen személyt képviselnek.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>Arcok társítása személyekhez

A következő kód a lemezképeket az előtagjaik alapján rendezi, észleli az arcokat, és hozzárendeli az arcokat a megfelelő **PersonGroup-személyekhez** a képfájl neve alapján.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

### <a name="train-persongroup"></a>PersonGroup betanítása

Az arcok hozzárendelése után betanítja a **PersonGroup** , hogy képes legyen azonosítani az egyes **személy** objektumaihoz kapcsolódó vizuális funkciókat. A következő kód meghívja az aszinkron **betanítási** módszert, és lekérdezi az eredményt, kinyomtatja az állapotot a konzolra.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

## <a name="identify-a-face"></a>Arc azonosítása

A következő kód több képpel rendelkező képet helyez el, és megkeresi az egyes személyek identitását a képen. Összehasonlítja az észlelt elemeket egy **PersonGroup**, egy olyan, a különböző **személy** objektumokat tartalmazó adatbázissal, amelyek az arc funkciói ismertek.

> [!IMPORTANT]
> A példa futtatásához először futtatnia kell a kódot [egy személy csoport létrehozása és betanítása csoportban](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Tesztelési rendszerkép beolvasása

A következő kód a projekt gyökerében található, amely egy _test-Image-person-Group. jpg_ képet keres, és betölti a program memóriájában. Ezt a lemezképet ugyanabban a tárházban találja, mint a [személyek létrehozása és betanítása csoportban](#create-and-train-a-person-group)használt rendszerképek: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>Forrásként szolgáló arcok észlelése a tesztelési képen

A következő kódrészlet az összes arc lekérésére és a tömbbe mentésére szolgáló, egyszerű Arcfelismerés.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces"></a>Arcok azonosítása

Az **[azonosított](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** metódus az észlelt arcok tömbjét veszi fel, és összehasonlítja őket az adott **PersonGroup** (a korábbi szakaszban meghatározott és betanított). Ha a csoport egyik tagjának is képesnek kell lennie az észlelt **személyre** , a rendszer menti az eredményt.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

A kód ezután a részletes egyezési eredményeket jeleníti meg a konzolon.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


## <a name="verify-faces"></a>Arcok ellenőrzése

Az ellenőrzési művelet egy arcfelismerés és egy másik Arcfelismerés, vagy egy **személy** objektum, és meghatározza, hogy ugyanahhoz a személyhez tartoznak-e.

A következő kód észleli az arcok két forrás képét, majd ellenőrzi, hogy mindegyiket egy célként megadott képpel szemben észleltek-e.

### <a name="get-test-images"></a>Tesztelési lemezképek beolvasása

A következő kódrészletek deklarálják azokat a változókat, amelyek az ellenőrzési művelet céljának és forrásának rendszerképeire mutatnak.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>Arcok észlelése ellenőrzéshez

A következő kód észleli a forrás-és a célként megadott képeket, és azokat változókba menti.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>Ellenőrzési eredmények beolvasása

A következő kód összehasonlítja az egyes forrás-lemezképeket a célként megadott képpel, és kinyomtat egy üzenetet, amely jelzi, hogy ugyanahhoz a személyhez tartoznak-e.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]


## <a name="take-a-snapshot-for-data-migration"></a>Pillanatkép készítése az adatok áttelepítéséhez

A pillanatképek funkció lehetővé teszi a mentett Arcfelismerés, például a betanított **PersonGroup**áthelyezését egy másik Azure Cognitive Services Face-előfizetésbe. Használhatja ezt a funkciót, ha például egy ingyenes próbaverziós előfizetéssel létrehozott egy **PersonGroup** objektumot, és most szeretné áttelepíteni egy fizetős előfizetésre. A pillanatképek szolgáltatás széles körű áttekintéséhez tekintse [meg az Arcfelismerés áttelepítését](../../Face-API-How-to-Topics/how-to-migrate-face-data.md) ismertető cikket.

Ebben a példában a [személy csoport létrehozása és betanítása](#create-and-train-a-person-group)során létrehozott **PersonGroup** telepíti át. Először hajtsa végre az adott szakaszt, vagy használjon saját Face adatszerkezet (eke) t.

### <a name="set-up-target-subscription"></a>Cél-előfizetés beállítása

Először is rendelkeznie kell egy másik Azure-előfizetéssel, egy Face erőforrással. Ezt úgy teheti meg, hogy megismétli a [beállítás](#set-up) szakasz lépéseit. 

Ezután hozza létre a következő változókat a **Main** metódus teteje közelében. Emellett új környezeti változókat is létre kell hoznia az Azure-fiók előfizetés-AZONOSÍTÓJÁRA, valamint az új (cél) fiók kulcsát, végpontját és előfizetési AZONOSÍTÓját.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_target_client)]

Ezután helyezze el az előfizetés-azonosító értékét egy tömbbe a következő lépésekhez.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_id)]

### <a name="authenticate-target-client"></a>A célként megadott ügyfél hitelesítése

A szkript későbbi részében mentse az eredeti ügyfél-objektumot a forrás-ügyfélként, majd hitelesítse az új ügyfél-objektumot a cél előfizetéséhez. 

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_auth)]

### <a name="take-a-snapshot"></a>Pillanatkép készítése

A következő lépés a pillanatkép elkészítése a **[Take](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Take)** használatával, amely az eredeti előfizetés adatait egy ideiglenes Felhőbeli helyre menti. Ez a metódus egy azonosítót ad vissza, amelyet a művelet állapotának lekérdezéséhez használ.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_take)]

Ezután kérdezze le az azonosítót, amíg a művelet be nem fejeződik.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_query)]

### <a name="apply-the-snapshot"></a>A pillanatkép alkalmazása

Az **[alkalmazás](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Apply)** művelettel írhatja be az újonnan feltöltött arc adatait a cél előfizetésbe. Ez a metódus egy azonosítót is visszaad.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply)]

Ismételje meg az azonosító lekérdezését egészen addig, amíg a művelet be nem fejeződik.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply_query)]

Miután elvégezte ezeket a lépéseket, elérheti a Face adatok szerkezeteit az új (cél) előfizetésből.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa a go alkalmazást a paranccsal `go run [arguments]` az alkalmazás könyvtárából.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Ha ebben a rövid útmutatóban létrehozott egy **PersonGroup** , és törölni szeretné, hívja meg a **[delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** metódust. Ha ebben a rövid útmutatóban az adatok áttelepítve lettek a pillanatkép-szolgáltatással, törölnie kell a megcélzott előfizetésbe mentett **PersonGroup** is.

## <a name="next-steps"></a>További lépések

Ebből a rövid útmutatóból megtudhatta, hogyan használhatja a Face Library-t az alábbi feladatok elvégzéséhez:. Ezután tekintse át a dokumentációt, és ismerkedjen meg a könyvtárral.

> [!div class="nextstepaction"]
> [Face API referenciája (go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [Mi az a Face szolgáltatás?](../../overview.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go)található.
