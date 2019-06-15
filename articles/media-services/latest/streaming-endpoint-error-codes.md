---
title: Az Azure Media Services formátumokat támogató csomagolási és forrás hibák |} A Microsoft Docs
description: Ez a témakör ismerteti a hibákat, jelenhet meg az Azure Media Services Streaming Endpoint (Orgin) szolgáltatásból.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: juliako
ms.openlocfilehash: ebcda6026f79bc88df91471d8be88316ba57bfc6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65411372"
---
# <a name="streaming-endpoint-origin-errors"></a>Streamelési végpontot (forrás) hibák 

Ez a témakör ismerteti a hibákat, az Azure Media Services kaphat [folyamatos átviteli végponton szolgáltatás](streaming-endpoint-concept.md).

## <a name="400-bad-request"></a>400 Hibás kérés

A kérés érvénytelen adatokat tartalmaz, és az ezen hibakódok és az alábbi okok egyike miatt elutasítva:

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|Egy URL-cím formátuma hiba. Ilyenek például a kérelmek érvénytelen típusú, egy érvénytelen töredék és a egy érvénytelen nyomon követése. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|A kérés nincs titkosítás címkét tartalmaz, az URL-címben. CMAF kérésekhez egy titkosítási címke URL-címét. Egyéb protokollok, amelyeken egynél több titkosítási típus biztosítani kell a titkosítási címke Egyértelműsítő. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|A kérelem Storage hibás kérés hiba miatt nem sikerült a kérés teljesítéséhez. |

## <a name="403-forbidden"></a>403 Tiltott

