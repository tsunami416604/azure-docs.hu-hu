---
title: Válassza ki a Visual Studióval és az Azure-szolgáltatásokkal rendelkező ügyfélalkalmazások létrehozására szolgáló előtér-fejlesztési platformot
description: Ismerkedjen meg a támogatott natív és platformfüggetlen nyelvekkel ügyfélalkalmazások létrehozásához.
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 891967e74e52c1311d73cbe13fff8c95e479478c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795496"
---
# <a name="choose-mobile-development-framework"></a>Mobile Development Framework kiválasztása
A fejlesztők az ügyféloldali technológiák használatával saját maguk hozhatnak létre mobil alkalmazásokat, adott keretrendszereket és mintákat használva a platformok közötti megközelítéshez. A fejlesztők natív (natív-platformon alapuló) alkalmazásokat hozhatnak létre, például az Objective-C és Java-alkalmazásokat, a Xamarin, a .NET C#-et és a hibrideket (Cordova használatával) és variánsait, a döntéstől függően. tényezők.

## <a name="native-platforms"></a>Natív platformok
A natív alkalmazások létrehozásához platform-specifikus programozási nyelvek, SDK-k, fejlesztési környezet és az operációs rendszer gyártói által biztosított egyéb eszközök szükségesek.

### <a name="ios"></a>iOS
Az Apple hozta létre és fejlesztette ki az iOS-re épülő alkalmazások Apple-eszközökön, azaz iPhone-on és iPaden való futtatásán.

- **Programozási nyelvek** – Objective-C, Swift
- **Ide** -Xcode
- **SDK** – iOS SDK

### <a name="android"></a>Android
A Google és a világ legnépszerűbb operációs rendszere által tervezett alkalmazások számos okostelefonon és tablettán futtathatók.

- **Programozási nyelv** – Java, Kotlin 
- **Ide** -Android Studio és Android fejlesztői eszközök 
- **SDK** – Android SDK

### <a name="windows"></a>Windows
- **Programozási nyelv** –C#
- **Ide** – Visual Studio, Visual Studio Code
- **SDK** – Windows SDK

#### <a name="pros"></a>Szakemberek
- Jó felhasználói élmény
- Rugalmas alkalmazások nagy teljesítményű és natív kódtárakkal való kapcsolati képességgel
- Nagyon biztonságos alkalmazások

#### <a name="cons"></a>Hátrányok
- Az alkalmazás csak egy platformon fut
- További fejlesztői erőforrások és költséges alkalmazások készítése
- Alacsonyabb kódok újrafelhasználása

## <a name="cross-platforms-and-hybrid-applications"></a>Platformok közötti és hibrid alkalmazások
A platformfüggetlen alkalmazások lehetővé teszi a natív Mobile-alkalmazások írását, a kódok megosztását és az iOS-, Android-és Windows-alapú futtatását.

### <a name="xamarin"></a>Xamarin
A Microsoft tulajdonában álló [Xamarin](https://visualstudio.microsoft.com/xamarin/) lehetővé teszi, hogy robusztus, platformfüggetlen mobil alkalmazásokat építsen ki a-ben C#egy olyan osztály-könyvtárral és futtatókörnyezettel, amely számos platformon, például iOS-en, Androidon és Windowson működik, miközben továbbra is natív (nem értelmezett) fordítást végez ) nagy teljesítményű alkalmazások. A Xamarin egyesíti a natív platformok összes képességeit, és számos hatékony funkciót biztosít.

- **Programozási nyelv** –C#
- **Ide** -Visual Studio Windows vagy Mac rendszeren

### <a name="react-native"></a>React Native
A Facebook által kiadott 2015-as verzióban a [natív reagálás](https://facebook.github.io/react-native/) egy [nyílt forráskódú](https://github.com/facebook/react-native) JavaScript-keretrendszer, amely valós, natív módon teszi elérhetővé az iOS és az Android rendszerhez készült mobil alkalmazásokat. A rendszer reagál, a Facebook JavaScript-könyvtára a felhasználói felületek létrehozásához, de a böngésző célzása helyett a mobil platformokra irányul. A natív reagálás a webes összetevők helyett natív összetevőket használ építőelemeként.
 
- **Programozási nyelv** – JavaScript
- **Ide** – Visual Studio Code

### <a name="unity"></a>Unity
 Az Unity a játékok létrehozására optimalizált motor, amely lehetővé teszi, hogy a fejlesztők kiváló minőségű 2D/3D alkalmazásokat C# hozzanak létre különböző platformokkal, például Windows, iOS, Android és Xbox rendszereken.

### <a name="cordova"></a>Cordova
A Cordova lehetővé teszi hibrid alkalmazások készítését az Apache Codova vagy a Visual Studio Code-ban a Cordova-bővítményekkel rendelkező Visual Studio Tools használatával. A hibrid megközelítéssel megoszthatja az összetevőket a webhelyekkel, és újrahasznosíthatja a webkiszolgáló-alapú alkalmazásokat a Cordova-alapú webalkalmazási módszerekkel.

#### <a name="pros"></a>Szakemberek
- Nagyobb programkód-használhatóság a több platformon egy kód létrehozásával
- Több platformon is kiszolgálhatja a szélesebb közönséget
- Drámai csökkenés a fejlesztési időszakban
- Könnyen elindítható és frissíthető

#### <a name="cons"></a>Hátrányok
- Alacsonyabb teljesítmény
- Rugalmasság hiánya
- Minden platform tartalmaz egy egyedi szolgáltatást és funkciót, hogy a natív alkalmazás még kreatív legyen
- Megnövelt felhasználói felület tervezésének ideje
- Eszköz korlátozása
