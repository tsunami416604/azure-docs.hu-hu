---
title: Azure Media Services magas rendelkezésre állás
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
ms.openlocfilehash: e2212c2ad5a0ab1899d281e99a89b33a2fc0298f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202285"
---
# <a name="azure-media-services-high-availability-guidance"></a>Azure Media Services magas rendelkezésre állási útmutató

A Azure Media Services kódolási szolgáltatás egy regionális batch-feldolgozó platform, amely jelenleg egyetlen régión belül nem magas rendelkezésre állásra van tervezve. A kódolási szolgáltatás jelenleg nem biztosítja a szolgáltatás azonnali feladatátvételét, ha a mögöttes összetevő vagy a függő szolgáltatások (például a Storage, az SQL stb.) regionális adatközpont-kimaradása vagy meghibásodása van.  Ez a cikk bemutatja, hogyan helyezheti üzembe a Media Servicest a magas rendelkezésre állású architektúra fenntartásához a feladatátvétel és az alkalmazások optimális rendelkezésre állásának biztosítása érdekében. 

A cikkben leírt irányelvek és ajánlott eljárások követésével csökkentheti a kódolási hibák, a késések és a helyreállítási idő minimalizálásának kockázatát, ha egy adott régióban áramkimaradás történik.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>Régiók közötti kódolási rendszer létrehozása

* [Hozzon létre](create-account-cli-how-to.md) két (vagy több) Azure Media Services fiókot.
* Fizessen elő **JobStateChange** -üzenetekre az egyes fiókokban.

    * A Media Services v3-as verzióban Azure Event Grid használatával történik. További információkért lásd:
    
       * [Event Grid példák](../../event-grid/receive-events.md), 
       * [Azure Event Grid sémák Media Services eseményekhez](media-services-event-schemas.md), 
       * Az [események regisztrálása a Azure Portal vagy a CLI](reacting-to-media-services-events.md) használatával (ezt a EVENTGRID Management SDK-val is elvégezheti)
       * [Microsoft. Azure. EVENTGRID SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (amely natív módon támogatja az Media Services eseményeket). 
       
        Azure Functions használatával Event Grid eseményeket is felhasználhat.
    * Media Services v2-ben ez a [NotificationEndpoints](../previous/media-services-dotnet-check-job-progress-with-webhooks.md)-on keresztül történik.
* [A feladatok létrehozásakor](transforms-jobs-concept.md):

    * Véletlenszerűen válasszon ki egy fiókot a jelenleg használt fiókok listájából (ez a lista általában mindkét fiókot tartalmazza, de ha problémát észlel, akkor csak egy fiókot tartalmazhat). Ha a lista üres, riasztást küld, hogy az operátor megvizsgálja.
    * Általános útmutatóként feladatra vagy [JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) egy [Media szolgáltatás számára fenntartott egységre](media-reserved-units-cli-how-to.md) van szükség (hacsak nem a [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) -t használja a v3-as verzióban).
    * A kiválasztott fiókhoz tartozó [Media szolgáltatás számára fenntartott egységek](media-reserved-units-cli-how-to.md) (MRUs) számának beolvasása. Ha a **Media szolgáltatás számára fenntartott egységek** száma még nincs a maximális értéknél, adja hozzá a feladatokhoz szükséges MRUs számát, és frissítse a szolgáltatást. Ha a feladatokhoz tartozó beküldési arány magas, és gyakran kérdezi le a MRUs, hogy a lehető legtöbbet találja, használjon egy elosztott gyorsítótárat az értékhez ésszerű időkorlát mellett.
    * Tartsa meg a fedélzeti feladatok számát.
* Ha a JobStateChange-kezelő értesítést kap arról, hogy egy adott tevékenység elérte az ütemezett állapotot, jegyezze fel az ütemezési állapotba és a használt régióba/fiókba való belépés időpontját.    
* Ha a JobStateChange-kezelő értesítést kap arról, hogy egy adott művelet elérte a feldolgozási állapotot, a feladathoz tartozó rekordot megjelölve feldolgozásként jelöli meg a feladatot.
* Ha a JobStateChange-kezelő értesítést kap arról, hogy a feladat elérte a befejezett/hibás/megszakított állapotot, állítsa be véglegesként a feladat rekordját, és állítsa le a fedélzeti feladatok darabszámát. Szerezze be a kiválasztott fiókhoz tartozó Media szolgáltatás számára fenntartott egységek számát, és hasonlítsa össze az aktuális MRU-számot a fedélzeti feladatok számával. Ha a fedélzeti szám kisebb, mint az MRU szám, akkor a rendszer csökkenti és frissíti a szolgáltatást.
* Külön folyamattal kell rendelkeznie, amely rendszeresen megvizsgálja a feladatok rekordjait. Ha az ütemezett állapot olyan feladatokkal rendelkezik, amelyek egy adott régióra vonatkozóan ésszerű időn belül nem fejlettek a feldolgozási állapotra, távolítsa el a régiót a jelenleg használt fiókok listájáról.

    * Az üzleti igényektől függően dönthet úgy, hogy azonnal megszakítja a feladatokat, és visszaküldi azokat a másik fiókba. Vagy további időt adhat nekik, hogy a következő állapotba lépjenek.   
    * Egy adott idő elteltével adja vissza a fiókot a jelenleg használt listához (feltételezve, hogy a régió helyreállt).
    
Ha úgy találja, hogy az MRU szám a nagy mennyiségű felveréssel van elválasztva, helyezze át a logikai műveletet az időszakos feladatba. Ellenőrizze, hogy az előzetes feladatot Beküldő logika összehasonlítja-e a fedélzeti darabszámot az aktuális MRU számmal, hogy meg kell-e frissítenie a MRUs.

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Igény szerinti, többrégiós adatfolyamok készítése 

* Az igény szerinti, többrégiós adatfolyam-továbbítás magában foglalja az [eszközök](assets-concept.md), a [tartalmi kulcsokra vonatkozó házirendek](content-key-policy-concept.md) (ha használatos), a [folyamatos átviteli szabályzatok](streaming-policy-concept.md)és a [streaming-lokátorok](streaming-locators-concept.md)másolását. 
* Mindkét régióban létre kell hoznia a házirendeket, és naprakészen kell tartania azokat. 
* A folyamatos átviteli lokátorok létrehozásakor ugyanazt a lokátor azonosító értéket, a ContentKey azonosító értékét és a ContentKey értéket kell használnia.  
* Ha kódolja a tartalmat, azt javasoljuk, hogy kódolja a tartalmat az A régióban, és tegye közzé, majd másolja a kódolt tartalmat a B régióba, és tegye közzé az a régióval megegyező értékek használatával.
* A Traffic Managert a forrás és a kulcs kézbesítési szolgáltatása (Media Services konfigurációban található állomásnév) használatával is használhatja (ez az egyéni kulcs-kiszolgáló URL-címéhez hasonlóan jelenik meg).

## <a name="next-steps"></a>Következő lépések

* [Fiók létrehozása](create-account-cli-how-to.md)
* Példák a [kód](https://docs.microsoft.com/samples/browse/?products=azure-media-services) megadására
