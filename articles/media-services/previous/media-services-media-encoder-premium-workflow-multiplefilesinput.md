---
title: Több bemeneti fájl és összetevő-tulajdonság a Prémium kódolóval - Azure | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogyan használhatja a setRuntimeProperties segítségével több bemeneti fájlt, és hogyan továbbíthat egyéni adatokat a Media Encoder Premium Workflow médiaprocesszornak.
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
ms.openlocfilehash: 27bdf82d4515678e28eadf07fe325860fe5df063
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250997"
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Több bemeneti fájl és összetevő-tulajdonságok használata a Prémium kódolóval
## <a name="overview"></a>Áttekintés
Vannak olyan esetek, amikor testre kell szabnia az összetevő tulajdonságait, meg kell adnia a Clip List XML-tartalmat, vagy több bemeneti fájlt kell küldenie, amikor feladatot küld el a **Media Encoder Premium Workflow** médiaprocesszorral. Néhány példa:

* Szöveg átfedése a videón, és a szöveges érték (például az aktuális dátum) beállítása futásidőben minden bemeneti videóhoz.
* A kliplista XML-jének testreszabása (egy vagy több forrásfájl megadásához, vágással vagy anélkül stb.).
* Egy emblémakép átfedése a bemeneti videón, miközben a videó kódolt.
* Több audio nyelvi kódolás.

Ha tudatni szeretné a **Media Encoder Premium Workflow vállalattal,** hogy a feladat létrehozásakor vagy több bemeneti fájl küldésekor módosítja a munkafolyamat egyes tulajdonságait, a **setRuntimeProperties** és/vagy **az átkódforrást**tartalmazó konfigurációs karakterláncot kell használnia. Ez a témakör bemutatja, hogyan kell használni őket.

## <a name="configuration-string-syntax"></a>Konfigurációs karakterlánc szintaxisa
A kódolási feladatban beállított konfigurációs karakterlánc a következőképpen néz ki:

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

A következő a C# kód, amely beolvassa az XML-konfigurációt egy fájlból, frissíti a megfelelő videofájlnévvel, és átadja azt a feladatnak:

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
### <a name="property-with-a-simple-value"></a>Egyszerű értékű tulajdonság
Bizonyos esetekben hasznos lehet egy összetevőtulajdonságot a Media Encoder Premium Workflow által végrehajtandó munkafolyamat-fájllal együtt testreszabni.

Tegyük fel, hogy olyan munkafolyamatot tervezett, amely átfedi a szöveget a videókon, és a szöveget (például az aktuális dátumot) futásidőben kell beállítani. Ehhez küldje el a kódolási feladat átfedési összetevőjének szövegtulajdonságának új értékeként beállítandó szöveget. Ezzel a mechanizmussal módosíthatja a munkafolyamat egy összetevőjének egyéb tulajdonságait (például az átfedés pozícióját vagy színét, az AVC kódoló bitrátáját stb.).

**A setRuntimeProperties** tulajdonság felülírása a munkafolyamat összetevőiben.

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

### <a name="property-with-an-xml-value"></a>XML-értékű tulajdonság
Ha olyan tulajdonságot szeretne beállítani, amely XML-értéket `<![CDATA[ and ]]>`vár, a beágyazása a használatával.

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
> Ügyeljen arra, hogy ne tegye `<![CDATA[`a kocsi vissza közvetlenül azután .

### <a name="propertypath-value"></a>propertyPath érték
Az előző példákban a propertyPath a "/Media File Input/filename" vagy a "/inactiveTimeout" vagy a "clipListXml" volt.
Ez általában az összetevő neve, majd a tulajdonság neve. Az elérési út több vagy kevesebb szinttel is rendelkezhet, például "/primarySourceFile" (mivel a tulajdonság a munkafolyamat gyökere) vagy "/Video Processing/Graphic Overlay/Opacitás" (mivel az átfedés egy csoportban van).    

Az elérési út és a tulajdonság nevének ellenőrzéséhez használja a közvetlenül az egyes tulajdonok mellett található műveletgombot. Kattintson erre a műveletgombra, és válassza **a Szerkesztés**lehetőséget. Ez megmutatja a tulajdonság tényleges nevét, és közvetlenül felette a névteret.

