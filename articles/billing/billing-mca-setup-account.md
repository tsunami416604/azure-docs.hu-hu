---
title: A számlázási fiók beállítása a Microsoft vevői szerződés – Azure |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be a számlázási fiók a Microsoft vevői szerződés.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 90f832319fa2343003af58bd99eb64c0cbd94dd8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57894546"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>A Microsoft vevői szerződés számlázási fiók beállítása

Ha a nagyvállalati szerződésre való beléptetésről lejárt, vagy készül, hogy lejárt, hogy újítsa meg a regisztráció egy Microsoft Ügyfélszerződéséhez jelentkezhet be. A megújítás a következő lépésekből áll:

1. Fogadja el az új Microsoft-ügyfél szerződését. Megismerheti a részleteket, és fogadja el az új szerződés, a Microsoft mező képviselőjével együttműködve.
2. Állítsa be az új számlázási fiók, amely a Microsoft új Ügyfélszerződéséhez jön létre.

Ez a cikk a telepítés után a meglévő számlázási változásokat tárgyalja, és végigvezeti Önt az új számlázási fiók beállítását.

A számlázási fiók beállításához kell az Azure-előfizetések számlázási a nagyvállalati szerződésre való beléptetésről a munkafolyamatra való áttérést az új fiókot. A telepítő nem érinti az Azure-előfizetésében futó szolgáltatások. Azonban ez módosítja a számlázás az előfizetések kezelése.

