---
title: "Azure AD Connect szinkronizálása: funkciók referencia |} Microsoft Docs"
description: "Az Azure AD Connect szinkronizálási szolgáltatás deklaratív kiépítés kifejezéseinek hivatkozását."
services: active-directory
documentationcenter: 
author: andkjell
manager: mtillman
editor: 
ms.assetid: 4f525ca0-be0e-4a2e-8da1-09b6b567ed5f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: d84a31e72d3e97ebb12f1747259fcb6e6b8fdcdc
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2018
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Azure AD Connect szinkronizálása: funkciók referencia
Az Azure AD Connectben funkciók segítségével kezelheti egy attribútum értékét a szinkronizálás során.  
A funkciók által használt szintaxis van kifejezve a következő formátumban:  
`<output type> FunctionName(<input type> <position name>, ..)`

Egy függvény túl van terhelve, és több szintaxissal fogad el, ha az összes érvényes szintaxissal vannak felsorolva.  
A funkciók erős típusmegadású vannak, és azok győződjön meg arról, hogy az átadott típus: megegyezik a dokumentált típusával.  
A típus nem egyezik, ha hiba fordul elő.

A típus szerint van megadva, a következő szintaxissal:

* **Bin** – bináris
* **logikai** – logikai
* **DT** – UTC dátum és idő
* **Enum** – az ismert állandók számbavétele
* **Exp** – eredménye egy logikai kifejezés
* **mvbin** – többértékű bináris
* **mvstr** – többértékű karakterlánc
* **mvref** – többértékű referencia
* **NUM** – numerikus
* **REF** – referencia
* **Str** – karakterlánc
* **var** – (szinte) bármilyen más típusú variant
* **"void"** – nem ad visszatérési értéket

A funkciók a típusú **mvbin**, **mvstr**, és **mvref** többértékű attribútumok csak használható. A Functions **bin**, **str**, és **ref** munkahelyi egyaránt egyértékű és többértékű jellemzőit.

