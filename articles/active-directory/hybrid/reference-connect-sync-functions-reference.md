---
title: 'Az Azure AD Connect szinkronizálása: Hivatkozási függvények |} A Microsoft Docs'
description: Az Azure AD Connect szinkronizálási deklaratív kiépítés kifejezéseinek hivatkozása.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4f525ca0-be0e-4a2e-8da1-09b6b567ed5f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1a40399ab0e27be5ba9dd01f2647bd5b8ccf10e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56202506"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Az Azure AD Connect szinkronizálása: Functions – referencia
Az Azure AD Connectben a funkciók a szinkronizálás során egy attribútumérték módosítására szolgálnak.  
A szintaxist a függvények van kifejezve a következő formátumban:  
`<output type> FunctionName(<input type> <position name>, ..)`

Ha egy függvény túl van terhelve, és több szintaxisok fogad el, az összes érvényes szintaxisok jelennek meg.  
A functions listaobjektum, és azok győződjön meg arról, hogy a típus átadott megegyezik a dokumentált típusa.  
A típus nem felel meg, ha hiba fordul elő.

A típus van megadva a következő szintaxissal:

* **doboz** – bináris
* **logikai** – logikai
* **DT** – UTC szerinti dátuma/ideje
* **Enum** – ismert állandók felsorolása
* **Exp** – eredménye egy logikai kifejezés
* **mvbin** – Multi-Valued bináris
* **mvstr** – Multi-Valued karakterlánc
* **mvref** – Multi-Valued referencia
* **NUM** – numerikus
* **REF** – referencia
* **Str** – karakterlánc
* **var** – variant (majdnem) bármilyen más típusú
* **typ void** – értéket nem ad vissza.

A functions típusait **mvbin**, **mvstr**, és **mvref** többértékű attribútumok csak működik. A Functions **bin**, **str**, és **ref** munkahelyi egyaránt egyértékű és többértékű attribútumok.

