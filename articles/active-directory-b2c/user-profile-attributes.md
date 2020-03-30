---
title: Felhasználói profil attribútumai az Azure Active Directory B2C-ben
description: Ismerje meg az Azure AD B2C címtárfelhasználói profilja által támogatott felhasználói erőforrástípus-attribútumokat. Ismerje meg a beépített attribútumokat, bővítményeket és azt, hogy az attribútumok hogyan felelnek meg a Microsoft Graph-nak.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057287"
---
# <a name="user-profile-attributes"></a>Felhasználói profil attribútumai

Az Azure Active Directory (Azure AD) B2C címtár felhasználói profilja egy beépített attribútumkészlettel érkezik, például a megadott név, a vezetéknév, a város, az irányítószám és a telefonszám. A felhasználói profilt kiterjesztheti a saját alkalmazásadataival külső adattár nélkül. Az Azure AD B2C felhasználói profilokhoz használható attribútumok többségét a Microsoft Graph is támogatja. Ez a cikk ismerteti a támogatott Azure AD B2C felhasználói profil attribútumok. Azt is megjegyzi, hogy a Microsoft Graph által nem támogatott attribútumok, valamint az Azure AD B2C-vel nem használható Microsoft Graph-attribútumok.

> [!IMPORTANT]
> Ne használjon beépített vagy bővítményattribútumokat bizalmas személyes adatok tárolására, például fiókhitelesítő adatok, kormányzati azonosító számok, kártyatulajdonosi adatok, pénzügyi számlaadatok, egészségügyi információk vagy bizalmas háttéradatok tárolására.

Külső rendszerekkel is integrálható. Használhatja például az Azure AD B2C-t a hitelesítéshez, de az ügyféladatok mérvadó forrásaként delegálhat egy külső ügyfélkapcsolat-kezelési (CRM) vagy ügyfélhűség-adatbázisra. További információt a [távoli profilmegoldásban](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) talál.