## <a name="functions-reference"></a>Functions – referencia
| Függvények listája |  |  |  |  |
| --- | --- | --- | --- | --- | --- |
| **Tanúsítvány** | | | | |
| [CertExtensionOids](#certextensionoids) |[CertFormat](#certformat) |[CertFriendlyName](#certfriendlyname) |[CertHashString](#certhashstring) | |
| [CertIssuer](#certissuer) |[CertIssuerDN](#certissuerdn) |[CertIssuerOid](#certissueroid) |[CertKeyAlgorithm](#certkeyalgorithm) | |
| [CertKeyAlgorithmParams](#certkeyalgorithmparams) |[CertNameInfo](#certnameinfo) |[CertNotAfter](#certnotafter) |[CertNotBefore](#certnotbefore) | |
| [CertPublicKeyOid](#certpublickeyoid) |[CertPublicKeyParametersOid](#certpublickeyparametersoid) |[CertSerialNumber](#certserialnumber) |[CertSignatureAlgorithmOid](#certsignaturealgorithmoid) | |
| [CertSubject](#certsubject) |[CertSubjectNameDN](#certsubjectnamedn) |[CertSubjectNameOid](#certsubjectnameoid) |[CertThumbprint](#certthumbprint) | |
[CertVersion](#certversion) |[IsCert](#iscert) | | | |
| **Átalakítás** | | | | |
| [CBool](#cbool) |[CDate](#cdate) |[CGuid](#cguid) |[ConvertFromBase64](#convertfrombase64) | |
| [ConvertToBase64](#converttobase64) |[ConvertFromUTF8Hex](#convertfromutf8hex) |[ConvertToUTF8Hex](#converttoutf8hex) |[CNum](#cnum) | |
| [CRef](#cref) |[CStr](#cstr) |[StringFromGuid](#StringFromGuid) |[StringFromSid](#stringfromsid) | |
| **Dátum és idő** | | | | |
| [DateAdd](#dateadd) |[DateFromNum](#datefromnum) |[FormatDateTime](#formatdatetime) |[Most](#now) | |
| [NumFromDate](#numfromdate) | | | | |
| **Könyvtár** | | | | |
| [DNComponent](#dncomponent) |[DNComponentRev](#dncomponentrev) |[EscapeDNComponent](#escapedncomponent) | | |
| **Értékelés** | | | | |
| [IsBitSet](#isbitset) |[IsDate](#isdate) |[Vagyis IsEmpty](#isempty) |[IsGuid](#isguid) | |
| [IsNull](#isnull) |[IsNullOrEmpty](#isnullorempty) |[IsNumeric](#isnumeric) |[IsPresent](#ispresent) | |
| [IsString](#isstring) | | | | |
| **Matematikai** | | | | |
| [BitAnd](#bitand) |[BitOr](#bitor) |[RandomNum](#randomnum) | | |
| **Többértékű** | | | | |
| [Tartalmazza](#contains) |[Száma](#count) |[Elem](#item) |[ItemOrNull](#itemornull) | |
| [Csatlakozás](#join) |[RemoveDuplicates](#removeduplicates) |[Vegyes](#split) | | |
| **Program folyamata** | | | | |
| [Hiba történt](#error) |[AZ IIF](#iif) |[Kiválasztás](#select) |[Kapcsoló](#switch) | |
| [Ha](#where) |[A](#with) | | | |
| **Szöveg** | | | | |
| [GUID](#guid) |[InStr](#instr) |[InStrRev](#instrrev) |[LCase](#lcase) | |
| [Balra](#left) |[Hossz](#len) |[LTrim](#ltrim) |[Mid](#mid) | |
| [PadLeft](#padleft) |[PadRight](#padright) |[PCase](#pcase) |[Cserélje le](#replace) | |
| [ReplaceChars](#replacechars) |[Jobbra](#right) |[RTrim](#rtrim) |[Trim](#trim) | |
| [UCase](#ucase) |[Word](#word) | | | |

- - -
### <a name="bitand"></a>BitAnd
**Leírás:**  
A BitAnd függvény megadott bits értéket állítja be.

**Szintaxis:**  
`num BitAnd(num value1, num value2)`

* Érték1, érték2: numerikus érték, amely együtt kell-e érték

**Megjegyzés:**  
Ez a funkció mindkét paraméter átalakítja a bináris megjelenítése, és beállítja egy kicsit:

* 0 – Ha egy vagy mindkét a megfelelő bit *maszk* és *jelző* : 0
* 1 – Ha a megfelelő bits mindkettő az 1.

Ez azt jelenti akkor adja vissza 0 minden esetben, kivéve ha a megfelelő bitek száma mindkét paraméter 1.

**Példa**  
`BitAnd(&HF, &HF7)`  
7 adja vissza, mert hexadecimális "F" AND "F7" határozni, hogy erre az értékre.

- - -
### <a name="bitor"></a>BitOr
**Leírás:**  
A BitOr függvény megadott bits értéket állítja be.

**Szintaxis:**  
`num BitOr(num value1, num value2)`

* Érték1, érték2: numerikus érték, amely együtt kell-e köztük

**Megjegyzés:**  
Ez a funkció mindkét paraméter a bináris megjelenítése alakítja át, és beállítja egy kicsit Ha egyik vagy mindkét a megfelelő bit maszk és jelző 1 1 és 0 Ha mindkét megfelelő bit 0. Ez azt jelenti az 1 értékkel tér vissza minden esetben, ahol a megfelelő mindkét paraméter bitje 0 kivételével.

- - -
### <a name="cbool"></a>CBool
**Leírás:**  
A CBool függvény a kiértékelt kifejezés alapuló logikai érték beolvasása.

**Szintaxis:**  
`bool CBool(exp Expression)`

**Megjegyzés:**  
Ha a kifejezés értéke nulla, majd CBool igaz értéket ad vissza, ellenkező esetben a visszatérési hamis.

**Példa**  
`CBool([attrib1] = [attrib2])`  

Értéket ad vissza IGAZ, ha mindkét attribútumok ugyanazon értékekkel rendelkeznek.

- - -
### <a name="cdate"></a>CDate
**Leírás:**  
A CDate függvény UTC DateTime egy karakterláncot ad vissza. Dátum és idő nincs szinkronban natív attribútumtípus, de egyes funkciókat használják.

**Szintaxis:**  
`dt CDate(str value)`

* Érték: A karakterlánc dátum, idő, és opcionálisan időzóna

**Megjegyzés:**  
A visszaadott karakterlánc mindig UTC formátumban van.

**Példa**  
`CDate([employeeStartTime])`  
Egy dátum és idő alapján az alkalmazott beolvasása kezdési időpontja

`CDate("2013-01-10 4:00 PM -8")`  
Visszaadja egy jelölő dátum és idő "2013-01-11 12:00-kor"


- - -
### <a name="certextensionoids"></a>CertExtensionOids
**Leírás:**  
A kritikus bővítmények tanúsítvány objektum Oid értékeit adja vissza.

**Szintaxis:**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData: egy X.509 tanúsítvány bájt tömb ábrázolását. A bájttömb (DER) kódolású bináris vagy Base64 kódolású X.509 adatok lehetnek.

- - -
### <a name="certformat"></a>CertFormat
**Leírás:**  
A X.509v3 tanúsítványt formátumú nevét adja vissza.

**Szintaxis:**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData: egy X.509 tanúsítvány bájt tömb ábrázolását. A bájttömb (DER) kódolású bináris vagy Base64 kódolású X.509 adatok lehetnek.

- - -
### <a name="certfriendlyname"></a>CertFriendlyName
**Leírás:**  
A tanúsítványhoz társított alias adja vissza.

**Szintaxis:**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData: egy X.509 tanúsítvány bájt tömb ábrázolását. A bájttömb (DER) kódolású bináris vagy Base64 kódolású X.509 adatok lehetnek.

- - -
### <a name="certhashstring"></a>CertHashString
**Leírás:**  
Egy hexadecimális karakterláncban a X.509v3 tanúsítványt SHA1 ujjlenyomat értékét adja vissza.

**Szintaxis:**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData: egy X.509 tanúsítvány bájt tömb ábrázolását. A bájttömb (DER) kódolású bináris vagy Base64 kódolású X.509 adatok lehetnek.

- - -
### <a name="certissuer"></a>CertIssuer
**Leírás:**  
A X.509v3 tanúsítványt kiállító hitelesítésszolgáltató nevét adja vissza.

**Szintaxis:**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData: egy X.509 tanúsítvány bájt tömb ábrázolását. A bájttömb (DER) kódolású bináris vagy Base64 kódolású X.509 adatok lehetnek.

- - -
### <a name="certissuerdn"></a>CertIssuerDN
**Leírás:**  
A tanúsítvány kiállítójának megkülönböztető nevét adja vissza.

**Szintaxis:**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData: egy X.509 tanúsítvány bájt tömb ábrázolását. A bájttömb (DER) kódolású bináris vagy Base64 kódolású X.509 adatok lehetnek.

- - -
### <a name="certissueroid"></a>CertIssuerOid
**Leírás:**  
Az objektumazonosító, a tanúsítvány kiállítójának adja vissza.

**Szintaxis:**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData: egy X.509 tanúsítvány bájt tömb ábrázolását. A bájttömb (DER) kódolású bináris vagy Base64 kódolású X.509 adatok lehetnek.

- - -
### <a name="certkeyalgorithm"></a>CertKeyAlgorithm
**Leírás:**  
Ezt a X.509v3 tanúsítványt karakterláncként nyilvánoskulcs-képzési algoritmus információkat ad vissza.

**Szintaxis:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: egy X.509 tanúsítvány bájt tömb ábrázolását. A bájttömb (DER) kódolású bináris vagy Base64 kódolású X.509 adatok lehetnek.

- - -
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**Leírás:**  
A nyilvánoskulcs-képzési algoritmus a X.509v3 tanúsítványt paramétereinek Hexadecimális karakterláncként adja vissza.

**Szintaxis:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: egy X.509 tanúsítvány bájt tömb ábrázolását. A bájttömb (DER) kódolású bináris vagy Base64 kódolású X.509 adatok lehetnek.

- - -
### <a name="certnameinfo"></a>CertNameInfo
**Leírás:**  
Ad vissza a tulajdonos és a kiállító nevét a tanúsítványt.

**Szintaxis:**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData: egy X.509 tanúsítvány bájt tömb ábrázolását. A bájttömb (DER) kódolású bináris vagy Base64 kódolású X.509 adatok lehetnek.
*   X509NameType: A tulajdonos X509NameType értéke.
*   includesIssuerName: igaz értékre közé tartozik a kibocsátó neve; Ellenkező esetben hamis.

- - -
### <a name="certnotafter"></a>CertNotAfter
**Leírás:**  
A dátumot adja vissza, amely után a tanúsítvány már nem érvényes helyi idő.

**Szintaxis:**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData: egy X.509 tanúsítvány bájt tömb ábrázolását. A bájttömb (DER) kódolású bináris vagy Base64 kódolású X.509 adatok lehetnek.

- - -
### <a name="certnotbefore"></a>CertNotBefore
**Leírás:**  
A tanúsítvány hatályba lép, a helyi idő a dátumot adja vissza.

**Szintaxis:**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData: egy X.509 tanúsítvány bájt tömb ábrázolását. A bájttömb (DER) kódolású bináris vagy Base64 kódolású X.509 adatok lehetnek.

- - -
### <a name="certpublickeyoid"></a>CertPublicKeyOid
**Leírás:**  
Az objektumazonosító a X.509v3 tanúsítványhoz tartozó nyilvános kulcs adja vissza.

**Szintaxis:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: egy X.509 tanúsítvány bájt tömb ábrázolását. A bájttömb (DER) kódolású bináris vagy Base64 kódolású X.509 adatok lehetnek.

- - -
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**Leírás:**  
Az objektumazonosító, a nyilvános kulcs paramétereit a X.509v3 tanúsítványt adja vissza.

**Szintaxis:**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData: egy X.509 tanúsítvány bájt tömb ábrázolását. A bájttömb (DER) kódolású bináris vagy Base64 kódolású X.509 adatok lehetnek.

- - -
### <a name="certserialnumber"></a>CertSerialNumber
**Leírás:**  
A X.509v3 tanúsítvány sorozatszámát adja eredményül.

**Szintaxis:**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData: egy X.509 tanúsítvány bájt tömb ábrázolását. A bájttömb (DER) kódolású bináris vagy Base64 kódolású X.509 adatok lehetnek.

- - -
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**Leírás:**  
Az objektumazonosító, a tanúsítvány aláírásának létrehozására használt algoritmus adja vissza.

**Szintaxis:**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData: egy X.509 tanúsítvány bájt tömb ábrázolását. A bájttömb (DER) kódolású bináris vagy Base64 kódolású X.509 adatok lehetnek.

- - -
### <a name="certsubject"></a>CertSubject
**Leírás:**  
A tanúsítvány tulajdonosának megkülönböztető nevét lekérése.

**Szintaxis:**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData: egy X.509 tanúsítvány bájt tömb ábrázolását. A bájttömb (DER) kódolású bináris vagy Base64 kódolású X.509 adatok lehetnek.

- - -
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**Leírás:**  
A tanúsítvány tulajdonosának megkülönböztető nevét adja vissza.

**Szintaxis:**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData: egy X.509 tanúsítvány bájt tömb ábrázolását. A bájttömb (DER) kódolású bináris vagy Base64 kódolású X.509 adatok lehetnek.

- - -
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**Leírás:**  
Egy tanúsítványt az objektumazonosító, a tulajdonos nevét adja vissza.

**Szintaxis:**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData: egy X.509 tanúsítvány bájt tömb ábrázolását. A bájttömb (DER) kódolású bináris vagy Base64 kódolású X.509 adatok lehetnek.

- - -
### <a name="certthumbprint"></a>CertThumbprint
**Leírás:**  
A tanúsítvány ujjlenyomatát adja vissza.

**Szintaxis:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: egy X.509 tanúsítvány bájt tömb ábrázolását. A bájttömb (DER) kódolású bináris vagy Base64 kódolású X.509 adatok lehetnek.

- - -
### <a name="certversion"></a>CertVersion
**Leírás:**  
A tanúsítvány X.509 formátumú verziója adja vissza.

**Szintaxis:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: egy X.509 tanúsítvány bájt tömb ábrázolását. A bájttömb (DER) kódolású bináris vagy Base64 kódolású X.509 adatok lehetnek.

- - -
### <a name="cguid"></a>CGuid
**Leírás:**  
A CGuid függvény GUID karakterláncos ábrázolása konvertálja a bináris megjelenítése.

**Szintaxis:**  
`bin CGuid(str GUID)`

* Ebben a mintában formátumú karakterlánc: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx vagy {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

- - -
### <a name="contains"></a>Contains
**Leírás:**  
A Contains belüli egy többértékű karakterlánc keresése

**Szintaxis:**  
`num Contains (mvstring attribute, str search)`-nagybetűk  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)`-nagybetűk

* attribútum: a többértékű attribútum kereséséhez.
* Keresés: karakterlánc az attribútumban található.
* Casetype: CaseInsensitive vagy CaseSensitive.

A többértékű attribútum, ahol a karakterlánc található index értéket ad vissza. 0 eredményül, ha a karakterlánc nem található.

**Megjegyzés:**  
Attribútumok a többértékű karakterlánc a keresés karakterláncrészletek megkeresi az értékek.  
A hivatkozási attribútum a keresett karakterlánc pontosan meg kell egyeznie az érték akkor, ha egyezés.

**Példa**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Ha a proxyAddresses attribútum egy elsődleges e-mail címet (nagybetűs által jelzett "SMTP:"), majd térjen vissza a proxyAddress attribútum, ellenkező esetben egy hibaüzenetet.

- - -
### <a name="convertfrombase64"></a>ConvertFromBase64
**Leírás:**  
A ConvertFromBase64 függvény a megadott base64-kódolású érték konvertál egy rendszeres karakterlánc.

**Szintaxis:**  
`str ConvertFromBase64(str source)`-feltételezi, hogy a Unicode kódolási  
`str ConvertFromBase64(str source, enum Encoding)`

* Forrás: Base64 kódolású karakterlánc  
* Kódolást: Unicode, ASCII, UTF8

**Példa**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Mindkét példák vissza "*Hello world!*"

- - -
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex
**Leírás:**  
A ConvertFromUTF8Hex függvény a megadott kódolású UTF8 hexadecimális érték karakterlánccá alakítja át.

**Szintaxis:**  
`str ConvertFromUTF8Hex(str source)`

* Forrás: UTF8 2 bájtos kódolású karakterlánc

**Megjegyzés:**  
A függvény és, hogy a megkülönböztető név attribútum rövid-e az eredmény a ConvertFromBase64([],UTF8) közötti különbség.  
Ezt a formátumot használja Azure Active Directory megkülönböztető neve.

**Példa**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Értéket ad vissza "*Hello world!*"

- - -
### <a name="converttobase64"></a>ConvertToBase64
**Leírás:**  
A ConvertToBase64 függvény egy karakterlánc konvertál egy Unicode Base64 kódolású karakterlánc.  
Konvertálja az értéket egy tömb egész számok a megfelelő karakterlánc-ábrázolása számjegyükkel base-64 kódolású.

**Szintaxis:**  
`str ConvertToBase64(str source)`

**Példa**  
`ConvertToBase64("Hello world!")`  
Visszaadja a "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

- - -
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Leírás:**  
A ConvertToUTF8Hex függvény egy karakterlánc UTF8 hexadecimális encoded értékre konvertálja.

**Szintaxis:**  
`str ConvertToUTF8Hex(str source)`

**Megjegyzés:**  
A kimeneti formátum függvény lesz az Azure Active Directory megkülönböztető név attribútum formátuma.

**Példa**  
`ConvertToUTF8Hex("Hello world!")`  
48656C6C6F20776F726C6421 beolvasása

- - -
### <a name="count"></a>Darabszám
**Leírás:**  
A Count függvénnyel elemek számát adja vissza egy többértékű attribútum

**Szintaxis:**  
`num Count(mvstr attribute)`

- - -
### <a name="cnum"></a>CNum
**Leírás:**  
A CNum függvény lép egy karakterláncot, és adja vissza egy numerikus adattípusú.

**Szintaxis:**  
`num CNum(str value)`

- - -
### <a name="cref"></a>CRef
**Leírás:**  
Alakít át egy hivatkozási attribútum

**Szintaxis:**  
`ref CRef(str value)`

**Példa**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

- - -
### <a name="cstr"></a>CStr
**Leírás:**  
A CStr függvény egy karakterlánc adattípusra konvertálja.

**Szintaxis:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* érték: egy numerikus értéket, a hivatkozási attribútum, vagy a logikai érték lehet.

**Példa**  
`CStr([dn])`  
Visszaadhatja "cn = Joe, dc = contoso, dc = com"

- - -
### <a name="dateadd"></a>DateAdd
**Leírás:**  
Egy dátumot, amelyhez a megadott időtartam hozzáadását tartalmazó dátumot adja vissza.

**Szintaxis:**  
`dt DateAdd(str interval, num value, dt date)`

* időköz: karakterlánc-kifejezés, amely hozzá szeretné adni az időköz. A karakterlánc a következő értékek egyikével kell rendelkeznie:
  * ÉÉÉÉ év
  * q negyedév
  * m hónap
  * y év napja
  * d nap
  * w hét napja
  * hh hét
  * h óra
  * n perc
  * s második
* érték: A hozzáadni kívánt egységek számát. (A jövőbeli dátumok eléréséhez) pozitív vagy negatív (dátuma a múltban eléréséhez) lehet.
* dátum: DateTime jelölő dátum, amelyhez az időköz kerül.

**Példa**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
3 hónapos hozzáadja, és egy dátum és idő "2001-04-01" jelző adja vissza.

- - -
### <a name="datefromnum"></a>DateFromNum
**Leírás:**  
A DateFromNum függvény konvertál egy értéket AD meg dátum formátumú dátum és idő típusra.

**Szintaxis:**  
`dt DateFromNum(num value)`

**Példa**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Visszaadja a 2012-01-01 jelölő dátum és idő 23:00:00

- - -
### <a name="dncomponent"></a>DNComponent
**Leírás:**  
A DNComponent függvény bal üzembe helyezésről meghatározott DN összetevője értékét adja vissza.

**Szintaxis:**  
`str DNComponent(ref dn, num ComponentNumber)`

* megkülönböztető név: a hivatkozási attribútum értelmezése
* ComponentNumber: A DN vissza az összetevő

**Példa**  
`DNComponent(CRef([dn]),1)`  
Megkülönböztető név esetén "cn = Joe, ou =...," Joe adja vissza

- - -
### <a name="dncomponentrev"></a>DNComponentRev
**Leírás:**  
A DNComponentRev függvény jobb (záró) üzembe helyezésről meghatározott DN összetevője értékét adja vissza.

**Szintaxis:**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* megkülönböztető név: a hivatkozási attribútum értelmezése
* ComponentNumber - való visszatéréshez DN az összetevőt
* Beállítások: DC – figyelmen kívül az összes összetevő "dc ="

**Példa**  
Megkülönböztető név esetén "cn Joe, ou = Atlanta –, ou = GA, ou = = US, dc = contoso, dc = com" majd  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
Mindkét vissza VELÜNK.

- - -
### <a name="error"></a>Hiba
**Leírás:**  
A hiba függvényt ad vissza egy egyéni hiba okozta.

**Szintaxis:**  
`void Error(str ErrorMessage)`

**Példa**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Ha az attribútum accountName nincs jelen, throw hiba az objektum.

- - -
### <a name="escapedncomponent"></a>EscapeDNComponent
**Leírás:**  
Az EscapeDNComponent függvény időt vesz igénybe a DN egy összetevőt, és így ábrázolhatók az LDAP-kiszolgálón lehet kilépni.

**Szintaxis:**  
`str EscapeDNComponent(str value)`

**Példa**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Ellenőrzi, hogy az objektum egy LDAP-címtár is létrehozható, akkor is, ha a displayName attribútum karakterek, amelyek az LDAP-kiszolgálón kell megjelölni.

- - -
### <a name="formatdatetime"></a>FormatDateTime
**Leírás:**  
A FormatDateTime függvény segítségével egy dátum/idő karakterláncot formázni a megadott formátumban

**Szintaxis:**  
`str FormatDateTime(dt value, str format)`

* érték: a dátum és idő formátumú érték
* formátum: átalakítása formátum képviselő karakterláncot.

**Megjegyzés:**  
A lehetséges értékek a formátum itt található: [felhasználói dátum és idő formátumú (Format függvény)](http://msdn2.microsoft.com/library/73ctwf33\(VS.90\).aspx)

**Példa**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
"2007-12-25" eredményez.

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
"20140905081453.0Z" eredményezhet

- - -
### <a name="guid"></a>GUID
**Leírás:**  
A függvény Guid hoz létre egy új véletlenszerű GUID-ja

**Szintaxis:**  
`str Guid()`

- - -
### <a name="iif"></a>AZ IIF
**Leírás:**  
Az IIF függvény a megadott feltétel alapján lehetséges értékek egyikét adja vissza.

**Szintaxis:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* feltétel: bármely érték vagy kifejezés, amelynek kiértékelése igaz vagy hamis.
* valueIfTrue: Ha a feltétel igaz, a visszaadott érték.
* valueIfFalse: Ha a feltétel hamis, a visszaadott érték.

**Példa**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Ha a felhasználó egy internalizálási, adja vissza egy felhasználó a "t-", más elejéhez adja hozzá az aliast, a felhasználói alias adja vissza.

- - -
### <a name="instr"></a>InStr
**Leírás:**  
InStr keresése a első előfordulása karakterláncrész egy karakterláncban.

**Szintaxis:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* stringcheck: karakterláncának keresése
* stringmatch: keresett karakterlánc
* Start: kiindulási helyzet a karakterláncrész keresése
* Hasonlítsa össze: vbTextCompare vagy vbBinaryCompare

**Megjegyzés:**  
A helyét, ahol a substring található, vagy a 0, ha nincs találat adja vissza.

**Példa**  
`InStr("The quick brown fox","quick")`  
5 Evalues

`InStr("repEated","e",3,vbBinaryCompare)`  
7 kiértékelésének eredménye

- - -
### <a name="instrrev"></a>InStrRev
**Leírás:**  
InStrRev keresése a utolsó előfordulása karakterláncrész egy karakterláncban.

**Szintaxis:**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: karakterláncának keresése
* stringmatch: keresett karakterlánc
* Start: kiindulási helyzet a karakterláncrész keresése
* Hasonlítsa össze: vbTextCompare vagy vbBinaryCompare

**Megjegyzés:**  
A helyét, ahol a substring található, vagy a 0, ha nincs találat adja vissza.

**Példa**  
`InStrRev("abbcdbbbef","bb")`  
7 beolvasása

- - -
### <a name="isbitset"></a>IsBitSet
**Leírás:**  
A függvény IsBitSet teszteket, ha egy bit van beállítva, vagy sem

**Szintaxis:**  
`bool IsBitSet(num value, num flag)`

* érték: egy numerikus értéket, amely evaluated.flag: egy numerikus érték, amely rendelkezik a kiértékelendő bit

**Példa**  
`IsBitSet(&HF,4)`  
Igaz értéket ad vissza, mert a bit "4" be van állítva a "F" hexadecimális érték

- - -
### <a name="isdate"></a>IsDate
**Leírás:**  
Ha a kifejezés lehet kiértékeli dátum/idő típusként, majd a IsDate függvény eredménye igaz.

**Szintaxis:**  
`bool IsDate(var Expression)`

**Megjegyzés:**  
Azt határozza meg, ha CDate() sikeres lehet.

- - -
### <a name="iscert"></a>IsCert
**Leírás:**  
Igaz értéket ad eredményül, ha a nyers adatok .NET X509Certificate2 tanúsítványobjektum lehet szerializálni.

**Szintaxis:**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData: egy X.509 tanúsítvány bájt tömb ábrázolását. A bájttömb (DER) kódolású bináris vagy Base64 kódolású X.509 adatok lehetnek.
- - -
### <a name="isempty"></a>Vagyis IsEmpty
**Leírás:**  
Ha az attribútum a CS vagy MV szerepel, de üres karakterláncra értékelődik ki, majd a vagyis IsEmpty függvény eredménye igaz.

**Szintaxis:**  
`bool IsEmpty(var Expression)`

- - -
### <a name="isguid"></a>IsGuid
**Leírás:**  
Ha a karakterlánc egyedi azonosítóvá konvertálhatók, majd a IsGuid függvény értékeli igaz értékre.

**Szintaxis:**  
`bool IsGuid(str GUID)`

**Megjegyzés:**  
GUID egy ezeket a mintákat a következő karakterláncként van definiálva: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx vagy {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Azt határozza meg, ha CGuid() sikeres lehet.

**Példa**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Ha a StrAttribute GUID formátuma, térjen vissza a bináris megjelenítése, ellenkező esetben adhat vissza Null.

- - -
### <a name="isnull"></a>IsNull
**Leírás:**  
Ha a kifejezés értéke Null, majd a IsNull függvény igaz értéket ad vissza.

**Szintaxis:**  
`bool IsNull(var Expression)`

**Megjegyzés:**  
Egy attribútum a rendszer Null által a attribútum hiányában van kifejezve.

**Példa**  
`IsNull([displayName])`  
Igaz értéket ad vissza, ha az attribútum nem szerepel a CS vagy MV.

- - -
### <a name="isnullorempty"></a>IsNullOrEmpty
**Leírás:**  
Ha a kifejezés null vagy üres karakterlánc, majd a IsNullOrEmpty függvény igaz értéket ad vissza.

**Szintaxis:**  
`bool IsNullOrEmpty(var Expression)`

**Megjegyzés:**  
Egy attribútum Ez akkor igaz értéket, ha az attribútum hiányzik, vagy megtalálható, de üres karakterlánc.  
Ez a függvény inverzét IsPresent neve.

**Példa**  
`IsNullOrEmpty([displayName])`  
Igaz értéket ad vissza, ha az attribútum nincs jelen, vagy a Tanúsítványszolgáltatások vagy MV üres karakterlánc.

- - -
### <a name="isnumeric"></a>IsNumeric
**Leírás:**  
A IsNumeric függvény jelzi, hogy egy kifejezés kiértékelése számú típusként logikai értéket adja vissza.

**Szintaxis:**  
`bool IsNumeric(var Expression)`

**Megjegyzés:**  
Azt határozza meg, ha CNum() elemzése a kifejezés sikeres lehet.

- - -
### <a name="isstring"></a>IsString
**Leírás:**  
Ha a kifejezés kiértékelése karakterlánc típusú, majd a IsString függvény eredménye igaz.

**Szintaxis:**  
`bool IsString(var expression)`

**Megjegyzés:**  
Azt határozza meg, ha CStr() elemzése a kifejezés sikeres lehet.

- - -
### <a name="ispresent"></a>IsPresent
**Leírás:**  
Ha a kifejezés értéke nem Null, és nem üres karakterláncot, majd a IsPresent függvény igaz értéket ad vissza.

**Szintaxis:**  
`bool IsPresent(var expression)`

**Megjegyzés:**  
Ez a függvény inverzét IsNullOrEmpty neve.

**Példa**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

- - -
### <a name="item"></a>Elem
**Leírás:**  
Az Item függvény egy elemet egy többértékű karakterlánc attribútum ad vissza.

**Szintaxis:**  
`var Item(mvstr attribute, num index)`

* attribútum: többértékű attribútum
* index: index a többértékű karakterlánc elemen.

**Megjegyzés:**  
Az Item függvény akkor hasznos, a Contains függvény együtt, mert az utóbbi függvény az index a többértékű attribútum elemen.

Hibát jelez, ha az index az értéktartományon kívül esik.

**Példa**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Az elsődleges e-mail címét adja meg.

- - -
### <a name="itemornull"></a>ItemOrNull
**Leírás:**  
A ItemOrNull függvény egy elemet egy többértékű karakterlánc attribútum ad vissza.

**Szintaxis:**  
`var ItemOrNull(mvstr attribute, num index)`

* attribútum: többértékű attribútum
* index: index a többértékű karakterlánc elemen.

**Megjegyzés:**  
A ItemOrNull függvény akkor hasznos, együtt a Contains függvény, mert az utóbbi függvény az index a többértékű attribútum elemen.

Ha az index az értéktartományon kívül esik, majd adja vissza Null értéket.

- - -
### <a name="join"></a>Csatlakozás
**Leírás:**  
Illesztési függvényhez időt vesz igénybe a többértékű karakterlánc, és az egyes elemek között megadott elválasztó egyértékű karakterláncot ad vissza.

**Szintaxis:**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* attribútum: többértékű attribútum, amely tartalmazza a karakterláncot, amelyet egyesíteni.
* elválasztó karakter: bármilyen karakterlánc, a visszaadott karakterlánc karakterláncrészletek elválasztó. Ha nincs megadva, a szóköz karakter ("") használatos. Ha elválasztó karakter egy nulla hosszúságú karakterlánc (""), illetve semmi sem, a lista összes elemének nélkül határolójelek van kibővítve.

**Megjegyzések**  
A csatlakozási és felosztott funkciók között paritásos van. Az illesztési függvényhez karakterláncok veszi, és csatlakoztatja őket egy elválasztó karakterlánc használatával egyetlen karakterláncot. A felosztott függvény lép egy karakterláncot, és elválasztja azt a elválasztó, térjen vissza a karakterláncok:. A fő különbség azonban, hogy csatlakozási karakterlánc bármely határoló karakterláncot is összefűzésére, vegyes csak elkülönítheti karakterláncok egy egyetlen karaktert elválasztó használatával.

**Példa**  
`Join([proxyAddresses],",")`  
Visszaadhatja: "SMTP:john.doe@contoso.com,smtp:jd@contoso.com"

- - -
### <a name="lcase"></a>LCase
**Leírás:**  
A LCase függvény egy karakterlánc karaktereinek összes kisbetűsre konvertálja.

**Szintaxis:**  
`str LCase(str value)`

**Példa**  
`LCase("TeSt")`  
"Test" adja vissza.

- - -
### <a name="left"></a>Balra
**Leírás:**  
A bal oldali függvény egy karakterlánc bal oldali egy megadott számú karaktert adja vissza.

**Szintaxis:**  
`str Left(str string, num NumChars)`

* karakterlánc: a karaktert adja vissza a karakterláncot.
* NumChars: egy szám karakterek kezdetétől (balra) karakterlánc vissza

**Megjegyzés:**  
A karakterlánc első numChars karaktereket tartalmazó karakterláncot:

* Ha numChars = 0, térjen vissza az üres karakterlánc.
* Ha numChars < 0, térjen vissza a bemeneti karakterlánc.
* Ha karakterlánc null értékű, térjen vissza az üres karakterlánc.

Ha a karakterlánc a száma a megadott numChars-nál kevesebb karaktert tartalmaz, egy karakterlánc, karakterlánc (amely, 1. paraméter levő összes karakter) azonos ad vissza.

**Példa**  
`Left("John Doe", 3)`  
"Joh" adja vissza.

- - -
### <a name="len"></a>Hossz
**Leírás:**  
A Len függvény egy karakterlánc számú karaktert adja vissza.

**Szintaxis:**  
`num Len(str value)`

**Példa**  
`Len("John Doe")`  
8 beolvasása

- - -
### <a name="ltrim"></a>LTrim
**Leírás:**  
LTrim függvény kezdő szóközöket eltávolít egy karakterláncból.

**Szintaxis:**  
`str LTrim(str value)`

**Példa**  
`LTrim(" Test ")`  
"Test" értéket ad vissza

- - -
### <a name="mid"></a>Mid
**Leírás:**  
A közép függvény egy karakterlánc megadott helyéről adja vissza a megadott számú karaktert.

**Szintaxis:**  
`str Mid(str string, num start, num NumChars)`

* karakterlánc: a karaktert adja vissza a karakterláncot.
* Indítsa el: egy szám kezdő pozíciója a visszaadandó karakterek a karakterláncban
* NumChars: egy szám karakterek karakterláncon visszaadására

**Megjegyzés:**  
Indítsa el a pozíció visszatérési numChars karakterek karakterlánc.  
Pozíció indítás karakterláncban numChars karaktereket tartalmazó karakterláncot:

* Ha numChars = 0, térjen vissza az üres karakterlánc.
* Ha numChars < 0, térjen vissza a bemeneti karakterlánc.
* Ha start > karakterlánc hosszát adja vissza a bemeneti karakterlánc.
* Ha start < = 0, térjen vissza a bemeneti karakterlánc.
* Ha karakterlánc null értékű, térjen vissza az üres karakterlánc.

Ha nem numChar karakterek fennmaradó karakterlánc pozíció indítás, amit a lehető legtöbb karaktert ad vissza.

**Példa**  
`Mid("John Doe", 3, 5)`  
"Hn Do" adja vissza.

`Mid("John Doe", 6, 999)`  
Visszaadja a "Doe"

- - -
### <a name="now"></a>Most
**Leírás:**  
A funkcióval egy dátum/idő az aktuális dátum és idő alapján a számítógép rendszer dátum és idő megadása adja vissza.

**Szintaxis:**  
`dt Now()`

- - -
### <a name="numfromdate"></a>NumFromDate
**Leírás:**  
A NumFromDate függvény dátumformátum AD meg egy dátumot adja vissza.

**Szintaxis:**  
`num NumFromDate(dt value)`

**Példa**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
129699324000000000 beolvasása

- - -
### <a name="padleft"></a>PadLeft
**Leírás:**  
A PadLeft függvény bal-kézi a megadott kitöltési karaktereket használ egy meghatározott hosszúságú karakterláncot.

**Szintaxis:**  
`str PadLeft(str string, num length, str padCharacter)`

* karakterlánc: a karakterlánc szegélynél.
* hossz: a kívánt karakterlánc hossza jelző egész számot.
* padCharacter: kitöltő karakterként használandó egyetlen karaktert tartalmazó karakterlánc

**Megjegyzés:**

* Ha a karakterlánc hossza nagyobb, mint, majd padCharacter ismételten fűz hozzá a (múlva) karakterlánc hossza azt hosszának egyenlőnek elején.
* padCharacter lehet szóköz karakter, de nem lehet null értékű.
* Ha a karakterlánc hossza hosszának nagyobbnak vagy azzal egyenlőnek, karakterlánc ad változatlan vissza.
* Ha karakterlánc hossza nagyobb vagy egyenlő, hosszra, karakterlánc megegyezik egy karakterláncot ad vissza.
* Ha a karakterlánc hossza nagyobb, mint, majd a kívánt hosszúságú új karakterlánc ad vissza egy padCharacter feltöltve tartalmazó karakterlánc.
* Karakterlánc értéke null, ha a függvény egy üres karakterláncot ad vissza.

**Példa**  
`PadLeft("User", 10, "0")`  
"000000User" adja vissza.

- - -
### <a name="padright"></a>PadRight
**Leírás:**  
A PadRight függvény jobb-kézi a megadott kitöltési karaktereket használ egy meghatározott hosszúságú karakterláncot.

**Szintaxis:**  
`str PadRight(str string, num length, str padCharacter)`

* karakterlánc: a karakterlánc szegélynél.
* hossz: a kívánt karakterlánc hossza jelző egész számot.
* padCharacter: kitöltő karakterként használandó egyetlen karaktert tartalmazó karakterlánc

**Megjegyzés:**

* Ha a karakterlánc hossza nagyobb, mint, majd padCharacter ismételten fűz hozzá a karakterlánc (jobb oldali) vége amíg rá nem kényszerül hosszának egyenlőnek hosszát.
* padCharacter lehet szóköz karakter, de nem lehet null értékű.
* Ha a karakterlánc hossza hosszának nagyobbnak vagy azzal egyenlőnek, karakterlánc ad változatlan vissza.
* Ha karakterlánc hossza nagyobb vagy egyenlő, hosszra, karakterlánc megegyezik egy karakterláncot ad vissza.
* Ha a karakterlánc hossza nagyobb, mint, majd a kívánt hosszúságú új karakterlánc ad vissza egy padCharacter feltöltve tartalmazó karakterlánc.
* Karakterlánc értéke null, ha a függvény egy üres karakterláncot ad vissza.

**Példa**  
`PadRight("User", 10, "0")`  
"User000000" adja vissza.

- - -
### <a name="pcase"></a>PCase
**Leírás:**  
PCase függvény összes szóközzel elválasztott karakterlánc szó első karaktere átalakítja nagybetű, és további karakterekkel kisbetű alakulnak.

**Szintaxis:**  
`String PCase(string)`

**Megjegyzés:**

* Ez a funkció jelenleg nem biztosít a megfelelő kis-és teljes mértékben nagybetű, például az angol szót konvertálni.

**Példa**  
`PCase("TEsT")`  
"Test" adja vissza.

`PCase(LCase("TEST"))`  
"Test" értéket ad vissza

- - -
### <a name="randomnum"></a>RandomNum
**Leírás:**  
A RandomNum függvény között egy megadott időszakban egy véletlenszerű számot ad vissza.

**Szintaxis:**  
`num RandomNum(num start, num end)`

* Indítsa el: egy szám alacsonyabb korlátot a véletlenszerű érték létrehozásához
* vége: készítése a felső határ véletlenszerű érték azonosító szám

**Példa**  
`Random(100,999)`  
734 adhat vissza.

- - -
### <a name="removeduplicates"></a>RemoveDuplicates
**Leírás:**  
A RemoveDuplicates függvény időt vesz igénybe a többértékű karakterlánc, és győződjön meg arról, hogy egyedi.

**Szintaxis:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Példa**  
`RemoveDuplicates([proxyAddresses])`  
Adja vissza egy megtisztított proxyAddress attribútumot, ahol minden ismétlődő értékek el lettek távolítva.

- - -
### <a name="replace"></a>Csere
**Leírás:**  
A csere függvény egy karakterlánc másik karakterláncra összes előfordulását lecseréli.

**Szintaxis:**  
`str Replace(str string, str OldValue, str NewValue)`

* karakterlánc: egy karakterláncot cserélje le az értékeket.
* OldValue: A karakterlánc kereséséhez, és cserélje le.
* Új érték: A cserélendő karakterláncot számára.

**Megjegyzés:**  
A függvény felismeri a következő különleges monikerek:

* \n – új sor
* \r – kocsivissza
* \t – lap

**Példa**  
`Replace([address],"\r\n",", ")`  
CRLF lecseréli egy vesszőt és területet, és előfordulhat, hogy "Egy Microsoft módja, Redmond, WA, USA"

- - -
### <a name="replacechars"></a>ReplaceChars
**Leírás:**  
A ReplaceChars függvény a ReplacePattern karakterláncban található karakterek összes előfordulását lecseréli.

**Szintaxis:**  
`str ReplaceChars(str string, str ReplacePattern)`

* karakterlánc: egy karakterlánc karaktereinek lecseréli.
* ReplacePattern: a lecserélni kívánt karakterek szótár tartalmazó karakterlánc.

A formátum: {source1}: {target1}, {source2}: {target2}, {sourceN}, {targetN} ahol a forrás az a karakter, keresése és cseréje karakterlánc cél.

**Megjegyzés:**

* A függvény minden egyes előfordulásakor meghatározott források veszi és azok a célokat.
* A forrás (unicode) pontosan egy karaktert kell lennie.
* A forrás nem lehet üres vagy hosszabb, mint egy karakter (feldolgozási hiba).
* A cél több karakter, például ö:oe, β:ss lehet.
* Lehet, hogy a tároló üres, amely azt jelzi, hogy az a karakter el kell távolítani.
* A forrás kis-és nagybetűket, és pontosan egyeznie kell.
* A, (vessző) és: (kettőspont) fenntartott karakterek, és ez a funkció nem helyettesíthető.
* Szóközt és más fehér karaktereket a ReplacePattern karakterláncban a rendszer figyelmen kívül hagyja.

**Példa**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Raksmorgas adja vissza

`ReplaceChars("O’Neil",%ReplaceString%)`  
Beolvasása "ONeil", az egyetlen osztásjelek van definiálva, el kell távolítani.

- - -
### <a name="right"></a>Jobbra
**Leírás:**  
A jobb oldali függvény a megadott számú karaktert ad vissza egy karakterlánc jobb (záró).

**Szintaxis:**  
`str Right(str string, num NumChars)`

* karakterlánc: a karaktert adja vissza a karakterláncot.
* NumChars: egy szám karakterek végén (jobb oldali) karakterlánc vissza

**Megjegyzés:**  
NumChars karaktereket a rendszer karakterlánc utolsó pozícióját adja vissza.

A karakterláncon belül az utolsó numChars karaktereket tartalmazó karakterláncot:

* Ha numChars = 0, térjen vissza az üres karakterlánc.
* Ha numChars < 0, térjen vissza a bemeneti karakterlánc.
* Ha karakterlánc null értékű, térjen vissza az üres karakterlánc.

Ha karakterlánc a száma a megadott NumChars-nál kevesebb karaktert tartalmaz, karakterlánc megegyezik egy karakterláncot ad vissza.

**Példa**  
`Right("John Doe", 3)`  
"Doe" adja vissza.

- - -
### <a name="rtrim"></a>RTrim
**Leírás:**  
RTrim függvény záró szóközt eltávolít egy karakterláncból.

**Szintaxis:**  
`str RTrim(str value)`

**Példa**  
`RTrim(" Test ")`  
"Test" adja vissza.

- - -
### <a name="select"></a>Válassza ezt:
**Leírás:**  
A folyamat egy többértékű attribútum (vagy egy kifejezés eredményének) szereplő összes érték a megadott függvény alapján.

**Szintaxis:**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* cikk: a többértékű attribútumban olyan elemet
* attribútum: a többértékű attribútum
* kifejezés: értékek gyűjteményét visszaadó kifejezés
* feltétel: semmilyen feladatot, amely képes a attribútumban elem

**Példák:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
Összes érték visszaadása a többértékű attribútum otherPhone után a kötőjeleket (-) el lett távolítva.

- - -
### <a name="split"></a>Megosztott
**Leírás:**  
A felosztott függvény elválasztóval elválasztott karakterlánc vesz igénybe, és lehetővé teszi egy többértékű karakterlánc.

**Szintaxis:**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* érték: a karakterlánc elválasztásához szövegelválasztó karaktert.
* elválasztó karakter: egyetlen, az elválasztó karakter használható.
* korlát: értékeket adhat vissza maximális számát.

**Példa**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
2 elemek többértékű karakterláncot ad vissza a proxyAddress attribútum használható.

- - -
### <a name="stringfromguid"></a>StringFromGuid
**Leírás:**  
Az StringFromGuid függvény bináris GUID vesz igénybe, és konvertálja azt a karakterláncot

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
A kapcsoló függvényt ad vissza az értékelt feltételek alapján egyetlen értéket.

**Szintaxis:**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* kifejezés: Variant kifejezés ki kell számítani.
* érték: vissza kell adni, ha a megfelelő kifejezés igaz értéket.

**Megjegyzés:**  
A kapcsoló függvény argumentumlista kifejezések és érték párokból áll. A kifejezések kiértékelése balról jobbra, és a rendelt érték, amely az első kifejezés igaz értéket ad vissza. Ha nincsenek megfelelően párosítva a kijelzők, a futásidejű hiba történik.

Például Kif1 értéke igaz, ha a kapcsoló érték1 adja vissza. Ha kifejezés-1 érték hamis, de kifejezés-2 értéke igaz, kapcsoló érték-2, és így tovább adja vissza.

Kapcsoló adja vissza egy Nothing ha:

* A kifejezések egyike sem igaz.
* Az első igaz kifejezés a megfelelő értékkel rendelkezik, amely null értékű.

Kapcsoló kiértékeli az összes kifejezést, annak ellenére, hogy csak az egyiket adja vissza. Emiatt érdemes figyelemmel a nemkívánatos hatásai. Például a nullával való osztást bármely kifejezés kiértékelése eredményez, ha hiba lép fel.

A hiba függvénynek, amely akkor adja vissza egyéni érték is lehet.

**Példa**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Néhány főbb városában szóbeli nyelvét adja eredményül, ellenkező esetben a hibát ad vissza.

- - -
### <a name="trim"></a>Trim
**Leírás:**  
A vágás függvény eltávolítja a kezdő és záró szóközök karakterláncból.

**Szintaxis:**  
`str Trim(str value)`  

**Példa**  
`Trim(" Test ")`  
"Test" adja vissza.

`Trim([proxyAddresses])`  
Eltávolítja a kezdő és záró szóközök proxyAddress attribútum szereplő összes értékhez.

- - -
### <a name="ucase"></a>UCase
**Leírás:**  
A UCase függvény egy karakterlánc karaktereinek összes nagybetűvel konvertálja.

**Szintaxis:**  
`str UCase(str string)`

**Példa**  
`UCase("TeSt")`  
"TEST" adja vissza.

- - -
### <a name="where"></a>Ha

**Leírás:**  
A megadott feltétel alapján többértékű attribútum (vagy egy kifejezés eredményének) értékek egy alhalmazát adja vissza.

**Szintaxis:**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* cikk: a többértékű attribútumban olyan elemet
* attribútum: a többértékű attribútum
* feltétel: bármely kifejezés igaz vagy hamis kiértékelhető
* kifejezés: értékek gyűjteményét visszaadó kifejezés

**Példa**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
A többértékű attribútum userCertificate, amely nem található a tanúsítvány értékek visszaadása.

- - -
### <a name="with"></a>A következővel:
**Leírás:**  
A With függvény segítségével egyszerűbbé teheti a összetett kifejezést egy változó segítségével egy alkifejezés, egy megjelenő képviselő vagy többször az összetett kifejezésben.

**Szintaxis:**
`With(var variable, exp subExpression, exp complexExpression)`  
* változó: a alkifejezés jelöli.
* alkifejezés: változó által képviselt alkifejezés.
* complexExpression: egy összetett kifejezés.

**Példa**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
Mint:  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
Amely a userCertificate attribútum csak leküldéshez tanúsítvány értékeket adja vissza.


- - -
### <a name="word"></a>Word
**Leírás:**  
A Word függvény egy karakterlánc, és a word számát adja vissza az elválasztó karaktert leíró paraméterek alapján belül található szót adja vissza.

**Szintaxis:**  
`str Word(str string, num WordNumber, str delimiters)`

* karakterlánc: a karakterláncot. térjen vissza a szót.
* WordNumber: egy szám mely word számot kell visszaadnia.
* elválasztó karaktert: egy karakterlánc, amely a szavakat azonosítására használt delimiter(s)

**Megjegyzés:**  
A határoló karaktereit elválasztott karakterlánc minden karakterlánchoz szavak azonosítja:

* Ha < 1-es számú, értéket ad vissza üres karakterlánc.
* Ha a karakterlánc értéke null, üres karakterláncot ad vissza.

Karakterlánc nagyobb számú karaktert tartalmaz, vagy karakterlánca nem tartalmazza az elválasztó karaktert által azonosított szavak, ha üres karakterláncot ad vissza.

**Példa**  
`Word("The quick brown fox",3," ")`  
Visszaadja a "nem"

`Word("This,string!has&many separators",3,",!&#")`  
Alakítanák vissza "tartalmaz"

## <a name="additional-resources"></a>További források
* [Deklaratív kiépítés kifejezéseinek ismertetése](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
* [Az Azure AD Connect-szinkronizálás: Szinkronizálási beállítások testreszabása](active-directory-aadconnectsync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
