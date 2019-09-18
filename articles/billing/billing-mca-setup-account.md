---
title: Számlázási fiók beállítása Microsoft-ügyfélszerződéshez – Azure
description: Megtudhatja, hogyan állíthatja be számlázási fiókját egy Microsoft-ügyfélszerződéshez.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 947bfe85d94a5d11eeb54bd6b24c4c515af024d4
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490699"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>Számlázási fiók beállítása Microsoft-ügyfélszerződéshez

Ha a Nagyvállalati Szerződéséhez kapcsolódó regisztrációja lejárt vagy hamarosan lejár, egy Microsoft-ügyfélszerződéssel megújíthatja a regisztrációját. Ez a cikk ismerteti, hogyan változik a meglévő számlázása a beállítás elvégzése után, és végigvezeti az új számlázási fiókja beállítási lépésein. A megújítás a következő lépéseket tartalmazza:

1. Fogadja el az új Microsoft-ügyfélszerződést. A Microsoft helyszíni munkatársa segítségével ismerje meg az új szerződés részleteit, majd fogadja el őket.
2. Állítsa be az új számlázási fiókot, amely az új Microsoft-ügyfélszerződéshez lett létrehozva.

A számlázási fiók beállításához át kell irányítania az Azure-előfizetései számlázását a Nagyvállalati Szerződés fiókjából az új fiókjába. A beállítás nem érinti az előfizetéseiben futó Azure-szolgáltatásokat. Módosítja azonban az előfizetés számlázási módját.

