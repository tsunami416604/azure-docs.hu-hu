---
title: Figyelés és naplózás – Azure
description: Ez a cikk áttekintést nyújt az élő videók elemzéséről IoT Edge figyelésről és naplózásról.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: ef00517fc61ac532bdd99c1e887dfd93d56a8c4f
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89567554"
---
# <a name="monitoring-and-logging"></a>Monitorozás és naplózás

Ebből a cikkből megtudhatja, hogyan fogadhat eseményeket az élő videó-elemzésből IoT Edge modulban a távoli figyeléshez. 

Azt is megismerheti, hogyan szabályozhatja a modul által generált naplókat.

## <a name="taxonomy-of-events"></a>Az események osztályozása

A IoT Edge élő videó-elemzések eseményeket bocsátanak ki vagy telemetria az alábbi besorolásnak megfelelően.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="Az események osztályozása":::

* Működés: a felhasználó által végrehajtott műveletek részeként vagy egy [adathordozó-gráf](media-graph-concept.md)végrehajtása során generált események.
   
   * Kötet: várhatóan alacsony (néhány percenként, vagy akár alacsonyabb arányban).
   * Példák:

      Rögzítés elindítva (alább), rögzítés leállítva
      
      ```
      {
        "body": {
          "outputType": "assetName",
          "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
          "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
          "eventTime": "2020-05-12T23:33:10.392Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Diagnosztika: olyan események, amelyek segítenek diagnosztizálni a problémákat és/vagy a teljesítménnyel kapcsolatos problémákat.

   * Kötet: lehet magas (percenként többször is).
   * Példák:
   
      Az RTSP- [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) kapcsolatos információk (alább) vagy a bejövő videóban található hézagok.

      ```
      {
        "body": {
          "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
          "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
          "eventTime": "2020-05-12T23:33:04.077Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Elemzés: a videó-elemzés részeként generált események.

   * Kötet: lehet magas (több alkalommal percenként vagy gyakrabban).
   * Példák:
      
      A rendszer észlelte a mozgást (alább), a következtetés eredményét.

   ```      
   {
     "body": {
       "timestamp": 143039375044290,
       "inferences": [
         {
           "type": "motion",
           "motion": {
             "box": {
               "l": 0.48954,
               "t": 0.140741,
               "w": 0.075,
               "h": 0.058824
             }
           }
         }
       ]
     },
     "applicationProperties": {
       "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
       "subject": "/graphInstances/Sample-Graph-2/processors/md",
       "eventType": "Microsoft.Media.Graph.Analytics.Inference",
       "eventTime": "2020-05-12T23:33:09.381Z",
       "dataVersion": "1.0"
     }
   }
   ```

A modul által kibocsátott eseményeket a rendszer a [IoT Edge hubhoz](../../iot-edge/iot-edge-runtime.md#iot-edge-hub)továbbítja, és innen más célhelyekre is átirányítható. 

### <a name="timestamps-in-analytic-events"></a>Az analitikus események időbélyegei

A fentiekben leírtaknak megfelelően a videó-elemzés részeként generált események időbélyegzővel vannak társítva. Ha a gráf topológiájának részeként [rögzítette az élő videót](video-recording-concept.md) , akkor ez az időbélyegző segít megkeresni, hogy a rögzített videó hol található az adott esemény bekövetkezésekor. A következő irányelvek azt ismertetik, hogyan képezhető le egy elemzési esemény időbélyege az [Azure Media Service-eszközbe](terminology.md#asset)rögzített videó idővonalára.

Először bontsa ki az `eventTime` értéket. Használja ezt az értéket egy [időtartomány-szűrőben](playback-recordings-how-to.md#time-range-filters) a rögzítés megfelelő részének lekéréséhez. Előfordulhat például, hogy olyan videót szeretne beolvasni, amely 30 másodpercet indít el az előtt `eventTime` , és 30 másodperccel később ér véget. A fenti példában, ahol a `eventTime` 2020-05-12T23:33:09.381 z, a +/-30-as időszakra vonatkozó HLS-jegyzékfájl iránti kérelem a következőhöz hasonlóan fog kinézni:

```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```

A fenti URL-cím egy úgynevezett [fő listát](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming)ad vissza, amely az URL-címeket tartalmazza a média lejátszási listáihoz. A média lista a következőhöz hasonló bejegyzéseket tartalmaz:

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
A fentiekben a bejegyzés azt jelzi, hogy egy videó töredék elérhető, amely időbélyeg-értékkel kezdődik `143039375031270` . Az `timestamp` analitikai eseményben szereplő érték ugyanazt az időkeretet használja, mint a média-lejátszási lista, és felhasználható a kapcsolódó videó-töredék azonosítására, és a megfelelő keretre való rákeresésre.

További információért olvassa el a HLS-ben található, a frame-ben pontos kereséssel foglalkozó [cikk](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS) egyikét.

## <a name="controlling-events"></a>Az események irányítása

A következő modul-Twin tulajdonságokat használhatja a modul [Twin JSON-sémájában](module-twin-configuration-schema.md)leírtaknak megfelelően az élő videó Analytics IoT Edge modulban közzétett operatív és diagnosztikai események vezérléséhez.

`diagnosticsEventsOutputName` – a tulajdonsághoz tartozó (bármilyen) érték belefoglalása és megadása, hogy a modul diagnosztikai eseményeket kapjon. Hagyja ki, vagy hagyja üresen a modult a diagnosztikai események közzétételének leállításához.
   
`operationalEventsOutputName` – a tulajdonsághoz tartozó (bármely) érték belefoglalása és megadása (bármilyen) a modul működési eseményeinek beszerzése érdekében. Hagyja ki, vagy hagyja üresen a modul működési események közzétételének leállításához.
   
Az elemzési eseményeket olyan csomópontok hozza létre, mint például a mozgásészlelési processzor vagy a HTTP-bővítmény processzora, az IoT hub-fogadó pedig az IoT Edge hubhoz küldi el őket. 

A [fenti események útválasztását](../../iot-edge/module-composition.md#declare-routes) a $edgeHub modul twin (az üzembe helyezési jegyzékben) kívánt tulajdonságán keresztül szabályozhatja:

```
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

A fentiekben a lvaEdge az élő videó Analytics neve IoT Edge modulon, és az útválasztási szabály a [deklarált útvonalakban](../../iot-edge/module-composition.md#declare-routes)definiált sémát követi.

> [!NOTE]
> Annak érdekében, hogy az elemzési események elérjék a IoT Edge központot, egy IoT hub-fogadó csomópontnak kell lennie, amely a mozgásérzékelő processzor-és/vagy bármely HTTP-bővítmény processzor-csomópontján felül van.

## <a name="event-schema"></a>Eseményséma

Az események a peremhálózati eszközről származnak, és a peremhálózati vagy a felhőben is felhasználhatók. A IoT Edge élő videó-elemzés által generált események megfelelnek az Azure IoT Hub által létrehozott [streaming Messaging-mintának](../../iot-hub/iot-hub-devguide-messages-construct.md) , a rendszer tulajdonságai, az alkalmazás tulajdonságai és a törzs alapján.

### <a name="summary"></a>Összegzés

A IoT Hubon keresztül megfigyelt minden esemény az alább leírtak szerint egy közös tulajdonsággal fog rendelkezni.

|Tulajdonság   |Tulajdonságtípus| Adattípus   |Leírás|
|---|---|---|---|
|üzenet-azonosító |rendszer |guid|  Egyedi eseményazonosító.|
|témakör| applicationProperty |sztring|    Azure Resource Manager a Media Services-fiók elérési útja.|
|tulajdonos|   applicationProperty |sztring|    Az eseményt kibocsátó entitás alútvonala.|
|eventTime| applicationProperty|    sztring| Az esemény létrehozásának ideje.|
|eventType| applicationProperty |sztring|    Eseménytípus azonosítója (lásd alább).|
|body (Törzs)|body (Törzs)  |object|    Adott esemény adatai.|
|dataVersion    |applicationProperty|   sztring  |{Major}. Kisebb|

### <a name="properties"></a>Tulajdonságok

#### <a name="message-id"></a>üzenet-azonosító

Esemény globálisan egyedi azonosítója (GUID)

#### <a name="topic"></a>témakör

A gráfhoz társított Azure Media Service-fiókot jelöli.

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>tulajdonos

Az eseményt kibocsátó entitás:

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

A tulajdonos tulajdonság lehetővé teszi, hogy az általános események le legyenek képezve a generáló modulra. Például érvénytelen RTSP-Felhasználónév vagy jelszó esetén a generált esemény a `Microsoft.Media.Graph.Diagnostics.ProtocolError` `/graphInstances/myGraph/sources/myRtspSource` csomóponton lenne.

#### <a name="event-types"></a>Eseménytípusok

Az Eseménytípus a következő sémának megfelelő névtérhez van rendelve:

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>Eseményosztályok

|Osztály neve|Description|
|---|---|
|Elemzés  |A tartalom elemzése részeként generált események.|
|Diagnosztika    |A problémák és a teljesítmény diagnosztizálását segítő események.|
|Működik    |Az erőforrás-művelet részeként generált események.|

Az események típusai az egyes események osztályokra jellemzőek.

Példák:

* Microsoft. Media. Graph. Analytics. következtetés
* Microsoft. Media. Graph. Diagnostics. AuthorizationError
* Microsoft. Media. Graph. Operational. GraphInstanceStarted

### <a name="event-time"></a>Esemény időpontja

Az esemény időpontját a ISO8601 karakterlánc írja le, és ez az esemény időpontjában következik be.

## <a name="logging"></a>Naplózás

Más IoT Edge modulokhoz hasonlóan a peremhálózati eszközön is ellenőrizheti [a tároló naplóit](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) . A naplókba írt adatokat a [következő modul Twin](module-twin-configuration-schema.md) tulajdonságai szabályozzák:

* Naplózási szint

   * Az engedélyezett értékek: részletes, információ, figyelmeztetés, hiba, nincs.
   * Az alapértelmezett érték az információ – a naplók hibát, figyelmeztetést és információt tartalmaznak. üzenetek.
   * Ha a figyelmeztetés értéket állítja be, a naplók hibaüzeneteket és figyelmeztető üzeneteket tartalmaznak
   * Ha a hiba értékre állítja a értéket, a naplók csak hibaüzeneteket fognak tartalmazni.
   * Ha a none értéket állítja be, a rendszer nem generál naplókat (ez nem ajánlott).
   * A részletes elemzést csak akkor használja, ha a problémák diagnosztizálásához Azure-támogatással kell megosztania a naplókat.
* logCategories

   * Vesszővel tagolt lista a következők közül egy vagy több közül: alkalmazás, események, MediaPipeline.
   * Alapértelmezett: alkalmazás, események.
   * Alkalmazás – ez a modul magas szintű információi, például a modul indítási üzenetei, környezeti hibák és közvetlen metódus-hívások.
   * Események – ezek a cikkben korábban ismertetett események.
   * MediaPipeline – ezek olyan alacsony szintű naplók, amelyek betekintést nyújthatnak a hibák elhárítása érdekében, például az RTSP-kompatibilis kamerával létesített kapcsolatok létesítésével kapcsolatos nehézségeket.
   
### <a name="generating-debug-logs"></a>Hibakeresési naplók generálása

Bizonyos esetekben előfordulhat, hogy a fentiekben leírtnál részletesebb naplókat kell létrehoznia, hogy az Azure támogatási szolgálata segítsen a probléma megoldásában. Ennek elvégzéséhez két lépés szükséges.

Először [csatolja a modul tárterületét az createOptions-](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage) on keresztül. Ha az [üzembe helyezési jegyzék sablonját](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) a gyors indítással vizsgálja, a következőt fogja látni:

```
"createOptions": {
   …
   "Binds": [
     "/var/local/mediaservices/:/var/lib/azuremediaservices/"
   ]
 }
```

A fentiek lehetővé teszi, hogy az Edge-modul a (Device) tároló "/var/local/Mediaservices/" elérési útjába írjon naplókat. Ha a következő kívánt tulajdonságot adja hozzá a modulhoz:

`"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

Ezután a modul bináris formában fogja írni a hibakeresési naplókat az (eszköz) tárolási útvonal/var/local/Mediaservices/debuglogs/, amelyet az Azure-támogatással oszthat meg.

## <a name="faq"></a>GYIK

[Gyakori kérdések](faq.md#monitoring-and-metrics)

## <a name="next-steps"></a>Következő lépések

[Folyamatos videófelvétel](continuous-video-recording-tutorial.md)
