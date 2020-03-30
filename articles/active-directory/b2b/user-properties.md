---
title: B2B vendégfelhasználó tulajdonságai – Azure Active Directory | Microsoft dokumentumok
description: Az Azure Active Directory B2B vendégfelhasználói tulajdonságai és állapotai a meghívásbeváltás előtt és után
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40f5002e361653614c966dc43301afa83eb7b200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050792"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Az Azure Active Directory B2B együttműködési felhasználóinak tulajdonságai

Ez a cikk ismerteti a b2B vendégfelhasználói objektum tulajdonságait és állapotait az Azure Active Directoryban (Azure AD) a meghívásbeváltás előtt és után. Az Azure AD-s vállalkozások közötti (B2B) együttműködési felhasználó usertype = Vendég. Ez a vendégfelhasználó általában egy partnerszervezettől származik, és alapértelmezés szerint korlátozott jogosultságokkal rendelkezik a meghívó könyvtárban.

A meghívó szervezet igényeitől függően az Azure AD B2B együttműködési felhasználó jaakövetkező fiókállapotokban lehet:

- 1. állapot: Az Azure AD egy külső példányában található, és a meghívó szervezet vendégfelhasználójaként jelenik meg. Ebben az esetben a B2B-felhasználó bejelentkezik egy Azure AD-fiók használatával, amely a meghívott bérlőhöz tartozik. Ha a partnerszervezet nem használja az Azure AD-t, az Azure AD vendégfelhasználója továbbra is létrejön. A követelmények az, hogy beváltsák a meghívót, és az Azure AD ellenőrzi az e-mail címét. Ez a megállapodás is nevezik a just-in-time (JIT) bérleti vagy a "vírusos" bérleti.

   > [!IMPORTANT]
   > **2021. március 31-től a**Microsoft már nem támogatja a meghívók beváltását nem felügyelt Azure AD-fiókok és bérlők létrehozásával a B2B együttműködési forgatókönyvekhez. Az előkészítés során arra bátorítjuk ügyfeleinket, hogy válasszák az [e-mailes egyszeri jelkód-hitelesítést.](one-time-passcode.md) Örömmel fogadjuk a nyilvános előnézeti funkcióval kapcsolatos visszajelzéseit, és izgatottan várjuk, hogy még több együttműködési módot hozzon létre.

- 2. állapot: Microsoft- vagy más fiókban található, és a gazdaszervezet vendégfelhasználójaként képviselteti magát. Ebben az esetben a vendégfelhasználó Microsoft-fiókkal vagy közösségi fiókkal (google.com vagy hasonló) jelentkezik be. A meghívott felhasználó identitása Microsoft-fiókként jön létre a meghívó szervezet címtárában az ajánlat-beváltás során.

- 3. állapot: A gazdaszervezet helyszíni Active Directoryjában található, és szinkronizálva van a gazdaszervezet Azure AD-jével. Az Azure AD Connect segítségével szinkronizálhatja a partnerfiókokat a felhőben, mint az Azure AD B2B felhasználók UserType = Vendég. Lásd: [Helyileg kezelt partnerfiókok hozzáférése a felhőalapú erőforrásokhoz.](hybrid-on-premises-to-cloud.md)

- 4. állapot: A gazdaszervezet Azure AD-ben található UserType = Vendég és hitelesítő adatok, amelyeket a gazdaszervezet kezel.

  ![A négy felhasználói állapotot ábrázoló diagram](media/user-properties/redemption-diagram.png)


Most nézzük meg, hogyan néz ki egy Azure AD B2B együttműködési felhasználó az Azure AD-ben.

### <a name="before-invitation-redemption"></a>A meghívás előtti visszaváltás

Az 1- es és 2-es állapotfiókok a vendégfelhasználók együttműködésének meghívása a vendégfelhasználók saját hitelesítő adatainak használatával. Amikor a meghívót először elküldi a vendégfelhasználónak, a rendszer fiókot hoz létre a címtárban. Ez a fiók nem rendelkezik hitelesítő adatokkal társítva, mert a hitelesítést a vendégfelhasználó identitásszolgáltatója végzi. A címtárban lévő vendég felhasználói fiók **Forrás** tulajdonsága **A felhasználó meghívására van**állítva. 

