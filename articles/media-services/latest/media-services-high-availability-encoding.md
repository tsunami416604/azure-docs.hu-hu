---
title: Azure Media Services magas rendelkezésre állású kódolás
description: Megtudhatja, hogyan hajthat végre feladatátvételt másodlagos Media Services-fiókra, ha regionális adatközpont-leállás vagy-meghibásodás történik.
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
ms.openlocfilehash: f5b02376111a3deba33cd5688330018bd7c370d8
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899216"
---
# <a name="media-services-high-availability-encoding"></a>Media Services magas rendelkezésre állású kódolás 

A Azure Media Services kódolási szolgáltatás egy regionális batch-feldolgozó platform, amely jelenleg egyetlen régión belül nem magas rendelkezésre állásra van tervezve. A kódolási szolgáltatás jelenleg nem biztosít azonnali feladatátvételt a szolgáltatáshoz, ha a mögöttes összetevő vagy a függő szolgáltatások (például a Storage, az SQL) esetében regionális adatközpont-leállás vagy meghibásodás történt. Ez a cikk bemutatja, hogyan helyezheti üzembe a Media Servicest a magas rendelkezésre állású architektúra fenntartásához a feladatátvétel és az alkalmazások optimális rendelkezésre állásának biztosítása érdekében.

A cikkben leírt irányelvek és ajánlott eljárások követésével csökkentheti a kódolási hibák, a késések és a helyreállítási idő minimalizálásának kockázatát, ha egy adott régióban áramkimaradás történik.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>Régiók közötti kódolási rendszer létrehozása

* [Hozzon létre](create-account-cli-how-to.md) két (vagy több) Azure Media Services fiókot.

    A két fióknak különböző régiókban kell lennie. További információ: [a Azure Media Services szolgáltatást üzembe helyező régiók](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).
* Töltse fel az adathordozót ugyanabba a régióba, ahonnan el szeretné küldeni a feladatot. A kódolás megkezdésével kapcsolatos további információkért lásd: [a feladatok bevitele HTTPS URL-](job-input-from-http-how-to.md) címről vagy egy [helyi fájlból származó feladatok létrehozása](job-input-from-local-file-how-to.md).

    Ha ezt követően újra el kell küldenie a [feladatot](transforms-jobs-concept.md) egy másik régióba, használhatja a JobInputHttp-t, vagy a [copy-blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) paranccsal másolhatja át az adatait a forrás-eszköz tárolójából a másik régióba tartozó tárgyieszköz-tárolóba.
