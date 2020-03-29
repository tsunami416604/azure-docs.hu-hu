---
title: Az Azure Media Services csomagolási és eredethibái | Microsoft dokumentumok
description: Ez a témakör az Azure Media Services streaming végpont (Orgin) szolgáltatásából származó hibákat ismerteti.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65411372"
---
# <a name="streaming-endpoint-origin-errors"></a>Végpont (Origin) streamelési hibái 

Ez a témakör az Azure Media Services [streaming végpontszolgáltatásából](streaming-endpoint-concept.md)származó hibákat ismerteti.

## <a name="400-bad-request"></a>400 rossz kérés

A kérelem érvénytelen információkat tartalmaz, és ezekkel a hibakódokkal elutasítják, és a következő okok egyike miatt:

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|URL-szintaxis vagy formátumhiba. Ilyenek például az érvénytelen típusra, érvénytelen töredékre vagy érvénytelen sávra vonatkozó kérelmek. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|A kérelem url-címében nincs titkosítási címke. A CMAF-kérelmekhez titkosítási címke szükséges az URL-címben. Más, egynél több titkosítási típussal konfigurált protokollok is megkövetelik a titkosítási címkét a félreérthetőséghez. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|A kérelem teljesítésére irányuló tárolási kérelem hibás kéréssel sikertelen. |

## <a name="403-forbidden"></a>403 – Tiltott

