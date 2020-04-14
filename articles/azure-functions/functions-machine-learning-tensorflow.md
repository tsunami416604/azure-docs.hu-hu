---
title: A Python és a TensorFlow használata gépi tanuláshoz az Azure-ban
description: A Python, a TensorFlow és az Azure Functions egy gépi tanulási modellel egy kép besorolásához annak tartalma alapján.
author: anthonychu
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: antchu
ms.custom: mvc
ms.openlocfilehash: 9d25e2e32f09cc681d85d5adffe53f1237d7200c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255498"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>Oktatóanyag: Gépi tanulási modellek alkalmazása az Azure Functionsben a Python és a TensorFlow használatával

Ebben a cikkben megtudhatja, hogyan használhatja a Python, TensorFlow és az Azure Functions egy gépi tanulási modell egy kép besorolása annak tartalma alapján. Mivel minden munkát helyileg, és nem hoz létre Azure-erőforrásokat a felhőben, nincs költség az oktatóanyag befejezéséhez.

> [!div class="checklist"]
> * Inicializáljon egy helyi környezetet az Azure Functions pythonban való fejlesztéséhez.
> * Egyéni TensorFlow gépi tanulási modell importálása egy függvényalkalmazásba.
> * Kiszolgáló nélküli HTTP API-t hozhat létre egy kép kutya vagy macska ként való besorolásához.
> * Használja fel az API-t egy webalkalmazásból.

