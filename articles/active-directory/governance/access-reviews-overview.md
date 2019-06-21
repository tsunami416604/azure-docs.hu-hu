---
title: Mik azok a hozzáférési felülvizsgálatok? – Az azure Active Directory |} A Microsoft Docs
description: Használja az Azure Active Directory hozzáférési felülvizsgálatok, szabályozási, kockázatkezelési és megfelelőségi irányelveinek a szervezet megfelel a csoporttagságot és az alkalmazás hozzáférési csoport szabályozhatja.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/05/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fcc804db66430598e72e9ebf31a8837eda1cca6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67204590"
---
# <a name="what-are-azure-ad-access-reviews"></a>Mik az Azure AD hozzáférési felülvizsgálatok?

Az Azure Active Directory (Azure AD) hozzáférési felülvizsgálatokkal a cégek hatékonyan kezelhetik a csoporttagságokat, a vállalati alkalmazások és a szerepkör-hozzárendelések engedélyezése. Felhasználó hozzáférését, hogy csak a megfelelő emberek számára hozzáférést kaphatnak folyamatos rendszeres időközönként tekinthető meg.

Íme egy videót, amely a hozzáférési felülvizsgálatok gyors áttekintést biztosít:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Miért vannak a hozzáférési felülvizsgálatok fontos?

Az Azure AD lehetővé teszi a szervezeten belül, és a külső szervezetek, például a partnerek a belső adattárakon egymással. Felhasználók csatlakozás csoportokhoz, meghívhatnak vendégeket, felhőalapú alkalmazások csatlakozhat, és távolról dolgozhassanak a munkahelyi vagy személyes eszközökhöz. Önkiszolgáló teljesítményét kihasználva, a kényelem érdekében egyes alkalmakkor teljesítetlen jobb hozzáférés-kezelési lehetőségeit szüksége.

- Az új alkalmazottak való csatlakozás, mert hogyan gondoskodik az produktív megfelelő hozzáférési jogosultsággal rendelkeznek?
- Személyek csapatok áthelyezése, illetve elhagyják a vállalatot, hogyan gondoskodik a régi hozzáférését a rendszer eltávolítja, különösen akkor, ha a vendégek magában foglalja?
- Túl sok hozzáférési jogosultságok feltörések eredményeket és meglátásokat bemutató naplózását, ahogy azt jelzik, hogy a hozzáférés fölötti felügyelet hiánya vezethet.
- Az erőforrás-tulajdonosok, győződjön meg arról, hogy rendszeresen tekintse át ki férhet hozzá az erőforrások proaktív bevonása kell.

## <a name="when-to-use-access-reviews"></a>Mikor érdemes használni a hozzáférési felülvizsgálatok?

- **Kiemelt szerepkörű felhasználók túl sok:** Azt, hogy hány felhasználó rendszergazdai hozzáféréssel rendelkezik-e célszerű, hány, azokat a globális rendszergazdák, ha vannak ilyenek, és meghívhatják a Vendégek vagy partnerek után érvényessége a felhasználóhoz egy felügyeleti feladat nem lettek eltávolítva. Újrahitelesítheti a szerepkör-hozzárendelés felhasználókat a [Azure AD-szerepkörök](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) például a globális rendszergazdák vagy [Azure-erőforrások szerepkörök](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) például a felhasználói hozzáférés rendszergazdája a a [az Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) tapasztalható.
- **Amikor az automation is létre lehessen hozni:** Létrehozhatók olyan szabályok, biztonsági vagy Office 365-csoportok, a dinamikus tagságot, de mi történik a HR-adatokat, ha nem az Azure ad-ben, vagy ha felhasználók továbbra is hozzá kell férniük a csere taníthat be a csoport elhagyása után? Ezután létrehozhat azoknak, akik továbbra is hozzá kell férniük kaphatnak folyamatos hozzáférést biztosítani, hogy a csoport értékelést.
- **Ha egy csoport egy új célra szolgál:** Ha egy csoportot, amelyet szeretne szinkronizálja az Azure ad-hez, vagy ha azt tervezi, hogy az értékesítési csapat csoport minden tagja számára a Salesforce alkalmazás, hasznos lenne, kérje meg a csoport tagságát, a csoport használatban van egy másik kockázati co előtt tekintse át a csoport tulajdonosa tartalomegyezés.
- **Üzleti kritikus fontosságú adatok elérése:** bizonyos erőforrásokhoz, szükséges lehet kívüli személyek kérni, hogy rendszeresen jelentkezzen ki, és adjon egy indoklás miért szükséges hozzáférés naplózási célokra.
- **Egy kivétel házirendlista karbantartásához:** Minden felhasználó egy ideális világában, a hozzáférési szabályzatok a szervezet erőforrásaihoz való hozzáférés biztonsága érdekében követnie. Azonban néha előfordulhatnak olyan üzleti esetek, amelyek kivételek kérik. A rendszergazdáknak kezelheti a feladat, felügyeletét, házirend kivételek elkerülése és auditorok biztosítanak arról, hogy az ilyen kivételek rendszeresen ellenőrizni.
- **Kérje meg a csoporttulajdonosok továbbra is szükségük van a csoporthoz tartozó vendégek megerősítéséhez:** Alkalmazottak hozzáférését egyes, a helyszínen IAM, de nem a meghívott vendégek előfordulhat, hogy automatizálható. Ha egy csoport hozzáférést biztosít a vendégek üzleti bizalmas tartalmat, akkor azt meg a csoporttulajdonos feladata, hogy erősítse meg a vendégek még hozzáférés valós üzleti szüksége.
- **Rendelkezik az ellenőrzések rendszeres időközönként Ismétlődés:** Hetente, havonta, negyedévente vagy évente, ha például beállíthatja a felhasználók set frekvencia ismétlődő a hozzáférési felülvizsgálatok, és a többi felhasználó értesítést kap kritika elején. Felülvizsgálók jóváhagyják vagy megtagadja a hozzáférést egy rövid felülettel és intelligens javaslatokat segítségével.

