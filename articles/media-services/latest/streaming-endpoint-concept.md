---
title: Streaming-végpontok (forrás) a Azure Media Servicesban | Microsoft Docs
description: Azure Media Services a streaming-végpont (forrás) olyan dinamikus csomagolást és folyamatos átviteli szolgáltatást jelent, amely közvetlenül az ügyfél-lejátszó alkalmazásnak, illetve egy Content Delivery Network (CDN) számára biztosít tartalmat a további terjesztéshez.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: 831ba217e99d1610383320ddf5706c6acfcdf48a
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67848902"
---
# <a name="streaming-endpoints"></a>Streamvégpontok 

Microsoft Azure Media Services a streaming- [végpont](https://docs.microsoft.com/rest/api/media/streamingendpoints) egy dinamikus (igény szerinti) csomagolási és forrás-szolgáltatást jelent, amely az élő és az igény szerinti tartalmat közvetlenül az ügyfél-lejátszó alkalmazásba, a közös adatfolyam-továbbítási adathordozók használatával tudja biztosítani. protokollok (HLS vagy DASH). Emellett az adatfolyam- **végpont** dinamikus (igény szerinti) titkosítást biztosít az iparág vezető DRMs.

Media Services fiók létrehozásakor a rendszer egy **alapértelmezett** folyamatos átviteli végpontot hoz létre egy leállított állapotban. Az **alapértelmezett** folyamatos átviteli végpont nem törölhető. További folyamatos átviteli végpontok is létrehozhatók a fiókban (lásd: [kvóták és korlátozások](limits-quotas-constraints.md)). 

> [!NOTE]
> A streaming-videók elindításához el kell indítania azt a **folyamatos átviteli végpontot** , amelyről továbbítani szeretné a videót. 
>  
> Csak akkor számítunk fel díjat, ha a folyamatos átviteli végpont futó állapotban van.

## <a name="naming-convention"></a>Elnevezési konvenció

Az alapértelmezett végpont esetében:`{AccountName}-{DatacenterAbbreviation}.streaming.media.azure.net`

További végpontok esetén:`{EndpointName}-{AccountName}-{DatacenterAbbreviation}.streaming.media.azure.net`

## <a name="types"></a>Típusú  

Kétféle **streamvégponttípus** létezik: **Standard szintű** (előzetes verzió) és **prémium**. A típust az adatfolyam-végpont számára lefoglalt`scaleUnits`méretezési egységek () száma határozza meg. 

A táblázat a típusokat írja le:  

|Type|Skálázási egységek|Leírás|
|--------|--------|--------|  
|**Standard**|0|Az alapértelmezett folyamatos átviteli végpont egy **szabványos** típus, amely módosítható a prémium típusra `scaleUnits`.|
|**Prémium**|>0|**Prémium** szintű A streaming-végpontok speciális számítási feladatokhoz alkalmasak, amelyek dedikált és skálázható sávszélesség-kapacitást biztosítanak. A **prémium** típusra a `scaleUnits` (folyamatos átviteli egységek) beállításával léphet be. `scaleUnits`dedikált kimenő kapacitást kínál, amely 200 Mbps-os növekményekben vásárolható meg. A **Prémium** típus használatakor mindegyik engedélyezett egység további sávszélesség-kapacitást nyújt az alkalmazásnak. |

> [!NOTE]
> Azon ügyfelek számára, akik nagy internetes célközönségeknek szánt tartalmat szeretnének kézbesíteni, javasoljuk, hogy engedélyezze a CDN használatát a streaming végponton.

SLA-információ: [díjszabás és SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Adatfolyam-típusok összehasonlítása

Funkció|Standard|Prémium
---|---|---
Teljesítmény |Akár 600 Mbps, és a CDN használata esetén sokkal nagyobb hatékonyságot biztosít.|200 MB/s átviteli egység (SU). Sokkal nagyobb hatékonyságot biztosíthat a CDN használatakor.
Tartalomkézbesítési hálózat (CDN)|Azure CDN, harmadik féltől származó CDN vagy nincs CDN.|Azure CDN, harmadik féltől származó CDN vagy nincs CDN.
A számlázás arányosan történik| Napi|Napi
Dinamikus titkosítás|Igen|Igen
Dinamikus csomagolás|Igen|Igen
Méretezés|Automatikus méretezés a célként megadott átviteli sebességgel.|További SUs
IP-szűrés/G20/egyéni gazdagép <sup>1</sup>|Igen|Igen
Progresszív letöltés|Igen|Igen
Ajánlott használat |A folyamatos átviteli forgatókönyvek túlnyomó többségét ajánljuk.|Professzionális használat.

<sup>1</sup> csak akkor használható közvetlenül a folyamatos átviteli végponton, ha a CDN nincs engedélyezve a végponton.<br/>

## <a name="properties"></a>properties 

Ez a szakasz részletesen ismerteti a folyamatos átviteli végpontok tulajdonságait. Az új adatfolyam-végpontok és az összes tulajdonság leírásának ismertetését példákat a [streaming Endpoint (adatfolyam-végpont](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)) című témakörben talál. 

- `accessControl`-A következő biztonsági beállítások konfigurálására szolgál ehhez a folyamatos átviteli végponthoz: Akamai aláírási fejléc-hitelesítési kulcsok és IP-címek, amelyek számára engedélyezett a végponthoz való kapcsolódás.<br />Ez a tulajdonság csak akkor állítható be `cdnEnabled` , ha hamis értékre van állítva.
- `cdnEnabled`-Azt jelzi, hogy engedélyezve van-e a folyamatos átviteli végpont Azure CDN integrációja (alapértelmezés szerint le van tiltva). Ha az értéke `cdnEnabled` True (igaz), a következő konfigurációk le `customHostNames` vannak `accessControl`tiltva: és.
  
    Nem minden adatközpont támogatja a Azure CDN integrációt. Az alábbi lépéseket követve ellenőrizhető, hogy az adatközpontja rendelkezik-e a Azure CDN integrációs szolgáltatással:
 
  - Próbálja meg a `cdnEnabled` igaz értéket beállítani.
  - Tekintse át a `HTTP Error Code 412` (z) (PreconditionFailed) visszaadott eredményét "a streaming Endpoint CdnEnabled tulajdonság nem állítható igaz értékre, mert a CDN-képesség nem érhető el az aktuális régióban." 

    Ha ezt a hibaüzenetet kapja, az adatközpont nem támogatja azt. Próbálkozzon egy másik adatközpontgal.
- `cdnProfile`-Ha `cdnEnabled` a értéke TRUE (igaz), akkor `cdnProfile` értékeket is megadhat. `cdnProfile`annak a CDN-profilnak a neve, amelybe a CDN-végpontot létrehozza a rendszer. Megadhat egy meglévő cdnProfile, vagy használhat egy újat. Ha az érték null, `cdnEnabled` és igaz, a rendszer az alapértelmezett "AzureMediaStreamingPlatformCdnProfile" értéket használja. Ha a megadott `cdnProfile` érték már létezik, a rendszer létrehoz egy végpontot. Ha a profil nem létezik, a rendszer automatikusan létrehoz egy új profilt.
- `cdnProvider`-Ha a CDN engedélyezve van, `cdnProvider` értékeket is megadhat. `cdnProvider`meghatározza, hogy melyik szolgáltatót fogja használni. Jelenleg három érték támogatott: "StandardVerizon", "PremiumVerizon" and "StandardAkamai". Ha nincs megadva érték, és `cdnEnabled` igaz, a rendszer a "StandardVerizon" értéket használja (ez az alapértelmezett érték).
- `crossSiteAccessPolicies`-A különböző ügyfelekre vonatkozó helyek közötti hozzáférési szabályzatok megadására szolgál. További információkért lásd: tartományok [közötti házirend fájljának specifikációja](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) és [egy szolgáltatás elérhetővé tétele a tartomány határain belül](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx).<br/>A beállítások csak Smooth Streamingra vonatkoznak.
- `customHostNames`-Egy adatfolyam-végpont konfigurálására szolgál egy egyéni állomásnévre irányuló forgalom fogadásához.  Ez a tulajdonság a standard és a prémium szintű streaming végpontok esetében érvényes, és `cdnEnabled`a következő esetekben állítható be: false.
    
    Media Servicesnak meg kell erősítenie a tartománynév tulajdonjogát. Media Services ellenőrzi a tartománynevek tulajdonjogát úgy, hogy `CName` egy olyan rekordot igényel, amely a Media Services fiók azonosítóját tartalmazza a használatban lévő tartományhoz hozzáadandó összetevőként. Ha például az "Sports.contoso.com" nevet szeretné használni a streaming végpont egyéni állomásneveként, akkor egy rekordot `<accountId>.contoso.com` úgy kell konfigurálni, hogy az Media Services ellenőrző állomásnév egyikére mutasson. Az ellenőrző állomásnév verifydns áll. \<Mediaservices-DNS-Zone >. 

    A következő, a különböző Azure-régiókra vonatkozó ellenőrzési rekordban használandó DNS-zónák a következők:
  
  - Észak-Amerika, Európa, Szingapúr, Hongkong KKT, Japán:
      
    - `media.azure.net`
    - `verifydns.media.azure.net`
      
  - Kína
        
    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`
        
    Egy olyan `CName` rekord például, amely a "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" kifejezést a "verifydns.Media.Azure.net" értékre képezi, igazolja, hogy a 945a4c4e-28ea-45cd-8ccb-a519f6b700ad Media Services azonosítója rendelkezik a contoso.com tartományának tulajdonjogával, így Ha bármilyen nevet engedélyez a contoso.com területen, akkor az adott fiókhoz tartozó streaming-végpont egyéni állomásneveként használható. A Media Service ID értékének megkereséséhez nyissa meg a [Azure Portal](https://portal.azure.com/) , és válassza ki a Media Service-fiókját. A **fiók azonosítója** megjelenik az oldal jobb felső sarkában.
        
    Ha egy egyéni állomásnév beállítása a `CName` rekord megfelelő ellenőrzése nélkül történt meg, a DNS-válasz meghiúsul, majd egy ideig gyorsítótárazva lesz. Ha megfelelő rekord van érvényben, eltarthat egy ideig, amíg újra nem érvényesítette a gyorsítótárazott választ. Az egyéni tartomány DNS-szolgáltatójától függően a rekord újraellenőrzéséhez néhány perctől akár egy óráig is eltarthat.
        
    `CName` Az adott `<accountId>.<parent domain>` `sports.contoso.com` `CName` leképezésen kívül létre kell hoznia egy másikat, amely az egyéni állomásnevet (például) az Media Services streaming-végpont állomásneve (például: `verifydns.<mediaservices-dns-zone>` `amstest-usea.streaming.media.azure.net`).
 
    > [!NOTE]
    > Az ugyanabban az adatközpontban található adatfolyam-végpontok nem oszthatják meg ugyanazt az egyéni állomásnevet.

    A Media Services jelenleg nem támogatja az SSL-t az egyéni tartományokkal. 
    
- `maxCacheAge`– Felülbírálja a streaming végpont által a média töredékeken és az igény szerinti jegyzékeken beállított alapértelmezett Max-Age HTTP-gyorsítótár-vezérlő fejlécet. Az érték másodpercben van megadva.
- `resourceState` -

    - Leállítva – a streaming végpont kezdeti állapota a létrehozás után
    - Indítás – a futó állapotba való áttérés folyamatban van
    - A fut – tartalmat továbbíthat az ügyfeleknek
    - Skálázás – a méretezési egységek növelése vagy csökkentése folyamatban van
    - Leállítás – a leállított állapotba való áttérés folyamatban van
    - Törlés – törlése folyamatban van
    
- `scaleUnits`-Az 200 Mbps-os növekményekben megvásárolható dedikált kimenő kapacitást biztosít. Ha **prémium** szintű típusra szeretne lépni, módosítsa `scaleUnits`a következőt:.

## <a name="working-with-cdn"></a>A CDN használata

A legtöbb esetben engedélyezve kell lennie a CDN-nek. Ha azonban egyidőben 500 nézőnél kevesebb várható, akkor ajánlott letiltani a CDN-t, mert a CDN a legjobban az egyidejűséghez skálázható.

### <a name="considerations"></a>Megfontolandó szempontok

* A folyamatos átviteli `hostname` végpont és a folyamatos átviteli URL-cím ugyanaz marad, függetlenül attól, hogy engedélyezi-e a CDN-t.
* Ha a tartalmat CDN használatával vagy anélkül szeretné tesztelni, létrehozhat egy másik, CDN-t nem támogató streaming-végpontot.

### <a name="detailed-explanation-of-how-caching-works"></a>A gyorsítótárazás működésének részletes ismertetése

A CDN hozzáadásakor nincs meghatározott sávszélesség-érték, mert a CDN-kompatibilis streaming-végponthoz szükséges sávszélesség mennyisége változhat. Sok a tartalom típusától, a népszerűtől, a bitrátától és a protokolloktól függ. A CDN csak a kérések gyorsítótárazását végzi. Ez azt jelenti, hogy a népszerű tartalmak közvetlenül a CDN-ből lesznek kézbesítve – ha a videó töredékét gyorsítótárazzák. Az élő tartalom valószínűleg gyorsítótárazva lesz, mert általában sok ember nézi pontosan ugyanazt a dolgot. Az igény szerinti tartalom egy kicsit bonyolultabb lehet, mert olyan tartalmakat is használhat, amelyek népszerűek, és nem. Ha több millió videós eszközt is tartalmaz, ahol egyikük sem népszerű (csak 1 vagy 2 néző egy héten), de több ezer ember nézi a különböző videókat, a CDN sokkal kevésbé lesz hatékony. Ha ez a gyorsítótár hiányzik, növelje a streaming végpont terhelését.
 
Azt is meg kell fontolnia, hogyan működik az adaptív adatfolyam. A rendszer minden egyes videó részletét gyorsítótárazza saját entitásként. Ha például egy adott videó első alkalommal van megtekintve, a személy csak néhány másodpercet hagy el itt, és csak a videó töredékek vannak társítva a CDN-ben a megfigyelt személyhez. Az adaptív streaming esetében általában 5 – 7 különböző videó-bitrátát használhat. Ha egy személy figyeli az egyik bitrátát, és egy másik személy más bitrátát figyel, akkor a CDN-ben külön-külön vannak gyorsítótárazva. Még akkor is, ha két személy nézi ugyanazt a sávszélességet, a különböző protokollokon keresztül lehet adatfolyamként szolgálni. Az egyes protokollokat (HLS, MPEG-DASH, Smooth Streaming) külön gyorsítótárazza a rendszer. Így az egyes bitráták és protokollok külön vannak gyorsítótárazva, és csak a kért videó-töredékek vannak gyorsítótárazva.

### <a name="enable-azure-cdn-integration"></a>Azure CDN integráció engedélyezése

A CDN-vel való kiépített folyamatos átviteli végpontok esetében a várakozási idő Media Services a DNS-frissítés végrehajtása előtt, hogy a rendszer leképezje a folyamatos átviteli végpontot a CDN-végpontra.

Ha később le kívánja tiltani/engedélyezni szeretné a CDN-t, a folyamatos átviteli végpontnak **leállított** állapotban kell lennie. Akár két óráig is eltarthat, amíg a Azure CDN integrációja engedélyezve lesz, és a módosítások az összes CDN-pop-ban aktívak lesznek. Az adatfolyam-végpontot és a streamet azonban megszakítások nélkül is elindíthatja a streaming végpontról, és az integráció befejezését követően a stream a CDN-ből lesz továbbítva. A kiépítési időszak alatt a folyamatos átviteli végpont **kezdő** állapotba kerül, és megfigyelheti a teljesítményt.

A standard folyamatos átviteli végpont létrehozásakor a rendszer alapértelmezés szerint a standard Verizon-t konfigurálja. A Premium Verizon vagy a standard szintű Akamai-szolgáltatók a REST API-k használatával konfigurálhatók. 

A CDN-integráció az összes Azure-adatközpontban engedélyezve van, kivéve Kínát és a szövetségi kormányzati régiókat.

> [!IMPORTANT]
> A Azure Media Services integrációja Azure CDN-mel a Verizon standard streaming **-** végpontok Azure CDNján valósul meg. A prémium szintű streaming végpontok az összes **Azure CDN árképzési szint és szolgáltató**használatával konfigurálhatók. Azure CDN szolgáltatásokkal kapcsolatos további információkért tekintse meg a [CDN áttekintését](../../cdn/cdn-overview.md).

### <a name="determine-if-dns-change-has-been-made"></a>Annak megállapítása, hogy létrejött-e a DNS-módosítás

Megadhatja, hogy egy folyamatos átviteli végponton történt-e a DNS-módosítás (a forgalom a Azure CDNra van irányítva) a használatával https://www.digwebinterface.com. Ha az eredmények azureedge.net az eredmények között, a forgalom mostantól a CDN-re mutat.

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="next-steps"></a>További lépések

Az [ebben a tárházban található](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) minta bemutatja, hogyan indíthatja el az alapértelmezett adatfolyam-végpontot a .net-tel.

