---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: abfb4f6ba9452581811db1f462089cbafc771266
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544209"
---
## <a name="prerequisites"></a>Előfeltételek

Az egyetlen előfeltétel egy Azure Speech-előfizetés. Ha még nem rendelkezik ilyennel, tekintse meg az új előfizetés létrehozásával kapcsolatos [útmutatót](../get-started.md#new-resource) .

## <a name="download-and-install"></a>Letöltés és telepítés

#### <a name="windows-install"></a>[Windows-telepítés](#tab/windowsinstall)

Kövesse az alábbi lépéseket a Speech CLI Windows rendszeren történő telepítéséhez:

1. A [.NET-keretrendszer 4,7](https://dotnet.microsoft.com/download/dotnet-framework/net471) vagy a [.net Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0) telepítése
2. Töltse le a Speech CLI [zip-archívumát](https://aka.ms/speech/spx-zips.zip), majd bontsa ki.
3. Nyissa meg a `spx-zips` letöltésből kinyert gyökérkönyvtárat, és bontsa ki a szükséges alkönyvtárat ( `spx-net471` a .NET-keretrendszer 4,7-es verziójához, vagy a `spx-netcore-win-x64` .net Core 3,0 x64-es processzoron).

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

***

## <a name="create-subscription-config"></a>Előfizetés konfigurációjának létrehozása

A beszédfelismerési parancssori felület használatának megkezdéséhez először meg kell adnia a beszédfelismerési előfizetési kulcs és a régió adatait. A régió azonosítójának megkereséséhez tekintse meg a [régiók támogatása](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) lapot. Miután megtörtént az előfizetési kulcs és a régió azonosítója (pl. `eastus`, `westus` ) futtassa a következő parancsokat.

```shell
spx config @key --set YOUR-SUBSCRIPTION-KEY
spx config @region --set YOUR-REGION-ID
```

Az előfizetés-hitelesítés mostantól a jövőbeli SPX-kérelmekhez van tárolva. Ha el kell távolítania a tárolt értékek valamelyikét, futtassa a vagy a parancsot `spx config @region --clear` `spx config @key --clear` .
