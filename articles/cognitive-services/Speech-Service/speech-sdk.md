---
title: Tudnivalók a kognitív szolgáltatások beszéd SDK |} Microsoft Docs
description: Az SDK-k érhető el a beszédfelismerés szolgáltatás áttekintése.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: b9b7b8af5ce3d75788fd2c4f5e0309b5ca561a8f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349715"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>Tudnivalók a kognitív szolgáltatások beszéd SDK

A kognitív szolgáltatások beszéd Software Development Kit (SDK) az alkalmazások natív hozzáférést biztosít a funkciók a beszédfelismerés szolgáltatás, így könnyebben fejlesztésére. Jelenleg az SDK hozzáférést biztosít **szöveg beszédfelismerés**, **beszéd fordítási**, és **leképezés felismerés**.

A tábla a jelenleg támogatott programozási nyelveket és operációs rendszereket sorolja fel.

|Támogatott operációs rendszer|Programozási nyelv|
|-|-|
|Windows|C/C++, C#|
|Linux|C/C++|
|Eszközök|Java\*|

\* *A Java SDK része a [beszéd eszközök SDK](speech-devices-sdk.md).*

## <a name="get-the-windows-sdk"></a>A Windows SDK

A Windows verzió a beszédfelismerés SDK magában foglalja a 32 bites és 64 bites C/C++ klienskódtárak segítségével, valamint a felügyelt (.NET) szalagtárak C# való használatra. A Visual Studio NuGet; használatával telepítheti az SDK-val Ehhez csak keressen a `Microsoft.CognitiveServices.Speech`.

## <a name="get-the-linux-sdk"></a>A Linux SDK beszerzése

Győződjön meg arról, hogy az a fordító szükséges és a könyvtárak a következő rendszerhéj-parancsok futtatásával:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

> [!NOTE]
> Ezek az utasítások azt feltételezik, Ubuntu 16.04 futtat a számítógépen (x86 vagy x64). Egy másik Ubuntu verziót, vagy egy másik Linux terjesztési igazítja a környezet az alábbi lépéseket.

Ezt követően [az SDK letöltése](https://aka.ms/csspeech/linuxbinary) és csomagolja ki a fájlokat a könyvtárba, amely az Ön által választott. Az alábbi táblázatban az SDK mappastruktúrájából.

|Útvonal|Leírás|
|-|-|
|`license.md`|Licenc|
|`third-party-notices.md`|Harmadik fél közleményei|
|`include`|Fejlécfájlok C és C++|
|`lib/x64`|Natív x64 kapcsolódik-e az alkalmazás könyvtára|
|`lib/x86`|Natív x86 kapcsolódik-e az alkalmazás könyvtára|

Alkalmazás létrehozása, másolja a szükséges bináris fájlokat (és könyvtárak) áthelyezi a fejlesztési környezetet, illetve tünteti fel azokat a felépítési folyamat szükség szerint.

## <a name="get-the-java-sdk"></a>Beolvasása a Java SDK

A Java SDK része a [beszéd eszközök SDK](speech-devices-sdk.md).

## <a name="next-steps"></a>További lépések

* [Beszéd próbaverziós előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [Lásd: how to a C# beszéd felismerésére](quickstart-csharp-windows.md)