## <a name="functions-reference"></a>Functions – referencia
| Függvények listája. |  |  |  |  |
| --- | --- | --- | --- | --- | --- |
| **Tanúsítvány** | | | | |
| [CertExtensionOids](#certextensionoids) |[CertFormat](#certformat) |[CertFriendlyName](#certfriendlyname) |[CertHashString](#certhashstring) | |
| [CertIssuer](#certissuer) |[CertIssuerDN](#certissuerdn) |[CertIssuerOid](#certissueroid) |[CertKeyAlgorithm](#certkeyalgorithm) | |
| [CertKeyAlgorithmParams](#certkeyalgorithmparams) |[CertNameInfo](#certnameinfo) |[CertNotAfter](#certnotafter) |[CertNotBefore](#certnotbefore) | |
| [CertPublicKeyOid](#certpublickeyoid) |[CertPublicKeyParametersOid](#certpublickeyparametersoid) |[CertSerialNumber](#certserialnumber) |[CertSignatureAlgorithmOid](#certsignaturealgorithmoid) | |
| [CertSubject](#certsubject) |[CertSubjectNameDN](#certsubjectnamedn) |[CertSubjectNameOid](#certsubjectnameoid) |[certThumbprint](#certthumbprint) | |
[ CertVersion](#certversion) |[IsCert](#iscert) | | | |
| **Átalakítás** | | | | |
| [CBool](#cbool) |[CDate](#cdate) |[CGuid](#cguid) |[ConvertFromBase64](#convertfrombase64) | |
| [ConvertToBase64](#converttobase64) |[ConvertFromUTF8Hex](#convertfromutf8hex) |[ConvertToUTF8Hex](#converttoutf8hex) |[CNum](#cnum) | |
| [CRef](#cref) |[CStr](#cstr) |[StringFromGuid](#StringFromGuid) |[StringFromSid](#stringfromsid) | |
| **Dátum és idő** | | | | |
| [DateAdd](#dateadd) |[DateFromNum](#datefromnum) |[FormatDateTime](#formatdatetime) |[most](#now) | |
| [NumFromDate](#numfromdate) | | | | |
| **Könyvtár** | | | | |
| [DNComponent](#dncomponent) |[DNComponentRev](#dncomponentrev) |[EscapeDNComponent](#escapedncomponent) | | |
| **Értékelés** | | | | |
| [IsBitSet](#isbitset) |[IsDate](#isdate) |[IsEmpty](#isempty) |[IsGuid](#isguid) | |
| [IsNull](#isnull) |[IsNullOrEmpty](#isnullorempty) |[IsNumeric](#isnumeric) |[IsPresent](#ispresent) | |
| [IsString](#isstring) | | | | |
| **Matematikai** | | | | |
| [BitAnd](#bitand) |[BitOr](#bitor) |[RandomNum](#randomnum) | | |
| **Többértékű** | | | | |
| [tartalmaz](#contains) |[Száma](#count) |[Elem](#item) |[ItemOrNull](#itemornull) | |
| [Csatlakozás](#join) |[RemoveDuplicates](#removeduplicates) |[Felosztás](#split) | | |
| **Programfolyamat** | | | | |
| [Hiba történt](#error) |[IIF](#iif) |[Kiválasztás](#select) |[Switch](#switch) | |
| [ahol](#where) |[a](#with) | | | |
| **Szöveg** | | | | |
| [GUID](#guid) |[InStr](#instr) |[InStrRev](#instrrev) |[LCase](#lcase) | |
| [Balra](#left) |[Len](#len) |[LTrim](#ltrim) |[Mid](#mid) | |
| [PadLeft](#padleft) |[PadRight](#padright) |[PCase](#pcase) |[cserélje le](#replace) | |
| [ReplaceChars](#replacechars) |[Jobbra](#right) |[RTrim](#rtrim) |[Trim](#trim) | |
| [UCase](#ucase) |[A Word](#word) | | | |

- - -
### <a name="bitand"></a>BitAnd
**Leírás:**  
A BitAnd függvénynek megadott bits értéket állítja be.

**Szintaxis:**  
`num BitAnd(num value1, num value2)`

* Érték1, érték2: numerikus értékeket, amelyeket együttesen kell érték

**Megjegyzés:**  
Ez a függvény mindkét paraméter alakítja át a bináris kódolású karakterláncként, és beállítja egy kicsit:

* 0 – Ha egy vagy mindkettő a megfelelő bit *maszk* és *jelző* : 0
* 1. Ha mindkettő a megfelelő bits 1.

Más szóval akkor adja vissza 0 minden esetben, kivéve, ha a megfelelő bitjeinek száma, mindkét paraméter 1.

**Példa**  
`BitAnd(&HF, &HF7)`  
7 adja vissza, mert ez az érték kiértékelése hexadecimális "F" és "F7".

- - -
### <a name="bitor"></a>BitOr
**Leírás:**  
A BitOr függvénynek megadott bits értéket állítja be.

**Szintaxis:**  
`num BitOr(num value1, num value2)`

* Érték1, érték2: numerikus értékeket, amelyeket együttesen kell-e köztük

**Megjegyzés:**  
Ez a függvény mindkét paraméter alakítja át a bináris kódolású karakterláncként, és beállítja egy kicsit Ha egyikét vagy mindkettőt a megfelelő bits a maszk és jelző 1 1-re, és 0-ra, ha mindkettő a megfelelő bits: 0. 1 más szóval, kivéve, ahol a megfelelő bitjeinek száma, mindkét paraméter 0 minden esetben visszaadja.

- - -
### <a name="cbool"></a>CBool
**Leírás:**  
A CBool függvény a kiértékelt kifejezés alapján logikai érték beolvasása.

**Szintaxis:**  
`bool CBool(exp Expression)`

**Megjegyzés:**  
Ha a kifejezés értéke nem nulla értéket, majd CBool igaz értéket ad vissza, ellenkező esetben azt HAMIS értéket ad vissza.

**Példa**  
`CBool([attrib1] = [attrib2])`  

Értéket ad vissza IGAZ, ha mindkét attribútumok ugyanazzal az értékkel rendelkezik.

- - -
### <a name="cdate"></a>CDate
**Leírás:**  
A CDate függvény egy UTC dátum/idő karakterláncot adja vissza. Dátum és idő nem szinkronizált natív attribútumtípust, de egyes funkciókat használják.

**Szintaxis:**  
`dt CDate(str value)`

* Érték: Egy karakterláncot egy dátum, idő és igény szerint időzóna

**Megjegyzés:**  
(UTC) mindig van a visszaadott karakterláncban.

**Példa**  
`CDate([employeeStartTime])`  
A kezdési idő értéket ad vissza egy dátum és idő alapján az alkalmazott

`CDate("2013-01-10 4:00 PM -8")`  
Adja vissza egy dátum és idő képviselő "2013-01-11 12:00-kor"


- - -
### <a name="certextensionoids"></a>CertExtensionOids
**Leírás:**  
A kritikus fontosságú Extensions tanúsítvány objektum Oid-értékeit adja eredményül.

**Szintaxis:**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData: Egy X.509 tanúsítvány bájt tömb ábrázolása. A bajtového Pole lehet (DER) kódolású bináris vagy Base64-kódolású X.509-adatokat.

- - -
### <a name="certformat"></a>CertFormat
**Leírás:**  
A X.509v3 tanúsítványt formátumát a nevét adja vissza.

**Szintaxis:**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData: Egy X.509 tanúsítvány bájt tömb ábrázolása. A bajtového Pole lehet (DER) kódolású bináris vagy Base64-kódolású X.509-adatokat.

- - -
### <a name="certfriendlyname"></a>CertFriendlyName
**Leírás:**  
A társított tanúsítvány-alias adja vissza.

**Szintaxis:**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData: Egy X.509 tanúsítvány bájt tömb ábrázolása. A bajtového Pole lehet (DER) kódolású bináris vagy Base64-kódolású X.509-adatokat.

- - -
### <a name="certhashstring"></a>CertHashString
**Leírás:**  
A hexadecimális karakterlánc a X.509v3 tanúsítványt SHA1 kivonatot értékét adja vissza.

**Szintaxis:**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData: Egy X.509 tanúsítvány bájt tömb ábrázolása. A bajtového Pole lehet (DER) kódolású bináris vagy Base64-kódolású X.509-adatokat.

- - -
### <a name="certissuer"></a>CertIssuer
**Leírás:**  
A X.509v3 tanúsítványt kiállító hitelesítésszolgáltató nevét adja vissza.

**Szintaxis:**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData: Egy X.509 tanúsítvány bájt tömb ábrázolása. A bajtového Pole lehet (DER) kódolású bináris vagy Base64-kódolású X.509-adatokat.

- - -
### <a name="certissuerdn"></a>CertIssuerDN
**Leírás:**  
A tanúsítvány kiállítójának megkülönböztető nevét adja vissza.

**Szintaxis:**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData: Egy X.509 tanúsítvány bájt tömb ábrázolása. A bajtového Pole lehet (DER) kódolású bináris vagy Base64-kódolású X.509-adatokat.

- - -
### <a name="certissueroid"></a>CertIssuerOid
**Leírás:**  
Az objektumazonosító, a tanúsítvány kiállítójának adja vissza.

**Szintaxis:**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData: Egy X.509 tanúsítvány bájt tömb ábrázolása. A bajtového Pole lehet (DER) kódolású bináris vagy Base64-kódolású X.509-adatokat.

- - -
### <a name="certkeyalgorithm"></a>CertKeyAlgorithm
**Leírás:**  
A X.509v3 tanúsítványt algoritmus információkat karakterláncként adja vissza.

**Szintaxis:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Egy X.509 tanúsítvány bájt tömb ábrázolása. A bajtového Pole lehet (DER) kódolású bináris vagy Base64-kódolású X.509-adatokat.

- - -
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**Leírás:**  
A X.509v3 tanúsítványt algoritmus paramétereinek Hexadecimális karakterláncként adja vissza.

**Szintaxis:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Egy X.509 tanúsítvány bájt tömb ábrázolása. A bajtového Pole lehet (DER) kódolású bináris vagy Base64-kódolású X.509-adatokat.

- - -
### <a name="certnameinfo"></a>CertNameInfo
**Leírás:**  
Adja vissza a tárgy és a kiállító nevét a tanúsítványból.

**Szintaxis:**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData: Egy X.509 tanúsítvány bájt tömb ábrázolása. A bajtového Pole lehet (DER) kódolású bináris vagy Base64-kódolású X.509-adatokat.
*   X509NameType: A tulajdonos X509NameType értékét.
*   includesIssuerName: kiállító neve; is igaz esetén pedig FALSE érték.

- - -
### <a name="certnotafter"></a>CertNotAfter
**Leírás:**  
Helyi idő, amelyet követően a tanúsítvány már nem érvényes a dátumot adja vissza.

**Szintaxis:**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData: Egy X.509 tanúsítvány bájt tömb ábrázolása. A bajtového Pole lehet (DER) kódolású bináris vagy Base64-kódolású X.509-adatokat.

- - -
### <a name="certnotbefore"></a>CertNotBefore
**Leírás:**  
Helyi idő, amelyen a tanúsítvány érvényessé válik a dátumot adja vissza.

**Szintaxis:**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData: Egy X.509 tanúsítvány bájt tömb ábrázolása. A bajtového Pole lehet (DER) kódolású bináris vagy Base64-kódolású X.509-adatokat.

- - -
### <a name="certpublickeyoid"></a>CertPublicKeyOid
**Leírás:**  
Az objektumazonosító a nyilvános kulcs a X.509v3 tanúsítványt adja vissza.

**Szintaxis:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Egy X.509 tanúsítvány bájt tömb ábrázolása. A bajtového Pole lehet (DER) kódolású bináris vagy Base64-kódolású X.509-adatokat.

- - -
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**Leírás:**  
Az objektumazonosító a nyilvános kulcs paramétereit a X.509v3 tanúsítványt adja vissza.

**Szintaxis:**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData: Egy X.509 tanúsítvány bájt tömb ábrázolása. A bajtového Pole lehet (DER) kódolású bináris vagy Base64-kódolású X.509-adatokat.

- - -
### <a name="certserialnumber"></a>CertSerialNumber
**Leírás:**  
A X.509v3 tanúsítvány sorozatszámát adja eredményül.

**Szintaxis:**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData: Egy X.509 tanúsítvány bájt tömb ábrázolása. A bajtového Pole lehet (DER) kódolású bináris vagy Base64-kódolású X.509-adatokat.

- - -
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**Leírás:**  
Az objektumazonosító, a tanúsítvány aláírásának létrehozására használt algoritmus adja vissza.

**Szintaxis:**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData: Egy X.509 tanúsítvány bájt tömb ábrázolása. A bajtového Pole lehet (DER) kódolású bináris vagy Base64-kódolású X.509-adatokat.

- - -
### <a name="certsubject"></a>CertSubject
**Leírás:**  
Egy tanúsítványt a tulajdonos megkülönböztető nevének beolvasása.

**Szintaxis:**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData: Egy X.509 tanúsítvány bájt tömb ábrázolása. A bajtového Pole lehet (DER) kódolású bináris vagy Base64-kódolású X.509-adatokat.

- - -
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**Leírás:**  
Egy tanúsítványt a tulajdonos megkülönböztető nevét adja vissza.

**Szintaxis:**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData: Egy X.509 tanúsítvány bájt tömb ábrázolása. A bajtového Pole lehet (DER) kódolású bináris vagy Base64-kódolású X.509-adatokat.

- - -
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**Leírás:**  
Egy tanúsítványt az objektumazonosító, a tulajdonos nevét adja vissza.

**Szintaxis:**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData: Egy X.509 tanúsítvány bájt tömb ábrázolása. A bajtového Pole lehet (DER) kódolású bináris vagy Base64-kódolású X.509-adatokat.

- - -
### <a name="certthumbprint"></a>certThumbprint
**Leírás:**  
A tanúsítvány ujjlenyomatát adja vissza.

**Szintaxis:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Egy X.509 tanúsítvány bájt tömb ábrázolása. A bajtového Pole lehet (DER) kódolású bináris vagy Base64-kódolású X.509-adatokat.

- - -
### <a name="certversion"></a>CertVersion
**Leírás:**  
A tanúsítvány X.509 formátumú verziója adja vissza.

**Szintaxis:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Egy X.509 tanúsítvány bájt tömb ábrázolása. A bajtového Pole lehet (DER) kódolású bináris vagy Base64-kódolású X.509-adatokat.

- - -
### <a name="cguid"></a>CGuid
**Leírás:**  
A CGuid függvény karakteres megjelenítésének egy GUID Azonosítót a bináris ábrázolásra alakítja át.

**Szintaxis:**  
`bin CGuid(str GUID)`

* Egy karakterlánc formátumú ebben a mintában: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx, vagy {: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

- - -
### <a name="contains"></a>Contains
**Leírás:**  
A Contains függvény megkeresi egy karakterláncot egy többértékű belüli

**Szintaxis:**  
`num Contains (mvstring attribute, str search)` -és nagybetűket  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)` -és nagybetűket

* attribútum: a többértékű attribútum keresésére.
* Keresés: az attribútum a keresendő karakterlánc.
* Casetype: CaseInsensitive vagy CaseSensitive.

Ahol a karakterlánc található a többértékű attribútumban index értéket ad vissza. 0 ad vissza, ha a karakterlánc nem található.

**Megjegyzés:**  
Többértékű karakterlánc-attribútumok a keresés karakterláncrészleteket megkeresi az értékek.  
Hivatkozási attribútumok esetén a keresett karakterlánc pontosan meg kell egyeznie az érték egyezést kell figyelembe venni.

**Példa**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Ha a proxyAddresses attribútum elsődleges e-mail-címmel rendelkezik (nagybetűs által jelzett "SMTP:"), majd vissza a proxyAddress attribútuma, ellenkező esetben a hibát adhat vissza.

- - -
### <a name="convertfrombase64"></a>ConvertFromBase64
**Leírás:**  
A ConvertFromBase64 függvény a megadott base64-kódolású érték konvertál egy rendszeres karakterláncot.

**Szintaxis:**  
`str ConvertFromBase64(str source)` -feltételezi, hogy a Unicode Encoding  
`str ConvertFromBase64(str source, enum Encoding)`

* Forrás: Base64-kódolású karakterlánc  
* Kódolás: Unicode, ASCII, UTF8

**Példa**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Mindkét példa adja vissza "*Hello world!*"

- - -
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex
**Leírás:**  
A ConvertFromUTF8Hex függvény egy karakterlánc UTF8 hexadecimális kódolású értékké konvertálja.

**Szintaxis:**  
`str ConvertFromUTF8Hex(str source)`

* Forrás: UTF8 2 bájtos kódolt karakterlánc

**Megjegyzés:**  
Ez a függvény és ConvertFromBase64([],UTF8) az, hogy a DN attribútum felhasználóbarát-e az eredmény közötti különbség.  
Ezt a formátumot az Azure Active Directory DN-t használja.

**Példa**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Adja vissza "*Hello world!*"

- - -
### <a name="converttobase64"></a>ConvertToBase64
**Leírás:**  
A ConvertToBase64 függvény egy karakterláncot Unicode Base64 kódolású karakterláncnak alakítja.  
Konvertálja az értéket egy egész számot tartalmazó tömb a megfelelő karakterlánc-ábrázolásra számjegynél base-64 kódolású.

**Szintaxis:**  
`str ConvertToBase64(str source)`

**Példa**  
`ConvertToBase64("Hello world!")`  
Returns "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

- - -
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Leírás:**  
A ConvertToUTF8Hex függvény egy karakterlánc UTF8 hexadecimális kódolású értékké alakítja.

**Szintaxis:**  
`str ConvertToUTF8Hex(str source)`

**Megjegyzés:**  
A kimeneti formátum, a függvény DN attribútumformátum lesz az Azure Active Directoryban.

**Példa**  
`ConvertToUTF8Hex("Hello world!")`  
48656C6C6F20776F726C6421 értéket ad vissza

- - -
### <a name="count"></a>Darabszám
**Leírás:**  
A Count függvénnyel az elemek számát adja vissza egy többértékű attribútum

**Szintaxis:**  
`num Count(mvstr attribute)`

- - -
### <a name="cnum"></a>CNum
**Leírás:**  
A CNum függvény egy karakterláncot vesz fel, és a egy numerikus adattípusú adja vissza.

**Szintaxis:**  
`num CNum(str value)`

- - -
### <a name="cref"></a>CRef
**Leírás:**  
Egy karakterláncot alakít egy referencia-attribútum

**Szintaxis:**  
`ref CRef(str value)`

**Példa**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

- - -
### <a name="cstr"></a>CStr
**Leírás:**  
A CStr függvény egy karakterlánc adattípus alakítja.

**Szintaxis:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* value: Egy számértéket, hivatkozási attribútum vagy logikai érték lehet.

**Példa**  
`CStr([dn])`  
Could return "cn=Joe,dc=contoso,dc=com"

- - -
### <a name="dateadd"></a>DateAdd
**Leírás:**  
Egy dátumot tartalmazó, amelyhez hozzá lett adva egy adott időtartam dátumot adja vissza.

**Szintaxis:**  
`dt DateAdd(str interval, num value, dt date)`

* időköz: A karakterlánc-kifejezés, amely az alapidőponthoz hozzáadandó. A karakterlánc a következő értékek egyikét kell rendelkeznie:
  * ÉÉÉÉ év
  * q negyedév
  * m hónap
  * év napja y
  * d nap
  * w hét napja
  * ww hét
  * h óra
  * n percben
  * s második
* value: A hozzáadandó egységek száma. (Az első jövőbeli dátumokat) pozitív vagy negatív, (az elmúlt dátumok megszerezni) lehet.
* dátum: Dátum és idő jelölő dátum, amelyhez az időközt ad hozzá.

**Példa**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
3 hónap hozzáadja, és adja vissza egy dátum és idő "2001-04-01" jelző.

- - -
### <a name="datefromnum"></a>DateFromNum
**Leírás:**  
A DateFromNum függvény konvertál egy értéket AD meg dátum formázása dátum és idő típusra.

**Szintaxis:**  
`dt DateFromNum(num value)`

**Példa**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Adja vissza egy dátum és idő, 2012-01-01 jelölő 23:00:00

- - -
### <a name="dncomponent"></a>DNComponent
**Leírás:**  
A DNComponent függvény bal azzal adott DN összetevő értékét adja vissza.

**Szintaxis:**  
`str DNComponent(ref dn, num ComponentNumber)`

* DN: a hivatkozási attribútum értelmezése
* ComponentNumber: A visszaadandó megkülönböztető összetevő

**Példa**  
`DNComponent(CRef([dn]),1)`  
Ha a dn "cn Joe, ou = =...," János adja vissza

- - -
### <a name="dncomponentrev"></a>DNComponentRev
**Leírás:**  
A DNComponentRev függvény jobb (Befejezés) lehetőséget adott DN összetevő értékét adja vissza.

**Szintaxis:**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* DN: a hivatkozási attribútum értelmezése
* ComponentNumber – a visszaadandó megkülönböztető összetevő
* Beállítások: Tartományvezérlő – hagyja figyelmen kívül az összes összetevő "dc ="

**Példa**  
Ha a dn "cn Joe, ou = Atlanta, ou = általánosan elérhető, ou = = USA, dc = contoso, dc = com" majd  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
Mindkét vissza velünk a KAPCSOLATOT.

- - -
### <a name="error"></a>Hiba
**Leírás:**  
A hiba függvény segítségével egyéni hibaüzenetet ad vissza.

**Szintaxis:**  
`void Error(str ErrorMessage)`

**Példa**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Ha az attribútum accountName nem található, throw hiba az objektum.

- - -
### <a name="escapedncomponent"></a>EscapeDNComponent
**Leírás:**  
A EscapeDNComponent függvény vesz igénybe a DN egy összetevő, és így ismétlésével finomítja az LDAP-kiszolgálón lehet kilépni.

**Szintaxis:**  
`str EscapeDNComponent(str value)`

**Példa**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Ellenőrzi, hogy az objektum egy LDAP-címtár is létrehozható, akkor is, ha a displayName attribútum karaktereket escape-karakterrel az LDAP-kiszolgálón.

- - -
### <a name="formatdatetime"></a>formatDateTime
**Leírás:**  
A FormatDateTime függvény segítségével egy karakterláncot egy dátum és idő formázása megadott formátumban

**Szintaxis:**  
`str FormatDateTime(dt value, str format)`

* érték: a dátum és idő formátumú érték
* formátum: a formátum konvertálása képviselő karakterláncot.

**Megjegyzés:**  
A lehetséges értékek a formátum itt található: [Egyéni dátum és idő a FORMAT függvény formátumok](https://docs.microsoft.com/dax/custom-date-and-time-formats-for-the-format-function).

**Példa**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
"2007-12-25" eredményez.

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
"20140905081453.0Z" eredményezhet

- - -
### <a name="guid"></a>Guid
**Leírás:**  
A függvény Guid hoz létre egy új, véletlenszerű GUID azonosítója

**Szintaxis:**  
`str Guid()`

- - -
### <a name="iif"></a>IIF
**Leírás:**  
A IIF függvény a megadott feltétel alapján lehetséges értékek egyikét adja vissza.

**Szintaxis:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* feltétel: bármilyen érték vagy kifejezés, amelynek kiértékelése igaz vagy hamis.
* valueIfTrue: Ha a feltétel igaz, a visszaadott érték.
* valueIfFalse: Ha a feltétel hamis, a visszaadott érték.

**Példa**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Ha a felhasználó egy belső munkatársa, adja vissza, aki "t-", más elejére hozzáadva az aliast, a felhasználói alias adja vissza.

- - -
### <a name="instr"></a>InStr
**Leírás:**  
Az InStr függvény megkeresi egy karakterláncrészletet a első előfordulása karakterlánc

**Szintaxis:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* stringcheck: karakterlánc keresése
* stringmatch: keresett karakterlánc
* Indítsa el: a karakterláncrész keresése kezdőpozíciója
* Hasonlítsa össze: vbTextCompare vagy vbBinaryCompare

**Megjegyzés:**  
Ha a substring található, vagy 0, ha nincs találat kezdőpozícióját adja vissza.

**Példa**  
`InStr("The quick brown fox","quick")`  
5 Evalues

`InStr("repEated","e",3,vbBinaryCompare)`  
7 kiértékelésének eredménye

- - -
### <a name="instrrev"></a>InStrRev
**Leírás:**  
A InStrRev függvény egy karakterlánc részkarakterláncot utolsó előfordulásának megkeresi

**Szintaxis:**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: karakterlánc keresése
* stringmatch: keresett karakterlánc
* Indítsa el: a karakterláncrész keresése kezdőpozíciója
* Hasonlítsa össze: vbTextCompare vagy vbBinaryCompare

**Megjegyzés:**  
Ha a substring található, vagy 0, ha nincs találat kezdőpozícióját adja vissza.

**Példa**  
`InStrRev("abbcdbbbef","bb")`  
7 beolvasása

- - -
### <a name="isbitset"></a>IsBitSet
**Leírás:**  
A függvény IsBitSet teszteket, ha egy belemehetünk van beállítva, vagy sem

**Szintaxis:**  
`bool IsBitSet(num value, num flag)`

* érték: egy numerikus értéket, amely evaluated.flag: egy numerikus érték, amely rendelkezik a bit, ki kell értékelni

**Példa**  
`IsBitSet(&HF,4)`  
Igaz értéket ad vissza, mert a bit "4" be van állítva a "F" hexadecimális érték

- - -
### <a name="isdate"></a>IsDate
**Leírás:**  
Ha a kifejezés lehet majd a IsDate függvény eredménye IGAZ kiértékeli dátum/idő típusúként.

**Szintaxis:**  
`bool IsDate(var Expression)`

**Megjegyzés:**  
Azt határozza meg, ha CDate() sikeres lehet.

- - -
### <a name="iscert"></a>IsCert
**Leírás:**  
Igaz értéket ad eredményül, ha a nyers adatok .NET X509Certificate2 tanúsítvány objektum lehet szerializálni.

**Szintaxis:**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Egy X.509 tanúsítvány bájt tömb ábrázolása. A bajtového Pole lehet (DER) kódolású bináris vagy Base64-kódolású X.509-adatokat.
- - -
### <a name="isempty"></a>IsEmpty
**Leírás:**  
Ha az attribútum a CS vagy MV szerepel, de üres karakterláncot ad vissza, majd az IsEmpty függvény eredménye igaz.

**Szintaxis:**  
`bool IsEmpty(var Expression)`

- - -
### <a name="isguid"></a>IsGuid
**Leírás:**  
Ha sikerült konvertálni a karakterlánc egy GUID Azonosítót, majd a IsGuid függvény igaz értékeli ki.

**Szintaxis:**  
`bool IsGuid(str GUID)`

**Megjegyzés:**  
Egy GUID Azonosítót a következő ezek a minták egyikét karakterláncként van definiálva: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx, vagy {: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Azt határozza meg, ha CGuid() sikeres lehet.

**Példa**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Ha a StrAttribute egy GUID formátumú, bináris kódolású karakterláncként ad vissza, ellenkező esetben a Null visszaadása.

- - -
### <a name="isnull"></a>IsNull
**Leírás:**  
Ha a kifejezés eredménye Null értékre, majd a IsNull függvény igaz értéket ad vissza.

**Szintaxis:**  
`bool IsNull(var Expression)`

**Megjegyzés:**  
Az attribútum által érhető el az attribútum a NULL értékű fejezzük ki.

**Példa**  
`IsNull([displayName])`  
Igaz értéket ad vissza, ha az attribútum nem található a CS vagy MV.

- - -
### <a name="isnullorempty"></a>IsNullOrEmpty
**Leírás:**  
Ha a kifejezés null értékű vagy üres karakterláncot, majd a IsNullOrEmpty függvény igaz értéket ad vissza.

**Szintaxis:**  
`bool IsNullOrEmpty(var Expression)`

**Megjegyzés:**  
Az attribútum esetében ez értékelné ki, TRUE, ha az attribútum hiányzik, vagy megtalálható, de üres karakterlánc.  
Ez a függvény inverzét IsPresent neve.

**Példa**  
`IsNullOrEmpty([displayName])`  
Igaz értéket ad vissza, ha az attribútum nem szerepel, vagy a CS vagy MV üres karakterlánc.

- - -
### <a name="isnumeric"></a>IsNumeric
**Leírás:**  
Az IsNumeric függvény jelzi, hogy egy kifejezés kiértékelhető-e egy szám típusú logikai értéket adja vissza.

**Szintaxis:**  
`bool IsNumeric(var Expression)`

**Megjegyzés:**  
Azt határozza meg, ha CNum() kifejezés elemzése sikeres lehet.

- - -
### <a name="isstring"></a>IsString
**Leírás:**  
Ha egy karakterlánc típusú kifejezés kiértékelése, majd a IsString függvény eredménye igaz.

**Szintaxis:**  
`bool IsString(var expression)`

**Megjegyzés:**  
Azt határozza meg, ha CStr() kifejezés elemzése sikeres lehet.

- - -
### <a name="ispresent"></a>IsPresent
**Leírás:**  
Ha a kifejezés, amely nem Null, és nem üres karakterláncra értékelődik ki, majd a IsPresent függvény igaz értéket ad vissza.

**Szintaxis:**  
`bool IsPresent(var expression)`

**Megjegyzés:**  
Ez a függvény inverzét IsNullOrEmpty neve.

**Példa**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

- - -
### <a name="item"></a>Elem
**Leírás:**  
Az Item függvény egy elemet egy többértékű karakterláncot attribútumot adja vissza.

**Szintaxis:**  
`var Item(mvstr attribute, num index)`

* attribútum: többértékű attribútum
* index: index a többértékű karakterlánc viselkednek.

**Megjegyzés:**  
Az Item függvény akkor hasznos, a Contains függvény együtt, mivel ez utóbbi függvény az index a többértékű attribútum viselkednek.

Hibát jelez, ha az index je mimo rozsah.

**Példa**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Elsődleges e-mail címét adja vissza.

- - -
### <a name="itemornull"></a>ItemOrNull
**Leírás:**  
A ItemOrNull függvény egy elemet egy többértékű karakterláncot attribútumot adja vissza.

**Szintaxis:**  
`var ItemOrNull(mvstr attribute, num index)`

* attribútum: többértékű attribútum
* index: index a többértékű karakterlánc viselkednek.

**Megjegyzés:**  
A ItemOrNull függvény akkor hasznos, a Contains függvény együtt, mivel ez utóbbi függvény az index a többértékű attribútum viselkednek.

Ha az index je mimo rozsah, majd adja vissza Null értéket.

- - -
### <a name="join"></a>Csatlakozás
**Leírás:**  
Illesztési függvényhez egy többértékű karakterláncot vesz fel, és a egy egyértékű karakterláncot ad vissza, az egyes elemek között megadott elválasztó.

**Szintaxis:**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* attribútum: Többértékű attribútumot tartalmazó karakterláncokat kell összefűzni.
* elválasztó karakter: A visszaadott karakterláncban a karakterláncrész különálló használt bármilyen karakterlánc. Ha nincs megadva, a szóköz karaktert ("") használja. Ha elválasztó egy nulla hosszúságú karakterlánc (""), illetve semmi sem, a listában szereplő összes rendszer összefűzéséhez nincs elválasztó.

**Megjegyzések**  
A csatlakozás és a Split függvény között paritásos van. Az illesztési függvényhez egy diagnosztikakonfigurációs tömböt foglal karakterláncok, és csatlakoztatja azokat egyetlen karakterláncot egy elválasztó sztring használatával. A Split függvény egy karakterláncot vesz fel, és elválasztja a: a kivonni kívánt a karakterláncok tömbjét adja vissza. A fő különbség azonban nem, hogy a Join is az összefűzés bármely elválasztó karakterlánccal, a felosztás csak elkülönítheti a karakterláncok egy egyetlen karaktert elválasztó segítségével.

**Példa**  
`Join([proxyAddresses],",")`  
Vissza: "SMTP:john.doe@contoso.com,smtp:jd@contoso.com"

- - -
### <a name="lcase"></a>LCase
**Leírás:**  
A LCase függvény egy karakterlánc karaktereinek összes kisbetű konvertálja.

**Szintaxis:**  
`str LCase(str value)`

**Példa**  
`LCase("TeSt")`  
"Test" adja vissza.

- - -
### <a name="left"></a>Balra
**Leírás:**  
A bal oldali függvény egy karakterlánc bal megadott számú karaktert adja vissza.

**Szintaxis:**  
`str Left(str string, num NumChars)`

* karakterlánc: a karakterláncot ad vissza a következőből származó karaktereket
* NumChars: egy azonosító szám karakterek visszaadásához a karakterlánc elején (balra)

**Megjegyzés:**  
A karakterlánc első numChars karaktereket tartalmazó karakterláncot:

* Ha numChars = 0, üres karakterláncot ad vissza.
* Ha numChars < 0, a bemeneti karakterláncot ad vissza.
* Ha karakterlánc értéke null, lépjen vissza az üres karakterlánc.

Ha a karakterlánc a szám a megadott numChars-nál kevesebb karaktert tartalmaz, egy karakterláncot (1. paraméterben levő összes karakter van, tartalmazó) azonos karakterláncot adja vissza.

**Példa**  
`Left("John Doe", 3)`  
"Joh" adja vissza.

- - -
### <a name="len"></a>Len
**Leírás:**  
A Len függvény egy karakterláncban szereplő karakterek számát adja vissza.

**Szintaxis:**  
`num Len(str value)`

**Példa**  
`Len("John Doe")`  
8 beolvasása

- - -
### <a name="ltrim"></a>LTrim
**Leírás:**  
LTrim függvény kezdő szóközt eltávolít egy karakterláncból.

**Szintaxis:**  
`str LTrim(str value)`

**Példa**  
`LTrim(" Test ")`  
"Test" értéket ad vissza

- - -
### <a name="mid"></a>Mid
**Leírás:**  
Mid függvény egy megadott pozíció a karakterlánc megadott számú karaktert adja vissza.

**Szintaxis:**  
`str Mid(str string, num start, num NumChars)`

* karakterlánc: a karakterláncot ad vissza a következőből származó karaktereket
* Indítsa el: egy szám a kezdő pozíció karakterláncot ad vissza a következőből származó karaktereket a
* NumChars: egy azonosító szám karakterek pozíció a karakterlánc visszaadása

**Megjegyzés:**  
Indítsa el a visszatérési numChars karakter pozíciótól kezdve karakterlánc.  
Pozíció kezdő karakterlánc numChars karaktert tartalmazó karakterlánc:

* Ha numChars = 0, üres karakterláncot ad vissza.
* Ha numChars < 0, a bemeneti karakterláncot ad vissza.
* Ha a start >, sztring hosszát adja vissza a bemeneti karakterlánc.
* Ha a start < = 0, a bemeneti karakterláncot ad vissza.
* Ha karakterlánc értéke null, lépjen vissza az üres karakterlánc.

Ha ott nem numChar kezdő pozíció, a karakterlánc fennmaradó karakterek száma, a lehető legtöbb karaktert adja vissza.

**Példa**  
`Mid("John Doe", 3, 5)`  
"Hn Do" adja vissza.

`Mid("John Doe", 6, 999)`  
Visszaadja a "Jakab"

- - -
### <a name="now"></a>Most
**Leírás:**  
A funkcióval adja vissza egy dátum és idő megadása az aktuális dátum és idő, a számítógép rendszer dátum és idő alapján.

**Szintaxis:**  
`dt Now()`

- - -
### <a name="numfromdate"></a>NumFromDate
**Leírás:**  
A NumFromDate függvény dátumot AD dátumformátum adja vissza.

**Szintaxis:**  
`num NumFromDate(dt value)`

**Példa**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
129699324000000000 beolvasása

- - -
### <a name="padleft"></a>PadLeft
**Leírás:**  
A PadLeft függvény bal-dolgozniuk egy karakterláncot egy megadott Kitöltő karakter használatával megadott hossza.

**Szintaxis:**  
`str PadLeft(str string, num length, str padCharacter)`

* karakterlánc: kitölti a karakterláncot.
* hossz: Egy egész számot jelölő karakterlánc kívánt hosszát.
* padCharacter: A kitöltő karakterként használata egy karaktert tartalmazó karakterlánc

**Megjegyzés:**

* Ha a karakterlánc hossza kisebb, mint a hossza, majd padCharacter ismételten hozzáfűzi (balra) karakterlánc hossza, amíg hossza egyenlő kezdete.
* PadCharacter szóköz karakter lehet, de nem lehet null értékű.
* Ha a karakterlánc hossza hossza nagyobb vagy azzal egyenlőnek, karakterlánc visszaadott változatlan marad.
* Ha a karakterlánc hossza nagyobb vagy egyenlő, hosszra, azonos karakterláncot karakterláncként adja vissza.
* Ha a karakterlánc hossza kisebb, mint a hossza, majd egy új karakterlánc a kívánt hosszát adja vissza egy padCharacter számokhoz tartalmazó karakterlánc.
* Karakterlánc értéke null, ha a függvény egy üres karakterláncot ad vissza.

**Példa**  
`PadLeft("User", 10, "0")`  
"000000User" adja vissza.

- - -
### <a name="padright"></a>PadRight
**Leírás:**  
A PadRight függvény jobb-dolgozniuk egy karakterláncot egy megadott Kitöltő karakter használatával megadott hossza.

**Szintaxis:**  
`str PadRight(str string, num length, str padCharacter)`

* karakterlánc: kitölti a karakterláncot.
* hossz: Egy egész számot jelölő karakterlánc kívánt hosszát.
* padCharacter: A kitöltő karakterként használata egy karaktert tartalmazó karakterlánc

**Megjegyzés:**

* Ha a karakterlánc hossza kisebb, mint a hossza, majd padCharacter ismételten hozzáfűzi végén (jobb oldali) karakterlánc hossza egyenlő hossza, amíg.
* PadCharacter szóköz karakter lehet, de nem lehet null értékű.
* Ha a karakterlánc hossza hossza nagyobb vagy azzal egyenlőnek, karakterlánc visszaadott változatlan marad.
* Ha a karakterlánc hossza nagyobb vagy egyenlő, hosszra, azonos karakterláncot karakterláncként adja vissza.
* Ha a karakterlánc hossza kisebb, mint a hossza, majd egy új karakterlánc a kívánt hosszát adja vissza egy padCharacter számokhoz tartalmazó karakterlánc.
* Karakterlánc értéke null, ha a függvény egy üres karakterláncot ad vissza.

**Példa**  
`PadRight("User", 10, "0")`  
"User000000" adja vissza.

- - -
### <a name="pcase"></a>PCase
**Leírás:**  
A PCase függvény szóközzel elválasztott szókezdő karakterlánc első karaktere átalakítja a nagybetűs, és minden egyéb, karaktereket kisbetűsre konvertálja.

**Szintaxis:**  
`String PCase(string)`

**Megjegyzés:**

* Ez a funkció jelenleg nem biztosít a megfelelő kis-és átalakítani egy szót teljes egészében nagybetűt, például az angol.

**Példa**  
`PCase("TEsT")`  
"Test" adja vissza.

`PCase(LCase("TEST"))`  
"Test" értéket ad vissza

- - -
### <a name="randomnum"></a>RandomNum
**Leírás:**  
A RandomNum függvény egy véletlenszerű számot ad vissza egy megadott időszakkal között.

**Szintaxis:**  
`num RandomNum(num start, num end)`

* Indítsa el: létrehozni a véletlenszerű értéket alacsonyabb korlátot azonosító szám
* végfelhasználók: létrehozni a véletlenszerű értéket felső határát azonosító szám

**Példa**  
`Random(100,999)`  
734 adhat vissza.

- - -
### <a name="removeduplicates"></a>RemoveDuplicates
**Leírás:**  
A RemoveDuplicates függvény egy többértékű karakterláncot vesz fel, és ellenőrizze, hogy egyedi.

**Szintaxis:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Példa**  
`RemoveDuplicates([proxyAddresses])`  
Adja vissza a megtisztított proxyAddress attribútuma, ahol az összes ismétlődő értékek el lettek távolítva.

- - -
### <a name="replace"></a>Csere
**Leírás:**  
A csere függvény egy karakterlánc egy másik karakterláncra összes előfordulását lecseréli.

**Szintaxis:**  
`str Replace(str string, str OldValue, str NewValue)`

* Karakterlánc: Egy karakterláncot cserélje le az értékeket.
* OldValue: A karakterlánc keresése, és cserélje le.
* Új érték: A cserélendő karakterláncot.

**Megjegyzés:**  
A függvény felismeri a következő különleges monikerek:

* \n – új sor
* \r – kocsivissza
* \t – Tab

**Példa**  
`Replace([address],"\r\n",", ")`  
Lecseréli az CRLF egy vesszőt és egy szóközt, és a "Egy Microsoft módja, Redmond, WA, USA" vezethet

- - -
### <a name="replacechars"></a>ReplaceChars
**Leírás:**  
A ReplaceChars függvény ReplacePattern karakterláncban található karakterek összes előfordulását lecseréli.

**Szintaxis:**  
`str ReplaceChars(str string, str ReplacePattern)`

* Karakterlánc: Egy karakterlánc karaktereinek helyett.
* ReplacePattern: cserélendő karakterek egy szótár tartalmazó karakterlánc.

{Source1} formátuma: {target1}, {source2}: {target2}, {sourceN}, {targetN} ahol forrása a karakter található, és célozhat meg a cserélendő karakterláncot.

**Megjegyzés:**

* A függvény minden egyes előfordulásakor meghatározott források vesz igénybe, és lecseréli azokat a célokat.
* A forrás (unicode) pontosan egy karakterből kell lennie.
* A forrás nem lehet üres vagy hosszabb, mint egy karaktert (elemzési hiba).
* A cél több karakter, például ö:oe, β:ss lehet.
* A cél lehet üres, amely azt jelzi, hogy az a karakter el kell távolítani.
* A forrás kis-és nagybetűket, és pontosan egyeznie kell.
* A, (vessző) és a: (kettőspont) fenntartott karakterek, és használja ezt a funkciót nem lehet lecserélni.
* Tárolóhelyek és egyéb fehér karakterek ReplacePattern karakterláncot a rendszer figyelmen kívül hagyja.

**Példa**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Raksmorgas adja vissza

`ReplaceChars("O’Neil",%ReplaceString%)`  
Adja vissza "ONeil", az egyetlen osztásjelek van definiálva, el kell távolítani.

- - -
### <a name="right"></a>Jobbra
**Leírás:**  
A Right függvény egy karakterlánc jobb (záró) a megadott számú karaktert adja vissza.

**Szintaxis:**  
`str Right(str string, num NumChars)`

* karakterlánc: a karakterláncot ad vissza a következőből származó karaktereket
* NumChars: egy azonosító szám karakterek végén (jobb oldali) karakterlánc visszaadása

**Megjegyzés:**  
NumChars karaktereket a rendszer karakterlánc utolsó pozícióját adja vissza.

Egy karakterlánc utolsó numChars karaktereket tartalmazó karakterlánc:

* Ha numChars = 0, üres karakterláncot ad vissza.
* Ha numChars < 0, a bemeneti karakterláncot ad vissza.
* Ha karakterlánc értéke null, lépjen vissza az üres karakterlánc.

Ha a karakterlánc a szám a megadott NumChars-nál kevesebb karaktert tartalmaz, egy karakterlánc, amely azonos karakterláncra adja vissza.

**Példa**  
`Right("John Doe", 3)`  
"János" adja vissza.

- - -
### <a name="rtrim"></a>RTrim
**Leírás:**  
RTrim függvény záró szóközök eltávolítása egy karakterláncot.

**Szintaxis:**  
`str RTrim(str value)`

**Példa**  
`RTrim(" Test ")`  
"Test" adja vissza.

- - -
### <a name="select"></a>Válassza ezt:
**Leírás:**  
A folyamat a megadott függvény alapján minden értékeket a többértékű attribútum (vagy egy kifejezés kimenetét).

**Szintaxis:**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* elem: A többértékű attribútum egy elemének felel meg
* attribútum: a többértékű attribútum
* kifejezés: egy kifejezés, amely értékek gyűjteményét adja vissza
* feltétel: az attribútum az elem feldolgozására képes függvényeket

**Példák:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
Összes értékének visszaadása a többértékű attribútum otherPhone után kötőjeleket (-) el lett távolítva.

- - -
### <a name="split"></a>Megosztott
**Leírás:**  
A Split függvény egy karakterláncot egy elválasztó elválasztva vesz fel, és lehetővé teszi egy többértékű karakterlánc.

**Szintaxis:**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* érték: elválasztásához szövegelválasztó karaktert a karakterlánc.
* elválasztó karakter: egyetlen, az elválasztó karakter használható.
* korlátot: maximális száma értékeket adhat vissza.

**Példa**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
2 elem többértékű karakterláncot ad vissza az proxyAddress attribútuma számára hasznos.

- - -
### <a name="stringfromguid"></a>StringFromGuid
**Leírás:**  
A StringFromGuid függvény vesz igénybe egy bináris GUID Azonosítót, és konvertálja karakterlánc

**Szintaxis:**  
`str StringFromGuid(bin GUID)`

- - -
### <a name="stringfromsid"></a>StringFromSid
**Leírás:**  
A StringFromSid függvény egy karakterlánc biztonsági azonosítót tartalmazó bájttömb konvertálja.

**Szintaxis:**  
`str StringFromSid(bin ObjectSID)`  

- - -
### <a name="switch"></a>Kapcsoló
**Leírás:**  
A kapcsoló függvény segítségével értékelt feltételek alapján egyetlen értéket ad vissza.

**Szintaxis:**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* kifejezés: Variant kifejezés szeretne értékelni.
* value: A visszaadandó megfelelő kifejezés igaz-e értéket.

**Megjegyzés:**  
A kapcsoló függvény argumentumlista kifejezések és érték párokat tartalmaz. A kifejezések kiértékelése balról jobbra, és a társított az első kifejezés kiértékelése igaz értéket ad vissza. Ha nincsenek megfelelően párosítva a részeit, a futásidejű hiba történik.

Ha például Kif1 értéke igaz, ha a kapcsoló érték1 adja vissza. Ha a kifejezés-1 értéke HAMIS, de kifejezés-2 értéke igaz, kapcsoló érték-2, és így tovább adja vissza.

Kapcsoló egy nem ad vissza. Ha:

* A kifejezések egyike sem igaz.
* Az első igaz kifejezés rendelkezik egy megfelelő értéket, amely má hodnotu Null.

Kapcsoló kiértékeli az összes kifejezés, annak ellenére, hogy csak az egyiket adja vissza. Ezért érdemes figyelemmel a nem kívánt mellékhatásokkal. Például ha egy osztás hiba: a kifejezés kiértékelésének eredménye bármely eredményez, hiba történik.

Érték is lehet a hiba-függvény, amely egy egyéni karakterláncot adna vissza.

**Példa**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Néhány fő városok beszélt nyelvet adja vissza, egyéb esetben hibát ad vissza.

- - -
### <a name="trim"></a>Levágás
**Leírás:**  
A Trim függvény eltávolítja a kezdő és záró szóközök egy karakterláncból.

**Szintaxis:**  
`str Trim(str value)`  

**Példa**  
`Trim(" Test ")`  
"Test" adja vissza.

`Trim([proxyAddresses])`  
Eltávolítja a kezdő és záró szóközök az proxyAddress attribútuma szereplő összes értékhez.

- - -
### <a name="ucase"></a>UCase
**Leírás:**  
A UCase függvény egy karakterlánc karaktereinek összes nagybetűs konvertálja.

**Szintaxis:**  
`str UCase(str string)`

**Példa**  
`UCase("TeSt")`  
Returns "TEST".

- - -
### <a name="where"></a>Ahol

**Leírás:**  
Meghatározott feltétel alapján többértékű attribútum (vagy egy kifejezés kimenetét) értékeket egy részhalmazát adja vissza.

**Szintaxis:**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* elem: A többértékű attribútum egy elemének felel meg
* attribútum: a többértékű attribútum
* feltétel: bármely kifejezés kiértékelése igaz vagy hamis
* kifejezés: egy kifejezés, amely értékek gyűjteményét adja vissza

**Példa**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
A tanúsítvány értékek visszaadása a többértékű attribútum userCertificate, amely nem járt le.

- - -
### <a name="with"></a>A következővel:
**Leírás:**  
A With funkciót biztosít arra, hogy egyszerűbben összetett kifejezést egy változó használatával, amelyek egy alkifejezés, amely akkor jelenik meg egy vagy több alkalommal a összetett kifejezésben.

**Szintaxis:**
`With(var variable, exp subExpression, exp complexExpression)`  
* változó: A alkifejezés jelöli.
* alkifejezés: alkifejezés változót jelöli.
* complexExpression: Összetett kifejezést.

**Példa**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
Funkcionalitását tekintve megegyezik a következő:  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
A userCertificate attribútum csak a lejárt tanúsítvány értékeket ad vissza.


- - -
### <a name="word"></a>Word
**Leírás:**  
A Word függvény egy karakterláncot, használata és a word számot adja vissza a kihagyni kívánt határolók leíró paraméterei alapján található egy szót adja vissza.

**Szintaxis:**  
`str Word(str string, num WordNumber, str delimiters)`

* karakterlánc: a karakterláncot ad vissza egy szót.
* WordNumber: egy szám, mely a word azonosítószám kell visszaadnia.
* elválasztó karakterek: egy karakterlánc, amely a szavakat azonosításához használt delimiter(s)

**Megjegyzés:**  
Mindegyik sztring karakter választja el az elválasztó karakterek az egyik karakterlánc szavak eszközként van azonosítva:

* Ha a < 1-es számú, értéket ad vissza üres karakterlánc.
* Ha a karakterlánc értéke null, üres karakterláncot ad vissza.

Ha a karakterlánc kisebb számú szavakat tartalmaz, vagy karakterlánca nem tartalmazza a szavakat az elválasztó karakterek által azonosított, üres karakterláncot ad vissza.

**Példa**  
`Word("The quick brown fox",3," ")`  
Adja vissza "brown"

`Word("This,string!has&many separators",3,",!&#")`  
Adna vissza "tartalmaz"

## <a name="additional-resources"></a>További források
* [Deklaratív kiépítés kifejezéseinek ismertetése](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Az Azure AD Connect szinkronizálása: Szinkronizálási beállítások testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
