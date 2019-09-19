---
title: Az Azure Cost Management adataihoz való hozzáférés hozzárendelése |} A Microsoft Docs
description: Ez a cikk bemutatja, ha az engedély hozzárendelése az Azure Cost Management-adatok különféle hozzáférési hatókörök.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: secdec18
ms.openlocfilehash: 332ec3930a7654fd5aecf1fc71ccb55c16df127f
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105127"
---
# <a name="assign-access-to-cost-management-data"></a>Cost Management adataihoz való hozzáférés hozzárendelése

Az Azure nagyvállalati szerződéssel rendelkező felhasználók számára a Azure Portal és a nagyvállalati (EA) portálon megadott engedélyek kombinációja határozza meg, hogy a felhasználó milyen szintű hozzáférést biztosít a Azure Cost Managementi adatmennyiséghez. A többi Azure-fiókkal rendelkező felhasználók számára a felhasználó hozzáférési szintje az, hogy Cost Management az adatmennyiséget. Ez a cikk végigvezeti a Cost Management adataihoz való hozzáférés hozzárendelése. Az engedélyek kombinációját hozzá van rendelve, miután a felhasználói nézetek adatokat a Cost Management alapján a hatókör, hogy ők is hozzáférhetnek, és az Azure Portalon válassza a hatókörön.

Adja meg az adatok összevonása, és a költségadatok való hozzáférésének a hatókör, amely a felhasználó kiválaszt Cost Management használja. Hatókörök használatakor a felhasználók ne válassza ki őket. Ehelyett, válassza ki a nagyobb hatókör, amely gyermek hatókörök visszaállítás akár, és ezután azok szűrő válassza ki, amit szeretnének megtekintéséhez. Adatok összevonása fontos tudni, mert vannak, akik nem rendelkezik hozzáféréssel, annak egy szülőhatóköréhez gyermek hatókörök összegző akár.

