---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: 87af99d5136a8bed93854e9396895cddeb22549a
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540474"
---
## <a name="download-and-install"></a>Letöltés és telepítés

#### <a name="windows-install"></a>[Windows-telepítés](#tab/windowsinstall)

Kövesse az alábbi lépéseket a Speech CLI Windows rendszeren történő telepítéséhez:

1. Windows rendszeren a platformhoz a [Microsoft Visual C++ terjeszthető változata szükséges a Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) -hez. Ha először telepíti az elsőt, újraindításra lehet szükség.
2. Töltse le a Speech CLI [zip-archívumát](https://aka.ms/speech/spx-zips.zip), majd bontsa ki.
3. Nyissa meg azt a könyvtárat, ahová kicsomagolta `spx-zips` . Ez a mappa számos platformon tartalmaz programfájlokat a Speech CLI-hez. 
4. Bontsa ki a platform ( `spx-net471` a .NET-keretrendszer 4,7-es vagy a .net Core 3,0-es verziójához készült) fájljait `spx-netcore-win-x64` egy x64-es processzoron. Ne feledje, hogy ezt a könyvtárat fogja futtatni `spx` .

### <a name="run-the-speech-cli"></a>A Speech CLI futtatása

1. Nyissa meg a parancssort vagy a PowerShellt, majd keresse meg azt a könyvtárat, ahová kibontotta a Speech CLI-t.  
2. A `spx` SPEECH parancssori felület Súgó parancsainak megtekintéséhez írja be a következőt:.

> [!NOTE]
> A PowerShell nem vizsgálja a helyi könyvtárat a parancs keresésekor. A PowerShellben váltson a könyvtár helyére, `spx` és hívja meg az eszközt a következő beírásával: `.\spx` .
> Ha hozzáadja ezt a könyvtárat az elérési úthoz, a PowerShell és a Windows-parancssor `spx` bármely könyvtárból megkereshető, az `.\` előtagot nem beleértve.

### <a name="font-limitations"></a>Betűkészletre vonatkozó korlátozások

Windows rendszeren a Speech CLI csak a parancssorban elérhető betűkészleteket jeleníti meg a helyi számítógépen.
A [Windows Terminal](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) a Speech CLI által interaktív módon létrehozott összes betűtípust támogatja.

Ha fájlba, például egy Jegyzettömbbe vagy egy webböngészőbe (például a Microsoft Edge) küldi el a kimenetet, az összes betűtípust is megjelenítheti.

#### <a name="linux-install"></a>[Linux-telepítés](#tab/linuxinstall)

A következő lépésekkel telepítheti a Speech CLI-t Linux rendszeren egy x64-es PROCESSZORon:

1. Telepítse a [.net Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0)-ot.
2. Töltse le a Speech CLI [zip-archívumát](https://aka.ms/speech/spx-zips.zip), majd bontsa ki.
3. Nyissa meg a `spx-zips` letöltésből kibontott gyökérkönyvtárat, és bontsa `spx-netcore-30-linux-x64` ki az új `~/spx` könyvtárat.
4. A terminálban írja be a következő parancsokat:
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

`spx`A SPEECH parancssori felület súgójának megjelenítéséhez írja be a következőt:.

#### <a name="docker-install-windows-linux-macos"></a>[Docker-telepítés (Windows, Linux, macOS)](#tab/dockerinstall)

A következő lépésekkel telepítheti a Speech CLI-t egy Docker-tárolóba:

1. A <a href="https://www.docker.com/get-started" target="_blank">Docker Desktop <span class="docon docon-navigate-external x-hidden-focus"></span> telepítése</a> a platformon, ha még nincs telepítve.
2. Írja be a következő parancsot egy új parancssorba vagy terminálba:
   ```console   
   docker pull msftspeech/spx
   ```
3. Írja be ezt a parancsot. A Speech CLI-vel kapcsolatos súgóban talál:
   ```console 
   docker run -it --rm msftspeech/spx help
   ```

### <a name="mount-a-directory-in-the-container"></a>Könyvtár csatlakoztatása a tárolóhoz

A Speech CLI-eszköz fájlokként menti a konfigurációs beállításokat, és minden parancs végrehajtásakor betölti ezeket a fájlokat (kivéve a Súgó parancsokat).
Ha a Speech CLI-t egy Docker-tárolón belül használja, csatlakoztatnia kell egy helyi könyvtárat a tárolóból, így az eszköz képes tárolni vagy megkeresni a konfigurációs beállításokat, és így az eszköz képes olvasni vagy írni a parancshoz szükséges fájlokat, például hangfájlokat a beszédhez.

Windows rendszeren írja be ezt a parancsot egy helyi címtár-beszédfelismerési parancssori felület létrehozásához a tárolón belül:

`mkdir c:\spx-data`

Vagy Linux vagy macOS rendszeren írja be ezt a parancsot egy terminálba egy könyvtár létrehozásához, és tekintse meg az abszolút elérési útját:

```bash
mkdir ~/spx-data
cd ~/spx-data
pwd
```

Az abszolút elérési utat fogja használni a Speech CLI hívásakor.

### <a name="run-speech-cli-in-the-container"></a>A Speech CLI futtatása a tárolóban

Ez a dokumentáció a `spx` nem Docker-telepítésekben használt SPEECH CLI-parancsot mutatja be.
Amikor a `spx` parancsot egy Docker-tárolóban hívja meg, csatlakoztatnia kell egy könyvtárat a tárolóban a fájlrendszerhez, ahol a SPEECH CLI képes tárolni és megtalálni a konfigurációs értékeket, valamint a fájlok olvasását és írását.

Windows rendszeren a parancsok a következőképpen fognak kezdődni:

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx
```

Linux vagy macOS rendszeren a parancsok az alábbi példához hasonlóan jelennek meg. Cserélje le a `ABSOLUTE_PATH` értéket a csatlakoztatott könyvtár abszolút elérési útjára. Ezt az elérési utat az `pwd` előző szakaszban szereplő parancs adta vissza. 

Ha a kulcs és a régió beállítása előtt futtatja ezt a parancsot, hibaüzenet jelenik meg a kulcs és a régió beállításakor:
```console   
sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
```

A `spx` tárolóban telepített parancs használatához mindig adja meg a fent látható teljes parancsot, majd a kérés paramétereit.
Windows rendszeren például a következő parancs állítja be a kulcsot:

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY
```

A parancssori eszközzel való nagyobb körű interakcióhoz egy BelépésiPont paraméter hozzáadásával elindíthat egy tárolót interaktív bash-rendszerhéj használatával.
Windows rendszeren írja be ezt a parancsot egy olyan tároló elindításához, amely egy interaktív parancssori felületet tesz elérhetővé, ahol több parancsot is megadhat `spx` :
```console
docker run -it --entrypoint=/bin/bash -v c:\spx-data:/data --rm msftspeech/spx
```

> [!WARNING]
> A számítógép mikrofonját nem használhatja, ha a Speech CLI-t a Docker-tárolón belül futtatja. A helyi csatlakoztatott címtárban azonban elolvashatja és mentheti a hangfájlokat. 

<!-- Need to troubleshoot issues with docker pull image

### Optional: Create a command line shortcut

If you're running the the Speech CLI from a Docker container on Linux or macOS you can create a shortcut. 

Follow these instructions to create a shortcut:
1. Open `.bash_profile` with your favorite text editor. For example:
   ```shell
   nano ~/.bash_profile
   ```
2. Next, add this function to your `.bash_profile`. Make sure you update this function with the correct path to your mounted directory:
   ```shell   
   spx(){
       sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
   }
   ```
3. Source your profile:
   ```shell
   source ~/.bash_profile
   ```
4. Now instead of running `sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx`, you can just type `spx` followed by arguments. For example: 
   ```shell
   // Get some help
   spx help recognize

   // Recognize speech from an audio file 
   spx recognize --file /mounted/directory/file.wav
   ```

> [!WARNING]
> If you change the mounted directory that Docker is referencing, you need to update the function in `.bash_profile`.
--->
***

## <a name="create-subscription-config"></a>Előfizetés konfigurációjának létrehozása

A beszédfelismerési parancssori felület használatának megkezdéséhez meg kell adnia a beszédfelismerési előfizetési kulcsot és a régió azonosítóját. Szerezze be ezeket a hitelesítő adatokat a [beszédfelismerési szolgáltatás ingyenes kipróbálásához](../overview.md#try-the-speech-service-for-free)szükséges lépések követésével.
Miután megtörtént az előfizetési kulcs és a régió azonosítója (pl. `eastus`, `westus` ) futtassa a következő parancsokat.

```console
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

Az előfizetés-hitelesítés mostantól a jövőbeli SPX-kérelmekhez van tárolva. Ha el kell távolítania a tárolt értékek valamelyikét, futtassa a vagy a parancsot `spx config @region --clear` `spx config @key --clear` .
