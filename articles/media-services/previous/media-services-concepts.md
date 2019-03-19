---
title: Az Azure Media Services alapfogalmaiért |} A Microsoft Docs
description: Ez a témakör áttekintést nyújt az Azure Media Services – alapelvek
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 79de09631b33f838c1a2f12fdc58b2648548004b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57998425"
---
# <a name="azure-media-services-concepts"></a>Az Azure Media Services – alapelvek 

Ez a témakör áttekintést nyújt a Media Services legfontosabb fogalmakat.

## <a name="a-idassetsassets-and-storage"></a><a id="assets"/>Eszközök és tárolás
### <a name="assets"></a>Objektumok
Egy [eszköz](https://docs.microsoft.com/rest/api/media/operations/asset) mindezen fájlok metaadatait és a digitális fájlok (beleértve a videókhoz, audiofájlokhoz, képeket, miniatűröket, szövegsávok és a feliratfájlok) tartalmaz. Miután a digitális fájlok feltöltése egy adategységbe, azok a Media Services encoding és adatfolyam-munkafolyamatok használható.

Az eszköz le van képezve egy blob-tárolóba az Azure Storage-fiókban, és a tárolóban blokkblobként tárolja a fájlokat, az eszközben. A lapblobok az Azure Media Services által nem támogatottak.

Amikor eldönti, milyen médiatartalmak feltöltésére és a egy eszköz tárolására, a következő szempontokat kell figyelembe venni:

* Egy eszköz csak egyetlen, egyedi példány médiatartalmak tartalmaznia kell. Ha például egy egyetlen szerkesztése TV epizód, filmet vagy hirdetmény.
* Egy eszköz nem tartalmazhat több verzió vagy szerkesztheti egy audiovizuális fájlt. Például egy eszköz nem megfelelő használata a volna egynél több TV epizód, hirdetés vagy egy eszköz belül egyetlen gyártási több kamera beállítások tárolására tett kísérlet. Több verzió vagy szerkesztheti egy audiovizuális fájlt tárolja egy eszköz elküldése kódolási feladatot, a streamelési és a szállítási később a munkafolyamatban az eszköz biztonságossá tételéhez nehézségek eredményezhet.  

### <a name="asset-file"></a>Objektumfájl
Egy [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) egy videó vagy hang-fájl, amely a blobtárolóban tárolt jelöli. Egy eszköz fájl mindig egy eszköz társítva, és az eszköz egy vagy több fájlt tartalmaz. A Media Services Encoder feladat sikertelen lesz, ha egy eszköz fájl objektumhoz nem kapcsolódik egy digitális fájlhoz a blobtárolóban.

A **AssetFile** -példány és a tényleges médiafájl két különböző objektumot. A AssetFile-példány a médiafájl kapcsolatos metaadatokat tartalmaz, amíg az adathordozó-fájl tartalmazza a tényleges médiatartalmakat.

Meg nem próbálja meg módosítani a Media Services Media Service API-k használata nélkül létrehozott blob-tárolók tartalmát.

### <a name="asset-encryption-options"></a>Az eszközintelligencia titkosítási beállítások
Tartalom feltöltése, tárolása és kézbesíteni szeretné típusától függően a Media Services biztosít a különböző titkosítási lehetőségeket, amelyek közül választhat.

>[!NOTE]
>Nincs titkosítás. Ez az alapértelmezett érték. Ez a beállítás használatakor a tartalom nem védett átvitel, sem tárolás.

Ha azt tervezi, hogy egy MP4-fájlt progresszív letöltés, használja ezt a beállítást a tartalmak feltöltésére.

**StorageEncrypted** – használja ezt a beállítást a tiszta tartalom helyileg, 256 bites AES-titkosítás titkosításához, és ezután töltse fel az Azure Storage helyén titkosítása. Storage-titkosítással védett adategységek automatikus a titkosítás és a kódolás előtt egy titkosított fájlrendszerbe kerülnek, és igény szerint újra titkosítja; az új kimeneti adategységként való feltöltés előtt. Az elsődleges használati eset, a tárolás titkosítása akkor, ha a kiváló minőségű bemeneti fájlok az erős titkosítás inaktív állapotban a lemezen. 

Annak érdekében, hogy a storage titkosított eszköz kézbesítéséhez, konfigurálnia kell az adategység továbbítási házirendjét, a Media Services tudja, hogyan szeretné a tartalmat. Az eszközintelligencia továbbítható, mielőtt a streamelési kiszolgáló eltávolítja a tárolás titkosítása, és streameli a tartalom a megadott továbbítási szabályzatát (például AES, a PlayReady vagy titkosítás nélkül). 

**CommonEncryptionProtected** -használja ezt a beállítást, ha szeretné titkosítani (vagy már a titkosított feltöltés) általános titkosítás vagy a PlayReady DRM (például védett Smooth Streaming PlayReady DRM-) tartalom.

**EnvelopeEncryptionProtected** – használja ezt a beállítást, ha azt szeretné, védelme (vagy az már védett feltöltése) HTTP Live Streaming (HLS) titkosítva az Advanced Encryption Standard (AES). Ha AES már titkosított HLS tölt fel, hogy titkosították Transform Manager használatával.

### <a name="access-policy"></a>Hozzáférési szabályzat
Egy [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy) engedélyek (például olvasási, írási és listában) és a hozzáférés időtartama meghatározása egy objektumba történik. Általában egy lokátort, majd használni kívánt eléréséhez az eszközintelligencia mappában található fájlokat a AccessPolicy objektum kellene átadnia.

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja (például olyan keresők szabályzatait, amelyek hosszú ideig érvényben maradnak, vagyis nem feltöltött szabályzatokat), a szabályzatazonosítónak is ugyanannak kell lennie. További információ [ebben](media-services-dotnet-manage-entities.md#limit-access-policies) a témakörben érhető el.

### <a name="blob-container"></a>BLOB-tároló
A blob-tároló egy készlet a blobok csoportosítását biztosítja. BLOB-tárolók használ a Media Services határ pontként hozzáférés-vezérléshez és a közös hozzáférésű Jogosultságkód (SAS) keresők eszközökből. Azure-tárfiók korlátlan számú blob-tárolók is tartalmazhat. Egy tároló korlátlan számú blob tárolására használható.

>[!NOTE]
> Meg nem próbálja meg módosítani a Media Services Media Service API-k használata nélkül létrehozott blob-tárolók tartalmát.
> 
> 

### <a name="a-idlocatorslocators"></a><a id="locators"/>Keresők
[Kereső](https://docs.microsoft.com/rest/api/media/operations/locator)s adjon meg egy belépési pont eléréséhez az eszközintelligencia mappában található fájlokat. Hozzáférési házirendek az engedélyek és időtartama, hogy egy ügyfél hozzáfér egy adott eszköz meghatározására szolgál. Lokátorok rendelkezhet több az egyhez kapcsolat egy hozzáférési szabályzattal együtt, hogy különböző lokátorok is biztosítanak különböző kezdési ideje és kapcsolattípusok különböző ügyfelek számára a ugyanezt az engedélyt és időtartama beállításainak; minden használata során azonban egy megosztott elérési szabályzat korlátozási is megszűnnek az Azure storage-szolgáltatások által beállított, miatt nem lehet több mint öt, egy adott eszköz egyszerre társított egyedi keresők. 

A Media Services két lokátortípust támogat: OnDemandOrigin keresők médiatartalmak (például MPEG DASH, HLS vagy Smooth Streaming) segítségével, vagy fokozatosan letölteni az adathordozót, a SAS URL-címet keresők fel- vagy letölteni media files to\from az Azure storage segítségével. 

>[!NOTE]
>A lista engedéllyel (AccessPermissions.List) nem használható egy OnDemandOrigin kereső létrehozásakor. 

### <a name="storage-account"></a>Tárfiók
Minden, az Azure Storage-hozzáférés tárfiókon keresztül történik. Egy Media Services-fiókját egy vagy több tárfiók is társíthat. Egy fiók korlátlan számú tárolók, tartalmazhat, mindaddig, amíg azok teljes mérete / storage-fiók alatt 500TB.  A Media Services SDK szolgáltatói eszközkészlet lehetővé teszi több tárfiók kezelése, és ezeket a fiókokat történő feltöltés közben az eszközök eloszlása a metrikák vagy véletlenszerű elosztás alapján webhelyen végezhet terheléselosztást biztosít. További információ kapcsolatban a Working with [Azure Storage](https://msdn.microsoft.com/library/azure/dn767951.aspx). 

## <a name="jobs-and-tasks"></a>Feladatok és tevékenységek
A [feladat](https://docs.microsoft.com/rest/api/media/operations/job) általában akkor használható, a folyamat (például index vagy kódolása) egy hang/kép bemutató. Több videó feldolgozása esetén, hozzon létre egy feladatot az egyes videók értéket kódolni kell.

Egy feladat tartalmaz metaadatokat, a végrehajtandó feldolgozást. Minden egyes feladat tartalmaz egy vagy több [feladat](https://docs.microsoft.com/rest/api/media/operations/task), amelyek egy atomi feldolgozási feladatot, a bemeneti eszközeinek, adja meg a kimeneti eszközök, egy médiaprocesszorral és a kapcsolódó beállításokat. A feladatokon belül feladatok összefűzhetők, ahol a kimeneti objektum egy feladat van megadva bemeneti objektuma következő feladata felé. Ezzel a módszerrel egy feladat az összes szükséges media feldolgozási tartalmazhat.

## <a id="encoding"></a>Kódolás
Az Azure Media Services media felhőalapú kódolási több lehetőséget biztosít.

A Media Services kezdi, amikor fontos kodekeket és a fájl formátumok közötti különbségek megértése.
A kodekeket, amely megvalósítja a tömörítés és kibontás algoritmusok, mivel fájlformátumok olyan tárolók, amelyek a tömörített videó tartsa a szoftvert.

Media Services dinamikus csomagolást, amely lehetővé teszi az adaptív sávszélességű MP4 vagy Smooth Streaming-kódolású tartalmak streamformátumok valamelyikében (MPEG DASH, HLS, Smooth Streaming), a Media Services által támogatott biztosít anélkül, hogy kellene csomagolni ezekbe adatfolyam-továbbítási formátumokba.

Kihasználásához [dinamikus csomagolási](media-services-dynamic-packaging-overview.md), kell kódolja a mezzanine (forrás) fájlt egy adaptív sávszélességű MP4-fájlokat vagy Smooth Streaming-fájlsorozattá és van legalább egy standard vagy prémium szintű streamvégpont állapot elindult.

A Media Services ebben a cikkben a következő igény szerinti kódolók ismertetett támogatja:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Media Encoder Premium-munkafolyamat](media-services-encode-asset.md#media-encoder-premium-workflow)

További információ a támogatott kódolók: [kódolók](media-services-encode-asset.md).

## <a name="live-streaming"></a>Élő adások online közvetítése
Az Azure Media Services esetén a csatorna egy olyan folyamatot jelent élőben közvetített tartalmak feldolgozása. Egy csatorna bemeneti élő Streamek kap a két módszer egyikével:

* A helyszíni élő kódoló és többféle sávszélességű RTMP vagy Smooth Streaming (töredékes MP4) küld a csatornát. Használhatja a következő élő kódolók, és többféle sávszélességű Smooth Streaming-kimenetre: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco és Elemental. A következő élő kódolók RTMP-kimenetre: Az Adobe Flash Live Encoder, Telestream Wirecast, Teradek, Haivision és Tricaster kódolókkal történő továbbítását. A feldolgozott adatfolyamok további átkódolása és kódolás nélkül csatornákon keresztül adja át. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.
* Egy egyféle sávszélességű adatfolyamot (a következő formátumok egyikében: A csatorna, amelyen engedélyezve van a valós idejű kódolás a Media Services RTMP vagy Smooth Streaming (töredékes MP4)) kerül. A csatorna ezután a bejövő egyfajta sávszélességű adatfolyamot élő kódolás útján többféle sávszélességű (adaptív) video-adatfolyammá alakítja. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.

### <a name="channel"></a>Csatorna
A Media Services szolgáltatásban [csatorna](https://docs.microsoft.com/rest/api/media/operations/channel)s felelősek az élő adatfolyam-tartalmak feldolgozása. Egy csatorna bemeneti végpontot biztosít (betöltési URL-címe), majd adja át egy élő átkódolók. A csatorna élő bemeneti streamekhez fogadja a élő átkódolók, és lehetővé teszi egy vagy több Streamvégpontok keresztül. Csatorna előnézeti végpont (előzetes verzió URL-cím), amellyel és ellenőrzéséhez a stream előnézetének a feldolgozás folytatása és a továbbítás előtti is biztosítanak.

A csatorna létrehozásakor kérheti le a bemeneti URL-címet és az előnézeti URL-címet. Az URL-címek beszerzéséhez a csatorna nem rendelkezik a lépések állapotban kell lennie. Ha készen áll az adatok elküldése egy élő átkódolók származó és a csatorna indítása, a csatornát kell elindítani. Miután az élő átkódolók elindul az adatok bevitele, megtekintheti a stream.

Minden egyes Media Services-fiók több csatornán, több programok és Streamvégpontok több is tartalmazhat. A sávszélesség- és biztonsági igényeitől függően egy vagy több csatornán dedikálhatja Streamvégpontok szolgáltatások. Bármely Streamvégpontok is lekéréshez tetszőleges csatornán.

### <a name="program-event"></a>Program (esemény)
A [(esemény) Program](https://docs.microsoft.com/rest/api/media/operations/program) lehetővé teszi, hogy a storage, az élő stream szegmenseinek közzétételét és. (Események) programokat a csatornák kezelik. A csatornák és programok viszonya hasonló a hagyományos televíziózáshoz, ahol csatorna tartalmaz egy állandó stream a tartalom és a egy programot az adott csatornán néhány időzített esemény hatókörét.
Megadhatja, hogy meg szeretné őrizni a program felvett tartalmát beállításával órák számát az **ArchiveWindowLength** tulajdonság. Ez az érték 5 perc és 25 óra közötti lehet.

ArchiveWindowLength is előírja az ügyfelek maximális számát is kérhet időbeli az aktuális élő pozíciótól. Az események hosszabbak lehetnek a megadott időtartamnál, de a rendszer folyamatosan elveti azokat a tartalmakat, amelyek korábbiak a megadott időtartamnál. Ennek a tulajdonságnak az értéke határozza meg azt is, hogy milyen hosszúra nőhetnek az ügyfél jegyzékfájljai.

Minden egyes program (esemény) az eszköz társítva. A program közzétételéhez létre kell hoznia egy lokátort a társított objektumhoz. Ez a lokátor teszi lehetővé az ügyfeleknek megadható streamelő URL-cím összeállítását.

A csatornák három egyidejűleg zajló programot támogatnak, így egy bejövő streamből több archívumot is létre lehet hozni. Ez lehetővé teszi az események különféle részeinek szükség szerinti közzétételét és archiválását. Az üzleti igény szerint például 6 órát kell archiválni egy programból, de csak az utolsó 10 percet kell közvetíteni. Ezt két egyidejűleg zajló program létrehozásával érheti el. Ebben az esetben állítsa be az egyik programot az esemény 6 órájának archiválására, de ne tegye közzé. A másik programot 10 perc archiválására állítsa be, és tegye is közzé.

További információkért lásd:

* [Az Azure Media Services segítségével élő kódolásra képes csatornák használata](media-services-manage-live-encoder-enabled-channels.md)
* [Helyszíni kódolóktól többszörös átviteli sebességű streameket fogadó csatornák használata](media-services-live-streaming-with-onprem-encoders.md)
* [Kvóták és korlátozások](media-services-quotas-and-limitations.md).

## <a name="protecting-content"></a>Tartalom védelme
### <a name="dynamic-encryption"></a>Dinamikus titkosítás
Az Azure Media Services lehetővé teszi, hogy elhagyják a számítógép tárolási, feldolgozási és kézbesítési kezdve a médiatartalmak védelmét. A Media Services lehetővé teszi a tartalom Advanced Encryption Standard (AES) (128 bites titkosítási kulcsok használatával) és az általános titkosítás (CENC) a PlayReady és/vagy Widevine DRM segítségével dinamikusan titkosítja. A Media Services emellett modult az AES-kulcsok és a PlayReady-licenc jogosult ügyfeleknek.

Jelenleg hogy titkosítsa a következő formátumban: HLS, MPEG DASH és Smooth Streaming. A progresszív letöltés nem titkosítható.

Ha azt szeretné, a Media Services az objektum titkosítására, meg kell rendel hozzá egy titkosítási kulcsot (CommonEncryption vagy EnvelopeEncryption) az eszköz és engedélyezési házirendek, a kulcs is konfigurálhatja.

Ha azt szeretné, a storage titkosított eszköz streamelésére, konfigurálnia kell az adategység továbbítási házirendjét annak érdekében, hogy adja meg, hogyan szeretné, hogy az objektumot.

Adatfolyam-lejátszó kér, amikor a Media Services dinamikus titkosítást a tartalom-boríték-titkosítással (AES) vagy a common Encryption titkosítás használata (a PlayReady vagy Widevine) használja a megadott kulcs. A stream visszafejteni, a Windows Media player a kulcs lesz kérhet a kulcstovábbítást. Annak eldöntéséhez, hogy-e a felhasználó jogosult-e a kulcs lekérése, a szolgáltatás kiértékeli az engedélyezési házirendeket, amelyek a kulcshoz megadott.

### <a name="token-restriction"></a>Token korlátozás
A tartalomkulcs-hitelesítési szabályzat rendelkezhet egy vagy több hitelesítési korlátozást: Nyissa meg a, jogkivonat-korlátozási vagy IP-korlátozást. A tokennel korlátozott szabályzatokhoz a Secure Token Service (Biztonsági jegykiadó szolgáltatás, STS) által kiadott tokennek kell tartoznia. A Media Services a Simple Web Tokens (SWT) és a JSON webes jogkivonat (JWT) formátumú jogkivonatokat támogatja. A Media Services nem biztosít jegykiadó szolgáltatások biztonságos. Létrehozhat egy egyéni STS. Az STS-re kell állítani a megadott kulcs és a probléma jogcímek jogkivonat korlátozás konfigurációjában megadott aláírt jogkivonat létrehozásához. A Media Services kulcstovábbítást visszaküldi a kért kulcs (vagy engedély-) az ügyfél, ha a jogkivonat érvényes, és a jogcímek, az token találat azok konfigurálva a kulcs (vagy licenc).

Konfigurálás a jogkivonat alapján korlátozott házirend, ha az elsődleges ellenőrzőkulcs, a kibocsátó és a célközönség paramétereket kell megadnia. Az elsődleges ellenőrzőkulcs tartalmazza a kulcsot, a jogkivonat írták-e, a kiállító a biztonságos jogkivonat-szolgáltatás, amely a jogkivonatot. A célközönség (más néven hatókör) ismerteti a jogkivonat a leképezést, vagy az erőforrás a token engedélyezi a hozzáférést. A Media Services kulcstovábbítást ellenőrzi, hogy ezeket az értékeket a jogkivonat egyezik a sablonban szereplő értékeket.

További információkért tekintse át a következő cikkeket:
- [Védelme tartalom áttekintése](media-services-content-protection-overview.md)
- [AES-128 védelme](media-services-protect-with-aes128.md)
- [PlayReady/Widevine védelme](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>Kézbesítés
### <a name="a-iddynamicpackagingdynamic-packaging"></a><a id="dynamic_packaging"/>A dinamikus csomagolás
Ha a Media Services dolgozik, javasoljuk, hogy a "mezzanine" formátumú fájlok kódolandó egy adaptív sávszélességű MP4-készletet, és alakítsa át a készlet a kívánt formátumban történő a [dinamikus csomagolási](media-services-dynamic-packaging-overview.md).

### <a name="streaming-endpoint"></a>Streamvégpont
A Streamvégpontok jelöli egy adatfolyam-szolgáltatást, amely tartalmat továbbít közvetlenül az ügyfél lejátszóalkalmazásába, vagy az egy Content Delivery Network (CDN) további terjesztés céljából (az Azure Media Services mostantól lehetőséget nyújt az Azure CDN-integrációt.) Az a végpont streamelési szolgáltatás kimenő adatfolyam élő stream, vagy igény szerinti Videoobjektum a Media Services-fiók is lehet. A Media Services ügyfelei általában egy **standard** szintű streamvégpontot vagy egy vagy több **prémium** szintű streamvégpontot választanak, saját igényeiknek megfelelően. Standard szintű streamvégpont a legtöbb streamelési feladat ellátására alkalmas. 

A szabványos streamvégpont a legtöbb streamelési feladat ellátására alkalmas. Standard szintű Streamelési végpontok ajánlat tartalomkézbesítés gyakorlatilag minden eszközre, HLS, MPEG-DASH, Smooth Streaming, valamint a Microsoft PlayReady, a Google Widevine, az Apple Fairplay, a dinamikus titkosítás, a dinamikus csomagolás révén rugalmasan és AES128.  Is méretezni őket a nagyon kicsi a nagyon nagy közönséget, az Azure CDN-integráció egyidejű megtekintők több ezer. Ha speciális feladatokat vagy a streamelési kapacitásra vonatkozó követelményeknek nem férnek el a standard streaming endpoint teljesítménycéljai vagy meg szeretné határozni a Streamvégpontok szolgáltatás kapacitását, kezeli a növekvő sávszélesség van szüksége, javasoljuk, hogy (más néven prémium streamelési egységek) skálázási egységeket kiosztania.

Javasoljuk, hogy a dinamikus csomagolás és/vagy a dinamikus titkosítás használata.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 

További információ [ebben](media-services-portal-manage-streaming-endpoints.md) a témakörben érhető el.

Alapértelmezés szerint legfeljebb 2 streamvégpontok a Media Services-fiók lehet. Kérjen nagyobb Korlátértékre, lásd: [kvóták és korlátozások](media-services-quotas-and-limitations.md).

Csak számítjuk fel, ha a Streamvégpontok belül futó állapotban van.

### <a name="asset-delivery-policy"></a>Objektumtovábbítási szabályzat
A Media Services-továbbítási munkafolyamat lépésben konfigurálja [kézbesítési házirendek eszközök](https://docs.microsoft.com/rest/api/media/operations/assetdeliverypolicy)streamként kívánt. Az objektumtovábbítási szabályzat arra utasítja a Media Services az objektum kézbesítendő módját: az adatfolyam-továbbítási protokoll kell az eszközintelligencia dinamikusan csomagolható (például MPEG DASH, HLS, Smooth Streaming, vagy az összes), dinamikusan titkosítani szeretné-e az objektumot, és hogyan (boríték vagy common encryption).

Ha egy tárolási titkosított eszköz, mielőtt az eszköz továbbítható, a streamelési kiszolgáló eltávolítja a tárolás titkosítása és adatfolyamként elküldi a tartalmát a megadott objektumtovábbítási szabályzat használatával. Például, hogy az eszköz Advanced Encryption Standard (AES) titkosítási kulccsal titkosított, állítsa a házirend típusát DynamicEnvelopeEncryption. Távolítsa el a storage-titkosítás, illetve streamelni az eszköz a titkosítatlan, NoDynamicEncryption típusú értékre.

### <a name="progressive-download"></a>Progresszív letöltés
Progresszív letöltés media előtt a teljes fájlt letöltötte időkódokat teszi lehetővé. MP4-fájlokat csak fokozatosan lehet letölteni.

>[!NOTE]
>Ha azt szeretné, a progresszív letöltés elérhetővé szeretné tenni, vissza kell fejtenie titkosított eszközökre.

Biztosíthatja a felhasználók számára a progresszív letöltési URL-címeket, akkor először hozzon létre egy OnDemandOrigin lokátort. A lokátor hoz létre, lehetővé teszi az alapútvonal az eszközre. Ezután meg kell hozzáfűznie MP4-fájl neve. Példa:

http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

### <a name="streaming-urls"></a>Streamelési URL-ek
Az ügyfeleknek a tartalom streamelésének. Streamelési URL-címek biztosíthatja a felhasználók számára, hogy először hozzon létre egy OnDemandOrigin lokátort. A lokátor létrehozása, biztosít az alapútvonal adatfolyam kívánt tartalmat tartalmazó objektumot. Azonban képesek lesznek streamelni az ehhez a tartalomhoz szüksége további az elérési út módosítása. A teljes URL-címe a streamelési jegyzékfájlt létrehozására, a lokátor elérési útjának értéke és a jegyzékfájlt (filename.ism) kell összefűzni fájl nevét. A lokátor elérési útja, majd hozzáfűzése /Manifest és a egy megfelelő formátumú (ha szükséges).

SSL-kapcsolaton keresztül is streamelheti a tartalmat. Ehhez ellenőrizze, hogy a streamelési URL-címek indítsa el a HTTPS. Jelenleg az AMS nem támogatja az SSL egyéni tartománnyal rendelkező.  

>[!NOTE]
>Csak akkor streamelheti SSL-en keresztül, ha a streamvégpontra, amelyről a tartalomkézbesítés 2014. szeptember 10-után jött létre. A streamelési URL-címek után szeptember 10 létrehozott streamvégpontok alapulnak, ha az URL-címet tartalmaz "streaming.mediaservices.windows.net" (a új formátum). Streamelési URL-címek, amelyek tartalmazzák a "origin.mediaservices.windows.net" (a régi formátumot) nem támogatja az SSL. Ha a régi formátumot az URL-címe van, és képesek lesznek streamelni az SSL-en keresztül szeretne, hozzon létre egy új streamvégpont. Az új streamvégpont alapján létrehozott URL-címek segítségével a tartalmak streamelésére SSL-en keresztül.

Az alábbi lista ismerteti a különböző formátumban, és a példákat mutat be:

* Smooth Streaming

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

* MPEG DASH

{Stream végpont neve-media services fiók name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

* Apple HTTP Live Streaming (HLS) V4

{Stream végpont neve-media services fiók name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

* Apple HTTP Live Streaming (HLS) V3

{Stream végpont neve-media services fiók name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

