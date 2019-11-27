---
title: Hozzáférés kiosztása Azure Cost Management-adatkapcsolathoz
description: Ez a cikk bemutatja, ha az engedély hozzárendelése az Azure Cost Management-adatok különféle hozzáférési hatókörök.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: vitavor
ms.custom: secdec18
ms.openlocfilehash: e3140ee990127db6815828314103a09dff7cf26e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219850"
---
# <a name="assign-access-to-cost-management-data"></a>Cost Management adataihoz való hozzáférés hozzárendelése

Az Azure nagyvállalati szerződéssel rendelkező felhasználók számára a Azure Portal és a nagyvállalati (EA) portálon megadott engedélyek kombinációja határozza meg, hogy a felhasználó milyen szintű hozzáférést biztosít a Azure Cost Managementi adatmennyiséghez. A többi Azure-fiókkal rendelkező felhasználók számára az Azure szerepköralapú hozzáférés-vezérlés használatával egyszerűbben definiálható, hogy a felhasználó milyen szintű hozzáférést Cost Management az adathoz. Ez a cikk végigvezeti a Cost Management adataihoz való hozzáférés hozzárendelése. Az engedélyek kombinációját hozzá van rendelve, miután a felhasználói nézetek adatokat a Cost Management alapján a hatókör, hogy ők is hozzáférhetnek, és az Azure Portalon válassza a hatókörön.

Adja meg az adatok összevonása, és a költségadatok való hozzáférésének a hatókör, amely a felhasználó kiválaszt Cost Management használja. Hatókörök használatakor a felhasználók ne válassza ki őket. Ehelyett, válassza ki a nagyobb hatókör, amely gyermek hatókörök visszaállítás akár, és ezután azok szűrő válassza ki, amit szeretnének megtekintéséhez. Adatok összevonása fontos tudni, mert vannak, akik nem rendelkezik hozzáféréssel, annak egy szülőhatóköréhez gyermek hatókörök összegző akár.

