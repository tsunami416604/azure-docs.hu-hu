---
title: Smooth Streaming Protocol (MS-SSTR) módosítás a HEVC-hez - Azure
description: Ez a specifikáció ismerteti a protokoll és a formátum töredezett MP4-alapú élő streamelés HEVC az Azure Media Services. Ebben a cikkben csak a HEVC biztosításához szükséges módosítások vannak megadva, kivéve a "(Nincs változás)" szöveget, amely csak a pontosítás kedvéért másolja a szöveget.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: johndeu
ms.openlocfilehash: be4009d418f2f8f3dff755e2e990efee593f070b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514221"
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc"></a>Smooth Streaming Protocol (MS-SSTR) módosítás a HEVC-hez 

## <a name="1-introduction"></a>1 Bemutatkozás 

Ez a cikk részletes módosításokat tartalmaz a Smooth Streaming Protocol specifikációhoz [MS-SSTR] annak érdekében, hogy a HEVC kódolású videó zökkenőmentes streamelése lehetővé váljon. Ebben a specifikációban csak a HEVC videokodek biztosításához szükséges változtatásokat vázoljuk fel. A cikk ugyanazt a számozási sémát követi, mint az [MS-SSTR] specifikáció. A cikkben bemutatott üres címsorok az olvasót az [MS-SSTR] specifikációban elfoglalt helyükre irányíthatják.  A "(Nincs változás)" azt jelzi, hogy a szöveg másolása csak a pontosítás céljából történik.

A cikk technikai végrehajtási követelményeket tartalmaz a HEVC video kodek (a "hev1" vagy a "hvc1" formátumú sávok használatával) a Smooth Streaming jegyzékben történő jelzésére, és a normatív hivatkozások frissülnek, hogy a jelenlegi MPEG szabványokra hivatkozzon tartalmazza a HEVC-t, a HEVC közös titkosítását és az ISO Base Media fájlformátum dobozneveit, hogy összhangban legyen a legújabb specifikációkkal. 

