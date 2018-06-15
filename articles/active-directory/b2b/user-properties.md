---
title: Egy Azure Active Directory B2B együttműködés felhasználó tulajdonságai |} Microsoft Docs
description: Az Azure Active Directory B2B együttműködés felhasználó tulajdonságainak konfigurálható
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/25/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 65fd23ecf81aebdb1fe74f800d20df6414c18317
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260069"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Egy Azure Active Directory B2B együttműködés felhasználó tulajdonságai

Az Azure Active Directory (Azure AD) üzleti vállalatközi (B2B) együttműködés felhasználó tulajdonképpen UserType rendelkező felhasználó Vendég =. A Vendég felhasználói általában egy fiókpartner-szervezet származik, és csak korlátozott jogosultságokkal a hívja fel a címtárban, alapértelmezés szerint.

Attól függően, hogy a hívja fel szervezet igényei az Azure AD B2B együttműködés felhasználói lehet a következő fiók állapota:

- 1. állapot: Az Azure AD külső példányának a következő helyen, és a Vendég felhasználó hívja fel a szervezet ábrázolva. Ebben az esetben a B2B felhasználó jelentkezik be egy Azure AD fiókkal, amely a meghívott bérlőhöz tartozik. Ha a fiókpartner-szervezet nem használhatja az Azure Active Directory, az Azure ad-ben a Vendég felhasználó továbbra is létrejön. A követelmények a következők:, hogy azok beváltani a meghívót, és az Azure AD ellenőrzi az e-mail címet. Ezzel az elrendezéssel egy közvetlenül az igény (szerinti JIT) bérlős és a "ugrásszerű" bérlőhöz is nevezik.

- 2. állapot: A Microsoft-fiókkal a következő helyen, és a gazdagép szervezet vendégfelhasználóként egy képviseli. Ebben az esetben a Vendég felhasználói egy Microsoft-fiókkal jelentkezik be. A meghívott felhasználó közösségi identity (google.com vagy hasonló), amely nincs Microsoft-fiókja, amely a Microsoft-fiók során létrejön ajánlat érvényesítési.

- 3. állapot: Az állomás szervezet helyi Active Directoryban a következő helyen, és szinkronizálása megtörtént-e a gazdagép szervezet Azure AD. Ebben a kiadásban során manuális módosítása az ilyen felhasználók a felhőben UserType PowerShell kell használnia.

- 4. állapot: Az állomás szervezet Azure-ban a következő helyen UserType az AD = Vendég és a hitelesítő adatokat, amelyeket a gazdagép szervezetem kezeli.

  ![a meghívó monogramja megjelenítése](media/user-properties/redemption-diagram.png)


Most nézzük meg, mi az Azure AD B2B együttműködés felhasználói állapot az 1. a következőképpen néz Azure AD-ben.

### <a name="before-invitation-redemption"></a>Meghívót visszaváltás előtt

