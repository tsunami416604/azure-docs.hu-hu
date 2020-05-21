---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: 6a38590d2511afaac6bd9213f53a8c69d0264eb4
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714672"
---
## <a name="prerequisites"></a>Előfeltételek

Az egyetlen előfeltétel egy Azure Speech-előfizetés. Ha még nem rendelkezik ilyennel, tekintse meg az új előfizetés létrehozásával kapcsolatos [útmutatót](../get-started.md#new-resource) .

## <a name="download-and-install"></a>Letöltés és telepítés

#### <a name="windows-install"></a>[Windows-telepítés](#tab/windowsinstall)

A következő lépésekkel telepítheti az SPX eszközt Windows rendszeren:

1. A [.NET-keretrendszer 4,7](https://dotnet.microsoft.com/download/dotnet-framework/net471) vagy a [.net Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0) telepítése
2. Töltse le az SPX-eszköz [zip-archívumát](https://aka.ms/speech/spx-zips.zip), majd bontsa ki.
3. Nyissa meg a `spx-zips` letöltésből kinyert gyökérkönyvtárat, és bontsa ki a szükséges alkönyvtárat ( `spx-net471` a .NET-keretrendszer 4,7-es verziójához, vagy a `spx-netcore-win-x64` .net Core 3,0 x64-es processzoron).

A parancssorban módosítsa a könyvtárat erre a helyre, majd írja be a parancsot az `spx` SPX eszköz súgójának megjelenítéséhez.

#### <a name="linux-install"></a>[Linux-telepítés](#tab/linuxinstall)

Az alábbi lépéseket követve telepítheti az SPX-eszközt a Linux rendszeren egy x64-es PROCESSZORon:

1. Telepítse a [.net Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0)-ot.
2. Töltse le az SPX-eszköz [zip-archívumát](https://aka.ms/speech/spx-zips.zip), majd bontsa ki.
3. Nyissa meg a `spx-zips` letöltésből kibontott gyökérkönyvtárat, és bontsa `spx-netcore-30-linux-x64` ki az új `~/spx` könyvtárat.
4. A terminálban írja be a következő parancsokat:
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

`spx`Az SPX eszköz súgójának megjelenítéséhez írja be a következőt:.

***

## <a name="create-subscription-config"></a>Előfizetés konfigurációjának létrehozása

Az SPX használatának megkezdéséhez először meg kell adnia a beszédfelismerési előfizetési kulcs és a régió információit. A régió azonosítójának megkereséséhez tekintse meg a [régiók támogatása](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) lapot. Miután megtörtént az előfizetési kulcs és a régió azonosítója (pl. `eastus`, `westus` ) futtassa a következő parancsokat.

```shell
spx config @key --set YOUR-SUBSCRIPTION-KEY
spx config @region --set YOUR-REGION-ID
```

Az előfizetés-hitelesítés mostantól a jövőbeli SPX-kérelmekhez van tárolva. Ha el kell távolítania a tárolt értékek valamelyikét, futtassa a vagy a parancsot `spx config @region --clear` `spx config @key --clear` .