Tekintse meg a [hozzáférés kiosztása Azure Cost Management](https://www.youtube.com/watch?v=J997ckmwTa8) videóval című témakört, amelyből megtudhatja, hogyan oszthatja meg a költségeket és a díjakat az Azure szerepköralapú hozzáférés-vezérléssel.

>[!VIDEO https://www.youtube.com/embed/J997ckmwTa8]

## <a name="cost-management-scopes"></a>A Cost Management hatókörök

A Cost Management számos Azure-fióktípus használatát támogatja. A támogatott fióktípus teljes listájának megtekintéséhez lásd: Cost Management- [adat megismerése](understand-cost-mgt-data.md). A fiók típusa határozza meg az elérhető hatóköröket.

### <a name="azure-ea-subscription-scopes"></a>Azure EA-előfizetési hatókörök

Az Azure EA-előfizetések költségadatok megtekintéséhez a felhasználónak legalább olvasási hozzáféréssel kell rendelkeznie a következő hatókörökhöz.

| **Hatókör** | **Meghatározás helye** | **Hozzáférési adatok megtekintéséhez** | **Nagyvállalati Szerződés előfeltételként szükséges beállítása** | **Összesíti az adatokat** |
| --- | --- | --- | --- | --- |
| Számlázási fiók<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Vállalati rendszergazda | None | A Nagyvállalati Szerződésben foglalt összes előfizetés |
| Részleg | [https://ea.azure.com](https://ea.azure.com/) | Részlegszintű rendszergazda | **DA költségek megtekintése** engedélyezve | A részleghez kapcsolt regisztrációs fiókhoz tartozó összes előfizetés |
| Regisztrációs fiók<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | Fióktulajdonos | **AO költségek megtekintése** engedélyezve | A regisztrációs fiókhoz tartozó összes előfizetés |
| Felügyeleti csoport | [https://portal.azure.com](https://portal.azure.com/) | Cost Management-olvasó (vagy olvasó) | **AO költségek megtekintése** engedélyezve | A felügyeleti csoport alá tartozó összes előfizetés |
| Előfizetés | [https://portal.azure.com](https://portal.azure.com/) | Cost Management-olvasó (vagy olvasó) | **AO költségek megtekintése** engedélyezve | Az előfizetésben szereplő összes erőforrás/erőforráscsoport |
| Erőforráscsoport | [https://portal.azure.com](https://portal.azure.com/) | Cost Management-olvasó (vagy olvasó) | **AO költségek megtekintése** engedélyezve | Az erőforráscsoportban található összes erőforrás |

<sup>1</sup> a számlázási fiók is nevezzük a nagyvállalati szerződés vagy a regisztráció.

<sup>2</sup> a regisztrációs fiók is nevezzük a fiók tulajdonosától.

Az alábbi ábra a Cost Management hatókörök és a szerepkörök és az EA-portál beállításai közötti kapcsolatot szemlélteti.

![A szerepkörökkel és az EA-portál beállításaival Cost Management hatókörök közötti kapcsolatot bemutató ábra](./media/assign-access-acm-data/scope-access-relationship-diagram.png)

Ha az EA Portalon le vannak tiltva a **megtekintési díjak** , megjelenik egy üzenet, amely a *szervezetnél letiltott költségeket* jeleníti meg, amikor a részlegek és a fiókok költségeit szeretné megtekinteni.

Hasonlóképpen, ha az **Ao View díjak** le vannak tiltva az EA-portálon, megjelenik egy üzenet, amely a *szervezetnél letiltott költségeket* jeleníti meg a regisztrációs fiókok, a felügyeleti csoportok, az előfizetések és az erőforráscsoportok költségeinek megtekintésekor.

## <a name="other-azure-account-scopes"></a>Egyéb Azure-fiók hatókörök

Más Azure-előfizetésekhez tartozó költségadatok megtekintéséhez a felhasználónak legalább olvasási hozzáféréssel kell rendelkeznie a következő hatókörökhöz:

- Azure-fiók
- Felügyeleti csoport
- Resource group

## <a name="enable-access-to-costs-in-the-ea-portal"></a>A nagyvállalati szerződések portáljának költségek hozzáférésének engedélyezése

A részleg hatókör van szükség a **DA költségek megtekintése** beállítás **engedélyezve** az EA-portálon. Minden hatókör szükséges a **AO költségek megtekintése** beállítás **engedélyezve** az EA-portálon.

Ahhoz, hogy egy lehetőséget:

1. Jelentkezzen be, a nagyvállalati szerződések portáljának [ https://ea.azure.com ](https://ea.azure.com) vállalati rendszergazdai fiókkal.
2. Válassza ki **kezelés** a bal oldali panelen.
3. A cost management hatóköröket, amely lehetővé szeretné tenni a hozzáférést, a díj a beállítást **DA költségek megtekintése** és/vagy **AO költségek megtekintése**.  
    ![Regisztrációs lapon, DA és AO nézetének megjelenítése díjak beállításai](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

Miután a nézet díj beállításai engedélyezve vannak, a legtöbb hatókörök is szükség szerepköralapú hozzáférés-vezérlés (RBAC) engedélyt konfigurálni az Azure Portalon.

## <a name="enterprise-administrator-role"></a>Vállalati rendszergazdai szerepkör

Alapértelmezés szerint a vállalati rendszergazda hozzáfér a számlázási fiók (a nagyvállalati szerződés/beléptetés), és minden egyéb hatókör, amely gyermek hatókörök. A vállalati rendszergazda hozzáférést rendel a hatókörök más felhasználók számára. Ajánlott eljárásként az üzletmenet folytonosságának mindig rendelkeznie kell a vállalati rendszergazdai hozzáféréssel rendelkező két felhasználót. A következő szakaszok példák az útmutatóban a vállalati rendszergazda hozzárendelése hozzáférési hatókörhöz más felhasználók számára.

## <a name="assign-billing-account-scope-access"></a>Számlázási fiók hozzáférési hatókör hozzárendelése

A számlázási fiók hatókörében használatához a nagyvállalati szerződések portáljának vállalati rendszergazdai engedélyt. A vállalati rendszergazda hozzáfér a teljes EA-regisztrációhoz, illetve több regisztrációk költségek megtekintése. Ilyenkor az Azure Portalon, a számlázási fiók hatókör nem szükséges.

1. Jelentkezzen be, a nagyvállalati szerződések portáljának [ https://ea.azure.com ](https://ea.azure.com) vállalati rendszergazdai fiókkal.
2. Válassza ki **kezelés** a bal oldali panelen.
3. Az a **regisztrációs** lapra, válassza ki a kezelni kívánt regisztrációt.  
    ![Válassza ki a regisztráció a nagyvállalati szerződések portálján](./media/assign-access-acm-data/ea-portal.png)
4. Kattintson a **+ adja hozzá a rendszergazda**.
5. A rendszergazda hozzáadása párbeszédpanelen válassza ki a hitelesítés típusát, és írja be a felhasználó e-mail címét.
6. Ha a felhasználó a költség- és használati adatok, csak olvasási hozzáféréssel kell rendelkeznie **csak olvasható**válassza **Igen**.  Ellenkező esetben válassza **nem**.
7. Kattintson a **Hozzáadás** hozhat létre a fiókot.  
    ![a rendszergazda hozzáadása mezőbe látható példa információk](./media/assign-access-acm-data/add-admin.png)

Az új felhasználó férjenek hozzá az adatokat a Cost Management akár 30 percig is eltarthat.

### <a name="assign-department-scope-access"></a>Részleg hozzáférési hatókör hozzárendelése

A részleg hatókör használatához részleg rendszergazdai (DA nézet díjon felül) hozzáférés az EA-portálon. A részleg rendszergazdája hozzáfér a költségek és a egy részleg vagy több szervezeti kapcsolódó használati adatok megtekintéséhez. A részleg a regisztrációs fiókhoz tartozó összes előfizetésre, a szervezeti egység kapcsolódó szerepel. Ilyenkor az Azure Portalon nem szükséges.

1. Jelentkezzen be, a nagyvállalati szerződések portáljának [ https://ea.azure.com ](https://ea.azure.com) vállalati rendszergazdai fiókkal.
2. Válassza ki **kezelés** a bal oldali panelen.
3. Az a **regisztrációs** lapra, válassza ki a kezelni kívánt regisztrációt.
4. Kattintson a **részleg** fülre, majd **-rendszergazda felvétele**.
5. A részleg rendszergazda hozzáadása párbeszédpanelen válassza ki a hitelesítés típusát, és írja be a felhasználó e-mail címét.
6. Ha a felhasználó a költség- és használati adatok, csak olvasási hozzáféréssel kell rendelkeznie **csak olvasható**válassza **Igen**.  Ellenkező esetben válassza **nem**.
7. Válassza ki a részlegek, amelyet szeretne részleg rendszergazdai engedély biztosítása.
8. Kattintson a **Hozzáadás** hozhat létre a fiókot.  
    ![Adja meg a szükséges információkat a részleg rendszergazda hozzáadása mezőbe](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>Regisztráció-fiók hozzáférési hatókör hozzárendelése

A regisztrációs fiók hatókörében használatához fiók tulajdonosa (költségek AO megtekintése) hozzáférést a nagyvállalati szerződéssel rendelkező portálon. A fiók tulajdonosa megtekintheti a költségek és a regisztrációs fiókhoz létrehozott előfizetésekhez kapcsolódó használati adatok. Ilyenkor az Azure Portalon nem szükséges.

1. Jelentkezzen be, a nagyvállalati szerződések portáljának [ https://ea.azure.com ](https://ea.azure.com) vállalati rendszergazdai fiókkal.
2. Válassza ki **kezelés** a bal oldali panelen.
3. Az a **regisztrációs** lapra, válassza ki a kezelni kívánt regisztrációt.
4. Kattintson a **fiók** fülre, majd **fiók hozzáadása**.
5. A fiók hozzáadása párbeszédpanelen jelölje be a **részleg** társítsa a fiókját, vagy fel hozzá nem rendelt.
6. Válassza ki a hitelesítés típusát, és írja be a fiók nevét.
7. Írja be a felhasználó e-mail címét, és szükség esetén írja be a költségközpont.
8. Kattintson a **Hozzáadás** hozhat létre a fiókot.  
    ![Adja meg a szükséges adatokat a fiók hozzáadása mezőbe egy olyan regisztrációs fiók](./media/assign-access-acm-data/add-account.png)

A fenti lépések végrehajtását követően a felhasználói fiók lesz az Enterprise Portal regisztrációs fiók és előfizetéseket hozhat létre. A felhasználó hozzáférhessen a költség- és használati adatokat, hozhatnak létre előfizetéseket.

## <a name="assign-management-group-scope-access"></a>Felügyeleti csoport hozzáférési hatókör hozzárendelése

A felügyeleti csoport hatókörének megtekintéséhez legalább a Cost Management olvasó (vagy olvasó) engedélyre van szükség. A felügyeleti csoport engedélyeit az Azure Portalon konfigurálhatja. Engedéllyel kell rendelkeznie legalább a felhasználói hozzáférés rendszergazdája (vagy a tulajdonos) a felügyeleti csoport hozzáférésének engedélyezésére vonatkozó másokkal. Az Azure EA-fiókok esetében továbbá engedélyeznie kell az **Ao View díjak** beállítást is az EA portálon.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Válassza ki **minden szolgáltatás** az oldalsáv keressen _felügyeleti csoportok_, majd **felügyeleti csoportok**.
3. Válassza ki a felügyeleti csoport a hierarchiában.
4. Kattintson a felügyeleti csoport neve melletti **részletek**.
5. Válassza ki **hozzáférés-vezérlés (IAM)** a bal oldali ablaktáblán.
6. Kattintson a **Hozzáadás** parancsra.
7. A **szerepkör**válassza **Cost Management olvasó**.
8. A **rendelhet hozzáféréseket**válassza **az Azure AD-felhasználó, csoport vagy alkalmazás**.
9. Hozzáférés hozzárendelése, keresse meg, és válassza ki a felhasználót.
10. Kattintson a **Save** (Mentés) gombra.  
    ![a felügyeleti csoport hozzáadása engedélyek mezőbe példaadatok](./media/assign-access-acm-data/add-permissions.png)

## <a name="assign-subscription-scope-access"></a>Előfizetés hozzáférési hatókör hozzárendelése

Egy előfizetéshez legalább a Cost Management Reader (vagy olvasó) engedély szükséges. Az Azure Portal előfizetés engedélyeket is konfigurálhat. Engedéllyel kell rendelkeznie legalább a felhasználói hozzáférés rendszergazdája (vagy a tulajdonos) az előfizetés hozzáférésének engedélyezésére vonatkozó másokkal. Az Azure EA-fiókok esetében továbbá engedélyeznie kell az **Ao View díjak** beállítást is az EA portálon.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Válassza ki **minden szolgáltatás** az oldalsáv keressen _előfizetések_, majd **előfizetések**.
3. Válassza ki előfizetését.
4. Válassza ki **hozzáférés-vezérlés (IAM)** a bal oldali ablaktáblán.
5. Kattintson a **Hozzáadás** parancsra.
6. A **szerepkör**válassza **Cost Management olvasó**.
7. A **rendelhet hozzáféréseket**válassza **az Azure AD-felhasználó, csoport vagy alkalmazás**.
8. Hozzáférés hozzárendelése, keresse meg, és válassza ki a felhasználót.
9. Kattintson a **Save** (Mentés) gombra.

## <a name="assign-resource-group-scope-access"></a>Erőforrás-csoport hozzáférési hatókör hozzárendelése

Egy erőforráscsoport használatához legalább a Cost Management Reader (vagy olvasó) engedéllyel. Az Azure Portalon egy erőforráscsoportba tartozó engedélyeket is konfigurálhat. Engedéllyel kell rendelkeznie legalább a felhasználói hozzáférés rendszergazdája (vagy a tulajdonos) hozzáférésének engedélyezésére vonatkozó mások az erőforráscsoportnak. Az Azure EA-fiókok esetében továbbá engedélyeznie kell az **Ao View díjak** beállítást is az EA portálon.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Válassza ki **minden szolgáltatás** az oldalsáv keressen _erőforráscsoportok_, majd **erőforráscsoportok**.
3. Válassza ki az erőforráscsoportot.
4. Válassza ki **hozzáférés-vezérlés (IAM)** a bal oldali ablaktáblán.
5. Kattintson a **Hozzáadás** parancsra.
6. A **szerepkör**válassza **Cost Management olvasó**.
7. A **rendelhet hozzáféréseket**válassza **az Azure AD-felhasználó, csoport vagy alkalmazás**.
8. Hozzáférés hozzárendelése, keresse meg, és válassza ki a felhasználót.
9. Kattintson a **Save** (Mentés) gombra.

## <a name="cross-tenant-authentication-issues"></a>Több-bérlős hitelesítési problémák

A Azure Cost Management jelenleg korlátozott támogatást biztosít a több-bérlős hitelesítéshez. Bizonyos esetekben, amikor megkísérli a hitelesítést a bérlők között, **hozzáférés-megtagadási** hibaüzenetet kaphat a Cost Analysis szolgáltatásban. Ez a probléma akkor fordulhat elő, ha szerepköralapú hozzáférés-vezérlést (RBAC) konfigurál egy másik bérlői előfizetéshez, majd megpróbálja megtekinteni a költségadatok megtekintését.

*A probléma*megoldása: A több-bérlős RBAC konfigurálása után várjon egy órát. Ezután próbálja meg megtekinteni a költségeket a Cost Analysis szolgáltatásban, vagy adja meg Cost Management hozzáférést a bérlők felhasználói számára.  


## <a name="next-steps"></a>További lépések

- Ha még nem elvégezte már az első rövid Költségkezelés, olvassa el a [elemezheti a költségek](quick-acm-cost-analysis.md).
