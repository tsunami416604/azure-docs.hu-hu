---
title: "Több bemeneti fájlok és a prémium szintű kódolás - Azure összetevő tulajdonságai |} Microsoft Docs"
description: "Ez a témakör azt ismerteti, hogyan setRuntimeProperties segítségével több bemeneti fájlt használja, és egyéni adatok továbbítását a Media Encoder prémium munkafolyamat media processzor."
services: media-services
documentationcenter: 
author: xpouyat
manager: cfowler
editor: 
ms.assetid: 7fb35bdd-9891-4401-a65b-ef3cc8190e8a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: xpouyat;anilmur;juliako
ms.openlocfilehash: df1ee5089a0af6ffce1431b658843fcb34a66ce5
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>A prémium szintű kódolás több bemeneti fájlok és összetevő tulajdonságai használja
## <a name="overview"></a>Áttekintés
Forgatókönyv, ahol előfordulhat, hogy testreszabásához szükséges összetevő tulajdonságai, adja meg a klip lista XML-tartalom, vagy több bemeneti fájl küld, ha a feladat elküldése a **Media Encoder prémium munkafolyamat** media processzor. Néhány példa:

* Szöveg felirataként videó-és a szöveges értéket (például az aktuális dátum) beállítása az egyes bemeneti videó futásidőben.
* Testreszabása az klip lista XML-(adjon meg egy vagy több forrásfájlokat, függetlenül a tisztítás, stb.).
* A bemeneti videóhoz a egy embléma felirataként, amíg a videó.
* Több hang nyelvi kódolást.

Ahhoz, hogy a **Media Encoder prémium munkafolyamat** tudja, hogy néhány tulajdonság a munkafolyamat több bemeneti fájlküldésre vagy a feladat létrehozásakor módosítani, kell használni, amely tartalmazza a konfigurációs karakterlánc **setRuntimeProperties** és/vagy **transcodeSource**. Ez a témakör azt ismerteti, hogyan is használhatja őket.

## <a name="configuration-string-syntax"></a>Konfigurációs karakterlánc-formátum:
A konfigurációs karakterláncot a kódolási feladat használja az XML-dokumentum, amely a következőképpen néz ki:

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

A következő egy C#-kódban, amely fájlból olvassa be az XML-konfiguráció, a jobb oldali videó fájlnév frissíti, és átadja a feladatot a feladatok:

