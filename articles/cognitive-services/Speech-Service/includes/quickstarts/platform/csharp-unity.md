---
title: 'Rövid útmutató: Beszéd SDK c# Unity platform beállításához - Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ezzel az útmutatóval beállíthatja a c# egység platformját az SDK beszédszolgáltatással.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: 8d9e05b5d8105cce8355d217cc6922c25bbe1730
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75467484"
---
Ez az útmutató bemutatja, hogyan kell telepíteni a [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) for [Unity](https://unity3d.com/).

> [!NOTE]
> A Speech SDK for Unity támogatja a Windows Desktop (x86 és x64) vagy az Univerzális Windows platformot (x86, x64, ARM/ARM64), az Android (x86, ARM32/64) és az iOS (x64 szimulátor, ARM32 és ARM64)

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

- [Unity 2018.3 vagy újabb,](https://store.unity.com/) [a Unity 2019.1 támogatásával az UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)számára .
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). A Visual Studio 2017 15.9-es vagy újabb verziója is elfogadható.
- A Windows ARM64 támogatásához telepítse az [ARM64 opcionális buildeszközeit és a Windows 10 ARM64-hez való Windows 10 SDK-t.](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/)

## <a name="install-the-speech-sdk"></a>A beszédfelismerési SDK telepítése

A Speech SDK for Unity telepítéséhez hajtsa végre az alábbi lépéseket:

1. Töltse le és nyissa meg a [Speech SDK for Unity](https://aka.ms/csspeech/unitypackage)-t , amely Unity eszközcsomagként (.unitypackage) van csomagolva, és már társítható a Unity-hez. Az eszközcsomag megnyitásakor megjelenik az **Egységimportálási csomag** párbeszédpanel. Előfordulhat, hogy egy üres projektet kell létrehoznia és megnyitnia ahhoz, hogy ez a lépés működjön.

   [![Egységcsomag importálása párbeszédpanel az Egységszerkesztőben](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png#lightbox)

1. Győződjön meg arról, hogy az összes fájl ki van jelölve, és válassza **az Importálás**lehetőséget. Néhány pillanat múlva a Unity eszközcsomag importálása a projektbe.

Az eszközcsomagok Unity beimportálásáról további információt a [Unity dokumentációban](https://docs.unity3d.com/Manual/AssetPackages.html)talál.

Most továbbléphet az alábbi [következő lépésekre.](#next-steps)

## <a name="next-steps"></a>További lépések

[!INCLUDE [windows](../quickstart-list.md)]
