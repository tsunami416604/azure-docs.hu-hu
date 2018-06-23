---
title: Ismerkedés a beszédfelismerés eszközök SDK-val |} Microsoft Docs
description: Előfeltételek, akkor az első lépések a beszédfelismerés eszközök SDK-val utasításait.
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/18/2018
ms.author: v-jerkin
ms.openlocfilehash: 6cc5ff1c532d67c48beac1a2a10d034f5d9d7501
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349719"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>Ismerkedés a beszédfelismerés eszközök SDK-val

A cikkből megtudhatja, hogyan lehet beállítani a fejlesztési számítógépen és a Beszédfelismerés eszköz szoftverfejlesztői készlet adattárházzal történő, a beszédfelismerés eszközök SDK használatával beszéd-kompatibilis eszközök. Akkor lesz majd hozza létre, és egy mintaalkalmazást az eszközre telepíteni. 

A mintaalkalmazás forráskódja megtalálható a beszédfelismerés eszközök SDK-val és is [elérhető a Githubon](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Előfeltételek

A beszédfelismerés eszközök SDK-val fejlesztési megkezdése előtt gyűjtse össze az adatokat és a szoftverek szükségesek.

* Szerezzen be egy szoftverfejlesztői készlet [a Roobo](http://ddk.roobo.com/). Kits érhetők el a lineáris vagy kör alakú mikrofon tömb konfigurációival; ki kell választania az igényeinek megfelelő azt.

    |Fejlesztői csomag konfigurálása|Beszélőfelismerési helye|
    |-----------------------------|------------|
    |Körkörös|Tetszőleges irányba a eszközről|
    |Lineáris|Az eszköz előtt|

* A beszédfelismerés eszközök SDK, beleértve az Android mintaalkalmazást, a beszédfelismerés eszközök SDK-ból a legújabb verzió beszerzéséhez [töltse le a hely](https://shares.datatransfer.microsoft.com/). Bontsa ki a ZIP-fájlt egy helyi mappát (például `C:\SDSDK`).

* Telepítés [Android Studio](https://developer.android.com/studio/) és [Vysor](http://vysor.io/download/) a számítógépen.

* Szerezzen be egy beszéd szolgáltatás [előfizetés kulcs](get-started.md). A 30 napos ingyenes próbaverziójának beszerzése, vagy az Azure irányítópultról kulcs beszerzése.

* Ha szeretné használni a beszédfelismerés szolgáltatás leképezési felismerés, előfizetni a [nyelvi ismertetése szolgáltatás](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) (LUIS) és [előfizetés-kulcs beszerzése](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/azureibizasubscription). 

    Ön [hozzon létre egy egyszerű LUIS modell](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/) vagy a minta LUIS modell `LUIS-example.json`, érhető el a beszédfelismerés eszközök SDK [töltse le a hely](https://shares.datatransfer.microsoft.com/). Töltse fel a modell JSON-fájl számára a [LUIS portal](https://www.lui.ai/applications) kattintva **importálási új alkalmazás** és a JSON-fájl kiválasztása.

## <a name="set-up-the-development-kit"></a>A csomag beállítása

1. Csatlakoztassa a szoftverfejlesztői készlet tápegység. A zöld power kijelző kell bonyolít le a felső board alatt.

1. A csomag kapcsolódni a számítógéphez egy USB-kábel mini.

    ![Csatlakozás a fejlesztői csomag](media/speech-devices-sdk/qsg-1.jpg)

1. A szoftverfejlesztői készlet megfelelően elhelyezés.

    |Fejlesztői csomag konfigurálása|Tájolás|
    |-----------------------------|------------|
    |Körkörös|Függőleges, és a felső határ irányuló mikrofonok|
    |Lineáris|Az oldalon, a felénk (lásd alább) mikrofonok|

    ![lineáris fejlesztői kit tájolását](media/speech-devices-sdk/qsg-2.jpg)

1. Telepítse a tanúsítványokat és az ébresztési word (kulcsszó) tábla fájlt, és beállítja az engedélyeket a hangeszköz. Írja be a következő parancsokat a parancsot.

    > [!NOTE]
    > Ezek a parancsok használata a Android hibakeresési híd `adb.exe`, amely az Android Studio telepítés része. Ez az eszköz található `C:\Users\[user name]\AppData\Local\Android\Sdk\platform-tools`. Előfordulhat, hogy ez a könyvtár hozzáadása a elérési útját több kényelmes meghívása `adb`. Ellenkező esetben meg kell adnia a teljes elérési útja az Ön `adb.exe` minden parancs, amely hívja meg a `adb`.

    ```
    adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/ 
    adb shell
    cd /data/ 
    chmod 777 roobo_setup.sh
    ./roobo_setup.sh
    exit
    ```

    > [!TIP]
    > A számítógép mikrofon és hangalapú elnémíthatja. Így biztosítható, a szoftverfejlesztői készlet mikrofonok dolgozik, és nem véletlenül indul el az eszközt az hang a számítógépről.
    
1.  Indítsa el a Vysor a számítógépen.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  Az eszköz alatt kell szerepelnie "Egy eszköz kiválasztása". Kattintson a **nézet** gomb látható. 
 
1.  A vezeték nélküli hálózathoz csatlakozni kattintva **beállítások**, majd **WLAN**.

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)
 
## <a name="run-a-sample-application"></a>A mintaalkalmazás futtatása

A Roobo tesztek futtatása, és a fejlesztői csomag telepítő érvényesítéséhez, hozza létre, és a mintaalkalmazás telepítése.

1.  Indítsa el az Android Studio.

1.  Válassza ki az Android Studio projekt megnyitása.

    ![Android studio – létező projekt megnyitása](media/speech-devices-sdk/qsg-5.png)
 
1.  Keresse meg a `C:\SDSDK\Android-Sample-Release\example`, majd kattintson a **OK** a példaprojekt megnyitásához.
 
1.  A beszédfelismerés előfizetés kulcs hozzáadása a forráskódot. Ha azt szeretné, leképezési felismerés kipróbálására, is hozzáadhat a [nyelvi ismertetése szolgáltatás](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) előfizetés kulcs és az alkalmazás azonosítóját. 

    A kulcsok és a következő sorokat a forrásfájl kerül az alkalmazásadatok `MainActivity.java`.

    ```java
    // Subscription
    private static final String SpeechSubscriptionKey = "[your speech key]";
    private static final String SpeechRegion = "westus";
    private static final String LuisSubscriptionKey = "[your LUIS key]";
    private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
    private static final String LuisAppId = "[your LUIS app id]"
    ```

1. Az alapértelmezett ébresztési word (kulcsszó) a "Számítógép."  Ha kívánja, próbálkozzon egy másik megadott ébresztési szavak, "Machine" és "Segéd." Alternatív szavak erőforrás fájlokat a "kulcsszó" mappában található a beszédfelismerés eszközök SDK-ban. Például `C:\SDSDK\Android-Sample-Release\keyword\Computer` a "Számítógép" használt fájlokat tartalmaz

    Is lehet [hozzon létre egy egyéni ébresztési word](speech-devices-sdk-create-kws.md).

    A kívánt ébresztési word telepítése:
 
    * Hozzon létre egy `keyword` \data\ mappája a parancssori ablakban a következő parancsok futtatásával az eszközön.

        ```
        adb shell
        cd /data
        mkdir keyword
        exit
        ```

    * Másolja a fájlokat `kws.table`, `kws_g.fst`, `kws_k.fst`, és `words_kw.txt`) az eszköz \data\keyword\ mappába. O-parancsablakban futtassa a következő parancsokat.

        ```
        adb push C:\SDSDK\Android-Sample-Release\keyword\kws.table /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_g.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_k.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\words_kw.txt /data/keyword
        ```
    
    * Ezeket a fájlokat a mintaalkalmazás hivatkozik. A következő sorokat található `MainActivity.java`. Győződjön meg arról, hogy a megadott kulcsszó használata egy és, hogy az elérési út mutat a `kws.table` fájlt, amely leküldeni az eszközre.
        
        ```java
        private static final String Keyword = "Computer";
        private static final String KeywordModel = "/data/keyword/kws.table";
        ```

        > [!NOTE]
        > Az Ön saját kódját, használhatja a `kws.table` fájl kulcsszó modell példány létrehozása, és indítsa el az alábbiak szerint használatát.
        >
        > ```java
        > KeywordRecognitionModel km = KeywordRecognitionModel.fromFile(KeywordModel);
        > final Task<?> task = reco.startKeywordRecognitionAsync(km);
        > ```

1.  A következő sorokat a mikrofon tömb geometriai beállításokat tartalmazó frissíteni.

    ```java
    private static final String DeviceGeometry = "Circular6+1";
    private static final String SelectedGeometry = "Circular6+1";
    ```

    |Változó|Jelentés|Rendelkezésre álló értékeket|
    |--------|-------|----------------|
    |`DeviceGeometry`|Fizikai mic konfiguráció|`Circular6+1` Körkörös fejlesztői csomag|
    ||| `Linear4` lineáris fejlesztői csomag|
    |`SelectedGeometry`|Szoftver mic konfiguráció|`Circular6+1` Körkörös fejlesztői csomag összes mics használatával|
    |||`Circular3+1` Körkörös fejlesztői csomag négy mics használatával|
    |||`Linear4` az összes mics használó lineáris fejlesztői csomag|
    |||`Linear2` két mics használó lineáris fejlesztői csomag|


1.  Válassza ki az alkalmazás összeállítása **"alkalmazás" futtatása** a Futtatás menüből. A telepítés helyének kiválasztása párbeszédpanel jelenik meg. Válassza ki az eszközét, és kattintson a **OK** az eszközre az alkalmazás telepítéséhez.

    ![Válassza ki a központi telepítési cél](media/speech-devices-sdk/qsg-7.png)
 
1.  A beszédfelismerés eszközök SDK mintaalkalmazás kezdődik, az itt látható beállítások megjelenítése.

    ![Beszéd eszköz mintaalkalmazás](media/speech-devices-sdk/qsg-8.png)

1. Play azt!

## <a name="troubleshooting"></a>Hibaelhárítás

Ha tanúsítványhibák a beszédfelismerés szolgáltatás használatakor, győződjön meg arról, hogy az eszköz a helyes dátum és idő.

Fejlesztési kapcsolatos további információkért lásd: Roobo tartozó [fejlesztői útmutató](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

Roobo olyan eszköz, amely rögzíti az összes hang flash memória, amely segítséget nyújthat hang problémák elhárításához nyújt. Az eszköz egy verzióját minden development kit konfigurációs biztosított. Válassza ki az eszközét, [a Roobo hely](http://ddk.roobo.com/), majd kattintson a **ROOBO eszközök** hivatkozás az oldal alján.
