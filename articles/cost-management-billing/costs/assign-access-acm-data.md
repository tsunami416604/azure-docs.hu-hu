---
title: Az Azure Cost Management adataihoz való hozzáférés hozzárendelése
description: Ez a cikk azt mutatja be, hogyan rendelhet hozzá az Azure Cost Management adataira vonatkozó engedélyeket a különböző hozzáférési hatókörök esetén.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.custom: secdec18
ms.openlocfilehash: 84637f74408724fec300a2a5cb49cd9f460ed395
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201084"
---
# <a name="assign-access-to-cost-management-data"></a>A Cost Management adataihoz való hozzáférés hozzárendelése

Az Azure nagyvállalati szerződéssel rendelkező felhasználók esetén az Azure Portalon és az Enterprise (EA) Portalon megadott engedélyek együttese határozza meg, hogy a felhasználók milyen szintű hozzáféréssel rendelkeznek az Azure Cost Management adataihoz. Más típusú Azure-fiókkal rendelkező felhasználók esetén a felhasználóknak a Cost Management adataihoz való hozzáférési szintje egyszerűbben határozható meg az Azure szerepköralapú hozzáférés-vezérlésével. Ez a cikk a Cost Management adataihoz való hozzáférés hozzárendelését mutatja be. Az engedélyek együttesének hozzárendelését követően a felhasználó annak a hatókörnek az alapján tekintheti meg a Cost Management adatait, amelyhez hozzáférése van, illetve amelyet kiválaszt az Azure Portalon.

A felhasználó által kiválasztott hatókör az adatkonszolidációt és a költségadatokhoz való hozzáférés szabályozását határozza meg a költségkezelés során. A hatókörök használatakor a felhasználók nem választhatnak ki egyszerre több hatókört. Ehelyett egy magasabb szintű hatókör választhatnak ki, amely alá több, alacsonyabb szintű gyermekhatókör is tartozik, és szűrő alkalmazásával kereshetik meg a kívánt adatokat. Az adatkonszolidáció azért fontos, mert nem minden személy rendelkezhet hozzáféréssel egy szülőhatókörhöz, amely több gyermekhatókört is magába foglal.