## <a name="where-do-you-create-reviews"></a>Ahol felülvizsgálatok létrehozni?

Attól függően, amit meg szeretné tekinteni, a hozzáférési felülvizsgálat fog létrehozni az Azure AD hozzáférési felülvizsgálatok, az Azure AD vállalati alkalmazásokat (az előzetes verzió) vagy az Azure AD PIM-ben.

| A felhasználók hozzáférési jogosultságok | Felülvizsgálók is lehet. | Tekintse át a létrehozott | Felülvizsgáló élmény |
| --- | --- | --- | --- |
| Biztonsági csoport tagjai</br>Office-csoport tagjai | Megadott felülvizsgálók</br>Csoporttulajdonosok</br>Önálló áttekintése | Azure AD hozzáférési felülvizsgálatok</br>Az Azure AD-csoportok | Hozzáférési panel |
| Egy csatlakoztatott alkalmazáshoz rendelve | Megadott felülvizsgálók</br>Önálló áttekintése | Azure AD hozzáférési felülvizsgálatok</br>Az Azure AD vállalati alkalmazások (az előzetes verzió) | Hozzáférési panel |
| Az Azure AD-szerepkörhöz | Megadott felülvizsgálók</br>Önálló áttekintése | [Az Azure AD PIM-ben](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure Portal |
| Azure-erőforrás-szerepkör | Megadott felülvizsgálók</br>Önálló áttekintése | [Az Azure AD PIM-ben](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure Portal |

## <a name="which-users-must-have-licenses"></a>Mely felhasználók rendelkeznie kell licencekkel?

Minden felhasználónak, aki a hozzáférési felülvizsgálatok kommunikál egy fizetős Azure AD Premium P2 licenccel kell rendelkeznie. Példák erre vonatkozóan:

- Rendszergazdák, akik a hozzáférési felülvizsgálat létrehozása
- Csoporttulajdonosok hozzáférési végrehajtó áttekintése
- Felülvizsgálók hozzárendelt felhasználók
- Egy önálló áttekintése végrehajtó felhasználók

Vendégfelhasználók tekintse át a saját hozzáférését is kérhet. Minden fizetős Azure AD Premium P2-licenc, amely a saját szervezet felhasználói egyik rendel használhatja az Azure AD--vállalatközi (B2B) alatt a külső felhasználó keretet legfeljebb öt vendégfelhasználók. Ezek a vendég felhasználók is az Azure AD Premium P2-funkciók is használhatók. További információkért lásd: [licencelési útmutató az Azure AD B2B együttműködés](../b2b/licensing-guidance.md).

Az alábbiakban néhány példa forgatókönyvet a segítségével meghatározhatja, hogy hány olyan licencet kell rendelkeznie.

| Forgatókönyv | Számítás | Szükséges licencek száma |
| --- | --- | --- |
| A rendszergazda 500 felhasználó csoportba tartozó hozzáférési felülvizsgálat hoz létre.<br/>Felülvizsgálók mint 3 csoporttulajdonosok rendeli hozzá. | 1 rendszergazda + 3 csoporttulajdonosok | 4 |
| A rendszergazda 500 felhasználó csoportba tartozó hozzáférési felülvizsgálat hoz létre.<br/>Lehetővé teszi egy önálló áttekintése. | 1 rendszergazda + felülvizsgálatban mint 500 felhasználó | 501 |
| A rendszergazda csoport hozzáférési felülvizsgálat hoz létre 5 és 25 vendég felhasználók.<br/>Lehetővé teszi egy önálló áttekintése. | 1 rendszergazda + 5 felhasználó felülvizsgálatban<br/>(vendégfelhasználók terjed ki a szükséges 1:5 aránya) | 6 |
| A rendszergazda csoport hozzáférési felülvizsgálat hoz létre 5 és 28 vendég felhasználók.<br/>Lehetővé teszi egy önálló áttekintése. | 1 rendszergazda + felülvizsgálatban mint 5 felhasználó + 1 felhasználó kötelező 1:5 arányú vendégfelhasználóknak | 7 |

Licencek hozzárendelése a által használt kapcsolatos információkért lásd: [hozzárendelni, vagy távolítsa el az Azure Active Directory portálon licenceit](../fundamentals/license-users-groups.md).

## <a name="learn-about-access-reviews"></a>Ismerje meg a hozzáférési felülvizsgálatok

Létrehozásával és a hozzáférési felülvizsgálatok elvégzése kapcsolatos további tudnivalókért tekintse meg a rövid bemutató:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Ha készen áll a szervezet a hozzáférési felülvizsgálatok üzembe helyezéséhez, kövesse az alábbi lépéseket a videóban felvétele, megkezdheti a rendszergazdák, és az első hozzáférési felülvizsgálat létrehozása.

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="next-steps"></a>További lépések

- [Csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat létrehozása](create-access-review.md)
- [Hozzáférési felülvizsgálat létrehozása Azure AD rendszergazdai szerepkörrel rendelkező felhasználókhoz](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [A csoportokat vagy alkalmazásokat hozzáférés felülvizsgálata](perform-access-review.md)
- [Csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat befejezése](complete-access-review.md)
