---
title: 'Gyors útmutató: Futtassa a Speech eszközök SDK Android rendszeren – beszédszolgáltatások'
titleSuffix: Azure Cognitive Services
description: Előfeltételek és a egy beszéd eszközök Android SDK – első lépések utasítások.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 5bebdd77afb9e9c77624ea4266ca217567dbf143
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072390"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-android"></a>Gyors útmutató: A beszédfelismerés Devices SDK-val mintaalkalmazás futtatásának androidon

Ezt a gyorsútmutatót követve elsajátíthatja a beszédfeldolgozó termék, illetve használja, mint a beszédfelismerés eszközök Androidhoz készült SDK használata egy [beszélgetés Beszédátírási](conversation-transcription-service.md) eszköz.

Ehhez az Útmutatóhoz egy [Azure Cognitive Services](get-started.md) beszédszolgáltatások erőforrás rendelkező fiókot. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](https://azure.microsoft.com/try/cognitive-services/) egy előfizetői azonosító beszerzéséhez.

A mintaalkalmazás forráskódja megtalálható a Speech Devices SDK-val. Is [elérhető a Githubon](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Előfeltételek

A beszédfelismerés eszközök SDK használata előtt kell:

* Kövesse az utasításokat a [development Kitet](get-speech-devices-sdk.md) az eszköz bekapcsolásához.

* Töltse le a legújabb verzióját a [Speech Devices SDK-val](https://aka.ms/sdsdk-download), és bontsa ki a .zip lépjen a munkakönyvtárba.
   > [!NOTE]
   > Az Android-minta-Release.zip fájlbeágyazások mintaalkalmazás Android, és ez a rövid útmutató azt feltételezi, hogy az alkalmazás C:\SDSDK\Android-Sample-Release kicsomagolta

* Az első egy [beszédszolgáltatások Azure-előfizetés kulcsa](get-started.md)

* Ha szeretné használni a beszélgetés Beszédátírási kell használnia egy [. kör alakú mikrofon eszköz](get-speech-devices-sdk.md) , és ez a funkció jelenleg csak az "en-US" és "zh-CN" régiókban, "centralus" és "eastasia" érhető el. Ezen beszélgetés Beszédátírási használandó régiók egyikében speech kulccsal kell rendelkeznie.

* Ha azt tervezi, használja a beszédszolgáltatások azonosítása céljából leképezések (műveletek) a felhasználó utterances, szüksége lesz egy [intelligens hangfelismerési szolgáltatás (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) előfizetés. A LUIS és szándékfelismerés kapcsolatos további tudnivalókért lásd: [ismeri fel a LUIS, beszédfelismerés leképezések C# ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp).

    Is [hozzon létre egy egyszerű LUIS-modellnek](https://docs.microsoft.com/azure/cognitive-services/luis/) vagy használja a LUIS-modell, a LUIS-example.json mintát. A LUIS-modell érhető el minta a [Speech eszközök SDK letöltési hely](https://aka.ms/sdsdk-luis). Feltölteni a JSON-fájlt is a modell a [LUIS portál](https://www.luis.ai/home), jelölje be **importálása új alkalmazás**, majd válassza ki a JSON-fájlt.

* Telepítés [Android Studio](https://developer.android.com/studio/) és [Vysor](https://vysor.io/download/) a számítógépen.

## <a name="set-up-the-device"></a>Az eszköz beállítása

1. Indítsa el a Vysor a számítógépen.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1. Az eszköz szerepelnie kell **válasszon egy eszközt**. Válassza ki a **nézet** gomb mellett az eszköz.

1. A mappa ikont választva a vezeték nélküli hálózathoz csatlakozni, és válassza **beállítások** > **WLAN**.

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

## <a name="run-the-sample-application"></a>A mintaalkalmazás futtatása

Az development kit konfigurációjának ellenőrzése, hozza létre és telepítse a mintaalkalmazást:

1. Indítsa el az Android Studio.

1. Válassza az **Open an existing Android Studio project** (Létező Android Studio-projekt megnyitása) lehetőséget.

   ![Android Studio – egy meglévő projekt megnyitása](media/speech-devices-sdk/qsg-5.png)

1. Ugrás a C:\SDSDK\Android-Sample-Release\example. Válassza ki **OK** a példaprojekt megnyitásához.

1. A beszédfelismerés előfizetési kulcs hozzáadása a forráskódban. Ha azt szeretné, próbálkozhat szándékának felismerése, is hozzáadhat a [hangfelismerési szolgáltatás](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) előfizetési kulcs és az alkalmazás azonosítóját.

   A beszéd- és LUIS adatait MainActivity.java hiányzóra:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Beszélgetés beszédátírási használja, ha a speech kulcs és a régió adatait conversation.java is szükséges:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. Az alapértelmezett ébresztési szó (kulcsszó) az "Számítógép". Megpróbálhatja egy másik megadott ébresztési szavakat, például "Gép" vagy "Assistant". Az erőforrásfájlokat, az alternatív ébresztési szavak szerepelnek, a beszéd Devices SDK-val, a kulcsszó mappában. Ha például C:\SDSDK\Android-Sample-Release\keyword\Computer ébresztési "Számítógép" szót használt fájlokat tartalmazza.

   > [!TIP]
   > Emellett [hozzon létre egy egyéni ébresztési szó](speech-devices-sdk-create-kws.md).

    Új ébresztési szó használatához frissítse az alábbi két sort a `MainActivity.java`, és másolja a hálózati ébresztési word csomagot az alkalmazásban. Például használja az ébresztési szót "Machine" szó a hálózati ébresztési csomag kws-machine.zip:

   * Másolja a hálózati ébresztési word csomagot abba a mappába "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\".
   * Frissítés a `MainActivity.java` kulcsszó és a csomag neve:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Frissítse a következő sorokat a mikrofon tömb geometriai beállításokat tartalmazzák:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   Ez a táblázat felsorolja a támogatott értékek:

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

1. Próbálja ki az új beszélgetés Beszédátírási bemutatót. Indítsa el a munkamenet indítása átírás. Alapértelmezés szerint a Vendég a mindenki számára. Azonban ha résztvevő hangalapú aláírásokat, elhelyezheti egy fájlba `/video/participants.properties` az eszközön. A beszédfelismerési aláírást létrehozni, tekintse meg [beszélgetések (SDK) alapuló átírás](how-to-use-conversation-transcription-service.md).

   ![Beszélgetés Beszédátírási bemutatóalkalmazást](media/speech-devices-sdk/qsg-15.png)

1. Kísérlet!

## <a name="troubleshooting"></a>Hibaelhárítás

   Ha nem sikerül, a beszéd-eszközre. Írja be a következő parancsot egy parancssori ablakot. Az eszközök listáját adja vissza:

   ```powershell
    adb devices
   ```

   > [!NOTE]
   > Ez a parancs használ az Android-hibakeresési híd `adb.exe`, amely az Android Studio telepítést része. Ez az eszköz található C:\Users\[felhasználónév] \AppData\Local\Android\Sdk\platform-eszközöket. Ebben a címtárban is hozzáadhat az elérési úthoz, hogy kényelmesebbé meghívásához `adb`. Ellenkező esetben meg kell adnia a teljes elérési útja a telepített minden parancshoz, amely meghívja a adb.exe `adb`.
   >
   > Ha hibaüzenet jelenik meg `no devices/emulators found` majd ellenőrizze, hogy az USB-kábel csatlakoztatva van, és győződjön meg arról, kiváló minőségű kábel szolgál.
   >

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tekintse át a kibocsátási megjegyzések](devices-sdk-release-notes.md)
