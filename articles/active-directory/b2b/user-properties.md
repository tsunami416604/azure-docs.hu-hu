---
title: Az Azure Active Directory B2B együttműködés felhasználói tulajdonságainak |} A Microsoft Docs
description: Az Azure Active Directory B2B együttműködés felhasználói tulajdonságok konfigurálható
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/25/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 0cfd7888acf942e4af875c37c2472ff086f9119b
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059640"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Egy Azure Active Directory B2B együttműködés felhasználó tulajdonságai

Egy Azure Active Directory (Azure AD) vállalatközi (B2B) együttműködés felhasználói a felhasználó a UserType = Vendég. A vendégfelhasználó általában egy fiókpartner-szervezet, és korlátozott jogosultságokkal a meghívó könyvtárban, alapértelmezés szerint.

A meghívó szervezetet szükségleteinek egy Azure AD B2B együttműködés felhasználói lehet, a következő fiók állapota:

- 1. állapot: Az Azure AD külső példányának a következő helyen, és a meghívó szervezetet vendégfelhasználó-kiszolgálókként. Ebben az esetben a B2B-felhasználó bejelentkezik egy az Azure AD-fiókkal, amely a meghívott bérlőhöz tartozik. Ha a fiókpartner-szervezet nem használ az Azure ad-ben, a vendégfelhasználó az Azure ad-ben továbbra is jön létre. A követelmények a következők:, hogy azok beváltani a meghívót, és az Azure AD ellenőrzi az e-mail-címükkel. Ezzel az elrendezéssel fokozott egy – igény (szerinti JIT) bérlős és a egy "vírusos" bérlős is nevezik.

- 2. állapot: Microsoft-fiók tárolt és a gazdagép szervezet vendégfelhasználó-kiszolgálókként. Ebben az esetben a Vendég felhasználó jelentkezik be egy Microsoft-fiókkal. A meghívott felhasználó társadalombiztosítási azonosító (google.com vagy hasonló), která není egy Microsoft-fiók jön létre egy Microsoft-fiók ajánlat érvényesítési során.

- 3. állapot: Az állomás a szervezet a helyszíni Active Directoryban tárolt, és szinkronizálja a gazdagép a szervezet Azure-ral AD. Ez a kiadás során manuális módosítása az ilyen felhasználók a felhőben a UserType PowerShell kell használnia.

- 4. állapot: Az állomás a szervezet Azure-ban tárolt AD a UserType = Vendég és hitelesítő adatokat, amelyeket a gazdagép szervezet felügyeli.

  ![Megjelenítés a meghívót küldő személy monogramja](media/user-properties/redemption-diagram.png)


Most nézzük meg, hogy egy Azure AD B2B együttműködés felhasználói állapot 1 néz ki az Azure ad-ben.

### <a name="before-invitation-redemption"></a>Mielőtt vendégmeghívás beváltása

