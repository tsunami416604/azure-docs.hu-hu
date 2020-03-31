---
title: 'Azure AD Connect szinkronizálás: Függvények – hivatkozás | Microsoft dokumentumok'
description: Deklaratív létesítési kifejezések hivatkozásaz Azure AD Connect-szinkronizálásban.
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
ms.openlocfilehash: 5c3102480e316c634930c356ae02f769767b7d08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69900047"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Azure AD Connect szinkronizálás: Függvények – referencia
Az Azure AD Connect ben függvények egy attribútumérték szinkronizálás a szinkronizálás során.  
A függvények szintaxisa a következő formátumban van kifejezve:  
`<output type> FunctionName(<input type> <position name>, ..)`

Ha egy függvény túl terhelt, és több szintaxist fogad el, az összes érvényes szintaxis megjelenik.  
A függvények erősen bevannak gépelve, és ellenőrzik, hogy a megadott típus megegyezik-e a dokumentált típussal.  
Ha a típus nem egyezik, a rendszer hibát jelez.

A típusok a következő szintaxissal vannak kifejezve:

* **bin** – bináris
* **bool** – logikai
* **dt** – UTC dátum/idő
* **enum** – ismert állandók felsorolása
* **exp** – Kifejezés, amely várhatóan logikai értékre értékel
* **mvbin** – Többértékű bináris
* **mvstr** – Többértékű karakterlánc
* **mvref** – Többértékű hivatkozás
* **numeric** – numerikus
* **ref** – Hivatkozás
* **str** – Karakterlánc
* **var** – Bármely más típusú (majdnem) változat
* **void** – nem ad vissza értéket

A **mvbin**, mvstr és **mvref** típusú függvények csak többértékű attribútumokon működnek. **mvstr** A **raktárhely**, **str**és **ref** függvények mind az egyértékű, mind a többértékű attribútumokon működnek.

## <a name="functions-reference"></a>Functions – referencia

