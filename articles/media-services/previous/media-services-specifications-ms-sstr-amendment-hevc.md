---
title: Az Azure Media Services - Smooth Streaming-protokoll (MS-SSTR) módosítását a HEVC |} A Microsoft Docs
description: Ez az meghatározás ismerteti a protokoll és a darabolt MP4-alapú élő Stream továbbítása az Azure Media Servicesben HEVC formátumát. Ez a módosítás a Smooth Streaming protokoll (MS-SSTR) támogatását, a betöltési HEVC dokumentációját és a streamelési. Csak a szükséges, hogy HEVC módosításokat ebben a cikkben megadott kívül volt "(nincs módosítás)" azt jelzi, hogy csak magyarázó szöveg másolódik.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: johndeu;
ms.openlocfilehash: 3261e84dcd3dee06071b0fed78b61e2959e3bbf9
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978766"
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc-legacy"></a>Zökkenőmentes Streamelési protokoll (MS-SSTR) módosítását a HEVC (örökölt)

## <a name="1-introduction"></a>1. bevezetés 

Ez a cikk ismerteti a alkalmazni lehessen a Smooth Streaming-protokoll specifikációinak [MS-SSTR] Smooth Streaming-HEVC kódolású videó engedélyezése részletes módosításait. Ez az meghatározás csak a szükséges, hogy a videó HEVC kodek módosítások azt tagolni. A cikk követi, mint az [MS-SSTR] specifikáció számozási ugyanazzal a sémával. Az egész cikkben bemutatott üres hírek az [MS-SSTR] specifikáció pozícióját az olvasó elhelyezés vannak megadva.  "(Nincs módosítás)" azt jelzi, hogy az egyértelműség csak szöveg másolódik.

A cikk a videó kodek HEVC Smooth Streaming jegyzékfájlban jelzés technikai megvalósítás követelményei és rendelkező hivatkozások frissítve lett, hogy a jelenlegi MPEG szabványok, például HEVC, Common Encryption HEVC és a box hivatkozik ISO alap Mediálního Souboru neveit összhangban vannak a legújabb specifikációk frissítve lett-e. 

