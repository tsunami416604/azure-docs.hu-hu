---
title: Mik azok a hozzáférési felülvizsgálatok? – Azure Active Directory | Microsoft Docs
description: A Azure Active Directory hozzáférési felülvizsgálatok segítségével szabályozhatja a csoporttagság és az alkalmazások elérését, hogy megfeleljen a szervezet irányítási, kockázatkezelési és megfelelőségi kezdeményezéseinek.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 08/05/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65def17622f026aa4869a4c60e7cb5146d56c5b0
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389525"
---
# <a name="what-are-azure-ad-access-reviews"></a>Mik azok az Azure AD-hozzáférési felülvizsgálatok?

A Azure Active Directory (Azure AD) hozzáférési felülvizsgálatok lehetővé teszik a szervezetek számára a csoporttagságok hatékony kezelését, a vállalati alkalmazások elérését és a szerepkör-hozzárendeléseket. A felhasználó hozzáférését rendszeresen felül lehet vizsgálni, hogy csak a megfelelő személyek férhessenek hozzájuk.

Íme egy videó, amely a hozzáférési felülvizsgálatok gyors áttekintését nyújtja:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Miért fontos a hozzáférési felülvizsgálatok?

Az Azure AD lehetővé teszi, hogy a szervezeten belül és a külső szervezetekkel, például a partnerekkel közösen működjön együtt. A felhasználók csatlakozhatnak a csoportokhoz, meghívhatják a vendégeket, csatlakozhatnak a felhőalapú alkalmazásokhoz, és távolról dolgozhatnak a munkahelyi vagy személyes eszközökről. Az önkiszolgáló energia kihasználásának kényelme a jobb hozzáférés-kezelési képességek szükségességét eredményezte.

- Ahogy új alkalmazottak csatlakoznak, hogyan gondoskodnak arról, hogy a megfelelő hozzáféréssel rendelkezzenek a hatékony munkavégzéshez?
- Ahogy a csapatok áthelyezik a csapatot, vagy elhagyják a vállalatot, hogyan távolítják el a régi hozzáférését, különösen akkor, ha a vendégek?
- A túlzott hozzáférési jogosultságok naplózási eredményekhez és biztonsághoz vezethetnek, mivel a hozzáférés szabályozásának hiányára utalnak.
- Proaktív módon kell felvennie az erőforrás-tulajdonosokat annak érdekében, hogy rendszeresen ellenőrizzék, ki férhet hozzá az erőforrásaihoz.

## <a name="when-to-use-access-reviews"></a>Mikor kell használni a hozzáférési felülvizsgálatokat?

