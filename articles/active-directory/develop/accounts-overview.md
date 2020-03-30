---
title: Microsoft identity platform fiókok & bérlői profilok Android | Azure
description: Az Android Microsoft identitásplatform-fiókjainak áttekintése
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.devlang: java
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: d0497ad68e7b29e6d8c83dd860ba8f509e229579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77611889"
---
# <a name="accounts--tenant-profiles-android"></a>Fiókok és bérlői profilok (Android)

Ez a cikk áttekintést `account` nyújt arról, hogy mi is a Microsoft identitásplatformon.

A Microsoft Authentication Library (MSAL) API a *felhasználó* kifejezést a *fiókkifejezéssel*helyettesíti. Ennek egyik oka az, hogy egy felhasználó (emberi vagy szoftverügynök) több fiókkal rendelkezhet, vagy használhat. Ezek a fiókok lehetnek a felhasználó saját szervezetében és/vagy más szervezetekben, amelyeknek a felhasználó tagja.

A Microsoft identity platformon lévő fiók a következőkből áll:

- Egyedi azonosító.  
- Egy vagy több hitelesítő adat a fiók tulajdonjogának/ellenőrzésének igazolására szolgál.
- Egy vagy több profil, amely attribútumokat, például:
  - Kép, utónév, családnév, cím, irodahely
- Egy fiók nak van egy jogosultsági forrása vagy nyilvántartási rendszere. Ez az a rendszer, ahol a fiók létrejön, és ahol a fiókhoz társított hitelesítő adatok at tárolják. A több-bérlős rendszerek, mint például a Microsoft `tenant` identitás platform, a rekord rendszer az, ahol a fiók jött létre. Ezt a bérlőt `home tenant`a.
- A Microsoft identity platformon lévő fiókok a következő nyilvántartási rendszerekkel rendelkeznek:
  - Az Azure Active Directory, beleértve az Azure Active Directory B2C.
  - Microsoft-fiók (Live).
- A Microsoft identity platformon kívüli rekordrendszerekből származó fiókok a Microsoft identitásplatformon belül jelennek meg, beleértve a következőket:
  - identitások csatlakoztatott helyszíni könyvtárakból (Windows Server Active Directory)
  - külső identitások a LinkedIn, GitHub, és így tovább.
  Ezekben az esetekben egy fiók rendelkezik egy eredeti nyilvántartási rendszerrel és egy rekordrendszerrel a Microsoft identitásplatformján belül.
- A Microsoft identity platform lehetővé teszi, hogy egy fiók több szervezethez (Azure Active Directory-bérlőkhöz) tartozó erőforrások eléréséhez használható.
  - Annak rögzítéséhez, hogy az egyik rekordrendszerből (AAD-bérlő A) származó fiók hozzáfér egy erőforráshoz egy másik rekordrendszerben (AAD Tenant B), a fiókot abban a bérlőben kell ábrázolni, ahol az erőforrás definiálva van. Ez úgy történik, hogy helyi nyilvántartást hoz létre a fiókról a B rendszer A rendszeréből.
  - Ez a helyi rekord, azaz a fiók ábrázolása az eredeti fiókhoz van kötve.
  - Az MSAL ezt a `Tenant Profile`helyi rekordot .
  - A bérlői profil különböző attribútumokkal rendelkezhet, amelyek megfelelnek a helyi környezetnek, például a beosztás, az Office helye, a kapcsolattartási adatok stb.
- Mivel egy fiók egy vagy több bérlőben is jelen lehet, előfordulhat, hogy egy fiók egynél több profillal rendelkezik.

