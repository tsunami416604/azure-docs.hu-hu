---
title: A beszédfelismerés eszközök SDK használatának első lépései
description: Előfeltételek és a Speech eszközök SDK – első lépések vonatkozó utasításokat.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/18/2018
ms.author: v-jerkin
ms.openlocfilehash: 266315a731eec8a2c0ab0a880ce9e1db58331184
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283136"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>A beszédfelismerés eszközök SDK használatának első lépései

Ez a cikk bemutatja, hogyan konfigurálhatja a fejlesztési számítógép és a Speech eszköz development Kitet, a beszéd eszköz SDK-val speech-kompatibilis eszközök fejlesztéséhez. Ezután hozhat létre és üzembe helyezünk egy mintaalkalmazást az eszközön. 

A mintaalkalmazás forráskódja megtalálható a Speech Devices SDK-val, és egyben [elérhető a Githubon](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Előfeltételek

Fejlesztés előtt a Speech eszköz SDK-val, gyűjtsön információkat és szoftverek szükségesek.

* A fejlesztői csomag beszerzése [a Roobo](http://ddk.roobo.com/). Készletek érhetők el lineáris vagy kör alakú mikrofon a tömb konfigurációk Válassza ki az igényeinek megfelelőt.

    |Development kit konfigurálása|Hangszóró helye|
    |-----------------------------|------------|
    |Kör alakú|Bármelyik irányba, az eszközről|
    |Lineáris|Az eszköz előtt|

* A beszéd Devices SDK-val, beleértve az Android mintaalkalmazást, a beszéd eszközök SDK legújabb verziójának beszerzéséhez [letöltőwebhelyéről](https://shares.datatransfer.microsoft.com/). Bontsa ki a ZIP-fájlt egy helyi mappába (például `C:\SDSDK`).

* Telepítés [Android Studio](https://developer.android.com/studio/) és [Vysor](http://vysor.io/download/) a számítógépen.

* Szerezzen be egy beszédszolgáltatás [előfizetési kulcs](get-started.md). Egy 30 napos ingyenes próbaverzió beszerzése, vagy szerezze be egy kulcsot az Azure-irányítópulton.

* Ha szeretné használni a Speech service szándékának felismerése, iratkozzon fel a [hangfelismerési szolgáltatás](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) (LUIS) és [egy előfizetési kulcsot beszerezni](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/azureibizasubscription). 

    Előfordulhat, hogy [hozzon létre egy egyszerű LUIS-modellnek](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/) vagy használja a LUIS-modellnek mintát `LUIS-example.json`, a beszéd eszköz SDK-ból elérhető [letöltőwebhelyéről](https://shares.datatransfer.microsoft.com/). Töltse fel a modell JSON-fájlt is a [LUIS portál](https://www.luis.ai/home) kattintva **importálása új alkalmazás** és a JSON-fájl kiválasztása.

## <a name="set-up-the-development-kit"></a>A fejlesztői készlet beállítása

1. Beépülő modul a fejlesztői készlet tápegység. Egy zöld power kijelző kell helyezzen üzembe az első tábla alatt.

1. A csomag kapcsolódni a számítógéphez egy USB-kábelen keresztül mini.

    ![Csatlakozás a fejlesztői csomag](media/speech-devices-sdk/qsg-1.jpg)

1. Elhelyezés megfelelően a development Kitet.

    |Development kit konfigurálása|Tájolás|
    |-----------------------------|------------|
    |Kör alakú|Mintha a mikrofonok a felső határ irányuló|
    |Lineáris|Az oldalán a mikrofonok felénk (lásd alább)|

    ![lineáris dev csomag tájolása](media/speech-devices-sdk/qsg-2.jpg)

1. Telepítse a tanúsítványokat és a hálózati ébresztési word (kulcsszó) tábla fájlt, és állítsa be az engedélyeket a hangeszköz. Írja be a következő parancsokat egy parancssori ablakban.

    > [!NOTE]
    > Ezek a parancsok használata az Android-hibakeresési híd `adb.exe`, amely az Android Studio telepítést része. Ez az eszköz található `C:\Users\[user name]\AppData\Local\Android\Sdk\platform-tools`. Előfordulhat, hogy adja hozzá ezt a könyvtárat az elérési úthoz, hogy kényelmesebbé meghívásához `adb`. Ellenkező esetben meg kell adnia a teljes elérési útja a telepített `adb.exe` minden parancshoz, amely meghívja a `adb`.

    ```
    adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/ 
    adb shell
    cd /data/ 
    chmod 777 roobo_setup.sh
    ./roobo_setup.sh
    exit
    ```

    > [!TIP]
    > A számítógép mikrofon és beszélőfelismerési vypnutí. Így biztos lehet dolgozik a fejlesztői készlet mikrofonok, és véletlenül nem aktiválja a hangot a számítógépről az eszközre.
    
1.  Indítsa el a Vysor a számítógépen.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  Az eszköz szerepelnie kell "Eszköz kiválasztása". Kattintson a **nézet** gomb mellett. 
 
1.  A vezeték nélküli hálózathoz csatlakozni kattintva **beállítások**, majd **WLAN**.

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)
 
## <a name="run-a-sample-application"></a>A mintaalkalmazás futtatása

Futtassa a Roobo teszteket, és az development kit konfigurációjának ellenőrzése, hozza létre és telepítse a mintaalkalmazást.

1.  Indítsa el az Android Studio.

1.  Válasszon egy meglévő Android Studio projekt megnyitása.

    ![Android studio – meglévő projekt megnyitása](media/speech-devices-sdk/qsg-5.png)
 
1.  Keresse meg a `C:\SDSDK\Android-Sample-Release\example`, majd kattintson a **OK** a példaprojekt megnyitásához.
 
1.  A beszédfelismerés előfizetési kulcs hozzáadása a forráskódban. Ha szeretné kipróbálni szándékának felismerése, is hozzáadhat a [hangfelismerési szolgáltatás](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) előfizetési kulcs és az alkalmazás azonosítóját. 

    A kulcsok és alkalmazással kapcsolatos információk a következő sorokat a forrásfájl `MainActivity.java`.

    ```java
    // Subscription
    private static final String SpeechSubscriptionKey = "[your speech key]";
    private static final String SpeechRegion = "westus";
    private static final String LuisSubscriptionKey = "[your LUIS key]";
    private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
    private static final String LuisAppId = "[your LUIS app id]"
    ```

1. Az alapértelmezett ébresztési szó (kulcsszó) a "Számítógép."  Ha szeretné, előfordulhat, hogy próbálkozzon egy másik megadott ébresztési szavak, "Gép" és "Assistant". Az erőforrásfájlokat, az alternatív szavak "kulcsszó" mappában található a Speech eszköz SDK-ban. Ha például `C:\SDSDK\Android-Sample-Release\keyword\Computer` tartalmazza a "Számítógép." használt fájlok

    Lehetősége van [hozzon létre egy egyéni ébresztési szó](speech-devices-sdk-create-kws.md).

    A kívánt ébresztési szó telepítése:
 
    * Hozzon létre egy `keyword` \data\ mappája a parancssori ablakban az alábbi parancsok futtatásával az eszközön.

        ```
        adb shell
        cd /data
        mkdir keyword
        exit
        ```

    * Másolja a fájlokat `kws.table`, `kws_g.fst`, `kws_k.fst`, és `words_kw.txt`) az eszköz \data\keyword\ mappába. O parancssori ablakban futtassa a következő parancsokat.

        ```
        adb push C:\SDSDK\Android-Sample-Release\keyword\kws.table /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_g.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_k.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\words_kw.txt /data/keyword
        ```
    
    * Ezeket a fájlokat a mintaalkalmazásban hivatkozhat. Keresse meg a következő sorokat `MainActivity.java`. Győződjön meg arról, hogy a megadott kulcsszót használ egy és, hogy az elérési út mutat, a `kws.table` fájlt, amely leküldeni az eszközre.
        
        ```java
        private static final String Keyword = "Computer";
        private static final String KeywordModel = "/data/keyword/kws.table";
        ```

        > [!NOTE]
        > A saját kódját is használhatja a `kws.table` fájl kulcsszó modell példányt hoz létre, és indítsa el a következő felismerése.
        >
        > ```java
        > KeywordRecognitionModel km = KeywordRecognitionModel.fromFile(KeywordModel);
        > final Task<?> task = reco.startKeywordRecognitionAsync(km);
        > ```

1.  Frissítés a következő sorokat a mikrofon tömb geometriai beállításokat tartalmazó.

    ```java
    private static final String DeviceGeometry = "Circular6+1";
    private static final String SelectedGeometry = "Circular6+1";
    ```

    |Változó|Jelentés|Elérhető értékek|
    |--------|-------|----------------|
    |`DeviceGeometry`|Fizikai mic-konfiguráció|`Circular6+1` a kör alakú dev csomag|
    ||| `Linear4` lineáris dev csomag|
    |`SelectedGeometry`|Szoftver mic-konfiguráció|`Circular6+1` a kör alakú dev csomag összes megkérhetném használatával|
    |||`Circular3+1` a négy megkérhetném használatával körkörös dev csomag|
    |||`Linear4` az összes megkérhetném használatával lineáris dev csomag|
    |||`Linear2` a két megkérhetném használatával lineáris dev csomag|


1.  Hozza létre az alkalmazást kiválasztásával **"alkalmazás" futtatása** a Futtatás menüből. A központi telepítési cél kiválasztása párbeszédpanel jelenik meg. Válassza ki az eszközét, és kattintson a **OK** az eszközön az alkalmazás telepítéséhez.

    ![telepítési cél kiválasztása](media/speech-devices-sdk/qsg-7.png)
 
1.  A beszédfelismerés Devices SDK-val mintaalkalmazás elindul, az itt látható lehetőségek megjelenítése.

    ![Beszéd eszköz mintaalkalmazás](media/speech-devices-sdk/qsg-8.png)

1. Játsszon egy kicsit azt!

## <a name="troubleshooting"></a>Hibaelhárítás

Ha tanúsítványhibák lép a Speech szolgáltatással, győződjön meg arról, a helyes dátum és idő van az eszközön.

Fejlesztési kapcsolatos további információkért lásd: Roobo a [– fejlesztési útmutató](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

Roobo olyan eszköz, amely rögzíti a memória, amely segíthet a hibaelhárítást a hang Flash összes hang biztosít. Az eszköz egy verzióját minden egyes development kit konfiguráció biztosítunk. Válassza ki az eszközét, [a Roobo hely](http://ddk.roobo.com/), majd kattintson a **ROOBO eszközök** hivatkozásra a lap alján.
