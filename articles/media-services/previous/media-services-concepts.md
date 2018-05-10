---
title: Az Azure Media Services alapfogalmaiért |} Microsoft Docs
description: Ez a témakör áttekintést nyújt az Azure Media Services Alapfogalmaiért
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: dcefc8bc-e2ea-4b38-a643-9010f4436fb5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: juliako
ms.openlocfilehash: cfc8ac74f626ada30dda72b58e4b4bc7f0d9e806
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="azure-media-services-concepts"></a>Az Azure Media Services alapfogalmaiért
Ez a témakör áttekintést nyújt a Media Services egyik legfontosabb fogalmak.

## <a name="a-idassetsassets-and-storage"></a><a id="assets"/>Eszközök és tárolás
### <a name="assets"></a>Objektumok
Egy [eszköz](https://docs.microsoft.com/rest/api/media/operations/asset) digitális fájlok (például a videó, hang, képeket, miniatűröket, szöveges nyomon követi és feliratfájlokat fájlok) és a mindezen fájlok metaadatait tartalmazza. Miután a digitális fájlok feltöltése az objektumba, akkor a Media Services kódolási és adatfolyam-munkafolyamatok is használható.

Az eszköz egy blob-tárolóba, az Azure Storage-fiók van hozzárendelve, és az adott tároló blokkblobként tárolja a fájlokat az eszköz. Lapblobokat nem Azure Media Services által támogatott.

Amikor eldönti, milyen Médiatartalom feltöltése és tárolása egy eszköz, a következők érvényesek:

* Az eszköz csak egyetlen, egyedi példánya médiatartalom kell tartalmaznia. Például egy egyetlen Szerkesztés TV epizód, movie vagy hirdetmény.
* Egy eszköz nem tartalmazhat több interpretációk vagy audiovizuális fájlok módosításokat. Például egy nem megfelelő használata egy eszköz akkor kell tárolására tett kísérlet egynél több TV epizód, a hirdetés vagy a több kamera beállítások egy eszköz belül egyetlen gyártási. Egy eszköz több interpretációk vagy módosításokat audiovizuális fájlok tárolása kódolási feladatok, streaming és biztonságossá tétele az eszköz később a munkafolyamatban kézbesítési elküldése nehézségek eredményezhet.  

### <a name="asset-file"></a>Eszköz fájl
Egy [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) egy video- vagy fájl a blob-tárolóban tárolt jelöli. Egy eszköz fájl mindig társítva van egy eszköz, és egy eszköz egy vagy több fájlt tartalmaz. A Media Services kódoló feladat sikertelen lesz, ha egy eszköz fájl objektumhoz nincs társítva egy digitális fájlhoz egy blob-tárolóban.

A **AssetFile** példány és a tényleges médiafájl két különböző objektum. A AssetFile példány media fájl metaadatainak tartalmaz, míg a médiafájl tartalmazza a tényleges médiatartalmakat.

Ne próbáljon Media Service API-k használata nélkül Media Services által előállított blob-tároló tartalmának módosításához.

### <a name="asset-encryption-options"></a>Eszköz titkosítási beállítások
Attól függően, hogy tartalom feltöltése, tárolására és biztosításához kívánt típusát a Media Services biztosít a különböző titkosítási beállításokat, amelyek közül választhat.

>[!NOTE]
>Nincs titkosítás. Ez az alapértelmezett érték. Ez a beállítás használatakor a tartalom nem védett átvitel, sem tárolás közben.

Ha azt tervezi, hogy egy MP4 progresszív letöltés, használja a beállítást használja a tartalmak feltöltéséhez.

**StorageEncrypted** – használja ezt a beállítást, hogy a tiszta tartalom helyileg az AES 256 bites titkosítás használata, és ezután töltse fel az Azure Storage helyén titkosítása. Storage-titkosítással védett adategységek automatikusan a titkosítás és helyezni egy titkosított fájlrendszerbe kódolás előtt, és egy új kimeneti eszközként feltöltés előtt esetleg újra titkosítja. A tárolás titkosítása elsődleges használati eset az, amikor biztonságossá tételéhez a kiváló minőségű bemeneti médiafájljait erős titkosítással aktívan a lemezen. 

A tárolási titkosított eszköz kezelni, konfigurálnia kell az adategység továbbítási házirendjét, a Media Services tudja, hogyan kívánja a tartalom. Mielőtt az eszköz továbbítható, a streamelési kiszolgáló eltávolítja a tárolás titkosítása, és az adatfolyamokat a tartalmat a megadott továbbítási házirendjét (például AES, PlayReady vagy titkosítás nélkül) használatával. 

**CommonEncryptionProtected** -használja ezt a beállítást, ha szeretné titkosítani (vagy már titkosítva feltöltése) tartalom általános titkosítás vagy a PlayReady DRM által (például védett Smooth Streaming egy PlayReady DRM).

**EnvelopeEncryptionProtected** – használja ezt a beállítást, ha szeretné védeni (vagy már védett feltöltése) HTTP Live Streaming (HLS) titkosítva az Advanced Encryption Standard (AES). Ha már AES titkosított HLS, akkor titkosították Transform Manager használatával.

### <a name="access-policy"></a>Hozzáférési házirend
Egy [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy) egy eszközhöz engedélyek (például az olvasási, írási és lista) és a hozzáférés időtartamát határozza meg. AccessPolicy objektum általában egy kereső, majd az eszköz tárolt fájlok eléréséhez használandó volna át.

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja (például olyan keresők szabályzatait, amelyek hosszú ideig érvényben maradnak, vagyis nem feltöltött szabályzatokat), a szabályzatazonosítónak is ugyanannak kell lennie. További információ [ebben](media-services-dotnet-manage-entities.md#limit-access-policies) a témakörben érhető el.

### <a name="blob-container"></a>A BLOB-tároló
A blob-tároló blobok blobkészletek csoportosítását biztosítja. BLOB tárolók használatosak a Media Services határ pont a hozzáférés-vezérlés és a közös hozzáférésű Jogosultságkód (SAS) lokátorokat eszközök. Egy Azure Storage-fiók korlátlan számú blob tárolók is tartalmazhat. Egy tároló korlátlan számú blob tárolására használható.

>[!NOTE]
> Ne próbáljon Media Service API-k használata nélkül Media Services által előállított blob-tároló tartalmának módosításához.
> 
> 

### <a name="a-idlocatorslocators"></a><a id="locators"/>Keresők
[Lokátor](https://docs.microsoft.com/rest/api/media/operations/locator)s adjon meg egy belépési pont egy eszköz tárolt fájlok eléréséhez. Hozzáférési házirend engedélyek és, hogy egy ügyfél hozzáfér egy adott eszköz időtartama meghatározására szolgál. Keresők rendelkezhet több az egyhez kapcsolatban áll egy hozzáférési házirendben, úgy, hogy a különböző keresők biztosíthat eltérő kezdési ideje és kapcsolattípusok különböző ügyfelek használatakor minden ugyanazt az engedélyt és időtartama beállítások; azonban a megosztott elérési házirend korlátozása az Azure storage szolgáltatások által beállított, miatt nem lehet egy adott eszközhöz társított egyszerre legfeljebb öt egyedi lokátorokat. 

Media Services két lokátortípust támogat: OnDemandOrigin keresők media (például MPEG DASH, HLS vagy Smooth Streaming) vagy fokozatosan letölteni a média és a SAS URL-cím keresők feltölteni, vagy töltse le a media fájlok to\from az Azure storage használatával. 

>[!NOTE]
>A lista engedélyt (AccessPermissions.List) nem használható az OnDemandOrigin lokátor létrehozása során. 

### <a name="storage-account"></a>Tárfiók
Azure Storage minden hozzáférés a storage-fiók segítségével történik. Egy Media Services-fiókját tárfiókok egy vagy több társíthatja. Egy fiók tartalmazhat egy korlátlan számú tárolót, mindaddig, amíg a teljes mérete a tárfiók 500TB.  A Media Services SDK szintű tooling lehetővé teszi több storage-fiókok kezelése, és ezekhez a fiókokhoz való feltöltés során az eszközök eloszlását alapján metrikák vagy véletlenszerű terjesztési terheléselosztásához biztosít. További információ kapcsolatban a Working with [Azure Storage](https://msdn.microsoft.com/library/azure/dn767951.aspx). 

## <a name="jobs-and-tasks"></a>Feladatok és feladatok
A [feladat](https://docs.microsoft.com/rest/api/media/operations/job) főként a folyamat (például index vagy kódolni) egy hang-és videófolyamot bemutató. Több videók feldolgozása esetén, hozzon létre egy feladatot az egyes videó kódolni kell.

A feladat végrehajtását feldolgozásával kapcsolatos metaadatokat tartalmaz. Minden feladat tartalmaz egy vagy több [feladat](https://docs.microsoft.com/rest/api/media/operations/task)s, amely egy atomi feldolgozási feladatot, a bemeneti eszközeinek, adja meg a kimeneti eszközök, media processzort és a kapcsolódó beállításokat. Egy feladat feladatok elérése együtt, ahol a kimeneti adategységen egy feladat a bemeneti eszköz a következő tevékenységhez megadott van. Így egy feladat minden szükséges media feldolgozása tartalmazhat.

## <a id="encoding"></a>Kódolás
Az Azure Media Services a felhőben media kódolás több lehetőséget biztosít.

A Media Services kezdte meg, fontos kodekeket és a fájl formátumok közötti különbségek megértése.
A kodekeket a szoftver, amely megvalósítja a tömörítési/kitömörítés algoritmusok, mivel fájlformátumok tárolói tartsa a tömörített videó.

Media Services dinamikus becsomagolást, amely lehetővé teszi, hogy az adaptív sávszélességű MP4 vagy Smooth Streaming-kódolású tartalmak (MPEG DASH, HLS, Smooth Streaming), a Media Services által támogatott streamformátumok biztosít anélkül, hogy át kellene őket csomagolni ezekbe a streamformátumokba.

Előnyeit [dinamikus becsomagolás](media-services-dynamic-packaging-overview.md), kódolja a mezzanine (forrás) fájlt az adaptív sávszélességű MP4 vagy adaptív sávszélességű Smooth Streaming-fájlsorozattá, és van legalább egy standard adatfolyam-továbbítási végpontra a prémium szintű indítása vagy állapotát.

A Media Services a következő igény szerinti kódolók, amelyek szerepelnek ebben a cikkben támogatja:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Media Encoder Premium-munkafolyamat](media-services-encode-asset.md#media-encoder-premium-workflow)

További információ a támogatott kódolók: [kódolók](media-services-encode-asset.md).

## <a name="live-streaming"></a>Élő adások online közvetítése
Azure Media Services a csatorna élő adatfolyam-tartalmak feldolgozására adatcsatorna jelöli. Egy csatorna élő bemeneti adatfolyamok megkapja az alábbi két módszer egyikével:

* Egy helyszíni élő kódoló többszörös sávszélességű RTMP vagy Smooth Streaming (töredékes MP4) küld a csatornát. Használhatja a következő élő kódolók képesek kimeneti többféle sávszélességű Smooth Streaming: MediaExcel, Ateme, kommunikációs képzelhető el, Envivo, Cisco és elemi. RTMP-kimenetre a következő élő kódolók képesek: Adobe Flash Live kódoló, Telestream Wirecast, Teradek, Haivision és Tricaster kódolók. A feldolgozott adatfolyamok haladnak át csatornák további átkódolás és kódolás nélkül. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.
* Egy egyféle sávszélességű adatfolyamot (a következő formátumok egyikében: RTP (MPEG-TS)), RTMP vagy Smooth Streaming (töredékes MP4)) küld a csatornának, amelyen engedélyezve van-e a Media Services élő kódolás. A csatorna ezután a bejövő egyfajta sávszélességű adatfolyamot élő kódolás útján többféle sávszélességű (adaptív) video-adatfolyammá alakítja. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.

### <a name="channel"></a>Csatorna
A Media Services szolgáltatásban [csatorna](https://docs.microsoft.com/rest/api/media/operations/channel)s felelősek az élő adatfolyam-tartalmak feldolgozása. Egy csatornát biztosít a bemeneti végpontja (feldolgozó URL-CÍMÉT), akkor adja meg az élő transcoder. A csatorna élő transcoder bemeneti élő adatfolyamokat fogad, és lehetővé teszi az adatfolyamként történő egy vagy több Streamvégpontok keresztül. Csatornák előnézeti végpont (előzetes verzió URL-cím), amelyekkel előzetes megtekintéséhez és az adatfolyam érvényesítése előtt további feldolgozás és a szállítási is megadhatja.

A bemeneti URL-címet és a kép URL-címet a csatorna létrehozásakor kérheti le. Ahhoz, hogy az URL-címek, a csatorna nem kell a elindított állapotban. Amikor készen áll a start kérdez le adatokat az élő transcoder a csatornának, el kell indítani a csatornát. Ha az élő transcoder megkezdi az adatok bevitele, megtekintheti az adatfolyam.

Minden egyes Media Services-fiók több csatornát, több alkalmazás, és több Streamvégpontok tartalmazhat. Attól függően, hogy a sávszélesség- és biztonsági igényeket StreamingEndpoint szolgáltatások is lehetne rendelni egy vagy több csatornát. Bármely StreamingEndpoint is lekéréses bármely csatornán.

### <a name="program-event"></a>A program (esemény)
A [(esemény) Program](https://docs.microsoft.com/rest/api/media/operations/program) teszi lehetővé a közzétételt és élő stream szegmenseinek tárolását. Programok (esemény) a csatornák kezelik. A csatornák és programok viszonya hasonló a hagyományos televíziózáshoz, ahol egy csatorna rendelkezik a tartalom állandó adatfolyam, és a program hatóköre a következő néhány időzített esemény adott csatornán.
Megadhatja, hogy hány órán át szeretné megőrizni a program felvett tartalmát úgy, hogy a **ArchiveWindowLength** tulajdonság. Ez az érték 5 perc és 25 óra közötti lehet.

ArchiveWindowLength is határozzák meg, hogy az ügyfelek maximális száma ugorhatnak vissza az idő, az az aktuális élő pozíciótól. Az események hosszabbak lehetnek a megadott időtartamnál, de a rendszer folyamatosan elveti azokat a tartalmakat, amelyek korábbiak a megadott időtartamnál. Ennek a tulajdonságnak az értéke határozza meg azt is, hogy milyen hosszúra nőhetnek az ügyfél jegyzékfájljai.

Minden program (esemény) társítva egy eszköz. A program közzétételéhez létre kell hoznia egy lokátort a társított objektumhoz. Ez a lokátor teszi lehetővé az ügyfeleknek megadható streamelő URL-cím összeállítását.

A csatornák három egyidejűleg zajló programot támogatnak, így egy bejövő streamből több archívumot is létre lehet hozni. Ez lehetővé teszi az események különféle részeinek szükség szerinti közzétételét és archiválását. Az üzleti igény szerint például 6 órát kell archiválni egy programból, de csak az utolsó 10 percet kell közvetíteni. Ezt két egyidejűleg zajló program létrehozásával érheti el. Ebben az esetben állítsa be az egyik programot az esemény 6 órájának archiválására, de ne tegye közzé. A másik programot 10 perc archiválására állítsa be, és tegye is közzé.

További információkért lásd:

* [Segítségével az Azure Media Services élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)
* [Helyszíni kódolóktól többszörös átviteli sebességű streameket fogadó csatornák használata](media-services-live-streaming-with-onprem-encoders.md)
* [Kvóták és korlátozások](media-services-quotas-and-limitations.md).

## <a name="protecting-content"></a>Tartalom védelme
### <a name="dynamic-encryption"></a>Dinamikus titkosítás
Az Azure Media Services lehetővé teszi a media a tárhely, feldolgozás és kézbesítési keresztül elhagyja óta. A Media Services lehetővé teszi, hogy a tartalom Advanced Encryption Standard (AES) (a 128 bites titkosítási kulcsok használatával) és a közös titkosítás (CENC) PlayReady és/vagy Widevine DRM segítségével dinamikusan titkosított. A Media Services AES-kulcsok és a PlayReady-licencek kézbesítéséhez hitelesített ügyfelek szolgáltatást is nyújt.

Jelenleg titkosíthatja a következő formátumban: HLS, MPEG DASH vagy Smooth Streaming. Progresszív letöltés nem titkosítható.

Ha azt szeretné, a Media Services az objektum titkosítására, meg kell rendelje hozzá egy titkosítási kulcsot (CommonEncryption vagy EnvelopeEncryption) az eszköz és engedélyezési házirendeket, a kulcs is konfigurálhatja.

Ha azt szeretné, hogy adatfolyamként küldje el a tárolási titkosított eszköz, konfigurálnia kell az adategység továbbítási házirendjét ahhoz, hogy határozza meg, hogyan képes biztosítani az objektumot.

Ha olyan adatfolyamot kell megadni a Windows Media Player van szükség, Media Services a megadott kulcs dinamikusan titkosítani a használatával egy boríték (az AES) vagy közös titkosítást (a PlayReady vagy Widevine) használja. Az adatfolyam visszafejtése, a Windows Media player a kulcs kézbesítési szolgáltatás fog igényelni a kulcsot. Döntse el, hogy a felhasználó jogosult-e a kulcs eléréséhez, hogy a szolgáltatás értékeli az engedélyezési házirendeket, amelyek a kulcshoz megadott.

### <a name="token-restriction"></a>Token korlátozása
A tartalomkulcs-hitelesítési házirend rendelkezhet egy vagy több engedélyezési korlátozás: Nyissa meg a, lexikális elem: korlátozás vagy IP-korlátozás. A tokennel korlátozott szabályzatokhoz a Secure Token Service (Biztonsági jegykiadó szolgáltatás, STS) által kiadott tokennek kell tartoznia. A Media Services Simple Web Tokens (SWT) és a JSON webes jogkivonat (JWT) formátumú tokeneket támogatja. A Media Services nem nyújt Secure Token szolgáltatásokat. Hozzon létre egy egyéni STS, vagy probléma jogkivonatokat a Microsoft Azure ACS kihasználja. Az STS be kell állítani a megadott kulcs és a probléma JOGCÍMEKKEL, amely a token korlátozás konfigurációjában megadott aláírt jogkivonat létrehozásához. A Media Services kulcs kézbesítési szolgáltatás visszaáll a kért (vagy licencelési) az ügyfél, ha a jogkivonat érvényes, és a jogcímek, az token találat azokat konfigurált kulcsot (vagy licenc).

Amikor a jogkivonat konfigurálása korlátozzák a házirend, az elsődleges hitelesítési kulcs, a kibocsátó és a célközönség paramétereket kell megadnia. Az elsődleges hitelesítési kulcs, amely a token aláírt kulcsot tartalmazza, a kibocsátó a biztonságos biztonságijogkivonat-szolgáltatás, amely kibocsátja a jogkivonatot. A célközönség (más néven hatókör) ismerteti a jogkivonat a leképezést, vagy az erőforrás a token engedélyezi a hozzáférést. A Media Services kulcs kézbesítési szolgáltatás ellenőrzi, hogy ezek az értékek a token egyeznek-e a sablonban szereplő értékeket.

További információkért tekintse át a következő cikkeket:
- [Védelme tartalom áttekintése](media-services-content-protection-overview.md)
- [AES-128 védelme](media-services-protect-with-aes128.md)
- [A PlayReady vagy Widevine védelme](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>Továbbítása
### <a name="a-iddynamicpackagingdynamic-packaging"></a><a id="dynamic_packaging"/>A dinamikus csomagolás
Amikor olyan Media Services, javasoljuk, hogy a mezzanine fájlok kódolása egy adaptív sávszélességű MP4 állítsa be, és a set átalakítása a kívánt formátum használatával az [dinamikus becsomagolás](media-services-dynamic-packaging-overview.md).

### <a name="streaming-endpoint"></a>Streamvégpont
Egy StreamingEndpoint jelöli egy adatfolyam-szolgáltatás által biztosított tartalom közvetlenül player ügyfélalkalmazást, vagy az a Content Delivery Network (CDN) későbbi terjesztés (Azure Media Services mostantól az Azure CDN integrációt biztosít.) A kimenő adatfolyam adatfolyam-továbbítási végpont szolgáltatásból egy élő adatfolyam, vagy a Media Services-fiók egy videó igény eszköz lehet. A Media Services ügyfelei általában egy **standard** szintű streamvégpontot vagy egy vagy több **prémium** szintű streamvégpontot választanak, saját igényeiknek megfelelően. Adatfolyam-továbbítási végpontra szabvány az adatfolyam-továbbítási munkaterhelések többségéhez alkalmas. 

A szabványos streamvégpont a legtöbb streamelési feladat ellátására alkalmas. Standard adatfolyam-végpontok a tartalmat továbbít a dinamikus becsomagolás kifejezést HLS, MPEG-DASH, Smooth Streaming, valamint a dinamikus titkosítás Microsoft PlayReady, Google Widevine, Apple Fairplay és az AES128 használatával szinte minden eszköz rugalmasságot kínálnak.  Ezek is méretezhető nagyon Small Azure CDN integrálásán keresztül egyidejű megjelenítők százait nagyon nagy célközönség. Ha egy speciális munkaterhelés vagy az adatfolyam-továbbítási kapacitásigények nem férnek el standard adatfolyam végpont átviteli tárolókra vagy a kapacitás a StreamingEndpoint szolgáltatás növekvő szabályozni szeretné sávszélesség van szüksége, javasoljuk, hogy a méretezési egységeket (más néven prémium streamelési egységek) lefoglalni.

A dinamikus csomagolás és/vagy a dinamikus titkosítás használata ajánlott.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 

További információ [ebben](media-services-portal-manage-streaming-endpoints.md) a témakörben érhető el.

Alapértelmezés szerint akkor is legfeljebb 2 adatfolyam-végpontok a Media Services-fiókhoz. A kérelem egy magasabb megengedett, lásd: [kvóták és korlátozások](media-services-quotas-and-limitations.md).

Csak számlázása, amikor a StreamingEndpoint futó állapotban van.

### <a name="asset-delivery-policy"></a>Objektumtovábbítási szabályzat
A lépések a Media Services-továbbítási munkafolyamat konfigurálását [továbbítási házirendjeit eszközök ](https://docs.microsoft.com/rest/api/media/operations/assetdeliverypolicy)közzétett kívánt. Hogyan szeretné az eszköz kézbesítendő közli az adategység továbbítási házirendjét Media Services: be kell az eszköz dinamikusan csomagolható (például MPEG DASH, HLS, Smooth Streaming, vagy az összes), az eszköz dinamikusan titkosítani szeretné-e, és hogy melyik adatfolyam-protokoll (boríték vagy közös titkosítási).

Ha egy tárolási titkosított eszközt, mielőtt az eszköz továbbítható, a streamelési kiszolgáló a tárolás titkosítása eltávolítja, és az adatfolyamokat, a tartalom a megadott objektumtovábbítási szabályzat segítségével. Például az Advanced Encryption Standard (AES) titkosítási kulccsal titkosított objektumot, hogy állítsa a házirend típusát DynamicEnvelopeEncryption. Tárolás titkosítása és adatfolyamként küldje el az eszköz szövegként, állítsa be a házirend típusát a NoDynamicEncryption.

### <a name="progressive-download"></a>Progresszív letöltés
Progresszív letöltés media előtt a teljes fájl letöltött lejátszásához teszi lehetővé. MP4-fájlokat csak fokozatosan lehet letölteni.

>[!NOTE]
>Ha a progresszív letöltés érhető el, hogy vissza kell fejtenie titkosított eszközökre.

Biztosít a felhasználók progresszív letöltési URL-címeket, akkor először létre kell hoznia egy OnDemandOrigin lokátort. A lokátor létrehozása, lehetővé teszi az alap elérési útja az eszközhöz. Majd szeretne hozzáfűzni MP4-fájl nevét. Példa:

http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

### <a name="streaming-urls"></a>Streamelési URL-ek
Adatfolyam-ügyfelek számára a tartalom. Streamelési URL-címek biztosít a felhasználók, akkor először hozzon létre egy OnDemandOrigin lokátort. A lokátor létrehozása, lehetővé teszi az alap elérési útja az eszközhöz, amely tartalmazza a tartalmat továbbítani kívánja. Azonban képesek lesznek streamelni ehhez a tartalomhoz szüksége további az elérési út módosítása. A teljes URL-címet a folyamatos átviteli jegyzékfájl létrehozásához a lokátor elérési út érték és a jegyzékfájl (filename.ism) kell összefűzésére fájl nevét. A lokátor elérési utat, majd hozzáfűzése /Manifest és megfelelő formátumú (ha szükséges).

Is SSL-kapcsolaton keresztül adatfolyam formájában a tartalmat. Ehhez ellenőrizze, hogy a streamelési URL-címének HTTPS kezdődhet. Jelenleg az AMS nem támogatja az SSL az egyéni tartomány.  

>[!NOTE]
>Akkor is csak adatfolyam SSL-en keresztül Ha a streamvégpontján, amelyről a tartalmat továbbít a 2014. szeptember 10 után készült. A streamelési URL-címeket a 10. szeptember után létrehozott streamvégpontok alapulnak, hogy az URL-címet tartalmazza-e "streaming.mediaservices.windows.net" (a új formátum). Adatfolyam-továbbítási URL-címek, amelyek tartalmazzák a "origin.mediaservices.windows.net" (a régi formátumot) nem támogatja az SSL. Ha a régi formátumban kell megadni az URL-címet, és képesek lesznek streamelni az SSL-en keresztül szeretné, hozzon létre egy új streamvégpontra. Az új streamvégpont alapján létre URL-címek használatával a adatfolyamként SSL-en keresztül.

Az alábbi lista ismerteti a különböző formátumban, és a példákat mutat be:

* Smooth Streaming

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

* MPEG DASH

{streaming endpoint név-media services fiók name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

* Apple HTTP élő adatfolyam-továbbítási (HLS) V4

{streaming endpoint név-media services fiók name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

* Apple HTTP élő adatfolyam-továbbítási (HLS) V3

{streaming endpoint név-media services fiók name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

