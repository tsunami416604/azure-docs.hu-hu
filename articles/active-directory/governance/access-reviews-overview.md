---
title: Mik az Azure AD hozzáférési felülvizsgálatok? | Microsoft Docs
description: Az Azure Active Directory hozzáférési felülvizsgálatok segítségével, szabályozási, kockázatkezelési és megfelelőségi irányelveinek a szervezet megfelel a csoporttagságot és az alkalmazás hozzáférési csoport szabályozhatja.
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
ms.date: 01/18/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5fa81e564cf28f8b5478abe9ec5bb63d0f11bd3
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57449665"
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

- **Kiemelt szerepkörű felhasználók túl sok:** Azt, hogy hány felhasználó rendszergazdai hozzáféréssel rendelkezik-e célszerű, hány, azokat a globális rendszergazdák, ha vannak ilyenek, és meghívhatják a Vendégek vagy partnerek után érvényessége a felhasználóhoz egy felügyeleti feladat nem lettek eltávolítva. Újrahitelesítheti a szerepkör-hozzárendelés felhasználókat a [az Azure AD-címtárbeli szerepkörök](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) például a globális rendszergazdák vagy [Azure-erőforrások szerepkörök](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) például a felhasználói hozzáférés rendszergazdája a a [Azure ad-ben Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) tapasztalható.
- **Amikor az automation is létre lehessen hozni:** Létrehozhatók olyan szabályok, biztonsági vagy Office 365-csoportok, a dinamikus tagságot, de mi történik a HR-adatokat, ha nem az Azure ad-ben, vagy ha felhasználók továbbra is hozzá kell férniük a csere taníthat be a csoport elhagyása után? Ezután létrehozhat azoknak, akik továbbra is hozzá kell férniük kaphatnak folyamatos hozzáférést biztosítani, hogy a csoport értékelést.
- **Ha egy csoport egy új célra szolgál:** Ha egy csoportot, amelyet szeretne szinkronizálja az Azure ad-hez, vagy ha azt tervezi, hogy az értékesítési csapat csoport minden tagja számára a Salesforce alkalmazás, hasznos lenne, kérje meg a csoport tagságát, a csoport használatban van egy másik kockázati co előtt tekintse át a csoport tulajdonosa tartalomegyezés.
- **Üzleti kritikus fontosságú adatok elérése:** bizonyos erőforrásokhoz, szükséges lehet kívüli személyek kérni, hogy rendszeresen jelentkezzen ki, és adjon egy indoklás miért szükséges hozzáférés naplózási célokra.
- **Egy kivétel házirendlista karbantartásához:** Az ideális világában a minden felhasználó követnie a hozzáférési szabályzatok a munkahelyi erőforrásokhoz való hozzáférés biztonságossá tétele érdekében. Azonban néha előfordulhatnak olyan üzleti esetek, amelyek kivételek kérik. A rendszergazdáknak kezelheti a feladat, felügyeletét, házirend kivételek elkerülése és auditorok biztosítanak arról, hogy az ilyen kivételek rendszeresen ellenőrizni.
- **Kérje meg a csoporttulajdonosok továbbra is szükségük van a csoporthoz tartozó vendégek megerősítéséhez:** Alkalmazottak hozzáférését egyes, a helyszínen IAM, de nem a meghívott vendégek előfordulhat, hogy automatizálható. Ha egy csoport hozzáférést biztosít a vendégek üzleti bizalmas tartalmat, akkor azt meg a csoporttulajdonos feladata, hogy erősítse meg a vendégek még hozzáférés valós üzleti szüksége.
- **Rendelkezik az ellenőrzések rendszeres időközönként Ismétlődés:** Hetente, havonta, negyedévente vagy évente, ha például beállíthatja a felhasználók set frekvencia ismétlődő a hozzáférési felülvizsgálatok, és a többi felhasználó értesítést kap kritika elején. Felülvizsgálók jóváhagyják vagy megtagadja a hozzáférést egy rövid felülettel és intelligens javaslatokat segítségével.

## <a name="where-do-you-create-reviews"></a>Ahol felülvizsgálatok létrehozni?

