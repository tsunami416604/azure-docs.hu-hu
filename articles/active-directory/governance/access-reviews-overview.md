---
title: Mik azok a hozzáférési felülvizsgálatok? - Azure Active Directory | Microsoft dokumentumok
description: Az Azure Active Directory hozzáférési felülvizsgálatok használatával szabályozhatja a csoporttagságot és az alkalmazás-hozzáférést, hogy megfeleljen a vállalatirányítási, kockázatkezelési és megfelelőségi kezdeményezéseknek a szervezetben.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262086"
---
# <a name="what-are-azure-ad-access-reviews"></a>Mik azok az Azure AD-hozzáférési felülvizsgálatok?

Az Azure Active Directory (Azure AD) hozzáférési felülvizsgálatok lehetővé teszik a szervezetek számára a csoporttagságok, a vállalati alkalmazásokhoz való hozzáférés és a szerepkör-hozzárendelések hatékony kezelését. A felhasználó hozzáférése rendszeresen ellenőrizhető, hogy csak a megfelelő személyek férhessenek hozzá.

Az alábbiakban egy videó ban olvashat szanaáterről:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Miért fontosak a hozzáférési felülvizsgálatok?

Az Azure AD lehetővé teszi, hogy belsőleg együttműködjön a szervezeten belül és a külső szervezetek, például partnerek felhasználóival. A felhasználók csatlakozhatnak csoportokhoz, meghívhatnak vendégeket, csatlakozhatnak a felhőalapú alkalmazásokhoz, és távolról dolgozhatnak a munkahelyi vagy személyes eszközeikről. Az önkiszolgáló hatalom kihasználásának kényelme jobb hozzáférés-kezelési képességekhez vezetett.

- Az új alkalmazottak csatlakozásával hogyan biztosítják, hogy megfelelő hozzáféréssel rendelkezzenek ahhoz, hogy termelékenyek legyenek?
- Ahogy az emberek csapatokat helyeznek át, vagy elhagyják a vállalatot, hogyan biztosítják a régi hozzáférés ük eltávolítását, különösen, ha vendégekről van szó?
- A túlzott hozzáférési jogok ellenőrzési megállapításokhoz és kompromisszumokhoz vezethetnek, mivel a hozzáférés ellenőrzésének hiányát jelzik.
- Proaktív módon kapcsolatba kell lépnie az erőforrás-tulajdonosokkal annak érdekében, hogy rendszeresen felülvizsgálják, ki férhet hozzá az erőforrásaikhoz.

## <a name="when-to-use-access-reviews"></a>Mikor kell használni a hozzáférési felülvizsgálatokat?

