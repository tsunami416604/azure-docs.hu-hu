---
title: Az Azure Media Services magas rendelkezésre állású kódolása
description: Megtudhatja, hogyan lehet feladatátvételt egy másodlagos Media Services-fiókba, ha egy regionális adatközpont-kimaradás vagy hiba történik.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: afaa7545fbcbab016249e73a2247817310c5cdfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78934194"
---
# <a name="media-services-high-availability-encoding"></a>Media Services magas rendelkezésre állású kódolás 

Az Azure Media Services kódolási szolgáltatás egy regionális kötegelt feldolgozási platform, és jelenleg nem tervezték a magas rendelkezésre állás egyetlen régión belül. A kódolási szolgáltatás jelenleg nem biztosít azonnali feladatátvételt a szolgáltatás, ha van egy regionális adatközpont kimaradás vagy az alapul szolgáló összetevő vagy függő szolgáltatások (például a tárolás, SQL) meghibásodása esetén. Ez a cikk bemutatja, hogyan telepítheti a Media Services magas rendelkezésre állású architektúrát feladatátvételsel, és hogyan biztosíthatja az alkalmazások optimális rendelkezésre állását.

A cikkben ismertetett irányelvek és gyakorlati tanácsok követésével csökkentheti a hibák, késések kódolásának kockázatát, és minimálisra csökkentheti a helyreállítási időt, ha egy adott régióban kimaradás történik.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>Hogyan építsünk egy régiók közötti kódolási rendszer

* [Hozzon létre](create-account-cli-how-to.md) két (vagy több) Azure Media Services-fiókot.

    A két fióknak különböző régiókban kell lennie. További információ: [azok a régiók, ahol az Azure Media Services szolgáltatás telepítve van.](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)
* Töltse fel a médiafájlokat ugyanabba a régióba, ahonnan a feladatot be szeretné küldeni. A kódolás elindításáról további információt a [Feladatbemenet létrehozása HTTPS-URL-címről](job-input-from-http-how-to.md) vagy [Feladatbevitel létrehozása helyi fájlból című](job-input-from-local-file-how-to.md)témakörben talál.

    Ha ezután újra el kell küldenie a [feladatot](transforms-jobs-concept.md) egy másik régióba, használhatja a JobInputHttp-t, vagy a [Copy-Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) segítségével másolhatja az adatokat a forráseszköz tárolójából egy másik régióban lévő eszköztárolóba.
* Iratkozzon fel a JobStateChange üzenetek minden fiókban az Azure Event Grid en keresztül. További információkért lásd:

    * [Audio Analytics-minta,](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer) amely bemutatja, hogyan figyelheti a feladatot az Azure Event Grid del, beleértve a tartalék hozzáadása esetén az Azure Event Grid üzenetek késik valamilyen okból.
    * [Azure Event Grid-sémák a Media Services-eseményekhez](media-services-event-schemas.md)
    * [Regisztráljon eseményekre az Azure Portalon vagy a CLI-n keresztül](reacting-to-media-services-events.md) (ezt az EventGrid Management SDK-val is megteheti)
    * [Microsoft.Azure.EventGrid SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (amely natív módon támogatja a Media Services-eseményeket).

    Event Grid-eseményeket is felhasználhat az Azure Functions segítségével.
* Feladat [létrehozásakor:](transforms-jobs-concept.md)

    * Véletlenszerűen válasszon ki egy fiókot a jelenleg használt fiókok listájából (ez a lista általában mindkét fiókot tartalmazza, de ha problémákat észlel, akkor csak egy fiókot tartalmazhat). Ha a lista üres, küldj riasztást, hogy az operátor megvizsgálhassa.
    * Általános útmutatás: [JobOutputonként](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) egy [adathordozószámára fenntartott egységre](media-reserved-units-cli-how-to.md) van szükség (kivéve, ha [videoanalyzerPreset-et](analyzing-video-audio-files-concept.md) használ, ahol joboutputonként 3 media fenntartott egység ajánlott).
    * A kiválasztott fiókhoz lefoglalt médiaegységek (MrUs) számának beszámítása. Ha az aktuális **adathordozó-lefoglalt egységek** száma még nem éri el a maximális értéket, adja meg a feladat által szükséges MRUs-ok számát, és frissítse a szolgáltatást. Ha a feladat benyújtási aránya magas, és gyakran lekérdezi az MRUs-t, hogy megtalálja, hogy a maximális, használjon elosztott gyorsítótárat az értékhez ésszerű időtúlértékkel.
    * Számolja meg a repülési feladatok számát.

* Amikor a JobStateChange-kezelő értesítést kap arról, hogy egy feladat elérte az ütemezett állapotot, jegyezze fel az ütemezési állapotba való belépés idejét és a használt régiót/fiókot.
* Amikor a JobStateChange-kezelő értesítést kap arról, hogy egy feladat elérte a feldolgozási állapotot, jelölje meg a feladat rekordját feldolgozásként.
* Amikor a JobStateChange-kezelő értesítést kap arról, hogy egy feladat elérte a Befejezett/Hiba/Visszavont állapotot, jelölje meg a feladat rekordját véglegesként, és csökkentse a repülési feladat számát. A kiválasztott fiókhoz lefoglalt médiaegységek számának beszerezhető, és hasonlítsa össze az aktuális MRU-számot a repülési feladat számával. Ha a repülési szám kisebb, mint az MRU-szám, majd csökkentse azt, és frissítse a szolgáltatást.
* Van egy külön folyamat, amely rendszeresen megnézi a feladatok ról uk rekordokat
    
    * Ha az ütemezett állapotban olyan feladatok vannak, amelyek egy adott régióban ésszerű időn belül nem jutottak a feldolgozási állapotba, távolítsa el a régiót a jelenleg használt fiókok listájáról.  Az üzleti követelményektől függően dönthet úgy, hogy azonnal törli ezeket a feladatokat, és újra elküldi őket a másik régióba. Vagy adhatnál nekik még egy kis időt, hogy a következő államba költözzenek.
    * A fiókban konfigurált médiafenntartott egységek számától és a beküldési aránytól függően előfordulhatnak olyan feladatok is, amelyek várólistára helyezett állapotban vannak, amelyeket a rendszer még nem vett fel feldolgozásra.  Ha a várólistára helyezett állapotban lévő feladatok listája egy régióban egy elfogadható korláton túlnő, ezek a feladatok visszavonhatók és elküldhetők a másik régióba.  Ez azonban annak a tünete lehet, hogy nincs elegendő media által fenntartott egység a fiókban az aktuális terheléshez.  Szükség esetén az Azure-támogatáson keresztül magasabb Media-szolgáltatás számára fenntartott egységkvótát kérhet.
    * Ha egy régiót eltávolítottak a fióklistáról, figyelje meg a helyreállítást, mielőtt újra hozzáadná a listához.  A regionális állapot figyelhető a régióban meglévő feladatokon keresztül (ha nem törölték és nem nyújtották be újra), a fiók egy idő után visszaadva a listához, valamint az operátorok figyelhetik az Azure-kommunikációt a kimaradásokról, amelyek hatással lehetnek a kimaradásokra. Azure Media Services.
    
Ha úgy találja, hogy az MRU-szám sokszor fel-le ver, helyezze át a decrement logikát az időszakos feladatra. A feladat előtti submit logika hasonlítsa össze a bevizsgálati szám az aktuális MRU-szám, hogy szükség van az MRU frissítésére.

## <a name="next-steps"></a>További lépések

* [Igény szerinti, igény szerinti, régiók közötti streamelést hozhat létre](media-services-high-availability-streaming.md)
* [Kódminták kijelentkezése](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