## <a name="prerequisites"></a>Előfeltételek 

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 3.7.4](https://www.python.org/downloads/release/python-374/). (A Python 3.7.4 és a Python 3.6.x ellenőrzése az Azure Functions; A Python 3.8-as és újabb verziói még nem támogatottak.)
- Az [Azure Functions alapvető eszközei](functions-run-local.md#install-the-azure-functions-core-tools)
- Kódszerkesztő, például [Visual Studio-kód](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>Előfeltétel-ellenőrzés

1. Egy terminál- vagy parancsablakban futtassa `func --version` annak ellenőrzéséhez, hogy az Azure Functions Core Tools 2.7.1846-os vagy újabb verziójú-e.
1. Futtassa `python --version` (Linux/MacOS) vagy `py --version` (Windows) a Python 3.7.x verziójelentések ellenőrzéséhez.

## <a name="clone-the-tutorial-repository"></a>Klónozza a bemutató tárházat

1. Terminál- vagy parancsablakban klónozza a következő tárházat a Git használatával:

    ```
    git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
    ```

1. Navigáljon a mappába, és vizsgálja meg annak tartalmát.

    ```
    cd functions-python-tensorflow-tutorial
    ```

    - *start* a munka mappát a tutorial.
    - *vége* a végső eredmény és a teljes végrehajtás az Ön referencia.
    - *erőforrások* tartalmazzák a gépi tanulási modell és segítő könyvtárak.
    - *előtétoldal* egy olyan webhely, amely meghívja a függvényalkalmazást.
    
## <a name="create-and-activate-a-python-virtual-environment"></a>Python virtuális környezet létrehozása és aktiválása

Nyissa meg a *start* mappát, és futtassa `.venv`a következő parancsokat a nevű virtuális környezet létrehozásához és aktiválásához. Ügyeljen arra, hogy a Python 3.7, amely az Azure Functions által támogatott.


# <a name="bash"></a>[Bash](#tab/bash)

```bash
cd start
```

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Ha a Python nem telepítette a venv csomagot a Linux disztribúcióra, futtassa a következő parancsot:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
cd start
```

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
```

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Az összes további parancsot ebben az aktivált virtuális környezetben futtatja. (A virtuális környezetből `deactivate`való kilépéshez futtassa a .)


## <a name="create-a-local-functions-project"></a>Helyi függvényprojekt létrehozása

Az Azure Functions egy függvényprojekt egy tároló egy vagy több egyedi függvények, amelyek mindegyike reagál egy adott eseményindító. A projekt összes függvénye azonos helyi és üzemeltetési konfigurációval rendelkezik. Ebben a szakaszban hozzon létre egy függvényprojektet, amely egyetlen sablonfüggvényt tartalmaz, `classify` amely HTTP-végpontot biztosít. Egy későbbi szakaszban további kódot adhat hozzá.

1. A *kezdőképernyő* mappában az Azure Functions Core Tools használatával inicializáljon egy Python függvényalkalmazást:

    ```
    func init --worker-runtime python
    ```

    Az inicializálás után a *kezdőmappa* különböző fájlokat tartalmaz a projekthez, beleértve a [local.settings.json](functions-run-local.md#local-settings-file) és [host.json](functions-host-json.md)nevű konfigurációs fájlokat. Mivel *a local.settings.json* tartalmazhat az Azure-ból letöltött titkokat, a fájl alapértelmezés szerint ki van zárva a forrásvezérlésből a *.gitignore* fájlban.

    > [!TIP]
    > Mivel egy függvényprojekt egy adott futásidőhöz van kötve, a projekt összes függvényét ugyanazzal a nyelvvel kell írni.

1. Adjon hozzá egy függvényt a projekthez `--name` a következő paranccsal, ahol `--template` az argumentum a függvény egyedi neve, és az argumentum megadja a függvény eseményindítóját. `func new`hozzon létre egy olyan almappát, amely megfelel a projekt választott nyelvének megfelelő kódfájlt tartalmazó függvénynévnek, valamint egy *function.json*nevű konfigurációs fájlt.

    ```
    func new --name classify --template "HTTP trigger"
    ```

    Ez a parancs létrehoz egy mappát, amely megfelel a függvény nevének, *osztályozza*a . Ebben a mappában két fájl található: * \_ \_\_\_init .py*, amely a függvénykódot tartalmazza, és a *function.json*, amely leírja a függvény eseményindítóját, valamint bemeneti és kimeneti kötéseit. A fájlok tartalmáról a [Fájl tartalmának vizsgálata a](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#optional-examine-the-file-contents) Python rövid útmutatójában című témakörben talál.


## <a name="run-the-function-locally"></a>Függvény helyi futtatása

1. Indítsa el a funkciót a helyi Azure Functions futásidejű állomás elindításával a *start* mappában:

    ```
    func start
    ```
    
1. Ha megjelenik `classify` a végpont a kimenetben, keresse ```http://localhost:7071/api/classify?name=Azure```meg az URL-címet, . A "Hello Azure!" üzenet meg kell jelennie a kimenetben.

1. A **Ctrl C billentyűvel**-**C** állítsa le az állomást.


## <a name="import-the-tensorflow-model-and-add-helper-code"></a>A TensorFlow-modell importálása és segítőkód hozzáadása

Ha módosítani `classify` szeretné a függvényt egy lemezkép tartalmának besorolásához, egy előre elkészített TensorFlow-modellt kell használnia, amely be van tanítva az Azure Custom Vision Service szolgáltatással, és exportált. A korábban klónozott minta *erőforrásmappájában* található modell a ttól függően osztályozza a képet, hogy kutya vagy macska van-e benne. Ezután hozzáad néhány segítő kódot és függőséget a projekthez.

Ha saját modellt szeretne készíteni a Custom Vision Service ingyenes szintjével, kövesse a [mintaprojekt tárházában](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md)található utasításokat.

> [!TIP]
> Ha a TensorFlow-modellt a függvényalkalmazástól függetlenül szeretné üzemeltetni, ehelyett csatlakoztathatja a modellt tartalmazó fájlmegosztást a Linux függvényalkalmazáshoz. További információ: [Fájlmegosztás csatlakoztatása Python függvényalkalmazásba az Azure CLI használatával.](./scripts/functions-cli-mount-files-storage-linux.md)

1. A *kezdőmappában* futtassa a következő parancsot a modellfájlok *classify* mappába másolásához. Ügyeljen arra, `\*` hogy a parancs tartalmazza. 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    cp ../resources/model/* classify
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    copy ..\resources\model\* classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\model\* classify
    ```
    
    ---
    
1. Ellenőrizze, hogy a *besorolási* mappa tartalmaz-e *model.pb* és *labels.txt*nevű fájlokat. Ha nem, ellenőrizze, hogy futtatta-e a parancsot a *start* mappában.

1. A *kezdőképernyő* mappában futtassa a következő parancsot a segítőkóddal rendelkező fájl classify mappába *másolásához:*

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    cp ../resources/predict.py classify
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    copy ..\resources\predict.py classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\predict.py classify
    ```
    
    ---

1. Ellenőrizze, hogy a *besorolási* mappa tartalmaz-e *predict.py*nevű fájlt.

1. Nyissa *meg a start/requirements.txt fájlt* egy szövegszerkesztőben, és adja hozzá a segítő kód által igényelt alábbi függőségeket:

    ```txt
    tensorflow==1.14
    Pillow
    requests
    ```
    
1. A *requirements.txt*fájl mentése .

1. Telepítse a függőségeket a következő parancs futtatásával a *start* mappában. A telepítés eltarthat néhány percig, amely idő alatt folytathatja a funkció módosítását a következő szakaszban.

    ```
    pip install --no-cache-dir -r requirements.txt
    ```
    
    Windows rendszerben a következő hibaüzenet jelenhet meg: "Nem lehetett csomagokat telepíteni egy EnvironmentError miatt: [Errno 2] Nincs ilyen fájl vagy könyvtár:" majd egy hosszú elérési út egy olyan fájlhoz, mint *a sharded_mutable_dense_hashtable.cpython-37.pyc*. Ez a hiba általában azért fordul elő, mert a mappaelérési út mélysége túl hosszú lesz. Ebben az esetben állítsa `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` be `1` a beállításkulcsot a hosszú elérési utak engedélyezéséhez. Másik lehetőségként ellenőrizze, hogy hol van telepítve a Python-értelmező. Ha a hely elérési útja hosszú, próbálja meg újratelepíteni egy rövidebb elérési úttal rendelkező mappába.

> [!TIP]
> Amikor *predict.py* az első előrejelzést kéri, `_initialize` egy nevű függvény betölti a TensorFlow modellt a lemezről, és globális változókban gyorsítótárazza. Ez a gyorsítótárazás felgyorsítja a későbbi előrejelzéseket. A globális változók használatával kapcsolatos további információkért tekintse meg az [Azure Functions Python fejlesztői útmutatóját.](functions-reference-python.md#global-variables)

## <a name="update-the-function-to-run-predictions"></a>A függvény frissítése előrejelzések futtatásához

1. Nyissa *meg\_\_a\_\_classify/ init .py* fájlt egy `import` szövegszerkesztőben, és adja hozzá a következő sorokat a meglévő utasítások után a szabványos JSON-könyvtár és a *gyorssegédek importálásához:*

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. Cserélje le a `main` függvény teljes tartalmát a következő kódra:

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="8-19":::

    Ez a függvény egy kép URL-címét `img`kapja egy lekérdezési karakterlánc nevű paraméterben. Ezután `predict_image_from_url` felhívja a segítő könyvtár ból a rendszerkép letöltése és besorolása a TensorFlow modell használatával. A függvény ezután http-választ ad vissza az eredményekkel. 

    > [!IMPORTANT]
    > Mivel ezt a HTTP-végpontot egy másik tartományban üzemeltetett `Access-Control-Allow-Origin` weblap nevezi meg, a válasz tartalmaz egy fejlécet, amely megfelel a böngésző eredetközi erőforrás-megosztási (CORS) követelményeinek.
    >
    > Éles alkalmazásban a `*` nagyobb biztonság érdekében módosítsa a weboldal konkrét eredetét.

1. Mentse a módosításokat, majd feltételezve, hogy a függőségek `func start`telepítése befejeződött, indítsa újra a helyi függvényállomást a segítségével. Ügyeljen arra, hogy futtassa a fogadó a *start* mappában a virtuális környezet aktiválva. Ellenkező esetben az állomás elindul, de a függvény meghívásakor hibák jelennek meg.

    ```
    func start
    ```

1. A böngészőben nyissa meg a következő URL-t a macskakép URL-címével rendelkező függvény meghívásához, és győződjön meg arról, hogy a visszaadott JSON a képet macskaként osztályozza.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
    ```
    
1. Tartsa futtassa az állomást, mert a következő lépésben használja. 

### <a name="run-the-local-web-app-front-end-to-test-the-function"></a>A helyi webalkalmazás előtérének futtatása a funkció teszteléséhez

A függvényvégpont egy másik webalkalmazásból történő meghívásának teszteléséhez van egy egyszerű alkalmazás a tárház *előtérmappájában.*

1. Nyisson meg egy új terminált vagy parancssort, és aktiválja a virtuális környezetet (amint azt korábban a [Python virtuális környezet létrehozása és aktiválása című csoportban](#create-and-activate-a-python-virtual-environment)leírtuk).

1. Keresse meg a tárház *előtér-mappáját.*

1. HTTP-kiszolgáló indítása pythonnal:

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash 
    python -m http.server
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    py -m http.server
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -m http.server
    ```

1. A böngészőben keresse `localhost:8000`meg a taurát, majd írja be az alábbi fényképURL-ek egyikét a szövegmezőbe, vagy használja a nyilvánosan elérhető képek URL-címét.

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`
    
1. Válassza **a Küldés** lehetőséget a függvényvégpont meghívásához a lemezkép besorolásához.

    ![A kész projekt képernyőképe](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

    Ha a böngésző hibát jelez a kép URL-címének elküldésekor, ellenőrizze azt a terminált, amelyben a függvényalkalmazást futtatja. Ha olyan hibaüzenetet lát, mint a "Nincs modul található "PIL", akkor előfordulhat, hogy a *start* mappában indította el a függvényalkalmazást anélkül, hogy először aktiválta volna a korábban létrehozott virtuális környezetet. Ha továbbra is hibákat lát, futtassa `pip install -r requirements.txt` újra a virtuális környezet aktiválásával, és keresse meg a hibákat.

> [!NOTE]
> A minta mindig osztályozza a tartalmát a kép, mint egy macska vagy egy kutya, függetlenül attól, hogy a kép tartalmaz vagy, alapértelmezett kutya. A tigrisek és párducok képei például általában macskaként vannak besorolva, de az elefántok, sárgarépa vagy repülőgépek képei kutyaként vannak besorolva.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Mivel az oktatóanyag teljes egészében helyileg fut a gépen, nincsenek Azure-erőforrások vagy szolgáltatások karbantartása.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre és szabhat testre egy HTTP API-végpontot az Azure Functions segítségével a images egy TensorFlow-modell használatával. Azt is megtanulta, hogyan hívhatja meg az API-t egy webalkalmazásból. Az oktatóanyagban szereplő technikák segítségével bármilyen összetettségű API-kat hozhat létre, miközben az Azure Functions által biztosított kiszolgáló nélküli számítási modellen fut.

> [!div class="nextstepaction"]
> [A függvény üzembe helyezése az Azure Functions szolgáltatásba az Azure CLI-útmutató használatával](./functions-run-local.md#publish)

Lásd még:

- [Telepítse a függvényt az Azure-ba a Visual Studio Code használatával.](https://code.visualstudio.com/docs/python/tutorial-azure-functions)
- [Az Azure Functions Python fejlesztői útmutatója](./functions-reference-python.md)
- [Fájlmegosztás csatlakoztatása Python-függvényalkalmazásba az Azure CLI használatával](./scripts/functions-cli-mount-files-storage-linux.md)