- Helyett a [a nagyvállalati szerződések portáljának](https://ea.azure.com), az Azure-szolgáltatások és a számlázási fog kezelni az a [az Azure portal](https://portal.azure.com).
- A díjakat a havi, digitális számlát fog kapni. Megtekintheti és elemezheti a számla a Azure Költségkezelés + számlázás lapján.
- Szervezeti egységek és a nagyvállalati szerződésre való beléptetésről fiók helyett fogja használni a számlázási szerkezetét és az új fiókot a hatókörök kezelése és a számlázás rendezéséhez.

A telepítés megkezdése előtt tegye a következőket javasoljuk:

- **Megismerheti az új számlázási fiók**
  - Az új fiók egyszerűbbé teszi a számlázás a szervezet számára. [Az új számlázási fiók gyors áttekintés](billing-mca-overview.md)
- **A telepítés befejezéséhez a hozzáférés ellenőrzése**
  - Csak bizonyos rendszergazdai engedélyekkel rendelkező felhasználók is a telepítés befejezéséhez. Ellenőrizze, hogy a [a telepítés befejezéséhez szükséges hozzáférést](#access-required-to-complete-the-setup).
- **A számlázási hierarchia módosításai ismertetése**
  - Új számlázási fiók meg van rendezve eltérően, mint a nagyvállalati szerződésre való beléptetésről. [Megismerheti az új fiókot a számlázási hierarchia módosításai](#understand-changes-to-your-billing-hierarchy).
- **A számlázási Adminisztrátorok hozzáférés módosításainak ismertetése**
  - A nagyvállalati szerződésre való beléptetésről a rendszergazdák hozzáférhetnek az új fiókot a számlázási hatókörök. [Megismerheti a hozzáférés módosításainak](#understand-changes-to-your-billing-administrators-access).
- **Váltják fel az új fiók nézetet nagyvállalati szerződés funkciók**
  - Tekintse meg a nagyvállalati szerződésre való beléptetésről helyébe a szolgáltatások az új fiókot a funkcióját.
- **Leggyakoribb kérdésekre adott válaszok megtekintése**
  - Nézet [további információkat](#additional-information) további információ a telepítés.

## <a name="access-required-to-complete-the-setup"></a>A telepítés befejezéséhez szükséges hozzáférést

A telepítés befejezéséhez hozzáférésre van szüksége a következő:

- A számlázási profilja, amely jött létre, amikor a Microsoft vevői megállapodást tulajdonosa. Számlázási profilokkal kapcsolatos további tudnivalókért lásd: [számlázási a profilok megismerése](billing-mca-overview.md#understand-billing-profiles).

- Vállalati rendszergazda engedélyezte a beléptetés, amely a meg nem újítják.

### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>Ha még nem egy vállalati rendszergazda engedélyezte a beléptetés

Kérheti, hogy a vállalati rendszergazdák, a beléptetési a számlázási fiók a telepítés befejezéséhez.

1. Jelentkezzen be az Azure Portalon, az e-mailben Önnek küldött a Microsoft Ügyfélszerződéséhez aláírásakor választott található hivatkozás segítségével.

2. Ha valaki a szervezetben a megállapodás, vagy nem rendelkezik az e-mailt, jelentkezzen be a következő hivatkozás használatával. Cserélje le **enrollmentNumber** sikerült megújítani a nagyvállalati szerződésen beléptetési számát.

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. Válassza ki a vállalati rendszergazdák, amelyet szeretne elküldeni a kérelmet.

   ![Képernyőkép azt mutatjuk be, a vállalati rendszergazdák meghívása](./media/billing-mca-setup-account/ea-mca-invite-admins.png)

4. Válassza ki **küldési kérelmek**.

   A rendszergazdák kap egy e-mailt az utasításokat a telepítés befejezéséhez.

### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>Ha Ön nem tulajdonosa a számlázási profilja

A felhasználó a szervezetben, akik a Microsoft Ügyfélszerződéséhez aláírt adnak a tulajdonosa az a számlázási profilja. Kérni a felhasználót, hogy vegye fel Önt tulajdonosként, hogy a telepítő is elvégezheti.  <!-- Todo Are there any next steps -->

## <a name="understand-changes-to-your-billing-hierarchy"></a>A számlázási hierarchia változásainak megértése

Az új számlázási fiók egyszerűbbé teszi a továbbfejlesztett számlázási és felügyeleti képességek optimalizálhatja a költségeket, miközben a szervezete számlázását. Az alábbi diagram bemutatja, hogyan vannak rendezve, a számlázás az új számlázási fiók.

![Ea-mca-post-váltás-hierarchia képe](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

1. A számlázási fiók használatával kezelheti a számlázás a Microsoft ügyfél-szerződését. Számlázási fiók kapcsolatos további információkért lásd: [számlázási fiók ismertetése](billing-mca-overview.md#understand-billing-account).
2. A számlázási profilja segítségével kezelheti a számlázási szervezetben, a nagyvállalati szerződésre való beléptetésről hasonló. Vállalati rendszergazdák a számlázási profilja tulajdonosai lesznek. Számlázási profilokkal kapcsolatos további tudnivalókért lásd: [számlázási a profilok megismerése](billing-mca-overview.md#understand-billing-profiles).
3. Egy számla szakasz használata a saját igényei szerint, a nagyvállalati szerződésre való beléptetésről részlegek hasonló költségeket rendszerezéséhez. Részleg számla szakaszok válik, és a részleg rendszergazdái válnak a megfelelő számla szakaszok tulajdonosai. Számla szakaszok kapcsolatos további információkért lásd: [számla szakaszokban megismerheti](billing-mca-overview.md#understand-invoice-sections).
4. A fiókok létrejöttek a nagyvállalati szerződésbe, az új számlázási fiók nem támogatottak. A fiók előfizetés tartozik, azok számára a megfelelő számla szakaszra. Fióktulajdonosok is előfizetéseinek létrehozásához és kezeléséhez a számla szakaszokat.

## <a name="understand-changes-to-your-billing-administrators-access"></a>A számlázási Adminisztrátorok hozzáférés módosításainak ismertetése

A hozzáférést, attól függően a nagyvállalati szerződésre való beléptetésről a számlázási Adminisztrátorok hozzáférhetnek az új fiókot a számlázási hatóköreit. Az alábbi táblázat mutatja be a hozzáférés a telepítés során:

| Meglévő szerepkört | POST átmenet szerepkör |
| --- | --- |
| **A vállalati rendszergazdák (olvasás = csak a nem)** | **-Számlázási profil tulajdonosa** </br> A számlázási profilt az összes funkció kezelését </br> - **A számla szakasz tulajdonos összes számla szakasz** </br> A számla szakaszokban az összes funkció kezelését |
| **A vállalati rendszergazdák (olvasás csak = Yes)** | **-A számlázás profil olvasója** </br> Írásvédett nézetben minden, a számlázási fiók</br>**-A számla szakasz olvasó összes számla szakaszban**</br> -A számla szakaszban minden, a csak olvasható nézet|
| **Szervezeti egység rendszergazdája (olvasás = csak a nem)** |**-A számla szakaszban a megfelelő részleg számára létrehozott számla szakasz a tulajdonos** </br>A számla a szakasz az összes funkció kezelését|
| **Szervezeti egység rendszergazdája (olvasás csak = Yes)**|**-A számla szakaszban a megfelelő részleg számára létrehozott számla szakasz olvasó**</br> A számla szakaszban minden, a csak olvasható nézet|
| **Fióktulajdonos** | **-A számla szakaszban a megfelelő részleg számára létrehozott azure előfizetésének létrehozója** </br>  A számla a szakasz az Azure-előfizetések létrehozása|

Az Azure Active Directory-bérlő van kiválasztva az új számlázási fiók a Microsoft vevői szerződés aláírása közben. Ha a bérlő nem létezik a szervezet számára, egy új bérlő jön létre. A bérlő Azure Active Directoryban a szervezet jelöli. A szervezet globális bérlői rendszergazdák a bérlő használatához az alkalmazások és a szervezeti adatok kezelésére.

Az új fiók csak a felhasználók a Microsoft vevői szerződés aláírása közben kiválasztott bérlőtől támogatja. Ha a nagyvállalati szerződés rendszergazdai engedéllyel rendelkező felhasználók a bérlő részét képezik, azok az új számlázási fiók hozzáférést fog kapni a telepítés során. Ha nem a bérlő egy része, nem érhetik el az új számlázási fiók, kivéve, ha felkéri őket.

Amikor meghívja a felhasználókat, azokat hozzá szeretné adni a bérlő vendégfelhasználók, és hozzáférhet a számlázási fiókba. A felhasználókat meghívni, a vendéghozzáférés bekapcsolva kell lennie a bérlő számára. További információkért lásd: [szabályozhatja a vendéghozzáférés az Azure Active Directoryban](https://docs.microsoft.com/en-us/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory). Ha a vendéghozzáférés ki van kapcsolva, lépjen kapcsolatba a kapcsolhatja be a bérlő globális rendszergazdája. <!-- Todo - How can they find their global administrator -->

## <a name="view-features-replaced-by-the-new-billing-account"></a>Megtekintheti a funkciókat váltotta fel az új számlázási fiók

A következő nagyvállalati szerződés funkciók cserélése számlázási fiókjához új funkciói a Microsoft ügyfél-szerződés.

### <a name="enterprise-agreement-accounts"></a>Nagyvállalati Szerződés fiókok

A fiókok, a nagyvállalati szerződésre való beléptetésről létrejöttek az új számlázási fiók nem támogatottak. A fiók-előfizetések tartoznak a számla szakaszban a megfelelő részleg számára létrehozott. Tulajdonosai válnak az Azure-előfizetés létrehozói és is előfizetéseinek létrehozásához és kezeléséhez a számla szakaszokat.

### <a name="notification-contacts"></a>Értesítési névjegyek

Értesítés-ügyfelek az Azure nagyvállalati szerződéssel kapcsolatos e-mail-üzeneteket küldi el. Ezek nem támogatottak az új számlázási fiók. Az Ön számlázási fiókjához számlázási profilok hozzáféréssel rendelkező felhasználók kapnak e-mailt az Azure-kreditek és számlákat.

### <a name="spending-quotas"></a>Költségkeret kvóták

A nagyvállalati szerződésre való beléptetésről a részlegek számára beállított költségkeret kvóták cserélése költségvetések új számlázási fiókban. Az egyes részlegek a regisztráció beállítása költségkvótáját költségvetési jön létre. A költségvetéshez további információkért lásd: [létrehozása és kezelése az Azure költségvetése](../cost-management/manage-budgets.md).

### <a name="cost-centers"></a>Költséghelyek

Az Azure-előfizetések a nagyvállalati szerződésre való beléptetésről a beállított költséghely átkerülnek az új számlázási fiók. Azonban a költséghelyek szervezeti egységek és a nagyvállalati szerződés fiókok nem támogatottak.

## <a name="additional-information"></a>További információ

A következő szakaszok a számlázási fiók beállításával kapcsolatos további információk.

### <a name="no-service-downtime"></a>A szolgáltatás leállása nélkül

Az előfizetésében meglévő Azure-szolgáltatások megszakítás nélkül futnak tovább. Csak az Azure-előfizetései számlázási kapcsolatait állítjuk át. Meglévő erőforrásait, erőforráscsoportjait vagy felügyeleti csoportjait ez nem érinti.

### <a name="user-access-to-azure-resources"></a>Azure-erőforrásokhoz való felhasználói hozzáférés

Az áttérés során nincs hatással az Azure-erőforrásokhoz való hozzáférést, amelyet az Azure RBAC (szerepköralapú hozzáférés-vezérlés) használatával.

### <a name="azure-reservations"></a>Azure-foglalások

A nagyvállalati szerződésre való beléptetésről bármely Azure-foglalásokat átkerül az új számlázási fiók. Az átállás alatt az előfizetéseiben érvényesített foglalási kedvezményekre nem lesz díj felszámolva.

### <a name="azure-marketplace-products"></a>Azure Marketplace-termékekkel

A nagyvállalati szerződésre való beléptetésről az Azure Marketplace-en termékek együtt az előfizetések kerülnek. Az áttérés során nem kell módosítania kellene a Piactéri termékek a szolgáltatás elérésére.

### <a name="support-plan"></a>Támogatási csomag

Támogatási előnyök átadása nem történik meg az átállás részeként. Az új számlázási fiók az Azure-előfizetések előnyeit lekérése új támogatási csomag megvásárlásához.

### <a name="past-charges-and-balance"></a>Múltbeli díjakat és elosztása

Váltás előtt díjait és a kreditek terheléselosztása az Azure Portalon keresztül a nagyvállalati szerződésre való beléptetésről lehet megtekinteni. <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>Mikor érdemes a telepítés befejeződött?

A számlázási fiók beállításának befejezéséhez, a nagyvállalati szerződésre való beléptetésről lejárta előtt. Ha lejár, a regisztráció, az Azure-előfizetésben lévő szolgáltatások továbbra is folyamatosan a futni fognak megszakítása nélkül. Azonban Ön díjakat számítjuk fel kereskedelmi a szolgáltatásokat.

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>A telepítés után a nagyvállalati szerződésre való beléptetésről módosításai

Azure-előfizetések az átállás manuálisan áthelyezhetők az új számlázási fiók után a nagyvállalati szerződésre való beléptetésről létrehozott. További információkért lásd: [első számlázási tulajdonosi jogokat, Azure-előfizetések más felhasználóktól](billing-mca-request-billing-ownership.md). A váltás után megvásárolható Azure-foglalások áthelyezése [Azure támogatási](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Emellett nyújthat a felhasználók hozzáférést a számlázási fiókjához a váltás után. További információkért lásd: [az Azure Portal számlázási szerepkörök kezelése](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="revert-the-transition"></a>Az átállás visszaállítása

A váltás nem állítható vissza. Után az új számlázási fiók továbbítjuk a számlázás az Azure-előfizetések, akkor nem lehet visszaállítani, a nagyvállalati szerződésre való beléptetésről.

### <a name="closing-your-browser-during-setup"></a>A böngésző bezárása a telepítés során

Végül kattintson a **átmenet Start**, bezárhatja a böngészőt. A beállítás az e-mailben kapott hivatkozásra kattintva térjen vissza, és indítsa el a váltás. Ha a böngésző, zárja be az átállás indítása után, az átmenet úgy működik tovább, a. Térjen vissza az átmenet állapotlapon átállás legfrissebb állapotának monitorozásához. E-mailt fog kapni, amikor a Váltás befejeződött.

## <a name="complete-the-setup-in-the-azure-portal"></a>Az Azure Portalon a telepítés befejezéséhez

A telepítés befejezéséhez hozzáférésre van szüksége az új számlázási fiók és a nagyvállalati szerződésre való beléptetésről is. További információkért lásd: [az Ön számlázási fiókjához a beállításának befejezéséhez szükséges hozzáférést](#access-required-to-complete-the-setup).

1. Jelentkezzen be az Azure Portalon, az e-mailben Önnek küldött a Microsoft Ügyfélszerződéséhez aláírásakor választott található hivatkozás segítségével.

2. Ha valaki a szervezetben a megállapodás, vagy nem rendelkezik az e-mailt, jelentkezzen be a következő hivatkozás használatával. Cserélje le **enrollmentNumber** sikerült megújítani a nagyvállalati szerződésen beléptetési számát.

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. Válassza ki **átmenet Start** a beállítása az előző lépésben. Ha kiválasztotta az átállás kezdete:

    ![A telepítővarázsló bemutató képernyőkép](./media/billing-mca-setup-account/ea-mca-set-up-wizard.png)

    - A nagyvállalati szerződés hierarchia megfelelő számlázási hierarchia az új számlázási fiók jön létre. További információkért lásd: [megismerheti a számlázási hierarchia módosításai](#understand-changes-to-your-billing-hierarchy).
    - A nagyvállalati szerződésre való beléptetésről rendszergazdákat, hogy azok továbbra is kezelheti a szervezete számlázását hozzáférést kapnak az új számlázási fiók.
    - A számlázás az Azure-előfizetések továbbítjuk az új fiókot. **Nem kell semmilyen hatással lévő Azure-szolgáltatások az átállás során. Futnak fog tartani üzemszünet nélkül**.
    - Ha rendelkezik Azure-foglalásokat, áthelyezett az új számlázási fiók azonos kedvezményt és kifejezés. A foglalási kedvezményt a váltás során alkalmazandó továbbra is.

4. Az átállás állapotát figyelemmel kísérheti a a **átmeneti állapot** lapot.

   ![Az átmeneti állapot bemutató képernyőkép](./media/billing-mca-setup-account/ea-mca-set-up-status.png)

## <a name="validate-the-billing-account-is-set-up-properly"></a>A számlázási ellenőrzése fiók megfelelően van beállítva

 Ellenőrizze a következőket annak biztosítása érdekében az új számlázási fiók megfelelően van beállítva:

### <a name="azure-subscriptions"></a>Azure-előfizetések

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A Keresés **Cost Management és számlázás**.

   ![Az Azure portál keresési bemutató képernyőkép](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Válassza ki a számlázási profilt. A hozzáférést, attól függően szükség lehet egy számlázási fiók kiválasztása. Válassza ki a számlázási fiók **profilok számlázási** és majd a számlázási profilja.

4. Válassza ki **Azure-előfizetések** bal oldalán.

   ![Előfizetések listáját bemutató képernyőkép](./media/billing-mca-setup-account/billing-mca-subscriptions-post-transition.png)

Az új számlázási fiók érdekében átalakulnak a nagyvállalati szerződésre való beléptetésről az Azure-előfizetések az Azure-előfizetések oldalon jelennek meg. Ha úgy véli, hogy hiányzik egy előfizetést, áttérés a számlázás az előfizetés manuálisan az Azure Portalon. További információkért lásd: [első számlázási tulajdonosi jogokat, más felhasználók az Azure-előfizetések](billing-mca-request-billing-ownership.md)

### <a name="azure-reservations"></a>Azure-foglalások

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A Keresés **Cost Management és számlázás**.

   ![Az Azure portál keresési bemutató képernyőkép](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Válassza ki a számlát szakaszt. A hozzáférést, attól függően szükség lehet egy számlázási fiók és a egy számlázási profilja kiválasztásához.  Válassza ki a számlázási fiók vagy a számlázási profilja **szakaszok számlázása** és majd egy számla szakaszban.

    ![Számla szakasz post átmenet listáját bemutató képernyőkép](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. Válassza ki **minden termék** bal oldalán.

5. A Keresés **fenntartott**.

    ![Előfizetések post átmenet listáját bemutató képernyőkép](./media/billing-mca-setup-account/billing-mca-azure-reservations-post-transition.png)

Az Azure foglalásokat, hogy az új számlázási fiók kerülnek át a nagyvállalati szerződésre való beléptetésről az összes termék oldalon jelennek meg. Ismételje meg a lépéseket, győződjön meg arról, hogy minden Azure-foglalást a nagyvállalati szerződésre való beléptetésről átkerülnek a számla szakaszokat. Ha úgy véli, hogy minden olyan Azure-foglalás hiányzik, [lépjen kapcsolatba az Azure-támogatás](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a Foglalás áthelyezése az új számlázási fiók.

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>Hozzáférés a vállalati rendszergazdák a számlázási profilban

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A Keresés **Cost Management és számlázás**.

   ![Az Azure portál keresési bemutató képernyőkép](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Válassza ki a létrehozott regisztrációjához számlázási profilt. A hozzáférést, attól függően szükség lehet egy számlázási fiók kiválasztása.  Válassza ki a számlázási fiók **profilok számlázási** és majd a számlázási profilja.

4. Válassza ki **hozzáférés-vezérlés (IAM)** bal oldalán.

   ![Hozzáférés a vállalati rendszergazdák post átmeneti bemutató képernyőkép](./media/billing-mca-setup-account/billing-mca-ea-admins-access-post-transition.png)

Vállalati rendszergazdák profil tulajdonosai a vállalati rendszergazdák csak olvasási engedélyekkel rendelkező felsorolt közben számlázási, profil-olvasók számlázási szerepelnek. Ha úgy véli, hogy hiányzik a hozzáférést a vállalati rendszergazdák, számukra hozzáférést biztosíthat az Azure Portalon. További információkért lásd: [kezelése az Azure Portal számlázási szerepkörök](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>Hozzáférés a vállalati rendszergazdák, a részleg rendszergazdái és a számla szakaszokon tulajdonosai

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A Keresés **Cost Management és számlázás**.

   ![Az Azure portál keresési bemutató képernyőkép](./media/billing-mca-setup-account/billing-search-cost-management-billing.png).

3. Válassza ki a számlát szakaszt. Számla szakaszok a neve megegyezik a megfelelő részleg felhasználói regisztrációk nagyvállalati szerződéssel rendelkezik. A hozzáférést, attól függően szükség lehet egy számlázási profilja és a egy számlázási fiók kiválasztásához. A számlázási vagy a számlázási fiók, jelölje ki **szakaszok számlázása** , majd egy számla szakaszt.

   ![Számla szakasz post átmenet listáját bemutató képernyőkép](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. Válassza ki **hozzáférés-vezérlés (IAM)** bal oldalán.

    ![Képernyőkép a osztály és rendszergazdák hozzáférési bejegyzés átmenet fiók hozzáférési](./media/billing-mca-setup-account/billing-mca-department-account-admins-access-post-transition.png)

Vállalati rendszergazdák és a részleg rendszergazdái szerepelnek számla szakasz tulajdonosai vagy a számla szakasz olvasók közben a részleg tulajdonosai Azure-előfizetés alkotói megjelöléssel láthatók. Ismételje meg az összes számla szakasz a nagyvállalati szerződésre való beléptetésről az összes részlegek számára a hozzáférés ellenőrzéséhez. Nem minden részleg részét tulajdonosai engedély be a egy számla szakasz nevű **alapértelmezett számla szakasz**. Ha úgy véli, hogy hiányzik a hozzáférést minden olyan rendszergazdák, számukra hozzáférést biztosíthat az Azure Portalon. További információkért lásd: [kezelése az Azure Portal számlázási szerepkörök](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha segítségre van szüksége, kell [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések

- [Az új számlázási fiók használatának első lépései](billing-mca-overview.md)

- [A Microsoft vevői szerződés az Ön számlázási fiókjához a teljes nagyvállalati szerződés feladatok](billing-mca-enterprise-operations.md)

- [Ön számlázási fiókjához való hozzáférés kezelése](billing-understand-mca-roles.md)
