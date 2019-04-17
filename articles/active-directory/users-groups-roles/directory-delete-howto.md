---
title: Törölje az Azure AD-címtár – Azure Active Directory |} A Microsoft Docs
description: Azt ismerteti, hogyan készíti elő az Azure AD-címtár törlésre, ideértve az önkiszolgáló könyvtárak
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91ac6b4530414850c52605bac8cb701aa2b877d4
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607296"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Az Azure Active Directoryban könyvtár törlése

A törölt Azure AD-címtár abban a könyvtárban található összes erőforrás is törlődik. A szervezet felkészítése a csökkenthetik az összes kapcsolódó erőforrás törlése előtt. Csak egy Azure Active Directory (Azure AD) globális rendszergazda törölheti az Azure AD-címtárat a portálról.

## <a name="prepare-the-directory"></a>A könyvtár előkészítése

Az Azure AD, amíg nem adja át, több ellenőrzést könyvtár nem törölhető. Az ellenőrzések csökkentse annak veszélyét, hogy az Azure AD-címtár törlése kedvezőtlen hatással van a bejelentkezni az Office 365 vagy a hozzáférés az Azure-erőforrások, például a felhasználói hozzáférés. Például ha az egy előfizetéshez tartozó címtár véletlenül törölnek, majd felhasználók nem férhetnek hozzá az adott előfizetéshez tartozó Azure-erőforrások. A rendszer a következő feltételek teljesülését ellenőrzi:

* Egy globális rendszergazda törölheti a címtárat, kivéve a címtár egyik felhasználó sem lehet. A címtár törlése előtt az összes többi felhasználót törölni kell. Ha a helyszíni felhasználók szinkronizálása, majd szinkronizálási először ki kell kapcsolni, és a felhasználók törölni kell a felhőalapú címtárat az Azure portal vagy Azure PowerShell-parancsmagok használatával.
* A címtárban nem lehetnek alkalmazások. Az alkalmazásokat a címtár törlése előtt el kell távolítani.
* Nem a multi-factor authentication-szolgáltatók, a címtárhoz társított lehet.
* Törölni kell minden, a címtárhoz társított Microsoft Online Services-előfizetést, például a Microsoft Azure-t, az Office 365-öt vagy a Prémium szintű Azure AD-t. Ha például az alapértelmezett címtár az Azure-ban lett létrehozva, nem törölheti azt mindaddig, amíg Azure-előfizetésének hitelesítése továbbra is ezen a címtáron alapul. Hasonlóképpen olyan címtárat sem törölhet, amelyhez egy másik felhasználó előfizetést társított.

## <a name="delete-the-directory"></a>A könyvtár törlése

