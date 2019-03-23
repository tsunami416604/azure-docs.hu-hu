---
title: A beszédfelismerés Devices SDK-val – beszédszolgáltatások használatának első lépései
titleSuffix: Azure Cognitive Services
description: Előfeltételek és a Speech eszközök SDK – első lépések vonatkozó utasításokat.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 0d520a9dc8d60a386d25694d441931fbc3ed38e4
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58352068"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>A beszédfelismerés eszközök SDK használatának első lépései

Ez a cikk ismerteti, hogyan konfigurálhatja a fejlesztési számítógép és a Speech eszköz development Kitet, a beszéd eszköz SDK-val speech-kompatibilis eszközök fejlesztéséhez. Ezután létrehozhatja és üzembe helyezünk egy mintaalkalmazást az eszközön.

A mintaalkalmazás forráskódja megtalálható a Speech Devices SDK-val. Is [elérhető a Githubon](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Előfeltételek

Fejlesztés a Speech Devices SDK-val a Kezdés előtt gyűjtse össze az adatokat és a szükséges szoftver:

* Get- [ROOBO a fejlesztői készlet](http://ddk.roobo.com/). Kezdőcsomagok lineáris vagy kör alakú mikrofon tömb konfigurációk érhetők el. Válassza ki az igényeinek megfelelő konfigurációját.

    |Development kit konfigurálása|Hangszóró helye|
    |-----------------------------|------------|
    |Kör alakú|Bármelyik irányba, az eszközről|
    |Lineáris|Az eszköz előtt|

* A beszédfelismerés eszközök SDK-t Androidos mintaalkalmazás tartalmazza a legújabb verziójának beszerzéséhez a [Speech eszközök SDK letöltési hely](https://shares.datatransfer.microsoft.com/). Bontsa ki a zip-fájlt egy helyi mappába, például a C:\SDSDK.

* Telepítés [Android Studio](https://developer.android.com/studio/) és [Vysor](https://vysor.io/download/) a számítógépen.

* Get- [beszédszolgáltatások előfizetési kulcs](get-started.md). 30 napos ingyenes próbaverzió beszerzése, illetve egy kulcs lekérése az Azure-irányítópulton.

* Ha szeretné használni a beszédszolgáltatások szándékának felismerése, iratkozzon fel a [hangfelismerési szolgáltatás](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) (LUIS) és [előfizetési kulcs lekérése](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription).

    Is [hozzon létre egy egyszerű LUIS-modellnek](https://docs.microsoft.com/azure/cognitive-services/luis/) vagy használja a LUIS-modell, a LUIS-example.json mintát. A LUIS-modell érhető el minta a [Speech eszközök SDK letöltési hely](https://shares.datatransfer.microsoft.com/). Feltölteni a JSON-fájlt is a modell a [LUIS portál](https://www.luis.ai/home), jelölje be **importálása új alkalmazás**, majd válassza ki a JSON-fájlt.

## <a name="set-up-the-development-kit"></a>A fejlesztői készlet beállítása

1. A fejlesztői készlet két micro USB-összekötővel rendelkezik. A bal oldali összekötő adatelérésnek köszönhetően a csomag és az alábbi képen kiemelt Power. A jobb oldalon egy vezérlő, és meg van jelölve az a képen hibakeresést.

    ![Csatlakozás a fejlesztői csomag](media/speech-devices-sdk/qsg-1.png)

1. A csomag Power micro USB-kábelen keresztül a kiemelt port kapcsolódni egy számítógéphez, illetve power adapter használatával. Egy zöld power kijelző fog helyezzen üzembe az első tábla alatt.

1. Szabályozhatja a fejlesztői csomag csatlakozni a hibakeresési port számítógép egy második micro USB-kábel használatával. Ez elengedhetetlen, használjon egy kiváló minőségű kábelt, megbízható kommunikációt.

1. Elhelyezés a fejlesztői készlet sem a kör alakú és lineáris konfigurációját.

    |Development kit konfigurálása|Tájolás|
    |-----------------------------|------------|
    |Kör alakú|Mintha a mikrofonok a felső határ irányuló|
    |Lineáris|Az oldalán a mikrofonok felénk (az alábbi ábrán látható)|

    ![lineáris dev csomag tájolása](media/speech-devices-sdk/qsg-2.png)

1. Telepítse a tanúsítványokat, és állítsa be az engedélyeket a hangeszköz. Egy parancssori ablakban írja be a következő parancsokat:

   ```
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Ezek a parancsok használata az Android-hibakeresési híd `adb.exe`, amely az Android Studio telepítést része. Ez az eszköz található C:\Users\[felhasználónév] \AppData\Local\Android\Sdk\platform-eszközöket. Ebben a címtárban is hozzáadhat az elérési úthoz, hogy kényelmesebbé meghívásához `adb`. Ellenkező esetben meg kell adnia a teljes elérési útja a telepített minden parancshoz, amely meghívja a adb.exe `adb`.
    >
    > Ha hibaüzenet jelenik meg `no devices/emulators found` ezután ellenőrizze, hogy az USB-kábel csatlakoztatva van, és a egy kiváló minőségű kábel. Használhat `adb devices` ellenőrizze, hogy a számítógép kommunikálni tudjon az a csomagban található, azon eszközök listáját adja vissza.

    > [!TIP]
    > A számítógép mikrofon és előadó, és ellenőrizze, hogy dolgozik a fejlesztői készlet mikrofonok vypnutí. Így nem fog véletlenül indít el az eszközt az audio a számítógépről.

1.  Indítsa el a Vysor a számítógépen.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  Az eszköz szerepelnie kell **válasszon egy eszközt**. Válassza ki a **nézet** gomb mellett az eszköz.

1.  A mappa ikont választva a vezeték nélküli hálózathoz csatlakozni, és válassza **beállítások** > **WLAN**.

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)

    > [!NOTE]
    > Ha a vállalati eszközök csatlakoztatása a Wi-Fi rendszer kapcsolatos házirendek, a MAC-cím beszerzése, és hogyan lehet csatlakozni a vállalati Wi-Fi kapcsolatban az informatikai részlegtől szüksége.
    >
    > A fejlesztői csomag a MAC-cím megkereséséhez válassza ki a fájlmappa ikonra az asztalon, a fejlesztői csomag.
    >
    >  ![Vysor fájlmappa](media/speech-devices-sdk/qsg-10.png)
    >
    > Válassza ki **beállítások**. Keresse meg a "mac-cím", és válassza **Mac-cím** > **speciális WLAN**. Írja le a MAC-cím, amely a párbeszédpanel alján jelenik meg.
    >
    > ![Vysor MAC-cím](media/speech-devices-sdk/qsg-11.png)
    >
    > Egyes vállalatok előfordulhat, hogy mennyi ideig lehet egy eszköz időkorlátja a Wi-Fi rendszer csatlakozik. Szüksége lehet egy adott számú nap eltelte után a fejlesztői csomag regisztrálása a Wi-Fi-rendszer a kiterjesztése.
    >
    > Ha azt szeretné, a beszélő csatlakoztatása a fejlesztői csomag, akkor csatlakozhat ki a következő vonal. Egy jó minőségű, 3.5-mm speaker kell kiválasztani.
    >
    > ![Vysor hang](media/speech-devices-sdk/qsg-14.png)

## <a name="run-a-sample-application"></a>A mintaalkalmazás futtatása

A ROOBO tesztek futtatása, és az development kit konfigurációjának ellenőrzése, hozza létre és telepítse a mintaalkalmazást:

1. Indítsa el az Android Studio.

1. Válassza az **Open an existing Android Studio project** (Létező Android Studio-projekt megnyitása) lehetőséget.

   ![Android Studio – egy meglévő projekt megnyitása](media/speech-devices-sdk/qsg-5.png)

1. Ugrás a C:\SDSDK\Android-Sample-Release\example. Válassza ki **OK** a példaprojekt megnyitásához.

1. A beszédfelismerés előfizetési kulcs hozzáadása a forráskódban. Ha azt szeretné, próbálkozhat szándékának felismerése, is hozzáadhat a [hangfelismerési szolgáltatás](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) előfizetési kulcs és az alkalmazás azonosítóját.

   A kulcsok és az alkalmazásadatok nyissa meg a következő sorokat a forrásfájl MainActivity.java:

   ```java
   // Subscription
   private static final String SpeechSubscriptionKey = "[your speech key]";
   private static final String SpeechRegion = "westus";
   private static final String LuisSubscriptionKey = "[your LUIS key]";
   private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
   private static final String LuisAppId = "[your LUIS app ID]"
   ```

1. Az alapértelmezett ébresztési szó (kulcsszó) az "Számítógép". Megpróbálhatja egy másik megadott ébresztési szavakat, például "Gép" vagy "Assistant". Az erőforrásfájlokat, az alternatív ébresztési szavak szerepelnek, a beszéd Devices SDK-val, a kulcsszó mappában. Ha például C:\SDSDK\Android-Sample-Release\keyword\Computer ébresztési "Számítógép" szót használt fájlokat tartalmazza.

    Emellett [hozzon létre egy egyéni ébresztési szó](speech-devices-sdk-create-kws.md).

    Új ébresztési szó használatához az alábbi két sort a MainActivity.java frissítése és az ébresztési word-csomagot másolja az alkalmazáshoz. Az ébresztési például word "Machine" szó a hálózati ébresztési csomag kws a-machine.zip:

   * Másolja a hálózati ébresztési word csomagot abba a mappába "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\".
   * Frissítse a MainActivity.java kulcsszó és a csomag neve: 
    
     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Frissítse a következő sorokat a mikrofon tömb geometriai beállításokat tartalmazzák:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```
   A következő táblázat ismerteti a rendelkezésre álló értékeket:

   |Változó|Jelentés|Elérhető értékek|
   |--------|-------|----------------|
   |`DeviceGeometry`|Fizikai mic-konfiguráció|A kör alakú dev csomag: `Circular6+1` |
   |||Egy lineáris dev csomag: `Linear4`|
   |`SelectedGeometry`|Szoftver mic-konfiguráció|A kör alakú fejlesztési Kit használó összes mikrofonok előtt: `Circular6+1`|
   |||A kör alakú fejlesztési Kit használó négy mikrofonok előtt: `Circular3+1`|
   |||Egy lineáris fejlesztési Kit használó összes mikrofonok előtt: `Linear4`|
   |||Egy lineáris dev csomag, amely két megkérhetném használ: `Linear2`|


1. A hozhat létre az alkalmazás a **futtatása** menüjében válassza **"alkalmazás" futtatása**. A **válassza ki a központi telepítési cél** párbeszédpanel jelenik meg.

1. Válassza ki az eszközt, és válassza **OK** az eszközön az alkalmazás telepítéséhez.

    ![Válassza ki a központi telepítési cél párbeszédpanel](media/speech-devices-sdk/qsg-7.png)

1. A Speech Devices SDK-val példa alkalmazás elindul, és megjeleníti a következő beállításokat:

   ![Beszéd Devices SDK-val példa mintaalkalmazás és beállítások](media/speech-devices-sdk/qsg-8.png)

1. Kísérlet!

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="certificate-failures"></a>Tanúsítványhibák

Ha tanúsítványhibák a Speech Services használata közben, győződjön meg arról, hogy az eszköz rendelkezik-e a helyes dátum és idő:

1. Lépjen a **beállítások**. A **rendszer**válassza **dátum és idő**.

    ![A beállítások területen válassza ki a dátum és idő](media/speech-devices-sdk/qsg-12.png)

1. Tartsa a **automatikus dátum és idő** lehetőség van kijelölve. A **válassza időzóna**, válassza ki az aktuális időzóna.

    ![Válassza ki a dátum és időzóna-beállításai](media/speech-devices-sdk/qsg-13.png)

    Ha látja, hogy a fejlesztői csomag idő megegyezik-e a számítógépen lévő idő, a fejlesztői csomag csatlakozik az internethez.

    Fejlesztési kapcsolatos további információkért lásd: a [ROOBO – fejlesztési útmutató](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

### <a name="audio"></a>Hang

ROOBO biztosít olyan eszköz, amely rögzíti az összes hanganyag flash-memória. Hang elhárításának segíthet. Az eszköz egy verzióját minden egyes development kit konfiguráció biztosítunk. Az a [ROOBO hely](http://ddk.roobo.com/), válassza ki az eszközt, és válassza a **ROOBO eszközök** hivatkozásra a lap alján.
