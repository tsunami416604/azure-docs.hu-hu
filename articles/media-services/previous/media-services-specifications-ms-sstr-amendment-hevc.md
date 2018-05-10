---
title: Az Azure Media Services - Smooth Streaming (MS-SSTR) protokoll módosítását a HEVC |} Microsoft Docs
description: Ez az előírás protokoll és töredezett MP4-alapú élő Stream továbbítása az Azure Media Services HEVC formátumát mutatja be. Ez a a Smooth Streaming protokoll (MS-SSTR) támogatását az HEVC betöltési documentaiton és adatfolyam-módosítás. Ebben a cikkben megadott csak annak érdekében, HEVC módosításokat kívül volt "(nincs módosítás)" azt jelzi, hogy a problémával csak szöveges másolódik.
services: media-services
documentationcenter: ''
author: cenkdin
manager: cfowler
editor: ''
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: johndeu;
ms.openlocfilehash: 78ec0e3ee4304e820bf64afa26440380887630a1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc"></a>Adatfolyam-továbbítási protokoll (MS-SSTR) módosításának zökkenőmentes HEVC

## <a name="1-introduction"></a>1. bevezetés 

Ez a cikk ismerteti a Smooth Streaming protokollspecifikációját [MS-SSTR] Smooth Streaming, HEVC kódolású videó engedélyezése alkalmazandó részletes módosításait. Ebben a specifikációban azt szerkezeti csak képes biztosítani a HEVC videó kodek szükséges módosításokat. A cikk az [MS-SSTR] specifikáció szerint ugyanazon számozási séma követi. Az üres hírek jelenik meg a teljes a cikk az [MS-SSTR] specifikáció pozícióját az olvasó elhelyezés állnak rendelkezésre.  "(Nincs módosítás)" azt jelzi, hogy az csak az egyértelműség szöveg másolódik.

A cikk ismerteti a videó kodek HEVC Smooth Streaming jegyzékben jelzés technikai kivitelezés követelményeit és rendelkező hivatkozások frissítve lett, hogy az aktuális MPEG szabványok gyűjteménye, amelyek HEVC, HEVC közös titkosítási és mezőben hivatkozik ISO talál médiafájl formátumát neveit frissítve lett, hogy azok konzisztensek legyenek a legújabb specifikációk. 