A kérelem a következő okok valamelyike miatt nem engedélyezett:

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|A kérelem teljesítésére irányuló tárolási kérelem hitelesítési hibával sikertelen. Ez akkor fordulhat elő, ha a tárolókulcsok ellett forgatva, és a szolgáltatás nem tudta szinkronizálni a tárolókulcsokat. <br/><br/>Lépjen kapcsolatba az Azure-támogatással a [Súgó + támogatás](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az Azure Portalon.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |Tárolási művelet hiba, a hozzáférés nem sikerült a fiók engedélyeinek elégtelensége miatt. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |A kérelem teljesítésére irányuló tárolási kérés sikertelen volt, mert a tárfiók le van tiltva. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |Tárolási művelet hiba, a hozzáférés általános hibák miatt nem sikerült. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |A kimeneti formátum blokkolva van a streamingpolicy konfigurációja miatt. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |A tartalom titkosítása szükséges, a kimeneti formátumhoz kézbesítési házirend szükséges. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |A titkosítás nincs beállítva a kézbesítési házirend beállításaiban. |

## <a name="404-not-found"></a>404 Nem található

A művelet olyan erőforráson próbál meg hatni, amely már nem létezik. Előfordulhat például, hogy az erőforrást már törölték.

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |A kért sáv nem található. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |A kért erőforrás nem található. |
|MPE_UNAUTHORIZED |0x80890244 |A hozzáférés nem engedélyezett. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |A kért időbélyeg nem található. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |A kért dinamikus jegyzékfájl-szűrő nem található. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |A kért töredékindex meghaladja az érvényes tartományt. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Élő médiabejegyzések nem találhatók a moov puffer bekapására. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |Nem található a töredék egy adott műsorszámkért időpontjában.<br/><br/>Lehet, hogy a szilánk nincs a raktárban. Próbálkozzon a bemutató egy másik rétegével, amelynek lehet, hogy töredéke van. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |Nem található a kért bitráta médiabejegyzése a jegyzékben. <br/><br/>Lehet, hogy a játékos kért egy videó sávot egy bizonyos bitráta, hogy nem volt a jegyzékben.|
|MPE_METADATA_NOT_FOUND |0x80890257 |Nem található bizonyos metaadatok a jegyzékfájlban, vagy nem található a tárolóból való újrabázis. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |Tárolási művelet hiba, az erőforrás nem található. |

## <a name="409-conflict"></a>409 Konfliktus

Egy erőforráshoz `POST` megadott azonosítót `PUT` egy meglévő erőforrás vette át. A probléma megoldásához használjon másik azonosítót az erőforráshoz.

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |Tárolási művelet, ütközési hiba.  |

## <a name="410"></a>410

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|Élő streamelés esetén, ha a szűrő, amely forceEndTimestamp értéke true, a kezdő vagy a záró időbélyeg kívül esik az aktuális DVR ablakban.|

## <a name="412-precondition-failure"></a>412 Előfeltételi hiba

A művelet olyan eTag-et adott meg, amely eltér a kiszolgálón elérhető verziótól, azaz optimista egyidejűségi hibát. Próbálkozzon újra a kérelemmel, miután elolvasta az erőforrás legújabb verzióját, és a kérésre frissítette az eTag-et.

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |A kért töredék nem áll készen.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|Tárolási művelet hiba, előfeltételi hiba.|

## <a name="415-unsupported-media-type"></a>415 Nem támogatott adathordozó-típus

Az ügyfél által küldött hasznos adatformátum nem támogatott formátumú.

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| Nem kell titkosítást alkalmazni a már titkosított tartalomra.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |A titkosítás érvénytelen a bemeneti formátumban.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| A kézbesítési házirend típusa érvénytelen.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |Az eredeti beállításokat több kimeneti formátum is megosztható.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|A médiaformátum vagy -típus nem támogatott. A Media Services például nem támogatja a 64 feletti minőségi szintszámot. Az FLV videocímkében a Media Services nem támogatja a több SPS-t és több PPS-t tartalmazó videokeretet.|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| A kért eszköz bemeneti formátuma nem támogatott. A Media Services támogatja a Smooth (élő), az MP4 (VoD) és a Progresszív letöltési formátumokat.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|A kért kimeneti formátum nem támogatott. A Media Services támogatja a Smooth, DASH(CSF, CMAF), HLS (v3, v4, CMAF) és progresszív letöltési formátumokat.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|Nem támogatott titkosítási típus történt.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|A kért adathordozó-típust a kimeneti formátum nem támogatja. A támogatott típusok a videó, hang vagy "SUBT" felirat.|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|A forráseszköz adathordozója olyan adathordozó-formátummal lett kódolva, amely nem kompatibilis a kimeneti formátummal.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|A forráseszköz olyan videoformátummal lett kódolva, amely nem kompatibilis a kimeneti formátummal. H.264, AVC, H.265 (HEVC, hev1 vagy hvc1) támogatottak.|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|A forráseszköz olyan hangformátummal lett kódolva, amely nem kompatibilis a kimeneti formátummal. A támogatott hangformátumok az AAC, E-AC3 (DD+), a Dolby DTS.|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|A forrásvédett eszköz nem konvertálható kimeneti formátumra.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|A kimeneti formátum nem támogatja a védelmi formátumot.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|A bemeneti formátum nem támogatja a védelmi formátumot.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|Érvénytelen video NAL egység, például csak az első NAL a mintában lehet AUD.|
|MPE_INVALID_NALU_SIZE|0x80890260|Érvénytelen NAL egységméret.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|Érvénytelen NAL egységhossz-érték.|
|MPE_FILTER_INVALID|0x80890236|Érvénytelen dinamikus jegyzékfájl-szűrők.|
|MPE_FILTER_VERSION_INVALID|0x80890237|Érvénytelen vagy nem támogatott szűrőverziók.|
|MPE_FILTER_TYPE_INVALID|0x80890238|Érvénytelen szűrőtípus.|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|Az érvénytelen tartományt a szűrő határozza meg.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|Érvénytelen track attribútumot a szűrő határoz meg.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|A kiszerelési ablak hosszát a szűrő határozza meg.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|Az érvénytelen élő biztonsági másolatot a szűrő határozza meg.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|Az örökölt szűrők csak egy absTimeInHNS elemet támogatnak.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|A szűrők alkalmazása után egyáltalán nincs több adatfolyam.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|A live back off túl van a DVR ablakon.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|Az élő vissza nagyobb, mint a bemutató ablak.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|Túllépte a tíz (10) maximálisan engedélyezett alapértelmezett szűrőt.|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|Több első videó minőség operátor nem engedélyezett a kombinált kérelem szűrők.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|Az első minőségi bitráta-attribútumok számának egynek kell lennie (1).|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|HLS szegmens időtartama kisebbnek kell lennie, mint egyharmada a DVR ablak és HLS vissza.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|A töredék időtartamának legfeljebb 20 másodpercnek kell lennie, különben a bemeneti minőségi szintek nincsenek időhöz igazítva.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|DTS-specifikus hiba, nem találja a ReservedBox, amikor meg kell jelenlennie a DTSSpecficBox során DTS box elemzés.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|DTS-specifikus hiba, nem található csatorna a DTSSpecficBox-ban a DTS mező elemzéssorán.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|DTS-specifikus hiba, mintatípus-eltérés a DTSSpecficBox-ban.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|DTS-specifikus hiba, a többeszköz beállítása, de a DTSH mintatípus nem egyezik.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|DTS-specifikus hiba, a magadatfolyam mérete érvénytelen.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|DTS-specifikus hiba, a mintafelbontás érvénytelen.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|DTS-specifikus hiba, a részfolyam-bővítmény indexe érvénytelen.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|DTS-specifikus hiba, a részadatfolyam-blokk száma érvénytelen.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|DTS-specifikus hiba, a mintavételi gyakoriság érvénytelen.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|DTS-specifikus hiba, a referencia-órakód az adatfolyam-segédbővítményben érvénytelen.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|DTS-specifikus hiba, a hangszórók újraleképezési készletének száma érvénytelen.|

A titkosítási cikkeket és példákat lásd:

- [Koncepció: tartalomvédelem](content-protection-overview.md)
- [Koncepció: Tartalomkulcs-házirendek](content-key-policy-concept.md)
- [Koncepció: Streamelési házirendek](streaming-policy-concept.md)
- [Minta: védelem AES titkosítással](protect-with-aes128.md)
- [Minta: védelem DRM-mel](protect-with-drm.md)

A szűrési útmutatót lásd:

- [Koncepció: dinamikus jegyzékek](filters-dynamic-manifest-overview.md)
- [Koncepció: szűrők](filters-concept.md)
- [Minta: szűrők létrehozása REST API-kkal](filters-dynamic-manifest-rest-howto.md)
- [Példa: szűrők létrehozása a .NET segítségével](filters-dynamic-manifest-dotnet-howto.md)
- [Minta: szűrők létrehozása CLI-vel](filters-dynamic-manifest-cli-howto.md)

Élő cikkek és minták:

- [Koncepció: élő közvetítés áttekintése](live-streaming-overview.md)
- [Koncepció: Élő események és élő kimenetek](live-events-outputs-concept.md)
- [Minta: élő közvetítésoktatóanyag](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 Tartomány nem satisfiable

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|Tárolási művelet hiba, http 416-os hibát adott vissza, érvénytelen tartomány.|

## <a name="500-internal-server-error"></a>500 Belső kiszolgálóhiba

A kérelem feldolgozása során a Media Services olyan hibát észlel, amely megakadályozza a feldolgozás folytatását.  

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|A Winhttp ERROR_WINHTTP_TIMEOUT (0x00002ee2) hibakódja alapján érkezett és lefordítva.|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|Winhttp ERROR_WINHTTP_CONNECTION_ERROR hibakódja (0x00002efe).|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|A Winhttp ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7) hibakódja érkezett és lefordítva.|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|Tárolási művelet hiba, a HTTP 500-as hibák egyikének általános InternalError.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|Tárolási művelet hiba, általános OperationTimedA HTTP 500-as hibák egyikén.|
|MPE_STORAGE_FAILURE|0x808900F2|Tárolási művelet hiba, más HTTP 500-os hibák, mint a InternalError vagy az OperationTimedOut.|

## <a name="503-service-unavailable"></a>503 A szolgáltatás nem érhető el

A kiszolgáló jelenleg nem tudja fogadni a kéréseket. Ezt a hibát a szolgáltatáshoz intézett túlzott kérés ek okozhatják. A Media Services szabályozási mechanizmusa korlátozza a szolgáltatáshoz túlzott kérést nyújtó alkalmazások erőforrás-használatát.

> [!NOTE]
> A hibaüzenet és a hibakód-karakterlánc segítségével részletesebb információkat kaphat az 503-as hiba okáról. Ez a hiba nem mindig jelent szabályozást.
> 

|Hibakód|Hexadecimális érték |Hibaleírás|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|Tárolási művelet hiba, a HTTP-kiszolgáló foglalt sági hibája: 503.|

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdéseket tehet fel, visszajelzést adhat, frissítéseket kaphat

Tekintse meg az [Azure Media Services közösségi](media-services-community.md) cikket, ahol különböző módokon tehet fel kérdéseket, küldhet visszajelzést, és kaphat frissítéseket a Media Services szolgáltatásról.

## <a name="see-also"></a>Lásd még

- [Kódolási hibakódok](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
- [Az Azure Media Services fogalmai](concepts-overview.md)
- [Kvóták és korlátozások](limits-quotas-constraints.md)

## <a name="next-steps"></a>További lépések

[Példa: access ErrorCode and Message from ApiException with .NET](configure-connect-dotnet-howto.md#connect-to-the-net-client)
