---
title: Válassza ki az előtér-fejlesztési platformot az ügyfélalkalmazások Visual Studio és Azure szolgáltatásokkal való létrehozásához
description: Ismerje meg a támogatott natív és platformfüggetlen nyelveket az ügyfélalkalmazások létrehozásához.
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 714916c755ce50f6e596cee4be85ceb0b142c789
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240858"
---
# <a name="choose-mobile-development-frameworks"></a>Válassza ki a mobil fejlesztési keretrendszereket
A fejlesztők ügyféloldali technológiáksegítségével maguk is hozhatnak létre mobilalkalmazásokat speciális keretrendszerek és minták használatával a platformok közötti megközelítéshez. Döntési tényezőik alapján a fejlesztők a következőket építhetik:
- Natív egyplatformos alkalmazások olyan nyelvek használatával, mint a C célkitűzés és a Java
- Platformfüggetlen alkalmazások a Xamarin, a .NET és a C használatával #
- Hibrid alkalmazások a Cordova és változatai használatával

## <a name="native-platforms"></a>Natív platformok
Natív alkalmazás létrehozása platformspecifikus programozási nyelvek, SDK-k, fejlesztői környezetek és az operációs rendszer szállítói által biztosított egyéb eszközök létrehozása szükséges.

### <a name="ios"></a>iOS
Az Apple által létrehozott és kifejlesztett iOS-t az Apple készülékeken, nevezetesen az iPhone-on és az iPaden való alkalmazások készítésére használják.

- **Programozási nyelvek**: Objective-C, Swift
- **IDE**: Xcode
- **SDK**: iOS SDK

### <a name="android"></a>Android
Által tervezett Google és a legnépszerűbb operációs rendszer a világon, Android létrehozásához használt alkalmazások futtatható számos okostelefonok és tabletta.

- **Programozási nyelv**: Java, Kotlin 
- **IDE**: Android Studio és Android fejlesztői eszközök 
- **SDK**: Android SDK

### <a name="windows"></a>Windows
- **Programozási nyelv**: C #
- **IDE**: Visual Studio, Visual Studio kód
- **SDK**: Windows SDK

#### <a name="pros"></a>Előnyök
- Jó felhasználói élmény
- Reszponzív alkalmazások nagy teljesítményű, és képes interfész natív könyvtárak
- Rendkívül biztonságos alkalmazások

#### <a name="cons"></a>Hátrányok
- Az alkalmazás csak egy platformon fut
- Több fejlesztői erőforrás-igényesés költségesebb alkalmazás létrehozása
- Alsó kód újrafelhasználása

## <a name="cross-platforms-and-hybrid-applications"></a>Platformok közötti és hibrid alkalmazások
A platformfüggetlen alkalmazások segítségével egyszer írhat natív mobilalkalmazásokat, oszthat meg kódot, és futtathatja őket iOS, Android és Windows rendszeren.

### <a name="xamarin"></a>Xamarin
A Microsoft tulajdonában lévő [Xamarin](https://visualstudio.microsoft.com/xamarin/) robusztus, platformfüggetlen mobilalkalmazásokat hozhat létre C#-ban. A Xamarin egy olyan osztálykönyvtárral és futásidejű vel rendelkezik, amely számos platformon működik, például iOS, Android és Windows rendszeren. Emellett natív (nem értelmezett) alkalmazásokat is összeállít, amelyek nagy teljesítményt nyújtanak. Xamarin egyesíti az összes képességeit a natív platformok, és hozzáteszi, számos erős funkciók saját.

- **Programozási nyelv**: C #
- **IDE**: Visual Studio Windows vagy Mac rendszeren

### <a name="react-native"></a>Reagálni natív
A Facebook által 2015-ben kiadott [React Native](https://facebook.github.io/react-native/) egy [nyílt forráskódú](https://github.com/facebook/react-native) JavaScript keretrendszer valódi, natív módon renderelésre szolgáló mobil alkalmazások írásához iOS és Android rendszerre. Ez alapján React, Facebook JavaScript könyvtár épület felhasználói felületek. A böngésző célzása helyett mobil platformokat céloz meg. A React Native a webes összetevők helyett natív összetevőket használ építőelemként.
 
- **Programozási nyelv**: JavaScript
- **IDE**: Visual Studio kód

### <a name="unity"></a>Unity
 Unity egy motor optimalizált játékok létrehozására. Segítségével kiváló minőségű 2D vagy 3D alkalmazásokat hozhat elő C# -val olyan platformokra, mint a Windows, az iOS, az Android és az Xbox.

### <a name="cordova"></a>Cordova
A Cordova lehetővé teszi hibrid alkalmazások készítését a Visual Studio Tools for Apache Cordova vagy a Visual Studio Code használatával a Cordova-bővítményekkel. A hibrid megközelítés, akkor megoszthatja alkatrészek weboldalak és újra webszerver-alapú alkalmazások üzemeltetett webalkalmazás megközelítések alapján Cordova.

#### <a name="pros"></a>Előnyök
- Nagyobb kódhasználhatóság egy kódbázis létrehozásával több platformhoz
- Több platformon is szélesebb közönséget lát el
- Drámai csökkentés a fejlesztési időben
- Könnyen indítható és frissíthető

#### <a name="cons"></a>Hátrányok
- Alacsonyabb teljesítmény
- A rugalmasság hiánya
- Minden platform egyedi funkciókkal és funkciókkal rendelkezik, hogy a natív alkalmazás kreatívabb legyen
- Megnövelt felhasználói felületi tervezési idő
- Szerszám korlátozása
