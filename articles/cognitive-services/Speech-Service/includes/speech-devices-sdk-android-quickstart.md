---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: dapine
ms.openlocfilehash: 44ca89cb9d1ecb1a6b7fb91a146cd440f40bb303
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77563219"
---
Ebből a rövid útmutatóból megtudhatja, hogyan használhatja az Androidhoz készült Speech Devices SDK-t, hogy beszédfelismerésre alkalmas terméket hozzon létre, vagy [beszélgetéses átírási](../conversation-transcription-service.md) eszközként használja azt.

Ehhez az útmutatóhoz egy Speech Service-erőforrással rendelkező [Azure Cognitive Services](../get-started.md) -fiókra van szükség. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](https://azure.microsoft.com/try/cognitive-services/) egy előfizetői azonosító beszerzéséhez.

A mintaalkalmazás forráskódja megtalálható a Speech Devices SDK-val. [A githubon is elérhető](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Előfeltételek

A Speech Devices SDK használatának megkezdése előtt a következőket kell tennie:

- Kövesse a [fejlesztői csomag](../get-speech-devices-sdk.md) utasításait az eszköz bekapcsolásához.

- Töltse le a [Speech Devices SDK](https://aka.ms/sdsdk-download)legújabb verzióját, és bontsa ki a. zip fájlt a munkakönyvtárba.

  > [!NOTE]
  > Ez a rövid útmutató azt feltételezi, hogy az alkalmazást a rendszer kinyeri a C:\SDSDK\Android-Sample-Release

- [Azure-előfizetési kulcs beszerzése a Speech Service-hez](../get-started.md)

- Ha azt tervezi, hogy a beszélgetés átírását használja, [körkörös mikrofonos eszközt](../get-speech-devices-sdk.md) kell használnia, és ez a funkció jelenleg csak az "en-us" és a "zh-CN" esetében érhető el a (z) "CentralUS" és a "eastasia" régióban. A beszélgetés átírásának használatához az egyik régióban kell lennie egy beszédfelismerési kulcsnak.

- Ha azt tervezi, hogy a Speech Service segítségével azonosítja a szándékokat (vagy műveleteket) a felhasználói hosszúságú kimondott szöveg, szüksége lesz egy [Language Understanding Service (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) előfizetésre. Ha többet szeretne megtudni a LUIS és a szándék felismeréséről, olvassa el a [beszédfelismerési szándékok C#FELISMERÉSe a Luis ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)használatával című témakört.

  [Létrehozhat egy egyszerű Luis-modellt](https://docs.microsoft.com/azure/cognitive-services/luis/) , vagy használhatja a Luis-example. JSON-t. A LUIS-modell a [Speech Devices SDK letöltési webhelyéről](https://aka.ms/sdsdk-luis)érhető el. A modell JSON-fájljának a Luis- [portálra](https://www.luis.ai/home)való feltöltéséhez válassza az **új alkalmazás importálása**lehetőséget, majd válassza ki a JSON-fájlt.

- Telepítse a [Android Studio](https://developer.android.com/studio/) és a [Vysor](https://vysor.io/download/) a számítógépre.

## <a name="set-up-the-device"></a>Az eszköz beállítása

1. Indítsa el a Vysor a számítógépen.

   ![Vysor](../media/speech-devices-sdk/qsg-3.png)

1. Az eszköznek az **eszköz kiválasztása**területen kell szerepelnie. Válassza a **nézet** gombot az eszköz mellett.

1. Kapcsolódjon a vezeték nélküli hálózathoz a mappa ikon kiválasztásával, majd válassza a **beállítások** > **WLAN**lehetőséget.

   ![Vysor WLAN](../media/speech-devices-sdk/qsg-4.png)

   > [!NOTE]
   > Ha a vállalati eszközök csatlakoztatása a Wi-Fi rendszer kapcsolatos házirendek, a MAC-cím beszerzése, és hogyan lehet csatlakozni a vállalati Wi-Fi kapcsolatban az informatikai részlegtől szüksége.
   >
   > A fejlesztői csomag a MAC-cím megkereséséhez válassza ki a fájlmappa ikonra az asztalon, a fejlesztői csomag.
   >
   > ![Vysor fájlmappa](../media/speech-devices-sdk/qsg-10.png)
   >
   > Válassza a **Beállítások**lehetőséget. Keressen rá a "MAC-címek" kifejezésre, majd válassza a **MAC-címek** > **speciális WLAN**lehetőséget. Írja le a MAC-cím, amely a párbeszédpanel alján jelenik meg.
   >
   > ![Vysor MAC-cím](../media/speech-devices-sdk/qsg-11.png)
   >
   > Egyes vállalatok előfordulhat, hogy mennyi ideig lehet egy eszköz időkorlátja a Wi-Fi rendszer csatlakozik. Szüksége lehet egy adott számú nap eltelte után a fejlesztői csomag regisztrálása a Wi-Fi-rendszer a kiterjesztése.

## <a name="run-the-sample-application"></a>A mintaalkalmazás futtatása

A fejlesztői csomag telepítésének ellenőrzéséhez hozza létre és telepítse a minta alkalmazást:

1. Indítsa el az Android Studio.

1. Válassza az **Open an existing Android Studio project** (Létező Android Studio-projekt megnyitása) lehetőséget.

   ![Android Studio – egy meglévő projekt megnyitása](../media/speech-devices-sdk/qsg-5.png)

1. Ugrás a C:\SDSDK\Android-Sample-Release\example. A példa projekt megnyitásához kattintson **az OK gombra** .

1. Konfigurálja a gradle a Speech SDK-ra való hivatkozáshoz. Az alábbi fájlok a Android Studio-ban található **Gradle-szkriptek** alatt találhatók.

    A **Build. gradle (projekt: példa)** frissítéséhez a allprojects blokknak a Maven-sorok hozzáadásával meg kell egyeznie az alábbi lépésekkel.

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

    A **Build. gradle (modul: alkalmazás)** frissítéséhez adja hozzá ezt a sort a függőségek szakaszhoz. 
    
    ```xml
    implementation'com.microsoft.cognitiveservices.speech:client-sdk:1.9.0'
    ```
    
1. Adja hozzá a beszédfelismerési előfizetéshez tartozó kulcsot a forráskódhoz. Ha szeretné kipróbálni a szándék felismerését, adja hozzá a [Language Understanding szolgáltatás](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) előfizetési kulcsát és az alkalmazás azonosítóját is.

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

1. Az alapértelmezett kulcsszó a "Computer". Kipróbálhatja a többi megadott kulcsszót is, például a "Machine" vagy a "Assistant" kifejezést. Ezen alternatív kulcsszavak erőforrásai a Speech Devices SDK-ban, a Kulcsszóválasztó mappában találhatók. Például a C:\SDSDK\Android-Sample-Release\keyword\Computer tartalmazza a "Computer" kulcsszóhoz használt fájlokat.

   > [!TIP]
   > [Egyéni kulcsszó is létrehozható](../speech-devices-sdk-create-kws.md).

   Új kulcsszó használatához frissítse a `MainActivity.java`következő két sorát, és másolja a Kulcsszóválasztó csomagot az alkalmazásba. Ha például a "Machine" kulcsszót szeretné használni a Kulcsszóválasztó csomag KWS-Machine. zip fájljában:

   - Másolja a kulcsszó-csomagot a "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\" mappába.
   - Frissítse a `MainActivity.java`t a kulcsszóval és a csomag nevével:

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

   | Változó | Jelentés | Elérhető értékek |
   | -------- | ------- | ---------------- |
   | `DeviceGeometry` | Fizikai mic-konfiguráció | Körkörös fejlesztői csomag esetén: `Circular6+1` |
   |          |         | Lineáris fejlesztői csomag esetén: `Linear4` |
   | `SelectedGeometry` | Szoftver mic-konfiguráció | Az összes mikrofont használó körkörös fejlesztői csomag esetében: `Circular6+1` |
   |          |         | Négy mikrofont használó körkörös fejlesztői csomag esetén: `Circular3+1` |
   |          |         | Az összes mikrofont használó lineáris fejlesztői csomag esetében: `Linear4` |
   |          |         | Két mikrofont használó lineáris fejlesztői csomag esetén: `Linear2` |

1. Az alkalmazás létrehozásához a **Futtatás** menüben válassza az alkalmazás **futtatása**lehetőséget. Megjelenik a **központi telepítési cél kiválasztása** párbeszédpanel.

1. Válassza ki az eszközt, majd kattintson **az OK** gombra az alkalmazás telepítéséhez az eszközön.

   ![Válassza ki a központi telepítési cél párbeszédpanel](../media/speech-devices-sdk/qsg-7.png)

1. A Speech Devices SDK-val példa alkalmazás elindul, és megjeleníti a következő beállításokat:

   ![Beszéd Devices SDK-val példa mintaalkalmazás és beállítások](../media/speech-devices-sdk/qsg-8.png)

1. Próbálja ki az új beszélgetés átiratának bemutatóját. A "kezdési munkamenet" megkezdése. Alapértelmezés szerint mindenki a vendég. Ha azonban a résztvevő hangaláírásai vannak, akkor az eszközön lévő `/video/participants.properties`ba helyezhetők. A hangaláírás létrehozásához tekintse meg a beszélgetések átírása [(SDK) című témakört](../how-to-use-conversation-transcription-service.md).

   ![Bemutató beszélgetés átirata alkalmazás](../media/speech-devices-sdk/qsg-15.png)

1. Kísérlet!

## <a name="troubleshooting"></a>Hibakeresés

Ha nem tud csatlakozni a beszédfelismerési eszközhöz. Írja be a következő parancsot egy parancssori ablakba. Az eszköz az eszközök listáját fogja visszaadni:

```powershell
 adb devices
```

> [!NOTE]
> Ez a parancs az androidos hibakeresési hidat használja, `adb.exe`, amely a Android Studio telepítés részét képezi. Ez az eszköz a C:\Users\[Felhasználónév] \AppData\Local\Android\Sdk\platform-tools. található. Ezt a könyvtárat hozzáadhatja az elérési úthoz, hogy kényelmesebb legyen a `adb`meghívása. Ellenkező esetben meg kell adnia az ADB. exe telepítésének teljes elérési útját minden olyan parancsnál, amely meghívja a `adb`.
>
> Ha hibaüzenet jelenik meg `no devices/emulators found` akkor ellenőrizze, hogy az USB-kábel csatlakoztatva van-e, és hogy a rendszer kiváló minőségű kábelt használ-e.