![Az ajánlat visszaváltás előtt](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Meghívó beváltása után

![Az ajánlat beváltást követően](media/user-properties/after-redemption.png)

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Az Azure AD B2B együttműködés felhasználói legfontosabb tulajdonságai
### <a name="usertype"></a>UserType
Ez a tulajdonság azt jelzi, hogy a felhasználót, hogy a gazdagép bérlős kapcsolat. Ez a tulajdonság két értékekkel rendelkezhet:
- Tag: Ez az érték azt jelzi, hogy a gazdagép szervezet és a egy felhasználó a szervezet Bérlista-alkalmazott. Például ennek a felhasználónak el kell érnie a belső helyek vár. Ez a felhasználó nem tekint egy külső közreműködő.

- Vendég: Ez az érték azt jelzi, hogy a felhasználó, aki nem tekinthető a vállalatnak, például egy külső közreműködő, partnerek, ügyfelek vagy hasonló felhasználói belső. A Vezérigazgató belső emlékeztető fogadni, vagy a juttatások igénybe vételéhez vállalati, például ilyen a felhasználó nem várható.

  > [!NOTE]
  > A UserType nem rendelkezik kapcsolattal, hogy a felhasználó bejelentkezik, a címtár szerepkörrel, a felhasználó, és így tovább. Ez a tulajdonság csak azt jelzi, hogy a felhasználó kapcsolat a gazdagép-szervezethez, és lehetővé teszi, hogy a szervezet, amely ezt a tulajdonságot függ házirendeknek az érvényesítését.

### <a name="source"></a>Forrás
Ez a tulajdonság azt jelzi, hogy a felhasználó bejelentkezik.

- Felhasználó meghívása: Ez a felhasználó kapott meghívót, de még nem még sikerült beváltani a meghívót.

- Külső Active Directory: Ez a felhasználó külső szervezet van tárolt, és egy Azure AD-fiókot, a másik szervezethez tartozó használatával hitelesíti. Jelentkezzen be az ilyen állapot 1 felel meg.

- Microsoft-fiók: Ez a felhasználó Microsoft-fiókkal van tárolt, és hitelesíti a Microsoft-fiók használatával. Jelentkezzen be az ilyen állapot 2 felel meg.

- A Windows Server Active Directory: A felhasználó jelentkezett be a helyszíni Active Directoryból, hogy a szervezethez tartozik. Jelentkezzen be az ilyen állapot 3 felel meg.

- Az Azure Active Directory: Ez a felhasználó végzi a hitelesítést egy az Azure AD-fiókkal, amely a szervezethez tartozik. Jelentkezzen be az ilyen állapot 4 felel meg.
  > [!NOTE]
  > Forrás- és UserType olyan független tulajdonságok. A forrás értéke nem jelenti azt, egy adott érték a UserType.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Az Azure AD B2B-felhasználók vehetők helyett vendégek tagként?
Általában egy Azure AD B2B-felhasználó és a Vendég felhasználó is megfelel. Ezért egy Azure AD B2B együttműködés felhasználói kerülnek be a felhasználó a UserType = Vendég alapértelmezés szerint. Azonban bizonyos esetekben a fiókpartner-szervezet tagja által felügyelt nagyobb szervezettel, amelyhez a gazdagép szervezet is tartozik. Ha igen, a gazdagép szervezet kíván kezelheti a felhasználókat a fiókpartner-szervezet tagjai vendégek helyett. Az Azure AD B2B meghívó Manager API-k használatával adja hozzá, vagy a gazdagép szervezet, amelynek a partnerszervezet felhasználó meghívása.

## <a name="filter-for-guest-users-in-the-directory"></a>A vendégfelhasználók számára a címtárban szűrése

![Vendég felhasználók szűrése](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Konvertálja a UserType
Jelenleg a felhasználók átalakítása UserType tag Vendég, és ez fordítva PowerShell használatával. A UserType tulajdonság azonban a szervezet számára a felhasználói viszony jelölésére értendő. Ezért ez a tulajdonság értékének kell megváltoztatnia, csak akkor, ha a kapcsolat a felhasználó a szervezet számára, megváltozik. Ha a kapcsolat a felhasználó módosítja, a problémák, például hogy az egyszerű felhasználónév (UPN) kell megváltoztatnia, beavatkozást igényel? A felhasználó továbbra is ugyanazokhoz az erőforrásokhoz való hozzáférése? Postaláda hozzárendelve? Ezért nem javasoljuk, hogy váltson a UserType atomi tevékenységként PowerShell használatával. Emellett abban az esetben ez a tulajdonság nem módosítható válik PowerShell-lel, nem ajánlott ezt az értéket a függőség véve.

## <a name="remove-guest-user-limitations"></a>Távolítsa el a Vendég felhasználói korlátozások
Előfordulhatnak olyan esetekben, ahol kíván adni a vendégfelhasználót magasabb jogosultságokkal. Vendégfelhasználó hozzáadása szerepkörhöz sem, és akár el is távolíthat Vendég alapértelmezés szerinti felhasználói korlátozásokon tagként ugyanazokat a jogosultságokat biztosíthat a címtárban.

Akkor lehet, hogy a vállalat címtárából vendégfelhasználó ugyanazokkal az engedélyekkel tag felhasználóként van megadva a Vendég felhasználó alapértelmezett korlátozások kikapcsolása.

![Távolítsa el a Vendég felhasználói korlátozások](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Készíthetek vendégfelhasználók látható az Exchange globális címlista?
Igen. Alapértelmezés szerint Vendég objektumok nem láthatók a szervezet globális címlista, de használhatja az Azure Active Directory PowerShell láthatóvá tegye őket. További információkért lásd: **lehet Vendég objektumok a globális címlista látható?** a [vendég hozzáférés az Office 365-csoportok](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6#PickTab=FAQ). 

## <a name="next-steps"></a>További lépések

* [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
* [B2B-együttműködés felhasználói jogkivonatok](user-token.md)
* [B2B-együttműködés felhasználói jogcímek társítása](claims-mapping.md)