Tekintse meg a [hozzáférés kiosztása Azure Cost Management](https://www.youtube.com/watch?v=J997ckmwTa8) videóval című témakört, amelyből megtudhatja, hogyan oszthatja meg a költségeket és a díjakat az Azure szerepköralapú hozzáférés-vezérléssel.

>[!VIDEO https://www.youtube.com/embed/J997ckmwTa8]

## <a name="cost-management-scopes"></a>A Cost Management hatókörök

A Cost Management számos Azure-fióktípus használatát támogatja. A támogatott fióktípus teljes listájának megtekintéséhez lásd: Cost Management- [adat megismerése](understand-cost-mgt-data.md). A fiók típusa határozza meg az elérhető hatóköröket.

### <a name="azure-ea-subscription-scopes"></a>Azure EA-előfizetési hatókörök

Az Azure EA-előfizetések költségadatok megtekintéséhez a felhasználónak legalább olvasási hozzáféréssel kell rendelkeznie a következő hatókörökhöz.

| **Hatókör** | **Meghatározás helye** | **Az adatmegjelenítéshez szükséges hozzáférés** | **Nagyvállalati Szerződés előfeltételként szükséges beállítása** | **Az adatösszesítés** |
| --- | --- | --- | --- | --- |
| Számlázási fiók<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Vállalati rendszergazda | None | A Nagyvállalati Szerződésben foglalt összes előfizetés |
| Részleg | [https://ea.azure.com](https://ea.azure.com/) | Részlegszintű rendszergazda | A **da View díjak** engedélyezve | A részleghez kapcsolt regisztrációs fiókhoz tartozó összes előfizetés |
| Regisztrációs fiók<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | Fióktulajdonos | **Ao View-díjak** engedélyezve | A regisztrációs fiókhoz tartozó összes előfizetés |
| Felügyeleti csoport | [https://portal.azure.com](https://portal.azure.com/) | Cost Management-olvasó (vagy olvasó) | **Ao View-díjak** engedélyezve | A felügyeleti csoport alá tartozó összes előfizetés |
| Előfizetést | [https://portal.azure.com](https://portal.azure.com/) | Cost Management-olvasó (vagy olvasó) | **Ao View-díjak** engedélyezve | Az előfizetésben szereplő összes erőforrás/erőforráscsoport |
| Erőforráscsoport | [https://portal.azure.com](https://portal.azure.com/) | Cost Management-olvasó (vagy olvasó) | **Ao View-díjak** engedélyezve | Az erőforráscsoportban található összes erőforrás |

<sup>1</sup> a számlázási fiókot nagyvállalati szerződés vagy beléptetésnek is nevezzük.

<sup>2</sup> a beléptetési fiókot a fiók tulajdonosaként is emlegetik.

Az alábbi ábra a Cost Management hatókörök és a szerepkörök és az EA-portál beállításai közötti kapcsolatot szemlélteti.

![A szerepkörökkel és az EA-portál beállításaival Cost Management hatókörök közötti kapcsolatot bemutató ábra](./media/assign-access-acm-data/scope-access-relationship-diagram.png)

Ha az EA Portalon le vannak tiltva a **megtekintési díjak** , megjelenik egy üzenet, amely a *szervezetnél letiltott költségeket* jeleníti meg, amikor a részlegek és a fiókok költségeit szeretné megtekinteni.

Hasonlóképpen, ha az **Ao View díjak** le vannak tiltva az EA-portálon, megjelenik egy üzenet, amely a *szervezetnél letiltott költségeket* jeleníti meg a regisztrációs fiókok, a felügyeleti csoportok, az előfizetések és az erőforráscsoportok költségeinek megtekintésekor.

## <a name="other-azure-account-scopes"></a>Egyéb Azure-fiók hatókörök

Más Azure-előfizetésekhez tartozó költségadatok megtekintéséhez a felhasználónak legalább olvasási hozzáféréssel kell rendelkeznie a következő hatókörökhöz:

- Azure-fiók
- Felügyeleti csoport
- Erőforráscsoport

A partnerek a Microsoft ügyfél-szerződése után különböző hatókörökkel érhetők el. A CSP-ügyfelek ezt követően használhatják Cost Management szolgáltatásokat, ha a CSP-partnerük engedélyezte őket. További információ: Ismerkedés [a Azure Cost Managementsal partnereink számára](get-started-partners.md).

## <a name="enable-access-to-costs-in-the-ea-portal"></a>A nagyvállalati szerződések portáljának költségek hozzáférésének engedélyezése

A részleg hatóköréhez az EA-portálon **engedélyezni** kell a **da View díjak** beállítást. Az összes többi hatókörön **engedélyezve** van az **Ao View charges** beállítás az EA portálon.

Ahhoz, hogy egy lehetőséget:

1. Jelentkezzen be az EA portálra [https://ea.azure.com](https://ea.azure.com) vállalati rendszergazdai fiókkal.
2. A bal oldali ablaktáblán kattintson a **kezelés** elemre.
3. Ahhoz, hogy a Cost Management hatókörei hozzáférhessenek a szolgáltatáshoz, engedélyezze a díjszabási lehetőséget a **da View díjak** és/vagy az **Ao View díjak**megadásához.  
    ![regisztráció lap, amely a DA és az AO View díjszabási lehetőségeket jeleníti meg](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

Miután a nézet díj beállításai engedélyezve vannak, a legtöbb hatókörök is szükség szerepköralapú hozzáférés-vezérlés (RBAC) engedélyt konfigurálni az Azure Portalon.

## <a name="enterprise-administrator-role"></a>Vállalati rendszergazdai szerepkör

Alapértelmezés szerint a vállalati rendszergazda hozzáfér a számlázási fiók (a nagyvállalati szerződés/beléptetés), és minden egyéb hatókör, amely gyermek hatókörök. A vállalati rendszergazda hozzáférést rendel a hatókörök más felhasználók számára. Ajánlott eljárásként az üzletmenet folytonosságának mindig rendelkeznie kell a vállalati rendszergazdai hozzáféréssel rendelkező két felhasználót. A következő szakaszok példák az útmutatóban a vállalati rendszergazda hozzárendelése hozzáférési hatókörhöz más felhasználók számára.

## <a name="assign-billing-account-scope-access"></a>Számlázási fiók hozzáférési hatókör hozzárendelése

A számlázási fiók hatókörében használatához a nagyvállalati szerződések portáljának vállalati rendszergazdai engedélyt. A vállalati rendszergazda hozzáfér a teljes EA-regisztrációhoz, illetve több regisztrációk költségek megtekintése. Ilyenkor az Azure Portalon, a számlázási fiók hatókör nem szükséges.

1. Jelentkezzen be az EA portálra [https://ea.azure.com](https://ea.azure.com) vállalati rendszergazdai fiókkal.
2. A bal oldali ablaktáblán kattintson a **kezelés** elemre.
3. A **beléptetés** lapon válassza ki a felügyelni kívánt beléptetést.  
    ![válassza ki a regisztrációt az EA portálon](./media/assign-access-acm-data/ea-portal.png)
4. Kattintson a **+ rendszergazda hozzáadása**lehetőségre.
5. A rendszergazda hozzáadása párbeszédpanelen válassza ki a hitelesítés típusát, és írja be a felhasználó e-mail címét.
6. Ha a **felhasználónak csak olvasási**jogosultsággal kell rendelkeznie a költségeket és a használati adatokat, válassza az **Igen**lehetőséget.  Ellenkező esetben válassza a **nem**lehetőséget.
7. A fiók létrehozásához kattintson a **Hozzáadás** gombra.  
    ![példa a rendszergazda hozzáadása mezőben látható információkra](./media/assign-access-acm-data/add-admin.png)

Az új felhasználó férjenek hozzá az adatokat a Cost Management akár 30 percig is eltarthat.

### <a name="assign-department-scope-access"></a>Részleg hozzáférési hatókör hozzárendelése

A részleg hatókör használatához részleg rendszergazdai (DA nézet díjon felül) hozzáférés az EA-portálon. A részleg rendszergazdája hozzáfér a költségek és a egy részleg vagy több szervezeti kapcsolódó használati adatok megtekintéséhez. A részleg a regisztrációs fiókhoz tartozó összes előfizetésre, a szervezeti egység kapcsolódó szerepel. Ilyenkor az Azure Portalon nem szükséges.

1. Jelentkezzen be az EA portálra [https://ea.azure.com](https://ea.azure.com) vállalati rendszergazdai fiókkal.
2. A bal oldali ablaktáblán kattintson a **kezelés** elemre.
3. A **beléptetés** lapon válassza ki a felügyelni kívánt beléptetést.
4. Kattintson a **részleg** fülre, majd a **rendszergazda hozzáadása**lehetőségre.
5. A részleg rendszergazda hozzáadása párbeszédpanelen válassza ki a hitelesítés típusát, és írja be a felhasználó e-mail címét.
6. Ha a **felhasználónak csak olvasási**jogosultsággal kell rendelkeznie a költségeket és a használati adatokat, válassza az **Igen**lehetőséget.  Ellenkező esetben válassza a **nem**lehetőséget.
7. Válassza ki a részlegek, amelyet szeretne részleg rendszergazdai engedély biztosítása.
8. A fiók létrehozásához kattintson a **Hozzáadás** gombra.  
    ![adja meg a szükséges adatokat az részleg hozzáadása adminisztrátor mezőben](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>Regisztráció-fiók hozzáférési hatókör hozzárendelése

A regisztrációs fiók hatókörében használatához fiók tulajdonosa (költségek AO megtekintése) hozzáférést a nagyvállalati szerződéssel rendelkező portálon. A fiók tulajdonosa megtekintheti a költségek és a regisztrációs fiókhoz létrehozott előfizetésekhez kapcsolódó használati adatok. Ilyenkor az Azure Portalon nem szükséges.

1. Jelentkezzen be az EA portálra [https://ea.azure.com](https://ea.azure.com) vállalati rendszergazdai fiókkal.
2. A bal oldali ablaktáblán kattintson a **kezelés** elemre.
3. A **beléptetés** lapon válassza ki a felügyelni kívánt beléptetést.
4. Kattintson a **fiók** fülre, majd a **fiók hozzáadása**lehetőségre.
5. A fiók hozzáadása mezőben válassza ki azt a **részleget** , amelyhez társítani szeretné a fiókot, vagy hagyja meg a hozzárendelés nélküliként.
6. Válassza ki a hitelesítés típusát, és írja be a fiók nevét.
7. Írja be a felhasználó e-mail címét, és szükség esetén írja be a költségközpont.
8. A fiók létrehozásához kattintson a **Hozzáadás** gombra.  
    ![írja be a szükséges adatokat a beléptetési fiók fiók hozzáadása mezőjébe](./media/assign-access-acm-data/add-account.png)

A fenti lépések végrehajtását követően a felhasználói fiók lesz az Enterprise Portal regisztrációs fiók és előfizetéseket hozhat létre. A felhasználó hozzáférhessen a költség- és használati adatokat, hozhatnak létre előfizetéseket.

## <a name="assign-management-group-scope-access"></a>Felügyeleti csoport hozzáférési hatókör hozzárendelése

A felügyeleti csoport hatókörének megtekintéséhez legalább a Cost Management olvasó (vagy olvasó) engedélyre van szükség. A felügyeleti csoport engedélyeit az Azure Portalon konfigurálhatja. Engedéllyel kell rendelkeznie legalább a felhasználói hozzáférés rendszergazdája (vagy a tulajdonos) a felügyeleti csoport hozzáférésének engedélyezésére vonatkozó másokkal. Az Azure EA-fiókok esetében továbbá engedélyeznie kell az **Ao View díjak** beállítást is az EA portálon.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Válassza a **minden szolgáltatás** lehetőséget az oldalsávon, keresse meg a _felügyeleti csoportokat_, majd válassza a **felügyeleti csoportok**lehetőséget.
3. Válassza ki a felügyeleti csoport a hierarchiában.
4. A felügyeleti csoport neve mellett kattintson a **részletek**elemre.
5. A bal oldali ablaktáblán válassza a **Access Control (iam)** lehetőséget.
6. Kattintson az **Hozzáadás** parancsra.
7. A **szerepkör**alatt válassza ki **Cost Management olvasót**.
8. **A hozzáférés társítása**területen válassza az **Azure ad-felhasználó,-csoport vagy-alkalmazás**lehetőséget.
9. Hozzáférés hozzárendelése, keresse meg, és válassza ki a felhasználót.
10. Kattintson a **Save** (Mentés) gombra.  
    ![például egy felügyeleti csoport engedélyek hozzáadása mezőjében](./media/assign-access-acm-data/add-permissions.png)

## <a name="assign-subscription-scope-access"></a>Előfizetés hozzáférési hatókör hozzárendelése

Egy előfizetéshez legalább a Cost Management Reader (vagy olvasó) engedély szükséges. Az Azure Portal előfizetés engedélyeket is konfigurálhat. Engedéllyel kell rendelkeznie legalább a felhasználói hozzáférés rendszergazdája (vagy a tulajdonos) az előfizetés hozzáférésének engedélyezésére vonatkozó másokkal. Az Azure EA-fiókok esetében továbbá engedélyeznie kell az **Ao View díjak** beállítást is az EA portálon.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Válassza a **minden szolgáltatás** lehetőséget az oldalsávon, keresse meg az _előfizetések_, majd az **előfizetések**elemet.
3. Válassza ki előfizetését.
4. A bal oldali ablaktáblán válassza a **Access Control (iam)** lehetőséget.
5. Kattintson az **Hozzáadás** parancsra.
6. A **szerepkör**alatt válassza ki **Cost Management olvasót**.
7. **A hozzáférés társítása**területen válassza az **Azure ad-felhasználó,-csoport vagy-alkalmazás**lehetőséget.
8. Hozzáférés hozzárendelése, keresse meg, és válassza ki a felhasználót.
9. Kattintson a **Save** (Mentés) gombra.

## <a name="assign-resource-group-scope-access"></a>Erőforrás-csoport hozzáférési hatókör hozzárendelése

Egy erőforráscsoport használatához legalább a Cost Management Reader (vagy olvasó) engedéllyel. Az Azure Portalon egy erőforráscsoportba tartozó engedélyeket is konfigurálhat. Engedéllyel kell rendelkeznie legalább a felhasználói hozzáférés rendszergazdája (vagy a tulajdonos) hozzáférésének engedélyezésére vonatkozó mások az erőforráscsoportnak. Az Azure EA-fiókok esetében továbbá engedélyeznie kell az **Ao View díjak** beállítást is az EA portálon.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Válassza a **minden szolgáltatás** lehetőséget az oldalsávon, keresse meg az _erőforráscsoportok_elemet, majd válassza az **erőforráscsoportok**lehetőséget.
3. Válassza ki az erőforráscsoportot.
4. A bal oldali ablaktáblán válassza a **Access Control (iam)** lehetőséget.
5. Kattintson az **Hozzáadás** parancsra.
6. A **szerepkör**alatt válassza ki **Cost Management olvasót**.
7. **A hozzáférés társítása**területen válassza az **Azure ad-felhasználó,-csoport vagy-alkalmazás**lehetőséget.
8. Hozzáférés hozzárendelése, keresse meg, és válassza ki a felhasználót.
9. Kattintson a **Save** (Mentés) gombra.

## <a name="cross-tenant-authentication-issues"></a>Több-bérlős hitelesítési problémák

A Azure Cost Management jelenleg korlátozott támogatást biztosít a több-bérlős hitelesítéshez. Bizonyos esetekben, amikor megkísérli a hitelesítést a bérlők között, **hozzáférés-megtagadási** hibaüzenetet kaphat a Cost Analysis szolgáltatásban. Ez a probléma akkor fordulhat elő, ha szerepköralapú hozzáférés-vezérlést (RBAC) konfigurál egy másik bérlői előfizetéshez, majd megpróbálja megtekinteni a költségadatok megtekintését.

*A probléma megkerüléséhez*: a több-bérlős RBAC konfigurálása után várjon egy órát. Ezután próbálja meg megtekinteni a költségeket a Cost Analysis szolgáltatásban, vagy adja meg Cost Management hozzáférést a bérlők felhasználói számára.  


## <a name="next-steps"></a>Következő lépések

- Ha még nem végezte el a Cost Management első gyors útmutatóját, olvassa el a [költségeket](quick-acm-cost-analysis.md).
