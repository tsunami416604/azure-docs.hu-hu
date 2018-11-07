---
title: Bemeneti fájlok és a prémium szintű Encoder - Azure összetevő tulajdonságai |} A Microsoft Docs
description: Ez a témakör ismerteti, hogyan setRuntimeProperties használatával több bemeneti fájlokat használ, és egyéni átadni a Media Encoder Premium munkafolyamat médiafeldolgozót.
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
ms.date: 10/30/2018
ms.author: xpouyat;anilmur;juliako
ms.openlocfilehash: 31bd3d54b085922de0ec822731eae79fe9a9943b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262097"
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Több bemeneti fájlok és összetevő tulajdonságai párbeszédpanel használata a prémium szintű kódoló
## <a name="overview"></a>Áttekintés
Vannak helyzetek, amelyekben szüksége lehet testreszabása összetevő tulajdonságai párbeszédpanel, adja meg a klip lista XML-tartalom, vagy több bemeneti fájlok küldése a feladat elküldésekor a **Media Encoder Premium munkafolyamat** médiafeldolgozót. Néhány példa:

* Szöveget rajzolhat, amelyek a videó és a szöveges érték (például az aktuális dátum) beállítása az egyes bemeneti videó futásidőben.
* Testreszabás, a klipek lista XML (adjon meg egy vagy több forrásfájlokat, vagy anélkül levágási stb.).
* Átfedő a a bemeneti videó kattintanak, a videó kódolása közben.
* Több többnyelvű hang kódolása.

Ahhoz, hogy a **Media Encoder Premium munkafolyamat** tudja, hogy módosítja a munkafolyamat egyes tulajdonságait a feladat létrehozásakor vagy több bemeneti fájlok küldése, szükség van egy konfigurációs karakterlánc, amely tartalmazza  **setRuntimeProperties** és/vagy **transcodeSource**. Ez a témakör bemutatja, hogyan használhatja őket.

## <a name="configuration-string-syntax"></a>Konfigurációs karakterlánc szintaxisát
A konfigurációs karakterlánc a kódolási feladat beállításához használja az XML-dokumentum, a következőhöz hasonló:

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

Az alábbiakban a C# kódot, amely az XML-konfiguráció olvas be egy fájlt, frissítse a megfelelő videó filename, és átadja a feladat egy feladatot:

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

## <a name="customizing-component-properties"></a>Összetevő tulajdonságai párbeszédpanel testreszabása
### <a name="property-with-a-simple-value"></a>Vlastnost egyszerű
Bizonyos esetekben hasznos lehet egy összetevő tulajdonság, és a munkafolyamat-fájlt, amelyet szeretne hajtható végre, a Media Encoder Premium munkafolyamat testreszabásához.

Tegyük fel, készült munkafolyamat lefedi a szöveget a videóit, és a szöveg (például az aktuális dátum) kellene állítani futásidőben. Ehhez a szöveg közötti értékre lehet beállítani az átmeneti területre összetevő a text tulajdonság új értéke a kódolási feladat küldése. Ez a mechanizmus segítségével többi tulajdonság-összetevők (például pozícióját vagy az átmeneti területre színét, az átviteli sebesség a AVC kódoló stb.) a munkafolyamatban.

**setRuntimeProperties** bírálja felül az összetevőket a munkafolyamat-tulajdonság segítségével.

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

### <a name="property-with-an-xml-value"></a>Egy XML tartalmazó tulajdonság
Olyan tulajdonságot, amely vár egy XML-érték beállításához magába foglalja a `<![CDATA[ and ]]>`.

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
> Ügyeljen arra, hogy nem kocsivissza visszatérési put után csupán `<![CDATA[`.

### <a name="propertypath-value"></a>propertyPath érték
A propertyPath volt az előző példák "médiafájl- / bemeneti/filename" vagy "/ inactiveTimeout" vagy "clipListXml".
Ez az általános, az összetevő neve, majd a tulajdonság nevét. Az elérési út rendelkezhet több vagy kevesebb szintek, például "/ primarySourceFile" (mivel a tulajdonság értéke a munkafolyamat gyökérmappájában) vagy "/ Video feldolgozási/kép területre/átlátszatlanság" (mert az átmeneti területre egy található).    