Attól függően, amit meg szeretné tekinteni a hozzáférési felülvizsgálat hoz létre az Azure AD hozzáférési felülvizsgálatok, az Azure AD vállalati alkalmazásokat (az előzetes verzió) vagy az Azure AD PIM-ben.

| A felhasználók hozzáférési jogosultságok | Felülvizsgálók is lehet. | Tekintse át a létrehozott | Felülvizsgáló élmény |
| --- | --- | --- | --- |
| Biztonsági csoport tagjai</br>Office-csoport tagjai | Megadott felülvizsgálók</br>Csoporttulajdonosok</br>Önkiszolgáló áttekintése | Az Azure AD hozzáférési felülvizsgálatok</br>Az Azure AD-csoportok | Hozzáférési panel |
| Egy csatlakoztatott alkalmazáshoz rendelve | Megadott felülvizsgálók</br>Önkiszolgáló áttekintése | Az Azure AD hozzáférési felülvizsgálatok</br>Az Azure AD vállalati alkalmazások (az előzetes verzió) | Hozzáférési panel |
| Az Azure AD-címtárbeli szerepkört | Megadott felülvizsgálók</br>Önkiszolgáló áttekintése | Az Azure AD PIM-ben | Azure Portal |
| Azure-erőforrás-szerepkör | Megadott felülvizsgálók</br>Önkiszolgáló áttekintése | Az Azure AD PIM-ben | Azure Portal |

## <a name="prerequisites"></a>Előfeltételek

A hozzáférési felülvizsgálatok használandó kell rendelkeznie a következő licencek egyikét:

- Prémium szintű Azure AD P2
- Enterprise Mobility + Security (EMS) E5 licenc

További információkért lásd: [hogyan: Regisztráljon az Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) vagy [Enterprise Mobility + Security E5 próba](https://aka.ms/emse5trial).

## <a name="get-started-with-access-reviews"></a>Ismerkedés a hozzáférési felülvizsgálatok

Létrehozásával és a hozzáférési felülvizsgálatok elvégzése kapcsolatos további tudnivalókért tekintse meg a rövid bemutató:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Ha készen áll a szervezet a hozzáférési felülvizsgálatok üzembe helyezéséhez, kövesse az alábbi lépéseket a videóban felvétele, megkezdheti a rendszergazdák, és az első hozzáférési felülvizsgálat létrehozása.

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="enable-access-reviews"></a>Engedélyezze a hozzáférési felülvizsgálatok

A hozzáférési felülvizsgálatok engedélyezéséhez kövesse az alábbi lépéseket.

1. Egy globális rendszergazdai vagy a felhasználóifiók-adminisztrátor, jelentkezzen be a [az Azure portal](https://portal.azure.com) ahol hozzáférés használni kívánt ellenőrzi.

1. Kattintson a **minden szolgáltatás** és keresse meg a hozzáférési felülvizsgálatok szolgáltatás.

1. Kattintson a **hozzáférési felülvizsgálatokkal**.

    ![Minden szolgáltatás - hozzáférési felülvizsgálatok](./media/access-reviews-overview/all-services-access-reviews.png)

1. Kattintson a navigációs lista **verziójába való felvételével** megnyitásához a **a hozzáférési felülvizsgálatok előkészítése** lap.

    ![A hozzáférési felülvizsgálatok előkészítése](./media/access-reviews-overview/onboard-button.png)

1. Kattintson a **létrehozás** hozzáférésének engedélyezésére ellenőrzi az aktuális könyvtárban található.

    ![A hozzáférési felülvizsgálatok előkészítése](./media/access-reviews-overview/onboard-access-reviews.png)

    A következő indításakor hozzáférési felülvizsgálatok, a hozzáférési felülvizsgálati beállítások engedélyezve lesz.

    ![A hozzáférési felülvizsgálatok engedélyezve](./media/access-reviews-overview/access-reviews-enabled.png)

## <a name="next-steps"></a>További lépések

- [Csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat létrehozása](create-access-review.md)
- [Hozzáférési felülvizsgálat létrehozása Azure AD rendszergazdai szerepkörrel rendelkező felhasználókhoz](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [A csoportokat vagy alkalmazásokat hozzáférés felülvizsgálata](perform-access-review.md)
- [Csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat befejezése](complete-access-review.md)
