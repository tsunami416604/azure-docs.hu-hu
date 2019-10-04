---
title: Microsoft Identity platform-fiókok és bérlői profilok (Android) | Azure
description: Az Androidhoz készült Microsoft Identity platform-fiókok áttekintése
services: active-directory
documentationcenter: ''
author: shoatman
manager: nadima
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7beab6759524037f86c83429644c1bb1fffe4d07
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679840"
---
# <a name="accounts--tenant-profiles-android"></a>Fiókok & bérlői profilok (Android)

Ez a cikk áttekintést nyújt arról, hogy mi a `account` a Microsoft Identity platformon.

A Microsoft Authentication Library (MSAL) API lecseréli a *felhasználó* kifejezést a term *fiókra*. Ennek egyik oka, hogy egy felhasználó (emberi vagy szoftver ügynök) több fiókot is használhat. Ezek a fiókok lehetnek a felhasználó saját szervezetében és/vagy más szervezetekben, amelyeknek a felhasználó a tagja.

A Microsoft Identity platform egyik fiókja a következőkből áll:

  - Egyedi azonosító.
  - Egy vagy több hitelesítő adat, amely a fiók tulajdonjogának vagy vezérlésének bemutatására szolgál.
  - Egy vagy több olyan attribútum, amely többek között a következőkből áll:
    - Kép, Utónév, család neve, cím, iroda helye
- A fióknak van egy szolgáltatói vagy nyilvántartási forrása. Ez az a rendszer, ahol a fiók létrejön, és ahol a fiókhoz társított hitelesítő adatok tárolódnak. A több-bérlős rendszerek (például a Microsoft Identity platform) esetében a rekord rendszere a fiók létrehozásának `tenant`. Ez a bérlő a `home tenant` néven is ismert.
- A Microsoft Identity platform fiókjai a következő nyilvántartási rendszerekkel rendelkeznek:
  - Azure Active Directory, beleértve a Azure Active Directory B2C.
  - Microsoft-fiók (élő).
- A Microsoft Identity platformon kívüli rekordokból származó fiókok a Microsoft Identity platformon belül jelennek meg, beleértve a következőket:
  - helyi könyvtárak identitásai (Windows Server Active Directory)
  - külső identitások a LinkedIn, a GitHub és így tovább.
  Ezekben az esetekben a fiók a Microsoft Identity platformon belül mind a forrás-, mind a rekordrendszer-rendszerrel rendelkezik.
- A Microsoft Identity platform lehetővé teszi, hogy az egyik fiók a több szervezethez (Azure Active Directory bérlőhöz) tartozó erőforrások elérésére legyen használva.
  - Annak rögzítéséhez, hogy az egyik HRE (A bérlő A) egy fiók egy másik (HRE-bérlői) rekordban lévő erőforráshoz fér hozzá, a fióknak szerepelnie kell abban a bérlőn, ahol az erőforrás definiálva van. Ezt úgy teheti meg, hogy helyi rekordot hoz létre a fiókból a B rendszeren.
  - Ez a helyi rekord, amely a fiók ábrázolása, az eredeti fiókhoz van kötve.
  - A MSAL ezt a helyi rekordot `Tenant Profile`-ként teszi elérhetővé.
  - A bérlői profilnak különböző attribútumai lehetnek, amelyek megfelelnek a helyi környezetnek, például a beosztás, az iroda helye, a kapcsolattartási adatok stb.
 
- Mivel előfordulhat, hogy egy fiók egy vagy több bérlőn is megtalálható, egy fiók több profillal is rendelkezhet.

