---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: 4a2d9f382045db2aeab80d9ecf5a05b031bcbc9e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400132"
---
Ebben a rövid útmutatóban megtudhatja, hogyan használhatja az Android-alapú beszédfelismerési eszközök SDK-t beszédfelismerésre képes termék létrehozásához vagy [beszélgetési átírási](../conversation-transcription-service.md) eszközként való használatához.

Ez az útmutató egy [Azure Cognitive Services-fiókot](../get-started.md) igényel egy beszédszolgáltatás-erőforrással. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](https://azure.microsoft.com/try/cognitive-services/) egy előfizetői azonosító beszerzéséhez.

A mintaalkalmazás forráskódja megtalálható a beszédeszközök SDK-ban. A [GitHubon](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)is elérhető.

## <a name="prerequisites"></a>Előfeltételek

A beszédfelismerési eszközök SDK használatának megkezdése előtt a következőkre van szükség:

- Kövesse a [fejlesztői készlethez](../get-speech-devices-sdk.md) mellékelt utasításokat a készülék bekapcsolására.

- Töltse le a [beszédeszközök SDK](https://aka.ms/sdsdk-download)legújabb verzióját, és bontsa ki a .zip-et a munkakönyvtárába.

  > [!NOTE]
  > Ez a rövid útmutató feltételezi, hogy az alkalmazás kibontása a C:\SDSDK\Android-Sample-Release

- [Azure-előfizetési kulcs beolvasása a beszédfelismerési szolgáltatáshoz](../get-started.md)

- Ha azt tervezi, hogy használja a beszélgetés átírása kell használni a [kör alakú mikrofon eszköz,](../get-speech-devices-sdk.md) és ez a funkció jelenleg csak az "en-US" és a "zh-CN" régiókban, "centralus" és "eastasia". A beszélgetési átírás használatához az egyik ilyen régióban rendelkeznie kell egy beszédbillentyűvel.

- Ha azt tervezi, hogy a beszédfelismerési szolgáltatás segítségével azonosítja a leképezések (vagy műveletek) a felhasználói utterances, szüksége lesz egy [language understanding service (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) előfizetés. A LUIS-ról és a szándékfelismerésről a [Beszédleképezések felismerése a LUIS, C# ( Beszédleképezések felismerése a LUIS, C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)) témakört.

  Létrehozhat [egy egyszerű LUIS-modellt,](https://docs.microsoft.com/azure/cognitive-services/luis/) vagy használhatja a minta LUIS-modell, LUIS-example.json. A minta LUIS modell elérhető a [beszédeszközök SDK letöltési oldalon.](https://aka.ms/sdsdk-luis) A modell JSON-fájljának a [LUIS-portálra](https://www.luis.ai/home)való feltöltéséhez válassza **az Új alkalmazás importálása**lehetőséget, majd válassza a JSON-fájlt.

- Telepítse [az Android Studio](https://developer.android.com/studio/) és a [Vysor](https://vysor.io/download/) alkalmazást a számítógépre.

## <a name="set-up-the-device"></a>Az eszköz beállítása

1. Indítsa el a Vysor-ot a számítógépen.

   ![Vysor (vysor)](../media/speech-devices-sdk/qsg-3.png)

1. Az eszköznek az Eszköz kiválasztása csoportban kell **szerepelnie.** Válassza az eszköz melletti **Nézet** gombot.

1. Csatlakozzon a vezeték nélküli hálózathoz a mappa ikonjának kiválasztásával, majd a **Beállítások** > **WLAN parancsra.**

   ![Vysor WLAN](../media/speech-devices-sdk/qsg-4.png)

   > [!NOTE]
   > Ha vállalata rendelkezik az eszközök Wi-Fi-rendszerhez való csatlakoztatásával kapcsolatos irányelvekkel, be kell szereznie a MAC-címet, és kapcsolatba kell lépnie az informatikai részleggel arról, hogyan csatlakoztathatja azt a vállalat Wi-Fi-hálózatához.
   >
   > A fejlesztői készlet MAC-címének megkereséséhez jelölje ki a fájlmappa ikonját a fejlesztői készlet asztalán.
   >
   > ![Vysor fájlmappa](../media/speech-devices-sdk/qsg-10.png)
   >
   > Válassza a **Beállítások lehetőséget.** Keressen rá a "mac-cím" kifejezésre, majd válassza a **Mac-cím** > **Speciális WLAN**lehetőséget. Írja le a párbeszédpanel alján megjelenő MAC-címet.
   >
   > ![Vysor MAC-cím](../media/speech-devices-sdk/qsg-11.png)
   >
   > Egyes vállalatok nak lehet egy időkorlát, hogy mennyi ideig lehet csatlakoztatni egy eszközt a Wi-Fi rendszerhez. Előfordulhat, hogy meghatározott számú nap elteltével ki kell terjesztenie a fejlesztői készlet regisztrációját a Wi-Fi-rendszerrel.

## <a name="run-the-sample-application"></a>A mintaalkalmazás futtatása

A fejlesztői készlet beállításának ellenőrzéséhez építse fel és telepítse a mintaalkalmazást:

1. Indítsa el az Android Studio alkalmazást.

1. Válassza az **Open an existing Android Studio project** (Létező Android Studio-projekt megnyitása) lehetőséget.

   ![Android Studio - Meglévő projekt megnyitása](../media/speech-devices-sdk/qsg-5.png)

1. Nyissa meg a C:\SDSDK\Android-Sample-Release\example webhelyet. A példaprojekt megnyitásához válassza az **OK gombot.**

1. Állítsa be a gradle-t úgy, hogy a beszédsdkra hivatkozzon. A következő fájlok találhatók a **Gradle Scripts** alatt az Android Studio-ban.

    Frissítse a **build.gradle(Project:példa)**, az allprojects blokk kell egyeznie az alábbi, hozzáadásával a maven sorokat.

    ```xml
    allprojects {
        repositories {
            google()
            jcenter()
            mavenCentral()
            maven {
                url 'https://csspeechstorage.blob.core.windows.net/maven/'
            }
        }
    }
    ```

    Frissítse a **build.gradle(Module:app)** alkalmazást úgy, hogy hozzáadja ezt a sort a függőségek szakaszhoz. 
    
    ```xml
    implementation'com.microsoft.cognitiveservices.speech:client-sdk:1.11.0'
    ```
    
1. Adja hozzá a beszéd-előfizetési kulcsot a forráskódhoz. Ha meg szeretné próbálni a szándékfelismerést, adja hozzá a [Language Understanding szolgáltatás előfizetési](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) kulcsát és az alkalmazásazonosítót is.

   A beszéd és a LUIS esetében az ön adatai a MainActivity.java-ba kerülnek:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Ha beszélgetésátírást használ, a speech key és a régió adataira is szükség van a conversation.java:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. Az alapértelmezett kulcsszó a "Számítógép". Kipróbálhatja a többi megadott kulcsszót is, például a "Gép" vagy az "Asszisztens". Az alternatív kulcsszavak erőforrásfájljai a Beszédeszközök SDK mappában, a kulcsszómappában találhatók. A C:\SDSDK\Android-Sample-Release\keyword\Computer például a "Számítógép" kulcsszóhoz használt fájlokat tartalmazza.

   > [!TIP]
   > [Egyéni kulcsszót](../speech-devices-sdk-create-kws.md)is létrehozhat.

   Új kulcsszó használatához frissítse a következő `MainActivity.java`két sort a alkalmazásban, és másolja a kulcsszócsomagot az alkalmazásba. Például, hogy használja a kulcsszó "Gép" a kulcsszó csomag kws-machine.zip:

   - Másolja a kulcsszócsomagot a "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\" mappába.
   - Frissítse `MainActivity.java` a kulcsszót és a csomag nevét:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Frissítse a mikrofontömb geometriai beállításait tartalmazó következő sorokat:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   Ez a táblázat a támogatott értékeket sorolja fel:

   | Változó | Jelentés | Elérhető értékek |
   | -------- | ------- | ---------------- |
   | `DeviceGeometry` | Fizikai mikrofon konfiguráció | Körfejlesztési készletesetén:`Circular6+1` |
   |          |         | Lineáris fejlesztői készlet esetén:`Linear4` |
   | `SelectedGeometry` | Szoftveres mikrofon konfiguráció | Az összes mikrofont használó körkörös fejlesztői készlethez:`Circular6+1` |
   |          |         | Négy mikrofont használó körkörös fejlesztői készletesetén:`Circular3+1` |
   |          |         | Az összes mikrofont használó lineáris fejlesztői készlet hez:`Linear4` |
   |          |         | Két mikrofont használó lineáris fejlesztői készlet esetén:`Linear2` |

1. Az alkalmazás létrehozásához válassza a **Futtatás** menü **"Alkalmazás futtatása" parancsát.** Megjelenik **a Telepítési cél kiválasztása** párbeszédpanel.

1. Válassza ki az eszközt, majd az **OK gombra** az alkalmazás eszközre való központi telepítéséhez.

   ![A Telepítési cél kiválasztása párbeszédpanel](../media/speech-devices-sdk/qsg-7.png)

1. A Beszédeszközök SDK példaalkalmazás elindul, és a következő beállításokat jeleníti meg:

   ![Minta beszédfelismerési eszközök SDK példa alkalmazás és beállítások](../media/speech-devices-sdk/qsg-8.png)

1. Próbálja ki az új beszélgetésátírás demót. Kezdje el az átírást a "Start Session" felirattal. Alapértelmezés szerint mindenki vendég. Ha azonban rendelkezik a résztvevők hangaláírásával, azok `/video/participants.properties` behelyezhetők az eszközön lévő fájlba. A hangaláírás létrehozásához tekintse meg az [Átírási beszélgetéseket (SDK).](../how-to-use-conversation-transcription-service.md)

   ![Demo beszélgetés átírása alkalmazás](../media/speech-devices-sdk/qsg-15.png)

1. Kísérlet!

## <a name="troubleshooting"></a>Hibaelhárítás

Ha nem tud csatlakozni a beszédeszközhöz. Írja be a következő parancsot a parancssorablakba. Ez visszaad egy listát az eszközök:

```powershell
 adb devices
```

> [!NOTE]
> Ez a parancs az Android `adb.exe`Debug Bridge programot használja, amely az Android Studio telepítésének része. Ez az eszköz a C:\Users\[felhasználónévben található]\AppData\Local\Android\Sdk\platform-tools. Hozzáadhatja ezt a könyvtárat az elérési úthoz, hogy kényelmesebb legyen a meghívás. `adb` Ellenkező esetben minden meghívja a műveletben meg kell adnia az `adb`adb.exe telepítésének teljes elérési útját.
>
> Ha hibát `no devices/emulators found` lát, ellenőrizze, hogy az USB-kábel csatlakoztatva van-e, és győződjön meg arról, hogy jó minőségű kábelt használ.
