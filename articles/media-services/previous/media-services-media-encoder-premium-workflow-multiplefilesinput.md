---
title: Több bemeneti fájl és összetevő tulajdonságai prémium szintű kódolóval – Azure | Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan használható a setRuntimeProperties több bemeneti fájl használatára és egyéni adatok továbbítására a Media Encoder Premium Workflow adathordozó-feldolgozónak.
services: media-services
documentationcenter: ''
author: xpouyat
manager: femila
editor: ''
ms.assetid: 7fb35bdd-9891-4401-a65b-ef3cc8190e8a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: xpouyat
ms.reviewer: anilmur;juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 70acbb239df3806437127e41510bfebc1358532a
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89015968"
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Több bemeneti fájl és összetevő tulajdonságainak használata prémium szintű kódolóval
## <a name="overview"></a>Áttekintés
Vannak olyan helyzetek, amikor szükség lehet az összetevő tulajdonságainak testreszabására, a Képlista XML-tartalmának megadására vagy több bemeneti fájl elküldésére, amikor a **Media Encoder Premium workflow** adathordozó-processzorral elküld egy feladatot. Néhány példa:

* Szöveg átfedése a videón, és a szöveges érték (például az aktuális dátum) beállítása futásidőben az egyes bemeneti videóknál.
* A klipek listájának XML-fájljának testreszabása (egy vagy több forrásfájl megadásához, vágással vagy anélkül).
* Embléma képének befedése a bemeneti videón a videó kódolásakor.
* Több hang nyelvi kódolása.

Ahhoz, hogy a **Media Encoder Premium workflow** tudja, hogy a feladat létrehozásakor vagy több bemeneti fájl elküldésekor megváltoztatja a munkafolyamat egyes tulajdonságait, olyan konfigurációs karakterláncot kell használnia, amely **setRuntimeProperties** és/vagy **transcodeSource**tartalmaz. Ez a témakör a használatuk módját ismerteti.

## <a name="configuration-string-syntax"></a>Konfigurációs karakterlánc szintaxisa
A kódolási feladatban beállított konfigurációs karakterlánc egy XML-dokumentumot használ, amely a következőképpen néz ki:

```xml
<?xml version="1.0" encoding="utf-8"?>
<transcodeRequest>
  <transcodeSource>
  </transcodeSource>
  <setRuntimeProperties>
    <property propertyPath="Media File Input/filename" value="VideoFileName.mp4" />
  </setRuntimeProperties>
</transcodeRequest>
```

A következő a C#-kód, amely beolvassa az XML-konfigurációt egy fájlból, a megfelelő videó fájlnevével frissíti azt, és átadja a feladatnak a feladat számára:

```csharp
string premiumConfiguration = ReadAllText(@"D:\home\site\wwwroot\Presets\SetRuntime.xml").Replace("VideoFileName", myVideoFileName);

// Declare a new job.
IJob job = _context.Jobs.Create("Premium Workflow encoding job");

// Get a media processor reference, and pass to it the name of the 
// processor to use for the specific task.
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

// Create a task with the encoding details, using a string preset.
ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                              processor,
                              premiumConfiguration,
                              TaskOptions.None);

// Specify the input assets
task.InputAssets.Add(workflow); // workflow asset
task.InputAssets.Add(video); // video asset with multiple files

// Add an output asset to contain the results of the job. 
// This output is specified as AssetCreationOptions.None, which 
// means the output asset is not encrypted. 
task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);
```

## <a name="customizing-component-properties"></a>Összetevő tulajdonságainak testreszabása
### <a name="property-with-a-simple-value"></a>Tulajdonság egyszerű értékkel
Bizonyos esetekben hasznos lehet egy összetevő-tulajdonságot a Media Encoder Premium Workflow által végrehajtandó munkafolyamat-fájllal együtt testreszabni.

