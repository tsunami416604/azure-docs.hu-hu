---
title: Az időeltolódás és az élő kimenetek használatával igény szerinti videolejátszást hozhat létre
titleSuffix: Azure Media Services
description: Ez a cikk azt ismerteti, hogy miként használhatja az időeltolódást és az élő kimeneteket az élő közvetítések rögzítésére és az igény szerinti lejátszás létrehozására.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 08/27/2019
ms.author: juliako
ms.openlocfilehash: 4c7618b60e5fd86a9b8b3f22fb3333c00cfdfa61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74899799"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>Az időeltolódás és az élő kimenetek használatával igény szerinti videolejátszást hozhat létre

Az Azure Media Servicesben a [Live Output](https://docs.microsoft.com/rest/api/media/liveoutputs) objektum olyan, mint egy digitális videofelvevő, amely elkapja és rögzíti az élő közvetítést egy eszközre a Media Services-fiókban. A rögzített tartalom megmarad az [eszköz](https://docs.microsoft.com/rest/api/media/assets) erőforrás által meghatározott tárolóban (a tároló a fiókhoz csatlakoztatott Azure Storage-fiókban található). Az Élő kimenet lehetővé teszi a kimenő élő közvetítés bizonyos tulajdonságainak szabályozását is, például azt, hogy az adatfolyam mekkora része kerül az archív felvételre (például a felhőbeli DVR kapacitására), vagy hogy a nézők mikor kezdhetik meg nézni az élő közvetítést. Az archívum a lemezen egy kör körös archív "ablak", amely csak az élő kimenet **archiveWindowLength** tulajdonságában megadott tartalom mennyiségét tartalmazza. Az ablakon kívül rekedő tartalom automatikusan törlődik a tárolótárolóból, és nem állítható helyre. Az archiveWindowLength érték egy ISO-8601 időtartamot (például PTHH:MM:SS) jelöli, amely a DVR kapacitását határozza meg. Az érték legalább három percés legfeljebb 25 óra között állítható be.

Az élő esemény és az élő kimenetek közötti kapcsolat hasonló a hagyományos televíziós közvetítéshez, mivel a csatorna (Élő esemény) folyamatos videófolyamot jelent, és a felvétel (Élő kimenet) egy adott időszegmensre terjed ki (például esti hírek 18:30-tól 19:00-ig). Miután az adatfolyam az élő eseménybe áramlik, megkezdheti a streamelési eseményt egy eszköz, a Live Output és a streamelési lokátor létrehozásával. Az Élő kimenet archiválja az adatfolyamot, és elérhetővé teszi a megtekintők számára a [Streaming Végponton](https://docs.microsoft.com/rest/api/media/streamingendpoints)keresztül. Több élő kimenetet (legfeljebb három) hozhat létre egy élő eseményen, különböző archiválási időtartamokkal és beállításokkal. Az élő közvetítési munkafolyamattal kapcsolatos további információkért tekintse meg az [általános lépéseket című szakaszt.](live-streaming-overview.md#general-steps)

## <a name="using-a-dvr-during-an-event"></a>DVR használata esemény közben

Ez a szakasz azt ismerteti, hogyan használhatja a DVR-t egy esemény során annak szabályozására, hogy az adatfolyam melyik részei érhetők el a "visszatekeréshez".

Az `archiveWindowLength` érték határozza meg, hogy a megtekintő milyen messzire mehet vissza az időben az aktuális élő pozícióból. Az `archiveWindowLength` érték azt is meghatározza, hogy az ügyfél jegyzékfájljai mennyi ideig növekedhetnek.

Tegyük fel, hogy egy focimeccset `ArchiveWindowLength` közvetítesz, és csak 30 perce van. Az a néző, aki 45 perccel a játék kezdete után kezdi meg az esemény megtekintését, legbőlegelheti a 15 perces jelet. A játék élő kimenetei mindaddig folytatódnak, amíg az élő esemény le nem áll. Az archívablakon kívül rekedő tartalom a rendszer folyamatosan eldobja a tárolóból, és nem állítható helyre. Ebben a példában az esemény kezdete és a 15 perces jel közötti videó lett volna törölve a DVR és a tároló blob storage az eszköz. Az archívum nem állítható helyre, és törlődik a tárolóból az Azure blob storage.The archive isn't recoverable and is removed from the container in Azure blob storage.

A Live Event legfeljebb három egyidejűleg futó Live Output-ot támogat (egyszerre legfeljebb 3 felvételt/archívumot hozhat létre egy élő közvetítésből). Ez a támogatás lehetővé teszi az esemény különböző részeinek szükség szerint közzétételét és archiválását. Tegyük fel, hogy egy 24 órás élő lineáris hírcsatornát kell közvetítenie, és a nap folyamán "felvételeket" kell létrehoznia a különböző programokról, hogy az ügyfelek nek igény szerinti tartalmat kínáljon a felzárkózás megtekintéséhez. Ebben a forgatókönyvben először hozzon létre egy elsődleges élő kimenet egy rövid archív ablak 1 óra vagy annál kevesebb, ez az elsődleges élő közvetítés, hogy a nézők is dallam-ba. Hozzon létre egy streamelési lokátort ehhez az élő kimenethez, és tegye közzé az alkalmazáson vagy a webhelyen "Live" hírcsatornaként. Az élő esemény futása közben programozott módon létrehozhat egy második egyidejű élő kimenetet a program elején (vagy 5 perccel korábban, hogy néhány fogantyút biztosítson a későbbi vágáshoz). Ez a második élő kimenet 5 perccel a program befejezése után törölhető. Ezzel a második eszközzel létrehozhat egy új streamelési lokátort, amely a programot igény szerinti eszközként teszi közzé az alkalmazás katalógusában. Ezt a folyamatot többször is megismételheti más programhatárok vagy olyan kiemelések esetében, amelyeket igény szerinti videóként szeretne megosztani, miközben az első élő kimenet "Élő" hírcsatornája továbbra is közvetíti a lineáris hírcsatornát.

## <a name="creating-an-archive-for-on-demand-playback"></a>Archívum létrehozása igény szerinti lejátszáshoz

Az az eszköz, amelyet az élő kimenet archivál, hogy automatikusan igény szerinti eszközlesz, amikor az élő kimenet törlődik. Az élő események leállítása előtt törölnie kell az összes élő kimenetet. Használhatja a választható jelző [removeOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) automatikusan eltávolítja a Live Outputs stop.

Még az esemény leállítása és törlése után is a felhasználók igény szerint streamelhetik az archivált tartalmat, mindaddig, amíg nem törli az eszközt. Egy eszköz nem törölhető, ha egy esemény használja; az eseményt először törölni kell.

Ha az élő kimenet eszközét egy streamelési lokátor használatával tette közzé, az élő esemény (a DVR-ablak hosszáig) továbbra is látható marad a streamelési lokátor lejárati vagy törlési lejáratáig, attól függően, hogy melyik következik be előbb.

További információkért lásd:

- [Élő közvetítés – áttekintés](live-streaming-overview.md)
- [Élő közvetítésoktató anyaga](stream-live-tutorial-with-api.md)

> [!NOTE]
> Az élő kimenet törlésekor nem törli a mögöttes eszközt és tartalmat az eszközben.

## <a name="next-steps"></a>További lépések

* [Subclip a videókat](subclip-video-rest-howto.md).
* [Szűrőket definiáljon az eszközökhöz.](filters-dynamic-manifest-rest-howto.md)