| A függvények listája |  |  |  |  |
| --- | --- | --- | --- | --- |
| **Tanúsítvány** | | | | |
| [CertExtensionOidok](#certextensionoids) |[Tanúsítványformátum](#certformat) |[CertFriendlyName (CertFriendlyName)](#certfriendlyname) |[CertHashString](#certhashstring) | |
| [CertIssuer](#certissuer) |[CertIssuerDN](#certissuerdn) |[CertIssuerOid](#certissueroid) |[CertKeyAlgorithm (CertKeyAlgorithm)](#certkeyalgorithm) | |
| [CertKeyAlgorithmParams](#certkeyalgorithmparams) |[CertNameInfo](#certnameinfo) |[CertNotAfter után](#certnotafter) |[CertNotElőtte](#certnotbefore) | |
| [CertPublicKeyOid (CertPublicKeyOid)](#certpublickeyoid) |[CertPublicKeyParametersOid](#certpublickeyparametersoid) |[CertSerialNumber](#certserialnumber) |[CertSignatureAlgorithmOid](#certsignaturealgorithmoid) | |
| [CertSubject](#certsubject) |[CertSubjectNameDN](#certsubjectnamedn) |[CertSubjectNameOid](#certsubjectnameoid) |[CertThumbprint](#certthumbprint) | |
[CertVersion (CertVersion)](#certversion) |[IsCert között](#iscert) | | | |
| **Átalakítás** | | | | |
| [Kbool](#cbool) |[CDátum](#cdate) |[CGuid között](#cguid) |[ConvertFromBase64](#convertfrombase64) | |
| [KonvertálásBase64](#converttobase64) |[ConvertFromUTF8Hex](#convertfromutf8hex) |[ConvertToUTF8Hex](#converttoutf8hex) |[CÉv](#cnum) | |
| [CRef](#cref) |[CStr](#cstr) |[StringFromGuid](#stringfromguid) |[Karakterláncszisid](#stringfromsid) | |
| **Dátum és idő** | | | | |
| [Dátum hozzáadása](#dateadd) |[DateFromNum (Dátumfromnum)](#datefromnum) |[FormatDateTime](#formatdatetime) |[Nwo](#now) | |
| [NumFromDate (Dátum kezdőszáma)](#numfromdate) | | | | |
| **Címtár** | | | | |
| [DNösszetevő](#dncomponent) |[DNComponentRev](#dncomponentrev) |[EscapeDNComponent (EscapeDNComponent)](#escapedncomponent) | | |
| **Értékelés** | | | | |
| [IsBitSet](#isbitset) |[IsDate (Dátum)](#isdate) |[Üres](#isempty) |[IsGuid között](#isguid) | |
| [IsNull](#isnull) |[IsNullOrEmpty](#isnullorempty) |[IsNumeric](#isnumeric) |[IsPresent (Jelen van)](#ispresent) | |
| [Karakterlánc](#isstring) | | | | |
| **Matematikai** | | | | |
| [Bitand (bitand)](#bitand) |[Bitor](#bitor) |[Véletlen](#randomnum) | | |
| **Többértékű** | | | | |
| [Tartalmaz](#contains) |[Darabszám](#count) |[Elem](#item) |[ItemOrNull](#itemornull) | |
| [Csatlakozás](#join) |[Másolateltávolítása](#removeduplicates) |[Split](#split) | | |
| **Programfolyamat** | | | | |
| [Hiba](#error) |[Iif](#iif) |[Kiválasztás](#select) |[Kapcsoló](#switch) | |
| [Ahol](#where) |[A](#with) | | | |
| **Szöveg** | | | | |
| [Guid](#guid) |[Instr](#instr) |[Instrrev között](#instrrev) |[LCase](#lcase) | |
| [Bal](#left) |[Len között](#len) |[LTrim között](#ltrim) |[Közepén](#mid) | |
| [PadBalra](#padleft) |[PadJobbra](#padright) |[PCase](#pcase) |[Helyettesít](#replace) | |
| [Helyettesítő karakter](#replacechars) |[Jobb](#right) |[RTrim között](#rtrim) |[Berendezés](#trim) | |
| [Ucase](#ucase) |[Word](#word) | | | |

---
### <a name="bitand"></a>Bitand (bitand)
**Leírás:**  
A BitAnd függvény megadott biteket állít be egy értékre.

**Szintaxis:**  
`num BitAnd(num value1, num value2)`

* érték1, érték2: numerikus értékek, amelyeket együtt kell

**Megjegyzések:**  
Ez a függvény mindkét paramétert bináris reprezentációvá alakítja, és egy kicsit a következőkre állítja be:

* 0 - ha az egyik vagy mindkét megfelelő bit *érték1* és *érték2* 0
* 1 - ha mindkét megfelelő bit 1.

Más szóval minden esetben 0 értéket ad vissza, kivéve, ha mindkét paraméter megfelelő bitje 1.

**Példa:**  
`BitAnd(&HF, &HF7)`  
7-et ad eredményül, mert hexadecimális "F" és "F7" kiértékelt értéket erre az értékre.

---
### <a name="bitor"></a>Bitor
**Leírás:**  
A BitOr függvény megadott biteket állít be egy értékre.

**Szintaxis:**  
`num BitOr(num value1, num value2)`

* érték1, érték2: numerikus értékek, amelyeket együtt kell vagy

**Megjegyzések:**  
Ez a függvény mindkét paramétert bináris reprezentációvá alakítja, és egy bitet 1-re állít be, ha a maszkban és a jelzőben az egyik vagy mindkét bit 1, és 0-ra, ha mindkét megfelelő bit 0. Más szóval minden esetben 1 értéket ad vissza, kivéve, ha mindkét paraméter megfelelő bitje 0.

---
### <a name="cbool"></a>Kbool
**Leírás:**  
A CBool függvény egy logikai értéket ad vissza a kiértékelt kifejezés alapján.

**Szintaxis:**  
`bool CBool(exp Expression)`

**Megjegyzések:**  
Ha a kifejezés nem nulla értéket ad eredményül, akkor a CBool függvény igaz értéket ad vissza, máskülönben hamis értéket ad vissza.

**Példa:**  
`CBool([attrib1] = [attrib2])`  

Igaz értéket ad vissza, ha mindkét attribútum értéke megegyezik.

---
### <a name="cdate"></a>CDátum
**Leírás:**  
A CDate függvény egy KARAKTERLÁNCBÓL származó UTC DateTime értéket ad vissza. A DateTime nem natív attribútumtípus a Szinkronizálás ban, de egyes függvények használják.

**Szintaxis:**  
`dt CDate(str value)`

* Érték: Dátummal, idővel és opcionálisan időzónával rendelkező karakterlánc

**Megjegyzések:**  
A visszaadott karakterlánc mindig UTC-ben van.

**Példa:**  
`CDate([employeeStartTime])`  
DateTime értéket ad eredményül az alkalmazott kezdési időpontja alapján.

`CDate("2013-01-10 4:00 PM -8")`  
A "2013-01-11 12:00 AM" értéket képviselő DateTime értéket adja eredményül.


---
### <a name="certextensionoids"></a>CertExtensionOidok
**Leírás:**  
Egy tanúsítványobjektum összes kritikus bővítményének Oid értékeit adja eredményül.

**Szintaxis:**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData: X.509-es tanúsítvány bájttömb-ábrázolása. A bájttömb lehet bináris (DER) kódolású vagy Base64 kódolású X.509-es adat.

---
### <a name="certformat"></a>Tanúsítványformátum
**Leírás:**  
Az X.509v3 tanúsítvány formátumának nevét adja eredményül.

**Szintaxis:**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData: X.509-es tanúsítvány bájttömb-ábrázolása. A bájttömb lehet bináris (DER) kódolású vagy Base64 kódolású X.509-es adat.

---
### <a name="certfriendlyname"></a>CertFriendlyName (CertFriendlyName)
**Leírás:**  
Egy tanúsítvány társított aliasát adja vissza.

**Szintaxis:**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData: X.509-es tanúsítvány bájttömb-ábrázolása. A bájttömb lehet bináris (DER) kódolású vagy Base64 kódolású X.509-es adat.

---
### <a name="certhashstring"></a>CertHashString
**Leírás:**  
Az X.509v3 tanúsítvány SHA1 kivonatértékét adja eredményül hexadecimális karakterláncként.

**Szintaxis:**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData: X.509-es tanúsítvány bájttömb-ábrázolása. A bájttömb lehet bináris (DER) kódolású vagy Base64 kódolású X.509-es adat.

---
### <a name="certissuer"></a>CertIssuer
**Leírás:**  
Az X.509v3 tanúsítványt kiállító hitelesítésszolgáltató nevét adja eredményül.

**Szintaxis:**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData: X.509-es tanúsítvány bájttömb-ábrázolása. A bájttömb lehet bináris (DER) kódolású vagy Base64 kódolású X.509-es adat.

---
### <a name="certissuerdn"></a>CertIssuerDN
**Leírás:**  
A tanúsítvány kibocsátójának megkülönböztető nevét adja eredményül.

**Szintaxis:**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData: X.509-es tanúsítvány bájttömb-ábrázolása. A bájttömb lehet bináris (DER) kódolású vagy Base64 kódolású X.509-es adat.

---
### <a name="certissueroid"></a>CertIssuerOid
**Leírás:**  
A tanúsítvány kibocsátójának Oidját adja eredményül.

**Szintaxis:**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData: X.509-es tanúsítvány bájttömb-ábrázolása. A bájttömb lehet bináris (DER) kódolású vagy Base64 kódolású X.509-es adat.

---
### <a name="certkeyalgorithm"></a>CertKeyAlgorithm (CertKeyAlgorithm)
**Leírás:**  
Az X.509v3 tanúsítvány kulcsalgoritmus-adatait adja eredményül karakterláncként.

**Szintaxis:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: X.509-es tanúsítvány bájttömb-ábrázolása. A bájttömb lehet bináris (DER) kódolású vagy Base64 kódolású X.509-es adat.

---
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**Leírás:**  
Az X.509v3 tanúsítvány hexadecimális karakterláncként való kulcsalgoritmus-paramétereit adja eredményül.

**Szintaxis:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: X.509-es tanúsítvány bájttömb-ábrázolása. A bájttömb lehet bináris (DER) kódolású vagy Base64 kódolású X.509-es adat.

---
### <a name="certnameinfo"></a>CertNameInfo
**Leírás:**  
A tulajdonos és a kiállító nevét adja vissza egy tanúsítványból.

**Szintaxis:**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData: X.509-es tanúsítvány bájttömb-ábrázolása. A bájttömb lehet bináris (DER) kódolású vagy Base64 kódolású X.509-es adat.
*   X509NameType: A tárgy X509NameType értéke.
*   includesIssuerName: true a kibocsátó nevének feltüntetéséhez; ellenkező esetben hamis.

---
### <a name="certnotafter"></a>CertNotAfter után
**Leírás:**  
Azt a helyi dátumot adja eredményül, amely után a tanúsítvány már nem érvényes.

**Szintaxis:**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData: X.509-es tanúsítvány bájttömb-ábrázolása. A bájttömb lehet bináris (DER) kódolású vagy Base64 kódolású X.509-es adat.

---
### <a name="certnotbefore"></a>CertNotElőtte
**Leírás:**  
Helyi idő szerint adja eredményül azt a dátumot, amikor a tanúsítvány érvényessé válik.

**Szintaxis:**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData: X.509-es tanúsítvány bájttömb-ábrázolása. A bájttömb lehet bináris (DER) kódolású vagy Base64 kódolású X.509-es adat.

---
### <a name="certpublickeyoid"></a>CertPublicKeyOid (CertPublicKeyOid)
**Leírás:**  
Az X.509v3 tanúsítvány nyilvános kulcsának Oidját adja vissza.

**Szintaxis:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: X.509-es tanúsítvány bájttömb-ábrázolása. A bájttömb lehet bináris (DER) kódolású vagy Base64 kódolású X.509-es adat.

---
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**Leírás:**  
Az X.509v3 tanúsítvány nyilvános kulcsparamétereinek Oidértékét adja vissza.

**Szintaxis:**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData: X.509-es tanúsítvány bájttömb-ábrázolása. A bájttömb lehet bináris (DER) kódolású vagy Base64 kódolású X.509-es adat.

---
### <a name="certserialnumber"></a>CertSerialNumber
**Leírás:**  
Az X.509v3 tanúsítvány sorozatszámát adja eredményül.

**Szintaxis:**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData: X.509-es tanúsítvány bájttömb-ábrázolása. A bájttömb lehet bináris (DER) kódolású vagy Base64 kódolású X.509-es adat.

---
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**Leírás:**  
A tanúsítvány aláírásának létrehozásához használt algoritmus Oidját adja eredményül.

**Szintaxis:**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData: X.509-es tanúsítvány bájttömb-ábrázolása. A bájttömb lehet bináris (DER) kódolású vagy Base64 kódolású X.509-es adat.

---
### <a name="certsubject"></a>CertSubject
**Leírás:**  
A tulajdonos megkülönböztető nevét beszerzi egy tanúsítványból.

**Szintaxis:**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData: X.509-es tanúsítvány bájttömb-ábrázolása. A bájttömb lehet bináris (DER) kódolású vagy Base64 kódolású X.509-es adat.

---
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**Leírás:**  
A tulajdonos megkülönböztető nevét adja vissza egy tanúsítványból.

**Szintaxis:**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData: X.509-es tanúsítvány bájttömb-ábrázolása. A bájttömb lehet bináris (DER) kódolású vagy Base64 kódolású X.509-es adat.

---
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**Leírás:**  
A tulajdonos nevének Oidját adja vissza egy tanúsítványból.

**Szintaxis:**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData: X.509-es tanúsítvány bájttömb-ábrázolása. A bájttömb lehet bináris (DER) kódolású vagy Base64 kódolású X.509-es adat.

---
### <a name="certthumbprint"></a>CertThumbprint
**Leírás:**  
Egy tanúsítvány ujjlenyomatát adja vissza.

**Szintaxis:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: X.509-es tanúsítvány bájttömb-ábrázolása. A bájttömb lehet bináris (DER) kódolású vagy Base64 kódolású X.509-es adat.

---
### <a name="certversion"></a>CertVersion (CertVersion)
**Leírás:**  
A tanúsítvány X.509 formátumú verzióját adja eredményül.

**Szintaxis:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: X.509-es tanúsítvány bájttömb-ábrázolása. A bájttömb lehet bináris (DER) kódolású vagy Base64 kódolású X.509-es adat.

---
### <a name="cguid"></a>CGuid között
**Leírás:**  
A CGuid függvény a GUID karakterlánc-ábrázolását bináris ábrázolásává alakítja.

**Szintaxis:**  
`bin CGuid(str GUID)`

* A String formázott ez a minta: xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx vagy {xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxxx

---
### <a name="contains"></a>Contains
**Leírás:**  
A Tartalmazza függvény karakterláncot talál egy többértékű attribútumban

**Szintaxis:**  
`num Contains (mvstring attribute, str search)`- kis- és nagybetűk megkülönböztetése  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)`- kis- és nagybetűk megkülönböztetése

* attribútum: a kereséshez többértékű attribútum.
* keresés: string megtalálni az attribútumot.
* Kis- és nagybetű: Kis- és nagybetűNem legkülönfélék vagy Kis- és nagybetűk megkülönböztetése.

Indexet ad vissza abban a többértékű attribútumban, ahol a karakterlánc található. A 0 értéket adja vissza, ha a karakterlánc nem található.

**Megjegyzések:**  
A többértékű karakterlánc-attribútumok esetében a keresés az értékekben lévő részkarakterláncokat keresi meg.  
Referenciaattribútumok esetén a keresett karakterláncnak pontosan meg kell egyeznie az egyezésnek minősülő értékkel.

**Példa:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Ha a proxyAddresses attribútum elsődleges e-mail címmel rendelkezik (amelyet nagybetűs "SMTP:" jelez), akkor adja vissza a proxyAddress attribútumot, máskülönben hibát ad vissza.

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**Leírás:**  
A ConvertFromBase64 függvény a megadott base64 kódolású értéket normál karakterláncsá alakítja.

**Szintaxis:**  
`str ConvertFromBase64(str source)`- feltételezi, Unicode kódolás  
`str ConvertFromBase64(str source, enum Encoding)`

* forrás: Base64 kódolású karakterlánc  
* Kódolás: Unicode, ASCII, UTF8

**Példa**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Mindkét példa vissza "*Hello world!*"

---
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex
**Leírás:**  
A ConvertFromUTF8Hex függvény a megadott UTF8 Hex kódolású értéket karakterlánccá alakítja.

**Szintaxis:**  
`str ConvertFromUTF8Hex(str source)`

* forrás: UTF8 2 bájtos kódolt csípés

**Megjegyzések:**  
A függvény és a ConvertFromBase64([],UTF8) közötti különbség, mivel az eredmény a DN attribútum számára megfelelő.  
Ezt a formátumot az Azure Active Directory dn-ként használja.

**Példa:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Visszatér:*"Hello world!*"

---
### <a name="converttobase64"></a>KonvertálásBase64
**Leírás:**  
A ConvertToBase64 függvény egy karakterláncot Unicode base64 karakterláncgá alakít át.  
Egy egész tömb értékét a 64-es alapjegyekkel kódolt karakterlánc-ábrázolási értékké alakítja.

**Szintaxis:**  
`str ConvertToBase64(str source)`

**Példa:**  
`ConvertToBase64("Hello world!")`  
Eredmény: "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Leírás:**  
A ConvertToUTF8Hex függvény egy karakterláncot UTF8 Hex kódolású értékké alakít át.

**Szintaxis:**  
`str ConvertToUTF8Hex(str source)`

**Megjegyzések:**  
A függvény kimeneti formátumát az Azure Active Directory dn attribútumformátumként használja.

**Példa:**  
`ConvertToUTF8Hex("Hello world!")`  
Eredménye 48656C6C6F20776F726C6421

---
### <a name="count"></a>Darabszám
**Leírás:**  
A Count függvény egy többértékű attribútum elemeinek számát adja eredményül.

**Szintaxis:**  
`num Count(mvstr attribute)`

---
### <a name="cnum"></a>CÉv
**Leírás:**  
A CNum függvény egy karakterláncot vesz fel, és numerikus adattípust ad vissza.

**Szintaxis:**  
`num CNum(str value)`

---
### <a name="cref"></a>CRef
**Leírás:**  
Karakterlánc átalakítása hivatkozási attribútummá

**Szintaxis:**  
`ref CRef(str value)`

**Példa:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

---
### <a name="cstr"></a>CStr
**Leírás:**  
A CStr függvény karakterlánc-adattípussá alakul át.

**Szintaxis:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* érték: Lehet numerikus érték, hivatkozási attribútum vagy logikai érték.

**Példa:**  
`CStr([dn])`  
Visszaadhatja a "cn=Joe,dc=contoso,dc=com"

---
### <a name="dateadd"></a>DateAdd
**Leírás:**  
Egy dátumot tartalmazó dátumot ad eredményül, amelyhez egy megadott időintervallum hozzá lett adva.

**Szintaxis:**  
`dt DateAdd(str interval, num value, dt date)`

* időköz: Karakterlánc-kifejezés, amely a hozzáadni kívánt időintervallum. A karakterláncnak az alábbi értékek egyikével kell rendelkeznie:
  * yyyy év
  * q Negyedév
  * m Hónap
  * y Az év napja
  * d Nap
  * w Hétköznap
  * ww Hét
  * h Óra
  * n Perc
  * s második
* érték: A hozzáadni kívánt egységek száma. Ez lehet pozitív (hogy dátumokat a jövőben) vagy negatív (hogy dátumokat a múltban).
* dátum: DateTime, amely az intervallum hozzáadásának dátumát jelöli.

**Példa:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
3 hónapot ad hozzá, és a "2001-04-01" értéket képviselő DateTime értéket ad vissza.

---
### <a name="datefromnum"></a>DateFromNum (Dátumfromnum)
**Leírás:**  
A DateFromNum függvény az AD dátumformátumában lévő értéket DateTime típussá alakítja.

**Szintaxis:**  
`dt DateFromNum(num value)`

**Példa:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
2012-01-01 23:00:00 dátumidőt ad eredményül.

---
### <a name="dncomponent"></a>DNösszetevő
**Leírás:**  
A DNComponent függvény egy megadott, balról haladó DN-összetevő értékét adja eredményül.

**Szintaxis:**  
`str DNComponent(ref dn, num ComponentNumber)`

* dn: a értelmezhető referenciaattribútum
* ComponentNumber: A DN visszaadandó összetevője

**Példa:**  
`DNComponent(CRef([dn]),1)`  
Ha a dn "cn=Joe,ou=...", akkor Joe-t ad vissza.

---
### <a name="dncomponentrev"></a>DNComponentRev
**Leírás:**  
A DNComponentRev függvény egy megadott DN-összetevő értékét adja vissza jobbról (a végből).

**Szintaxis:**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* dn: a értelmezhető referenciaattribútum
* ComponentNumber - A dn-ben visszaadandó összetevő
* Beállítások: DC – A "dc=" minden összetevő figyelmen kívül hagyása

**Példa:**  
Ha a dn "cn=Joe,ou=Atlanta,ou=GA,ou=US, dc=contoso,dc=com" akkor  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
Mindkettő visszatér MINKET.

---
### <a name="error"></a>Hiba
**Leírás:**  
A Hiba funkció egyéni hiba visszaadására szolgál.

**Szintaxis:**  
`void Error(str ErrorMessage)`

**Példa:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Ha a accountName attribútum nem található, jelenítsen meg egy hibát az objektumon.

---
### <a name="escapedncomponent"></a>EscapeDNComponent (EscapeDNComponent)
**Leírás:**  
Az EscapeDNComponent függvény a DN egy összetevőjét veszi fel, és elkerüli azt, hogy az LDAP-ban is képviselhető legyen.

**Szintaxis:**  
`str EscapeDNComponent(str value)`

**Példa:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Gondoskodik arról, hogy az objektum ldap-könyvtárban is létrehozható legyen, még akkor is, ha a displayName attribútum olyan karaktereket tartalmaz, amelyeket az LDAP-ban meg kell kerülni.

---
### <a name="formatdatetime"></a>FormatDateTime
**Leírás:**  
A FormatDateTime függvény a DateTime formátumú karakterláncra való formázására szolgál.

**Szintaxis:**  
`str FormatDateTime(dt value, str format)`

* érték: egy érték datetime formátumban
* formátum: a konvertálni kívánt formátumot jelölő karakterlánc.

**Megjegyzések:**  
A formátum lehetséges értékei itt találhatók: [A FORMAT függvény egyéni dátum- és időformátumai](https://docs.microsoft.com/dax/custom-date-and-time-formats-for-the-format-function).

**Példa:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
Eredmények a "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
"20140905081453.0Z"

---
### <a name="guid"></a>Guid
**Leírás:**  
A Guid függvény új véletlenszerű GUID-t hoz létre

**Szintaxis:**  
`str Guid()`

---
### <a name="iif"></a>Iif
**Leírás:**  
Az IIF függvény egy megadott feltételen alapuló lehetséges értékek egyikét adja vissza.

**Szintaxis:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* feltétel: bármely érték vagy kifejezés, amely igaz vagy hamis értékű.
* valueIfTrue: Ha a feltétel értéke igaz, a visszaadott érték.
* valueIfFalse: Ha a feltétel értéke hamis, a visszaadott érték.

**Példa:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Ha a felhasználó gyakornok, akkor egy olyan felhasználó aliasát adja vissza, akinek a elején "t-" van hozzáadva, máshol pedig a felhasználó aliasát adja vissza.

---
### <a name="instr"></a>Instr
**Leírás:**  
Az InStr függvény megkeresi egy karakterlánc részkarakterláncának első előfordulását

**Szintaxis:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* stringcheck: keresendő karakterlánc
* stringmatch: karakterlánc található
* start: kezdő pozíció a karakterlánc részhalmazának megkereséséhez
* összehasonlítás: vbTextCompare vagy vbBinaryCompare

**Megjegyzések:**  
Azt a pozíciót adja eredményül, ahol a karakterláncrész található, vagy a 0 értéket, ha nem.

**Példa:**  
`InStr("The quick brown fox","quick")`  
Eértékek értéke 5-re

`InStr("repEated","e",3,vbBinaryCompare)`  
Kiértékeljük a 7

---
### <a name="instrrev"></a>Instrrev között
**Leírás:**  
Az InStrRev függvény megkeresi egy karakterlánc részkarakterláncának utolsó előfordulását

**Szintaxis:**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: keresendő karakterlánc
* stringmatch: karakterlánc található
* start: kezdő pozíció a karakterlánc részhalmazának megkereséséhez
* összehasonlítás: vbTextCompare vagy vbBinaryCompare

**Megjegyzések:**  
Azt a pozíciót adja eredményül, ahol a karakterláncrész található, vagy a 0 értéket, ha nem.

**Példa:**  
`InStrRev("abbcdbbbef","bb")`  
7-et ad vissza

---
### <a name="isbitset"></a>IsBitSet
**Leírás:**  
Az IsBitSet tesztek funkció, ha egy bit be van állítva, vagy nem

**Szintaxis:**  
`bool IsBitSet(num value, num flag)`

* érték: kiértékelendő numerikus érték.jelző: olyan numerikus érték, amely a kiértékelendő bitet

**Példa:**  
`IsBitSet(&HF,4)`  
Igaz értéket ad vissza, mert a "4" bit az "F" hexadecimális értékben van beállítva.

---
### <a name="isdate"></a>IsDate (Dátum)
**Leírás:**  
Ha a kifejezés datetime típusként értékelhető, akkor az IsDate függvény értéke Igaz lesz.

**Szintaxis:**  
`bool IsDate(var Expression)`

**Megjegyzések:**  
Annak meghatározására szolgál, hogy a CDate() sikeres lehet-e.

---
### <a name="iscert"></a>IsCert között
**Leírás:**  
Igaz értéket ad vissza, ha a nyers adatok szerializálhatók a .NET X509Certificate2 tanúsítványobjektumba.

**Szintaxis:**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData: X.509-es tanúsítvány bájttömb-ábrázolása. A bájttömb lehet bináris (DER) kódolású vagy Base64 kódolású X.509-es adat.
---
### <a name="isempty"></a>IsEmpty
**Leírás:**  
Ha az attribútum jelen van a CS vagy MV, de értékeli egy üres karakterláncot, majd az IsEmpty függvény kiértékelt igaz.

**Szintaxis:**  
`bool IsEmpty(var Expression)`

---
### <a name="isguid"></a>IsGuid között
**Leírás:**  
Ha a karakterlánc guid azonosítóvá alakítható, akkor az IsGuid függvény igaz értékre értékelve.

**Szintaxis:**  
`bool IsGuid(str GUID)`

**Megjegyzések:**  
A GUID definíciója: karakterlánc, amely az alábbi minták valamelyikét követi: xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxxx vagy {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Annak meghatározására szolgál, hogy a CGuid() sikeres lehet-e.

**Példa:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Ha a StrAttribute guid formátumú, adja vissza a bináris ábrázolás, ellenkező esetben vissza a Null.

---
### <a name="isnull"></a>IsNull
**Leírás:**  
Ha a kifejezés eredménye Null, akkor az IsNull függvény igaz értéket ad vissza.

**Szintaxis:**  
`bool IsNull(var Expression)`

**Megjegyzések:**  
Egy attribútum esetében a Null értéket az attribútum hiánya fejezi ki.

**Példa:**  
`IsNull([displayName])`  
Igaz értéket ad vissza, ha az attribútum nem szerepel a CS vagy az MV értékben.

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**Leírás:**  
Ha a kifejezés null vagy üres karakterlánc, akkor az IsNullOrEmpty függvény igaz értéket ad vissza.

**Szintaxis:**  
`bool IsNullOrEmpty(var Expression)`

**Megjegyzések:**  
Egy attribútum esetében ez igaz értéket ad ki, ha az attribútum hiányzik, vagy jelen van, de üres karakterlánc.  
A függvény inverzének neve IsPresent.

**Példa:**  
`IsNullOrEmpty([displayName])`  
Igaz értéket ad vissza, ha az attribútum nincs jelen, vagy üres karakterlánc a CS-ben vagy az MV-ben.

---
### <a name="isnumeric"></a>IsNumeric
**Leírás:**  
Az IsNumeric függvény logikai értéket ad vissza, amely azt jelzi, hogy egy kifejezés számtípusként értékelhető-e.

**Szintaxis:**  
`bool IsNumeric(var Expression)`

**Megjegyzések:**  
Annak meghatározására szolgál, hogy a CNum() sikeres lehet-e a kifejezés elemzéséhez.

---
### <a name="isstring"></a>Karakterlánc
**Leírás:**  
Ha a kifejezés karakterlánctípusra értékelhető, akkor az IsString függvény értéke Igaz lesz.

**Szintaxis:**  
`bool IsString(var expression)`

**Megjegyzések:**  
Annak meghatározására szolgál, hogy a CStr() sikeres lehet-e a kifejezés elemzéséhez.

---
### <a name="ispresent"></a>IsPresent (Jelen van)
**Leírás:**  
Ha a kifejezés nem Null karakterláncot ad eredményül, akkor az IsPresent függvény igaz értéket ad vissza.

**Szintaxis:**  
`bool IsPresent(var expression)`

**Megjegyzések:**  
A függvény inverzneve IsNullOrEmpty.

**Példa:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Elem
**Leírás:**  
A Cikk függvény egy elemet ad vissza egy többértékű karakterláncból/attribútumból.

**Szintaxis:**  
`var Item(mvstr attribute, num index)`

* attribútum: többértékű attribútum
* index: index a többértékű karakterlánc egy eleméhez.

**Megjegyzések:**  
A Cikk függvény a Tartalmazza függvénnyel együtt hasznos, mivel az utóbbi függvény visszaadja az indexet a többértékű attribútum egy elemének.

Hibát okoz, ha az index kívül esik a határokon.

**Példa:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Az elsődleges e-mail címet adja vissza.

---
### <a name="itemornull"></a>ItemOrNull
**Leírás:**  
Az ItemOrNull függvény egy elemet ad vissza egy többértékű karakterláncból/attribútumból.

**Szintaxis:**  
`var ItemOrNull(mvstr attribute, num index)`

* attribútum: többértékű attribútum
* index: index a többértékű karakterlánc egy eleméhez.

**Megjegyzések:**  
Az ItemOrNull függvény a Tartalmazza függvénnyel együtt hasznos, mivel az utóbbi függvény visszaadja az indexet a többértékű attribútum egy elemének.

Ha az index kívül esik a határokon, akkor null értéket ad vissza.

---
### <a name="join"></a>Csatlakozás
**Leírás:**  
Az Illesztés függvény egy többértékű karakterláncot vesz fel, és egy egyértékű karakterláncot ad vissza, amelynek minden elem közé megadott elválasztó van beszúrva.

**Szintaxis:**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* attribútum: Az egyesítendő karakterláncokat tartalmazó többértékű attribútum.
* határoló: Bármely karakterlánc, amely a visszaadott karakterlánc részkarakterláncait választja el. Ha nincs megadva, a program a szóközkaraktert (" ") használja. Ha a Határoló érték nulla hosszúságú karakterlánc ("") vagy Semmi, a lista összes eleme elvan ágyazva, határolók nélkül.

**Megjegyzések**  
Az Illesztés és a Felosztás függvény ek között paritás van. A Join függvény karakterláncok tömbjét veszi fel, és egy határoló karakterlánc használatával csatlakozik hozzájuk, hogy egyetlen karakterláncot ad vissza. A Split függvény egy karakterláncot vesz fel, és elválasztja azt a határolójelnél, hogy karakterlánctömböt ad vissza. Azonban a legfontosabb különbség az, hogy a Join bármely határoló karakterláncgal összefűzheti a karakterláncokat, a Split csak egyetlen karakterhatárolóval tudja elválasztani a karakterláncokat.

**Példa:**  
`Join([proxyAddresses],",")`  
Visszatérhet: "SMTP:john.doe@contoso.comsmtp:jd@contoso.com, "

---
### <a name="lcase"></a>LCase
**Leírás:**  
Az LCase függvény a karakterlánc összes karakterét kisbetűssé alakítja.

**Szintaxis:**  
`str LCase(str value)`

**Példa:**  
`LCase("TeSt")`  
A "teszt" értéket adja eredményül.

---
### <a name="left"></a>Bal
**Leírás:**  
A Bal függvény megadott számú karaktert ad vissza a karakterlánc bal oldalán.

**Szintaxis:**  
`str Left(str string, num NumChars)`

* karakterlánc: az a karakterlánc, amelyből a karaktereket ad vissza
* NumChars: a karakterlánc elejétől (balra) visszaadandó karakterek számát azonosító szám

**Megjegyzések:**  
Karakterlánc első numChars karakterét tartalmazó karakterlánc:

* Ha numChars = 0, üres karakterláncot ad vissza.
* Ha a numChars < 0, adja vissza a bemeneti karakterláncot.
* Ha a karakterlánc null értékű, üres karakterláncot ad vissza.

Ha a karakterlánc kevesebb karaktert tartalmaz, mint a numChars paraméterben megadott szám, akkor a karakterlánccal (azaz az 1. paraméter összes karakterét tartalmazó) karakterláncot ad vissza.

**Példa:**  
`Left("John Doe", 3)`  
Visszaadja a "Joh".

---
### <a name="len"></a>Len
**Leírás:**  
A Len függvény egy karakterláncban lévő karakterek számát adja vissza.

**Szintaxis:**  
`num Len(str value)`

**Példa:**  
`Len("John Doe")`  
8-at ad vissza

---
### <a name="ltrim"></a>LTrim között
**Leírás:**  
Az LTrim függvény eltávolítja a kezdő fehér szóközöket a karakterláncból.

**Szintaxis:**  
`str LTrim(str value)`

**Példa:**  
`LTrim(" Test ")`  
"Teszt" értéket ad eredményül.

---
### <a name="mid"></a>Közepes
**Leírás:**  
A Középső függvény megadott számú karaktert ad vissza egy karakterlánc megadott pozíciójából.

**Szintaxis:**  
`str Mid(str string, num start, num NumChars)`

* karakterlánc: az a karakterlánc, amelyből a karaktereket ad vissza
* start: egy szám, amely azonosítja a kezdő pozíciót a karakterláncban, hogy visszaadja a karaktereket
* NumChars: a karakterláncban elfoglalt helyről visszaadandó karakterek számát azonosító szám

**Megjegyzések:**  
A pozícióból induló numChars karaktereket adja vissza a karakterláncban kezdődően.  
NumChars karaktereket tartalmazó karakterlánc a pozícióból karakterláncban kezdődik:

* Ha numChars = 0, üres karakterláncot ad vissza.
* Ha a numChars < 0, adja vissza a bemeneti karakterláncot.
* Ha a kezdő > a karakterlánc hosszát, adja vissza a bemeneti karakterláncot.
* Ha <= 0 indítása, adja vissza a bemeneti karakterláncot.
* Ha a karakterlánc null értékű, üres karakterláncot ad vissza.

Ha a pozíció kezdőhelyétől nem maradnak numChar karakterek a karakterláncban, a lehető legtöbb karaktert adja vissza a rendszer.

**Példa:**  
`Mid("John Doe", 3, 5)`  
Eredménye: "hn Do".

`Mid("John Doe", 6, 999)`  
"Doe" értéket ad eredményül.

---
### <a name="now"></a>Now
**Leírás:**  
A Most függvény egy DateTime értéket ad vissza, amely a számítógép rendszerdátumának és időpontjának megfelelően megadja az aktuális dátumot és időpontot.

**Szintaxis:**  
`dt Now()`

---
### <a name="numfromdate"></a>NumFromDate (Dátum kezdőszáma)
**Leírás:**  
A NumFromDate függvény az AD dátumformátumában adja vissza a dátumot.

**Szintaxis:**  
`num NumFromDate(dt value)`

**Példa:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Eredménye 129699324000000000000000000

---
### <a name="padleft"></a>PadBalra
**Leírás:**  
A PadLeft függvény egy megadott hosszúságú karakterláncot balra párnáz egy megadott kitöltési karakter használatával.

**Szintaxis:**  
`str PadLeft(str string, num length, str padCharacter)`

* string: a pad karakterlánc.
* hossz: Egész szám, amely a karakterlánc kívánt hosszát jelöli.
* padCharacter: A pad karakterként használandó egyetlen karakterből álló karakterlánc

**Megjegyzések:**

* Ha a karakterlánc hossza kisebb, mint a hossza, akkor a padCharacter a karakterlánc elejéhez (balra) többször hozzáfűzi, amíg a hossza meg nem egyezik.
* A PadCharacter szóközkarakter lehet, de nem lehet null érték.
* Ha a karakterlánc hossza egyenlő vagy annál nagyobb, a karakterlánc változatlanul lesz visszaadva.
* Ha a karakterlánc hossza hosszabb vagy egyenlő, akkor a karakterlánccal azonos karakterláncot ad vissza.
* Ha a karakterlánc hossza rövidebb, akkor a rendszer egy új, a kívánt hosszúságú karakterláncot ad vissza, amely egy padKarakterkarakterrel bélelt karakterláncot tartalmaz.
* Ha a karakterlánc null értékű, a függvény üres karakterláncot ad vissza.

**Példa:**  
`PadLeft("User", 10, "0")`  
Eredménye: "000000User".

---
### <a name="padright"></a>PadJobbra
**Leírás:**  
A PadRight függvény jobb oldali függvénye egy megadott hosszúságú karakterláncot egy megadott kitöltési karakter használatával.

**Szintaxis:**  
`str PadRight(str string, num length, str padCharacter)`

* string: a pad karakterlánc.
* hossz: Egész szám, amely a karakterlánc kívánt hosszát jelöli.
* padCharacter: A pad karakterként használandó egyetlen karakterből álló karakterlánc

**Megjegyzések:**

* Ha a karakterlánc hossza kisebb, mint a hossza, akkor a padCharacter a karakterlánc végéhez (jobbra) többször hozzáfűzi, amíg a hossza meg nem egyezik.
* A padCharacter szóközkarakter lehet, de nem lehet null érték.
* Ha a karakterlánc hossza egyenlő vagy annál nagyobb, a karakterlánc változatlanul lesz visszaadva.
* Ha a karakterlánc hossza hosszabb vagy egyenlő, akkor a karakterlánccal azonos karakterláncot ad vissza.
* Ha a karakterlánc hossza rövidebb, akkor a rendszer egy új, a kívánt hosszúságú karakterláncot ad vissza, amely egy padKarakterkarakterrel bélelt karakterláncot tartalmaz.
* Ha a karakterlánc null értékű, a függvény üres karakterláncot ad vissza.

**Példa:**  
`PadRight("User", 10, "0")`  
A "User000000" értéket adja vissza.

---
### <a name="pcase"></a>PCase
**Leírás:**  
A PCase függvény a karakterláncban lévő szóközök első karakterét nagybetűssé alakítja, a többi karaktert pedig kisbetűssé alakítja.

**Szintaxis:**  
`String PCase(string)`

**Megjegyzések:**

* Ez a funkció jelenleg nem biztosít megfelelő burkolatot egy teljesen nagybetűs szó, például egy betűszó konvertálásához.

**Példa:**  
`PCase("TEsT")`  
A "Teszt" értéket adja eredményül.

`PCase(LCase("TEST"))`  
"Teszt" értéket ad eredményül.

---
### <a name="randomnum"></a>Véletlen
**Leírás:**  
A RandomNum függvény egy megadott intervallum közötti véletlen számot ad eredményül.

**Szintaxis:**  
`num RandomNum(num start, num end)`

* start: egy szám, amely azonosítja a véletlenszerű érték alsó határát, hogy
* végén: egy szám, amely azonosítja a véletlenszerű érték felső határát, hogy

**Példa:**  
`Random(100,999)`  
Visszajöhet 734-re.

---
### <a name="removeduplicates"></a>Másolateltávolítása
**Leírás:**  
A RemoveDuplicates függvény egy többértékű karakterláncot vesz fel, és győződjön meg arról, hogy minden érték egyedi.

**Szintaxis:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Példa:**  
`RemoveDuplicates([proxyAddresses])`  
Egy fertőtlenített proxyAddress attribútumot ad vissza, amelyben az összes ismétlődő értéket eltávolították.

---
### <a name="replace"></a>Csere
**Leírás:**  
A Csere függvény egy karakterlánc összes előfordulását egy másik karakterláncra cseréli.

**Szintaxis:**  
`str Replace(str string, str OldValue, str NewValue)`

* karakterlánc: Értékeket lecserélő karakterlánc.
* OldValue: A kereséséshelyettesítő karakterlánc.
* NewValue: Az a karakterlánc, amelynek a karakterláncát le kell cserélni.

**Megjegyzések:**  
A függvény a következő speciális monikereket ismeri fel:

* \n – Új sor
* \r – Fuvarkocsi-visszaadás
* \t – Lap

**Példa:**  
`Replace([address],"\r\n",", ")`  
A CRLF helyére vesszőt és helyet helyez el, és ez a "One Microsoft Way, Redmond, WA, USA" kifejezéshez vezethet.

---
### <a name="replacechars"></a>Helyettesítő karakter
**Leírás:**  
A ReplaceChars függvény a ReplacePattern karakterláncban található karakterek összes előfordulását felülírja.

**Szintaxis:**  
`str ReplaceChars(str string, str ReplacePattern)`

* karakterlánc: Karaktereket lecserélő karakterlánc.
* ReplacePattern: szótárt tartalmazó karakterlánc, amelynek karaktereit le kell cserélni.

A formátum {source1}:{target1},{source2}:{target2},{sourceN},{targetN} ahol a forrás az a karakter, amelyet meg kell találni, és meg kell céloznia azt a karakterláncot, amelyet cserélni kell.

**Megjegyzések:**

* A függvény a definiált források minden előfordulását, és lecseréli őket a célokat.
* A forrásnak pontosan egy (unicode) karakternek kell lennie.
* A forrás nem lehet üres vagy egy karakternél hosszabb (elemzési hiba).
* A cél több karaktert is tartalmazhat, például ö:oe, β:ss.
* A cél üres lehet, jelezve, hogy a karaktert el kell távolítani.
* A forrás nak a kis- és nagybetűket kell figyelembe vennie, és pontosegyezésnek kell lennie.
* A , (vessző) és : (kettőspont) fenntartott karakterek, és ezzel a függvénnyel nem cserélhetők le.
* A ReplacePattern karakterlánc szóközeit és egyéb fehér karaktereit a rendszer figyelmen kívül hagyja.

**Példa:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Visszaadja a Raksmorgas értékét

`ReplaceChars("O’Neil",%ReplaceString%)`  
Az "ONeil" értéket adja vissza, az egyes kullancsot el kell távolítani.

---
### <a name="right"></a>Jobb
**Leírás:**  
A Jobb oldali függvény egy karakterlánc jobb oldaláról (végén) megadott számú karaktert ad eredményül.

**Szintaxis:**  
`str Right(str string, num NumChars)`

* karakterlánc: az a karakterlánc, amelyből a karaktereket ad vissza
* NumChars: a karakterlánc végéről (jobbra) visszaadandó karakterek számát azonosító szám

**Megjegyzések:**  
A NumChars karakterek et a karakterlánc utolsó pozíciójából adja vissza a rendszer.

Karakterlánc utolsó numChars karakterét tartalmazó karakterlánc:

* Ha numChars = 0, üres karakterláncot ad vissza.
* Ha a numChars < 0, adja vissza a bemeneti karakterláncot.
* Ha a karakterlánc null értékű, üres karakterláncot ad vissza.

Ha a karakterlánc kevesebb karaktert tartalmaz, mint a NumChars-ban megadott szám, akkor a karakterlánccal azonos karakterláncot ad vissza.

**Példa:**  
`Right("John Doe", 3)`  
Eredménye "Doe".

---
### <a name="rtrim"></a>RTrim között
**Leírás:**  
Az RTrim függvény eltávolítja a záró fehér szóközöket a karakterláncból.

**Szintaxis:**  
`str RTrim(str value)`

**Példa:**  
`RTrim(" Test ")`  
Eredmény: "Teszt".

---
### <a name="select"></a>Válassza ezt:
**Leírás:**  
Az összes érték feldolgozása egy többértékű attribútumban (vagy egy kifejezés kimenetében) a megadott függvény alapján.

**Szintaxis:**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* elem: A többértékű attribútum egy elemét jelöli
* attribútum: a többértékű attribútum
* kifejezés: értékgyűjteményt visszaadó kifejezés
* feltétel: minden olyan funkció, amely képes feldolgozni egy elemet az attribútumban

**Példák:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
Adja vissza az összes értéket a többértékű attribútum otherPhone után kötőjelek (-) eltávolították.

---
### <a name="split"></a>Felosztás
**Leírás:**  
A Split függvény egy határolóval elválasztott karakterláncot vesz fel, és többértékű karakterláncot tesz leendővé.

**Szintaxis:**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* érték: a elválasztó karakterrel rendelkező karakterlánc.
* határoló: egyetlen karakter, amelyet határolójelként kell használni.
* limit: a visszaadható értékek maximális száma.

**Példa:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Egy többértékű karakterláncot ad vissza, amelynek 2 eleme hasznos a proxyAddress attribútumhoz.

---
### <a name="stringfromguid"></a>StringFromGuid
**Leírás:**  
A StringFromGuid függvény bináris GUID azonosítót vesz fel, és karakterláncgá alakítja

**Szintaxis:**  
`str StringFromGuid(bin GUID)`

---
### <a name="stringfromsid"></a>Karakterláncszisid
**Leírás:**  
A StringFromSid függvény egy biztonsági azonosítót tartalmazó bájttömböt konvertál karakterláncsá.

**Szintaxis:**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="switch"></a>Kapcsoló
**Leírás:**  
A Switch függvény egyetlen értéket ad vissza a kiértékelt feltételek alapján.

**Szintaxis:**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* kifejezés: A kiértékelni kívánt variant kifejezés.
* érték: Visszaadandó érték, ha a megfelelő kifejezés Igaz.

**Megjegyzések:**  
A Kapcsoló függvény argumentumlistája kifejezések és értékek párjaiból áll. A kifejezések kiértékelése balról jobbra történik, és az első kiértékelendő kifejezéshez tartozó értéket a d. Ha az alkatrészek nincsenek megfelelően párosítva, futásidejű hiba lép fel.

Ha például a Kif1 értéke Igaz, akkor a Switch függvény az értéket1 adja vissza. Ha az expr-1 értéke Hamis, de az expr-2 értéke Igaz, akkor a Switch értéke 2, és így tovább.

A Kapcsoló a Semmi értéket adja vissza, ha:

* Egyik kifejezés sem igaz.
* Az első Igaz kifejezés megfelelő értéke Null.

A Switch kiértékeli az összes kifejezést, még akkor is, ha csak az egyiket adja vissza. Emiatt meg kell nézni a nemkívánatos mellékhatásokat. Ha például bármely kifejezés kiértékelése nulla hibával történő osztást eredményez, hiba történik.

Az érték lehet az Error függvény is, amely egyéni karakterláncot ad vissza.

**Példa:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Egyes nagyobb városokban beszélt nyelvet ad eredményül, ellenkező esetben hibát ad vissza.

---
### <a name="trim"></a>Trim
**Leírás:**  
A Vágás funkció eltávolítja a kezdő és záró fehér szóközöket a karakterláncból.

**Szintaxis:**  
`str Trim(str value)`  

**Példa:**  
`Trim(" Test ")`  
A "Teszt" értéket adja eredményül.

`Trim([proxyAddresses])`  
Eltávolítja a proxyAddress attribútum egyes értékeinek kezdő és záró szóközeit.

---
### <a name="ucase"></a>Ucase
**Leírás:**  
A UCase függvény a karakterlánc összes karakterét nagybetűssé alakítja.

**Szintaxis:**  
`str UCase(str string)`

**Példa:**  
`UCase("TeSt")`  
Eredményül "TESZT".

---
### <a name="where"></a>Ahol

**Leírás:**  
Értékek egy részét adja vissza egy többértékű attribútumból (vagy egy kifejezés kimenetéből) egy adott feltétel alapján.

**Szintaxis:**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* elem: A többértékű attribútum egy elemét jelöli
* attribútum: a többértékű attribútum
* feltétel: bármely kifejezés, amely igaz vagy hamis
* kifejezés: értékgyűjteményt visszaadó kifejezés

**Példa:**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
Adja vissza a tanúsítvány értékeit a többértékű attribútum userCertificate, amelyek nem jártak le.

---
### <a name="with"></a>With
**Leírás:**  
A With függvény lehetővé teszi az összetett kifejezések egyszerűsítését egy változó használatával egy olyan alkifejezés ábrázolására, amely egy vagy több alkalommal jelenik meg az összetett kifejezésben.

**Szintaxis:**
`With(var variable, exp subExpression, exp complexExpression)`  
* változó: Az alkifejezést jelöli.
* alkifejezés: változóval ábrázolt alkifejezés.
* complexExpression: Összetett kifejezés.

**Példa:**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
Funkcionálisan egyenértékű a következőkkal:  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
Amely csak a userCertificate attribútumban le nem járt tanúsítványértékeket ad vissza.


---
### <a name="word"></a>Word
**Leírás:**  
A Word függvény egy karakterláncban lévő szót ad vissza a használandó határolókat és a visszaadandó szószámot leíró paraméterek alapján.

**Szintaxis:**  
`str Word(str string, num WordNumber, str delimiters)`

* karakterlánc: az a karakterlánc, amelyből egy szót vissza kell adni.
* WordNumber: egy szám, amely meghatározza, hogy melyik szószámot kell visszaadni.
* határolók: a határolójel(ek)et jelölő karakterlánc, amelyet a szavak azonosítására kell használni

**Megjegyzések:**  
A határolójelek ben lévő karakterek egyikével elválasztott karakterlánc-karakterláncokat a következő szavakként azonosítják:

* Ha a szám < 1, üres karakterláncot ad vissza.
* Ha a karakterlánc null értékű, üres karakterláncot ad vissza.

Ha a karakterlánc kevesebb, mint számszavakat tartalmaz, vagy a karakterlánc nem tartalmaz határolószavakat, a program üres karakterláncot ad vissza.

**Példa:**  
`Word("The quick brown fox",3," ")`  
"Barna" értéket ad eredményül.

`Word("This,string!has&many separators",3,",!&#")`  
Visszatérne "van"

## <a name="additional-resources"></a>További források
* [A deklaratív üzembe helyezési kifejezések ismertetése](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Azure AD Connect Sync: Szinkronizálási beállítások testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
