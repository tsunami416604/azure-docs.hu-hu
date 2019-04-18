---
title: A B2B tulajdonságainak Vendég felhasználó – Azure Active Directory |} A Microsoft Docs
description: Az Azure Active Directory B2B-vendégként felhasználói tulajdonságok és állapotok előtt és után vendégmeghívás beváltása
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d4466e4ac7a4e818da6332254e3094eccbaf2b4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59257603"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Egy Azure Active Directory B2B együttműködés felhasználó tulajdonságai

Ez a cikk ismerteti a tulajdonságok és az Azure Active Directory (Azure AD) a B2B-Vendég felhasználói objektum állapotokat előtt és után vendégmeghívás beváltása. Egy Azure AD vállalatközi (B2B) együttműködés felhasználói a felhasználó a UserType = Vendég. A vendégfelhasználó általában egy fiókpartner-szervezet, és korlátozott jogosultságokkal a meghívó könyvtárban, alapértelmezés szerint.

A meghívó szervezetet szükségleteinek egy Azure AD B2B együttműködés felhasználói lehet, a következő fiók állapota:

- 1. állapot: Az Azure AD külső példányának a következő helyen, és a meghívó szervezetet vendégfelhasználó-kiszolgálókként. Ebben az esetben a B2B-felhasználó bejelentkezik egy az Azure AD-fiókkal, amely a meghívott bérlőhöz tartozik. Ha a fiókpartner-szervezet nem használ az Azure ad-ben, a vendégfelhasználó az Azure ad-ben továbbra is jön létre. A követelmények a következők:, hogy azok beváltani a meghívót, és az Azure AD ellenőrzi az e-mail-címükkel. Ezzel az elrendezéssel fokozott egy – igény (szerinti JIT) bérlős és a egy "vírusos" bérlős is nevezik.

- 2. állapot: A Microsoft vagy más fiók tárolt és a gazdagép szervezet vendégfelhasználó-kiszolgálókként. Ebben az esetben a Vendég felhasználó bejelentkezik egy Microsoft-fiókjával vagy a közösségi fiók (google.com vagy hasonló). A meghívott felhasználó identitása alatt ajánlat érvényesítési egy Microsoft-fiók a meghívó szervezetet címtárban jön létre.

- 3. állapot: A gazdagép a szervezet a helyszíni Active Directoryban tárolt, és szinkronizálja a gazdagép a szervezet Azure AD. Az Azure AD Connect használatával a partner-fiókok szinkronizálása a felhőbe, az Azure AD B2B-felhasználók a UserType = Vendég. Lásd: [helyileg kezelt partner fiókok hozzáférést biztosít a felhőbeli erőforrások](hybrid-on-premises-to-cloud.md).

- 4. állapot: A gazdagép a szervezet Azure-ban tárolt AD a UserType = Vendég és hitelesítő adatokat, amelyeket a gazdagép szervezet felügyeli.

  ![A négy felhasználói állapotok ábrázoló diagram](media/user-properties/redemption-diagram.png)


Most nézzük meg, az Azure AD B2B együttműködés felhasználói néz ki az Azure ad-ben.

### <a name="before-invitation-redemption"></a>Mielőtt vendégmeghívás beváltása

Állapot: 1 és 2. állapot fiókok olyan vendégfelhasználók működhet a vendég felhasználók saját hitelesítő adataival eredményét. A meghívó a Vendég felhasználó először küld, amikor egy fiókot a címtárban jön létre. Ez a fiók nem rendelkezik, mert a vendégfelhasználó identitásszolgáltató hitelesítés történik tartozó hitelesítő adatokat. A **forrás** a Vendég felhasználói fiókhoz a címtár tulajdonsága **Invited felhasználói**. 