A [How to assign access with Azure Cost Management](https://www.youtube.com/watch?v=J997ckmwTa8) (Hozzáférés hozzárendelése az Azure Cost Managementtel) című videóból megtudhatja, hogyan rendelhet hozzá hozzáférést az Azure szerepköralapú hozzáférés-vezérlésével a költségek és díjak megtekintéséhez.

>[!VIDEO https://www.youtube.com/embed/J997ckmwTa8]

## <a name="cost-management-scopes"></a>Cost Management-hatókörök

A Cost Management számos különféle Azure-fióktípust támogat. A támogatott fióktípusok teljes listáját lásd: [A Cost Management adatainak értelmezése](understand-cost-mgt-data.md). A fiók típusa határozza meg az elérhető hatóköröket.

### <a name="azure-ea-subscription-scopes"></a>Azure EA-előfizetések hatókörei

Az Azure EA-előfizetések költségadatainak megtekintéséhez a felhasználóknak legalább olvasási jogosultsággal kell rendelkezniük az alábbi hatókörök legalább egyikéhez.

| **Hatókör** | **Meghatározás helye** | **Az adatok megtekintéséhez szükséges hozzáférés** | **Nagyvállalati Szerződés előfeltételként szükséges beállítása** | **Adatok összesítésének helye** |
| --- | --- | --- | --- | --- |
| Számlázási fiók<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Vállalati rendszergazda | None | A Nagyvállalati Szerződésben foglalt összes előfizetés |
| Részleg | [https://ea.azure.com](https://ea.azure.com/) | Részlegszintű rendszergazda | **Díjtételek megtekintése a részlegszintű rendszergazda számára** engedélyezve | A részleghez kapcsolt regisztrációs fiókhoz tartozó összes előfizetés |
| Regisztrációs fiók<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | Fióktulajdonos | **Díjtételek megtekintése a fióktulajdonos számára** engedélyezve | A regisztrációs fiókhoz tartozó összes előfizetés |
| Felügyeleti csoport | [https://portal.azure.com](https://portal.azure.com/) | Cost Management-olvasó (vagy olvasó) | **Díjtételek megtekintése a fióktulajdonos számára** engedélyezve | A felügyeleti csoport alá tartozó összes előfizetés |
| Előfizetés | [https://portal.azure.com](https://portal.azure.com/) | Cost Management-olvasó (vagy olvasó) | **Díjtételek megtekintése a fióktulajdonos számára** engedélyezve | Az előfizetésben szereplő összes erőforrás/erőforráscsoport |
| Erőforráscsoport | [https://portal.azure.com](https://portal.azure.com/) | Cost Management-olvasó (vagy olvasó) | **Díjtételek megtekintése a fióktulajdonos számára** engedélyezve | Az erőforráscsoportban található összes erőforrás |

<sup>1</sup> A számlázási fiókot Nagyvállalati Szerződésnek vagy Beléptetésnek is nevezik.

<sup>2</sup> A regisztrációs fiókra fióktulajdonos néven is hivatkoznak.


## <a name="other-azure-account-scopes"></a>Egyéb Azure-fiókok hatókörei

Az egyéb Azure-előfizetések költségadatainak megtekintéséhez a felhasználóknak legalább olvasási jogosultsággal kell rendelkezniük az alábbi hatókörök legalább egyikéhez:

- Azure-fiók
- Felügyeleti csoport
- Erőforráscsoport

Számos különféle hatókör érhető el, miután a partnerek előkészítik az ügyfeleket egy Microsoft-ügyfélszerződéshez. A CSP-ügyfelek ezután akkor használhatják a Cost Management funkcióit, ha a CSP-partnerük engedélyezte őket. További információ: [Ismerkedés a partnerek számára készült Azure Cost Management szolgáltatással](get-started-partners.md).

## <a name="enable-access-to-costs-in-the-ea-portal"></a>Költségekhez való hozzáférés engedélyezése az EA Portalon

A részleg hatókör esetén a **Díjtételek megtekintése a részlegszintű rendszergazda számára** beállítást **Engedélyezve** értékűre kell beállítani az EA Portalon. A többi hatókör esetén a **Díjtételek megtekintése a fióktulajdonos számára** beállítást kell **Engedélyezve** értékűre beállítani az EA Portalon.

A beállítások engedélyezése:

1. Jelentkezzen be az EA Portalra a [https://ea.azure.com](https://ea.azure.com) címen egy vállalati rendszergazdai fiókkal.
2. A bal oldali panelen válassza a **Kezelés** lehetőséget.
3. Engedélyezze a **Díjtételek megtekintése a részlegszintű rendszergazda számára** és/vagy a **Díjtételek megtekintése a fióktulajdonos számára** díjtétel-beállítást azoknál a Cost Management-hatóköröknél, amelyekhez hozzáférést szeretne biztosítani.  
    ![Díjtételek megtekintése a részlegszintű rendszergazda és a fióktulajdonos számára díjtétel-beállítások a Regisztráció lapon](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

A díjtételek megtekintésére vonatkozó beállítások engedélyezése után a hatókörök többsége megköveteli szerepköralapú hozzáférés-vezérlési (RBAC-) engedélyek konfigurálását az Azure Portalon.

## <a name="enterprise-administrator-role"></a>Vállalati rendszergazdai szerepkör

Alapértelmezés szerint a vállalati rendszergazda hozzáférhet a számlázási fiókhoz (Nagyvállalati Szerződés/beléptetés) és az összes többi hatókörhöz, amelyek gyermekhatókörök. A vállalati rendszergazda rendelhet a hatókörökhöz való hozzáférést más felhasználókhoz. Az üzletmenet folytonosságának biztosítása érdekében az ajánlott eljárás az, hogy mindig legyen két, vállalati rendszergazdai hozzáféréssel rendelkező felhasználó a vállalatban. Az alábbi szakaszok példái bemutatják, hogyan rendelhet a vállalati rendszergazda a hatókörökhöz való hozzáférést más felhasználókhoz.

## <a name="assign-billing-account-scope-access"></a>Hozzáférés hozzárendelése a számlázási fiók hatókörhöz

A számlázási fiók hatókörhöz való hozzáféréshez vállalati szintű rendszergazdai engedély szükséges az EA Portalon. A vállalati rendszergazda a teljes EA-regisztrációban vagy több regisztrációban is rendelkezik jogosultsággal a költségek megtekintéséhez. Az Azure Portalon nem kell műveletet végrehajtani a számlázási fiók hatókör esetén.

1. Jelentkezzen be az EA Portalra a [https://ea.azure.com](https://ea.azure.com) címen egy vállalati rendszergazdai fiókkal.
2. A bal oldali panelen válassza a **Kezelés** lehetőséget.
3. A **Regisztráció** lapon válassza ki a kezelni kívánt regisztrációt.  
    ![a regisztráció kiválasztása az EA Portalon](./media/assign-access-acm-data/ea-portal.png)
4. Kattintson a **+ Adminisztrátor hozzáadása** elemre.
5. Az Adminisztrátor hozzáadása panelen válassza ki a hitelesítés típusát, és adja meg a felhasználó e-mail-címét.
6. Amennyiben a felhasználó csak olvasási hozzáféréssel rendelkezhet a költség- és használati adatokhoz, a **Csak olvasás** területen jelölje be az **Igen** választógombot.  Egyéb esetben a **Nem** választógombot jelölje be.
7. Kattintson a **Hozzáadás** gombra a fiók létrehozásához.  
    ![példaadatok az Adminisztrátor hozzáadása panelen](./media/assign-access-acm-data/add-admin.png)

Akár 30 percet is igénybe vehet, mire az új felhasználó hozzáférhet a Cost Management adataihoz.

### <a name="assign-department-scope-access"></a>Hozzáférés hozzárendelése a részleg hatókörhöz

A részleg hatókörhöz való hozzáféréshez a részlegszintű rendszergazdai (Díjtételek megtekintése a részlegszintű rendszergazda számára) hozzáférés szükséges az EA Portalon. A részlegszintű rendszergazda a részleghez vagy több részleghez kapcsolódó költség- és használati adatok megtekintésére rendelkezik jogosultsággal. A részleg adatai magukban foglalják a részleghez kapcsolt regisztrációs fiókhoz tartozó összes előfizetést. Az Azure Portalon nem kell műveletet végrehajtani.

1. Jelentkezzen be az EA Portalra a [https://ea.azure.com](https://ea.azure.com) címen egy vállalati rendszergazdai fiókkal.
2. A bal oldali panelen válassza a **Kezelés** lehetőséget.
3. A **Regisztráció** lapon válassza ki a kezelni kívánt regisztrációt.
4. Kattintson a **Részleg** lapra, majd az **Adminisztrátor hozzáadása** elemre.
5. A Részlegszintű rendszergazda hozzáadása panelen válassza ki a hitelesítés típusát, és adja meg a felhasználó e-mail-címét.
6. Amennyiben a felhasználó csak olvasási hozzáféréssel rendelkezhet a költség- és használati adatokhoz, a **Csak olvasás** területen jelölje be az **Igen** választógombot.  Egyéb esetben a **Nem** választógombot jelölje be.
7. Válassza ki a részlegeket, amelyekre vonatkozóan részlegszintű rendszergazdai engedélyt szeretne adni.
8. Kattintson a **Hozzáadás** gombra a fiók létrehozásához.  
    ![a kötelező adatok megadása a Részlegszintű rendszergazda hozzáadása panelen](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>Hozzáférés hozzárendelése a regisztrációs fiók hatókörhöz

A regisztrációs fiók hatókörhöz való hozzáféréshez a fióktulajdonosi (Díjtételek megtekintése a fióktulajdonos számára) hozzáférés szükséges az EA Portalon. A fióktulajdonos a regisztrációs fiókban létrehozott előfizetésekhez kapcsolódó költség- és használati adatokat tekintheti meg. Az Azure Portalon nem kell műveletet végrehajtani.

1. Jelentkezzen be az EA Portalra a [https://ea.azure.com](https://ea.azure.com) címen egy vállalati rendszergazdai fiókkal.
2. A bal oldali panelen válassza a **Kezelés** lehetőséget.
3. A **Regisztráció** lapon válassza ki a kezelni kívánt regisztrációt.
4. Kattintson a **Fiók** lapra, majd a **Fiók hozzáadása** elemre.
5. A Fiókok hozzáadása panelen válassza ki azt a **részleget**, amelyhez a fiókot társítani szeretni, vagy hagyja a fiókot hozzárendelés nélkül.
6. Válassza ki a hitelesítés típusát, és adja meg a fiók nevét.
7. Adja meg a felhasználó e-mail-címet, majd igény szerint a költséghelyet.
8. Kattintson a **Hozzáadás** gombra a fiók létrehozásához.  
    ![a kötelező adatok megadása a regisztrációs fiók Fiók hozzáadása paneljén](./media/assign-access-acm-data/add-account.png)

A fenti lépések elvégzése után a felhasználói fiókból regisztrációs fiók lesz az Enterprise Portalon, és létrehozhat előfizetéseket. A felhasználó hozzáférhet az általa létrehozott előfizetések költség- és használati adataihoz.

## <a name="assign-management-group-scope-access"></a>Hozzáférés hozzárendelése a felügyeleti csoport hatókörhöz

A felügyeleti csoport hatókör megtekintéséhez legalább Cost Management-olvasó (vagy olvasó) engedély szükséges. A felügyeleti csoportokra vonatkozó engedélyeket az Azure Portalon konfigurálhatja. Legalább a Felhasználói hozzáférés rendszergazdája (vagy Tulajdonos) engedéllyel kell rendelkeznie a felügyeleti csoportra vonatkozóan ahhoz, hogy másoknak engedélyezhesse a hozzáférést. Az Azure EA-fiókok esetén pedig engedélyeznie kell a **Díjtételek megtekintése a fióktulajdonos számára** beállítást is az Azure Portalon.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Az oldalsávon válassza a **Minden szolgáltatás** lehetőséget, keressen rá a _felügyeleti csoportok_ kifejezésre, és válassza a **felügyeleti csoportok** elemet.
3. Válassza ki a felügyeleti csoportot a hierarchiában.
4. Kattintson a felügyeleti csoport neve melletti a **Részletek** lehetőségre.
5. A bal oldali panelen válassza a **Hozzáférés-vezérlés (IAM)** elemet.
6. Kattintson a **Hozzáadás** parancsra.
7. A **Szerepkör** területen válassza a **Cost Management-olvasó** lehetőséget.
8. A **Hozzáférés hozzárendelése** területen válassza az **Azure AD, felhasználó, csoport vagy alkalmazás** elemet.
9. Hozzáférés hozzárendeléséhez keresse meg, majd válassza ki a felhasználót.
10. Kattintson a **Save** (Mentés) gombra.  
    ![példaadatok a felügyeleti csoport Engedélyek hozzáadása paneljén](./media/assign-access-acm-data/add-permissions.png)

## <a name="assign-subscription-scope-access"></a>Hozzáférés hozzárendelése az előfizetés hatókörhöz

Az előfizetéshez való hozzáféréshez legalább Cost Management-olvasó (vagy olvasó) engedély szükséges. Az előfizetésekre vonatkozó engedélyeket az Azure Portalon konfigurálhatja. Legalább a Felhasználói hozzáférés rendszergazdája (vagy Tulajdonos) engedéllyel kell rendelkeznie az előfizetésre vonatkozóan ahhoz, hogy másoknak engedélyezhesse a hozzáférést. Az Azure EA-fiókok esetén pedig engedélyeznie kell a **Díjtételek megtekintése a fióktulajdonos számára** beállítást is az Azure Portalon.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Az oldalsávon válassza a **Minden szolgáltatás** lehetőséget, keressen rá az _előfizetések_ kifejezésre, és válassza az **Előfizetések** elemet.
3. Válassza ki előfizetését.
4. A bal oldali panelen válassza a **Hozzáférés-vezérlés (IAM)** elemet.
5. Kattintson a **Hozzáadás** parancsra.
6. A **Szerepkör** területen válassza a **Cost Management-olvasó** lehetőséget.
7. A **Hozzáférés hozzárendelése** területen válassza az **Azure AD, felhasználó, csoport vagy alkalmazás** elemet.
8. Hozzáférés hozzárendeléséhez keresse meg, majd válassza ki a felhasználót.
9. Kattintson a **Save** (Mentés) gombra.

## <a name="assign-resource-group-scope-access"></a>Hozzáférés hozzárendelése az erőforráscsoport hatókörhöz

Az erőforráscsoporthoz való hozzáféréshez legalább Cost Management-olvasó (vagy olvasó) engedély szükséges. Az erőforráscsoportokra vonatkozó engedélyeket az Azure Portalon konfigurálhatja. Legalább a Felhasználói hozzáférés rendszergazdája (vagy Tulajdonos) engedéllyel kell rendelkeznie az erőforráscsoportra vonatkozóan ahhoz, hogy másoknak engedélyezhesse a hozzáférést. Az Azure EA-fiókok esetén pedig engedélyeznie kell a **Díjtételek megtekintése a fióktulajdonos számára** beállítást is az Azure Portalon.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Az oldalsávon válassza a **Minden szolgáltatás** lehetőséget, keressen rá az _erőforráscsoportok_ kifejezésre, és válassza az **Erőforráscsoportok** elemet.
3. Válassza ki az erőforráscsoportot.
4. A bal oldali panelen válassza a **Hozzáférés-vezérlés (IAM)** elemet.
5. Kattintson a **Hozzáadás** parancsra.
6. A **Szerepkör** területen válassza a **Cost Management-olvasó** lehetőséget.
7. A **Hozzáférés hozzárendelése** területen válassza az **Azure AD, felhasználó, csoport vagy alkalmazás** elemet.
8. Hozzáférés hozzárendeléséhez keresse meg, majd válassza ki a felhasználót.
9. Kattintson a **Save** (Mentés) gombra.

## <a name="cross-tenant-authentication-issues"></a>A több-bérlős hitelesítéssel kapcsolatos problémák

Az Azure Cost Management jelenleg korlátozott mértékben támogatja a több-bérlős hitelesítést. A bérlők hitelesítése során bizonyos körülmények között előfordulhat, hogy **Hozzáférés megtagadva** hibát kap a költségelemzésben. Ez a probléma akkor fordulhat elő, ha szerepköralapú hozzáférés-vezérlést (RBAC) konfigurál egy másik bérlő előfizetéséhez, majd megpróbálja megtekinteni a költségadatokat.

*Megkerülő megoldás a problémára*: A több-bérlős RBAC konfigurálását követően várjon egy órát. Ezután próbálja meg megtekinteni a költségeket a költségelemzésben, vagy adja meg a Cost Managementhez való hozzáférést mindkét bérlő felhasználói számára.  


## <a name="next-steps"></a>További lépések

- Ha még nem végezte el a Cost Management első lépéseit, itt megtekintheti a [költségelemzés elkezdésének](quick-acm-cost-analysis.md) lépéseit.