Az alábbi táblázat felsorolja az Azure AD B2C címtár felhasználói profilja által támogatott [felhasználói erőforrástípus-attribútumokat.](https://docs.microsoft.com/graph/api/resources/user) A következő információkat adja az egyes attribútumokról:

- Az Azure AD B2C által használt attribútumnév (amelyet zárójelben a Microsoft Graph név követ, ha más)
- Attribútumadattípusa
- Attribútum leírása
- Ha az attribútum elérhető az Azure Portalon
- Ha az attribútum használható egy felhasználói folyamatban
- Ha az attribútum használható egy egyéni szabályzat [Ban Azure AD technikai profil,](active-directory-technical-profile.md) &gt;és &lt;amely szakaszban (&lt;InputClaims&gt;, &lt;OutputClaims , vagy PersistedClaims&gt;)

|Név     |Típus     |Leírás|Azure portál|Felhasználói folyamatok|Egyéni szabályzat|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Logikai|Azt jelzi, hogy a felhasználói fiók engedélyezve van-e vagy le van tiltva: **igaz,** ha a fiók engedélyezve van, egyébként **hamis**.|Igen|Nem|Tartós, Kimenet|
|korcsoport        |Sztring|A felhasználó korcsoportja. Lehetséges értékek: null, Undefined, Minor, Adult, NotAdult.|Igen|Nem|Tartós, Kimenet|
|alternativeSecurityId ([Identitások](manage-user-accounts-graph-api.md#identities-property))|Sztring|Egyetlen felhasználói identitás a külső identitásszolgáltatótól.|Nem|Nem|Bemenet, Tartós, Kimenet|
|alternativeSecurityIds ([Identitások](manage-user-accounts-graph-api.md#identities-property))|alternatív securityId gyűjtemény|Külső identitásszolgáltatók felhasználói identitásainak gyűjteménye.|Nem|Nem|Tartós, Kimenet|
|city            |Sztring|Az a város, amelyben a felhasználó található. Max hossz 128.|Igen|Igen|Tartós, Kimenet|
|consentProvidedForMinor|Sztring|Azt jelzi, hogy a hozzájárulást kiskorú számára adták-e meg. Engedélyezett értékek: null, megadott, megtagadva vagy nemSzükséges.|Igen|Nem|Tartós, Kimenet|
|ország         |Sztring|Az az ország/régió, amelyben a felhasználó található. Példa: "US" vagy "UK". Max hossz 128.|Igen|Igen|Tartós, Kimenet|
|createdDateTime|DateTime|A felhasználói objektum létrehozásának dátuma. Csak olvasható.|Nem|Nem|Tartós, Kimenet|
|creationType    |Sztring|Ha a felhasználói fiók egy Azure Active Directory B2C-bérlő helyi fiókjaként lett létrehozva, az érték LocalAccount vagy nameCoexistence. Csak olvasható.|Nem|Nem|Tartós, Kimenet|
|dateOfBirth     |Dátum|születési dátum.|Nem|Nem|Tartós, Kimenet|
|Részleg      |Sztring|Annak a részlegnek a neve, amelyben a felhasználó dolgozik. Max hossz 64.|Igen|Nem|Tartós, Kimenet|
|displayName     |Sztring|A felhasználó megjelenítendő neve. Max hossz 256.|Igen|Igen|Tartós, Kimenet|
|faxtelefon1-es<sup>1</sup> szám|Sztring|A felhasználó üzleti faxkészülékének telefonszáma.|Igen|Nem|Tartós, Kimenet|
|givenName       |Sztring|A felhasználó utóneve (utóneve). Max hossz 64.|Igen|Igen|Tartós, Kimenet|
|jobTitle        |Sztring|A felhasználó beosztása. Max hossz 128.|Igen|Igen|Tartós, Kimenet|
|nem módosíthatóAzonosító     |Sztring|Olyan azonosító, amelyet általában a helyszíni Active Directoryból áttelepített felhasználók számára használnak.|Nem|Nem|Tartós, Kimenet|
|legalAgeGroupClassification|Sztring|Jogi korcsoport besorolása. Írásvédett, és az ageGroup és consentProvidedForMinor tulajdonságok alapján számítva. Engedélyezett értékek: null, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult and adult.|Igen|Nem|Tartós, Kimenet|
|legalCountry<sup>1</sup>  |Sztring|jogi célból.|Nem|Nem|Tartós, Kimenet|
|Levelezés            |Sztring|A felhasználó SMTP-címe, példáulbob@contoso.com" ". Csak olvasható.|Nem|Nem|Tartós, Kimenet|
|mailNickName    |Sztring|A felhasználó e-mail aliasa. Max hossz 64.|Nem|Nem|Tartós, Kimenet|
|mobil (mobilePhone) |Sztring|A felhasználó elsődleges mobiltelefonszáma. Max hossz 64.|Igen|Nem|Tartós, Kimenet|
|netId           |Sztring|Nettó azonosító.|Nem|Nem|Tartós, Kimenet|
|objectId        |Sztring|Globálisan egyedi azonosító (GUID), amely a felhasználó egyedi azonosítója. Példa: 12345678-9abc-def0-1234-56789abcde. Csak olvasható, megváltoztathatatlan.|Csak olvasható|Igen|Bemenet, Tartós, Kimenet|
|egyéb E-mailek      |Karakterlánc-gyűjtemény|A felhasználó további e-mail címeinek listája. Példa: ["bob@contoso.com", "Robert@fabrikam.com"].|Igen (másodlagos e-mail)|Nem|Tartós, Kimenet|
|jelszó        |Sztring|A helyi fiók jelszava a felhasználó létrehozása során.|Nem|Nem|Kitartott|
|passwordPolicies     |Sztring|A jelszó házirendje. Ez egy karakterlánc, amely különböző házirendnévből áll, vesszővel elválasztva. azaz "DisablePasswordExpiration, DisableStrongPassword".|Nem|Nem|Tartós, Kimenet|
|physicalDeliveryOfficeName (officeLocation)|Sztring|Az iroda helye a felhasználó telephelyén. Max hossz 128.|Igen|Nem|Tartós, Kimenet|
|postai kód      |Sztring|A felhasználó postai címének irányítószáma. Az irányítószám a felhasználó országára/régiójára vonatkozik. Az Amerikai Egyesült Államokban ez az attribútum tartalmazza az irányítószámot. Max hossz 40.|Igen|Nem|Tartós, Kimenet|
|preferredLanguage (elsődleges nyelv)    |Sztring|A felhasználó előnyben részesített nyelve. Az ISO 639-1 kódot kell követnie. Példa: "en-US".|Nem|Nem|Tartós, Kimenet|
|refreshTokensValidFromDateTime|DateTime|Az ezen időpont előtt kiadott frissítési jogkivonatok érvénytelenek, és az alkalmazások hibaüzenetet kapnak, ha egy érvénytelen frissítési jogkivonatot használ egy új hozzáférési jogkivonat beszerzéséhez. Ha ez történik, az alkalmazás nak be kell szereznie egy új frissítési jogkivonatot az engedélyezett végpontra vonatkozó kéréssel. Csak olvasható.|Nem|Nem|Kimenet|
|signInNames ([Identitások](manage-user-accounts-graph-api.md#identities-property)) |Sztring|A címtárban bármilyen típusú helyi fiókfelhasználó egyedi bejelentkezési neve. Ezzel a helyi fióktípus megadása nélkül kaphat be bejelentkezési értékű felhasználót.|Nem|Nem|Input (Bemenet)|
|signInNames.userName ([identitások](manage-user-accounts-graph-api.md#identities-property)) |Sztring|A címtárban lévő helyi fiókfelhasználó egyedi felhasználóneve. Ezzel hozhat létre vagy kaphat le egy adott bejelentkezési felhasználónevet használó felhasználót. Ha ezt csak a PersistedClaims-ben adja meg a javítás művelet során, akkor a signInNames más típusait is eltávolítja. Ha új típusú signInNames-et szeretne hozzáadni, meg kell őriznie a meglévő signInNames értéket is.|Nem|Nem|Bemenet, Tartós, Kimenet|
|signInNames.phoneNumber ([Identitások](manage-user-accounts-graph-api.md#identities-property)) |Sztring|A címtárban lévő helyi fiókfelhasználó egyedi telefonszáma. Ezzel hozhat létre vagy kaphat le egy adott bejelentkezési telefonszámot használó felhasználót. Ha ezt csak a PersistedClaims-ben adja meg a javítás művelet során, akkor a signInNames más típusait is eltávolítja. Ha új típusú signInNames-et szeretne hozzáadni, meg kell őriznie a meglévő signInNames értéket is.|Nem|Nem|Bemenet, Tartós, Kimenet|
|signInNames.emailCím ([Identitások](manage-user-accounts-graph-api.md#identities-property))|Sztring|A címtárban lévő helyi fiókfelhasználó egyedi e-mail címe. Ezzel hozhat létre vagy kaphat le egy adott bejelentkezési e-mail-címmel rendelkező felhasználót. Ha ezt csak a PersistedClaims-ben adja meg a javítás művelet során, akkor a signInNames más típusait is eltávolítja. Ha új típusú signInNames-et szeretne hozzáadni, meg kell őriznie a meglévő signInNames értéket is.|Nem|Nem|Bemenet, Tartós, Kimenet|
|state           |Sztring|A felhasználó címében lévő állam vagy tartomány. Max hossz 128.|Igen|Igen|Tartós, Kimenet|
|utcacím   |Sztring|A felhasználó munkahelyének lakcíme. Maximális hossz 1024.|Igen|Igen|Tartós, Kimenet|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|Sztring|A felhasználó másodlagos telefonszáma, amelyet a többtényezős hitelesítéshez használnak.|Igen|Nem|Tartós, Kimenet|
|strongAuthenticationEmailAddress<sup>1</sup>|Sztring|A felhasználó SMTP-címe. Példa:bob@contoso.com" " Ez az attribútum a felhasználónév-házirenddel történő bejelentkezéshez, a felhasználói e-mail-cím tárolásához használatos. Az e-mail cím, majd használt egy jelszó-visszaállítási folyamat.|Igen|Nem|Tartós, Kimenet|
|strongAuthenticationPhoneNumber<sup>1</sup>|Sztring|A felhasználó elsődleges telefonszáma, amelyet a többtényezős hitelesítéshez használnak.|Igen|Nem|Tartós, Kimenet|
|surname         |Sztring|A felhasználó vezetékneve (családnév vagy vezetéknév). Max hossz 64.|Igen|Igen|Tartós, Kimenet|
|phoneNumber (businessPhone első belépése)|Sztring|A felhasználó telephelyének elsődleges telefonszáma.|Igen|Nem|Tartós, Kimenet|
|userPrincipalName    |Sztring|A felhasználó egyszerű felhasználóneve (UPN). Az upn egy internetes bejelentkezési név a felhasználó számára az RFC 822 internetes szabvány alapján. A tartománynak jelen kell lennie a bérlő ellenőrzött tartományok gyűjteményében. Ez a tulajdonság a fiók létrehozásakor szükséges. Megváltoztathatatlan.|Nem|Nem|Bemenet, Tartós, Kimenet|
|usageLocation (használathelye)   |Sztring|Olyan felhasználók számára szükséges, akik nek jogi követelményük miatt licenceket kapnak, hogy ellenőrizzék a szolgáltatások elérhetőségét az országokban. Nem semmisíthető. Kétbetűs országkód (ISO 3166 szabvány). Példák: "US", "JP" és "GB".|Igen|Nem|Tartós, Kimenet|
|userType (felhasználótípusa)        |Sztring|Olyan karakterlánc-érték, amely a címtárban lévő felhasználói típusok osztályozására használható. Az értéknek tagnak kell lennie. Csak olvasható.|Csak olvasható|Nem|Tartós, Kimenet|
|userState (externalUserState)<sup>2</sup>|Sztring|Csak az Azure AD B2B-fiók esetén azt jelzi, hogy a meghívás Függőben elfogadás vagy Elfogadva.|Nem|Nem|Tartós, Kimenet|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|DateTime|A UserState tulajdonság legutóbbi módosításának időbélyege.|Nem|Nem|Tartós, Kimenet|
|<sup>1 1</sup> A Microsoft Graph nem támogatja<br><sup>2.</sup> Nem használható az Azure AD B2C-vel||||||


## <a name="extension-attributes"></a>Kiterjesztés attribútumai

Gyakran létre kell hoznia saját attribútumait, ahogy a következő esetekben:

- Egy ügyfél felé néző alkalmazásnak meg kell maradnia egy attribútumhoz, például a **LoyaltyNumber**attribútumhoz.
- Az identitásszolgáltató egyedi felhasználói azonosítóval rendelkezik, például **uniqueUserGUID,** amelyet menteni kell.
- Az egyéni felhasználói útnak meg kell maradnia egy felhasználó állapotában, például **migrationStatus**.

Az Azure AD B2C kibővíti az egyes felhasználói fiókokban tárolt attribútumok készletét. A bővítményattribútumok kiterjesztik a könyvtárban lévő felhasználói objektumok [sémáját.](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions) A bővítményattribútumok csak egy alkalmazásobjektumon regisztrálhatók, még akkor is, ha egy felhasználó adatait tartalmazhatják. A bővítmény attribútum a b2c-extensions-app nevű alkalmazáshoz van csatolva. Ne módosítsa ezt az alkalmazást, mivel az Azure AD B2C felhasználói adatok tárolására használja. Ez az alkalmazás az Azure Active Directory alkalmazás regisztrációk.

> [!NOTE]
> - Legfeljebb 100 bővítményattribútum írható bármely felhasználói fiókba.
> - Ha a b2c-extensions-app alkalmazás törlődik, ezek a bővítmény attribútumok törlődnek az összes felhasználó, valamint az esetleges adatokat tartalmaznak.
> - Ha az alkalmazás töröl egy bővítményattribútumot, az törlődik az összes felhasználói fiókból, és az értékek törlődnek.
> - A bővítmény attribútum ának alapjául szolgáló név a "Extension_" + Application ID + "_" + Attribútumnév formátumban jön létre. Ha például létrehoz egy Extension Attribútumot, a LoyaltyNumber-t, és a b2c-extensions-app alkalmazásazonosítója 831374b3-bd50-41bf-aa54-263ec9e050fc, az alapul szolgáló bővítmény attribútumneve a következő lesz: extension_831374b3bd5041bfaa54263ec9e050fc_ Hűségszám. Az alapul szolgáló nevet használja a Graph API-lekérdezések futtatásakor felhasználói fiókok létrehozásához vagy frissítéséhez.

A következő adattípusok támogatottak, ha egy tulajdonságot definiál egy sémabővítményben:

|Tulajdonság típusa |Megjegyzések  |
|--------------|---------|
|Logikai    | Lehetséges értékek: **igaz** vagy **hamis**. |
|DateTime   | ISO 8601 formátumban kell megadni. Lesz tárolva UTC.   |
|Egész szám    | 32 bites érték.               |
|Sztring     | Legfeljebb 256 karakter.     |

## <a name="next-steps"></a>További lépések
További információ a bővítményattribútumokról:
- [Sémabővítmények](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)
- [Egyéni attribútumok definiálása felhasználói folyamattal](user-flow-custom-attributes.md)
- [Egyéni attribútumok definiálása egyéni házirenddel](custom-policy-custom-attributes.md)