A hivatkozott Smooth Streaming-protokoll specifikációinak [MS-SSTR] ismerteti, hogy az élő és igény szerinti digitális média, például az audio- és videotartalmak, az a következő módszerekkel formátumának: egy kiszolgálóról egy másik kiszolgálóra, és a egy webkiszolgálóhoz forrása a egy HTTP-alapú kiszolgáló.
Egy MPEG-4 használatát ([[MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787)-alapú struktúra adattovábbítás HTTP protokollon keresztül lehetővé teszi, hogy zökkenőmentesen, közel valós időben tömörített médiatartalmak különböző minőségi szintek közötti váltás. Ez egy állandó lejátszás nyújtotta szolgáltatások körét a HTTP ügyfél végfelhasználó még akkor is, ha a hálózat és a videó megjelenítési feltételek módosítása a ügyfélszámítógépen és eszközön.

## <a name="11-glossary"></a>1.1 szószedet 

Az alábbi feltételek vannak definiálva *[MS-GLOS]*:

>   **globálisan egyedi azonosítóját (GUID) univerzálisan egyedi azonosítót (UUID)**

Ez a dokumentum az alábbi feltételek vonatkoznak:

>  **összeállítás idő:** Az idő minta egyike jelenik meg az ügyfél a [[ISO/IEC-14496 – 12].](https://go.microsoft.com/fwlink/?LinkId=183695)

>   **CENC**: Common Encryption, meghatározva [ISO/IEC 23001-7] második kiadás.

>   **Dekódolási idő:** Az idő minta nem kell Nedal dekódovat az ügyfélen, ahogyan az az [[ISO/IEC http://go.microsoft.com/fwlink/?LinkId=18369514496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)

**töredék:** Egy külön letölthető egysége **media** , amely magában foglalja egy vagy több **minták**.

>   **HEVC:** Magas hatékonyságát a videó kódolása, az [ISO/IEC 23008-2]

>   **jegyzékfájl:** Kapcsolatos metaadatokat a **bemutató** , amely lehetővé teszi, hogy a kéréseket ügyfél **media**. **adathordozó:** Az ügyfél használják lejátszása hang, videó és szöveges adatok tömörített egy **bemutató**. **médiaformátum:** A hang- vagy jelölő olyan tömörített, jól definiált formátumú **minta**.

>   **bemutató:** A készlet összes **Streamek** és a egy egyetlen movie lejátszásához szükséges kapcsolódó metaadatokat. **A kérelem:** Az ügyfélről a kiszolgálónak küldött, ahogyan az a HTTP üzenet [[RFC2616].](https://go.microsoft.com/fwlink/?LinkId=90372) **Válasz:** A kiszolgálóról az ügyfélnek küldött, ahogyan az a HTTP üzenet [[RFC2616].](https://go.microsoft.com/fwlink/?LinkId=90372)

>   **Minta:** A legkisebb elosztásnak (például egy keretben), amelyben **media** tárolt és feldolgozott.

>   **MÁJUS, ÉRDEMES, KELL, NEM KELL, NEM KELL:** Ezeket a feltételeket (a nagybetűs) leírtak szerint használt [[RFC2119].](https://go.microsoft.com/fwlink/?LinkId=90317) Választható működés feltételeit összes utasítást vagy előfordulhat, hogy, SHOULD, vagy nem kell.

## <a name="12-references"></a>1.2 referenciák

>   Nyissa meg a Microsoft-specifikációk dokumentációs hivatkozásokat nem tartalmazzák a közzétételi év mivel hivatkozások a dokumentumok, amelyek gyakran frissülnek a legújabb verzióra. Egyéb dokumentumokra mutató hivatkozásokat tartalmaznak egy közzétételi év, ha ilyen.

### <a name="121-normative-references"></a>1.2.1-es rendelkező hivatkozások 

>  [MS-SSTR] Smooth Streaming protokoll *v20140502* [https://msdn.microsoft.com/library/ff469518.aspx](https://msdn.microsoft.com/library/ff469518.aspx)

>   [ISO/IEC 14496 – 12] Nemzetközi szabványügyi szervezet, "--audiovizuális objektumok kódolása – információtechnológiai rész 12: ISO alap Mediálního Souboru", ISO/IEC 14496-12:2014, a kiadás 4, valamint a módosítási csomag 1, 1. és 2 módosításait.
>   <http://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>

>   [ISO/IEC 14496-15] International Organization for Standardization, "Information technology -- Coding of audio-visual objects -- Part 15: Kocsivissza NAL strukturált egység videó ISO alap Media fájlformátumban", ISO 14496-15:2015, Edition 3.
>   <http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>

>   [ISO/IEC 23008-2] Információtechnológiai – nagy hatékonyságú kódolása és media kézbesítése a heterogén környezetek – 2. rész: Nagy hatékonyságú videó kódolási: 2013-as vagy a legújabb kiadás   <http://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>

>   [ISO/IEC 23001-7] Információtechnológiai – MPEG rendszerek technológiák – 7. rész: ISO alap media fájl formátumú fájlok, CENC Edition 2:2015 Common Encryption titkosítás <http://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>

>   [RFC-6381] IETF RFC-6381, "a"Kodekek"és"Profilok"paramétereket"gyűjtőhöz"adathordozó-típusok" <http://tools.ietf.org/html/rfc6381>

>   [MPEG4-RA] A MP4 Igénylésregisztrációs szolgáltatóként, a "MP4REG", [http://www.mp4ra.org   ](https://go.microsoft.com/fwlink/?LinkId=327787)

>   [RFC2119] Bradner, s, "kulcs szavak RFC jelzése követelményszintjének való használatra" BCP 14, RFC 2119, 1997. március   [http://www.rfc-editor.org/rfc/rfc2119.txt   ](https://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 informatív referenciák 

>   [MS-GLOS] A Microsoft Corporation, "*Windows protokollok fő szószedet*."

>   [RFC3548] Josefsson, s, kiadás "A Base16 Base32 és Base64 kódolású adatok kódolásokat", RFC 3548, 2003. július [http://www.ietf.org/rfc/rfc3548.txt   ](https://go.microsoft.com/fwlink/?LinkId=90432)

>   [RFC5234] Laticauda, d, kiadás és Overell, o., "kibővített BNF szintaxis leírásában: ABNF", 68-as, RFC 5234, január 2008, Standard   [http://www.rfc-editor.org/rfc/rfc5234.txt   ](https://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>1.3 – áttekintés 

>   A Smooth Streaming specifikációinak HEVC kézbesítéséhez szükséges csak történt változások alatt vannak megadva. A hivatkozott Smooth Streaming-specifikációnak [MS-SSTR] a hely karbantartása változatlan szakaszfejléceket felsorolása.

## <a name="14-relationship-to-other-protocols"></a>1.4-es egyéb protokollok kapcsolat 

## <a name="15-prerequisitespreconditions"></a>1.5-ös Előfeltételek az és Előfeltételek 

## <a name="16-applicability-statement"></a>1.6-os alkalmazhatósági nyilatkozatot 

## <a name="17-versioning-and-capability-negotiation"></a>1.7-es verziókezelés és képesség egyeztetése 

## <a name="18-vendor-extensible-fields"></a>1.8-as szállító-bővíthető mezők 

>   A következő metódust kell használni HEVC videó formátumban Streamek azonosításához:

>   * **Egyéni kódok leíró médiaformátumok:** Ez a funkció által biztosított a **FourCC** szakaszban megadott mezőt *2.2.2.5*.
>   Végrehajtók is győződjön meg arról, hogy bővítmények nem ütköznek a MPEG4-RA, a bővítmény kódok regisztrálás által [[ISO/IEC-14496 – 12] ](https://go.microsoft.com/fwlink/?LinkId=183695)

## <a name="19-standards-assignments"></a>1.9 szabványok hozzárendelések 

## <a name="2-messages"></a>2 darab 

## <a name="21-transport"></a>2.1 átviteli

## <a name="22-message-syntax"></a>2.2-es üzenet szintaxis 

### <a name="221-manifest-request"></a>2.2.1-es jegyzékfájl kérelem 

### <a name="222-manifest-response"></a>2.2.2 jegyzékfájl válasz 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothStreamingMedia 

>   **MinorVersion (változó):** A jegyzékfájl válaszüzenet alverziója. 2-re kell állítani. (Nincs változás)

>   **Időskálára (változó):** Egy második lépésközök számát megadott időtartam attribútum idő mértékét. Az alapértelmezett érték
>   10000000. (Nincs változás)

>   Javasolt érték 90000 videókban és tört képkockasebesség videót (például 30/1.001 Hz) tartalmazó töredék pontos időtartama megjelenítésére.

#### <a name="2222-protectionelement"></a>2.2.2.2 ProtectionElement 

A ProtectionElement kell jelenik meg, ha az általános titkosítás (CENC) video-és audióstreameket lett alkalmazva. Titkosított HEVC Streamek megfelel az általános titkosítás 2. kiadás [ISO/IEC 23001-7]. Adatok szeletelése csak az a VCL NAL egységek kell titkosítva.

#### <a name="2223-streamelement"></a>2.2.2.3 StreamElement 

>   **StreamTimeScale (variable):** Az ideje és időtartama, valamint a streamet, a megadott értékeket is a lépésközök számát egy második időskálára. 90000 érték HEVC adatfolyamok használata ajánlott. Audio-adatfolyamokat a hullám minta gyakorisága (például 48000 vagy 44100) egyező érték használata ajánlott.

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlPattern 

#### <a name="225-trackelement"></a>2.2.5 TrackElement 

>   **FourCC (változó):** Egy négy karakterből álló kódot, amely azonosítja az adathordozó formátumot minden mintához szolgál. Az értékek a következő tartomány a következő szemantikai értelemben van fenntartva:

>  * "hev1": A track használata HEVC videó, használja a "hev1" a videó minták minta [ISO/IEC-14496-15] a megadott leírás formátum.

>   **CodecPrivateData (változó):** Bájt hexadecimális kódolt karakterlánc, amely meghatározza az adott media formátumra és az összes minta közös paraméterek a pályán adatokat jelöli. A formátum és a szemantikai jelentést készít bájt művelettől értékét a **FourCC** mezőt az alábbiak szerint:

>   * Ha egy TrackElement HEVC videó ismerteti a **FourCC** mező egyenlő **"hev1"** és;

>   A **CodecPrivateData** mezőt tartalmaznia kell egy hexadecimális kódolású karakterláncként az alábbi bájt folyamattal, a megadott ABNF [[RFC5234]:](https://go.microsoft.com/fwlink/?LinkId=123096) (nincs módosítás a MS-SSTR)

>   * %x 00 %x 00 %x 00 %x 01 SPSField %x 00 %x 00 %x 00 %x 01 PPSField

>   * SPSField tartalmazza a feladatütemezési paraméter beállítása (Szervizcsomagok).

>   * PPSField a szelet paraméter beállítása (PPS) tartalmazza.

>   Megjegyzés: A videó paraméter beállítása (alelnökök) CodecPrivateData nem szerepel, de a fájl fejlécében tárolt fájlok "hvcC" mezőben kell tartoznia. Smooth Streaming protokollt használó rendszerek kell jelezze további dekódolási paraméterek (például HEVC réteg) használatával az egyéni attribútum "kodekek."

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   A **SmoothStreamingMedia a főverzió** mező 2 értékre kell állítani és **MinorVersion** mező 2 értékre kell állítani. (Nincs változás)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>2.2.3 töredék kérelem 

>   **Megjegyzés**: Az alapértelmezett adathordozó formátum, lekéri az **MinorVersion** 2 és a "hev1" a "iso8" márka ISO alap Mediálního Souboru [ISO/IEC 14496 – 12] ISO alap Media fájl formátuma negyedik kiadása és [ISO/IEC 23001-7] Common Encryption második megadott Kiadás.

### <a name="224-fragment-response"></a>2.2.4 töredék válasz 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   A **TfxdBox** elavult, és a függvény váltotta fel a nyomon követése töredék dekódolási idő mezőbe (tfdt) [ISO/IEC 14496 – 12] szakaszban 8.8.12 megadott.

>   **Megjegyzés**: Egy ügyfél egy kódrészletet időtartama számíthatja megengedő a Track futtatása mezőben (trun) minta időtartamok vagy egységárat minták száma az alapértelmezett minta időtartama lejár. A baseMediaDecodeTime a "tfdt" plusz töredék időtartam a következő töredék idő paramétere az URL megegyezik.

>   Egy előállítói referencia idő mező (prft) kell beilleszteni egy filmet töredék mező (moof) előtt szükség szerint, jelzi az UTC-idő nyomon követése töredék dekódolási idő a film töredék mezőben hivatkozik az első minta megfelelő megadott [ISO/IEC 14496 – 12] szakasz 8.16.5.

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   A **TfrfBox** elavult, és a függvény váltotta fel a nyomon követése töredék dekódolási idő mezőbe (tfdt) [ISO/IEC 14496 – 12] szakaszban 8.8.12 megadott.

>   **Megjegyzés**: Egy ügyfél egy kódrészletet időtartama számíthatja megengedő a Track futtatása mezőben (trun) minta időtartamok vagy egységárat minták száma az alapértelmezett minta időtartama lejár. A baseMediaDecodeTime a "tfdt" plusz töredék időtartam a következő töredék idő paramétere az URL megegyezik. Előretekintés címek elavultak, mert azok késleltetés élő adatfolyam-továbbítás.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   A **TfhdBox** és a kapcsolódó mezők beágyazására minta metaadatok a részlet egy alapértelmezett értéket. Szintaxisát a **TfhdBox** mező egy szigorú alkészletéből állnak, a nyomon követése töredék fejléc mezőben definiált szintaxisa a következő [[ISO/IEC-14496 – 12]](https://go.microsoft.com/fwlink/?LinkId=183695) 8.8.7 szakaszban.

>   **BaseDataOffset (8 bájt):** Az eltolás (bájt) kezdetétől fogva a **MdatBox** mezőt a mintául szolgáló mezője a **MdatBox** mező. Az alapértelmezett – base-az-moof jelölőt (0x020000) kell beállítani, hogy jelezze ezt a korlátozást.

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   A **TrunBox** és a kapcsolódó mezők beágyazására minta metaadatait a kért töredék száma. Szintaxisa a következő **TrunBox** egy szigorú alkészletéből verzió 1 követése töredék futtatása mezőben megadott [[ISO/IEC-14496-](https://go.microsoft.com/fwlink/?LinkId=183695)*12]* 8.8.8 szakaszban.

>   **SampleCompositionTimeOffset (4 bájt):** A minta összeállítás idő eltolása beállítani, hogy a részlet az első bemutatott mintát bemutató idején az dekódolási idő az első dekódolt minta nem minden egyes minta. Negatív videó minta összeállítás eltolások kell használni,

>   ahogyan az az [[ISO/IEC-14496 – 12].](https://go.microsoft.com/fwlink/?LinkId=183695)

>   Megjegyzés: Videó, hang egyenlő a dekódolt kép puffer legnagyobb eltávolító késleltetés elmaradt által okozott videó szinkronizálási hiba elkerülhető, és fenntartja bemutató időzítési között, előfordulhat, hogy különböző eltávolító késések alternatív töredék.

>   Ebben a szakaszban megadott ABNF megadott mezőket szintaxisa a következő [[RFC5234],](https://go.microsoft.com/fwlink/?LinkId=123096) változatlan marad, kivéve a következők szerint:

>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>2.2.4.9 töredékben válasz általános mezők 

### <a name="225-sparse-stream-pointer"></a>2.2.5 ritka Stream mutató 

### <a name="226-fragment-not-yet-available"></a>2.2.6 töredékben még nem érhető el 

### <a name="227-live-ingest"></a>2.2.7 élő betöltés 

#### <a name="2271-filetype"></a>2.2.7.1 FileType 

>   **Fájltípus (változó):** megadja az MPEG-4 szánt és altípus használatát ([[MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787) fájlt, és magas szintű attribútumok.

>   **MajorBrand (változó):** A fő márka a médiafájl. Állítsa "isml."

>   **MinorVersion (változó):** A médiafájl alverziója. 1-re kell állítani.

>   **CompatibleBrands (változó):** Adja meg a támogatott migrációval MPEG-4.
>   Tartalmaznia kell "ccff" és "iso8."

>   Ebben a szakaszban megadott ABNF megadott mezőket szintaxisa a következő [[RFC5234],](https://go.microsoft.com/fwlink/?LinkId=123096) a következő:

    FileType = MajorBrand MinorVersion CompatibleBrands
    MajorBrand = STRING_UINT32
    MinorVersion = STRING_UINT32
    CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)

**Megjegyzés**: A kompatibilitási márkái "ccff" és "iso8" azt jelzik, hogy megfelelnek-e töredék "Közös tároló formátum" és a Common Encryption [ISO/IEC 23001-7] és ISO alap Media fájl formátuma Edition 4 [ISO/IEC 14496 – 12].

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StreamSMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>2.2.7.5 töredék 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 fejléc kiterjesztett követése töredék 

### <a name="228-server-to-server-ingest"></a>2.2.8 betöltési kiszolgálók 

## <a name="3-protocol-details"></a>3 protokoll részletei 


## <a name="31-client-details"></a>3.1-es ügyfél részletei 

### <a name="311-abstract-data-model"></a>3.1.1 absztrakt adatmodellt 

#### <a name="3111-presentation-description"></a>3.1.1.1 bemutató leírása 

>   A bemutató leírása adatelem magában foglalja a bemutató minden metaadat.

>   Bemutató metaadatok: Metaadatok, amely a bemutató minden Streamek közös készlete. Bemutató metaadatok a következő mezőket, szakaszban megadott tartalmazza *2.2.2.1*:

>   * **MajorVersion**
>   * **MinorVersion**
>   * **TimeScale**
>   * **Időtartam**
>   * **IsLive**
>   * **LookaheadCount**
>   * **DVRWindowLength**

>   A bemutatókat tartalmazó HEVC Streamek kell beállítani:

    MajorVersion = 2
    MinorVersion = 2

>   LookaheadCount = 0 (Megjegyzés: A mezők elavult)

>   Bemutatókat is kell beállítania:

    TimeScale = 90000

>   Stream-gyűjtemény: Leírás a Stream adatelem, gyűjteménye szakaszában meghatározottak szerint *3.1.1.1.2*.

>   Protection leírása: Elemgyűjtemény metaadatok rendszerleírás védelmi adatok, szakaszában meghatározottak szerint *3.1.1.1.1*.

##### <a name="31111-protection-system-metadata-description"></a>3.1.1.1.1 metaadatok védelmi rendszerleírás. 

>   A védelem metaadatok rendszerleírás adatelem egyetlen Content Protection rendszerspecifikus metaadatok magában foglalja. (Nincs változás)

>   Védelem fejléc leírása: A Content protection metaadatokat, amelyek egyetlen Content Protection rendszer vonatkozik. Védelem fejléc leírása a következő mezőket, szakaszban megadott tartalmazza *2.2.2.2*:

>   * **SystemID**
>   * **ProtectionHeaderContent**

##### <a name="31112-stream-description"></a>3.1.1.1.2 Stream leírása 

###### <a name="311121-track-description"></a>3.1.1.1.2.1 nyomon leírása 

###### <a name="3111211-custom-attribute-description"></a>3.1.1.1.2.1.1 egyéni attribútum leírása 

##### <a name="3113-fragment-reference-description"></a>3.1.1.3 töredék hivatkozás leírása 

###### <a name="31131-track-specific-fragment-reference-description"></a>3.1.1.3.1 követése-specifikus töredék hivatkozás leírása 

#### <a name="3112-fragment-description"></a>3.1.1.2 töredék leírása 

##### <a name="31121-sample-description"></a>3.1.1.2.1 Mintaleírás 

### <a name="312-timers"></a>3.1.2 időzítők 

### <a name="313-initialization"></a>3.1.3 inicializálása 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 a magasabb rétegek kiváltott események 

#### <a name="3141-open-presentation"></a>3.1.4.1 bemutató megnyitása 

#### <a name="3142-get-fragment"></a>3.1.4.2 töredék beolvasása 

#### <a name="3143-close-presentation"></a>3.1.4.3 bemutató bezárása 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5 eseményeket dolgoz fel és alkalmazás-előkészítés szabályok 

#### <a name="3151-manifest-request-and-manifest-response"></a>3.1.5.1 manifest kérelem és válasz Manifest 

#### <a name="3152-fragment-request-and-fragment-response"></a>3.1.5.2 töredékben kérelem és válasz Töredékben

## <a name="32-server-details"></a>3.2-es kiszolgáló részletei

## <a name="33-live-encoder-details"></a>3.3-as élőadás-kódoló részletei 

## <a name="4-protocol-examples"></a>4 protokoll-példák 

## <a name="5-security"></a>5 biztonsági 

## <a name="51-security-considerations-for-implementers"></a>5.1 a szolgáltatást bevezetők részére biztonsági szempontok

>   Ha a tartalom átvitelét a protokoll használatával kereskedelmi értékes, egy tartalom védelmére rendszer használandó a tartalom jogosulatlan használatának megakadályozása érdekében. A **ProtectionElement** számára, a Content Protection rendszer használatával kapcsolatos metaadatok is használható. Védett hang- és videotartalmak titkosított MPEG Common Encryption második kiadás által megadott: 2015 [ISO/IEC 23001-7].

>   **Megjegyzés**: HEVC videókhoz csak szelet VCL NALs az adattitkosítás. Szelet fejlécek és más NALs előtt visszafejtési bemutató alkalmazások érhetők el. egy biztonságos videó elérési utat, a titkosított adatok nem áll rendelkezésre, bemutató alkalmazások.

## <a name="52-index-of-security-parameters"></a>5.2 a biztonsági paraméterek-Index 


| **Biztonsági paraméter**  | **Section**         |
|-------------------------|---------------------|
| ProtectionElement       | *2.2.2.2*           |
| Common Encryption mezők | *[ISO/IEC 23001-7]* |

## <a name="53-common-encryption-boxes"></a>5.3 common Encryption mezők

Az alábbi mezőkben szerepel töredék válaszok lehet, ha a Common Encryption titkosítás a alkalmazni, és meg van határozva a [ISO/IEC 23001-7] vagy [ISO/IEC 14496 – 12]:

1.  Védelem rendszer adott fejléc-Box (pssh)

2.  Minta titkosítási mezőben (senc)

3.  Minta kiegészítő információkat eltolás területen (saio)

4.  Minta kiegészítő információkat mérete mező (saiz)

5.  Leírás Mintacsoportmező (sgpd)

6.  Minta (sbgp) elemcsoport

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