A kérelem nem engedélyezett a következő okok egyike miatt:

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|Hitelesítési hiba történt a tároló a kérés teljesítéséhez küldött kérelem sikertelen. Ez akkor fordulhat elő, ha a tárelérési kulcsok elforgatott lettek, és a szolgáltatás nem tudta szinkronizálni a tárelérési kulcsok. <br/><br/>Lépjen kapcsolatba az Azure támogatja a [súgó + támogatás](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az Azure Portalon.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |Storage-műveleti hiba, hozzáférési fiók megfelelő jogosultságok hiánya miatt nem sikerült. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |A kérés teljesítéséhez tárolási irányuló kérelem sikertelen volt, mert a storage-fiók van letiltva. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |Storage-műveleti hiba, általános hibák miatt nem sikerült hozzáférést. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |A kimeneti formátum a StreamingPolicy konfiguráció miatt le van tiltva. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |Titkosításra szükség a tartalomhoz, Objektumtovábbítási szabályzat megadása kötelező a kimeneti formátum. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |Titkosítás nem módosíthatók a kézbesítési szabályzat-beállítások állítja be. |

## <a name="404-not-found"></a>404 – Nem található

A művelet megpróbálta hatókör olyan erőforrást, amely már nem létezik. Ha például az erőforrás már törölve lett.

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |Nem található a kért nyomon követése. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |A kért erőforrás nem található. |
|MPE_UNAUTHORIZED |0x80890244 |A hozzáférés nem engedélyezett. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |A kért időbélyeg nem található. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |Nem található a kért dinamikusjegyzék-szűrő. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |A kért töredék index az érvényes tartományon kívül van. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Az élő media bejegyzések beolvasni moov puffer nem található. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |Nem található a részlet egy adott nyomon követése a kért időben.<br/><br/>Lehet, hogy a részlet nem a storage-ban. Próbálja ki a bemutatót, előfordulhat, hogy egy kódrészletet egy másik rétege. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |Nem sikerült az adathordozó-bejegyzés található a kért sávszélességű a jegyzékfájlban. <br/><br/>Lehet, hogy a Windows Media player egy videó nyomon követése egy bizonyos sávszélességű, amelynek a jegyzékfájlban nem kéri.|
|MPE_METADATA_NOT_FOUND |0x80890257 |Nem található az egyes metaadatok a jegyzékfájlban, vagy nem található a storage-ból rebase. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |Storage-műveleti hiba, az erőforrás nem található. |

## <a name="409-conflict"></a>409 ütközés

Az erőforrás megadva az azonosító egy `PUT` vagy `POST` műveletet a meglévő erőforrás foglalt. Az erőforrás egy másik azonosító használata a probléma megoldásához.

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |Tárolási művelet hiba történt, ütközés hiba.  |

## <a name="410"></a>410

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|Az élő streameléshez a szűrőt, amelynek beállítása igaz, a kezdő vagy záró időbélyeg forceEndTimestamp esetén a jelenlegi DVR időszakon kívül.|

## <a name="412-precondition-failure"></a>412 előfeltétel-hiba

A művelet a megadott eTag, amely eltér a verzió érhető el a kiszolgálón, azt jelenti, egy optimista párhuzamossági hiba. A legújabb verzióra, az erőforrás olvasása és frissítése az eTag-kéréssel kapcsolatos után ismételje meg a kérelmet.

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |A kért töredék nem áll készen.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|Storage-műveleti hiba, egy előfeltétel-hiba.|

## <a name="415-unsupported-media-type"></a>415 nem támogatott adathordozó-típus

Az ügyfél által küldött adattartalom formátuma nem támogatott formátumú szerepel.

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| Már a titkosított tartalmat a titkosítás nem érvényesek.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |A titkosítás a bemeneti formátum érvénytelen.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| Kézbesítési szabályzat típusa érvénytelen.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |Az eredeti beállítások szerint több kimeneti formátumok sikerült oszthatók meg.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|A formátum vagy típusa nem támogatott. Például a Media Services nem támogatja, amely több mint 64 minőségi szintek száma. FLV videó címkét, a Media Services nem támogatja a Szervizcsomagok több és több PPS. videó keret|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| A kért objektum bemeneti formátum nem támogatott. Media Services támogatja (élő), Smooth MP4 (VoD) és a progresszív letöltés formátumok.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|A kért kimeneti formátum nem támogatott. Media Services támogatja a Smooth, DASH (CSF, CMAF), HLS (v3, v4, CMAF), és a progresszív letöltés formátumok.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|Nem támogatott titkosítási típus észlelt.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|A kért adathordozó-típus nem támogatott a kimeneti formátum. A támogatott típusok a következők: a videó, hang vagy "SUBT" felirat.|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|Az eszköz adathordozója kódolt az adathordozó formátumú, amely nem kompatibilis a kimeneti formátum.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|Az adatforrás-objektum, amely nem kompatibilis a kimeneti formátum a videó formátumban kódolt. H.264 AVC, H.265 (HEVC, hev1 vagy hvc1) támogatottak.|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|Az adatforrás-objektum, amely nem kompatibilis a kimeneti formátum hang formátumban kódolva lett. Hang támogatott formátumok a következők: AAC, Dolby DTS, E-AC3 (nn +).|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|A kimeneti formátum a védett adatforrás-objektum nem konvertálható.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|A védelem formátum nem támogatott a kimeneti formátum.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|A védelem formátum nem támogatott a bemeneti formátum.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|Érvénytelen videó NAL egység, például a minta csak az első NAL is lehet egy AUD.|
|MPE_INVALID_NALU_SIZE|0x80890260|NAL egység mérete érvénytelen.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|NAL egység hosszúságú érték érvénytelen.|
|MPE_FILTER_INVALID|0x80890236|Érvénytelen dinamikusjegyzék-szűrők.|
|MPE_FILTER_VERSION_INVALID|0x80890237|Szűrő érvénytelen vagy nem támogatott verziójú.|
|MPE_FILTER_TYPE_INVALID|0x80890238|Szűrőtípus érvénytelen.|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|Érvénytelen tartomány van megadva a szűrővel.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|Érvénytelen követése attribútum van megadva a szűrővel.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|Érvénytelen megjelenítési időszak hossza a szűrő van megadva.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|Élő visszatartási érvénytelen szűrő van megadva.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|Csak egyetlen absTimeInHNS elem örökölt szűrők használata támogatott.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|Nincs nincs több adatfolyam minden a szűrők alkalmazása után.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|Az élő visszatartási van túl a DVR-ablakot.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|Az élő visszatartási értéke nagyobb, mint a megjelenítési ablakot.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|Túllépte a maximális engedélyezett alapértelmezett szűrők tíz (10).|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|Több első videó minősége operátor nem engedélyezett kombinált kérelem szűrőket.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|Az első minőségi sávszélességű attribútumok számát (1) egyikét kell megadni.|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|HLS-szegmens időtartama kisebb, mint a DVR-ablakot, és a HLS egyharmada biztonsági kell.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|Töredék időtartamok legfeljebb körülbelül 20 másodperc kell lennie, vagy a bemeneti minőségi szintet nem idő igazítva.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|Hiba történt a DTS-specifikus, nem található a ReservedBox, amikor azt kell mutatnia a DTSSpecficBox DTS box elemzése során.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|DTS-specifikus hiba: nem található a DTSSpecficBox a DTS-box elemzésekor csatornákat.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|DTS-specifikus hiba, a minta adattípus-eltérés a DTSSpecficBox a.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|Több eszköz az DTS-specifikus hiba történt, de DTSH minta adattípus-eltérés van beállítva.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|Hiba történt a DTS-specifikus, core adatfolyam mérete érvénytelen.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|Hiba történt a DTS-specifikus, a minta megoldás je neplatná.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|Hiba történt a DTS-specifikus, alárendelt stream bővítmény index érvénytelen.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|Hiba történt a DTS-specifikus, alárendelt stream letiltása száma érvénytelen.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|Hiba történt a DTS-specifikus, mintavételi gyakoriság érvénytelen.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|Hiba történt a DTS-specifikus, alárendelt stream-bővítmény hivatkozási óra kódja érvénytelen.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|DTS-specifikus hiba történt, az előadók társítása ismételt leképezésnek set száma érvénytelen.|

Titkosítási cikkek és példákért lásd:

- [Fogalom: a content protection](content-protection-overview.md)
- [Fogalom: Tartalmi kulcs házirendjei](content-key-policy-concept.md)
- [Fogalom: Streamelési házirendek](streaming-policy-concept.md)
- [. Példa: az AES-titkosítással védeni](protect-with-aes128.md)
- [Példa: DRM védelme](protect-with-drm.md)

Szűrő útmutatásért lásd:

- [Fogalom: a dinamikus jegyzékek](filters-dynamic-manifest-overview.md)
- [Koncepció: szűrők](filters-concept.md)
- [Példa: szűrők létrehozása REST API-k](filters-dynamic-manifest-rest-howto.md)
- [Példa: szűrők létrehozása .NET használatával](filters-dynamic-manifest-dotnet-howto.md)
- [Példa: szűrők létrehozása a CLI-vel](filters-dynamic-manifest-cli-howto.md)

Élő cikkek és minták című témakörben talál:

- [Fogalom: az élő adatfolyam-továbbítási áttekintése](live-streaming-overview.md)
- [Fogalom: Élő események és élő kimenetek](live-events-outputs-concept.md)
- [Minta: élő adatfolyam-továbbítási oktatóanyag](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 tartomány nem teljesíthető.

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|Tárolási művelet hiba: Érvénytelen tartomány http 416 hibát adott vissza.|

## <a name="500-internal-server-error"></a>500 Belső kiszolgálóhiba

A kérelem feldolgozása során a Media Services hibába valamilyen hiba, amely megakadályozza a folyamatos feldolgozását.  

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|A fogadott és a lefordított Winhttp hibakód ERROR_WINHTTP_TIMEOUT (0x00002ee2).|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|A fogadott és a lefordított Winhttp hibakód ERROR_WINHTTP_CONNECTION_ERROR (0x00002efe).|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|A fogadott és a lefordított Winhttp hibakód, mint az ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7).|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|Storage-műveleti hiba, általános InternalError egy HTTP 500-as.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|Storage-műveleti hiba, általános OperationTimedOut egy HTTP 500-as.|
|MPE_STORAGE_FAILURE|0x808900F2|Storage-műveleti hiba, egyéb InternalError vagy OperationTimedOut HTTP 500-as hibák.|

## <a name="503-service-unavailable"></a>503 A szolgáltatás nem érhető el

A kiszolgáló jelenleg nem küldött fogadási kérelmekre küld. Ez a hiba oka túl sok a szolgáltatáshoz érkező kérések. A Media Services-szabályozás mechanizmus az alkalmazásokat, amelyek a szolgáltatás túl sok kérést az erőforrás-használati korlátozza.

> [!NOTE]
> Ellenőrizze az okkal kapcsolatos részletes információkért az 503-as hibát kapott hibaüzenet és hibakód karakterláncát. Ez a hiba nem mindig jelenti szabályozás.
> 

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|Storage-műveleti hiba, HTTP-kiszolgáló foglalt hiba 503-as kapott.|

## <a name="ask-questions-give-feedback-get-updates"></a>Tegyen fel kérdéseket, küldje el visszajelzését, frissítések beszerzése

Tekintse meg a [Azure Media Services-Közösség](media-services-community.md) kérdések, küldje el visszajelzését, és tudnivalók a Media Services-frissítések különböző módon olvashatja.

## <a name="see-also"></a>Lásd még

- [Kódolási hibakódok](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
- [Az Azure Media Services – alapelvek](concepts-overview.md)
- [Kvóták és korlátozások](limits-quotas-constraints.md)

## <a name="next-steps"></a>További lépések

[Példa: ErrorCode és üzenet elérését ApiException .NET-tel](configure-connect-dotnet-howto.md#connect-to-the-net-client)
