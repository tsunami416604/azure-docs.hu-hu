---
title: 'Gyors útmutató: A Speech Devices SDK futtatása Android-Speech Service-ben'
titleSuffix: Azure Cognitive Services
description: Előfeltételek és utasítások az Android Speech Devices SDK használatának első lépéseihez.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: f7039b525cf0d52670b8d76a24d8ec3ea5115772
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559105"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-android"></a>Gyors útmutató: A Speech Devices SDK-minta alkalmazás futtatása Androidon

Ebből a rövid útmutatóból megtudhatja, hogyan használhatja az Androidhoz készült Speech Devices SDK-t, hogy beszédfelismerésre alkalmas terméket hozzon létre, vagy [beszélgetéses](conversation-transcription-service.md) átírási eszközként használja azt.

Ehhez az útmutatóhoz egy Speech Services-erőforrással rendelkező [Azure Cognitive Services](get-started.md) -fiókra van szükség. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](https://azure.microsoft.com/try/cognitive-services/) egy előfizetői azonosító beszerzéséhez.

A mintaalkalmazás forráskódja megtalálható a Speech Devices SDK-val. Is [elérhető a Githubon](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Előfeltételek

A Speech Devices SDK használatának megkezdése előtt a következőket kell tennie:

* Kövesse a [fejlesztői csomag](get-speech-devices-sdk.md) utasításait az eszköz bekapcsolásához.

* Töltse le a [Speech Devices SDK](https://aka.ms/sdsdk-download)legújabb verzióját, és bontsa ki a. zip fájlt a munkakönyvtárba.
   > [!NOTE]
   > A Android-Sample-Release. zip fájl tartalmazza az Android minta alkalmazást, és ez a rövid útmutató azt feltételezi, hogy az alkalmazás ki van kinyerve a C:\SDSDK\Android-Sample-Release

* Azure-előfizetési kulcs beszerzése [a Speech Serviceshez](get-started.md)

* Ha azt tervezi, hogy a beszélgetés átírását használja, [körkörös mikrofonos eszközt](get-speech-devices-sdk.md) kell használnia, és ez a funkció jelenleg csak az "en-us" és a "zh-CN" esetében érhető el a (z) "CentralUS" és a "eastasia" régióban. A beszélgetés átírásának használatához az egyik régióban kell lennie egy beszédfelismerési kulcsnak.

* Ha azt tervezi, hogy a Speech Services segítségével azonosítja a szándékokat (vagy műveleteket) a felhasználói hosszúságú kimondott szöveg, szüksége lesz egy [Language Understanding Service (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) előfizetésre. Ha többet szeretne megtudni a LUIS és a szándék felismeréséről, olvassa el a [beszédfelismerési szándékok C#FELISMERÉSe a Luis ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)használatával című témakört.

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

A fejlesztői csomag telepítésének ellenőrzéséhez hozza létre és telepítse a minta alkalmazást:

1. Indítsa el az Android Studio.

1. Válassza az **Open an existing Android Studio project** (Létező Android Studio-projekt megnyitása) lehetőséget.

   ![Android Studio – egy meglévő projekt megnyitása](media/speech-devices-sdk/qsg-5.png)

1. Ugrás a C:\SDSDK\Android-Sample-Release\example. Válassza ki **OK** a példaprojekt megnyitásához.

1. Adja hozzá a beszédfelismerési előfizetéshez tartozó kulcsot a forráskódhoz. Ha azt szeretné, próbálkozhat szándékának felismerése, is hozzáadhat a [hangfelismerési szolgáltatás](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) előfizetési kulcs és az alkalmazás azonosítóját.

   A Speech and LUIS esetében az adatai a MainActivity. Java-ba kerülnek:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Ha a beszélgetési átiratot használja, a beszédfelismerési kulcs és a régió információi is szükségesek a beszélgetésben. Java:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. Az alapértelmezett ébresztési szó (kulcsszó) az "Számítógép". Megpróbálhatja egy másik megadott ébresztési szavakat, például "Gép" vagy "Assistant". Az erőforrásfájlokat, az alternatív ébresztési szavak szerepelnek, a beszéd Devices SDK-val, a kulcsszó mappában. Ha például C:\SDSDK\Android-Sample-Release\keyword\Computer ébresztési "Számítógép" szót használt fájlokat tartalmazza.

   > [!TIP]
   > Emellett [hozzon létre egy egyéni ébresztési szó](speech-devices-sdk-create-kws.md).

    Új Felébresztési szó használatához frissítse a következő két sort `MainActivity.java`a alkalmazásban, és másolja a Wake Word-csomagot az alkalmazásba. Ha például a "Machine" szót szeretné használni a Wake Word csomag KWS-Machine. zip fájljában:

   * Másolja a Wake Word-csomagot a "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\" mappába.
   * Frissítse a `MainActivity.java` kulcsszót és a csomag nevét:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Frissítse a következő sorokat a mikrofon tömb geometriai beállításokat tartalmazzák:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   Ez a táblázat a támogatott értékeket sorolja fel:

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

1. Próbálja ki az új beszélgetés átiratának bemutatóját. A "kezdési munkamenet" megkezdése. Alapértelmezés szerint mindenki a vendég. Ha azonban a résztvevő hang-aláírása is van, akkor az eszközön található fájlba `/video/participants.properties` helyezhetők. A hangaláírás létrehozásához tekintse meg a beszélgetések átírása [(SDK)](how-to-use-conversation-transcription-service.md)című témakört.

   ![Bemutató beszélgetés átirata alkalmazás](media/speech-devices-sdk/qsg-15.png)

1. Kísérlet!

## <a name="troubleshooting"></a>Hibaelhárítás

   Ha nem tud csatlakozni a beszédfelismerési eszközhöz. Írja be a következő parancsot egy parancssori ablakba. Az eszköz az eszközök listáját fogja visszaadni:

   ```powershell
    adb devices
   ```

   > [!NOTE]
   > Ez a parancs az androidos hibakeresési `adb.exe`hidat használja, amely a Android Studio telepítésének része. Ez az eszköz található C:\Users\[felhasználónév] \AppData\Local\Android\Sdk\platform-eszközöket. Ebben a címtárban is hozzáadhat az elérési úthoz, hogy kényelmesebbé meghívásához `adb`. Ellenkező esetben meg kell adnia a teljes elérési útja a telepített minden parancshoz, amely meghívja a adb.exe `adb`.
   >
   > Ha hibaüzenet `no devices/emulators found` jelenik meg, ellenőrizze, hogy az USB-kábel csatlakoztatva van-e, és hogy a rendszer kiváló minőségű kábelt használ-e.
   >

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tekintse át a kibocsátási megjegyzések](devices-sdk-release-notes.md)