![Művelet/Szerkesztés](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Tulajdonság](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Több bemeneti fájl
A **Media Encoder Premium Workflow** számára elküldött minden feladathoz két eszközre van szükség:

* Az első egy *munkafolyamat-eszköz,* amely munkafolyamat-fájlt tartalmaz. A munkafolyamat-fájlokat a [Munkafolyamat-tervező](media-services-workflow-designer.md)segítségével tervezheti meg.
* A második egy *Media Asset,* amely tartalmazza a kódolni kívánt médiafájlt(fájlokat).

Ha több médiafájlt küld a **Media Encoder Premium Workflow** kódolónak, a következő korlátozások érvényesek:

* Az összes médiafájlnak ugyanabban a *médiaeszközben*kell lennie. Több médiaeszköz használata nem támogatott.
* Ebben a Media Asset eszközben be kell állítania az elsődleges fájlt (ideális esetben ez az a fő videofájl, amelyet a kódolónak fel kell dolgoznia).
* A **setRuntimeProperties** és/vagy **transcodeSource** elemet tartalmazó konfigurációs adatokat át kell adni a processzornak.
  * **A setRuntimeProperties** tulajdonság a fájlnév tulajdonság vagy a munkafolyamat összetevőinek más tulajdonságának felülbírálására szolgál.
  * **A transcodeSource** a Clip List XML-tartalom megadására szolgál.

Kapcsolatok a munkafolyamatban:

* Ha egy vagy több Media File Input összetevőt használ, és a **setRuntimeProperties tulajdonságot** kívánja használni a fájlnév megadásához, akkor ne csatlakoztassa hozzá az elsődleges fájlösszetevő-pint. Győződjön meg arról, hogy nincs kapcsolat az elsődleges fájlobjektum és a médiafájl bemenete(i) között.
* Ha a Clip List XML és egy médiaforrás-összetevőt szeretné használni, akkor mindkettőt összekapcsolhatja.

![Nincs kapcsolat az elsődleges forrásfájl és a médiafájl bemenete között](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Ha a setRuntimeProperties tulajdonság beállításához a setRuntimeProperties tulajdonságot használja, az elsődleges fájl nem kapcsolódik a Media File Input összetevő(k)hez.*

![Kapcsolat a kliplista XML-fájljáról a kliplista forrásával](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*A clip list XML-t médiaforráshoz csatlakoztathatja, és használhatja a transcodeSource-t.*

### <a name="clip-list-xml-customization"></a>A Kliplista XML-testreszabása
A kliplista XML-jét futásidőben a **transcodeSource** használatával adhatja meg a munkafolyamatban. Ehhez a kliplista XML-pinjét csatlakoztatni kell a munkafolyamat Médiaforrás-összetevőjéhez.

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

Ha meg szeretné adni a /primarySourceFile kapcsolót, hogy ezt a tulajdonságot a "Kifejezések" használatával nevezze el a kimeneti fájloknak, akkor javasoljuk, hogy a Clip List XML-t a /primarySourceFile tulajdonság *után* adja át tulajdonságként, hogy a kliplistát felülbírálja az /primarySourceFile beállítás.

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

További képkockapontos vágással:

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

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>1. példa: Egy kép átfedése a videó tetejére

### <a name="presentation"></a>Megjelenítés
Vegyünk egy példát, amelyben a videó kódolása közben egy emblémaképet szeretne áttenni a bemeneti videóra. Ebben a példában a bemeneti videó neve "Microsoft_HoloLens_Possibilities_816p24.mp4", az embléma neve "logo.png". Hajtsa végre a következő lépéseket:

* Hozzon létre egy munkafolyamat-eszközt a munkafolyamat-fájllal (lásd a következő példát).
* Hozzon létre egy Media Asset, amely két fájlt tartalmaz: MyInputVideo.mp4, mint az elsődleges fájl és MyLogo.png.
* Küldjön egy feladatot a Media Encoder Premium Workflow médiaprocesszornak a fenti bemeneti eszközökkel, és adja meg a következő konfigurációs karakterláncot.

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

A fenti példában a program elküldi a videofájl nevét a Media File Bemeneti összetevőnek és az elsődlegesSourceFile tulajdonságnak. Az emblémafájl nevét a program elküldi egy másik, a grafikus átfedés-összetevőhöz csatlakoztatott médiafájl-bemenetnek.

> [!NOTE]
> A videofájl nevét a rendszer elküldi az elsődlegesSourceFile tulajdonságnak. Ennek az az oka, hogy ezt a tulajdonságot a munkafolyamatban a megfelelő kimeneti fájlnév kifejezések használatával történő létrehozásához használja.

### <a name="step-by-step-workflow-creation"></a>A munkafolyamat részletes létrehozása
Az alábbi lépésekkel két fájlt tartalmazó munkafolyamatot hozhat létre: egy videót és egy képet. Ez fedi a képet a videó tetején.

Nyissa meg **a Munkafolyamat-tervezőt,** és válassza **a Fájlúj** > **munkaterület** > **átkódolási tervét.**

Az új munkafolyamat három elemet mutat:

* Elsődleges forrásfájl
* Kliplista XML-fájlja
* Kimeneti fájl/eszköz  

![Új kódolási munkafolyamat](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Új kódolási munkafolyamat*

A bemeneti médiafájl elfogadásához kezdje a Media File Input összetevő hozzáadásával. Ha összetevőt szeretne hozzáadni a munkafolyamathoz, keresse meg azt a Tárház keresőmezőjében, és húzza a kívánt bejegyzést a tervező ablaktáblájára.

Ezután adja hozzá a munkafolyamat tervezéséhez használt videofájlt. Ehhez kattintson a Háttér ablaktáblára a Munkafolyamat-tervezőben, és keresse meg az Elsődleges forrásfájl tulajdonságot a jobb oldali tulajdonságpanelen. Kattintson a mappa ikonra, és válassza ki a megfelelő videofájlt.

![Elsődleges fájlforrás](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Elsődleges fájlforrás*

Ezután adja meg a videofájlt a Media File Input összetevőben.   

![Médiafájl bemeneti forrása](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Médiafájl bemeneti forrása*

Amint ez megtörtént, a Media File Input összetevő megvizsgálja a fájlt, és feltölti a kimeneti csapokat, hogy tükrözze a vizsgált fájlt.

A következő lépés egy "Video Data Type Updater" hozzáadása a Rec.709 színterének megadásához. Adjon hozzá egy "Video Format Converter"-t, amely adatelrendezés/elrendezés típusra van állítva = Konfigurálható sík. Ez átalakítja a videostreamet olyan formátumra, amely az átfedési összetevő forrásaként vehető igénybe.

![video adattípus-frissítő és formátumátalakító](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Video adattípus-frissítő és formátumátalakító*

![Elrendezés típusa = Konfigurálható sík](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Az elrendezés típusa konfigurálható sík*

Ezután adjon hozzá egy Video Overlay összetevőt, és csatlakoztassa a (tömörítetlen) videotűt a médiafájl bemenetének (tömörítetlen) videotűjéhez.

Adjon hozzá egy másik médiafájl-bemenetet (az emblémafájl betöltéséhez), kattintson erre az összetevőre, és nevezze át "Media File Input Logo" névre, és jelöljön ki egy képet (például .png fájlt) a fájltulajdonságban. Csatlakoztassa a Tömörítetlen képtűt az átfedés tömörítetlen képtűjéhez.

![Összetevő- és képfájl-forrás átfedése](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Összetevő- és képfájl-forrás átfedése*

Ha módosítani szeretné az embléma pozícióját a videón (például a videó bal felső sarkától számított 10 százalékon szeretné elhelyezni), törölje a jelet a "Kézi bevitel" jelölőnégyzetből. Ezt azért teheti meg, mert médiafájl-bemenettel biztosítja az emblémafájlt az átfedési összetevő számára.

![Átfedéspozíció](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Átfedéspozíció*

Ha a videostreamet H.264-re szeretné kódolni, adja hozzá az AVC videokódoló és az AAC kódoló összetevőit a tervezőfelülethez. Csatlakoztassa a csapokat.
Állítsa be az AAC kódolót, és válassza a Hangformátum konvertálása/készlet : 2.0 (L, R) lehetőséget.

![Hang- és videokódolók](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Hang- és videokódolók*

Most adja hozzá az **ISO Mpeg-4 Multiplexer** és file output komponenseket, és csatlakoztassa a csapokat az ábrán látható módon. **File Output**

![MP4 multiplexer és fájlkimenet](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 multiplexer és fájlkimenet*

Be kell állítania a kimeneti fájl nevét. Kattintson a **Fájlkimenet** összetevőre, és szerkesztse a fájl kifejezését:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Fájlkimenet neve](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Fájlkimenet neve*

A munkafolyamathelyi futtatásával ellenőrizheti, hogy megfelelően fut-e.

Miután befejeződik, futtathatja az Azure Media Servicesben.

Először készítsen elő egy eszközt az Azure Media Services-ben két fájllal: a videofájllal és az emblémával. Ezt a .NET vagy a REST API használatával teheti meg. Ezt az Azure Portalon vagy az [Azure Media Services Explorerben](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) is megteheti.

Ez az oktatóanyag bemutatja, hogyan kezelheti az eszközöket az AMSE segítségével. Egy eszközhöz kétféleképpen adhat hozzá fájlokat:

* Hozzon létre egy helyi mappát, másolja a benne lévő két fájlt, és húzza a mappát az **Eszköz** lapra.
* Töltse fel a videofájlt eszközként, jelenítse meg az eszközadatait, lépjen a fájlok lapra, és töltsön fel egy további fájlt (emblémát).

> [!NOTE]
> Győződjön meg arról, hogy elsődleges fájlt állít be az eszközben (a fő videofájlban).

![Eszközfájlok az AMSE-ben](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Eszközfájlok az AMSE-ben*

Válassza ki az eszközt, és válassza ki, hogy kódolja azt prémium kódolóval. Töltse fel a munkafolyamatot, és jelölje ki.

Kattintson a gombra az adatok processzornak való átadására, és adja hozzá a következő XML-t a futásidejű tulajdonságok beállításához:

![Prémium kódoló az AMSE-ben](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Prémium kódoló az AMSE-ben*

Ezután illessze be a következő XML-adatokat. Meg kell adnia a videofájl nevét a Médiafájl bemenetéhez és az elsődlegesForrásfájlhoz is. Adja meg az embléma fájlnevének nevét is.

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

Ha a .NET SDK használatával hozza létre és futtatja a feladatot, akkor ezeket az XML-adatokat kell átadni konfigurációs karakterláncként.

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

Miután a feladat befejeződött, az MP4 fájl a kimeneti eszköz megjeleníti az átfedés!

![Fedvény a videón](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Fedvény a videón*

A mintamunkafolyamatot letöltheti a [GitHubról.](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/)

## <a name="example-2--multiple-audio-language-encoding"></a>2. példa : Több hangnyelvi kódolás

A [GitHubon](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding)több hangnyelvi kódolási munkafolyamat is elérhető.

Ez a mappa tartalmaz egy minta munkafolyamat, amely lehet használni kódolni egy MXF fájlt egy multi MP4 fájlok eszköz több hangsávot.

Ez a munkafolyamat feltételezi, hogy az MXF fájl egy hangsávot tartalmaz; a további hangsávokat külön hangfájlként kell átadni (WAV vagy MP4...).

A kódoláshoz kövesse az alábbi lépéseket:

* Hozzon létre egy Media Services-eszközt az MXF-fájllal és a hangfájlokkal (0–18 hangfájl).
* Győződjön meg arról, hogy az MXF-fájl elsődleges fájlként van beállítva.
* Hozzon létre egy feladatot és egy feladatot a prémium munkafolyamat-kódoló processzorral. Használja a megadott munkafolyamatot (MultiMP4-1080p-19audio-v1.workflow).
* Adja át a setruntime.xml adatokat a feladatnak (ha az Azure Media Services Explorert használja, használja az "XML-adatok átadása a munkafolyamatba" gombot).
  * Frissítse az XML-adatokat a megfelelő fájlnevek és nyelvek címkéinek megadásához.
  * A munkafolyamat audio-1-ről audio 18-ra nevű audio-összetevőket tartalmaz.
  * Az RFC5646 a nyelvi címke esetében támogatott.

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

* A kódolt eszköz többnyelvű hangsávot fog tartalmazni, és ezeknek a sávoknak kiválaszthatónak kell lenniük az Azure Media Player ben.

## <a name="see-also"></a>Lásd még
* [Prémium kódolás bemutatása az Azure Media Services szolgáltatásban](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [A prémium szintű kódolás használata az Azure Media Servicesszolgáltatásban](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Igény szerinti tartalom kódolása az Azure Media Services szolgáltatással](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Media Encoder Prémium munkafolyamat-formátumok és kodekek](media-services-premium-workflow-encoder-formats.md)
* [Munkafolyamat-fájlok mintaa](https://github.com/Azure/azure-media-services-samples)
* [Azure Media Services Explorer eszköz](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