- **Túl sok felhasználó van a Kiemelt szerepkörökben:** Érdemes megfigyelni, hogy hány felhasználó rendelkezik rendszergazdai hozzáféréssel, hány globális rendszergazda, és ha vannak olyan meghívott vendégek vagy partnerek, akik nem lettek eltávolítva a felügyeleti feladatok elvégzése után. A szerepkör-hozzárendelési felhasználókat újra hitelesítheti az [Azure ad-szerepkörökben](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) , például a globális rendszergazdák vagy az [Azure-erőforrások szerepkörei](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) , például a felhasználói hozzáférés RENDSZERGAZDÁJA a [Azure ad Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) felületén.
- **Ha az automatizálás nem valósítható meg:** Létrehozhat szabályokat a dinamikus tagsághoz a biztonsági csoportokban vagy az Office 365-csoportokban, de mi a teendő, ha a HR-információ nem az Azure AD-ban van, vagy ha a felhasználóknak továbbra is hozzáférésre van szükségük a csoport elhagyása után? Ezután létrehozhat egy felülvizsgálatot az adott csoporton annak biztosítása érdekében, hogy a hozzáférést igénylő felhasználók továbbra is hozzáférhessenek.
- Egy **csoport új célra való használata esetén:** Ha olyan csoporttal rendelkezik, amely szinkronizálva lesz az Azure AD-vel, vagy ha azt tervezi, hogy az Salesforce mindenki számára engedélyezi az értékesítési csoport csoport tagjai számára, érdemes megkérnie a csoport tulajdonosát, hogy tekintse át a csoport tagságát, mielőtt a csoportot más kockázati csoporthoz használják. tartalomegyezés.
- **Üzleti szempontból kritikus fontosságú adathozzáférés:** bizonyos erőforrások esetében előfordulhat, hogy meg kell kérnie az azon kívüli személyeket, hogy rendszeresen kijelentkezzenek, és indokolják, hogy miért van hozzáférésük a naplózáshoz.
- **A szabályzatok kivételi listájának fenntartása:** Az ideális világban minden felhasználó a hozzáférési házirendeket követve gondoskodik a szervezet erőforrásaihoz való hozzáférésről. Bizonyos esetekben azonban előfordulhat, hogy kivételeket kell elvégeznie. Rendszergazdaként kezelheti ezt a feladatot, elkerülheti a házirend-kivételek felügyeletét, és biztosíthatja a könyvvizsgálók számára, hogy a kivételeket rendszeresen felülvizsgálják.
- **Kérje meg a csoport tulajdonosait, hogy erősítse meg, hogy továbbra is szükségük van a csoportok** tagjaira: Előfordulhat, hogy az alkalmazotti hozzáférés automatizálható néhány helyszíni IAM-val, de nem meghívott vendégként. Ha egy csoport hozzáférést biztosít a vendégek számára az üzleti szempontból kényes tartalmakhoz, akkor a tulajdonos feladata annak megerősítése, hogy a vendégek még mindig jogos üzleti igényekkel rendelkeznek.
- **Az értékelések rendszeres időközönként ismétlődnek:** A felhasználók ismétlődő hozzáférési felülvizsgálatok állíthatók be, például hetente, havonta, negyedévente vagy évente, a felülvizsgálók pedig az egyes felülvizsgálatok elején lesznek értesítve. A felülvizsgálók elfogadják vagy megtagadhatják a hozzáférést egy felhasználóbarát felülettel és az intelligens javaslatok segítségével.

## <a name="where-do-you-create-reviews"></a>Hol hozhat létre felülvizsgálatokat?

Attól függően, hogy mit szeretne áttekinteni, az Azure AD hozzáférési felülvizsgálatok, az Azure ad Enterprise-alkalmazások (előzetes verzió) vagy az Azure AD PIM használatával hozhatja létre a hozzáférési felülvizsgálatot.

| Felhasználók hozzáférési jogai | A felülvizsgálók lehetnek | Felülvizsgálat létrehozva | Felülvizsgáló felülete |
| --- | --- | --- | --- |
| Biztonsági csoport tagjai</br>Office-csoporttagok | Megadott véleményezők</br>Csoport tulajdonosainak</br>Önálló felülvizsgálat | Azure AD hozzáférési felülvizsgálatok</br>Azure AD-csoportok | Hozzáférési panel |
| Hozzárendelve egy csatlakoztatott alkalmazáshoz | Megadott véleményezők</br>Önálló felülvizsgálat | Azure AD hozzáférési felülvizsgálatok</br>Azure AD Enterprise-alkalmazások (előzetes verzió) | Hozzáférési panel |
| Azure AD-szerepkör | Megadott véleményezők</br>Önálló felülvizsgálat | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure Portal |
| Azure-erőforrás szerepkör | Megadott véleményezők</br>Önálló felülvizsgálat | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure Portal |


## <a name="onboard-access-reviews"></a>A fedélzeti hozzáférési felülvizsgálatok

A hozzáférési felülvizsgálatok előkészítéséhez kövesse az alábbi lépéseket.