![Képernyőfelvétel: az ajánlat visszaváltás előtt a felhasználó tulajdonságai](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Meghívó beváltása után

Miután a Vendég felhasználó elfogadta a meghívást, az **forrás** tulajdonság értéke módosul, a vendégfelhasználó identitásszolgáltató alapján.

A vendégfelhasználók számára az 1. állapot a **forrás** van **külső Azure Active Directory**.

![Az ajánlat beváltást követően állapot 1 vendégfelhasználó](media/user-properties/after-redemption-state1.png)

A vendégfelhasználók számára a állapota 2. a **forrás** van **Microsoft Account**.

![Vendégfelhasználó állapota 2 ajánlat beváltást követően](media/user-properties/after-redemption-state2.png)

A vendégfelhasználók számára az állapot 3. és 4 állapot a **forrás** tulajdonsága **Azure Active Directory** vagy **Windows Server Active Directory**a következő szakaszban leírtak szerint.

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Az Azure AD B2B együttműködés felhasználói legfontosabb tulajdonságai
### <a name="usertype"></a>UserType
Ez a tulajdonság azt jelzi, hogy a felhasználót, hogy a gazdagép bérlős kapcsolat. Ez a tulajdonság két értékekkel rendelkezhet:
- Tag: Ez az érték azt jelzi, hogy egy alkalmazott a gazdagép szervezet és a egy felhasználó a szervezet Bérlista a. Például ennek a felhasználónak el kell érnie a belső helyek vár. Ez a felhasználó nem számít egy külső közreműködő.

- Vendég: Ez az érték azt jelzi, hogy a felhasználó, aki nem tekinthető a vállalatnak, például egy külső közreműködő, a partner vagy az ügyfél belső. Az ilyen felhasználók várhatóan nem kap egy Vezérigazgató belső emlékeztető vagy vállalati előnnyel jár, például kap.

  > [!NOTE]
  > A UserType nem rendelkezik kapcsolattal, hogy a felhasználó bejelentkezik, a címtár szerepkörrel, a felhasználó, és így tovább. Ez a tulajdonság csak azt jelzi, hogy a felhasználó kapcsolat a gazdagép-szervezethez, és lehetővé teszi, hogy a szervezet, amely ezt a tulajdonságot függ házirendeknek az érvényesítését.

### <a name="source"></a>Forrás
Ez a tulajdonság azt jelzi, hogy a felhasználó bejelentkezik.

- A meghívott felhasználó: Ez a felhasználó kapott meghívót, de még nem még sikerült beváltani a meghívót.

- Külső Active Directory: Ez a felhasználó külső szervezet tárolt van, és egy Azure AD-fiókot, a másik szervezethez tartozó használatával hitelesíti. Jelentkezzen be az ilyen állapot 1 felel meg.

- Microsoft-fiók: Ez a felhasználó Microsoft-fiókkal van tárolt, és hitelesíti a Microsoft-fiók használatával. Jelentkezzen be az ilyen állapot 2 felel meg.

- A Windows Server Active Directory: Ez a felhasználó bejelentkezett a helyszíni Active Directoryból, hogy a szervezethez tartozik. Jelentkezzen be az ilyen állapot 3 felel meg.

- Azure Active Directory: Ez a felhasználó hitelesíti magát egy az Azure AD-fiókkal, amely a szervezethez tartozik. Jelentkezzen be az ilyen állapot 4 felel meg.
  > [!NOTE]
  > Forrás- és UserType olyan független tulajdonságok. A forrás értéke nem jelenti azt, egy adott érték a UserType.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Az Azure AD B2B-felhasználók vehetők helyett vendégek tagként?
Általában egy Azure AD B2B-felhasználó és a Vendég felhasználó is megfelel. Ezért egy Azure AD B2B együttműködés felhasználói kerülnek be a felhasználó a UserType = Vendég alapértelmezés szerint. Azonban bizonyos esetekben a fiókpartner-szervezet tagja által felügyelt nagyobb szervezettel, amelyhez a gazdagép szervezet is tartozik. Ha igen, a gazdagép szervezet kíván kezelheti a felhasználókat a fiókpartner-szervezet tagjai vendégek helyett. Az Azure AD B2B meghívó Manager API-k használatával adja hozzá, vagy a gazdagép szervezet, amelynek a partnerszervezet felhasználó meghívása.

## <a name="filter-for-guest-users-in-the-directory"></a>A vendégfelhasználók számára a címtárban szűrése

![Képernyőfelvétel: a szűrő a vendégfelhasználók számára](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Konvertálja a UserType
Átalakítása UserType tag Vendég, és ez fordítva PowerShell használatával lehetőség. Azonban a UserType tulajdonság jelöli a felhasználói kapcsolat a szervezet számára. Ezért módosítania kell a tulajdonság csak akkor, ha a kapcsolat a felhasználó a szervezet módosítások. Ha a kapcsolat a felhasználó módosítja, az egyszerű felhasználónév (UPN) módosítani kell? A felhasználó továbbra is ugyanazokhoz az erőforrásokhoz való hozzáférése? Postaláda hozzárendelve? PowerShell-lel atomi tevékenységként a UserType módosítása nem ajánlott. Is abban az esetben ez a tulajdonság nem módosítható válik PowerShell-lel, nem ajánlott ezt az értéket a függőség véve.

## <a name="remove-guest-user-limitations"></a>Távolítsa el a Vendég felhasználói korlátozások
Előfordulhatnak olyan esetekben, ahol kíván adni a vendégfelhasználót magasabb jogosultságokkal. Vendégfelhasználó hozzáadása szerepkörhöz sem, és akár el is távolíthat Vendég alapértelmezés szerinti felhasználói korlátozásokon tagként ugyanazokat a jogosultságokat biztosíthat a címtárban.

Akkor lehet, hogy a vállalat címtárából vendégfelhasználó jogosult tag felhasználóként az alapértelmezett határérték kikapcsolása.

![Képernyőfelvétel: a külső felhasználók beállítás a felhasználói beállítások](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Készíthetek vendégfelhasználók látható az Exchange globális címlista?
Igen. Alapértelmezés szerint Vendég objektumok nem látható a szervezet globális címlista, de használhatja az Azure Active Directory PowerShell láthatóvá tegye őket. További információkért lásd: **lehet Vendég objektumok a globális címlista látható?** a [vendégfelhasználói hozzáférés az Office 365-csoportok felügyelete](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?redirectSourcePath=%252fen-us%252farticle%252fmanage-guest-access-in-office-365-groups-9de497a9-2f5c-43d6-ae18-767f2e6fe6e0&view=o365-worldwide#faq). 

## <a name="next-steps"></a>További lépések

* [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
* [B2B-együttműködés felhasználói jogkivonatok](user-token.md)
* [B2B-együttműködés felhasználói jogcímek társítása](claims-mapping.md)
