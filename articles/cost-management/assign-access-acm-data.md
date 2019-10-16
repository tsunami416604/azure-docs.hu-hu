---
title: Hozzáférés kiosztása Azure Cost Management-adatkapcsolathoz
description: Ez a cikk azt mutatja be, hogy a különböző hozzáférési hatókörökhöz való adatAzure Cost Managementhoz rendeljen engedélyeket.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: secdec18
ms.openlocfilehash: ebc56d27b7adc8f1fea9eafabe1b211f3f0ad560
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375133"
---
# <a name="assign-access-to-cost-management-data"></a>Hozzáférés kiosztása Cost Management-adatkapcsolathoz

Az Azure nagyvállalati szerződéssel rendelkező felhasználók számára a Azure Portal és a nagyvállalati (EA) portálon megadott engedélyek kombinációja határozza meg, hogy a felhasználó milyen szintű hozzáférést biztosít a Azure Cost Managementi adatmennyiséghez. A többi Azure-fiókkal rendelkező felhasználók számára az Azure szerepköralapú hozzáférés-vezérlés használatával egyszerűbben definiálható, hogy a felhasználó milyen szintű hozzáférést Cost Management az adathoz. Ez a cikk végigvezeti a Cost Management-adateléréshez való hozzáférés kiosztásán. Az engedélyek kombinációjának hozzárendelése után a felhasználó megtekinti a Cost Managementon lévő, a Azure Portalban kiválasztott hatókörön alapuló hatókört.

A felhasználó által kiválasztott hatókör a Cost Management az adatok összevonásához és a költségadatokhoz való hozzáférés szabályozásához használatos. Hatókörök használatakor a felhasználók nem választhatják ki a többszörös kijelölést. Ehelyett egy nagyobb hatókört választanak ki, amelyet a gyermek-hatókörök összesítenek, majd a szűrésük után leszűrik a megtekinteni kívánt értéket. Az adatösszesítés fontos, hogy tisztában legyen azzal, hogy egyes személyek nem férhetnek hozzá olyan szülő hatókörhöz, amelyet a gyermek hatókörök összesítenek.