1. Jelentkezzen be a [Azure AD felügyeleti központ](https://aad.portal.azure.com) egy olyan fiókkal, amely a szervezet globális rendszergazdája.

2. Válassza az **Azure Active Directory** elemet.

3. A törölni kívánt címtárra váltani.
  
   ![Erősítse meg a szervezet törlése előtt](./media/directory-delete-howto/delete-directory-command.png)

4. Válassza ki **Delete directory**.
  
   ![Válassza ki a parancs törli a szervezet](./media/directory-delete-howto/delete-directory-list.png)

5. A címtár nem felel meg egy vagy több ellenőrzést, ha van megadott hogyan adhatók át a további információkra mutató hivatkozás. Után át kell adnia az összes ellenőrzés, jelölje be az **törlése** a folyamat befejezéséhez.

## <a name="if-you-cant-delete-the-directory"></a>Ha a könyvtár nem törölhető

Ha konfigurálta az Azure AD-címtárhoz, előfordulhat, hogy is aktiválta előfizetések licenc-alapú Azure AD Premium P2, Office 365 vállalati prémium vagy Enterprise Mobility + Security E5 hasonlóan a szervezet számára. Véletlen adatvesztés elkerülése érdekében olyan címtárat sem törölhet, csak az előfizetések teljes mértékben törlése. Az előfizetések kell lennie egy **megszüntetett** lehetővé teszi, hogy a címtár törlésre. Egy **lejárt** vagy **megszakított** előfizetés áthelyezi a **letiltott** állapotát, és az utolsó fáziséit a **megszüntetett** állapota.

Mire számítson, ha az Office 365 próba-előfizetés lejárt (nem fizetős Partner/CSP, nagyvállalati szerződéssel vagy mennyiségi licencelési beleértve) tekintse meg az alábbi táblázat. Az Office 365 adatok megőrzésére és az előfizetés életciklusának további információkért lásd: [mi történik az adatokkal és a hozzáférés, ha az Office 365 üzleti előfizetési befejeződik?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Előfizetés állapota | Adatok | Adatokhoz való hozzáférés
----- | ----- | -----
Aktív (próbaverzió 30 nap) | Az összes elérhető adat | Felhasználók normális férhetnek hozzá az Office 365-fájlok vagy alkalmazások<br>A rendszergazdák normál hozzáférhet a Microsoft 365 felügyeleti központot és erőforrások 
Lejárt (30 nap) | Az összes elérhető adat| Felhasználók normális férhetnek hozzá az Office 365-fájlok vagy alkalmazások<br>A rendszergazdák normál hozzáférhet a Microsoft 365 felügyeleti központot és erőforrások
Le van tiltva (30 nap) | Csak rendszergazda számára elérhető adatok | Felhasználók nem férhetnek hozzá, az Office 365-fájlok vagy alkalmazások<br>A rendszergazdák a Microsoft 365 felügyeleti központ eléréséhez, de nem szükséges licencek kiosztása vagy felhasználók frissítése
Felfüggesztést (30 nap után le van tiltva) | Törölt adatok (Ha nincs más szolgáltatások is automatikusan törölve) | Felhasználók nem férhetnek hozzá, az Office 365-fájlok vagy alkalmazások<br>A rendszergazdák hozzáférhetnek a Microsoft 365 felügyeleti központban vásárolhat és más előfizetések kezelése

## <a name="delete-a-subscription"></a>Előfizetés törlése

A megszüntetett állapotban három nap, a Microsoft 365 felügyeleti központ használatával a törlendő előfizetés is elhelyezhető.

1. Jelentkezzen be a [Microsoft 365 felügyeleti központban](https://admin.microsoft.com) a szervezet globális rendszergazdai fiókkal. Ha a "Contoso" címtár, amely a contoso.onmicrosoft.com-ot a kezdeti alapértelmezett tartomány törölni próbál, jelentkezzen be egy egyszerű felhasználónév például admin@contoso.onmicrosoft.com.

2. Válassza ki **számlázási** válassza **előfizetések**, majd válassza ki az előfizetést, szeretné visszavonni. Miután rákattintott **Mégse**, frissítse az oldalt.
  
   ![Előfizetés törlése a hivatkozás törlése](./media/directory-delete-howto/delete-command.png)
  
3. Válassza ki **törlése** törölni az előfizetést, és fogadja el a feltételeket és kikötéseket. Összes adat véglegesen törlődik a három napon belül. Ha megváltoztatja döntését újraaktiválhatja az előfizetés a 3 napos időszakban.
  
   ![Figyelmesen olvassa el a használati feltételek](./media/directory-delete-howto/delete-terms.png)

4. Most az előfizetés állapota megváltozott, az előfizetés törlésre van kijelölve. Az előfizetés beírja a **megszüntetett** állapot 72 óra múlva.

5. Miután egy előfizetést törölt a címtárban, és 72 óra telt, jelentkezhet be az Azure AD felügyeleti központ biztonsági újra és van kell nincs szükség művelet, és nem blokkolja-e a címtár törlése előfizetések. Sikerült törölni az Azure AD-címtár tudjon kell lennie.
  
   ![át, törlés képernyő előfizetés ellenőrzése](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Van egy próba-előfizetést, amely blokkolja a törlése

Nincsenek [önkiszolgáló regisztrációs termékek](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) , a Microsoft Power bi-ban, a Rights Management Services, a Microsoft Power Apps vagy a Dynamics 365, az egyes felhasználók Office 365-höz, ami szintén létrehoz egy vendégfelhasználó való hitelesítéshez keresztül regisztrálhat. az Azure AD-címtárhoz. Önkiszolgáló termékek directory törlések letiltása, amíg teljesen törlődik a címtárban, adatvesztés elkerülése érdekében. Azok törölhetők kizárólag az Azure AD-rendszergazda számára, hogy a felhasználó regisztrált egyenként, vagy hozzá lett rendelve a termék.

Önkiszolgáló regisztráció termékek hogyan hozzá vannak rendelve a két típusa van: 

* Szervezeti szintű hozzárendelése: Azure AD-rendszergazdát a termék rendel a teljes szervezet számára, és a felhasználó is lehet aktívan használnak a szolgáltatás a szervezeti szintű hozzárendelés akkor is, ha azok nem teszi lehetővé külön-külön.
* Felhasználó-hozzárendelés szint: Önkiszolgáló regisztráció során egy adott felhasználó lényegében rendel a termék maguk nem rendszergazda. Ha a szervezet rendszergazdája felügyeljen (lásd: [rendszergazdai átvételt egy nem felügyelt címtár](domains-admin-takeover.md), rendelheti hozzá a rendszergazda is közvetlenül a termék a nem önkiszolgáló felhasználók számára.  

Amikor megkezdi az önkiszolgáló regisztráció termék törlését, a művelet véglegesen törli az adatokat, és eltávolítja az összes felhasználói hozzáférés a szolgáltatáshoz. Azok a felhasználók, az ajánlat lett hozzárendelve, egyenként, vagy a szervezet szintjén majd le van tiltva, a bejelentkezés, vagy fér hozzá a meglévő adatokat. Ha szeretné megakadályozni az adatvesztést a önkiszolgáló regisztrációs termékkel, például [a Microsoft Power BI-irányítópultok](https://docs.microsoft.com/power-bi/service-export-to-pbix) vagy [Rights Management Services szabályzatkonfiguráció](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy), győződjön meg arról, hogy az adatok biztonsági mentésének és mentett máshol.

Jelenleg elérhető önkiszolgáló regisztrációs termékekre és szolgáltatásokra vonatkozó további információkért lásd: [elérhető önkiszolgáló programok](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs).

Mire számítson, ha az Office 365 próba-előfizetés lejárt (nem fizetős Partner/CSP, nagyvállalati szerződéssel vagy mennyiségi licencelési beleértve) tekintse meg az alábbi táblázat. Az Office 365 adatok megőrzésére és az előfizetés életciklusának további információkért lásd: [mi történik az adatokkal és a hozzáférés, ha az Office 365 üzleti előfizetési befejeződik?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide).

Termék állapota | Adatok | Adatokhoz való hozzáférés
------------- | ---- | --------------
Aktív (próbaverzió 30 nap) | Az összes elérhető adat | Felhasználók normális férhetnek hozzá az önkiszolgáló regisztrációs termék, fájlok vagy alkalmazások<br>A rendszergazdák normál hozzáférhet a Microsoft 365 felügyeleti központot és erőforrások
Törölve | Adat törölve | Felhasználók nem férhetnek hozzá, önkiszolgáló regisztrációs termék, fájlok vagy alkalmazások<br>A rendszergazdák hozzáférhetnek a Microsoft 365 felügyeleti központban vásárolhat és más előfizetések kezelése

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Hogyan törölhetek egy önkiszolgáló regisztrációs terméket az Azure Portalon?

Önkiszolgáló regisztrációs termék például a Microsoft Power bi-ban vagy az Azure Rights Management Services, elhelyezhet egy **törlése** azonnal törölve az Azure AD-portálon az állapot.

1. Jelentkezzen be a [Azure AD felügyeleti központ](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) a szervezet globális rendszergazdai fiókkal. Ha a "Contoso" címtár, amely a contoso.onmicrosoft.com-ot a kezdeti alapértelmezett tartomány törölni próbál, jelentkezzen be egy egyszerű felhasználónév például admin@contoso.onmicrosoft.com.

2. Válassza ki **licencek**, majd válassza ki **önkiszolgáló regisztrációs termékek**. Láthatja az összes önkiszolgáló regisztrációs termék elkülönítve a munkaállomás-alapú előfizetéseket. Válassza ki a termék véglegesen törli. Íme egy példa a Microsoft Power bi-ban:

    ![a felhasználónév rosszul adott meg, vagy nem található](./media/directory-delete-howto/licenses-page.png)

3. Válassza ki **törlése** törölheti a terméket, és fogadja el a használati adatok azonnal és visszavonhatatlanul törlődik. A törlési művelet eltávolítja az összes felhasználó és szervezet férhessenek hozzá a termék. Kattintson az Igen gombra a törlés folytatná.  

    ![a felhasználónév rosszul adott meg, vagy nem található](./media/directory-delete-howto/delete-product.png)

4. Ha bejelöli **Igen**, a törlés önkiszolgáló termék kezdődik. Egy üzenetet kap, a Törlés folyamatban van.  

    ![a felhasználónév rosszul adott meg, vagy nem található](./media/directory-delete-howto/progress-message.png)

5. Most az önkiszolgáló regisztráció termék állapota megváltozott a **törölt**. Amikor frissíti az oldalt, a termék el kell távolítani a **önkiszolgáló regisztrációs termékek** lapot.  

    ![a felhasználónév rosszul adott meg, vagy nem található](./media/directory-delete-howto/product-deleted.png)

6. Ha törölt minden termék, jelentkezhet be az Azure AD felügyeleti központ újra, és nincs szükség művelet, és nem blokkolja-e a címtár törlésre termékek kell. Sikerült törölni az Azure AD-címtár tudjon kell lennie.

    ![a felhasználónév rosszul adott meg, vagy nem található](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>További lépések

[Az Azure Active Directory-dokumentáció](https://docs.microsoft.com/azure/active-directory/)