- **Túl sok felhasználó emelt szintű szerepkörben:** Célszerű ellenőrizni, hogy hány felhasználó rendelkezik rendszergazdai hozzáféréssel, közülük hány globális rendszergazda, és hogy vannak-e olyan meghívott vendégek vagy partnerek, akiket nem távolítottak el a felügyeleti feladat elvégzését követően. Újrahitelesítheti a szerepkör-hozzárendelés-felhasználók az [Azure AD-szerepkörökben,](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) például a globális rendszergazdák, vagy [az Azure-erőforrások szerepkörök,](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) például a felhasználói hozzáférés-rendszergazda az [Azure AD kiemelt identitáskezelés (PIM)](../privileged-identity-management/pim-configure.md) élményben.
- **Ha az automatizálás kivitelezhetetlen:** Létrehozhat szabályokat a dinamikus tagsághoz a biztonsági csoportokban vagy az Office 365-csoportokban, de mi van, ha a HR-adatok nem az Azure AD-ben vannak, vagy ha a felhasználóknak továbbra is szükségük van a hozzáférésre a csoport elhagyása után a csere? Ezután létrehozhat egy felülvizsgálatot a csoporton, hogy azok, akiknek továbbra is hozzáférésre van szükségük, folyamatos hozzáféréssel rendelkezzenek.
- **Ha egy csoportot új célra használnak:** Ha van egy csoport, amely szinkronizálni fog az Azure AD-vel, vagy ha azt tervezi, hogy engedélyezi az alkalmazás Salesforce mindenki számára az értékesítési csapat csoport, hasznos lenne kérni a csoport tulajdonosa, hogy tekintse át a csoporttagság előtt a csoport használata egy másik kockázati tartalom.
- **Üzleti szempontból kritikus fontosságú adatokhoz való hozzáférés:** bizonyos erőforrások esetében előfordulhat, hogy meg kell kérnie az informatikai részlegen kívüli személyeket, hogy rendszeresen jelentkezzenek ki, és indokolják meg, hogy miért van szükségük hozzáférésre naplózási célokra.
- **A házirend kivétellistájának karbantartása:** Egy ideális világban minden felhasználó követné a hozzáférési szabályzatokat a szervezet erőforrásaihoz való biztonságos hozzáférés érdekében. Néha azonban vannak olyan üzleti esetek, amelyek kivételeket igényelnek. Informatikai rendszergazdaként kezelheti ezt a feladatot, elkerülheti a házirend-kivételek felügyeletét, és bizonyíthatja, hogy ezeket a kivételeket rendszeresen felülvizsgálják.
- **Kérje meg a csoporttulajdonosokat, hogy erősítsék meg, hogy továbbra is szükségük van a csoportjukban lévő vendégekre:** Előfordulhat, hogy az alkalmazottak hozzáférése automatikus lehet néhány helyszíni IAM-mal, de a meghívott vendégekkel nem. Ha egy csoport hozzáférést biztosít a vendégeknek az üzleti szempontból érzékeny tartalmakhoz, akkor a csoport tulajdonosának felelőssége annak megerősítése, hogy a vendégeknek továbbra is jogos üzleti igényük van a hozzáférésre.
- **A vélemények rendszeresidőközönként ismétlődnek:** Beállíthatja a felhasználók ismétlődő hozzáférési felülvizsgálatát meghatározott gyakorisággal, például heti, havi, negyedéves vagy éves szinten, és az ellenőrzők értesítést kapnak az egyes értékelések elején. A véleményezők jóváhagyhatják vagy megtagadhatják a hozzáférést egy barátságos felülettel és intelligens javaslatok segítségével.

## <a name="where-do-you-create-reviews"></a>Hol hoz létre véleményeket?

Attól függően, hogy mit szeretne áttekinteni, létrehozza a hozzáférési felülvizsgálataz Azure AD-hozzáférés-felülvizsgálatok, az Azure AD vállalati alkalmazások (előzetes verzióban) vagy az Azure AD PIM.

| A felhasználók hozzáférési jogai | Az ellenőrzők | Az ellenőrzés létrehozva: | Véleményezői tapasztalat |
| --- | --- | --- | --- |
| A biztonsági csoport tagjai</br>Office-csoport tagjai | Megadott ellenőrzők</br>Csoporttulajdonosok</br>Önértékelés | Azure AD hozzáférési felülvizsgálatok</br>Azure AD-csoportok | Hozzáférési panel |
| Csatlakoztatott alkalmazáshoz rendelve | Megadott ellenőrzők</br>Önértékelés | Azure AD hozzáférési felülvizsgálatok</br>Azure AD nagyvállalati alkalmazások (előzetes verzióban) | Hozzáférési panel |
| Azure AD szerepkör | Megadott ellenőrzők</br>Önértékelés | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure portál |
| Azure-erőforrás-szerepkör | Megadott ellenőrzők</br>Önértékelés | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure portál |


## <a name="create-access-reviews"></a>Hozzáférési felülvizsgálatok létrehozása

