---
title: Mik azok a hozzáférési felülvizsgálatok? – Az azure Active Directory |} A Microsoft Docs
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
ms.date: 01/10/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5499c8808c3916842071df1f03a865efd98719f6
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376783"
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
- Egy **csoport új célra való használata esetén:** Ha olyan csoporttal rendelkezik, amely szinkronizálva lesz az Azure AD-vel, vagy ha azt tervezi, hogy engedélyezi az alkalmazás Salesforce a mindenki számára az értékesítési csoport csoportban, akkor érdemes megkérnie a csoport tulajdonosát, hogy tekintse át a csoport tagságát, mielőtt a csoportot más kockázati tartalomban használják.
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


## <a name="create-access-reviews"></a>Hozzáférési felülvizsgálatok létrehozása

Hozzáférési felülvizsgálatok létrehozásához kövesse az alábbi lépéseket:

1. Lépjen a [Azure Portal](https://portal.azure.com) a hozzáférési felülvizsgálatok kezeléséhez, és jelentkezzen be globális rendszergazdaként vagy felhasználói rendszergazdaként.

1. Keresse meg és válassza ki a **Azure Active Directory**.

      ![Azure Active Directory Azure Portal keresése](media/access-reviews-overview/search-azure-active-directory.png)

1. Válassza az **identitás szabályozása**lehetőséget.

1. A kezdeti lépések lapon kattintson a hozzáférés- **ellenőrzés létrehozása** gombra.

   ![Hozzáférési felülvizsgálatok kezdőlapja](./media/access-reviews-overview/access-reviews-overview-create-access-reviews.png) 


## <a name="learn-about-access-reviews"></a>Tudnivalók a hozzáférési felülvizsgálatokról

A hozzáférési felülvizsgálatok létrehozásával és végrehajtásával kapcsolatos további információkért tekintse meg ezt a rövid bemutatót:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Ha készen áll a hozzáférési felülvizsgálatok üzembe helyezésére a szervezetében, kövesse az alábbi lépéseket a videóban a bevezetéshez, a rendszergazdák betanításához és az első hozzáférési felülvizsgálat létrehozásához.

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>Hány licencre van szükség?

Győződjön meg arról, hogy a címtár legalább annyi prémium szintű Azure AD P2-licenccel rendelkezik, mint a következő feladatokat végző alkalmazottakkal:

- Felülvizsgáló hozzárendelt tagok és vendég felhasználók
- Önellenőrzést végző tagok és vendég felhasználók
- Hozzáférési felülvizsgálatot végző tulajdonosok csoportosítása
- Hozzáférési felülvizsgálatot végző alkalmazások tulajdonosai

Prémium szintű Azure AD P2-licencek **nem** szükségesek a következő feladatokhoz:

- A globális rendszergazdai vagy felhasználói rendszergazdai szerepkörrel rendelkező felhasználók számára nem szükséges licenc, amely a hozzáférési felülvizsgálatokat állítja be, konfigurálja a beállításokat, vagy alkalmazza a döntéseket a felülvizsgálatok alapján.

Minden, a saját szervezete felhasználói számára hozzárendelt fizetett prémium szintű Azure AD P2-licenc esetében használhatja az Azure AD Business-to-Business (B2B) használatát, hogy a külső felhasználói támogatás keretében legfeljebb öt vendéget hívjon fel. Ezek a vendég felhasználók prémium szintű Azure AD P2 funkciókat is használhatják. További információ: [Azure ad B2B együttműködés licencelési útmutatója](../b2b/licensing-guidance.md).

További információ a licencekről: [licencek kiosztása vagy eltávolítása a Azure Active Directory portál használatával](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Példa licencelési forgatókönyvekre

Íme néhány példa a licencekre, amelyek segítségével meghatározhatja a szükséges licencek számát.

| Forgatókönyv | Számítás | Licencek száma |
| --- | --- | --- |
| A rendszergazda létrehoz egy hozzáférési felülvizsgálatot az A csoportban a 75-felhasználók és 1 csoport tulajdonosa számára, és a csoport tulajdonosát a véleményezőként rendeli hozzá. | 1 a csoport tulajdonosának licence felülvizsgáló | 1 |
| A rendszergazda az 500-es és 3 csoportbeli tulajdonosok hozzáférési felülvizsgálatát hozza létre a B csoport számára, és a 3 csoport tulajdonosait felülvizsgáló rendeli hozzá. | 3 licenc minden csoport tulajdonosának felülvizsgáló | 3 |
| A rendszergazda 500 felhasználóval hoz létre hozzáférési felülvizsgálatot a B csoport számára. Saját felülvizsgálatot tesz lehetővé. | 500 licenc az egyes felhasználókhoz, mint önfelülvizsgáló | 500 |
| A rendszergazda a C csoport hozzáférési felülvizsgálatát 50 tag felhasználókkal és 25 vendég felhasználóval hozza létre. Saját felülvizsgálatot tesz lehetővé. | 50 licenc minden felhasználóhoz, mint önellenőrzési.<br/>(a vendég felhasználói a szükséges 1:5-os arányban szerepelnek) | 50 |
| A rendszergazda a D csoporthoz tartozó hozzáférési felülvizsgálatot hoz létre 6 tagú felhasználóval és 108 vendég felhasználóval. Saját felülvizsgálatot tesz lehetővé. | 6 licenc minden felhasználó számára, mint önálló véleményezők + 16 további licencek a szükséges 1:5 arányban az összes 108 vendég felhasználó számára. 6 licenc, amely 6\*5 = 30 vendég felhasználóra vonatkozik. A fennmaradó (108-6\*5) = 78 vendég felhasználó, 78/5 = 16 további licenc szükséges. Így összesen 6 + 16 = 22 licenc szükséges. | 22 |

## <a name="next-steps"></a>Következő lépések

- [Csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása](create-access-review.md)
- [Hozzáférési felülvizsgálat létrehozása Azure AD rendszergazdai szerepkörrel rendelkező felhasználókhoz](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Csoportok vagy alkalmazások hozzáférésének ellenőrzése](perform-access-review.md)
- [Csoportok vagy alkalmazások hozzáférési felülvizsgálatának befejezése](complete-access-review.md)