A hivatkozott Smooth Streaming Protocol specifikáció [MS-SSTR] az élő és igény szerinti digitális média, például audió és videó szolgáltatásához használt vezetékformátumot írja le a következő módon: kódolóról webkiszolgálóra, kiszolgálóról másik kiszolgálóra, valamint http-ügyfélhez.
Az MPEG-4 ([[MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787)alapú adatstruktúra HTTP-n keresztüli kézbesítése lehetővé teszi a zökkenőmentes, közel valós idejű váltást a tömörített médiatartalmak különböző minőségi szintjei között. Az eredmény egy állandó lejátszási élmény a HTTP-ügyfél végfelhasználója számára, még akkor is, ha a hálózati és videorenderelési feltételek megváltoznak az ügyfélszámítógépen vagy eszközön.

## <a name="11-glossary"></a>1.1 Szószedet 

Az *[MS-GLOS]* a következő kifejezéseket határozza meg:

>   **globálisan egyedi azonosító (GUID) univerzálisan egyedi azonosító (UUID)**

A következő kifejezések a jelen dokumentumra vonatkoznak:

>  **kompozíciós idő:** Az [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695)című részben meghatározott minta bemutatása az ügyfélnél.
> 
>   **CENC**: Közös titkosítás az [ISO/IEC 23001-7] második kiadásban meghatározottak szerint.
> 
>   **dekódolási idő:** Az [[ISO/IEC 14496-12:2008]](https://go.microsoft.com/fwlink/?LinkId=183695)mezőben meghatározott a minta dekódolásának időpontja.

**töredék:** Egy vagy több **mintát**tartalmazó, egymástól függetlenül letölthető **adathordozó-egység** .

>   **HEVC:** Nagy hatásfokú videokódolás az [ISO/IEC 23008-2]
> 
>   **nyilvánvaló:** A **bemutató** metaadatai, amelyek lehetővé teszik az ügyfél számára, hogy **médiakéréseket**tegyen. **média:** Az ügyfél által a **bemutató**lejátszásához használt tömörített hang-, video- és szöveges adatok . **média formátum:** Jól definiált formátum a hang vagy videó tömörített **mintaként**való megjelenítéséhez .
> 
>   **bemutató:** Az egyetlen **mozgókép** lejátszásához szükséges összes adatfolyam és kapcsolódó metaadat készlete. **kérés:** Az ügyfél által a kiszolgálónak küldött HTTP-üzenet az [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372) **válasz:** Http-üzenet, amelyet a kiszolgáló küldött az ügyfélnek az [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372)
> 
>   **minta:** A legkisebb alapvető egység (például keret), amelyben az **adathordozó** tárolódik és feldolgozódik.
> 
>   **MÁJUS, KELL, KELL, NEM, NEM:** Ezeket a kifejezéseket (minden nagybetűvel) az [[RFC2119]](https://go.microsoft.com/fwlink/?LinkId=90317) minden fakultatív viselkedési utasítás may, should vagy NOT használatával használja.

## <a name="12-references"></a>1.2 Referenciák

>   A Microsoft Open Specifications dokumentációjára való hivatkozások nem tartalmazzák a közzétételi évet, mivel a hivatkozások a dokumentumok legújabb verziójára mutatnak, amelyeket gyakran frissítenek. Az egyéb dokumentumokra való hivatkozások közé tartozik a közzétételi év, amikor az elérhető.

### <a name="121-normative-references"></a>1.2.1 Normatív referenciák 

>  [MS-SSTR] Simítású streamelési protokoll *v20140502*[https://msdn.microsoft.com/library/ff469518.aspx](https://msdn.microsoft.com/library/ff469518.aspx)
> 
>   [ISO/IEC 14496-12] Nemzetközi Szabványügyi Szervezet, "Information technology -- Audiovizuális objektumok kódolása -- 12. rész: ISO Base Media File Format", ISO/IEC 14496-12:2014, Edition 4, Plus Corrigendum 1, 1& 2.
>   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>
> 
>   [ISO/IEC 14496-15] International Organization for Standardization, "Information technology -- Coding of audio-visual objects -- Part 15: Carriage of NAL unit structured video in the ISO Base Media File Format", ISO 14496-15:2015, Edition 3.
>   <https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>
> 
>   [ISO/IEC 23008-2] Informatika - Nagy hatékonyságú kódolás és médiaszállítás heterogén környezetben - 2. rész: Nagy hatékonyságú videókódolás: 2013 vagy legújabb kiadás<https://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>
> 
>   [ISO/IEC 23001-7] Információtechnológia – MPEG-rendszerek technológiái – 7.<https://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>
> 
>   [RFC-6381] IETF RFC-6381, "A kodekek" és a "Profilok" paraméterei a "Gyűjtő" adathordozó-típusokhoz"<https://tools.ietf.org/html/rfc6381>
> 
>   [MPEG4-RA] Az MP4 regisztrációs hatóság, "MP4REG",[http://www.mp4ra.org](https://go.microsoft.com/fwlink/?LinkId=327787)
> 
>   [RFC2119] Bradner, S., "Key words for use in RFC to Indicate Requirement Levels", BCP 14, RFC 2119, March 1997,[https://www.rfc-editor.org/rfc/rfc2119.txt](https://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 Tájékoztató referenciák 

>   [MS-GLOS] Microsoft Corporation, "*Windows protokollok fő szószedete*"
> 
>   [RFC3548] Josefsson, S., Ed., "The Base16, Base32, and Base64 Data Encodings", RFC 3548, July 2003,[https://www.ietf.org/rfc/rfc3548.txt](https://go.microsoft.com/fwlink/?LinkId=90432)
> 
>   [RFC5234] Crocker, D., Ed., és Overell, P., "Augmented BNF for Syntax Specifications: ABNF", STD 68, RFC 5234, January 2008,[https://www.rfc-editor.org/rfc/rfc5234.txt](https://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>1.3 Áttekintés 

>   Az alábbiakban csak a HEVC szállításához szükséges Smooth Streaming specifikáció módosításait kell megadni. A változatlan szakaszfejlécek a hivatkozott Smooth Streaming specifikációban (MS-SSTR) való hely megőrzésérdekében jelennek meg.

## <a name="14-relationship-to-other-protocols"></a>1.4 Kapcsolat más jegyzőkönyvekkel 

## <a name="15-prerequisitespreconditions"></a>1.5 Előfeltételek/előfeltételek 

## <a name="16-applicability-statement"></a>1.6 Alkalmazhatósági nyilatkozat 

## <a name="17-versioning-and-capability-negotiation"></a>1.7 Verziószámozás és képességek egyeztetése 

## <a name="18-vendor-extensible-fields"></a>1.8 Szállító bővíthető mezők 

>   A hevc videoformátumot használó adatfolyamok azonosítása a következő módszert kell alkalmazni:
> 
>   * **Egyéni leíró kódok médiaformátumokhoz:** Ezt **a** képességet a *2.2.2.5.*
>   A kivitelezők biztosíthatják, hogy a bővítmények ne ütközhessenek a bővítménykódok nak az [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695)

## <a name="19-standards-assignments"></a>1.9 Szabványok engedményei 

## <a name="2-messages"></a>2 Üzenetek 

## <a name="21-transport"></a>2.1 Közlekedés

## <a name="22-message-syntax"></a>2.2 Üzenet szintaxisa 

### <a name="221-manifest-request"></a>2.2.1 Jegyzékfájl igénylése 

### <a name="222-manifest-response"></a>2.2.2 Jegyzékfájlra adott válasz 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothStreamingMedia 

>   **MinorVersion (változó):** A jegyzékfájl-válasz üzenet alverziója. 2-re kell állítani. (Nincs változás)
> 
>   **Időskála (változó):** Az Időtartam attribútum időskálája, amely egy másodperc alatt a növekmények számaként van megadva. Az alapértelmezett érték 
> 1. (Nincs változás)
> 
>    Az ajánlott érték 90000 a tört képkocka-videót tartalmazó videokeretek és töredékek pontos időtartamának (például 30/1,001 Hz).

#### <a name="2222-protectionelement"></a>2.2.2.2 Protektitív elem 

A ProtectionElement elemnek jelen kell lennie, amikor a közös titkosítást (CENC) alkalmazták a video- vagy audiostreamekre. A HEVC titkosított adatfolyamainak meg kell felelniük a Common Encryption 2nd Edition szabványnak [ISO/IEC 23001-7]. Csak a VCL NAL egységben lévő szeletadatokat kell titkosítani.

#### <a name="2223-streamelement"></a>2.2.2.3 StreamElement 

>   **StreamTimeScale (változó):** Az adatfolyam időtartamának és időértékeinek időskálája, amely egy másodperc alatt a növekmények számaként van megadva. A HEVC-adatfolyamok esetében 90000 érték ajánlott. A hullámforma mintafrekvenciájának megfelelő érték (például 48000 vagy 44100) ajánlott a hangfolyamokhoz.

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlMinta 

#### <a name="225-trackelement"></a>2.2.5 TrackElement 

>   **FourCC (változó):** Négykarakteres kód, amely azonosítja, hogy melyik adathordozó-formátumot használja az egyes mintákhoz. A következő értéktartomány a következő szemantikai jelentéssel van fenntartva:
> 
> * "hev1": Az erre a sávra szánt videominták HEVC videót használnak az [ISO/IEC-14496-15] mezőben meghatározott "hev1" mintaleírás-formátum használatával.
>
> * "hvc1": Az erre a sávra szánt videominták HEVC videót használnak az [ISO/IEC-14496-15] mezőben meghatározott "hvc1" mintaleírás-formátum használatával.
> 
>   **CodecPrivateData (változó):** Az adathordozó-formátumra jellemző és a sávban lévő összes mintára jellemző paramétereket megadó adatok hex-kódolt bájtok karakterláncaként jelennek meg. A bájtsorozat formátuma és szemantikai jelentése a **FourCC** mező értékététől függ az alábbiak szerint:
> 
>   * Amikor egy TrackElement leírja a HEVC videót, a **FourCC** mezőnek **"hev1"** vagy **"hvc1"** értékűnek kell
> 
>   A **CodecPrivateData** mezőnek tartalmaznia kell az ABNF [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096) mezőben megadott következő bájtsorozat hex-kódolt karakterlánc-ábrázolását: (nincs változás az MS-SSTR-től)
> 
>   * %x00 %x00 %x00 %x01 SPSMező %x00 %x00 %x00 %x01 PPSField
> 
>   * Az SPSField tartalmazza a Sequence Parameter Set (SPS) készletet.
> 
>   * A PPSField tartalmazza a Szelet paraméterkészletet (PPS).
> 
>   Megjegyzés: A Video Parameter Set (VPS) nem szerepel a CodecPrivateData-ban, hanem a tárolt fájlok fájlfejlécében kell lennie a "hvcC" mezőben. A Smooth Streaming protokollt használó rendszereknek további dekódolási paramétereket (például HEVC-réteget) kell jelezniük a "kodekek" egyéni attribútum használatával.

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamfragmentElement 

>   A **SmoothStreamingMedia MajorVersion** mezőjének 2-re, **a MinorVersion** mezőnek pedig 2-re kell lennie. (Nincs változás)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>2.2.3 Töredékkérelem 

>   **Megjegyzés:** A **MinorVersion** 2 és a "hev1" vagy "hvc1" esetében kért alapértelmezett adathordozó-formátum az "iso8" márka ISO Base Media File Format , amelyet az [ISO/IEC 14496-12] ISO Base Media File Format Fourth Edition és [ISO/IEC 23001-7] Common Encryption Second Edition kiadásban meghatározott ISO Base Media File File Format formátumban határoztak meg.

### <a name="224-fragment-response"></a>2.2.4 A töredékre adott válasz 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   A **TfxdBox** elavult, és funkcióját az [ISO/IEC 14496-12] 8.8.12.
> 
>   **Megjegyzés:** Az ügyfél kiszámíthatja egy töredék időtartamát a Track Run Box ("Trun") mezőben felsorolt mintaidőtartamok összegzésével, vagy megszorozva a minták számát az alapértelmezett mintaidőtartamának szorzatával. A baseMediaDecodeTime a "tfdt" plusz töredék időtartama megegyezik a következő töredék URL-idő paramétere.
> 
>   Szükség szerint be kell illeszteni egy gyártói referenciaidő-mezőt ("prft") a filmtöredékdoboz ("moof") előtt, jelezve az [ISO/IEC 14496-12] 8.16.5.

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   A **TfrfBox** elavult, és funkcióját az [ISO/IEC 14496-12] 8.8.12.
> 
>   **Megjegyzés:** Az ügyfél kiszámíthatja egy töredék időtartamát a Track Run Box ("Trun") mezőben felsorolt mintaidőtartamok összegzésével, vagy megszorozva a minták számát az alapértelmezett mintaidőtartamának szorzatával. A baseMediaDecodeTime a "tfdt" plusz töredék időtartama megegyezik a következő töredék URL-idő paramétere. Előre tekintcímek elavult, mert késlelteti az élő streamelést.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   A **TfhdBox** és a kapcsolódó mezők a töredékben mintametaadatok alapértelmezett beállításait foglalják magukban. A **TfhdBox** mező szintaxisa az [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) 8.8.7.
> 
>   **BaseDataOffset (8 bájt):** A **MdatBox** mező elejétől a **MdatBox** mező mintamezőjéig az eltolás bájtban. A korlátozás jelzéséhez be kell állítani az alapértelmezett bázis-is-moof jelzőt (0x020000).

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   A **TrunBox** és a kapcsolódó mezők mintametaadatokat foglalnak magukban a kért töredékhez. A **TrunBox** szintaxisa az[[ISO/IEC-14496-](https://go.microsoft.com/fwlink/?LinkId=183695)*12]* 8.8.8.
> 
>   **SampleCompositionTimeOffset (4 bájt):** Az egyes minták mintavételi időeltolási ideje úgy van beállítva, hogy a töredékben bemutatott első minta megjelenítési ideje megegyezik az első dekódolt minta dekódolási idejével. Negatív videominta-összetételi eltolásokat kell alkalmazni,
> 
>   az [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) meghatározottak szerint.
> 
>   Megjegyzés: Ezzel elkerülhető a videoszinkronizálási hiba, amelyet a videoelmaradt hang a legnagyobb dekódolt képpuffer-eltávolítási késleltetéssel egyenlő, és megtartja a megjelenítés időzítését az alternatív töredékek között, amelyek eltérő eltávolítási késleltetéssel rendelkezhetnek.
> 
>   Az ebben a szakaszban meghatározott mezők aBNF [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096) mezőben megadott szintaxisa ugyanaz marad, kivéve az alábbiakat:
> 
>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>2.2.4.9 Töredékválasz közös mezők 

### <a name="225-sparse-stream-pointer"></a>2.2.5 Ritka streammutató 

### <a name="226-fragment-not-yet-available"></a>2.2.6 A töredék még nem áll rendelkezésre 

### <a name="227-live-ingest"></a>2.2.7 Élő lenyelés 

#### <a name="2271-filetype"></a>2.2.7.1 Fájltípus 

>   **FileType (változó):** az MPEG-4 ([[MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787) fájl és a magas szintű attribútumok altípusát és tervezett használatát adja meg.
> 
>   **MajorBrand (változó):** A médiafájl fő márkája. Be kell állítani, hogy "isml."
> 
>   **MinorVersion (változó):** A médiafájl alverziója. 1-re kell állítani.
> 
>   **CompatibleBrands (változó):** Az MPEG-4 támogatott márkáit adja meg.
>   A "ccff" és az "iso8" szót kell tartalmaznia.
> 
>   Az ebben a szakaszban meghatározott mezők aBNF [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096) mezőben megadott szintaxisa a következő:

    FileType = MajorBrand MinorVersion CompatibleBrands
    MajorBrand = STRING_UINT32
    MinorVersion = STRING_UINT32
    CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)

**Megjegyzés:** A "ccff" és "iso8" kompatibilitási márkák azt jelzik, hogy a töredékek megfelelnek a "Common Container File Format" és a Common Encryption [ISO/IEC 23001-7] és az ISO Base Media File Format Edition 4 [ISO/IEC 14496-12] szabványnak.

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StreamSMIL 

#### <a name="2273-liveservermanifestbox"></a>LiveServerManifestBox 2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>2.2.7.5 Töredék 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 Track Fragment Bővített fejléc 

### <a name="228-server-to-server-ingest"></a>2.2.8 Kiszolgálóról kiszolgálóra történő betöltés 

## <a name="3-protocol-details"></a>3 A protokoll részletei 


## <a name="31-client-details"></a>3.1 Az ügyfél adatai 

### <a name="311-abstract-data-model"></a>3.1.1 Absztrakt adatmodell 

#### <a name="3111-presentation-description"></a>3.1.1.1. 

>   A Bemutató leírása adatelem a bemutató összes metaadatát beágyazi.
> 
>   Bemutató metaadatai: A bemutató összes adatfolyamára vonatkozóan közös metaadatkészlet. A bemutató metaadatai a *2.2.2.1.*
> 
> * **MajorVersion (MajorVersion)**
> * **MinorVersion (Kisebb verzió)**
> * **Időskála**
> * **Időtartam**
> * **Élő is**
> * **LookaheadCount (ElőreCount)**
> * **DVRAblakhossza**
> 
>   A HEVC-áramokat tartalmazó bemutatók a következőket határozzák meg:

    MajorVersion = 2
    MinorVersion = 2

>   LookaheadCount = 0 (Megjegyzés: Elavult dobozok)
> 
>   Előadások is meg:

    TimeScale = 90000

>   Stream gyűjtemény: Stream Description adatelemek gyűjteménye a *3.1.1.1.2*szakaszban meghatározottak szerint.
> 
>   Védelem leírása: A védelmi rendszer metaadatainak gyűjteménye A *3.1.1.1.1.*

##### <a name="31111-protection-system-metadata-description"></a>3.1.1.1.1 Védelmi Rendszer metaadatainak leírása 

>   A védelmi rendszer metaadat-leíró adateleme egyetlen tartalomvédelmi rendszerre jellemző metaadatokat foglal magában. (Nincs változás)
> 
>   Védelmi fejléc leírása: Egyetlen tartalomvédelmi rendszerre vonatkozó tartalomvédelmi metaadatok. A védelem fejlécének leírása a *2.2.2.2.*
> 
>   * **SystemID azonosító**
>   * **ProtectionHeaderContent**

##### <a name="31112-stream-description"></a>3.1.1.1.2 A stream leírása 

###### <a name="311121-track-description"></a>3.1.1.1.2.1 Pálya leírása 

###### <a name="3111211-custom-attribute-description"></a>3.1.1.1.2.1.1 Egyéni attribútumleírás 

##### <a name="3113-fragment-reference-description"></a>3.1.1.3 A töredékek hivatkozási leírása 

###### <a name="31131-track-specific-fragment-reference-description"></a>3.1.1.3.1 Pályaspecifikus töredék hivatkozási leírása 

#### <a name="3112-fragment-description"></a>3.1.1.2 A töredékek leírása 

##### <a name="31121-sample-description"></a>3.1.1.2.1 Minta leírása 

### <a name="312-timers"></a>3.1.2 Időzítők 

### <a name="313-initialization"></a>3.1.3 Inicializálás 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 Magasabb rétegű aktivált események 

#### <a name="3141-open-presentation"></a>3.1.4.1 Bemutató megnyitása 

#### <a name="3142-get-fragment"></a>3.1.4.2 Töredék bekése 

#### <a name="3143-close-presentation"></a>3.1.4.3 Bemutató lezárása 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5 Események feldolgozása és szekvenálási szabályok 

#### <a name="3151-manifest-request-and-manifest-response"></a>3.1.5.1 Jegyzékfájl-kérelem és jegyzékfájlra adott válasz 

#### <a name="3152-fragment-request-and-fragment-response"></a>3.1.5.2 A töredékkérelmek és a töredékek válasza

## <a name="32-server-details"></a>3.2 A kiszolgáló adatai

## <a name="33-live-encoder-details"></a>3.3 Élő kódoló részletek 

## <a name="4-protocol-examples"></a>4 Jegyzőkönyvi példák 

## <a name="5-security"></a>5 Biztonság 

## <a name="51-security-considerations-for-implementers"></a>5.1 Biztonsági szempontok a kivitelezők számára

>   Ha az ezzel a protokollal szállított tartalom kereskedelmi értéke magas, tartalomvédelmi rendszert kell használni a tartalom jogosulatlan használatának megakadályozására. A **ProtectionElement** tartalomvédelmi rendszer használatával kapcsolatos metaadatok hordozására használható. A védett hang- és videotartalmat az MPEG Common Encryption Second Edition: 2015 [ISO/IEC 23001-7] által meghatározott módon kell titkosítani.
> 
>   **Megjegyzés:** HeVC videó esetén csak a VCL NAL-ekben lévő szeletadatok vannak titkosítva. A szeletfejlécek és más nalikek a bemutatóalkalmazások számára a visszafejtés előtt érhetők el. biztonságos videoútvonalon a bemutatóalkalmazások számára nem érhetők el titkosított információ.

## <a name="52-index-of-security-parameters"></a>5.2 Biztonsági paraméterek indexe 


| **Biztonsági paraméter**  | **Section**         |
|-------------------------|---------------------|
| ProtectionElement (Védelemelem)       | *2.2.2.2*           |
| Gyakori titkosítási mezők | *[ISO/IEC 23001-7]* |

## <a name="53-common-encryption-boxes"></a>5.3 Közös titkosítási dobozok

A következő mezők lehetnek jelen a töredékválaszokban a közös titkosítás alkalmazásakor, és az [ISO/IEC 23001-7] vagy az [ISO/IEC 14496-12] mezőben vannak megadva:

1.  Védelmi rendszerspecifikus fejlécdoboz ('pssh')

2.  Mintatitkosító doboz ('senc')

3.  Minta kiegészítő információs eltolási doboz ('saio')

4.  Minta kiegészítő információs méret doboz ("saiz")

5.  Mintacsoport leírása mező ('sgpd')

6.  Minta a csoportdobozba ('sbgp')

---

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