> [!NOTE]
> Az MSAL a Microsoft-fiókrendszert (Live, MSA) a Microsoft identitásplatformon belül egy másik bérlőként kezeli. A Microsoft-fiók bérlői azonosítója:`9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>Számlaáttekintő diagram

![Fiókáttekintő diagram](./media/accounts-overview/accounts-overview.svg)

A fenti ábrán:

- A `bob@contoso.com` fiók a helyszíni Windows Server Active Directoryban (a helyszíni rekordrendszer) jön létre.
- A `tom@live.com` fiók a Microsoft-fiók bérlőjében jön létre.
- `bob@contoso.com`legalább egy erőforráshoz hozzáfér az Alábbi Azure Active Directory-bérlőkben:
  - contoso.com (nyilvántartási felhőrendszer – a helyszíni nyilvántartási rendszerhez kapcsolva)
  - fabrikam.com
  - woodgrovebank.com
  - Ezekhez a `bob@contoso.com` bérlőkhöz minden bérlőhöz létezik bérlői profil.
- `tom@live.com`a következő Microsoft-bérlők erőforrásaihoz fér hozzá:
  - contoso.com
  - fabrikam.com
  - Ezekhez a `tom@live.com` bérlőkhöz minden bérlőhöz létezik bérlői profil.
- A tomra és Bobra vonatkozó információk más bérlőknél eltérhetnek a nyilvántartási rendszertől. Ezek eltérhetnek az olyan attribútumoktól, mint a Beosztás, az Office helye és így tovább. Lehetnek csoportok és/vagy szerepkörök tagjai az egyes szervezeteken belül (Azure Active Directory-bérlő). Ezt az információt bob@contoso.com bérlői profilként hivatkozunk.

Az bob@contoso.com ábrán, tom@live.com és hozzáférhet a különböző Azure Active Directory-bérlők erőforrásaihoz. További információ: [Add Azure Active Directory B2B együttműködési felhasználók az Azure Portalon.](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)

## <a name="accounts-and-single-sign-on-sso"></a>Fiókok és egyszeri bejelentkezés (SSO)

Az MSAL token gyorsítótára fiókonként *egyetlen frissítési jogkivonatot* tárol. Ez a frissítési jogkivonat segítségével csendben hozzáférési jogkivonatokat kérhet több Microsoft identitásplatform-bérlőtől. Amikor egy bróker telepítve van egy eszközön, a fiókot a bróker kezeli, és az eszköz szintű egyszeri bejelentkezés lehetővé válik.

> [!IMPORTANT]
> Az üzleti felhasználók (B2C) fiók és a frissítési jogkivonat viselkedése eltér a Microsoft identitásplatform többi részétől. További információ: [B2C Policies & Accounts](#b2c-policies--accounts).

## <a name="account-identifiers"></a>Fiókazonosítók

Az MSAL-fiókazonosító nem fiókobjektum-azonosító. Ez nem azt jelentette, hogy elemezni és / vagy hivatkozott közvetíteni semmi más, mint egyediség a Microsoft identitás platformon.

Az Azure AD hitelesítési könyvtár (ADAL) kompatibilitás, valamint az ADAL-ról MSAL-ra való áttérés megkönnyítése érdekében az MSAL az MSAL-gyorsítótárban elérhető fiók bármely érvényes azonosítójával kereshet fiókokat.  Például a következő mindig ugyanazt a tom@live.com fiókobjektumot kéri be, mert az egyes azonosítók érvényesek:

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>Hozzáférés egy fiókkal kapcsolatos jogcímekhez

A hozzáférési jogkivonat kérése mellett az MSAL mindig kér egy azonosító jogkivonatot minden egyes bérlőtől. Ezt úgy éri el, hogy mindig a következő hatóköröket kéri:

- Openid
- profil

Az azonosító jogkivonat a jogcímek listáját tartalmazza. `Claims`név-érték párok a fiókról, és a kérés hez használatosak.

Mint korábban említettük, minden bérlő, ahol egy fiók létezik, különböző információkat tárolhat a fiókról, beleértve, de nem kizárólagosan az attribútumokat, például: beosztás, iroda helye és így tovább.

Bár egy fiók több szervezet tagja vagy vendége is lehet, az MSAL nem kérdez meg egy szolgáltatást, hogy leszámítsa azoknak a bérlőknek a listáját, amelyeknek a fiók tagja. Ehelyett az MSAL összeáll egy listát a bérlők, hogy a fiók jelen van, a token kérelmek eredményeként, amelyek történtek.

A fiókobjektumban elérhető jogcímek mindig a "főbérlő"/{authority} jogcímek egy fiókhoz. Ha ezt a fiókot nem használták jogkivonat kéréséhez az otthoni bérlőhöz, az MSAL nem tud jogcímeket biztosítani a fiókobjektumon keresztül.  Példa:

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> A fiókobjektumból elérhető jogcímek listájának megtekintéséhez olvassa el [a id_token](https://docs.microsoft.com/azure/active-directory/develop/id-tokens#claims-in-an-id_token)

> [!TIP]
> További jogcímek id_token, olvassa el a választható jogcímek [dokumentációját Hogyan: Opcionális jogcímek biztosítása az Azure AD-alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)

### <a name="access-tenant-profile-claims"></a>Bérlői profil jogcímei elérése

Ha más bérlőknél is szeretné elérni a fiókkal kapcsolatos jogcímeket, először a fiókobjektumot kell a programra `IMultiTenantAccount`vetnie. Az összes fiók lehet több-bérlős, de az MSAL-on keresztül elérhető bérlői profilok száma azon alapul, hogy mely bérlők kért jogkivonatokat az aktuális fiók használatából.  Példa:

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>B2C-házirendek & fiókok

Egy fiók frissítési jogkivonatai nincsenek megosztva a B2C-házirendek között. Ennek eredményeképpen a jogkivonatok használatával történő egyszeri bejelentkezés nem lehetséges. Ez nem jelenti azt, hogy az egyszeri bejelentkezés nem lehetséges. Ez azt jelenti, hogy az egyszeri bejelentkezésnek olyan interaktív élményt kell használnia, amelyben egy cookie elérhető az egyszeri bejelentkezés engedélyezéséhez.

Ez azt is jelenti, hogy az MSAL esetében, ha különböző B2C-házirendek használatával szerez be jogkivonatokat, akkor ezeket külön fiókokként kezeli a program - mindegyik saját azonosítóval. Ha egy fiókot szeretne használni a `acquireTokenSilent`token igényléséhez a használatával, akkor ki kell választania a fiókot a fiókok listájából, amely megfelel a jogkivonat-kérelemmel használt szabályzatnak. Példa:

```java
// Get Account For Policy

String policyId = "SignIn";
IAccount signInPolicyAccount = getAccountForPolicyId(app, policyId);

private IAccount getAccountForPolicy(IPublicClientApplication app, String policyId)
{
    List<IAccount> accounts = app.getAccounts();

    foreach(IAccount account : accounts)
   {
        if (account.getClaims().get("tfp").equals(policyId))
        {
            return account;
        }
    }

    return null;
}
```