![Az ajánlat visszaváltás előtt](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Meghívót érvényesítési után

![Az ajánlat érvényesítési után](media/user-properties/after-redemption.png)

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Az Azure AD B2B együttműködés felhasználó kulcstulajdonságok
### <a name="usertype"></a>UserType
Ez a tulajdonság azt jelzi, hogy a felhasználót, hogy a gazdagép bérleti kapcsolat. Ez a tulajdonság két értékeket veheti fel:
- Tag: Az érték adja meg a gazdagép szervezet és a szervezet Bérlista egy felhasználóra alkalmazott. Például ennek a felhasználónak el kell érnie a csak belső helyek vár. Ez a felhasználó nem tekinthető egy külső közreműködő.

- Vendég: Ez az érték azt jelzi, a felhasználó, aki nem számít, például egy külső közreműködő, partner, ügyfél vagy hasonló felhasználói a vállalat belső használatára. Ilyen felhasználói várhatóan nem tudnák a Vezérigazgató belső emlékeztető jelenik meg, vagy vállalati előnyeit, például kap.

  > [!NOTE]
  > A UserType nincs kapcsolat, hogy a felhasználó bejelentkezik, a felhasználó, és így tovább a címtár szerepkörrel rendelkezik. Ez a tulajdonság egyszerűen jelzi a felhasználó kapcsolatot a gazdagép szervezet számára, és lehetővé teszi a vállalat házirendek kényszerítését, ez a tulajdonság függ.

### <a name="source"></a>Forrás
Ez a tulajdonság jelzi, hogy a felhasználó bejelentkezik.

- A meghívott felhasználó: Ez a felhasználó kérték, de még nem beváltott meghívót.

- Külső Active Directory: Ez a felhasználó a külső szervezetek van a következő helyen, és az Azure AD-fiókot, a másik szervezethez tartozó használatával. Ez a típus a bejelentkezési állapot 1 felel meg.

- Microsoft-fiók: Ez a felhasználó van a Microsoft-fiókkal a következő helyen, és a Microsoft-fiók használatával. Ez a típus a bejelentkezési állapot 2 felel meg.

- Windows Server Active Directory: A bejelentkezett felhasználó a helyi Active Directoryból, amely a szervezethez tartozik. Ez a típus a bejelentkezési állapot 3 felel meg.

- Az Azure Active Directory: A felhasználó hitelesíti számára egy Azure AD-fiókot, amely a szervezethez tartozik. Ez a típus a bejelentkezési állapot 4 felel meg.
  > [!NOTE]
  > Forrás- és UserType is független tulajdonságait. A forrás érték nem feltétlenül jelenti azt egy adott értéket a UserType.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Azure AD B2B felhasználók felveheti tagként vendégek helyett?
Általában az Azure AD B2B felhasználót és a Vendég felhasználó is megfelel. Ezért az Azure AD B2B együttműködés felhasználói meg van adva egy UserType rendelkező felhasználó Vendég = alapértelmezés szerint. Azonban bizonyos esetekben a fiókpartner-szervezet tagja nagyobb cég, amelyhez a gazdagép szervezet is tartozik. Ha igen, a gazdagép szervezet kíván kezelheti a felhasználókat az erőforráspartner-szervezet tagként vendégek helyett. Az Azure AD B2B meghívó Manager API-k segítségével adja hozzá, vagy a gazdagép szervezet tagja a fiókpartner-szervezet a felhasználó meghívása.

## <a name="filter-for-guest-users-in-the-directory"></a>A vendégfelhasználók a címtárban szűrő

![Szűrő vendégfelhasználók számára](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Alakítsa át UserType
Azt jelenleg a felhasználók át UserType tag Vendég, és fordítva a PowerShell használatával. Azonban a UserType tulajdonság kellene a felhasználói viszony jelölésére a szervezet számára. Ezért ez a tulajdonság értékének kell módosítani, csak akkor, ha módosítja a kapcsolatot a felhasználó a szervezet számára. Ha a kapcsolat a felhasználó módosítja, a problémák, például hogy az egyszerű felhasználónév (UPN) kell módosítani, foglalkozzon? A felhasználó továbbra is hozzáférhetnek az erőforrásokhoz? Egy postaláda rendelhető? Ezért nem javasoljuk a UserType módosítása atomi tevékenységként PowerShell használatával. Ezenkívül abban az esetben, ha ezt a tulajdonságot a PowerShell használatával válik nem módosítható, nem ajánlott véve egy függőséget meg ezt az értéket.

## <a name="remove-guest-user-limitations"></a>Távolítsa el a Vendég felhasználói korlátozásai
Előfordulhat, ha szeretne adni a vendégfelhasználók magasabb jogosultságokkal. Vendég felhasználó hozzáadása minden olyan szerepkört, és akkor is távolítsa el az alapértelmezett Vendég felhasználó korlátozásokat a felhasználó az azonos jogosultságokat tagként adhat a címtárban.

Akkor lehet, hogy a vállalati címtárban Vendég felhasználó kap egy tag felhasználóként ugyanazokkal az engedélyekkel az alapértelmezett Vendég felhasználó korlátozások kikapcsolása.

![Távolítsa el a Vendég felhasználói korlátozásai](media/user-properties/remove-guest-limitations.png)

## <a name="next-steps"></a>További lépések

* [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
* [B2B együttműködés felhasználói jogkivonatokhoz](user-token.md)
* [B2B együttműködés felhasználói jogcímek leképezése](claims-mapping.md)