* Fizessen elő az egyes fiókokban lévő JobStateChange-üzenetekre Azure Event Gridon keresztül. További információkért lásd:

    * A [hangelemzés mintája](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer) , amely bemutatja, hogyan figyelheti a feladatokat Azure Event Grid beleértve a tartalék hozzáadását, ha a Azure Event Grid üzenetek valamilyen okból késleltetve vannak.
    * [Media Services eseményekhez Azure Event Grid sémák](media-services-event-schemas.md)
    * Az [események regisztrálása a Azure Portal vagy a CLI](reacting-to-media-services-events.md) használatával (ezt a EVENTGRID Management SDK-val is elvégezheti)
    * [Microsoft. Azure. EVENTGRID SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (amely natív módon támogatja az Media Services eseményeket).

    Azure Functions használatával Event Grid eseményeket is felhasználhat.
*    A [feladatok](transforms-jobs-concept.md)létrehozásakor:
    
    * Véletlenszerűen válasszon ki egy fiókot a jelenleg használt fiókok listájából (ez a lista általában mindkét fiókot tartalmazza, de ha problémát észlel, akkor csak egy fiókot tartalmazhat). Ha a lista üres, riasztást küld, hogy az operátor megvizsgálja.
    * Általános útmutatóként egy [JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) (kivéve, ha a [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) 3 Media szolgáltatás [számára fenntartott](media-reserved-units-cli-how-to.md) egységet használ, JobOutput ajánlott).
    * A kiválasztott fiókhoz tartozó Media szolgáltatás számára fenntartott egységek (MRUs) számának beolvasása. Ha a **Media szolgáltatás számára fenntartott egységek** száma még nincs a maximális értéknél, adja hozzá a feladatokhoz szükséges MRUs számát, és frissítse a szolgáltatást. Ha a feladatokhoz tartozó beküldési arány magas, és gyakran kérdezi le a MRUs, hogy a lehető legtöbbet találja, használjon egy elosztott gyorsítótárat az értékhez ésszerű időkorlát mellett.
    * Tartsa meg a fedélzeti feladatok számát.

* Ha a JobStateChange-kezelő értesítést kap arról, hogy egy adott tevékenység elérte az ütemezett állapotot, jegyezze fel az ütemezési állapotba és a használt régióba/fiókba való belépés időpontját.
* Ha a JobStateChange-kezelő értesítést kap arról, hogy egy adott művelet elérte a feldolgozási állapotot, a feladathoz tartozó rekordot megjelölve feldolgozásként jelöli meg a feladatot.
* Ha a JobStateChange-kezelő értesítést kap arról, hogy a feladat elérte a befejezett/hibás/megszakított állapotot, állítsa be véglegesként a feladat rekordját, és állítsa le a fedélzeti feladatok darabszámát. Szerezze be a kiválasztott fiókhoz tartozó Media szolgáltatás számára fenntartott egységek számát, és hasonlítsa össze az aktuális MRU-számot a fedélzeti feladatok számával. Ha a fedélzeti szám kisebb, mint az MRU szám, akkor a rendszer csökkenti és frissíti a szolgáltatást.
* Külön folyamattal kell rendelkeznie, amely rendszeresen megvizsgálja a feladatok rekordjait
    
    * Ha az ütemezett állapot olyan feladatokkal rendelkezik, amelyek egy adott régióra vonatkozóan ésszerű időn belül nem fejlettek a feldolgozási állapotra, távolítsa el a régiót a jelenleg használt fiókok listájáról.  Az üzleti igényektől függően dönthet úgy, hogy azonnal megszakítja a feladatokat, és visszaküldi azokat a másik régióba. Vagy további időt adhat nekik, hogy a következő állapotba lépjenek.
    * A fiókon konfigurált Media szolgáltatás számára fenntartott egységek számától és a beküldési sebességtől függően előfordulhat, hogy a rendszer a várólistán lévő feladatok esetében is felveszi a feladatokat, mert a rendszer még nem vette fel a feldolgozásra.  Ha a várólistán lévő feladatok listája egy adott régióban egy elfogadható korláton túlnyúlva nő, akkor ezeket a feladatokat megszakíthatja, és elküldheti a másik régiónak.  Ez azonban annak a tünete lehet, hogy nincs elég Media szolgáltatás számára fenntartott egység konfigurálva a fiókban az aktuális terheléshez.  Ha szükséges, magasabb szintű Media szolgáltatás számára fenntartott egységre vonatkozó kvótát igényelhet az Azure-támogatással.
    * Ha egy régiót eltávolítottak a fiók listájából, figyelje a helyreállítást a listához való hozzáadás előtt.  A regionális állapot a régió meglévő feladatain keresztül figyelhető (ha nem voltak megszakítva és nem lettek elküldve), a fiók egy adott időtartam után visszakerül a listához, és az operátorok figyelik az Azure-kommunikációt az esetlegesen befolyásoló kimaradások miatt Azure Media Services.
    
Ha úgy találja, hogy az MRU szám a nagy mennyiségű felveréssel van elválasztva, helyezze át a logikai műveletet az időszakos feladatba. Ellenőrizze, hogy az előzetes feladatot Beküldő logika összehasonlítja-e a fedélzeti darabszámot az aktuális MRU számmal, hogy meg kell-e frissítenie a MRUs.

## <a name="next-steps"></a>További lépések

* [Igény szerinti, többrégiós adatfolyamok készítése](media-services-high-availability-streaming.md)
* Példák a [kód](https://docs.microsoft.com/samples/browse/?products=azure-media-services) megadására