> [!NOTE]
> A MSAL a Microsoft Identity platformon belüli másik bérlőként kezeli a Microsoft-fiók rendszerét (élő, MSA). Az Microsoft-fiók bérlő bérlői azonosítója: `9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>Fiók áttekintése diagram

![Fiók áttekintése diagram](./media/accounts-overview/accounts-overview.png)

A fenti ábrán:

- A (z) `bob@contoso.com` fiók a helyszíni Windows Server Active Directory (a rekord forrása a helyszíni rendszer) jön létre.
- A `tom@live.com` fiók a Microsoft-fiók bérlőben jön létre.
- a `bob@contoso.com` a következő Azure Active Directory bérlők legalább egy erőforrásához hozzáfér:
  - contoso.com (a rekord Felhőbeli rendszere – a rekord a helyszíni rendszerhez kapcsolódik)
  - fabrikam.com
  - woodgrovebank.com
  - A `bob@contoso.com` bérlői profilja minden egyes bérlőben megtalálható.
- `tom@live.com` a következő Microsoft-bérlők erőforrásaihoz fér hozzá:
  - contoso.com
  - fabrikam.com
  - A `tom@live.com` bérlői profilja minden egyes bérlőben megtalálható.
- A más bérlők Tom és Bob szolgáltatásával kapcsolatos információk eltérhetnek a rekordból. Eltérőek lehetnek az attribútumok, például a beosztás, az iroda helye stb. Az egyes szervezetekben (Azure Active Directory bérlőn) belüli csoportok és/vagy szerepkörök tagjai lehetnek. Ezeket az információkat bob@contoso.com bérlői profilként tekintjük át.

A diagramon a bob@contoso.com és a tom@live.com a különböző Azure Active Directory bérlők erőforrásaihoz fér hozzá. További információ: [Azure Active Directory B2B együttműködéssel rendelkező felhasználók hozzáadása a Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

## <a name="accounts-and-single-sign-on-sso"></a>Fiókok és egyszeri bejelentkezés (SSO)

Az MSAL jogkivonat-gyorsítótár *egyetlen frissítési jogkivonatot* tárol egy fiókban. A frissítési token használatával csendesen kérhet hozzáférési jogkivonatokat több Microsoft Identity platform-bérlőtől. Ha egy ügynök telepítve van egy eszközön, a fiókot a közvetítő felügyeli, és az eszközre érvényes egyszeri bejelentkezés válik elérhetővé.

> [!IMPORTANT]
> A vállalatok közötti (B2C) fiók és a frissítési jogkivonat viselkedése eltér a Microsoft Identity platform többi részétől. További információ: B2C- [szabályzatok & fiókok](#b2c-policies--accounts).

## <a name="account-identifiers"></a>Fiókazonosító

A MSAL fiók azonosítója nem fiók-objektumazonosító. Nem kell elemezni és/vagy támaszkodni arra, hogy a Microsoft Identity platformon belül bármilyen egyediséget közvetítenek.

Az Azure AD Authentication Library (ADAL) szolgáltatással való kompatibilitás érdekében, valamint a ADAL-ről a MSAL-re való Migrálás megkönnyítéséhez a MSAL a MSAL-gyorsítótárban elérhető fiók bármely érvényes azonosítójának használatával kereshet fiókokat.  A következőkben például mindig ugyanaz a fiók-objektum lesz lekérdezve a tom@live.com esetében, mert az egyes azonosítók érvényesek:

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>Fiókhoz tartozó jogcímek elérése

A hozzáférési token igénylése mellett a MSAL is minden bérlőtől kér azonosító jogkivonatot. Ezt mindig a következő hatókörökre kéri:

- OpenID
- profile

Az azonosító jogkivonat a jogcímek listáját tartalmazza. @no__t – 0 a fiókra vonatkozó név-érték párok, amelyeket a rendszer a kérelem elvégzéséhez használ.

Ahogy azt korábban említettük, az egyes bérlők, ahol egy fiók létezik, különböző adatokat tárolhatnak a fiókról, többek között a következő attribútumokra: beosztás, iroda helye stb.

Habár egy fiók lehet tag vagy vendég több szervezeten belül, a MSAL nem kérdez le egy szolgáltatást, hogy lekérje azon bérlők listáját, amelyeknek a fiók tagja. Ehelyett a MSAL létrehoz egy listát azokról a bérlők listájáról, amelyeken a fiók szerepel, a jogkivonat-kérelmek eredményeképpen.

A fiók objektumon közzétett jogcímek mindig a fiók "Kezdőlap bérlője"/{Authority} származó jogcímek. Ha ez a fiók nem használ jogkivonatot a saját bérlője számára, a MSAL nem tud jogcímeket biztosítani a fiók objektumon keresztül.  Példa:

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> A fiók objektumból elérhető jogcímek listájának megtekintéséhez tekintse meg a [jogcímeket egy id_token](https://docs.microsoft.com/azure/active-directory/develop/id-tokens#claims-in-an-id_token)

> [!TIP]
> Ha további jogcímeket szeretne felvenni a id_token, tekintse meg a [How választható jogcímek dokumentációját: Opcionális jogcímek megadása az Azure AD-alkalmazáshoz @ no__t-0

### <a name="access-tenant-profile-claims"></a>Bérlői profil jogcímeinek elérése

Ha más bérlők által megjelenő fiókhoz szeretne jogcímeket elérni, először el kell végeznie a fiók objektumát `IMultiTenantAccount` értékre. Az összes fiók több-bérlő lehet, de a MSAL-n keresztül elérhető bérlői profilok száma attól függ, hogy mely bérlők igényeltek jogkivonatot az aktuális fiók használatával.  Példa:

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>B2C-szabályzatok & fiókok

A fiókok frissítési jogkivonatai nem oszthatók meg a B2C-szabályzatok között. Ennek eredményeképpen a tokeneket használó egyszeri bejelentkezés nem lehetséges. Ez nem jelenti azt, hogy az egyszeri bejelentkezés nem lehetséges. Ez azt jelenti, hogy az egyszeri bejelentkezéshez olyan interaktív élményt kell használni, amelyben az egyszeri bejelentkezés lehetővé teszi a cookie-t.

Ez azt is jelenti, hogy a MSAL esetén, ha különböző B2C-szabályzatokat használó jogkivonatokat vásárol, ezeket külön fiókokként kezeli a rendszer, amelyek mindegyike saját azonosítóval rendelkezik. Ha egy fiókot szeretne használni a `acquireTokenSilent` használatával a token igényléséhez, akkor ki kell választania a fiókot azon fiókok listájából, amelyek megfelelnek a jogkivonat-kérelemmel használt szabályzatnak. Példa:

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