1. Globális rendszergazdaként vagy felhasználói rendszergazdaként jelentkezzen be arra a [Azure Portal](https://portal.azure.com) , ahol hozzáférési felülvizsgálatokat kíván használni.

1. A bal oldali navigációs sávon kattintson a **Azure Active Directory**elemre.

1. A bal oldali menüben kattintson az **identitások szabályozása**elemre.

1. Kattintson a **hozzáférési felülvizsgálatok**lehetőségre.
 
    ![Hozzáférési felülvizsgálatok kezdőlapja](./media/access-reviews-overview/access-reviews-overview-onboard.png)

1. A lapon kattintson a beléptetés **most** gombra.
    
      ![Hozzáférési felülvizsgálatok előkészítése](./media/access-reviews-overview/access-reviews-overview-select-onboard.png)


## <a name="learn-about-access-reviews"></a>Tudnivalók a hozzáférési felülvizsgálatokról

A hozzáférési felülvizsgálatok létrehozásával és végrehajtásával kapcsolatos további információkért tekintse meg ezt a rövid bemutatót:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Ha készen áll a hozzáférési felülvizsgálatok üzembe helyezésére a szervezetében, kövesse az alábbi lépéseket a videóban a bevezetéshez, a rendszergazdák betanításához és az első hozzáférési felülvizsgálat létrehozásához.

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="which-users-must-have-licenses"></a>Mely felhasználóknak kell licenccel rendelkezniük?

Minden, a hozzáférési felülvizsgálattal kommunikáló felhasználónak fizetős prémium szintű Azure AD P2 licenccel kell rendelkeznie. Példák:

- Hozzáférési felülvizsgálatot létrehozó rendszergazdák
- Hozzáférési felülvizsgálatot végző tulajdonosok csoportosítása
- Felülvizsgáló hozzárendelt felhasználók
- Önellenőrzést végző felhasználók

Megkérheti a vendég felhasználókat, hogy vizsgálják felül a saját hozzáférését. Minden, a saját szervezete felhasználói számára hozzárendelt fizetett prémium szintű Azure AD P2-licenc esetében használhatja az Azure AD Business-to-Business (B2B) használatát, hogy a külső felhasználói támogatás keretében legfeljebb öt vendéget hívjon fel. Ezek a vendég felhasználók prémium szintű Azure AD P2 funkciókat is használhatják. További információ: [Azure ad B2B együttműködés licencelési útmutatója](../b2b/licensing-guidance.md).

Íme néhány példa a szükséges licencek számának meghatározására.

| Alkalmazási helyzet | Számítás | Szükséges licencek száma |
| --- | --- | --- |
| A rendszergazda létrehoz egy hozzáférési felülvizsgálatot az A csoportban, 500 felhasználóval. A 3 csoport tulajdonosait felülvizsgáló rendeli hozzá. | 1 licenc a rendszergazda + 3 licenchez minden csoport tulajdonosának felülvizsgáló való használata esetén. | 4 |
| A rendszergazda létrehoz egy hozzáférési felülvizsgálatot az A csoportban, 500 felhasználóval. Saját felülvizsgálatot tesz lehetővé. | 1 licenc a rendszergazda + 500-licencekhez az egyes felhasználókhoz, mint önellenőrzéses. | 501 |
| A rendszergazda 5 felhasználót és 25 vendég felhasználót hoz létre a B csoport hozzáférési felülvizsgálatával. Saját felülvizsgálatot tesz lehetővé. | 1 licenc a rendszergazda + 5 licenchez minden felhasználóhoz, mint önellenőrzési.<br/>(a vendég felhasználói a szükséges 1:5-os arányban szerepelnek) | 6 |
| A rendszergazda a C csoport hozzáférési felülvizsgálatát 5 felhasználóval és 108 vendég felhasználóval hozza létre. Saját felülvizsgálatot tesz lehetővé. | 1 licenc a rendszergazda + 5 licenc minden felhasználóhoz, mint önálló véleményezők + 16 további licencek a szükséges 1:5 arányban az összes 108 vendég felhasználó számára.<br/>1 + 5 = 6 licenc, amely 5 @ no__t-06 = 30 vendég felhasználóra vonatkozik. A fennmaradó (108-5 @ no__t-06) = 78 vendég felhasználó, 78/5 = 16 további licenc szükséges. Így összesen 6 + 16 = 22 licenc szükséges. | 22 |

További információ a licencek felhasználási módjaihoz való hozzárendeléséről: [licencek kiosztása vagy eltávolítása a Azure Active Directory portál használatával](../fundamentals/license-users-groups.md).

## <a name="next-steps"></a>Következő lépések

- [Csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása](create-access-review.md)
- [Hozzáférési felülvizsgálat létrehozása Azure AD rendszergazdai szerepkörrel rendelkező felhasználókhoz](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Csoportok vagy alkalmazások hozzáférésének ellenőrzése](perform-access-review.md)
- [Csoportok vagy alkalmazások hozzáférési felülvizsgálatának befejezése](complete-access-review.md)
