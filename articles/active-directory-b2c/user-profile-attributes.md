---
title: Felhasználói profil attribútumai a Azure Active Directory B2C
description: A Azure AD B2C Directory felhasználói profil által támogatott felhasználói erőforrástípus attribútumainak megismerése. Ismerje meg a beépített attribútumokat és bővítményeket, valamint azt, hogy az attribútumok hogyan Microsoft Graph.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e921f0a40f53b1d08831047d1cb89ca26de41402
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80057287"
---
# <a name="user-profile-attributes"></a>Felhasználói profilattribútumok

A Azure Active Directory (Azure AD) B2C címtár felhasználói profilja egy beépített attribútummal rendelkezik, például a név, a vezetéknév, a város, az irányítószám és a telefonszám számára. A felhasználói profilt külső adattár nélkül is kiterjesztheti saját alkalmazásadatok használatával. Microsoft Graph a Azure AD B2C felhasználói profilokkal használható attribútumok többségét is támogatja. Ez a cikk a felhasználói profilok támogatott Azure AD B2C attribútumait ismerteti. Emellett azokat az attribútumokat is megjegyzi, amelyeket a Microsoft Graph nem támogat, valamint olyan Microsoft Graph attribútumokat, amelyeket nem kíván használni a Azure AD B2C.

> [!IMPORTANT]
> Ne használjon beépített vagy kiterjesztési attribútumokat a bizalmas személyes adatok, például a fiók hitelesítő adatainak, a kormányzati azonosító számok, a kártyatulajdonos-adatok, a pénzügyi fiókadatok, az egészségügyi információk vagy a bizalmas háttér-információk tárolásához.

A külső rendszerekkel is integrálható. Használhatja például a Azure AD B2Ct a hitelesítéshez, de a delegálást egy külső ügyfélkapcsolat-kezelési (CRM) vagy ügyfél-törzsvásárlói adatbázisra, amely az ügyféladatok mérvadó forrása. További információt a [távoli profil](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) megoldásában talál.

