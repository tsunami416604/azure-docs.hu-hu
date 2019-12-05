---
title: 'Rövid útmutató: a Speech Devices SDK futtatása Android-Speech Service-ben'
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
ms.openlocfilehash: ccc6c2554576d91d2236fb84f81c8aeccdc07be7
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815350"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-android"></a>Rövid útmutató: a beszédfelismerési eszközök SDK-minta alkalmazásának futtatása Androidon

Ebből a rövid útmutatóból megtudhatja, hogyan használhatja az Androidhoz készült Speech Devices SDK-t, hogy beszédfelismerésre alkalmas terméket hozzon létre, vagy [beszélgetéses átírási](conversation-transcription-service.md) eszközként használja azt.

Ehhez az útmutatóhoz egy Speech Service-erőforrással rendelkező [Azure Cognitive Services](get-started.md) -fiókra van szükség. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](https://azure.microsoft.com/try/cognitive-services/) egy előfizetői azonosító beszerzéséhez.

A minta alkalmazás forráskódját a Speech Devices SDK tartalmazza. [A githubon is elérhető](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Előfeltételek

A Speech Devices SDK használatának megkezdése előtt a következőket kell tennie:

- Kövesse a [fejlesztői csomag](get-speech-devices-sdk.md) utasításait az eszköz bekapcsolásához.

- Töltse le a [Speech Devices SDK](https://aka.ms/sdsdk-download)legújabb verzióját, és bontsa ki a. zip fájlt a munkakönyvtárba.

  > [!NOTE]
  > A Android-Sample-Release. zip fájl tartalmazza az Android minta alkalmazást, és ez a rövid útmutató azt feltételezi, hogy az alkalmazás ki van kinyerve a C:\SDSDK\Android-Sample-Release

- [Azure-előfizetési kulcs beszerzése a Speech Service-hez](get-started.md)

- Ha azt tervezi, hogy a beszélgetés átírását használja, [körkörös mikrofonos eszközt](get-speech-devices-sdk.md) kell használnia, és ez a funkció jelenleg csak az "en-us" és a "zh-CN" esetében érhető el a (z) "CentralUS" és a "eastasia" régióban. A beszélgetés átírásának használatához az egyik régióban kell lennie egy beszédfelismerési kulcsnak.

- Ha azt tervezi, hogy a Speech Service segítségével azonosítja a szándékokat (vagy műveleteket) a felhasználói hosszúságú kimondott szöveg, szüksége lesz egy [Language Understanding Service (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) előfizetésre. Ha többet szeretne megtudni a LUIS és a szándék felismeréséről, olvassa el a [beszédfelismerési szándékok C#FELISMERÉSe a Luis ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)használatával című témakört.

  [Létrehozhat egy egyszerű Luis-modellt](https://docs.microsoft.com/azure/cognitive-services/luis/) , vagy használhatja a Luis-example. JSON-t. A LUIS-modell a [Speech Devices SDK letöltési webhelyéről](https://aka.ms/sdsdk-luis)érhető el. A modell JSON-fájljának a Luis- [portálra](https://www.luis.ai/home)való feltöltéséhez válassza az **új alkalmazás importálása**lehetőséget, majd válassza ki a JSON-fájlt.

- Telepítse a [Android Studio](https://developer.android.com/studio/) és a [Vysor](https://vysor.io/download/) a számítógépre.

## <a name="set-up-the-device"></a>Az eszköz beállítása

1. Indítsa el a Vysor a számítógépen.

   ![Vysor](media/speech-devices-sdk/qsg-3.png)

1. Az eszköznek az **eszköz kiválasztása**területen kell szerepelnie. Válassza a **nézet** gombot az eszköz mellett.

1. Kapcsolódjon a vezeték nélküli hálózathoz a mappa ikon kiválasztásával, majd válassza a **beállítások** > **WLAN**lehetőséget.

   ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)

   > [!NOTE]
   > Ha a vállalata rendelkezik házirendekkel az eszközök Wi-Fi rendszerhez való csatlakoztatásáról, be kell szereznie a MAC-címeket, és kapcsolatba kell lépnie az informatikai részleggel arról, hogy miként lehet csatlakoztatni a vállalati Wi-Fi-hez.
   >
   > A fejlesztői csomag MAC-címeinek megkereséséhez válassza ki a fájl mappa ikont a fejlesztői csomag asztalán.
   >
   > ![Vysor fájl mappája](media/speech-devices-sdk/qsg-10.png)
   >
   > Kattintson a **Beállítások** elemre. Keressen rá a "MAC-címek" kifejezésre, majd válassza a **MAC-címek** > **speciális WLAN**lehetőséget. Jegyezze fel a párbeszédpanel alján megjelenő MAC-címeket.
   >
   > ![Vysor MAC-címe](media/speech-devices-sdk/qsg-11.png)
   >
   > Előfordulhat, hogy egyes vállalatoknak van időkorlát arra vonatkozóan, hogy mennyi ideig csatlakozhatnak egy eszköz a Wi-Fi rendszerhez. Előfordulhat, hogy egy adott számú nap elteltével ki kell terjesztenie a fejlesztői készlet regisztrációját a Wi-Fi rendszerbe.

## <a name="run-the-sample-application"></a>A mintaalkalmazás futtatása

A fejlesztői csomag telepítésének ellenőrzéséhez hozza létre és telepítse a minta alkalmazást:

1. Android Studio elindítása.

1. Válassza az **Open an existing Android Studio project** (Létező Android Studio-projekt megnyitása) lehetőséget.

   ![Android Studio – meglévő projekt megnyitása](media/speech-devices-sdk/qsg-5.png)

1. Ugrás a C:\SDSDK\Android-Sample-Release\example. A példa projekt megnyitásához kattintson **az OK gombra** .

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
   > [Egyéni kulcsszó is létrehozható](speech-devices-sdk-create-kws.md).

   Új kulcsszó használatához frissítse a `MainActivity.java`következő két sorát, és másolja a Kulcsszóválasztó csomagot az alkalmazásba. Ha például a "Machine" kulcsszót szeretné használni a Kulcsszóválasztó csomag KWS-Machine. zip fájljában:

   - Másolja a kulcsszó-csomagot a "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\" mappába.
   - Frissítse a `MainActivity.java`t a kulcsszóval és a csomag nevével:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Frissítse a következő sorokat, amelyek tartalmazzák a mikrofon tömb geometriájának beállításait:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   Ez a táblázat a támogatott értékeket sorolja fel:

   | Változó | Jelentés | Elérhető értékek |
   | -------- | ------- | ---------------- |
   | `DeviceGeometry` | Fizikai MIC-konfiguráció | Körkörös fejlesztői csomag esetén: `Circular6+1` |
   |          |         | Lineáris fejlesztői csomag esetén: `Linear4` |
   | `SelectedGeometry` | Szoftveres MIC-konfiguráció | Az összes mikrofont használó körkörös fejlesztői csomag esetében: `Circular6+1` |
   |          |         | Négy mikrofont használó körkörös fejlesztői csomag esetén: `Circular3+1` |
   |          |         | Az összes mikrofont használó lineáris fejlesztői csomag esetében: `Linear4` |
   |          |         | Két mikrofont használó lineáris fejlesztői csomag esetén: `Linear2` |

1. Az alkalmazás létrehozásához a **Futtatás** menüben válassza az alkalmazás **futtatása**lehetőséget. Megjelenik a **központi telepítési cél kiválasztása** párbeszédpanel.

1. Válassza ki az eszközt, majd kattintson **az OK** gombra az alkalmazás telepítéséhez az eszközön.

   ![Telepítési cél kiválasztása párbeszédpanel](media/speech-devices-sdk/qsg-7.png)

1. Elindul a Speech Devices SDK példa alkalmazás, amely a következő lehetőségeket jeleníti meg:

   ![Példa a beszédfelismerési eszközök SDK-alkalmazására és lehetőségeire](media/speech-devices-sdk/qsg-8.png)

1. Próbálja ki az új beszélgetés átiratának bemutatóját. A "kezdési munkamenet" megkezdése. Alapértelmezés szerint mindenki a vendég. Ha azonban a résztvevő hangaláírásai vannak, akkor az eszközön lévő `/video/participants.properties`ba helyezhetők. A hangaláírás létrehozásához tekintse meg a beszélgetések átírása [(SDK) című témakört](how-to-use-conversation-transcription-service.md).

   ![Bemutató beszélgetés átirata alkalmazás](media/speech-devices-sdk/qsg-15.png)

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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A kibocsátási megjegyzések áttekintése](devices-sdk-release-notes.md)