Ellenőrizze az elérési út és a tulajdonság nevét, használja a műveletgombként jelenik, amely közvetlenül mellett minden egyes tulajdonság. A művelet gombra kattintva, és válassza ki **szerkesztése**. Ez megjeleníti a tényleges nevét, a tulajdonságot, és azonnal felett, a névteret.

![A művelet/szerkesztése](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Tulajdonság](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Több bemeneti fájllal
Minden egyes elküldött feladatot a **Media Encoder Premium munkafolyamat** két eszközre van szükség:

* Az első egy egy *munkafolyamat eszköz* , amely tartalmaz egy munkafolyamatfájlt. Munkafolyamat-fájlok használatával is tervezhet a [munkafolyamat-tervezővel](media-services-workflow-designer.md).
* A második érték van egy *médiaobjektum* , amely tartalmazza a kódolni kívánt médiafájl.

Ha több médiafájlok küld a **Media Encoder Premium munkafolyamat** kódoló, a következő korlátozások vonatkoznak:

* A médiafájlokat kell lennie az egyazon *médiaobjektum*. Több médiatartalmak használata nem támogatott.
* Az elsődleges fájlnak meg kell adni a médiaobjektum (ideális esetben ez a fő fájl, amely a kódoló felkéri, hogy feldolgozása).
* Szükséges konfigurációs adatokat, amely tartalmazza a **setRuntimeProperties** és/vagy **transcodeSource** elem a processzor.
  * **setRuntimeProperties** felülbírálása, a filename tulajdonság, vagy a munkafolyamat az összetevők egy másik tulajdonságot használja.
  * **transcodeSource** klip lista XML-tartalomban meghatározására szolgál.

A munkafolyamat kapcsolatok:

* Ha egy vagy több adathordozó bemeneti fájllal összetevőt használja, és szeretné használni **setRuntimeProperties** , adja meg a fájlnevet, majd ne csatlakozzon az elsődleges fájl összetevő PIN-kód őket. Győződjön meg arról, hogy ez nem az elsődleges fájl objektum és az adathordozó fájl Input(s) között.
* Ha inkább klip lista XML és a egy médiaforrást az összetevőt, majd együtt csatlakozzon is.

![Nincs kapcsolat elsődleges forrásfájlból Media bemeneti fájllal](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Ez nem az elsődleges fájlnak a Media bemeneti fájllal összetevőknek, a filename tulajdonság beállítása setRuntimeProperties használatakor.*

![A lista forrása a klip XML klip listából kapcsolat](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Forrás-adathordozó klip lista XML csatlakozni, és transcodeSource használja.*

### <a name="clip-list-xml-customization"></a>Grafikus lista XML testreszabása
Megadhatja a klipek lista XML futásidőben a munkafolyamat használatával **transcodeSource** konfigurációjában XML-karakterláncot. Ehhez a klip lista XML PIN-kódot kell csatlakoztatni a munkafolyamatban a forrás-adathordozó összetevő.

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

Adja meg ezt a tulajdonságot a kimeneti fájlok nevét a "Kifejezés" a használandó /primarySourceFile szeretne, akkor azt javasoljuk, a klipek lista XML passing tulajdonságként *után* a /primarySourceFile tulajdonságot használja, a klipek ne kelljen Lista bírálható felül a /primarySourceFile beállításban.

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

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>1. példa: Átfedi a képet a videó felett

### <a name="presentation"></a>Bemutató
Fontolja meg egy példa, amelyben meg szeretné átfedő a a bemeneti videó kattintanak, a videó kódolása közben. Ebben a példában a bemeneti videó neve "Microsoft_HoloLens_Possibilities_816p24.mp4", és az embléma "logo.png" nevű. Az alábbi lépéseket is végre kell hajtania:

* Egy munkafolyamat-objektum létrehozása a munkafolyamat fájlja (lásd az alábbi példát).
* Hozzon létre egy médiaobjektum, amely tartalmazza a két fájlt: az elsődleges fájlnak és MyLogo.png MyInputVideo.mp4.
* Feladat küldése a Media Encoder Premium munkafolyamat médiafeldolgozót. a fenti bemeneti eszközökkel, és adja meg a következő konfigurációs karakterlánc.

Konfiguráció:

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

A fenti példában a fájl nevét az adathordozó bemeneti fájllal összetevő és a primarySourceFile tulajdonság zajlik. Az embléma fájl neve elküldi egy másik Media bemeneti fájllal, amely a grafikus területre összetevő csatlakozik.

> [!NOTE]
> A videó fájlnév elküldi a primarySourceFile tulajdonság. Ennek az az oka, hogy ezt a tulajdonságot használja a munkafolyamat létrehozásához a megfelelő kimeneti fájl nevét kifejezések, például használatával.

### <a name="step-by-step-workflow-creation"></a>Részletes munkafolyamat létrehozása
A következő lépéseket egy munkafolyamatot, amely két fájlt fogadja bemeneti adatként létrehozni: egy videót és a egy képet. Ez lesz átfedő a kép a videó felett.

Nyissa meg **munkafolyamat-tervezővel** válassza **fájl** > **új munkaterület** > **átkódolása tervezet**.

Az új munkafolyamat három elemet jeleníti meg:

* Elsődleges forrásfájl
* Klip lista XML
* Kimeneti fájl vagy eszköz  

![Új kódolási munkafolyamatokat.](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Új kódolási munkafolyamatokat.*

Annak érdekében, hogy fogadja a bemeneti fájlt, első lépésként adja hozzá az adathordozó fájl bemeneti összetevő. Összetevő hozzáadása a munkafolyamat, a tárház keresőmezőbe keresése, és a kívánt bejegyzést a Tervező ablaktáblán húzzon.

Ezután adja hozzá a videofájl, a munkafolyamat tervezéséhez használható. Ehhez a munkafolyamat-tervezővel háttér ablaktábláján kattintson, és keresse meg a elsődleges forrásfájl tulajdonság a jobb oldali tulajdonság panelen. A mappa ikonra, és válassza ki a megfelelő videó fájlt.

![Elsődleges fájl forrása](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Elsődleges fájl forrása*

Ezután adja meg a fájl, a Media bemeneti fájllal összetevő.   

![Médiafájl bemeneti forrás](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Médiafájl bemeneti forrás*

Amint ez megtörtént, az adathordozó bemeneti fájllal összetevőt a fájl vizsgálata, és töltse fel a kimeneti PIN-kódok, hogy a fájlt, amely azt megvizsgálni.

A következő lépés, hogy adjon hozzá egy "a videó adatok típusa Updater" Rec.709 szín lemezterületre megadásához. Adjon hozzá egy "a videó konverter", amely adatokat elrendezés/elrendezés típus = Síkvetület konfigurálható. A video-adatfolyamot konvertálja az átmeneti területre összetevő forrásaként elvégezhet formátumra.

![videó adatok típusa Updater és konverter](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*videó adatok típusa Updater és konverter*

![Elrendezés típus = Síkvetület konfigurálható](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Elrendezés típus Síkvetület konfigurálható:*

Ezután a videó átfedő összetevő hozzáadása, és csatlakozzon a (tömörítetlen) videó PIN-kód az adathordozó bemeneti fájllal (tömörítetlen) videó PIN kódját.

Adjon hozzá egy másik Media bemeneti fájllal (a betölteni az embléma fájlt), ez az összetevő kattintson, és nevezze át a "Media fájl bemeneti embléma", majd válassza ki a kép (.png fájlnak a példában) a fájl tulajdonságban. Csatlakozzon a tömörítetlen kép PIN-kód az átmeneti területre a tömörítetlen kép PIN-kódot.

![Összetevő- és képfájlok területre fájl forrása](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Összetevő- és képfájlok területre fájl forrása*

Ha az embléma videó pozícióját a módosítani kívánt (például érdemes: 10 % engedményt a videó bal felső sarokban helyezze el), törölje a jelet az "Manuális bevitel" jelölőnégyzetből. Ezt megteheti, mert egy adathordozó bemeneti fájllal, adja meg az átmeneti területre összetevő az embléma fájlt is.

![Felirat pozíciója](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Felirat pozíciója*

A video-adatfolyamot H.264 kódolásához, adja hozzá a AVC Videókódoló és AAC kódoló összetevő a Tervező felületére. Csatlakozás a PIN-kódok.
Állítsa be az AAC-kódoló, és válassza ki a konverziós/készlet hang formátum: 2.0-s (L, R).

![Audio- és kódolók](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Audio- és kódolók*

Most adja hozzá a **ISO Mpeg-4 Multiplexer** és **kimeneti fájl** összetevőket, és kapcsolódjon az PIN-kód látható módon.

![MP4 multiplexer és a kimeneti fájl](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 multiplexer és a kimeneti fájl*

Meg kell állítania a kimeneti fájl nevét. Kattintson a **kimeneti fájl** összetevő és a kifejezést a fájl szerkesztése:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Fájl kimeneti neve](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Fájl kimeneti neve*

Helyileg, hogy ellenőrizze, hogy megfelelően fut-e a munkafolyamat futtatása.

Annak befejeződése után futtathatja az Azure Media Servicesben.

Először készítsen elő egy eszköz két fájlt az Azure Media Services: a videó fájl- és az emblémát. Ehhez a .NET vagy REST API használatával. Azt is teheti az Azure portal használatával, vagy [Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

Ez az oktatóanyag bemutatja, hogyan kezelheti az AMSE eszköz. Fájlok hozzáadása egy eszköz két módja van:

* Hozzon létre egy helyi mappát, másolja a két fájlt, és áthúzása a mappát a **eszköz** fülre.
* Töltse fel a videófájlt eszközként, az eszköz adatainak megjelenítése, a fájlok lapon nyissa meg és töltse fel egy további fájlt (embléma).

> [!NOTE]
> Feltétlenül állítson be egy elsődleges fájl az eszközben (a fő videó fájlt).

![Az AMSE eszköz fájlok](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Az AMSE eszköz fájlok*

Válassza ki a, és válassza ki a kódoláshoz a prémium szintű kódoló. Töltse fel a munkafolyamat, és válassza ki azt.

A gombra kattintva adatokat adnak át a processzor, és adja hozzá a következő XML formátumú futásidejű tulajdonságainak:

![Az AMSE prémium szintű kódoló](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Az AMSE prémium szintű kódoló*

Ezt követően illessze be a következő XML-adatokat. Adja meg a fájl nevét az adathordozó bemeneti fájllal és a primarySourceFile kell. Adja meg túl az emblémát a fájl neve.

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

A .NET SDK használatával hozhat létre és futtathat a feladatütemezés, ha az XML-adatok a konfigurációs karakterláncból átadandó rendelkezik.

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

A feladat befejezése után a MP4-fájlt a kimeneti adategység megjeleníti az átmeneti területre!

![Videó átfedő](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Videó átfedő*

Letöltheti a minta munkafolyamat [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).

## <a name="example-2--multiple-audio-language-encoding"></a>2. példa: Több többnyelvű hang kódolása

Több többnyelvű hang kódolási workfkow érhető el a példa [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding).

Ez a mappa tartalmaz egy munkafolyamat-minta, amelyek segítségével egy többszörös MP4 fájlok eszközhöz több hangsáv MXF fájl kódolása.

Ez a munkafolyamat feltételezi, hogy az MXF-fájl tartalmaz egy hangsávra; a további hangsáv (WAV vagy MP4...) külön hang fájlokként kell átadni.

Kódolás, kövesse ezeket a lépéseket:

* Hozzon létre egy Media Services eszköz az MXF fájl- és a hangot fájlok (0 – 18 hang).
* Győződjön meg arról, hogy az MXF-fájl elsődleges fájl van beállítva.
* Hozzon létre egy feladatot és a egy feladatot, a prémium szintű munkafolyamat kódoló processzor használatával. A megadott munkafolyamat (MultiMP4-1080p-19audio-v1.workflow) használja.
* Setruntime.xml továbbítja azokat a feladat (Ha használja az Azure Media Services Explorer, használja a "XML-adatokat átadni a munkafolyamat" gombot).
  * Frissítse az XML-adatok a megfelelő fájl neve és nyelvek címkék adhatók meg.
  * A munkafolyamat hang 18-ra Audio 1 nevű hang összetevőket tartalmazza.
  * A nyelvi címke RFC5646 támogatott.

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

* A kódolt objektumhoz több nyelv hangsáv fogja tartalmazni, és ezek nyomon követi az Azure Media Player választható kell lennie.

## <a name="see-also"></a>Lásd még
* [Introducing Premium Encoding in Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [Az Azure Media Services prémium szintű Encoding használata](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Az Azure Media Services az igény szerinti tartalomterjesztésről kódolás](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Media Encoder Premium munkafolyamat formátumai és kodekei](media-services-premium-workflow-encoder-formats.md)
* [Minta munkafolyamat-fájlok](https://github.com/Azure/azure-media-services-samples)
* [Az Azure Media Services Explorer eszköz](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