Az alábbi táblázat az Azure AD B2C Directory felhasználói profil által támogatott [felhasználói erőforrástípus](https://docs.microsoft.com/graph/api/resources/user) attribútumokat sorolja fel. A következő információkat adja meg az egyes attribútumokról:

- Az Azure AD B2C által használt attribútum neve (amelyet a Microsoft Graph neve zárójelben, ha más)
- Attribútum adattípusa
- Attribútum leírása
- Ha az attribútum elérhető a Azure Portal
- Ha az attribútum felhasználói folyamatokban is használható
- Ha az attribútum az [Azure ad egy adott technikai profiljában](active-directory-technical-profile.md) &lt;és a (szabályzattípushoz&gt;, &lt;OutputClaims&gt;vagy &lt;PersistedClaims&gt;) szakaszban is használható.

|Name (Név)     |Típus     |Leírás|Azure Portal|Felhasználói folyamatok|Egyéni szabályzat|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Logikai|Azt jelzi, hogy a felhasználói fiók engedélyezve van vagy le van tiltva: **igaz** , ha a fiók engedélyezve van, ellenkező esetben **hamis**.|Igen|Nem|Megőrzött, kimenet|
|korcsoport        |Sztring|A felhasználó korcsoport. Lehetséges értékek: NULL, nem definiált, kisebb, felnőtt, NotAdult.|Igen|Nem|Megőrzött, kimenet|
|alternativeSecurityId ([identitások](manage-user-accounts-graph-api.md#identities-property))|Sztring|A külső identitás szolgáltatójának egyetlen felhasználói identitása.|Nem|Nem|Bemenet, megőrzött, kimenet|
|alternativeSecurityIds ([identitások](manage-user-accounts-graph-api.md#identities-property))|alternatív securityId-gyűjtemény|A külső identitás-szolgáltatók felhasználói identitásának gyűjteménye.|Nem|Nem|Megőrzött, kimenet|
|city            |Sztring|A város, amelyben a felhasználó található. Maximális hosszúság 128.|Igen|Igen|Megőrzött, kimenet|
|consentProvidedForMinor|Sztring|Azt határozza meg, hogy a beleegyezik-e a kiskorú számára. Megengedett értékek: NULL, engedélyezett, megtagadva vagy notRequired.|Igen|Nem|Megőrzött, kimenet|
|ország         |Sztring|Az ország/régió, amelyben a felhasználó található. Példa: "US" vagy "UK". Maximális hosszúság 128.|Igen|Igen|Megőrzött, kimenet|
|createdDateTime|DateTime|A felhasználói objektum létrehozásának dátuma. Csak olvasható.|Nem|Nem|Megőrzött, kimenet|
|creationType    |Sztring|Ha a felhasználói fiók helyi fiókként lett létrehozva egy Azure Active Directory B2C bérlő számára, akkor az érték a LocalAccount vagy a nameCoexistence. Csak olvasható.|Nem|Nem|Megőrzött, kimenet|
|dateOfBirth     |Dátum|születési dátum.|Nem|Nem|Megőrzött, kimenet|
|Részleg      |Sztring|Annak a részlegnek a neve, amelyben a felhasználó működik. Maximális hosszúság 64.|Igen|Nem|Megőrzött, kimenet|
|displayName     |Sztring|A felhasználó megjelenítendő neve. Maximális hosszúság 256.|Igen|Igen|Megőrzött, kimenet|
|<sup>1</sup> . érték facsimiletelephonenumber|Sztring|A felhasználó üzleti faxhoz tartozó telefonszám.|Igen|Nem|Megőrzött, kimenet|
|givenName       |Sztring|A felhasználó megadott neve (keresztnév). Maximális hosszúság 64.|Igen|Igen|Megőrzött, kimenet|
|Beosztás        |Sztring|A felhasználó beosztása. Maximális hosszúság 128.|Igen|Igen|Megőrzött, kimenet|
|immutableId     |Sztring|A helyszíni Active Directoryból áttelepített felhasználók számára általában használt azonosító.|Nem|Nem|Megőrzött, kimenet|
|legalAgeGroupClassification|Sztring|Jogi korcsoport besorolása. Írásvédett, és a Beszerzésimennyiség és a consentProvidedForMinor tulajdonságok alapján számítható ki. Megengedett értékek: NULL, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult és Adult.|Igen|Nem|Megőrzött, kimenet|
|<sup>1</sup> . legalCountry  |Sztring|Ország jogi célokra.|Nem|Nem|Megőrzött, kimenet|
|Levelezés            |Sztring|A felhasználó SMTP-címe, például: "bob@contoso.com". Csak olvasható.|Nem|Nem|Megőrzött, kimenet|
|mailNickName    |Sztring|A felhasználó levelezési aliasa. Maximális hosszúság 64.|Nem|Nem|Megőrzött, kimenet|
|mobil (mobiltelefon) |Sztring|A felhasználó elsődleges mobil telefonszáma. Maximális hosszúság 64.|Igen|Nem|Megőrzött, kimenet|
|a netI           |Sztring|NET-azonosító.|Nem|Nem|Megőrzött, kimenet|
|objectId        |Sztring|Globálisan egyedi azonosító (GUID), amely a felhasználó egyedi azonosítója. Példa: 12345678-9ABC-def0-1234-56789abcde. Csak olvasható, nem módosítható.|Csak olvasható|Igen|Bemenet, megőrzött, kimenet|
|otherMails      |Karakterlánc-gyűjtemény|A felhasználóhoz tartozó további e-mail-címek listája. Példa: ["bob@contoso.com", "Robert@fabrikam.com"].|Igen (másodlagos e-mail)|Nem|Megőrzött, kimenet|
|jelszó        |Sztring|A helyi fiók jelszava a felhasználó létrehozásakor.|Nem|Nem|Kitartott|
|passwordPolicies     |Sztring|A jelszó szabályzata. Ez egy olyan karakterlánc, amely különböző nevű nevet tartalmaz vesszővel elválasztva. azaz "DisablePasswordExpiration, DisableStrongPassword".|Nem|Nem|Megőrzött, kimenet|
|physicalDeliveryOfficeName (officeLocation)|Sztring|Az iroda helye a felhasználó üzleti helyén. Maximális hosszúság 128.|Igen|Nem|Megőrzött, kimenet|
|Irányítószám      |Sztring|A felhasználó postai levélcímének postai irányítószáma. A postai irányítószám a felhasználó országára/régiójára vonatkozik. Az Amerikai Egyesült Államokban ez az attribútum tartalmazza a ZIP-kódot. Maximális hosszúság 40.|Igen|Nem|Megőrzött, kimenet|
|preferredLanguage    |Sztring|A felhasználó előnyben részesített nyelve. Az ISO 639-1 kódot kell követnie. Példa: "en-US".|Nem|Nem|Megőrzött, kimenet|
|refreshTokensValidFromDateTime|DateTime|A jelen időpont előtt kiadott frissítési tokenek érvénytelenek, és az alkalmazások hibaüzenetet kapnak, amikor érvénytelen frissítési tokent használnak új hozzáférési jogkivonat beszerzéséhez. Ha ez történik, az alkalmazásnak új frissítési jogkivonatot kell bekérnie egy kérelemnek az engedélyezés végpontba való beszerzésével. Csak olvasható.|Nem|Nem|Kimenet|
|signInNames ([identitások](manage-user-accounts-graph-api.md#identities-property)) |Sztring|A helyi fiók felhasználójának egyedi bejelentkezési neve a címtárban. Ezzel a beállítással a bejelentkezési értékkel rendelkező felhasználókat a helyi fiók típusának megadása nélkül kérheti le.|Nem|Nem|Input (Bemenet)|
|signInNames. userName ([identitások](manage-user-accounts-graph-api.md#identities-property)) |Sztring|A helyi fiók felhasználójának egyedi felhasználóneve a címtárban. Ezzel a paranccsal létrehozhat vagy beszerezhet egy adott bejelentkezési felhasználónévvel rendelkező felhasználót. Ha ezt a PersistedClaims a javítási művelet során egyedül adja meg, a más típusú signInNames is törlődik. Ha új típusú signInNames szeretne hozzáadni, akkor is meg kell őriznie a meglévő signInNames.|Nem|Nem|Bemenet, megőrzött, kimenet|
|signInNames. telefonszám ([identitások](manage-user-accounts-graph-api.md#identities-property)) |Sztring|A helyi fiók felhasználójának egyedi telefonszáma a címtárban. Ezzel a paranccsal létrehozhat vagy beszerezhet egy adott bejelentkezési telefonszámmal rendelkező felhasználót. Ha ezt a PersistedClaims a javítási művelet során egyedül adja meg, a más típusú signInNames is törlődik. Ha új típusú signInNames szeretne hozzáadni, akkor is meg kell őriznie a meglévő signInNames.|Nem|Nem|Bemenet, megőrzött, kimenet|
|signInNames. emailAddress ([identitások](manage-user-accounts-graph-api.md#identities-property))|Sztring|A címtárban lévő helyi fiók felhasználójának egyedi e-mail-címe. Ezzel a paranccsal létrehozhat vagy beszerezhet egy adott bejelentkezési e-mail-címmel rendelkező felhasználót. Ha ezt a PersistedClaims a javítási művelet során egyedül adja meg, a más típusú signInNames is törlődik. Ha új típusú signInNames szeretne hozzáadni, akkor is meg kell őriznie a meglévő signInNames.|Nem|Nem|Bemenet, megőrzött, kimenet|
|state           |Sztring|A felhasználó címe szerinti állam vagy tartomány. Maximális hosszúság 128.|Igen|Igen|Megőrzött, kimenet|
|streetAddress   |Sztring|A felhasználó üzletviteli helyének címe. Maximális hosszúság 1024.|Igen|Igen|Megőrzött, kimenet|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|Sztring|A felhasználó másodlagos telefonszáma, amely a többtényezős hitelesítéshez használatos.|Igen|Nem|Megőrzött, kimenet|
|<sup>1</sup> . strongAuthenticationEmailAddress|Sztring|A felhasználó SMTP-címe. Példa: "bob@contoso.com" Ez az attribútum a Felhasználónév-szabályzattal való bejelentkezéshez használatos a felhasználói e-mail-cím tárolásához. Az e-mail-cím, amelyet a rendszer a jelszó-visszaállítási folyamat során használ.|Igen|Nem|Megőrzött, kimenet|
|<sup>1</sup> . strongAuthenticationPhoneNumber|Sztring|A felhasználó elsődleges telefonszáma, amely a többtényezős hitelesítéshez használatos.|Igen|Nem|Megőrzött, kimenet|
|surname         |Sztring|A felhasználó vezetékneve (családi név vagy vezetéknév). Maximális hosszúság 64.|Igen|Igen|Megőrzött, kimenet|
|telephoneNumber (businessPhones első bejegyzése)|Sztring|A felhasználó üzleti helyének elsődleges telefonszáma.|Igen|Nem|Megőrzött, kimenet|
|userPrincipalName    |Sztring|A felhasználó egyszerű felhasználóneve (UPN). Az UPN a felhasználó Internet-stílusú bejelentkezési neve, amely az Internet standard RFC 822. A tartománynak jelen kell lennie a bérlő ellenőrzött tartományok gyűjteményében. Ez a tulajdonság a fiók létrehozásakor szükséges. Nem módosítható.|Nem|Nem|Bemenet, megőrzött, kimenet|
|usageLocation   |Sztring|Olyan felhasználók számára szükséges, akik licenceket kapnak, mert az országok szolgáltatásainak rendelkezésre állásának ellenőrzését jogi követelmény okozta. Nem üres. Két betűs országkód (ISO standard 3166). Példák: "US", "JP" és "GB".|Igen|Nem|Megőrzött, kimenet|
|userType        |Sztring|Karakterlánc-érték, amely a címtárban lévő felhasználói típusok besorolására használható. Az értéknek tagnak kell lennie. Csak olvasható.|Csak olvasható|Nem|Megőrzött, kimenet|
|userState (externalUserState)<sup>2</sup>|Sztring|Csak az Azure AD B2B-fiók esetében jelzi, hogy a meghívás PendingAcceptance vagy elfogadva van-e.|Nem|Nem|Megőrzött, kimenet|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|DateTime|Megjeleníti a UserState tulajdonság legutóbbi módosításának időbélyegét.|Nem|Nem|Megőrzött, kimenet|
|<sup>1</sup> Microsoft Graph nem támogatja<br><sup>2</sup> Nem használható a Azure AD B2C||||||


## <a name="extension-attributes"></a>Bővítmény attribútumai

A következő esetekben gyakran létre kell hoznia a saját attribútumait:

- Egy ügyfél felé irányuló alkalmazásnak meg kell őriznie egy olyan attribútumot, mint a **LoyaltyNumber**.
- Az identitás-szolgáltató egyedi felhasználói azonosítóval rendelkezik, például **uniqueUserGUID** , amelyet menteni kell.
- Egy egyéni felhasználói útra van szükség egy felhasználó állapotára, például a **migrationStatus**.

Azure AD B2C kiterjeszti az egyes felhasználói fiókokban tárolt attribútumok készletét. A bővítmény attribútumai [kiterjesztik](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions) a címtárban lévő felhasználói objektumok sémáját. A bővítmény attribútumai csak egy alkalmazás-objektumon regisztrálhatók, annak ellenére, hogy egy felhasználóhoz tartozó adatmennyiséget tartalmaznak. A bővítmény attribútum a B2C-Extensions-app nevű alkalmazáshoz van csatolva. Ne módosítsa ezt az alkalmazást, mert az Azure AD B2C használja a felhasználói adattároláshoz. Az alkalmazás az Azure Active Directory Alkalmazásregisztrációk alatt található.

> [!NOTE]
> - Akár 100-es kiterjesztési attribútum is írható bármely felhasználói fiókba.
> - Ha a B2C-Extensions-App alkalmazás törölve lett, a rendszer eltávolítja ezeket a bővítmény-attribútumokat az összes felhasználótól a bennük található adatokkal együtt.
> - Ha az alkalmazás töröl egy Extension attribútumot, a rendszer eltávolítja az összes felhasználói fiókból, és törli az értékeket.
> - A kiterjesztés attribútum mögöttes neve "Extension_" + Application ID + "_" + attribútum néven jön létre. Ha például LoyaltyNumber hoz létre, és a B2C-Extensions-app Application ID értéke 831374b3-bd50-41bf-aa54-263ec9e050fc, a mögöttes kiterjesztés attribútum neve a következő lesz: extension_831374b3bd5041bfaa54263ec9e050fc_LoyaltyNumber. Az alapul szolgáló nevet a felhasználói fiókok létrehozásához vagy frissítéséhez Graph API lekérdezések futtatásakor használhatja.

A következő adattípusok támogatottak, amikor definiál egy tulajdonságot a séma bővítményeiben:

|Tulajdonság típusa |Megjegyzések  |
|--------------|---------|
|Logikai    | Lehetséges értékek: **true** vagy **false**. |
|DateTime   | ISO 8601 formátumban kell megadni. A rendszer UTC formátumban tárolja.   |
|Egész szám    | 32 bites érték.               |
|Sztring     | 256 karakter maximális száma.     |

## <a name="next-steps"></a>További lépések
További információ a bővítmény attribútumairól:
- [Sémakiterjesztések](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)
- [Egyéni attribútumok definiálása felhasználói folyamattal](user-flow-custom-attributes.md)
- [Egyéni attribútumok definiálása egyéni házirenddel](custom-policy-custom-attributes.md)
