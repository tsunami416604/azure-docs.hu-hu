---
title: Az Azure Media Services fogalmai | Microsoft dokumentumok
description: Ez a cikk rövid áttekintést nyújt a Microsoft Azure Media Services fogalmairól és a részletekért más cikkekre mutató hivatkozásokról.
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
ms.openlocfilehash: 69e2c053c9fb874889bc3d5b08be6e0c7ce875a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162905"
---
# <a name="azure-media-services-concepts"></a>Az Azure Media Services fogalmai 

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)

Ez a témakör áttekintést nyújt a Media Services legfontosabb fogalmairól.

## <a name="assets-and-storage"></a><a id="assets"/>Eszközök és tárolás
### <a name="assets"></a>Objektumok
[Az eszköz](https://docs.microsoft.com/rest/api/media/operations/asset) digitális fájlokat (beleértve a videót, hangot, képeket, miniatűr gyűjteményeket, szöveges pályákat és feliratfájlokat) és a fájlok metaadatait tartalmazza. Miután a digitális fájlokat feltöltötte egy eszközbe, fel lehet használni őket a Media Services kódolási és streamelési munkafolyamatokban.

Egy eszköz le van képezve egy blob tárolóaz Azure Storage-fiókban, és az eszköz ben tárolt fájlok blokk blobok a tárolóban. Az Azure Media Services nem támogatja a lapblobokat.

Annak eldöntésekor, hogy milyen médiatartalmat töltsön fel és tároljon egy eszközben, a következő szempontok at kell alkalmazni:

* Egy eszköz csak egy, egyedi médiatartalom-példányt tartalmazhat. Például egy tv-epizód, film vagy hirdetés egyetlen szerkesztése.
* Egy eszköz nem tartalmazhat több interpretációt vagy audiovizuális fájl szerkesztését. Egy eszköz nem megfelelő használatára példa egy eszköz nem megfelelő használata, ha egynél több tv-epizódot, hirdetést vagy több kameraszöget próbál tárolni egyetlen termelésből egy eszközbelsejében. Ha egy audiovizuális fájl több interpretációját vagy szerkesztését tárol egy eszközben, az nehézségekbe ütközhet a kódolási feladatok beküldésében, a streamelésben és az eszköz kézbesítésének biztosításában a munkafolyamat későbbi részében.  

### <a name="asset-file"></a>Eszközfájl
Az [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) egy blobtárolóban tárolt tényleges video- vagy hangfájlt jelöl. Egy eszközfájl mindig egy eszközhöz van társítva, és egy eszköz egy vagy több fájlt tartalmazhat. A Media Services kódoló feladata sikertelen, ha egy eszközfájl-objektum nincs társítva egy blobtárolóban lévő digitális fájlhoz.

Az **AssetFile** példány és a tényleges médiafájl két különálló objektum. Az AssetFile példány metaadatokat tartalmaz a médiafájlról, míg a médiafájl a tényleges médiatartalmat tartalmazza.

Ne kísérelje meg a Media Services által a Media Services API-k használata nélkül létrehozott blobtárolók tartalmának módosítását.

### <a name="asset-encryption-options"></a>Eszköztitkosítási beállítások
A feltölteni, tárolni és szállítani kívánt tartalom típusától függően a Media Services különböző titkosítási lehetőségeket kínál, amelyek közül választhat.

>[!NOTE]
>Nem használ titkosítást. Ez az alapértelmezett érték. Ha ezt a beállítást használja, a tartalom nem védett az átvitel vagy a tárolóban lévő nyugalmi helyen.

Ha progresszív letöltéssel szeretne MP4-et szállítani, ezzel a beállítással töltheti fel a tartalmat.

**StorageEncrypted** – Ezzel a beállítással titkosíthatja a titkosítatlan tartalmat helyileg AES 256 bites titkosítással, majd töltse fel az Azure Storage-ba, ahol az inaktív titkosítástárolódik. A tárolótitkosítással védett eszközök automatikusan titkosítatlanok lesznek, és a kódolás előtt egy titkosított fájlrendszerbe kerülnek, és opcionálisan újra titkosítva lesznek, mielőtt új kimeneti eszközként újra feltöltenék őket. A tárolótitkosítás elsődleges használati esete az, ha a kiváló minőségű bemeneti médiafájlokat erős titkosítással szeretné biztosítani a lemezen. 

A tárolóval titkosított eszköz biztosításához konfigurálnia kell az eszköz kézbesítési szabályzatát, hogy a Media Services tudja, hogyan szeretné kézbesíteni a tartalmat. Az eszköz streamelése előtt a streamelési kiszolgáló eltávolítja a tárolótitkosítást, és a megadott kézbesítési házirend (például AES, PlayReady vagy titkosítás nélkül) használatával streameli a tartalmat. 

**CommonEncryptionProtected** - Akkor használja ezt a beállítást, ha általános titkosítással vagy PlayReady DRM-mel (például PlayReady DRM-mel védett) tartalmat szeretne titkosítani (vagy már titkosított) szeretne titkosítani (vagy feltölteni).

**EnvelopeEncryptionProtected** – Akkor használja ezt a beállítást, ha meg szeretné védeni (vagy feltölteni a már védett) HTTP Live Streaming (HLS) titkosítást advanced encryption standard (AES) titkosítással. Ha a HLS-t már titkosította az AES-szel, akkor azt a Transform Manager titkosította.

### <a name="access-policy"></a>Hozzáférési szabályzat
Az [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy) határozza meg az engedélyeket (például az olvasást, az írást és a listát) és az eszközhöz való hozzáférés időtartamát. Általában átkell adnia egy AccessPolicy objektumot egy lokátornak, amelyet ezután egy eszközben található fájlok elérésére használnak.

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja (például olyan keresők szabályzatait, amelyek hosszú ideig érvényben maradnak, vagyis nem feltöltött szabályzatokat), a szabályzatazonosítónak is ugyanannak kell lennie. További információ [ebben](media-services-dotnet-manage-entities.md#limit-access-policies) a témakörben érhető el.

### <a name="blob-container"></a>Blobtároló
A blob tároló blobok egy csoportja csoportosítását biztosítja. A Blob-tárolók a Media Servicesben a hozzáférés-vezérlés határpontjaként és az eszközök megosztott hozzáférés-hozzáférési aláírási (SAS) lokátoraiként használatosak. Az Azure Storage-fiók korlátlan számú blobtárolót tartalmazhat. Egy tároló korlátlan számú blob tárolására használható.

>[!NOTE]
> Ne kísérelje meg a Media Services által a Media Services API-k használata nélkül létrehozott blobtárolók tartalmának módosítását.
> 
> 

### <a name="locators"></a><a id="locators"/>Lokátorok
[A lokátor](https://docs.microsoft.com/rest/api/media/operations/locator)belépési pontot biztosít az eszközben található fájlok eléréséhez. A hozzáférési szabályzat segítségével határozza meg az engedélyeket és időtartamot, amelyhez az ügyfél hozzáfér egy adott eszközhöz. A lokátorok nak több egyhez kapcsolata lehet egy hozzáférési házirenddel, így a különböző lokátorok különböző kezdési és kapcsolattípusokat biztosíthatnak a különböző ügyfeleknek, miközben ugyanazt az engedély- és időtartam-beállítást használják; azonban az Azure storage-szolgáltatások által beállított megosztott hozzáférési házirend-korlátozás miatt nem rendelkezhet egyszerre ötnél több egyedi lokátorral egy adott eszközhöz társítva. 

A Media Services kétféle lokátort támogat: az OnDemandOrigin lokátorokat, amelyek médiaadatfolyamok streamelésére szolgálnak (például MPEG DASH, HLS vagy Smooth Streaming), vagy fokozatosan letöltik az adathordozókat és a SAS URL-lokátorokat, amelyek médiafájlok feltöltéséhez vagy letöltéséhez szolgálnak az Azure storage-ból. 

>[!NOTE]
>A listaengedély (AccessPermissions.List) nem használható onDemandOrigin lokátor létrehozásakor. 

### <a name="storage-account"></a>Tárfiók
Az Azure Storage-hoz való hozzáférés egy tárfiókon keresztül történik. A Media Service-fiók egy vagy több tárfiókhoz társítható. Egy fiók korlátlan számú tárolót tartalmazhat, feltéve, hogy teljes méretük tárfiókonként 500 TB alatt van.  A Media Services SDK-szintű eszközöket biztosít, amelyek lehetővé teszik több tárfiók kezelését és az eszközök elosztásának terheléselosztását a fiókokba való feltöltés során metrikák vagy véletlenszerű elosztás alapján. További információ: Az [Azure Storage kezelése.](https://msdn.microsoft.com/library/azure/dn767951.aspx) 

## <a name="jobs-and-tasks"></a>Feladatok és feladatok
A [feladatot](https://docs.microsoft.com/rest/api/media/operations/job) általában egy hang-/videobemutató feldolgozására (például indexelésre vagy kódolásra) használják. Ha több videót dolgoz fel, hozzon létre egy feladatot minden egyes kódolandó videóhoz.

A feladat metaadatokat tartalmaz a végrehajtandó feldolgozással kapcsolatban. Minden feladat egy vagy több [feladatot](https://docs.microsoft.com/rest/api/media/operations/task)tartalmaz, amelyek meghatározzák az atomi feldolgozási feladatot, a bemeneti eszközöket, a kimeneti eszközöket, a médiaprocesszort és a hozzá tartozó beállításokat. A feladaton belüli feladatok összeláncolhatók, ahol az egyik feladat kimeneti eszköze a következő feladat bemeneti eszközeként van megadva. Ily módon egy feladat a médiabemutatóhoz szükséges összes feldolgozást tartalmazhatja.

## <a name="encoding"></a><a id="encoding"></a>Kódolás
Az Azure Media Services több lehetőséget biztosít a felhőben lévő médiakódoláshoz.

A Media Services szolgáltatással való indításkor fontos megérteni a kodekek és a fájlformátumok közötti különbséget.
Kodekek a szoftver, amely végrehajtja a tömörítési / kicsomagolási algoritmusok, míg a fájlformátumok tárolók, hogy tartsa a tömörített videó.

A Media Services dinamikus csomagolást biztosít, amely lehetővé teszi az adaptív sávszélességű MP4 vagy Smooth Streaming kódolású tartalom kézbesítését a Media Services által támogatott streamelési formátumokban (MPEG DASH, HLS, Smooth Streaming), anélkül, hogy újra be kellene csomagolnia ezeket a streamelési formátumokat.

A dinamikus [csomagolás](media-services-dynamic-packaging-overview.md)előnyeinek kihasználásához a mezzanine (forrás) fájlt adaptív sávszélességű MP4-fájlokba vagy adaptív sávszélességű Smooth Streaming fájlokba kell kódolnia, és legalább egy szabványos vagy prémium szintű streamelési végpontot kell beállítania elindított állapotban.

A Media Services a jelen cikkben ismertetett alábbi igény szerinti kódolókat támogatja:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Media Encoder Premium-munkafolyamat](media-services-encode-asset.md#media-encoder-premium-workflow)

A támogatott kódolókról a [Kódolók](media-services-encode-asset.md)című témakörben talál tájékoztatást.

## <a name="live-streaming"></a>Élő streamelés
Az Azure Media Servicesben a csatorna egy folyamatot jelöl az élő streamelési tartalom feldolgozásához. A csatorna kétféleképpen fogad élő bemeneti adatfolyamokat:

* A helyszíni élő kódoló többbitrátású RTMP vagy sima streamelést (töredezett MP4) küld a csatornának. A következő élő kódolókat használhatja, amelyek többátviteli sim idejű smooth streaminget adnak ki: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco és Elemental. A következő élő kódolók kimeneti RTMP: Adobe Flash Live Encoder, [Telestream Wirecast](media-services-configure-wirecast-live-encoder.md), Teradek, Haivision és Tricaster kódolók. A bevitt adatfolyamok további átkódolás és kódolás nélkül haladnak át a csatornákon. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.
* A rendszer egyetlen bitráta-adatfolyamot küld (a következő formátumok egyikében: RTMP vagy Smooth Streaming (Töredezett MP4)) a media services-szel élő kódolásra engedélyezett csatornának. A csatorna ezután a bejövő egyfajta sávszélességű adatfolyamot élő kódolás útján többféle sávszélességű (adaptív) video-adatfolyammá alakítja. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.

### <a name="channel"></a>Csatorna
A Media Services alkalmazásban [a Csatorna](https://docs.microsoft.com/rest/api/media/operations/channel)felelős az élő közvetítési tartalmak feldolgozásáért. A csatorna egy bemeneti végpontot (betöltési URL-címet) biztosít, amelyet ezután egy élő átkódolónak ad meg. A csatorna élő bemeneti adatfolyamokat fogad az élő átkódertől, és elérhetővé teszi egy vagy több StreamingEndpoints-on keresztüli streamelésre. A csatornák egy előnézeti végpontot (előnézeti URL-t is) biztosítanak, amelyet a stream további feldolgozás és kézbesítés előtt történő előnézeti megtekintéséhez és érvényesítéséhez használhat.

A betöltési URL-címet és az előnézeti URL-t a csatorna létrehozásakor kaphatja meg. Az URL-címek leszámításához a csatornának nem kell elindított állapotban lennie. Ha készen áll arra, hogy élő átkóderből adatokat nyomtasson a csatornába, a csatornát el kell indítani. Miután az élő transzkóder megkezdi az adatok betöltését, megtekintheti az adatfolyam előnézetét.

Minden Media Services-fiók több csatornát, több programot és több StreamingEndpoints pontot tartalmazhat. A sávszélességtől és a biztonsági igényektől függően a StreamingEndpoint-szolgáltatások egy vagy több csatornához is fellehet őket vetni. Bármely StreamingEndpoint bármely csatornáról lehúzható.

### <a name="program-event"></a>Program (esemény)
A [Program (esemény)](https://docs.microsoft.com/rest/api/media/operations/program) lehetővé teszi a szegmensek közzétételének és tárolásának szabályozását élő közvetítésben. A csatornák programokat (eseményeket) kezelnek. A Csatorna és a Program kapcsolat hasonló a hagyományos médiához, ahol a csatorna állandó tartalomfolyammal rendelkezik, és a program hatóköre az adott csatorna bizonyos időzített eseményére terjed ki.
Az **ArchiveWindowLength** tulajdonság beállításával megadhatja, hogy hány órát szeretne megőrizni a program rögzített tartalma. Ez az érték 5 perc és 25 óra közötti lehet.

Az ArchiveWindowLength azt is előírja, hogy az ügyfelek legfeljebb mennyi időt kérhetnek vissza az aktuális élő pozícióból. Az események hosszabbak lehetnek a megadott időtartamnál, de a rendszer folyamatosan elveti azokat a tartalmakat, amelyek korábbiak a megadott időtartamnál. Ennek a tulajdonságnak az értéke határozza meg azt is, hogy milyen hosszúra nőhetnek az ügyfél jegyzékfájljai.

Minden program (esemény) egy eszközhöz van társítva. A program közzétételéhez létre kell hoznia egy lokátort a társított eszközhöz. Ez a lokátor teszi lehetővé az ügyfeleknek megadható streamelő URL-cím összeállítását.

A csatornák három egyidejűleg zajló programot támogatnak, így egy bejövő streamből több archívumot is létre lehet hozni. Ez lehetővé teszi az események különféle részeinek szükség szerinti közzétételét és archiválását. Az üzleti igény szerint például 6 órát kell archiválni egy programból, de csak az utolsó 10 percet kell közvetíteni. Ezt két egyidejűleg zajló program létrehozásával érheti el. Ebben az esetben állítsa be az egyik programot az esemény 6 órájának archiválására, de ne tegye közzé. A másik programot 10 perc archiválására állítsa be, és tegye is közzé.

További információkért lásd:

* [Élő kódolást az Azure Media Services szolgáltatással való éles kódolásra engedélyezett csatornákkal való együttműködés](media-services-manage-live-encoder-enabled-channels.md)
* [Többbites átviteli sebességű élő közvetítést fogadó csatornák kal a helyszíni kódolóktól](media-services-live-streaming-with-onprem-encoders.md)
* [Kvóták és korlátozások](media-services-quotas-and-limitations.md).

## <a name="protecting-content"></a>Tartalom védelme
### <a name="dynamic-encryption"></a>Dinamikus titkosítás
Az Azure Media Services lehetővé teszi, hogy biztosítsa az adathordozót attól az időponttól kezdve, hogy elhagyja a számítógépet a tárolás, feldolgozás és kézbesítés révén. A Media Services lehetővé teszi, hogy a playready és/vagy a Widevine DRM használatával dinamikusan titkosítsa a tartalmat advanced encryption Standard (AES) (128 bites titkosítási kulcsok használatával) és közös titkosítással (CENC). A Media Services szolgáltatást is biztosít az AES-kulcsok és PlayReady-licencek engedélyezett ügyfelek nek történő kézbesítéséhez.

Jelenleg a következő streamelési formátumokat titkosíthatja: HLS, MPEG DASH és Smooth Streaming. A progresszív letöltések nem titkosíthatók.

Ha azt szeretné, hogy a Media Services titkosítson egy eszközt, hozzá kell rendelnie egy titkosítási kulcsot (CommonEncryption vagy EnvelopeEncryption) az eszközhöz, és engedélyezési házirendeket kell konfigurálnia a kulcshoz.

Ha egy tároló titkosított eszközt szeretne streamelni, konfigurálnia kell az eszköz kézbesítési szabályzatát annak érdekében, hogy megadja, hogyan szeretné kézbesíteni az eszközt.

Amikor egy adatfolyamot egy lejátszó kér, a Media Services a megadott kulccsal dinamikusan titkosítja a tartalmat borítéktitkosítással (AES-sel) vagy közös titkosítással (PlayReady vagy Widevine használatával). Az adatfolyam visszafejtéséhez a játékos kérni fogja a kulcsot a kulcskézbesítési szolgáltatástól. Annak eldöntéséhez, hogy a felhasználó jogosult-e a kulcs bekésésére, a szolgáltatás kiértékeli a kulcshoz megadott engedélyezési házirendeket.

### <a name="token-restriction"></a>Token korlátozása
A tartalomkulcs-engedélyezési házirendhez egy vagy több engedélyezési korlátozás vonatkozhat: nyitott, jogkivonat-korlátozás vagy IP-korlátozás. A tokennel korlátozott szabályzatokhoz a Secure Token Service (Biztonsági jegykiadó szolgáltatás, STS) által kiadott tokennek kell tartoznia. A Media Services a Simple Web Tokens (SWT) és a JSON Web Token (JWT) formátumú tokeneket támogatja. A Media Services nem biztosít biztonságos jogkivonat-szolgáltatásokat. Egyéni STS-t hozhat létre. Az STS-t úgy kell konfigurálni, hogy hozzon létre egy jogkivonatot a megadott kulccsal, és kiadja a jogkivonat-korlátozási konfigurációban megadott jogcímeket. A Media Services-kulcs kézbesítési szolgáltatás a kért kulcsot (vagy licencet) visszaküldi az ügyfélnek, ha a jogkivonat érvényes, és a jogkivonatban szereplő jogcímek megegyeznek a kulcshoz (vagy licenchez) konfigurált jogcímekkel.

A korlátozott jogkivonat-házirend konfigurálásakor meg kell adnia az elsődleges ellenőrzési kulcsot, a kibocsátót és a közönség paramétereit. Az elsődleges ellenőrzési kulcs tartalmazza a kulcsot, amely a jogkivonat ot aláírták, a kibocsátó a biztonságos jogkivonat-szolgáltatás, amely kiadja a jogkivonatot. A közönség (más néven hatókör) a jogkivonat vagy az erőforrás szándékát írja le, amelyhez a jogkivonat engedélyezi a hozzáférést. A Media Services-kulcs kézbesítési szolgáltatás ellenőrzi, hogy ezek az értékek a jogkivonatban megegyeznek-e a sablonban lévő értékekkel.

További információkért tekintse át a következő cikkeket:
- [Tartalom védelem – áttekintés](media-services-content-protection-overview.md)
- [Védje az AES-128-as](media-services-protect-with-aes128.md)
- [Védelem playready/widevine-nal](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>Szállít
### <a name="dynamic-packaging"></a><a id="dynamic_packaging"/>Dinamikus csomagolás
A Media Services szolgáltatással végzett munka során ajánlott a mezzanine fájlokat adaptív MP4-es bitráta-készletbe kódolni, majd a dinamikus csomagolás segítségével a kívánt formátumra [konvertálni.](media-services-dynamic-packaging-overview.md)

### <a name="streaming-endpoint"></a>Streamvégpont
A StreamingEndpoint olyan adatfolyam-szolgáltatást jelöl, amely közvetlenül egy ügyféllejátszó alkalmazásnak vagy egy tartalomkézbesítési hálózatnak (CDN) képes továbbítani a tartalmat további terjesztés céljából (az Azure Media Services most már biztosítja az Azure CDN-integrációt.) A streamelési végpontszolgáltatás kimenő adatfolyama lehet egy élő közvetítés vagy egy videoon-igény szerinti eszköz a Media Services-fiókban. A Media Services ügyfelei általában egy **standard** szintű streamvégpontot vagy egy vagy több **prémium** szintű streamvégpontot választanak, saját igényeiknek megfelelően. A standard streamelési végpont a legtöbb streamelési számítási feladathoz alkalmas. 

A szabványos streamvégpont a legtöbb streamelési feladat ellátására alkalmas. A standard streamelési végpontok rugalmasságot biztosítanak ahhoz, hogy a tartalmat szinte minden eszközre eljuttassák a HLS, Az MPEG-DASH és a Smooth Streaming dinamikus csomagolásán keresztül, valamint dinamikus titkosítást a Microsoft PlayReady, a Google Widevine, az Apple Fairplay és AES128.  Emellett a nagyon kicsiés a nagyon nagy közönség között is méretezhetők, több ezer egyidejű megtekintővel az Azure CDN-integráción keresztül. Ha speciális számítási feladattal rendelkezik, vagy a streamelési kapacitás követelmények nem felelnek meg a szabványos streamelési végpont átviteli céloknak, vagy szeretné szabályozni a StreamingEndpoint szolgáltatás kapacitását a növekvő sávszélesség-igények kielégítésére, ajánlott osztásegységek (más néven prémium szintű streamelési egységek) lefoglalása.

Javasoljuk, hogy dinamikus csomagolást és/vagy dinamikus titkosítást használjon.

>[!NOTE]
>Amikor az AMS-fiók jön létre egy **alapértelmezett** streamelési végpont ot a fiók **leállított** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 

További információ [ebben](media-services-portal-manage-streaming-endpoints.md) a témakörben érhető el.

Alapértelmezés szerint legfeljebb 2 streamelési végpont lehet a Media Services-fiókban. Ha magasabb korlátot szeretne kérni, olvassa el a [Kvóták és korlátozások](media-services-quotas-and-limitations.md).

A számlázás csak akkor történik meg, ha a StreamingEndpoint futó állapotban van.

### <a name="asset-delivery-policy"></a>Eszközkézbesítési irányelv
A Media Services tartalomkézbesítési munkafolyamatának egyik lépése az adatfolyamként streamelni kívánt [eszközök kézbesítési szabályzatainak](https://docs.microsoft.com/rest/api/media/operations/assetdeliverypolicy)konfigurálása. Az eszközkézbesítési szabályzat megmondja a Media Services-nek, hogyan szeretné az eszköz kézbesítését: melyik streamelési protokollba kell dinamikusan csomagolni az eszközt (például MPEG DASH, HLS, Smooth Streaming vagy az összes), függetlenül attól, hogy dinamikusan szeretné-e titkosítani az ön eszközét és módját (boríték vagy közös titkosítás).

Ha rendelkezik egy tároló titkosított eszközzel, mielőtt az eszköz streamelhető, a streamelési kiszolgáló eltávolítja a tároló titkosítást, és streameli a tartalmat a megadott kézbesítési szabályzat használatával. Ha például az eszközt speciális titkosítási szabvány (AES) titkosítási kulccsal titkosíthatja, állítsa a házirend típusát DynamicEnvelopeEncryption értékre. A tárolótitkosítás eltávolításához és az eszköz titkosításának kiürítéséhez állítsa a házirend típusát NoDynamicEncryption értékre.

### <a name="progressive-download"></a>Progresszív letöltés
A progresszív letöltés lehetővé teszi a médialejátszás megkezdését a teljes fájl letöltése előtt. Csak fokozatosan tölthet le MP4-fájlt.

>[!NOTE]
>Ha azt szeretné, hogy a titkosított eszközök visszafejtsék, progresszív letöltésre is rendelkezésre kell állniuk.

Ahhoz, hogy a felhasználók progresszív letöltési URL-eket, először létre kell hoznia egy OnDemandOrigin lokátor. A lokátor létrehozása, megadja az eszköz alap elérési útját. Ezután hozzá kell fűznie az MP4-fájl nevét. Példa:

http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

### <a name="streaming-urls"></a>Streamelési URL-ek
A tartalom streamelése az ügyfelek számára. Ahhoz, hogy a felhasználók a streamelési URL-eket, először létre kell hoznia egy OnDemandOrigin lokátor. A lokátor létrehozása, megadja az alap elérési útját az adatfolyamként adatfolyamként adatfolyamként imitátort tartalmazó eszközhöz. Ahhoz azonban, hogy ezt a tartalmat streamelhesse, tovább kell módosítania ezt az elérési utat. A streamelési jegyzékfájl teljes URL-címének létrehozásához össze kell fűznie a lokátor elérési útját és a jegyzékfájl (filename.ism) fájlnevét. Ezután fűzze hozzá a /Manifest parancsot, és szükség esetén megfelelő formátumot a lokátor elérési úthoz.

A tartalmat SSL-kapcsolaton keresztül is streamelheti. Ehhez győződjön meg arról, hogy a streamelési URL-ek HTTPS-lel kezdődnek. Jelenleg az AMS nem támogatja az SSL-t egyéni tartományokkal.  

>[!NOTE]
>Csak akkor streamelhet SSL-en keresztül, ha az a streamelési végpont, amelyből a tartalmat kézbesíti, 2014. Ha a streamelési URL-címek a szeptember 10.-e után létrehozott streamelési végpontokon alapulnak, az URL-cím "streaming.mediaservices.windows.net" (az új formátum) tartalmazza. A "origin.mediaservices.windows.net" (a régi formátum) tartalmazó streamelési URL-ek nem támogatják az SSL-t. Ha az URL-cím a régi formátumban van, és ssl-en keresztül szeretne streamelni, hozzon létre egy új streamelési végpontot. Az új streamelési végpont alapján létrehozott URL-címekkel streamelheti a tartalmat SSL-en keresztül.

Az alábbi lista különböző streamelési formátumokat ismertet, és példákat mutat be:

* Smooth Streaming

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

* MPEG DASH

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest(format=mpd-time-csf)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

* Apple HTTP Live Streaming (HLS) V4

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest(format=m3u8-aapl)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

* Apple HTTP Live Streaming (HLS) V3

{streaming endpoint name-media services fiók neve}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