A hivatkozott Smooth Streaming protokollspecifikációját [MS-SSTR] formátumának lehet a következő módszerekkel élő és igény digitális adathordozón, például a hang- és a videó ismerteti: egy kiszolgálóról egy másik kiszolgálóra, illetve a webkiszolgálón, forrása a a kiszolgáló HTTP-ügyfelek számára.
Egy MPEG-4 használatát ([[MPEG4-RA])](http://go.microsoft.com/fwlink/?LinkId=327787)-alapú struktúra adattovábbítás HTTP Protokollon keresztül lehetővé teszi, hogy zökkenőmentes közel valós idejű különböző szolgáltatásminőségi szinteket tömörített médiatartalom közötti váltás. Az eredmény egy állandó lejátszás élmény, a felhasználó HTTP ügyfél, akkor is, ha hálózati és a videó megjelenítési feltételek módosítása az ügyfélszámítógépen és eszközön.

## <a name="11-glossary"></a>1.1 szószedet 

A következő feltételek definiált *[MS-GLOS]*:

>   **globálisan egyedi azonosítóját (GUID) univerzálisan egyedi azonosítóval (UUID)**

Ez a dokumentum a következő feltételek vonatkoznak:

>  **Összeállítási idő:** idő minta számára jelenik meg az ügyfél a [[ISO/IEC-14496-12].](http://go.microsoft.com/fwlink/?LinkId=183695)

>   **CENC**: Common Encryption, a második Edition [ISO/IEC 23001-7].

>   **Dekódolási idő:** minta kell adni az időt az ügyfélen dekódolandó a [[ISO/IEChttp://go.microsoft.com/fwlink/?LinkId=18369514496-12].](http://go.microsoft.com/fwlink/?LinkId=183695)

**töredék:** külön letölthető egységének **media** , amely magában foglalja egy vagy több **minták**.

>   **HEVC:** magas hatékonyságát videó kódolási, az [ISO/IEC 23008-2]

>   **manifest:** metaadatait a **bemutató** , amely lehetővé teszi egy ügyfelet, így a kérelmek **media**. **Media:** tömörített hang, a kép és a szöveg adatok számára, hogy az ügyfél által használt egy **bemutató**. **formátum:** egy jól meghatározott formátumú hang- vagy tüntetik a tömörített **minta**.

>   **bemutató:** a készlet az összes **adatfolyamok** és kapcsolódó metaadatok számára, hogy egyetlen film szükséges. **kérelem:** egy HTTP küldött üzenet az ügyféltől a kiszolgáló, a [[RFC2616].](http://go.microsoft.com/fwlink/?LinkId=90372) **válasz:** a kiszolgálóról az ügyfélnek küldött, az egy HTTP-üzenet [[RFC2616].](http://go.microsoft.com/fwlink/?LinkId=90372)

>   **Példa:** a legkisebb alapvető egység (például a keret), amelyben **media** van tárolása és feldolgozása.

>   **Előfordulhat, hogy, SHOULD, futnia kell, nem kell nem:** ezeket a feltételeket (a nagybetűs) használt a [[RFC2119].](http://go.microsoft.com/fwlink/?LinkId=90317) Választható működés használati összes utasítást vagy előfordulhat, hogy, SHOULD, vagy nem kell.

## <a name="12-references"></a>1.2 hivatkozások 
-----------

>   Nyissa meg a Microsoft-specifikációk dokumentációját nem vonatkozik az közzétételi évente mert hivatkozások a dokumentumok, amelyek gyakran frissülnek a legújabb verzióra. Egyéb dokumentumokat mutató hivatkozásokat tartalmaz egy közzétételi év, ha ilyen.

 ### <a name="121-normative-references"></a>1.2.1-es rendelkező hivatkozások 

>  [MS-SSTR] Adatfolyam-protokoll sima *v20140502* [ http://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/[MS-SSTR] .pdf](http://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/%5bMS-SSTR%5d.pdf)

>   [ISO/IEC 14496-12] Nemzetközi szabványügyi, "--audiovizuális objektumok kódolása--informatika rész 12: ISO alap médiafájl formátumát", ISO/IEC 14496-12:2014, a kiadás 4, valamint a módosítási csomag 1, módosítások 1 és 2.
>   <http://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>

>   [ISO/IEC 14496 – 15] Szabványügyi szervezet "--audiovizuális objektumok kódolása--informatika rész 15: NAL strukturált egység videó alap ISO-Media fájlformátum szállítása", ISO 14496-15:2015, Edition 3.
>   <http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>

>   [ISO/IEC 23008-2] Informatikai – nagy hatékonyságú kódolása és media kézbesítési heterogén környezetben – 2. rész: nagy hatékonyságú videó kódolási: 2013 vagy legújabb kiadása   <http://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>

>   [ISO/IEC 23001-7] Az információtechnológia – MPEG rendszertechnológiára érvényes – 7. rész: közös ISO-titkosításra kiinduló media formátumú fájlok, CENC Edition 2:2015 <http://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>

>   [RFC-6381] IETF RFC-6381, "a"Kodekek"és"Profilok"paramétereinek"gyűjtő"típusú" <http://tools.ietf.org/html/rfc6381>

>   [MPEG4-RA] A MP4 Registration Authority "MP4REG" [http://www.mp4ra.org   ](http://go.microsoft.com/fwlink/?LinkId=327787)

>   [RFC2119] Bradner, S., "kulcs szavak adja meg a követelmény szintre RFC használatra", BCP 14, RFC 2119, 1997. március,   [http://www.rfc-editor.org/rfc/rfc2119.txt   ](http://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 informatív hivatkozások 

>   [MS-GLOS] A Microsoft Corporation "*Windows protokollok fő szószedet*."

>   [RFC3548] Kiadás Josefsson, S., "A Base16 Base32 és Base64 adatok kódolások", RFC 3548, 2003. július [http://www.ietf.org/rfc/rfc3548.txt   ](http://go.microsoft.com/fwlink/?LinkId=90432)

>   [RFC5234] Laticauda, D., kiadás és Overell, p, "szintaxis leírásában BNF kiegészítve: ABNF", a standard 68-as, RFC 5234, 2008. január   [http://www.rfc-editor.org/rfc/rfc5234.txt   ](http://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>1.3 – áttekintés 
---------

>   A Smooth Streaming-előírásoknak megfelelően HEVC kézbesítését szükséges csak történt változások megadott feltételeknek. A hivatkozott Smooth Streaming specifikációját [MS-SSTR] a hely karbantartása változatlan szakasz fejlécek felsorolása.

## <a name="14-relationship-to-other-protocols"></a>1.4-es kapcsolat egyéb protokollok 
--------------------------------

## <a name="15-prerequisitespreconditions"></a>1.5-ös Előfeltételek/Előfeltételek 
----------------------------

## <a name="16-applicability-statement"></a>1.6-os alkalmazhatósági utasítás 
------------------------

## <a name="17-versioning-and-capability-negotiation"></a>1.7 versioning és funkció egyeztetése 
--------------------------------------

## <a name="18-vendor-extensible-fields"></a>1.8 szállító-bővíthető mezők 
-------------------------

>   A következő módszert alkalmazzák HEVC videó formátumban adatfolyamok azonosítása:

>   * **Az adathordozó-formátumok egyéni leíró kódok:** biztosítja ezt a képességet a **FourCC** mezőt, a szakasz *2.2.2.5*.
>   Végrehajtók bizonyosodjon meg arról, hogy bővítmények nem ütköznek a MPEG4-RA, a bővítmény kódok regisztrálása által [[ISO/IEC-14496-12] ](http://go.microsoft.com/fwlink/?LinkId=183695)

## <a name="19-standards-assignments"></a>1.9 szabványok hozzárendelések 
----------------------

# <a name="2-messages"></a>2 üzenet 

## <a name="21-transport"></a>2.1-es átviteli 
----------

## <a name="22-message-syntax"></a>2.2 üzenetek szintaxisának 
---------------

### <a name="221-manifest-request"></a>2.2.1-en manifest kérelem 

### <a name="222-manifest-response"></a>2.2.2 manifest válasz 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothStreamingMedia 

>   **MinorVersion (változó):** alverziószáma Manifest válaszüzenetben. 2 értékre kell állítani. (Nincs módosítás)

>   **Időskálára (változó):** az az időtartam attribútum, egy második lépésekben a számának megadott időskálára. Az alapértelmezett érték:
>   10000000. (Nincs módosítás)

>   Javasolt érték 90000 lapblobok videó keretek és törtrész képkockasebességhez videó (például 30/1.001 Hz) tartalmazó töredék pontos időtartama.

#### <a name="2222-protectionelement"></a>2.2.2.2 ProtectionElement 

A ProtectionElement kell jelenik meg, ha a Common Encryption (CENC) video- vagy adatfolyamok alkalmazták. Titkosított HEVC adatfolyamok megfelel az Common Encryption 2. kiadás [ISO/IEC 23001-7]. A VCL NAL egységek kell csak szelet adatokat titkosítani.

#### <a name="2223-streamelement"></a>2.2.2.3 StreamElement 

>   **StreamTimeScale (változó):** időtartama és az idő értékét az adatfolyam a lépésközök számát egy második megadott idő beosztása. 90000 érték HEVC adatfolyamok ajánlott. A hanghullám mintavételi gyakoriság (például 48000 vagy 44100) egyező érték használata ajánlott a hang adatfolyamokat.

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlPattern 

#### <a name="225-trackelement"></a>2.2.5 TrackElement 

>   **FourCC (változó):** mely formátum használata minden egyes minta négy karakterből álló kódot. A következő értéktartományon a következő szemantikai értelemben van fenntartva:

>  * "hev1": a szám videó minták HEVC videó, "hev1" minta leírás meghatározott formátumban [ISO/IEC-14496 – 15] használata.

>   **CodecPrivateData (változó):** , amely a nyomon követése, adja meg az adathordozó formátumra adott és az összes minta közös paraméterek adatok jelölt hexadecimális kódolt bájt karakterláncként. A formátum és a szemantikai szerinti bájt feladatütemezési változó értékét a **FourCC** mezőt az alábbiak szerint:

>   * Amikor egy TrackElement HEVC a videó bemutatja a **FourCC** mező egyenlő **"hev1"** és;

>   A **CodecPrivateData** mező tartalmaznia kell a következő bájtnál művelet, ABNF megadott hexadecimális kódolt karakterlánc-ábrázolása [[RFC5234]:](http://go.microsoft.com/fwlink/?LinkId=123096) (nincs módosítás a MS-SSTR)

>   * %x 00 %x 00 %x 00 %x 01 SPSField %x 00 %x 00 %x 00 %x 01 PPSField

>   * SPSField a feladatütemezési paraméter beállítása (SP) tartalmazza.

>   * PPSField a szelet paraméter beállítása (PPS) tartalmazza.

>   Megjegyzés: A videó paraméter beállítása (alelnökök) CodecPrivateData nem szerepel, de a fájl fejlécében, a "hvcC" mezőben tárolt fájlok kell szerepelnie. Smooth Streaming protokollt használó rendszerek kell jelezze további dekódolási paraméterek (például HEVC réteg) használatával az egyéni attribútum "kodekek."

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   A **SmoothStreamingMedia tartozó MajorVersion** mezőt kell beállítani 2, és **MinorVersion** mező 2 értékre kell állítani. (Nincs módosítás)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>2.2.3 töredék kérelem 

>   **Megjegyzés:**: a kért alapértelmezett media formátuma **MinorVersion** 2 és a "hev1" rendszer "iso8" márka ISO talál médiafájl formátumát a megadott [ISO/IEC 14496-12] ISO talál Media fájl formátuma negyedik Edition és a [ISO/IEC 23001-7] Közös titkosítási második kiadás.

### <a name="224-fragment-response"></a>2.2.4 töredék válasz 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   A **TfxdBox** elavult, és az a szám töredék dekódolása idő mezőbe (tfdt) [ISO/IEC 14496-12] szakaszban 8.8.12 megadott helyett funkciókat.

>   **Megjegyzés:**: ügyfél számíthatja töredéket időtartama a Track futtatása mezőben (trun) minta időtartamok megengedő vagy a szám és minták alkalommal fordult elő az alapértelmezett minta időtartam. "Tfdt" plusz töredék időtartama baseMediaDecodeTime eredménye a következő töredéke URL-idő paramétere.

>   A gyártó hivatkozás idő mezőbe (prft) egy Movie töredék megjelenítése (moof) előtt lehet beszúrni igény szerint, annak jelzésére nyomon követése töredék dekódolása idején a Movie töredék mezőbe hivatkozik az első mintában megfelelő UTC idő szerint megadott [ISO/IEC 14496 – 12] szakasz 8.16.5.

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   A **TfrfBox** elavult, és az a szám töredék dekódolása idő mezőbe (tfdt) [ISO/IEC 14496-12] szakaszban 8.8.12 megadott helyett funkciókat.

>   **Megjegyzés:**: ügyfél számíthatja töredéket időtartama a Track futtatása mezőben (trun) minta időtartamok megengedő vagy a szám és minták alkalommal fordult elő az alapértelmezett minta időtartam. "Tfdt" plusz töredék időtartama baseMediaDecodeTime eredménye a következő töredéke URL-idő paramétere. Előre keresse meg, azok késhet élő adatfolyam-címek elavult.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   A **TfhdBox** és a kapcsolódó mezők beágyazására egy minta metaadatok a töredékben szereplő alapértelmezett értéket. A szintaxisát a **TfhdBox** mező része a szigorú definiált követése töredék fejléc mezőben szintaxisa a következő [[ISO/IEC-14496-12]](http://go.microsoft.com/fwlink/?LinkId=183695) 8.8.7 szakasz.

>   **BaseDataOffset (8 bájt):** az eltolás bájtokban kezdetétől a **MdatBox** minta mezőjébe mezőjét a **MdatBox** mező. Ez a korlátozás jelezze, az alapértelmezett-alap-az-moof jelző (0x020000) kell beállítani.

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   A **TrunBox** és a kapcsolódó mezők beágyazására minta metaadatait a kért töredék száma. Szintaxisa a következő **TrunBox** verzió 1 követése töredék Futtatás mezőben meghatározott szigorú részhalmazát [[ISO/IEC-14496-](http://go.microsoft.com/fwlink/?LinkId=183695)*12]* 8.8.8 szakasz.

>   **SampleCompositionTimeOffset (4 bájt):** minden egyes minta a minta összeállítás idő eltolódását beállítani, hogy a töredékben szereplő első bemutatott minta bemutató idején a dekódolt mintát első dekódolási időpontnál. Negatív videó minta összeállítás eltolások kell használni,

>   a [[ISO/IEC-14496-12].](http://go.microsoft.com/fwlink/?LinkId=183695)

>   Megjegyzés: Ez elkerülhető a dekódolt kép puffer legnagyobb eltávolító késleltetés hang egyenlő elmaradt videó által okozott videó szinkronizálási hiba, és bemutató időzítési közötti alternatív töredék, amelyek különböző eltávolító késést tart fenn.

>   Ebben a szakaszban megadott ABNF megadott mezőket szintaxisa a következő [[RFC5234]](http://go.microsoft.com/fwlink/?LinkId=123096) változatlan marad, kivéve az alábbiak szerint:

>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>2.2.4.9 darabolható válasz közös mezők 

### <a name="225-sparse-stream-pointer"></a>2.2.5 ritka adatfolyam mutató 

### <a name="226-fragment-not-yet-available"></a>2.2.6 darabolható még nem érhető el 

### <a name="227-live-ingest"></a>2.2.7 működés közbeni betöltési 

#### <a name="2271-filetype"></a>2.2.7.1 fájltípus 

>   **Fájltípus (változó):** MPEG-4 altípus és szánt használatát határozza meg ([[MPEG4-RA])](http://go.microsoft.com/fwlink/?LinkId=327787) fájlt, és magas szintű attribútumok.

>   **MajorBrand (változó):** a médiafájl fő márkájú. Meg kell "isml."

>   **MinorVersion (változó):** a médiafájl alverzió. 1 értékre kell állítani.

>   **CompatibleBrands (változó):** határozza meg a támogatott márkájú MPEG-4.
>   Tartalmaznia kell "ccff" és "iso8."

>   Ebben a szakaszban megadott ABNF megadott mezőket szintaxisa a következő [[RFC5234]](http://go.microsoft.com/fwlink/?LinkId=123096) a következőképpen történik:

    FileType = MajorBrand MinorVersion CompatibleBrands
    MajorBrand = STRING_UINT32
    MinorVersion = STRING_UINT32
    CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)

**Megjegyzés:**: A kompatibilitási márkákat "ccff" és "iso8" azt jelzi, hogy töredék összhangban van a "Közös tároló formátum" és a Common Encryption [ISO/IEC 23001-7] és a ISO talál Media fájl formátuma Edition 4 [ISO/IEC 14496-12].

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StreamSMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>2.2.7.5 töredék 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 bővített fejléc követése töredék 

### <a name="228-server-to-server-ingest"></a>2.2.8 betöltési kiszolgálók 

# <a name="3-protocol-details"></a>3 protokoll adatai 


## <a name="31-client-details"></a>3.1 ügyféladatok 

### <a name="311-abstract-data-model"></a>3.1.1 absztrakt adatmodell 

#### <a name="3111-presentation-description"></a>3.1.1.1 bemutató leírása 

>   A bemutató leírása adatelem magában foglalja a bemutató minden metaadatait.

>   Bemutató metaadatok: Metaadatokat, amelyek közös összes adatfolyamot a bemutató készlete. Bemutató metaadatok magában foglalja a következő mezőket, szakaszában megadott *2.2.2.1*:

>   * **Főverzió**
>   * **MinorVersion**
>   * **Időskálára**
>   * **Időtartam**
>   * **IsLive**
>   * **LookaheadCount**
>   * **DVRWindowLength**

>   Bemutatókat tartalmazó HEVC adatfolyamokat kell beállítani:

    MajorVersion = 2
    MinorVersion = 2

>   LookaheadCount = 0 (Megjegyzés: a mezőkben elavult)

>   Bemutatók is kell beállítani:

    TimeScale = 90000

>   Adatfolyam gyűjtemény: Olyan elemek gyűjteménye, adatfolyam leírás adatok, mint a megadott szakasz *3.1.1.1.2*.

>   Védelmi Leírás: Egy elemek gyűjteménye, védelmi rendszer metaadatok leírását adatok, mint a megadott szakasz *3.1.1.1.1*.

##### <a name="31111-protection-system-metadata-description"></a>3.1.1.1.1 védelmi rendszer metaadatok leírása 

>   A védelmi rendszer metaadatok leírását adatelem egyetlen tartalom védelmi rendszerspecifikus metaadatok magában foglalja. (Nincs módosítás)

>   Védelmi fejléc Leírás: A védett tartalom metaadatok egy egyetlen tartalom védelmi rendszerre vonatkozó. Védelmi fejléc leírása tartalmazza a következő mezőket, szakaszában megadott *2.2.2.2*:

>   * **SystemID**
>   * **ProtectionHeaderContent**

##### <a name="31112-stream-description"></a>3.1.1.1.2 adatfolyam leírása 

###### <a name="311121-track-description"></a>3.1.1.1.2.1 nyomon leírása 

###### <a name="3111211-custom-attribute-description"></a>3.1.1.1.2.1.1 egyéni attribútum leírása 

##### <a name="3113-fragment-reference-description"></a>3.1.1.3 töredék hivatkozás leírása 

###### <a name="31131-track-specific-fragment-reference-description"></a>3.1.1.3.1 követése-specifikus töredék hivatkozás leírása 

#### <a name="3112-fragment-description"></a>3.1.1.2 töredék leírása 

##### <a name="31121-sample-description"></a>3.1.1.2.1 Mintaleírás 

### <a name="312-timers"></a>3.1.2 időzítők 

### <a name="313-initialization"></a>3.1.3 inicializálása 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 magasabb szintű kiváltott esemény 

#### <a name="3141-open-presentation"></a>3.1.4.1 Nyissa meg a bemutató 

#### <a name="3142-get-fragment"></a>3.1.4.2 töredék beolvasása 

#### <a name="3143-close-presentation"></a>3.1.4.3 zárja be a bemutató 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5 eseményeket dolgoz fel, és alkalmazás-előkészítés szabályok 

#### <a name="3151-manifest-request-and-manifest-response"></a>3.1.5.1 manifest kérés és válasz Manifest 

#### <a name="3152-fragment-request-and-fragment-response"></a>3.1.5.2 darabolható kérés és válasz darabolható

## <a name="32-server-details"></a>3.2-es kiszolgáló részletei

## <a name="33-live-encoder-details"></a>3.3 élő kódoló részletei 

# <a name="4-protocol-examples"></a>4 protokoll példák 

# <a name="5-security"></a>5 biztonsági 

## <a name="51-security-considerations-for-implementers"></a>5.1 végrehajtók biztonsági szempontjai 
-----------------------------------------

>   Ha a tartalom átvitelét a protokollt használó kereskedelmi értéke magas, a tartalom védelmi rendszer használandó a tartalom jogosulatlan használatának megakadályozása érdekében. A **ProtectionElement** egy tartalom védelmére rendszer használatával kapcsolatos metaadatok végrehajtásához használható. Védett audió és videó titkosított MPEG közös titkosítási második Edition által megadott: 2015 [ISO/IEC 23001-7].

>   **Megjegyzés:**: HEVC videót, csak szelet VCL NALs az adattitkosítás. Szelet fejlécek és egyéb NALs előtt visszafejtési bemutató alkalmazások számára érhetők el. biztonságos videó az elérési utak, titkosított információ nincs bemutató alkalmazások.

# <a name="52-index-of-security-parameters"></a>5.2 a biztonsági paraméterek indexe 
-----------------------------


| **Biztonsági paraméter**  | **Section**         |
|-------------------------|---------------------|
| ProtectionElement       | *2.2.2.2*           |
| Közös titkosítási mezőbe | *[ISO/IEC 23001-7]* |

# <a name="53-common-encryption-boxes"></a>5.3 közös titkosítási mezőbe
-----------------------

Az alábbi mezőkben töredék válaszok jelen lehetnek, amikor Common Encryption alkalmazni, és meg van határozva a [ISO/IEC 23001-7] vagy [ISO/IEC 14496-12]:

1.  Védelmi rendszer adott fejléc-mező (pssh)

2.  Minta titkosítási területen (senc)

3.  Minta kiegészítő információkat eltolás területen (saio)

4.  Minta kiegészítő információkat méretezés (saiz)

5.  A minta leírás csoportpanel (sgpd)

6.  Minta csoportpanel (sbgp)

-----------------------

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