Tegyük fel, hogy olyan munkafolyamatot alakított ki, amely átfedésben van egy szöveggel a videókon, és a szöveg (például az aktuális dátum) futtatását futásidőben kell beállítani. Ezt úgy teheti meg, hogy a kódolási feladatból elküldi az átfedési összetevő Text (szöveg) tulajdonságának új értékként megadott szövegét. Ezzel a mechanizmussal módosíthatja a munkafolyamat egyik összetevőjének egyéb tulajdonságait (például az átfedés pozícióját vagy színét, az AVC-kódoló bitrátáját stb.).

a **setRuntimeProperties** a munkafolyamat összetevőiben lévő tulajdonságok felülbírálására szolgál.

Példa:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
      <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
  </setRuntimeProperties>
</transcodeRequest>
```

### <a name="property-with-an-xml-value"></a>XML-értéket tartalmazó tulajdonság
Egy XML-értéket megadó tulajdonság beállításához használja a következőt: `<![CDATA[ and ]]>` .

Példa:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

> [!NOTE]
> Győződjön meg arról, hogy a kocsit csak utána helyezi vissza `<![CDATA[` .

### <a name="propertypath-value"></a>Propertypath szintaxisnak érték
Az előző példákban a Propertypath szintaxisnak "/Media file input/filename" vagy "/inactiveTimeout" vagy "clipListXml" volt.
Ez általában az összetevő neve, majd a tulajdonság neve. Az elérési útnak több vagy kevesebb szintje lehet, például a "/primarySourceFile" (mivel a tulajdonság a munkafolyamat gyökerén található) vagy "/video Processing/Graphic overlay/opacitás" (mivel az átfedés egy csoportban található).    

Az elérési út és a tulajdonság nevének vizsgálatához használja az egyes tulajdonságok mellett azonnal megjelenő művelet gombot. Ehhez kattintson a művelet gombra, és válassza a **Szerkesztés**lehetőséget. Ekkor megjelenik a tulajdonság tényleges neve, és közvetlenül felette a névtér.

![Művelet/szerkesztés](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Tulajdonság](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Több bemeneti fájl
A **Media Encoder Premium workflow** elküldött minden feladat két eszközt igényel:

* Az első egy munkafolyamat-fájlt tartalmazó *munkafolyamat-eszköz* . Munkafolyamat-fájlokat a [munkafolyamat-tervező](media-services-workflow-designer.md)használatával tervezhet.
* A második egy olyan *média-eszköz* , amely tartalmazza a kódolni kívánt médiafájl (oka) t.

Ha több médiafájlt küld a **Media Encoder Premium workflow** kódolónak, a következő korlátozások érvényesek:

* Az összes médiafájlnak ugyanabban a *média-eszközben*kell lennie. A több adathordozós eszközök használata nem támogatott.
* Az elsődleges fájlt be kell állítania ebben az adathordozó-eszközben (ideális esetben ez a fő videofájl, amelyet a kódolónak fel kell dolgoznia).
* A **setRuntimeProperties** és/vagy **transcodeSource** elemet tartalmazó konfigurációs adategységeket át kell adni a processzornak.
  * a **setRuntimeProperties** a filename tulajdonság vagy más tulajdonság felülbírálására szolgál a munkafolyamat összetevőiben.
  * a **transcodeSource** a KLIPEK XML-tartalmának megadására szolgál.

Kapcsolatok a munkafolyamatban:

* Ha egy vagy több médiafájl-beviteli összetevőt használ, és azt tervezi, hogy a **setRuntimeProperties** használatával adja meg a fájlnevet, akkor ne kapcsolódjon hozzájuk az elsődleges fájl-összetevő PIN-kódjához. Győződjön meg arról, hogy nincs kapcsolat az elsődleges fájl objektum és a médiafájl-bemenet (ek) között.
* Ha inkább a klipek XML-és egy adathordozó-forrás összetevőjét szeretné használni, akkor mindkettőt összekapcsolhatja.

![Nincs az elsődleges forrásfájl és a médiafájl bemenete közötti kapcsolódás](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Ha a setRuntimeProperties használatával beállítja a filename tulajdonságot, az elsődleges fájl nem kapcsolódik a médiafájl bemeneti összetevőjéhez.*

![A klipek XML-fájlból a listára való kapcsolódási lista forrása](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*A legördülő lista XML-fájlját összekapcsolhatjuk a média forrásával, és transcodeSource is használhat.*

### <a name="clip-list-xml-customization"></a>Klipek listájának XML-testreszabása
A munkafolyamatban megadhatja a klipek XML-fájlját futásidőben a **transcodeSource** használatával a konfigurációs karakterlánc XML-ben. Ehhez a klip-lista XML-PIN-kódjának csatlakoztatva kell lennie a munkafolyamat Media Source összetevőjéhez.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <transcodeSource>
      <clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
      </clipList>
    </transcodeSource>
    <setRuntimeProperties>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Ha meg szeretné adni a/primarySourceFile, hogy ez a tulajdonság a "kifejezések" használatával nevezze el a kimeneti fájlokat, akkor azt javasoljuk, hogy a klip lista XML-fájlját a/primarySourceFile tulajdonság *utáni* tulajdonságként adja át, hogy ne legyen felülbírálva a/primarySourceFile beállítás.

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

További keretek – pontos vágás:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>1. példa: képek átfedése a videó fölé

### <a name="presentation"></a>Megjelenítés
Vegyünk egy példát arra, hogy a videó kódolásakor a bemeneti videóban lévő embléma képét szeretné átfedőként használni. Ebben a példában a bemeneti videó neve "Microsoft_HoloLens_Possibilities_816p24.mp4", és a logó neve "logo.png". A következő lépéseket kell végrehajtania:

* Hozzon létre egy munkafolyamat-objektumot a munkafolyamat-fájllal (lásd a következő példát).
* Hozzon létre egy olyan adathordozó-objektumot, amely két fájlt tartalmaz: MyInputVideo.mp4 elsődleges fájlként, és MyLogo.png.
* Küldjön egy feladatot a Media Encoder Premium Workflow adathordozó-feldolgozónak a fenti bemeneti eszközökkel, és adja meg a következő konfigurációs karakterláncot.

Konfigurálás:

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

A fenti példában a videofájl neve a Media file input összetevőbe és a primarySourceFile tulajdonságba lesz küldve. Az embléma nevét egy másik médiafájl-bemenetre kell elküldeni, amely a grafikus overlay összetevőhöz csatlakozik.

> [!NOTE]
> A rendszer elküldi a videofájl nevét a primarySourceFile tulajdonságnak. Ennek az az oka, hogy ezt a tulajdonságot használja a munkafolyamatban a kimeneti fájl helyes nevének a kifejezésekkel való létrehozásához, például:.

### <a name="step-by-step-workflow-creation"></a>Lépésről lépésre a munkafolyamat létrehozása
A következő lépésekkel hozhat létre egy olyan munkafolyamatot, amely két fájlt bemenetként fogad: egy videót és egy képet. A képet a videó tetején fogja átfedőként ábrázolni.

Nyissa meg a **munkafolyamat-tervezőt** , és válassza a **fájl**  >  **Új munkaterület**  >  **átkódoló tervezet**lehetőséget.

Az új munkafolyamat három elemet mutat be:

* Elsődleges forrásfájl
* Klipek XML-listája
* Kimeneti fájl/eszköz  

![Új kódolási munkafolyamat](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Új kódolási munkafolyamat*

A bemeneti médiafájl elfogadásához Kezdje a Media file input összetevő hozzáadásával. Ha hozzá szeretne adni egy összetevőt a munkafolyamathoz, keresse meg az adattár keresési mezőjében, és húzza a kívánt bejegyzést a tervező ablaktáblára.

Ezután adja hozzá a munkafolyamat tervezéséhez használni kívánt videofájl-fájlt. Ehhez kattintson a Munkafolyamat-tervező háttér paneljére, és keresse meg az elsődleges forrásfájl tulajdonságot a jobb oldali Tulajdonságok ablaktáblán. Kattintson a mappa ikonra, és válassza ki a megfelelő videofájl-fájlt.

![Elsődleges fájl forrása](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Elsődleges fájl forrása*

Ezután adja meg a videofájlokat a médiafájl bemeneti összetevőjében.   

![Médiafájl bemeneti forrása](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Médiafájl bemeneti forrása*

Amint ez megtörtént, a médiafájl-bemeneti összetevő megvizsgálja a fájlt, és feltölti a kimeneti PIN-kód tartalmát, hogy tükrözze a megvizsgálandó fájlt.

A következő lépés egy "video adattípus-frissítő" hozzáadása a REC. 709 színtér megadásához. Adja hozzá a "video Format Converter" értéket, amely adatelrendezési/elrendezési típus = konfigurálható síkbeli értékre van beállítva. Ezzel a videó streamet olyan formátumra alakítja át, amely az átfedési összetevő forrásaként is elvégezhető.

![videó adattípus-Updater és Format Converter](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Videó adattípus-Updater és Format Converter*

![Elrendezés típusa = konfigurálható síkbeli](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Az elrendezés típusa konfigurálható síkbeli*

Ezután adjon hozzá egy video overlay-összetevőt, és kapcsolja a (tömörítetlen) videó PIN-kódját a médiafájl bemenetének (tömörítetlen) videós PIN-kódjához.

Adjon hozzá egy másik médiafájl-bevitelt (az embléma betöltéséhez), kattintson erre az összetevőre, és nevezze át a "Media file input logo" névre, és válasszon egy képet (például egy. png fájlt) a fájl tulajdonságban. Kapcsolja össze a kibontott képpin-kódot az átfedés tömörítetlen képpin-kódjával.

![Átfedési összetevő és képfájl forrása](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Átfedési összetevő és képfájl forrása*

Ha módosítani szeretné az embléma pozícióját a videón (például előfordulhat, hogy a videó bal felső sarkában 10%-ot szeretne elhelyezni), törölje a jelet a "manuális bevitel" jelölőnégyzetből. Ezt azért teheti meg, mert médiafájl-bevitelt használ, hogy megadja az embléma-fájlt az átfedésben lévő összetevőnek.

![Átfedési pozíció](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Átfedési pozíció*

A videó stream H. 264-re való kódolásához adja hozzá az AVC-videó Kódolóját és az AAC-kódoló összetevőit a tervező felületéhez. A PIN-kódok összekapcsolásához.
Állítsa be az AAC-kódolót, és válassza a hangformátum átalakítása/előre beállított: 2,0 (L, R) lehetőséget.

![Hang-és video-kódolók](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Hang-és video-kódolók*

Most adja hozzá az **ISO MPEG-4 multiplexer** és a **fájl kimeneti** összetevőit, és az ábrán látható módon kapcsolja össze a PIN-kódokat.

![MP4 multiplexer és fájl kimenete](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 multiplexer és fájl kimenete*

Be kell állítania a kimeneti fájl nevét. Kattintson a **fájl kimeneti** összetevőjére, és szerkessze a kifejezést a fájlhoz:

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4`

![Fájl kimeneti neve](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Fájl kimeneti neve*

A munkafolyamatot helyileg is futtathatja annak ellenőrzéséhez, hogy megfelelően fut-e.

A befejeződése után Azure Media Services futtathatja.

Először készítsen elő egy adategységet Azure Media Services két fájllal: a videofájl és az embléma. Ezt a .NET vagy a REST API használatával teheti meg. Ezt a Azure Portal vagy a [Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) használatával is elvégezheti.

Ebből az oktatóanyagból megtudhatja, hogyan kezelheti az eszközöket a AMSE használatával. Két módon adhat hozzá fájlokat egy eszközhöz:

* Hozzon létre egy helyi mappát, másolja ki a két fájlt, majd húzza a mappát az **eszköz** lapra.
* Töltse fel a videofájl eszközként való feltöltését, jelenítse meg az eszköz adatait, lépjen a fájlok lapra, és töltsön fel egy további fájlt (embléma).

> [!NOTE]
> Ügyeljen arra, hogy az adategységben (a fő videofájl) elsődleges fájl legyen megadva.

![Adategységek fájljai a AMSE-ben](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Adategységek fájljai a AMSE-ben*

Válassza ki az eszközt, és válassza a prémium szintű kódolóval való kódolást. Töltse fel a munkafolyamatot, és válassza ki.

Kattintson a gombra az adatfeldolgozáshoz, és adja hozzá a következő XML-kódot a futásidejű tulajdonságok beállításához:

![Prémium szintű kódoló a AMSE-ben](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Prémium szintű kódoló a AMSE-ben*

Ezután illessze be a következő XML-fájlokat. Meg kell adnia a videofájl nevét a médiafájl-bevitelhez és a primarySourceFile. Adja meg a embléma fájlnevét is.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*

Ha a .NET SDK használatával hozza létre és futtatja a feladatot, az XML-adatoknak a konfigurációs sztringként kell átadni.

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

A feladatok befejezése után a kimeneti eszköz MP4-fájlja megjeleníti az átfedést!

![Átfedés a videón](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Átfedés a videón*

A minta munkafolyamatot letöltheti a [githubról](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).

## <a name="example-2--multiple-audio-language-encoding"></a>2. példa: több hang nyelvi kódolása

A [githubon](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding)több hang-nyelvi kódolási munkafolyamat is elérhető.

Ez a mappa egy minta munkafolyamatot tartalmaz, amely egy MXF-fájlnak több hangsávot tartalmazó több MP4-fájlba való kódolására használható.

Ez a munkafolyamat feltételezi, hogy a MXF-fájl egyetlen hangsávot tartalmaz; a további hangsávokat külön hangfájlként kell megadni (WAV vagy MP4...).

A kódoláshoz kövesse az alábbi lépéseket:

* Hozzon létre egy Media Services objektumot a MXF-fájllal és a hangfájlokkal (0 – 18 hangfájl).
* Győződjön meg arról, hogy a MXF-fájl elsődleges fájlként van beállítva.
* Hozzon létre egy feladatot és egy feladatot a prémium szintű munkafolyamat-kódoló processzor használatával. Használja a megadott munkafolyamatot (MultiMP4-1080p-19audio-v1. workflow).
* Adja át a setruntime.xml adatoknak a feladatnak (ha Azure Media Services Explorert használ, használja az "XML-adatok átadása a munkafolyamatnak" gombot).
  * Az XML-adatok frissítésével adja meg a megfelelő fájlneveket és nyelveket.
  * A munkafolyamathoz audio-összetevők vannak elnevezve: hang 1 – audio 18.
  * A RFC5646 a Language címke esetében támogatott.

```xml
<?xml version="1.0" encoding="utf-16"?>
<transcodeRequest>
  <setRuntimeProperties>
    <property propertyPath="Media File Input Video/filename" value="MainVideo.mxf" />
    <property propertyPath="Language/language_code" value="en" />
    <property propertyPath="/primarySourceFile" value="MainVideo.mxf" />
    <property propertyPath="Audio 1/Media File Input/filename" value="french-audio.wav" />
    <property propertyPath="Audio 1/Language/language_code" value="fr" />
    <property propertyPath="Audio 2/Media File Input/filename" value="german-audio.wav" />
    <property propertyPath="Audio 2/Language/language_code" value="de" />
    <property propertyPath="Audio 3/Media File Input/filename" value="japanese-audio.wav" />
    <property propertyPath="Audio 3/Language/language_code" value="ja" />
  </setRuntimeProperties>
</transcodeRequest>
```

* A kódolt eszköz többnyelvű hangsávokat tartalmaz, és ezek a számok Azure Media Playerban választhatók ki.

## <a name="see-also"></a>Lásd még
* [Prémium szintű kódolás bemutatása Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [Prémium szintű kódolás használata a Azure Media Servicesban](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Igény szerinti tartalom kódolása Azure Media Services](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Formátumok és kodekek Media Encoder Premium Workflow](media-services-premium-workflow-encoder-formats.md)
* [Munkafolyamat-fájlok mintája](https://github.com/Azure/azure-media-services-samples)
* [Azure Media Services Explorer eszköz](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