![Képernyőkép a felhasználói tulajdonságokról az ajánlat beváltása előtt](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Meghívás után visszaváltás

Miután a vendégfelhasználó elfogadja a meghívást, a **Forrás** tulajdonság frissül a vendégfelhasználó identitásszolgáltatója alapján.

Az 1-es állapotú vendégfelhasználók számára a **forrás** **külső Azure Active Directory.**

![1. állapotú vendégfelhasználó az ajánlat beváltása után](media/user-properties/after-redemption-state1.png)

A 2. **Source** **Microsoft Account**

![2. állapotú vendégfelhasználó az ajánlat beváltása után](media/user-properties/after-redemption-state2.png)

A 3- as és 4-es állapotú vendégfelhasználók számára a **Forrás** tulajdonság az Azure Active Directory vagy a **Windows Server Active Directory** **(A** következő szakaszban leírtakszerint) lesz beállítva.

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Az Azure AD B2B együttműködési felhasználó fő tulajdonságai
### <a name="usertype"></a>UserType (Felhasználótípus)
Ez a tulajdonság a felhasználó és a gazdagép kapcsolatát jelzi. Ennek a tulajdonságnak két értéke lehet:
- Tag: Ez az érték a gazdaszervezet egyik alkalmazottját és a szervezet bérlistáján szereplő felhasználót jelzi. Ez a felhasználó például csak belső webhelyekhez szeretne hozzáférni. Ez a felhasználó nem tekinthető külső közreműködőnek.

- Vendég: Ez az érték azt jelzi, hogy egy felhasználó nem tekinthető a vállalat on belüli, például egy külső munkatárs, partner vagy ügyfél. Egy ilyen felhasználótól nem várható el, hogy megkapja a vezérigazgató belső feljegyzését, vagy például vállalati előnyöket kapjon.

  > [!NOTE]
  > A UserType nem függ ahhoz, hogy a felhasználó hogyan jelentkezik be, a felhasználó könyvtári szerepköre és így tovább. Ez a tulajdonság egyszerűen jelzi a felhasználó kapcsolatát a gazdaszervezettel, és lehetővé teszi a szervezet számára, hogy a tulajdonságtól függő házirendeket kényszerítse.

### <a name="source"></a>Forrás
Ez a tulajdonság azt jelzi, hogy a felhasználó hogyan jelentkezik be.

- Meghívott felhasználó: Ez a felhasználó meghívásra került, de még nem váltott be meghívót.

- Külső Azure Active Directory: Ez a felhasználó egy külső szervezetben található, és hitelesíti egy Azure AD-fiók, amely a másik szervezethez tartozik. Az ilyen típusú bejelentkezés az 1.

- Microsoft-fiók: Ez a felhasználó egy Microsoft-fiókban található, és egy Microsoft-fiókkal hitelesíti magát. Az ilyen típusú bejelentkezés megfelel a 2.

- Windows Server Active Directory: Ez a felhasználó a szervezethez tartozó helyszíni Active Directoryból van bejelentkezve. Az ilyen típusú bejelentkezés megfelel a 3.

- Azure Active Directory: Ez a felhasználó hitelesíti egy Azure AD-fiók, amely ehhez a szervezethez tartozik. Az ilyen típusú bejelentkezés megfelel a 4.
  > [!NOTE]
  > A forrás és a UserType független tulajdonságok. A Forrás értéke nem jelent konkrét értéket a UserType típushoz.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Az Azure AD B2B-felhasználók hozzáadhatók tagok helyett a vendégek?
Általában egy Azure AD B2B-felhasználó és a vendég felhasználó szinonimája. Ezért egy Azure AD B2B együttműködési felhasználó a felhasználó felhasználóként a UserType = Guest alapértelmezés szerint. Bizonyos esetekben azonban a partnerszervezet egy nagyobb szervezet tagja, amelyhez a fogadó szervezet is tartozik. Ha igen, előfordulhat, hogy a gazdaszervezet a partnerszervezet felhasználóit a vendégek helyett tagként szeretné kezelni. Az Azure AD B2B Invitation Manager API-k használatával a partnerszervezetből származó felhasználótagot vehet fel vagy hívhat meg a gazdaszervezethez tagként.

## <a name="filter-for-guest-users-in-the-directory"></a>Szűrő a címtárban lévő vendégfelhasználók számára

![A vendégfelhasználók szűrőjét bemutató képernyőkép](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>UserType konvertálása
A UserType-ot tagról vendégre konvertálhatja, és fordítva a PowerShell használatával. A UserType tulajdonság azonban a felhasználó és a szervezet kapcsolatát jelöli. Ezért ezt a tulajdonságot csak akkor módosítsa, ha a felhasználó és a szervezet kapcsolata megváltozik. Ha a felhasználó kapcsolata megváltozik, az egyszerű felhasználónév (UPN) megváltozik? Továbbra is a felhasználónak kell hozzáférnie ugyanazokhoz az erőforrásokhoz? Hozzá kell rendelni egy postaládát? Nem javasoljuk a UserType módosítása a PowerShell atomi tevékenységként való használatával. Továbbá abban az esetben, ha ez a tulajdonság a PowerShell használatával nem lesz megváltoztathatatlan, nem javasoljuk, hogy függőséget az érték.

## <a name="remove-guest-user-limitations"></a>Vendégfelhasználók korlátozásának eltávolítása
Előfordulhatnak olyan esetek, amikor magasabb szintű jogosultságokat szeretne adni a vendégfelhasználóknak. Vendégfelhasználót bármely szerepkörhöz hozzáadhat, és akár el is távolíthatja a címtáralapértelmezett vendégfelhasználói korlátozásait, hogy a felhasználó nak ugyanazokat a jogosultságokat adja, mint a tagok.

Lehetőség van az alapértelmezett korlátozások kikapcsolására, hogy a vállalati címtárvendégei ugyanolyan engedélyekkel rendelkezhessenek, mint a tagfelhasználó.

![Képernyőkép a Külső felhasználók beállításról a felhasználói beállításokközött](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Láthatóvá tehetem a vendégfelhasználókat az Exchange globális címlistájában?
Igen. Alapértelmezés szerint a vendégobjektumok nem láthatók a szervezet globális címlistájában, de az Azure Active Directory PowerShell segítségével láthatóvá teheti őket. További információt a **Vendégobjektumok láthatóvá tétele a globális címlistában?** A vendéghozzáférés kezelése az [Office 365-csoportokban című témakörben talál.](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?redirectSourcePath=%252fen-us%252farticle%252fmanage-guest-access-in-office-365-groups-9de497a9-2f5c-43d6-ae18-767f2e6fe6e0&view=o365-worldwide#add-guests-to-the-global-address-list) 

## <a name="next-steps"></a>További lépések

* [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
* [B2B együttműködési felhasználói jogkivonatok](user-token.md)
* [B2B együttműködési felhasználói jogcímleképezés](claims-mapping.md)
