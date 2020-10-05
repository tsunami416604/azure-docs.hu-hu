---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: 3c176f103371bfb1b35231f222b2045f1f4a3ef9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91327046"
---
## <a name="download-and-install"></a>Letöltés és telepítés

#### <a name="windows-install"></a>[Windows-telepítés](#tab/windowsinstall)

Kövesse az alábbi lépéseket a Speech CLI Windows rendszeren történő telepítéséhez:

1. Töltse le a Speech CLI [zip-archívumát](https://aka.ms/speech/spx-zips.zip), majd bontsa ki.
2. Nyissa meg a `spx-zips` letöltésből kinyert gyökérkönyvtárat, és bontsa ki a szükséges alkönyvtárat ( `spx-net471` a .NET-keretrendszer 4,7-es verziójához, vagy a `spx-netcore-win-x64` .net Core 3,0 x64-es processzoron).

A parancssorban módosítsa a könyvtárat erre a helyre, majd írja be a parancsot a `spx` beszédfelismerési parancssori felület súgójának megjelenítéséhez.

> [!NOTE]
> Windows rendszeren a Speech CLI csak a parancssorban elérhető betűkészleteket jeleníti meg a helyi számítógépen.
> A [Windows Terminal](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) a Speech CLI által interaktív módon létrehozott összes betűtípust támogatja.
> Ha fájlba, például egy Jegyzettömbbe vagy egy webböngészőbe (például a Microsoft Edge) küldi el a kimenetet, az összes betűtípust is megjelenítheti.

> [!NOTE]
> A PowerShell nem vizsgálja a helyi könyvtárat a parancs keresésekor. A PowerShellben váltson a könyvtár helyére, `spx` és hívja meg az eszközt a következő beírásával: `.\spx` .
> Ha hozzáadja ezt a könyvtárat az elérési úthoz, a PowerShell és a Windows-parancssor `spx` bármely könyvtárból megkereshető, az `.\` előtagot nem beleértve.

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

#### <a name="docker-install"></a>[Docker-telepítés](#tab/dockerinstall)

> [!NOTE]
> <a href="https://www.docker.com/get-started" target="_blank">Docker Desktop a platformhoz <span class="docon docon-navigate-external x-hidden-focus"></span> </a> telepítve kell lennie.

A következő lépésekkel telepítheti a Speech CLI-t egy Docker-tárolóba:

1. Írja be a következő parancsot egy új parancssorba vagy terminálba:  `docker pull msftspeech/spx`
2. Írja be ezt a parancsot. A Speech CLI-vel kapcsolatos súgóban talál: `docker run -it --rm msftspeech/spx help`

### <a name="mount-a-directory-in-the-container"></a>Könyvtár csatlakoztatása a tárolóhoz

A Speech CLI-eszköz fájlokként menti a konfigurációs beállításokat, és minden parancs végrehajtásakor betölti ezeket a fájlokat (kivéve a Súgó parancsokat).
Ha a Speech CLI-t egy Docker-tárolón belül használja, csatlakoztatnia kell egy helyi könyvtárat a tárolóból, így az eszköz képes tárolni vagy megkeresni a konfigurációs beállításokat, és így az eszköz képes olvasni vagy írni a parancshoz szükséges fájlokat, például hangfájlokat a beszédhez.

Windows rendszeren írja be ezt a parancsot egy helyi címtár-beszédfelismerési parancssori felület létrehozásához a tárolón belül:

`mkdir c:\spx-data`

Vagy Linux vagy Mac rendszeren írja be ezt a parancsot egy terminálba egy könyvtár létrehozásához, és tekintse meg az abszolút elérési útját:

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

`docker run -it -v c:\spx-data:/data --rm msftspeech/spx`

Linux vagy Mac rendszeren a parancsok a következőhöz hasonlóak lesznek:

`sudo docker run -it -v /ABSOLUTE_PATH:/data --rm msftspeech/spx`

> [!NOTE]
> Cserélje le a `/ABSOLUTE_PATH` parancsot a fenti szakaszban szereplő parancs által megjelenített abszolút elérési útra `pwd` .

A `spx` tárolóban telepített parancs használatához mindig adja meg a fent látható teljes parancsot, majd a kérés paramétereit.
Windows rendszeren például a következő parancs állítja be a kulcsot:

`docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY`

> [!NOTE]
> A számítógép mikrofonját vagy hangszóróját nem használhatja, ha a Speech CLI-t egy Docker-tárolón belül futtatja.
> Ha ezeket az eszközöket szeretné használni, adja át a hangfájlokat a és a Speech CLI-ből a Docker-tárolón kívüli rögzítéshez/lejátszáshoz.
> A Speech CLI-eszköz elérheti a fenti lépésekben beállított helyi könyvtárat.

***

## <a name="create-subscription-config"></a>Előfizetés konfigurációjának létrehozása

A beszédfelismerési parancssori felület használatának megkezdéséhez először meg kell adnia a beszédfelismerési előfizetési kulcs és a régió adatait. A régió azonosítójának megkereséséhez tekintse meg a [régiók támogatása](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) lapot. Miután megtörtént az előfizetési kulcs és a régió azonosítója (pl. `eastus`, `westus` ) futtassa a következő parancsokat.

```shell
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

Az előfizetés-hitelesítés mostantól a jövőbeli SPX-kérelmekhez van tárolva. Ha el kell távolítania a tárolt értékek valamelyikét, futtassa a vagy a parancsot `spx config @region --clear` `spx config @key --clear` .