```c#
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

## <a name="customizing-component-properties"></a>Összetevő tulajdonságai testreszabása
### <a name="property-with-a-simple-value"></a>Egyszerű értéke
Bizonyos esetekben célszerű egy összetevő tulajdonság, és a munkafolyamat-fájlt, amelyet szeretne hajthatják végre a Media Encoder prémium munkafolyamat testreszabása.

Tegyük fel, akkor a munkafolyamat átfedések szöveg a videók, és a következő futtatási állítható be a szöveget (például az aktuális dátum) kellene. Ehhez a kódolási feladat a állítható be az új értéket az átmeneti területre összetevő text tulajdonságához szöveg küldésével. A mechanizmus segítségével más egy összetevő (például pozícióját vagy az átmeneti területre színe, az átviteli AVC kódoló, stb.) a munkafolyamat tulajdonságainak módosítása.

**setRuntimeProperties** felül a munkafolyamat-összetevők tulajdonság.

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

### <a name="property-with-an-xml-value"></a>Az XML-értéke tulajdonság
Olyan tulajdonságon, amely vár egy XML-érték beállításához foglalják magukban a `<![CDATA[ and ]]>`.

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
> Ügyeljen arra, hogy nem kocsivissza visszatérési put után csak `<![CDATA[`.

### <a name="propertypath-value"></a>a propertyPath érték
A fenti példákban a propertyPath lett "/ Media File bemeneti/fájlnév" vagy "/ inactiveTimeout" vagy "clipListXml".
Ez az általános, az összetevő neve, majd a tulajdonságnevet kell megadni. Az elérési út is van több vagy kevesebb, mint "/ primarySourceFile" (mert a tulajdonság a munkafolyamat gyökerében) vagy "/ videó feldolgozási/kép átfedő/átlátszatlanság" (mivel az átmeneti területre csoportban).    

Ellenőrizze az elérési út és a tulajdonság nevét, használja az akciógombra kattinthat, amely közvetlenül mellett minden egyes tulajdonsága. A művelet gombra kattintva, és válassza ki **szerkesztése**. Ez azt mutatja majd, a tényleges nevét, a tulajdonság, és azonnal felette, a névtér.

![A művelet/szerkesztése](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Tulajdonság](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Több bemeneti fájl
Minden tevékenység elküldött a **Media Encoder prémium munkafolyamat** két eszközök igényel:

* Az első egy egy *munkafolyamat eszköz* , amely a munkafolyamat-fájlt tartalmaz. Munkafolyamat-fájlok segítségével megtervezheti a [munkafolyamat-Tervező](media-services-workflow-designer.md).
* A második érték van egy *Media eszköz* , amely tartalmazza a media (oka) t, amelyet szeretne kódolni.

Ha több médiafájlok küldjük a **Media Encoder prémium munkafolyamat** kódoló, a következő korlátozások vonatkoznak:

* A médiafájlokat kell lennie ugyanazon *Media eszköz*. Több adathordozó eszközök használata nem támogatott.
* Meg kell adnia az elsődleges fájlnak a Media eszköz (ideális esetben azt a fő videofájl, amely a kódoló kapcsolatba kell feldolgozni).
* Szükséges konfigurációs adatok, amely tartalmazza a **setRuntimeProperties** és/vagy **transcodeSource** elemben, amely a processzor.
  * **setRuntimeProperties** felül a filename tulajdonságban vagy az összetevők a munkafolyamat egy másik tulajdonságot.
  * **transcodeSource** klip lista XML-tartalom szolgál.

A munkafolyamat kapcsolatok:

* Ha egy vagy több adathordozó fájl bemeneti összetevőket használnak, és tervezi használni az **setRuntimeProperties** adja meg a fájlnevet, majd nem csatlakozzon az elsődleges fájl összetevő PIN-kód őket. Győződjön meg arról, hogy nincs-e az elsődleges fájl objektum és az adathordozó fájl Input(s) közötti kapcsolat.
* Ha szeretné használni a klip lista XML és egy forrás-adathordozó összetevőt, majd összekapcsolhatja mindkét együtt.

![Nincs kapcsolat elsődleges forrásfájlból Media fájl bemeneti](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Nincs Media fájl bemeneti elemét/elemeit az elsődleges fájlnak a kapcsolat beállítása a filename tulajdonságban setRuntimeProperties használatakor.*

![XML-forráslista levágása klip listából kapcsolat](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Forrás-adathordozó klip lista XML csatlakozni, és transcodeSource használja.*

### <a name="clip-list-xml-customization"></a>Lista XML testreszabási levágása
Megadhatja a klip lista XML futásidőben a munkafolyamatban használatával **transcodeSource** konfigurációjában karakterlánc XML. Ehhez a klip lista XML PIN-kódot kell csatlakoztatni a forrás-adathordozó összetevő a munkafolyamatban.

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

Ha meg szeretné határozni /primarySourceFile használni ezt a tulajdonságot "Kifejezések" használatával, a kimeneti fájlok nevét, akkor javasoljuk, hogy átadja a klip lista XML-tulajdonságként *után* a /primarySourceFile tulajdonság kívánja kerülni a /primarySourceFile beállításával bírálható klip listájában.

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

A további keret pontos tisztítás:

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

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>1. példa: Átfedő fölött a videó kép

### <a name="presentation"></a>Bemutató
Vegye figyelembe például szeretné egy embléma lemezképét a bemeneti videóhoz átfedő, amíg a videó. Ebben a példában a bemeneti videó "Microsoft_HoloLens_Possibilities_816p24.mp4" nevű, és az embléma neve "logo.png". Az alábbi lépéseket kell végrehajtani:

* Hozzon létre egy munkafolyamat-eszközt a a munkafolyamat-fájlt (lásd a következő példát).
* Hozzon létre egy adathordozó eszköz, amely két fájlt tartalmaz: az elsődleges fájl és MyLogo.png MyInputVideo.mp4.
* A feladat elküldése a Media Encoder prémium munkafolyamat media processzor, a fenti bemeneti eszközök, és adja meg a következő konfigurációs karakterlánc.

A konfiguráció:

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

A fenti példában a videó fájl neve elküldi az adathordozó fájl bemeneti összetevő és a primarySourceFile tulajdonság. Az embléma fájl nevét, amely a grafikus átfedő összetevő kapcsolódik egy másik Media fájl bemeneti zajlik.

> [!NOTE]
> A videó fájlnév nem jut hozzá a primarySourceFile tulajdonság. A hiba okát is használják ezt a tulajdonságot a munkafolyamat készítéséhez a megfelelő kimeneti fájl nevét kifejezésekkel, például.

### <a name="step-by-step-workflow-creation"></a>A munkafolyamat részletes létrehozása
Az alábbiakban olyan munkafolyamatot, amely két fájlt fogadja bemeneti adatként létrehozásának lépései: videó és lemezképet. A kép fölött a videó azt fogja átfedő.

Nyissa meg **munkafolyamat-Tervező** válassza **fájl** > **új munkaterület** > **átkódolására tervezetének**.

Az új munkafolyamat három elemeit tartalmazza:

* Elsődleges forrásfájl
* XML klip listázása
* Kimeneti fájl vagy eszköz  

![Új kódolási munkafolyamat](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Új kódolási munkafolyamat*

Ahhoz, hogy fogadja el a bemeneti médiafájl, első lépésként adja hozzá az adathordozó fájl bemeneti összetevőt. Vegyen fel összetevőt a munkafolyamathoz, keresse meg azt a tárház keresési mezőbe, és a kívánt bejegyzés húzza a Tervező ablak.

Ezután adja hozzá a video-fájl a munkafolyamat tervezéséhez. Ehhez az szükséges, kattintson a háttér panelre munkafolyamat-tervezővel, és keresse meg az elsődleges forrásfájl tulajdonság, a jobb oldali tulajdonság panelen. A mappa ikonra, és válassza ki a megfelelő video-fájlt.

![Elsődleges fájl forrás](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Elsődleges fájl forrás*

Ezt követően adja meg a videofájl a Media fájl bemeneti összetevő.   

![A médiafájl bemeneti forrása](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*A médiafájl bemeneti forrása*

Amint ez történik, az adathordozó fájl bemeneti összetevő vizsgálhatja meg a fájl, és a kimeneti PIN-kód megfelelően a fájlt, amely megvizsgálja az feltöltése.

A következő lépés hozzáadása egy "videó adatok típusa Frissítőjének" adhatja meg a Rec.709 szín terület. Adja hozzá a "videó konverter" adatok elrendezés/elrendezés típusra van állítva = síkbeli konfigurálható. Ez a video-adatfolyamot átalakítása átvihető az átmeneti területre összetevő forrásaként formátumú.

![videó típusú frissítési adatok és konverter](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Videó adatok típusa Frissítőjének és konverter*

![Típusának konfigurálható síkbeli =](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Elrendezés típus síkbeli konfigurálható:*

A következő videó átfedő összetevő hozzáadása, és a (tömörítetlen) video PIN-kód csatlakozzon a media fájl bemeneti (tömörítetlen) video PIN kódját.

Egy másik Media fájl bemenete (betölteni az embléma fájlt), vegye fel ezt az összetevőt parancsára, és nevezze át a "Media fájl bemeneti embléma", és jelölje ki a képfájl (például .png fájl) a fájl tulajdonság. A tömörített kép PIN-kód csatlakozni a tömörített kép PIN-kódot az átfedés.

![Összetevő- és képfájlok forrásfájlt átfedő](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Összetevő- és képfájlok forrásfájlt átfedő*

Ha a pozíciót a videót a módosítani kívánt (például érdemes helyezze el a 10 százaléka ki a videó bal felső sarkában), törölje a jelet az "Manuális bevitel" jelölőnégyzetből. Ez végezhető el, mert egy Media fájl bemeneti segítségével adja meg az átmeneti területre összetevőre embléma fájlját.

![Átfedő pozíciója](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Átfedő pozíciója*

A video-adatfolyamot H.264 kódolására, adja hozzá a AVC Videókódoló és AAC kódoló összetevőket a Tervező felületére. Csatlakoztassa a PIN-kód.
Állítsa be a AAC kódoló, és válassza ki a hang formátum konverziós/készlet: 2.0 (L, R).

![Hang- és kódolók](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Hang- és kódolók*

Ezután adja hozzá a **ISO Mpeg-4 Multiplexer** és **kimeneti fájl** összetevők és csatlakoztassa a PIN-kód.

![MP4 multiplexer és a kimeneti fájl](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 multiplexer és a kimeneti fájl*

Állítsa be a kimeneti fájl nevét kell. Kattintson a **kimeneti fájl** összetevő és a kifejezést a fájl szerkesztése:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Kimeneti fájlnév](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Kimeneti fájlnév*

A munkafolyamat győződjön meg arról, hogy megfelelően fut-e el helyileg is futtathatja.

Miután a Befejezés után futtatható Azure Media Services.

Először készítsen egy eszköz két fájlokat az Azure Media Services: a videofájl és az emblémát. Ehhez a .NET vagy a REST API használatával. Is ehhez az Azure portál használatával vagy [Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

Ez az oktatóanyag bemutatja, hogyan AMSE rendelkező eszközök kezeléséhez. Fájlok hozzáadása egy eszköz két módja van:

* Hozzon létre egy helyi mappát, másolja a két fájlt, és húzással a mappát a **eszköz** fülre.
* Eszközként a videofájl feltöltése, az eszköz adatait jeleníti meg, lépjen a fájlok lapra és töltse fel egy további fájlt (embléma).

> [!NOTE]
> Feltétlenül állítson be egy elsődleges fájl az adategységben (a fő videofájl).

![Az AMSE eszköz fájlok](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Az AMSE eszköz fájlok*

Válassza ki az objektumot, és válassza a prémium szintű kódolás kódolása. Töltse fel a munkafolyamat, és válassza ki azt.

A gombra kattintva adatokat adnak át a processzor, és adja hozzá a következő XML futásidejű tulajdonságainak beállításához:

![Prémium szintű kódolás az AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Prémium szintű kódolás az AMSE*

Majd illessze be a következő XML-adataiban. Meg kell adnia a videó fájl nevét az adathordozó fájl bemeneti és a primarySourceFile. Adja meg a fájl neve az embléma túl.

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

Ha a .NET SDK használatával hozzon létre, és futtatni a feladatot, az XML-adatok ki lesznek átadva, a konfigurációs karakterláncból.

```c#
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

A feladat befejezése után a kimeneti adategységen MP4 fájlban megjeleníti az átmeneti területre!

![A videó az átfedő](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*A videó az átfedő*

Letöltheti a minta-munkafolyamat [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).

## <a name="example-2--multiple-audio-language-encoding"></a>2. példa: Több nyelvi hang kódolás

Példa kódolási workfkow érhető el több hang nyelvi [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding).

Ez a mappa tartalmaz egy minta-munkafolyamat egy többszörös MP4 fájlok eszköz több zeneszámok MXF fájlt kódolni használható.

A munkafolyamat azt feltételezi, hogy a MXF fájl tartalmazza-e egy hang követése; a további zeneszámok (WAV vagy MP4...) külön hang fájlokként kell átadni.

Kódolására, tegye a következőket:

* Hozzon létre egy Media Services eszköz a MXF, és a hang fájlokat (0 – 18 hangfájlok).
* Győződjön meg arról, hogy a MXF fájl be van állítva az elsődleges fájl.
* Hozzon létre egy feladatot, és egy feladatot, a prémium szintű munkafolyamat kódolás processzor használatával. A megadott munkafolyamat (MultiMP4-1080p-19audio-v1.workflow) használja.
* A setruntime.xml adatokat adnak át a feladat (Azure Media Services Explorer használatakor használja az "XML-adatok átadása a munkafolyamat" gombot).
  * Frissítse a helyes nevek és nyelvek címkék adhatók meg az XML-adatok.
  * A munkafolyamat hang 18-ra hang 1 nevű hang részből áll.
  * A nyelvcímkének RFC5646 esetén támogatott.

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

* A kódolt objektumhoz több nyelv zeneszámok fogja tartalmazni, és ezek nyomon követi az Azure Media Player választható kell lennie.

## <a name="see-also"></a>Lásd még:
* [Prémium szintű kódolás az Azure Media Services bemutatása](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [Azure Media Services használata a prémium szintű kódolás](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Az Azure Media Services kódolási igény tartalom](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Media Encoder prémium szintű munkafolyamat-formátumok és kodekek](media-services-premium-workflow-encoder-formats.md)
* [Minta munkafolyamat-fájlok](https://github.com/AzureMediaServicesSamples/Encoding-Presets/tree/master/VoD/MediaEncoderPremiumWorkfows)
* [Azure Media Services Explorer eszköz](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
