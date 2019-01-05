---
title: Az Azure Cost Management adataihoz való hozzáférés hozzárendelése |} A Microsoft Docs
description: Ez a cikk bemutatja, ha az engedély hozzárendelése az Azure Cost Management-adatok különféle hozzáférési hatókörök.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: secdec18
ms.openlocfilehash: 50f048dc36b0248cdbbd85d91c00b7947f9ddc1f
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052138"
---
# <a name="assign-access-to-cost-management-data"></a>Cost Management adataihoz való hozzáférés hozzárendelése

A felhasználók többsége számára az Azure portal és a Nagyvállalati portálon megadott engedélyek kombinációját az Azure Cost Management adataihoz való hozzáférés egy felhasználó szintjét határozza meg. Ez a cikk végigvezeti a Cost Management adataihoz való hozzáférés hozzárendelése. Az engedélyek kombinációját hozzá van rendelve, miután a felhasználói nézetek adatokat a Cost Management alapján a hatókör, hogy ők is hozzáférhetnek, és az Azure Portalon válassza a hatókörön.

Adja meg az adatok összevonása, és a költségadatok való hozzáférésének a hatókör, amely a felhasználó kiválaszt Cost Management használja. Hatókörök használatakor a felhasználók ne válassza ki őket. Ehelyett, válassza ki a nagyobb hatókör, amely gyermek hatókörök visszaállítás akár, és ezután azok szűrő válassza ki, amit szeretnének megtekintéséhez. Adatok összevonása fontos tudni, mert vannak, akik nem rendelkezik hozzáféréssel, annak egy szülőhatóköréhez gyermek hatókörök összegző akár.

## <a name="cost-management-scopes"></a>A Cost Management hatókörök

Költségadatok megtekintése, a felhasználó kell rendelkeznie legalább olvasási hozzáféréssel kell rendelkeznie egy vagy több, a következő hatókörök.

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

A következő ábra szemlélteti a Cost Management hatókörök szerepkörökkel és a nagyvállalati szerződéssel rendelkező portálbeállítások közötti kapcsolat.

![Cost Management hatókörök szerepkörökkel és a Nagyvállalati portál beállításai között bemutató diagram](./media/assign-access-acm-data/scope-access-relationship-diagram.png)

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

A felügyeleti csoport hatóköre használatához legalább a Cost Management Reader (vagy olvasó) engedéllyel. A felügyeleti csoport engedélyeit az Azure Portalon konfigurálhatja. Engedéllyel kell rendelkeznie legalább a felhasználói hozzáférés rendszergazdája (vagy a tulajdonos) a felügyeleti csoport hozzáférésének engedélyezésére vonatkozó másokkal. És akkor is engedélyeznie kell a **AO költségek megtekintése** a nagyvállalati szerződések portáljának beállítását.

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

Egy előfizetéshez legalább a Cost Management Reader (vagy olvasó) engedély szükséges. Az Azure Portal előfizetés engedélyeket is konfigurálhat. Engedéllyel kell rendelkeznie legalább a felhasználói hozzáférés rendszergazdája (vagy a tulajdonos) az előfizetés hozzáférésének engedélyezésére vonatkozó másokkal. És akkor is engedélyeznie kell a **AO költségek megtekintése** a nagyvállalati szerződések portáljának beállítását.

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

Egy erőforráscsoport használatához legalább a Cost Management Reader (vagy olvasó) engedéllyel. Az Azure Portalon egy erőforráscsoportba tartozó engedélyeket is konfigurálhat. Engedéllyel kell rendelkeznie legalább a felhasználói hozzáférés rendszergazdája (vagy a tulajdonos) hozzáférésének engedélyezésére vonatkozó mások az erőforráscsoportnak. És akkor is engedélyeznie kell a **AO költségek megtekintése** a nagyvállalati szerződések portáljának beállítását.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Válassza ki **minden szolgáltatás** az oldalsáv keressen _erőforráscsoportok_, majd **erőforráscsoportok**.
3. Válassza ki az erőforráscsoportot.
4. Válassza ki **hozzáférés-vezérlés (IAM)** a bal oldali ablaktáblán.
5. Kattintson a **Hozzáadás** parancsra.
6. A **szerepkör**válassza **Cost Management olvasó**.
7. A **rendelhet hozzáféréseket**válassza **az Azure AD-felhasználó, csoport vagy alkalmazás**.
8. Hozzáférés hozzárendelése, keresse meg, és válassza ki a felhasználót.
9. Kattintson a **Save** (Mentés) gombra.

## <a name="next-steps"></a>További lépések

- Ha még nem elvégezte már az első rövid Költségkezelés, olvassa el a [elemezheti a költségek](quick-acm-cost-analysis.md).