Hozzáférési felülvizsgálatok létrehozásához kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portalon](https://portal.azure.com) a hozzáférési felülvizsgálatok kezeléséhez, és jelentkezzen be globális rendszergazdaként vagy felhasználóként.

1. Keresse meg és válassza ki az **Azure Active Directoryt**.

      ![Az Azure Portal keresés az Azure Active Directoryban](media/access-reviews-overview/search-azure-active-directory.png)

1. Válassza **az Identitás-irányítási lehetőséget.**

1. Az Első lépések lapon kattintson a **Hozzáférés-ellenőrzés létrehozása** gombra.

   ![Access-ellenőrzések kezdőlapja](./media/access-reviews-overview/access-reviews-overview-create-access-reviews.png) 


## <a name="learn-about-access-reviews"></a>További információ a hozzáférés-felülvizsgálatokról

Ha többet szeretne megtudni a hozzáférési ellenőrzések létrehozásáról és végrehajtásáról, tekintse meg ezt a rövid bemutatót:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Ha készen áll a hozzáférési felülvizsgálatok telepítésére a szervezetben, kövesse az alábbi lépéseket a videóban a fedélzetre, tanítsa be a rendszergazdákat, és hozza létre az első hozzáférési felülvizsgálatot!

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>Hány licenccel kell rendelkeznie?

Győződjön meg arról, hogy a címtár legalább annyi Azure AD Premium P2 licenccel rendelkezik, mint ahány alkalmazottal rendelkezik, akik a következő feladatokat fogják ellátni:

- Véleményezőként kijelölt tag- és vendégfelhasználók
- Önellenőrzést végző tag- és vendégfelhasználók
- Hozzáférés-felülvizsgálatot végző csoporttulajdonosok
- Hozzáférés-felülvizsgálatot végző alkalmazástulajdonosok

Az Azure AD Premium P2 licencek **nem** szükségesek a következő feladatokhoz:

- Nincs szükség licencre a globális rendszergazdai vagy felhasználói szerepkörrel rendelkező felhasználók számára, akik hozzáférési felülvizsgálatokat állítanak be, konfigurálják a beállításokat, vagy alkalmazzák az ellenőrzésekből származó döntéseket.

Minden egyes fizetett Azure AD Premium P2 licenc, amely hozzárendeli a saját szervezet felhasználóinak, használhatja az Azure AD-től-business (B2B) legfeljebb öt vendég felhasználók a külső felhasználói keret. Ezek a vendégfelhasználók azure AD Premium P2 funkciókat is használhatnak. További információ: [Azure AD B2B együttműködési licencelési útmutató.](../b2b/licensing-guidance.md)

A licencekről további információt [a Licencek hozzárendelése vagy eltávolítása az Azure Active Directory portálon című témakörben talál.](../fundamentals/license-users-groups.md)

### <a name="example-license-scenarios"></a>Példa licencforgatókönyvekre

Íme néhány példa licencforgatókönyv, amely segít meghatározni a licencek számát.

| Forgatókönyv | Számítás | Licencek száma |
| --- | --- | --- |
| A rendszergazda 75 felhasználóval és 1 csoporttulajdonossal hozza létre az A csoport hozzáférési felülvizsgálatát, és a csoport tulajdonosát rendeli hozzá véleményezőnek. | 1 licenc a csoport tulajdonosának véleményezőként | 1 |
| A rendszergazda 500 felhasználóval és 3 csoporttulajdonossal hozza létre a B csoport hozzáférés-felülvizsgálatát, és a 3 csoporttulajdonost rendeli hozzá ellenőrzőként. | 3 licenc minden csoporttulajdonosszámára véleményezőként | 3 |
| A rendszergazda 500 felhasználóval hozza létre a B csoport hozzáférési felülvizsgálatát. Önértékeléssé teszi. | 500 licenc minden felhasználó számára, mint önellenőrző | 500 |
| A rendszergazda 50 taggal és 25 vendégfelhasználóval hozza létre a C csoport hozzáférési felülvizsgálatát. Önértékeléssé teszi. | 50 licenc minden felhasználó számára, mint önellenőrző.<br/>(a vendégfelhasználók at a szükséges 1:5 arány ban vannak lefedve) | 50 |
| A rendszergazda 6 tagból és 108 vendégfelhasználóból hozza létre a D csoport hozzáférési felülvizsgálatát. Önértékeléssé teszi. | 6 licenc minden felhasználó számára, mint önellenőrző + 16 további licenc, hogy lefedje mind a 108 vendég felhasználót a szükséges 1:5 arányban. 6 licenc, amely\*6 5=30 vendégfelhasználót fed le. A fennmaradó (108-6\*5)=78 vendégfelhasználó esetében 78/5=16 további licenc szükséges. Így összesen 6+16=22 licenc szükséges. | 22 |

## <a name="next-steps"></a>További lépések

- [Csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása](create-access-review.md)
- [Hozzáférési felülvizsgálat létrehozása Azure AD rendszergazdai szerepkörrel rendelkező felhasználókhoz](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Csoportokhoz vagy alkalmazásokhoz való hozzáférés áttekintése](perform-access-review.md)
- [Csoportok vagy alkalmazások hozzáférés-felülvizsgálatának befejezése](complete-access-review.md)