Tekintse meg a [hozzáférés kiosztása Azure Cost Management](https://www.youtube.com/watch?v=J997ckmwTa8) videóval című témakört, amelyből megtudhatja, hogyan oszthatja meg a költségeket és a díjakat az Azure szerepköralapú hozzáférés-vezérléssel.

>[!VIDEO https://www.youtube.com/embed/J997ckmwTa8]

## <a name="cost-management-scopes"></a>Hatókörök Cost Management

A Cost Management számos Azure-fióktípus használatát támogatja. A támogatott fióktípus teljes listájának megtekintéséhez lásd: Cost Management- [adat megismerése](understand-cost-mgt-data.md). A fiók típusa határozza meg az elérhető hatóköröket.

### <a name="azure-ea-subscription-scopes"></a>Azure EA-előfizetési hatókörök

Az Azure EA-előfizetések költségadatok megtekintéséhez a felhasználónak legalább olvasási hozzáféréssel kell rendelkeznie a következő hatókörökhöz.

| **Hatókör** | **Meghatározás helye** | **Az adatmegjelenítéshez szükséges hozzáférés** | **Nagyvállalati Szerződés előfeltételként szükséges beállítása** | **Az adatösszesítés** |
| --- | --- | --- | --- | --- |
| Számlázási fiók<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Vállalati rendszergazda | None | A Nagyvállalati Szerződésben foglalt összes előfizetés |
| Részleg | [https://ea.azure.com](https://ea.azure.com/) | Részlegszintű rendszergazda | A **da View díjak** engedélyezve | A részleghez kapcsolt regisztrációs fiókhoz tartozó összes előfizetés |
| Regisztrációs fiók<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | Fióktulajdonos | **Ao View-díjak** engedélyezve | A regisztrációs fiókhoz tartozó összes előfizetés |
| Felügyeleti csoport | [https://portal.azure.com](https://portal.azure.com/) | Cost Management-olvasó (vagy olvasó) | **Ao View-díjak** engedélyezve | A felügyeleti csoport alá tartozó összes előfizetés |
| Előfizetés | [https://portal.azure.com](https://portal.azure.com/) | Cost Management-olvasó (vagy olvasó) | **Ao View-díjak** engedélyezve | Az előfizetésben szereplő összes erőforrás/erőforráscsoport |
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

## <a name="enable-access-to-costs-in-the-ea-portal"></a>A költségekhez való hozzáférés engedélyezése az EA portálon

A részleg hatóköréhez az EA-portálon **engedélyezni** kell a **da View díjak** beállítást. Az összes többi hatókörön **engedélyezve** van az **Ao View charges** beállítás az EA portálon.

Lehetőség engedélyezése:

1. Jelentkezzen be az EA portálra [https://ea.azure.com](https://ea.azure.com) címen vállalati rendszergazdai fiókkal.
2. A bal oldali ablaktáblán kattintson a **kezelés** elemre.
3. Ahhoz, hogy a Cost Management hatókörei hozzáférhessenek a szolgáltatáshoz, engedélyezze a díjszabási lehetőséget a **da View díjak** és/vagy az **Ao View díjak**megadásához.  
    @no__t – 0Enrollment lap, amely a DA és az AO View díjszabási beállításait mutatja @ no__t-1

A megtekintési lehetőség engedélyezése után a legtöbb hatókör a Azure Portal szerepköralapú hozzáférés-vezérlési (RBAC) engedély-konfigurációját is igényli.

## <a name="enterprise-administrator-role"></a>Vállalati rendszergazda szerepkör

Alapértelmezés szerint a vállalati rendszergazda hozzáférhet a számlázási fiókhoz (Nagyvállalati Szerződés/beléptetés) és az összes többi hatókörhöz, amelyek alárendelt hatókörök. A vállalati rendszergazda más felhasználók számára is hozzáférést rendel a hatókörökhöz. Az üzletmenet folytonosságának ajánlott eljárása, hogy mindig legyen két vállalati rendszergazdai hozzáféréssel rendelkező felhasználó. A következő szakaszokban példákat talál a vállalati rendszergazda számára a hatókörökhöz való hozzáférés kiosztására más felhasználók számára.

## <a name="assign-billing-account-scope-access"></a>Számlázási fiók hatókör-hozzáférésének kiosztása

A Számlázási fiók hatókörének eléréséhez vállalati rendszergazdai engedély szükséges az EA portálon. A vállalati rendszergazda a teljes EA-regisztrációban vagy több regisztráción keresztül tekintheti meg a költségeket. Nincs szükség beavatkozásra a Számlázási fiók hatókörének Azure Portal.

1. Jelentkezzen be az EA portálra [https://ea.azure.com](https://ea.azure.com) címen vállalati rendszergazdai fiókkal.
2. A bal oldali ablaktáblán kattintson a **kezelés** elemre.
3. A **beléptetés** lapon válassza ki a felügyelni kívánt beléptetést.  
    @no__t – 0select a regisztrációt az EA portálon @ no__t-1
4. Kattintson a **+ rendszergazda hozzáadása**lehetőségre.
5. A rendszergazda hozzáadása mezőben válassza ki a hitelesítés típusát, és írja be a felhasználó e-mail-címét.
6. Ha a **felhasználónak csak olvasási**jogosultsággal kell rendelkeznie a költségeket és a használati adatokat, válassza az **Igen**lehetőséget.  Ellenkező esetben válassza a **nem**lehetőséget.
7. A fiók létrehozásához kattintson a **Hozzáadás** gombra.  
    ![example információ a rendszergazda hozzáadása mezőbe @ no__t-1

Akár 30 percet is igénybe vehet, mielőtt az új felhasználó hozzáférhessen a Cost Management lévő adatszolgáltatásokhoz.

### <a name="assign-department-scope-access"></a>Részleg hatókör-hozzáférésének kiosztása

A részleg hatókörének eléréséhez a részleg rendszergazdájának (DA View charges) hozzáférésre van szüksége az EA portálon. A részleg rendszergazdája hozzáférhet a részleghez vagy több részleghez társított költségek és használati adatok megtekintéséhez. A részleg adatai tartalmazzák a részleghez kapcsolódó beléptetési fiókhoz tartozó összes előfizetést. Nincs szükség beavatkozásra a Azure Portal.

1. Jelentkezzen be az EA portálra [https://ea.azure.com](https://ea.azure.com) címen vállalati rendszergazdai fiókkal.
2. A bal oldali ablaktáblán kattintson a **kezelés** elemre.
3. A **beléptetés** lapon válassza ki a felügyelni kívánt beléptetést.
4. Kattintson a **részleg** fülre, majd a **rendszergazda hozzáadása**lehetőségre.
5. A részleg-rendszergazda hozzáadása mezőben válassza ki a hitelesítés típusát, majd írja be a felhasználó e-mail címét.
6. Ha a **felhasználónak csak olvasási**jogosultsággal kell rendelkeznie a költségeket és a használati adatokat, válassza az **Igen**lehetőséget.  Ellenkező esetben válassza a **nem**lehetőséget.
7. Válassza ki azokat a részlegeket, amelyekhez rendszergazdai jogosultságot szeretne adni.
8. A fiók létrehozásához kattintson a **Hozzáadás** gombra.  
    ![enter szükséges információk a részleg hozzáadása rendszergazda mezőben @ no__t-1

## <a name="assign-enrollment-account-scope-access"></a>Beléptetési fiók hatókör-hozzáférésének kiosztása

A beléptetési fiók hatóköréhez a fiók tulajdonosának (AO View charges) hozzáférésre van szüksége az EA portálon. A fiók tulajdonosa megtekintheti az adott beléptetési fiókból létrehozott előfizetésekhez kapcsolódó költségeket és használati adatokat. Nincs szükség beavatkozásra a Azure Portal.

1. Jelentkezzen be az EA portálra [https://ea.azure.com](https://ea.azure.com) címen vállalati rendszergazdai fiókkal.
2. A bal oldali ablaktáblán kattintson a **kezelés** elemre.
3. A **beléptetés** lapon válassza ki a felügyelni kívánt beléptetést.
4. Kattintson a **fiók** fülre, majd a **fiók hozzáadása**lehetőségre.
5. A fiók hozzáadása mezőben válassza ki azt a **részleget** , amelyhez társítani szeretné a fiókot, vagy hagyja meg a hozzárendelés nélküliként.
6. Válassza ki a hitelesítés típusát, és írja be a fiók nevét.
7. Írja be a felhasználó e-mail-címét, majd opcionálisan írja be a Cost centert.
8. A fiók létrehozásához kattintson a **Hozzáadás** gombra.  
    ![enter szükséges információk a fiók hozzáadása mezőben a következő regisztrációs fiókhoz: @ no__t-1

A fenti lépések elvégzése után a felhasználói fiók beléptetési fiók lesz az Enterprise Portalon, és előfizetéseket hozhat létre. A felhasználó a létrehozott előfizetések esetében elérheti a költségeket és a használati adatokat.

## <a name="assign-management-group-scope-access"></a>Felügyeleti csoport hatókör-hozzáférésének kiosztása

A felügyeleti csoport hatókörének megtekintéséhez legalább a Cost Management olvasó (vagy olvasó) engedélyre van szükség. A Azure Portal egy felügyeleti csoportra vonatkozó engedélyeket is beállíthat. Ahhoz, hogy mások számára engedélyezze a hozzáférést, legalább a felhasználói hozzáférés rendszergazdai (vagy tulajdonos) engedélyekkel kell rendelkeznie a felügyeleti csoport számára. Az Azure EA-fiókok esetében továbbá engedélyeznie kell az **Ao View díjak** beállítást is az EA portálon.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Válassza a **minden szolgáltatás** lehetőséget az oldalsávon, keresse meg a _felügyeleti csoportokat_, majd válassza a **felügyeleti csoportok**lehetőséget.
3. Válassza ki a felügyeleti csoportot a hierarchiában.
4. A felügyeleti csoport neve mellett kattintson a **részletek**elemre.
5. A bal oldali ablaktáblán válassza a **Access Control (iam)** lehetőséget.
6. Kattintson a **Hozzáadás** parancsra.
7. A **szerepkör**alatt válassza ki **Cost Management olvasót**.
8. **A hozzáférés társítása**területen válassza az **Azure ad-felhasználó,-csoport vagy-alkalmazás**lehetőséget.
9. A hozzáférés hozzárendeléséhez keresse meg, majd válassza ki a felhasználót.
10. Kattintson a **Save** (Mentés) gombra.  
    ![example információ a felügyeleti csoport engedélyek hozzáadása mezőjében @ no__t-1

## <a name="assign-subscription-scope-access"></a>Előfizetés hatókör-hozzáférésének kiosztása

Az előfizetéshez való hozzáféréshez legalább a Cost Management olvasó (vagy olvasó) engedély szükséges. Az előfizetésre vonatkozó engedélyeket a Azure Portal lehet konfigurálni. A hozzáférés mások számára való engedélyezéséhez legalább a felhasználói hozzáférés-adminisztrátor (vagy tulajdonos) engedéllyel kell rendelkeznie az előfizetéshez. Az Azure EA-fiókok esetében továbbá engedélyeznie kell az **Ao View díjak** beállítást is az EA portálon.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Válassza a **minden szolgáltatás** lehetőséget az oldalsávon, keresse meg az _előfizetések_, majd az **előfizetések**elemet.
3. Válassza ki előfizetését.
4. A bal oldali ablaktáblán válassza a **Access Control (iam)** lehetőséget.
5. Kattintson a **Hozzáadás** parancsra.
6. A **szerepkör**alatt válassza ki **Cost Management olvasót**.
7. **A hozzáférés társítása**területen válassza az **Azure ad-felhasználó,-csoport vagy-alkalmazás**lehetőséget.
8. A hozzáférés hozzárendeléséhez keresse meg, majd válassza ki a felhasználót.
9. Kattintson a **Save** (Mentés) gombra.

## <a name="assign-resource-group-scope-access"></a>Erőforrás-csoport hatókör-hozzáférésének kiosztása

Az erőforráscsoporthoz való hozzáféréshez legalább a Cost Management olvasó (vagy olvasó) engedély szükséges. A Azure Portal egy erőforráscsoporthoz is beállíthat engedélyeket. A mások számára való hozzáférés engedélyezéséhez legalább a felhasználói hozzáférés-felügyeleti (vagy tulajdonosi) engedéllyel kell rendelkeznie az erőforráscsoporthoz. Az Azure EA-fiókok esetében továbbá engedélyeznie kell az **Ao View díjak** beállítást is az EA portálon.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Válassza a **minden szolgáltatás** lehetőséget az oldalsávon, keresse meg az _erőforráscsoportok_elemet, majd válassza az **erőforráscsoportok**lehetőséget.
3. Válassza ki az erőforráscsoportot.
4. A bal oldali ablaktáblán válassza a **Access Control (iam)** lehetőséget.
5. Kattintson a **Hozzáadás** parancsra.
6. A **szerepkör**alatt válassza ki **Cost Management olvasót**.
7. **A hozzáférés társítása**területen válassza az **Azure ad-felhasználó,-csoport vagy-alkalmazás**lehetőséget.
8. A hozzáférés hozzárendeléséhez keresse meg, majd válassza ki a felhasználót.
9. Kattintson a **Save** (Mentés) gombra.

## <a name="cross-tenant-authentication-issues"></a>Több-bérlős hitelesítési problémák

A Azure Cost Management jelenleg korlátozott támogatást biztosít a több-bérlős hitelesítéshez. Bizonyos esetekben, amikor megkísérli a hitelesítést a bérlők között, **hozzáférés-megtagadási** hibaüzenetet kaphat a Cost Analysis szolgáltatásban. Ez a probléma akkor fordulhat elő, ha szerepköralapú hozzáférés-vezérlést (RBAC) konfigurál egy másik bérlői előfizetéshez, majd megpróbálja megtekinteni a költségadatok megtekintését.

*A probléma megkerüléséhez*: a több-bérlős RBAC konfigurálása után várjon egy órát. Ezután próbálja meg megtekinteni a költségeket a Cost Analysis szolgáltatásban, vagy adja meg Cost Management hozzáférést a bérlők felhasználói számára.  


## <a name="next-steps"></a>Következő lépések

- Ha még nem végezte el a Cost Management első gyors útmutatóját, olvassa el a [költségeket](quick-acm-cost-analysis.md).