- Az [EA Portal](https://ea.azure.com) helyett Azure-beli szolgáltatásait és számlázását az [Azure Portalon](https://portal.azure.com) fogja kezelni.
- A díjakról havonta digitális számlát fog kapni. A számlát az Azure Költségkezelés + számlázás oldalon tekintheti meg és elemezheti.
- Az Nagyvállalati Szerződés regisztrációjában szereplő részlegek és fiók helyett az új fiók számlázási struktúráját és hatóköreit fogja használni a számlázás kezeléséhez és rendszerezéséhez.

A beállítás megkezdése előtt érdemes elvégeznie az alábbi lépéseket:

- **Az új számlázási fiók megismerése**
  - Az új fiókja leegyszerűsíti a szervezet számlázását. [Az új számlázási fiókjának rövid áttekintése](billing-mca-overview.md)
- **A beállítás végrehajtásához szükséges hozzáférés ellenőrzése**
  - Csak bizonyos rendszergazdai jogosultságokkal rendelkező felhasználók hajthatják végre a beállítást. Ellenőrizze, hogy rendelkezik-e a [beállítás végrehajtásához szükséges hozzáféréssel](#access-required-to-complete-the-setup).
- **A számlázási hierarchia változásainak megismerése**
  - Az új fiók számlázási rendszere eltér a Nagyvállalati Szerződés regisztrációjához kapcsolódó rendszertől. [Ismerje meg az új fiók számlázási hierarchiáját](#understand-changes-to-your-billing-hierarchy).
- **A számlázási rendszergazda hozzáférési jogosultságait érintő változások megismerése**
  - A Nagyvállalati Szerződés regisztrációjának rendszergazdái hozzáférhetnek az új fiók számlázási hatóköreihez.[Ismerje meg a hozzáférésüket érintő változásokat](#changes-to-billing-administrator-access).
- **Azon nagyvállalati szerződéses szolgáltatások megtekintése, amelyeket az új fiók más szolgáltatásokra cserél**
  - Tekintse meg a Nagyvállalati Szerződés regisztrációjának azon szolgáltatásait, amelyeket az új fiók más szolgáltatásokra cserél.
- **A gyakori kérdésekre adott válaszok megtekintése**
  - Tudjon meg [további információkat](#additional-information), hogy még közelebbről megismerkedhessen a beállítási folyamattal.

## <a name="access-required-to-complete-the-setup"></a>A beállítás végrehajtásához szükséges hozzáférés

A beállítás befejezéséhez a következő hozzáférésre van szükség:

- Annak a számlázási profilnak a tulajdonosa, amely a Microsoft-ügyfélszerződés aláírásakor jött létre. A számlázási profilokkal kapcsolatos további információkért lásd [a számlázási profilok ismertetését](billing-mca-overview.md#billing-profiles).

- A megújított regisztráció vállalati rendszergazdája.

### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>Amennyiben Ön nem vállalati rendszergazda a regisztráció tekintetében

Megkérheti a regisztráció vállalati rendszergazdáit, hogy hajtsák végre a számlázási fiók beállítását.

1. Jelentkezzen be az Azure Portalra annak a hivatkozásnak a használatával, amelyet a Microsoft-ügyfélszerződés aláírásakor kapott.

2. Ha a szervezet más tagja írta alá a szerződést, vagy nincs a birtokában az e-mail-üzenet, jelentkezzen be a következő hivatkozás használatával. Cserélje le az **enrollmentNumber** értéket a megújított Nagyvállalati Szerződés regisztrációs számára.

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. Válassza ki azokat a vállalati rendszergazdákat, akiknek el kívánja küldeni a kérést.

   ![A vállalati rendszergazdák meghívását szemléltető képernyőkép](./media/billing-mca-setup-account/ea-mca-invite-admins.png)

4. Válassza a **Kérés elküldése** elemet.

   A rendszergazdák egy instrukciókat tartalmazó e-mailt fognak kapni a beállítás végrehajtásához.

### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>Amennyiben Ön nem a számlázási profil tulajdonosa

A szervezet azon felhasználója, aki aláírta a Microsoft-ügyfélszerződést, a számlázási profil tulajdonosa lesz. A beállítás befejezéséhez kérje meg a felhasználót, hogy adja Önt hozzá tulajdonosként.  <!-- Todo Are there any next steps -->

## <a name="understand-changes-to-your-billing-hierarchy"></a>A számlázási hierarchia változásainak megismerése

Az új számlázási fiókja leegyszerűsíti a szervezet számlázását, valamint továbbfejlesztett számlázási és költségkezelési képességeket nyújt. Az alábbi ábra az új számlázási fiók számlázási rendszerét mutatja be.

![Az ea-mca-post-transition-hierarchy képe](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

1. A Microsoft-ügyfélszerződés számláinak kezeléséhez a számlázási fiókot használhatja. A számlázási fiókkal kapcsolatos további információkért lásd [a számlázási fiókok ismertetését](billing-mca-overview.md#your-billing-account).
2. A számlázási profillal a nagyvállalati szerződéses regisztrációhoz hasonlóan kezelheti a szervezet számlázását. A vállalati rendszergazdák a számlázási profil tulajdonosai lesznek. A számlázási profilokkal kapcsolatos további információkért lásd [a számlázási profilok ismertetését](billing-mca-overview.md#billing-profiles).
3. A költségek igény szerinti rendszerezését a nagyvállalati szerződéses regisztráció részlegeihez hasonlóan a számlaszakaszokkal teheti meg. A részlegekből számlázási szakaszok lesznek, a részlegszintű rendszergazdákból pedig a megfelelő számlázási szakaszok tulajdonosai. A számlázási szakaszokkal kapcsolatos további információkért lásd [a számlázási szakaszok ismertetését](billing-mca-overview.md#invoice-sections).
4. A Nagyvállalati Szerződésben létrehozott fiókok nem támogatottak az új számlázási fiókban. A fiók előfizetései a részlegek megfelelő számlázási szakaszához tartoznak. A fióktulajdonosok létrehozhatják és kezelhetik a számlázási szakaszaikhoz tartozó előfizetéseket.

## <a name="changes-to-billing-administrator-access"></a>Számlázási rendszergazda hozzáférését érintő változások

A hozzáférésüktől függően a nagyvállalati szerződéses regisztráció számlázási rendszergazdái hozzáférést kapnak az új fiók számlázási hatóköreihez. A következő táblázat ismerteti a hozzáférés változásait a beállítás után:

| Meglévő szerepkör | Váltás utáni szerepkör |
| --- | --- |
| **Vállalati rendszergazda (Csak olvasási engedély = Nem)** | **- Számlázásiprofil-tulajdonos** </br> A számlázási profil egészét felügyeli </br> - **Számlaszakasz tulajdonosa minden számlaszakaszon** </br> A számlaszakaszok tartalmát felügyeli |
| **Vállalati rendszergazda (Csak olvasási engedély = Igen)** | **- Számlázásiprofil-olvasó** </br> - A számlázási fiók egészének írásvédett nézete</br>**- Számlaszakasz olvasója minden számlaszakaszon**</br> - A számlaszakasz tartalmának írásvédett nézete|
| **Részlegszintű rendszergazda (Csak olvasási engedély = Nem)** |**- Az adott részleghez létrehozott számlaszakasz tulajdonosa** </br>A számlaszakasz tartalmát felügyeli|
| **Részlegszintű rendszergazda (Csak olvasási engedély = Igen)**|**- Az adott részleghez létrehozott számlaszakasz olvasója**</br> A számlaszakasz tartalmának írásvédett nézete|
| **Fióktulajdonos** | **- Az adott részleghez létrehozott számlaszakaszhoz tartozó Azure-előfizetés létrehozója** </br>  Azure-előfizetések létrehozása a saját számlaszakaszokhoz|

A Microsoft-ügyfélszerződés aláírásakor egy Azure Active Directory-bérlő lesz kiválasztva az új számlázási fiókhoz. Ha szervezet még nem rendelkezik bérlőkkel, létrejön egy új bérlő. A bérlő képviseli a szervezetet az Azure Active Directoryban. A szervezet globális bérlői rendszergazdái a bérlővel kezelhetik az alkalmazások és az adatok hozzáférését a szervezetben.

Az új fiókja csak a Microsoft-ügyfélszerződés aláírása során kiválasztott bérlő felhasználóit támogatja. Ha a Nagyvállalati Szerződésében adminisztratív jogosultsággal rendelkező felhasználók részei a bérlőnek, hozzáférést kapnak az új számlázási fiókhoz a beállítás során. Ha nem részei a bérlőnek, csak akkor férhetnek hozzá az új számlázási fiókhoz, ha Ön meghívja őket.

A felhasználók meghívásával vendégként adja őket hozzá a bérlőhöz, így hozzáférést kapnak a számlázási fiókhoz. A felhasználók meghívásához a vendéghozzáférésnek bekapcsolva kell lennie a bérlőn. További információért tekintse meg az [Azure Active Directory vendéghozzáféréseinek kezelését](https://docs.microsoft.com/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory) ismertető részt. Ha a vendéghozzáférés ki van kapcsolva, forduljon a bérlő globális rendszergazdájához, és kérje meg, hogy kapcsolja be. <!-- Todo - How can they find their global administrator -->

## <a name="view-replaced-features"></a>Leváltott szolgáltatások megtekintése

A következő nagyvállalati szerződéses szolgáltatásoknak a Microsoft-ügyfélszerződéshez tartozó számlázási fiók új szolgáltatásai lépnek a helyébe.

### <a name="enterprise-agreement-accounts"></a>Nagyvállalati szerződéses fiókok

A nagyvállalati szerződéses regisztrációban létrehozott fiókok nem támogatottak az új számlázási fiókban. A fiók előfizetései a részlegek megfelelő számlázási szakaszához tartoznak. A fióktulajdonosok az Azure-előfizetések létrehozóivá válnak, és létrehozhatják, valamint kezelhetik a számlaszakaszukhoz tartozó előfizetéseket.

### <a name="notification-contacts"></a>Kapcsolattartók

A kapcsolattartók kapják meg az Azure Nagyvállalati Szerződésre vonatkozó tájékoztató e-maileket. Az új számlázási fiókban nem támogatottak. A számlázási fiókjában lévő számlázási profilokhoz hozzáférő felhasználók kapják meg az Azure-kreditekkel és -számlákkal kapcsolatos e-maileket.

### <a name="spending-quotas"></a>Költségkvóták

A nagyvállalati szerződéses regisztrációban a részlegekhez beállított költségkvótákat a költségkeretek váltják fel az új számlázási fiókban. Minden egyes, a regisztrációban a részlegekhez beállított költségkvótához létrejön egy költségkeret. A költségkeretekről az [Azure-költségkeretek létrehozását és kezelését](../cost-management/manage-budgets.md) ismertető szakasz nyújt további információt.

### <a name="cost-centers"></a>Költséghelyek

A Nagyvállalati Szerződés regisztrációjának Azure-előfizetéseinél beállított költséghelyek továbbítódnak az új számlázási fiókba. A részlegek és a nagyvállalati szerződéses fiókok esetében azonban nem támogatottak a költséghelyek.

## <a name="additional-information"></a>További információ

A következő szakaszok további információval szolgálnak a számlázási fiók beállításáról.

### <a name="no-service-downtime"></a>Nincs szolgáltatáskiesés

Az előfizetésében lévő Azure-szolgáltatások megszakítás nélkül futnak tovább. Csak az Azure-előfizetései számlázási kapcsolatát állítjuk át. Meglévő erőforrásait, erőforráscsoportjait vagy felügyeleti csoportjait ez nem érinti.

### <a name="user-access-to-azure-resources"></a>Felhasználói hozzáférés az Azure-erőforrásokhoz

Az Azure RBAC (szerepköralapú hozzáférés-vezérlés) használatával beállított Azure-erőforrások hozzáférése érintetlen marad az átváltás során.

### <a name="azure-reservations"></a>Azure Reservations

A Nagyvállalati Szerződésben foglalt összes Azure Reserved VM Instances-példány is át lesz helyezve az új számlázási fiókba. Az átállás alatt az előfizetéseiben érvényesített foglalási kedvezmények nem módosulnak.

### <a name="azure-marketplace-products"></a>Azure Marketplace-termékek

A nagyvállalati szerződéses regisztráció összes Azure Marketplace-terméke az előfizetésekkel együtt lesz áthelyezve. A Marketplace-termékek szolgáltatásokhoz való hozzáférése az átváltás során nem változik.

### <a name="support-plan"></a>Támogatási csomag

A támogatási előnyök nem lesznek áthelyezve az átváltás során. Vásároljon új támogatási csomagot, ha az új számlázási fiókban is igénybe szeretné venni az előnyöket az Azure-előfizetésekhez.

### <a name="past-charges-and-balance"></a>Korábbi díjak és egyenleg

Az átváltás előtti kreditszám és egyenleg megtekinthető a Nagyvállalati Szerződés regisztrációjában az Azure Portalon keresztül. <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>Mikor kell végrehajtani a beállítást?

A számlázási fiókja beállítását a nagyvállalati szerződés regisztrációja lejárta előtt hajtsa végre. Ha lejár a regisztrációja, az Azure-előfizetések szolgáltatásai továbbra is futni fognak megszakítás nélkül. A szolgáltatások használatáért azonban végfelhasználói árat kell fizetnie.

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>A nagyvállalati szerződés regisztráció beállítás utáni változásai

Az átváltás után a nagyvállalati szerződéses regisztrációhoz létrehozott Azure-előfizetések manuálisan helyezhetők át az új számlázási fiókba. További információért lásd az [Azure-előfizetések számlázási tulajdonjogának beszerzése más felhasználóktól](billing-mca-request-billing-ownership.md) című részt. Az átváltás után megvásárolt Azure Reserved VM Instances-példányok áthelyezéséhez [forduljon az Azure ügyfélszolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Az átváltás után is adhat hozzáférést számlázási fiókjához a felhasználóknak. További információért lásd a [számlázási szerepkörök az Azure Portalon való kezeléséről](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal) szóló részt.

### <a name="revert-the-transition"></a>Az átváltás visszafordítása

Az átváltás nem fordítható vissza. Ha Azure-előfizetései számlázását átállítja az új számlázási fiókra, többé nem állíthatja vissza a Nagyvállalati Szerződésére.

### <a name="closing-your-browser-during-setup"></a>Böngésző bezárása a beállítás során

Mielőtt az **átváltás indítására** szolgáló lehetőségre kattint, bezárhatja a böngészőt. Az e-mailben kapott hivatkozás használatával visszatérhet a beállításhoz, és megkezdheti az átváltást. Ha bezárja a böngészőt az átváltás elindítása után, az átváltási folyamat továbbra is futni fog. Az átváltás állapotát az átváltás állapotát jelző oldalra visszatérve később is ellenőrizheti. E-mailt fog kapni arról, ha az átváltás befejeződött.

## <a name="complete-the-setup-in-the-azure-portal"></a>A beállítás befejezése az Azure Portalon

A beállítás befejezéséhez hozzá kell férnie az új számlázási fiókhoz és a nagyvállalati szerződés regisztrációjához is. További információért lásd a [számlázási fiók beállításának végrehajtásához szükséges hozzáférést](#access-required-to-complete-the-setup) ismertető részt.

1. Jelentkezzen be az Azure Portalra annak a hivatkozásnak a használatával, amelyet a Microsoft-ügyfélszerződés aláírásakor kapott.

2. Ha a szervezet más tagja írta alá a szerződést, vagy nincs a birtokában az e-mail-üzenet, jelentkezzen be a következő hivatkozás használatával. Cserélje le az **enrollmentNumber** értéket a megújított Nagyvállalati Szerződés regisztrációs számára.

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. Válassza az **átváltás indítására** szolgáló lehetőséget a beállítás utolsó lépésében. Miután kiválasztotta az átváltás elindítását:

    ![A beállítási varázslót megjelenítő képernyőkép](./media/billing-mca-setup-account/ea-mca-set-up-wizard.png)

    - A Nagyvállalati Szerződés hierarchiának megfelelő számlázási hierarchia jön létre az új számlázási fiókban. További információért lásd: [A számlázási hierarchia változásainak megismerése](#understand-changes-to-your-billing-hierarchy).
    - A nagyvállalati szerződéses regisztráció rendszergazdái hozzáférést kapnak az új számlázási fiókhoz, így továbbra is kezelhetik a szervezet számlázását.
    - Az Azure-előfizetései számlázása az új fiókon történik. **Az átváltás folyamata nem lesz hatással az Azure-szolgáltatásaira. Továbbra is problémamentesen fognak futni**.
    - Ha rendelkezik Azure Reserved VM Instances-példányokkal, akkor az új számlázási fiókra kerülnek ugyanazzal a kedvezménnyel és időtartammal. A foglalási kedvezmény továbbra is érvényes lesz a váltás során.

4. Az **átváltás állapotát** jelző lapon figyelheti az átváltás állapotát.

   ![Az átváltás állapotát megjelenítő képernyőkép](./media/billing-mca-setup-account/ea-mca-set-up-status.png)

## <a name="validate-billing-account-set-up"></a>Beállított számlázási fiók ellenőrzése

 Az alábbi lépésekkel ellenőrizze, hogy az új számlázási fiók megfelelően lett-e beállítva:

### <a name="azure-subscriptions"></a>Azure-előfizetések

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép az Azure Portal keresőmezőjéről](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Válassza ki a számlázási profilt. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási fiókot. A számlázási fiókon belül válassza ki a **Számlázási profilok** lehetőséget, majd a számlázási profilt.

4. A bal oldalon válassza az **Azure-előfizetések** elemet.

   ![Képernyőkép az előfizetések listájáról](./media/billing-mca-setup-account/billing-mca-subscriptions-post-transition.png)

Azok az Azure-előfizetések, amelyek a nagyvállalati szerződéses regisztrációról lettek átváltva az új számlázási fiókra, az Azure-előfizetések lapon jelennek meg. Ha úgy gondolja, hogy valamelyik előfizetés hiányzik, manuálisan váltsa át az előfizetés számlázását az Azure Portalon. További információért lásd az [Azure-előfizetések számlázási tulajdonjogának beszerzése más felhasználóktól](billing-mca-request-billing-ownership.md) című részt.

### <a name="azure-reservations"></a>Azure Reservations

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép az Azure Portal keresőmezőjéről](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Válasszon ki egy számlázási szakaszt. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási fiókot vagy számlázási profilt.  A számlázási fiókon vagy a számlázási profilon belül válassza a **Számlaszakaszok** lehetőséget, majd válasszon ki egy számlaszakaszt.

    ![Képernyőkép a számlaszakaszok listájáról az átváltás után](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. A bal oldalon válassza az **Összes termék** elemet.

5. Keressen rá a **Foglalt** kifejezésre.

    ![Képernyőkép az előfizetések listájáról az átváltás után](./media/billing-mca-setup-account/billing-mca-azure-reservations-post-transition.png)

Azok az Azure Reserved VM Instances-példányok, amelyek a nagyvállalati szerződéses regisztrációról lettek átváltva az új számlázási fiókra, az Összes termék lapon jelennek meg. Ismételje meg a lépéseket az összes számlaszakasz esetén, hogy leellenőrizze, mindegyik Azure Reserved VM Instances-példány át lett-e helyezve a nagyvállalati szerződéses regisztrációról. Ha úgy gondolja, hogy valamelyik Azure Reserved VM Instances-példány hiányzik, [forduljon az Azure-támogatáshoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), hogy áthelyezhesse a foglalást az új számlázási fiókba.

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>A vállalati rendszergazdák hozzáférése a számlázási profilon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép az Azure Portal keresőmezőjéről](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Válassza ki a regisztrációjához létrehozott számlázási profilt. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási fiókot.  A számlázási fiókon belül válassza ki a **Számlázási profilok** lehetőséget, majd a számlázási profilt.

4. A bal oldalon válassza az **Access control (IAM)** lehetőséget.

   ![A vállalati rendszergazdák átváltás utáni hozzáférését megjelenítő képernyőkép](./media/billing-mca-setup-account/billing-mca-ea-admins-access-post-transition.png)

A vállalati rendszergazdák számlázásiprofil-tulajdonosokként vannak felsorolva, míg a csak olvasási jogosultsággal rendelkező vállalati rendszergazdák számlázásiprofil-olvasókként jelennek meg. Ha úgy gondolja, hogy hiányzik valamelyik vállalati rendszergazda hozzáférése, hozzáférést adhat neki az Azure Portalon. További információért lásd [a számlázási szerepkörök az Azure Portalon történő kezelését](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>A vállalati rendszergazdák, a részlegszintű rendszergazdák és a fióktulajdonosok hozzáférése a számlaszakaszokhoz

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép az Azure Portal keresőmezőjéről](./media/billing-mca-setup-account/billing-search-cost-management-billing.png).

3. Válasszon ki egy számlázási szakaszt. A számlaszakaszoknak ugyanaz a neve, mint a hozzájuk tartozó nagyvállalati szerződéses regisztrációban lévő részlegeknek. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási profilt vagy számlázási fiókot. A számlázási profilon vagy számlázási fiókon belül válassza a **Számlaszakaszok** lehetőséget, majd válasszon ki egy számlaszakaszt.

   ![Képernyőkép a számlaszakaszok listájáról az átváltás után](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. A bal oldalon válassza az **Access control (IAM)** lehetőséget.

    ![A részlegszintű rendszergazdák és a fiókadminisztrátorok átváltás utáni hozzáférését megjelenítő képernyőkép](./media/billing-mca-setup-account/billing-mca-department-account-admins-access-post-transition.png)

A vállalati rendszergazdák és a részlegszintű rendszergazdák a számlaszakasz tulajdonosaiként vagy a számlaszakasz olvasóiként, míg a részlegben lévő fiókok tulajdonosai az Azure-előfizetések létrehozóiként vannak feltüntetve. Ismételje meg a lépéseket az összes számlaszakasz esetén, hogy leellenőrizze a nagyvállalati szerződéses regisztráció részlegeinek hozzáférését. Azok a fióktulajdonosok, akik egyetlen részlegnek sem voltak részei, az **Alapértelmezett számlaszakasz** nevű számlaszakaszhoz kapnak hozzáférést. Ha úgy gondolja, hogy hiányzik valamelyik rendszergazda hozzáférése, hozzáférést adhat neki az Azure Portalon. További információért lásd [a számlázási szerepkörök az Azure Portalon történő kezelését](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Kapcsolatfelvétel a támogatási szolgáltatással

Ha segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések

- [Új számlázási fiók – első lépések](billing-mca-overview.md)

- [Nagyvállalati szerződéses feladatok elvégzése a Microsoft-ügyfélszerződés számlázási fiókjában](billing-mca-enterprise-operations.md)

- [A számlázási fiókjához való hozzáférés kezelése](billing-understand-mca-roles.md)
