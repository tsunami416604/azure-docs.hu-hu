---
title: Azure AD-könyvtár törlése – Azure Active Directory | Microsoft dokumentumok
description: Bemutatja, hogyan készíthet elő egy Azure AD-könyvtárat törlésre, beleértve az önkiszolgáló könyvtárakat is
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
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47a60ed44ddf057ef983f8f76f23fd784bc3efd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961816"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Könyvtár törlése az Azure Active Directoryban

Ha egy Azure AD-címtár törlődik, a címtárban található összes erőforrás is törlődik. Készítse elő a szervezetet a kapcsolódó erőforrások törlésének minimalizálásával. Csak egy Azure Active Directory (Azure AD) globális rendszergazda törölheti az Azure AD-címtárat a portálról.

## <a name="prepare-the-directory"></a>A könyvtár előkészítése

Az Azure AD-ben nem törölhetegy könyvtárat, amíg több ellenőrzésen nem halad át. Ezek az ellenőrzések csökkentik annak kockázatát, hogy az Azure AD-címtár törlése negatívan befolyásolja a felhasználói hozzáférést, például az Office 365-be való bejelentkezést vagy az Azure-beli erőforrások elérését. Például ha az előfizetéshez társított könyvtár véletlenül törlődik, majd a felhasználók nem férhetnek hozzá az Azure-erőforrásokhoz az adott előfizetéshez. A rendszer a következő feltételek teljesülését ellenőrzi:

* A címtárban csak egy globális rendszergazda lehet felhasználó, aki törli a könyvtárat. A címtár törlése előtt az összes többi felhasználót törölni kell. Ha a felhasználók szinkronizálása a helyszíni, majd a szinkronizálást először ki kell kapcsolni, és a felhasználókat törölni kell a felhőbeli címtárban az Azure Portalon vagy az Azure PowerShell-parancsmagok használatával.
* A címtárban nem lehetnek alkalmazások. A könyvtár törlése előtt minden alkalmazást el kell távolítani.
* A címtárhoz nem lehetnek többtényezős hitelesítésszolgáltatók.
* Törölni kell minden, a címtárhoz társított Microsoft Online Services-előfizetést, például a Microsoft Azure-t, az Office 365-öt vagy a Prémium szintű Azure AD-t. Ha például az alapértelmezett címtár az Azure-ban lett létrehozva, nem törölheti azt mindaddig, amíg Azure-előfizetésének hitelesítése továbbra is ezen a címtáron alapul. Hasonlóképpen olyan címtárat sem törölhet, amelyhez egy másik felhasználó előfizetést társított.

## <a name="delete-the-directory"></a>A könyvtár törlése

1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a szervezet globális rendszergazdája.

2. Válassza az **Azure Active Directory**lehetőséget.

3. Váltson a törölni kívánt könyvtárra.
  
   ![Szervezet megerősítése törlés előtt](./media/directory-delete-howto/delete-directory-command.png)

4. Válassza **a Könyvtár törlése**lehetőséget.
  
   ![válassza ki a parancsot, hogy törölje a szervezet](./media/directory-delete-howto/delete-directory-list.png)

5. Ha a címtár nem felel meg egy vagy több ellenőrzésen, akkor a rendszer további információkat tartalmaz a továbbhaladásról. Miután átment az összes ellenőrzésen, a folyamat befejezéséhez válassza a **Törlés** lehetőséget.

## <a name="if-you-cant-delete-the-directory"></a>Ha nem tudja törölni a könyvtárat

Az Azure AD-címtár konfigurálásakor előfordulhat, hogy aktiválta a licencalapú előfizetéseket a szervezetéhez, például az Azure AD Premium P2, az Office 365 Vállalati Prémium verzió vagy az Enterprise Mobility + Security E5. A véletlen adatvesztés elkerülése érdekében nem törölhet idáig a címtárat, amíg az előfizetések teljesen meg nem törlődnek. Az előfizetéseknek **megszüntetett** állapotban kell lenniük a címtár törlésének engedélyezéséhez. Egy **lejárt** vagy **törölt** előfizetés a **Letiltott** állapotba kerül, és a végső szakasz a **megszüntetett** állapot.

A próbaverziós Office 365-előfizetés lejártakor (a fizetett partner/csp, nagyvállalati szerződés vagy mennyiségi licencelés nélkül) az alábbi táblázatban található. Az Office 365 adatmegőrzési és előfizetési életciklusáról a [Mi történik az adataimmal és a hozzáférésemmel az Office 365 vállalati verziós előfizetésem lejártakor című](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3)témakörben talál. 

Előfizetés állapota | Adatok | Az adatokhoz való hozzáférés
----- | ----- | -----
Aktív (30 nap a tárgyalásra) | Mindenki számára elérhető adatok | A felhasználók normál hozzáféréssel rendelkeznek az Office 365-fájlokhoz vagy alkalmazásokhoz<br>A rendszergazdák normál hozzáféréssel rendelkeznek a Microsoft 365 felügyeleti központhoz és az erőforrásokhoz 
Lejárt (30 nap) | Mindenki számára elérhető adatok| A felhasználók normál hozzáféréssel rendelkeznek az Office 365-fájlokhoz vagy alkalmazásokhoz<br>A rendszergazdák normál hozzáféréssel rendelkeznek a Microsoft 365 felügyeleti központhoz és az erőforrásokhoz
Mozgáskorlátozott (30 nap) | A rendszergazdák számára csak a rendszergazdák számára elérhető adatok | A felhasználók nem férnek hozzá az Office 365-fájlokhoz vagy az alkalmazásokhoz<br>A rendszergazdák hozzáférhetnek a Microsoft 365 Felügyeleti központhoz, de nem rendelhetnek licenceket a felhasználókhoz, illetve nem frissíthetik azokat
Deprovisioned (30 nappal a letiltás után) | Adatok törlése (automatikusan törlődik, ha más szolgáltatás nincs használatban) | A felhasználók nem férnek hozzá az Office 365-fájlokhoz vagy az alkalmazásokhoz<br>A rendszergazdák hozzáférhetnek a Microsoft 365 felügyeleti központhoz, hogy más előfizetéseket vásároljanak és kezeljenek

## <a name="delete-a-subscription"></a>Előfizetés törlése

Az előfizetést a Microsoft 365 Felügyeleti központ használatával három napon belül törölni kell a **megszüntetett** állapotba.

1. Jelentkezzen be a [Microsoft 365 Felügyeleti központba](https://admin.microsoft.com) egy olyan fiókkal, amely a szervezet globális rendszergazdája. Ha a kezdeti alapértelmezett tartományt tartalmazó "Contoso" könyvtárat szeretné törölni, contoso.onmicrosoft.com, admin@contoso.onmicrosoft.comjelentkezzen be egy upn-nel, például .

2. Tekintse meg az új Microsoft 365 felügyeleti központ előnézetét, és győződjön meg arról, hogy **az új felügyeleti központ kipróbálása** kapcsoló engedélyezve van.

   ![Az m365-ös felügyeleti központ új élményének megtekintése](./media/directory-delete-howto/preview-toggle.png)

3. Ha az új felügyeleti központ engedélyezve van, törölnie kell az előfizetést, mielőtt törölheti. Válassza **a Számlázás** lehetőséget, majd válassza a Termékek & **szolgáltatások**lehetőséget, majd válassza az **Előfizetés lemondása lehetőséget** a lemondani kívánt előfizetéshez. Egy visszajelzési oldalra kerül.

   ![A lemondani kívánt előfizetés kiválasztása](./media/directory-delete-howto/cancel-choose-subscription.png)

4. Töltse ki a visszajelzési űrlapot, és válassza az **Előfizetés lemondása** lehetőséget az előfizetés lemondásához.

   ![A Lemondás parancs az előfizetés előzetes verziójában](./media/directory-delete-howto/cancel-command.png)

5. Most már törölheti az előfizetést. Válassza a törölni kívánt előfizetés **Törlés** lehetőséget. Ha nem találja az előfizetést a **Termékek & szolgáltatások** lapon, győződjön meg arról, hogy az Előfizetés **állapota** **Minden.**

   ![Hivatkozás törlése az előfizetés törléséhez](./media/directory-delete-howto/delete-command.png)

6. Válassza **az Előfizetés törlése lehetőséget** az előfizetés törléséhez és a feltételek elfogadásához. Minden adat három napon belül véglegesen törlődik. Ha meggondolja magát, a három napos időszak alatt [újraaktiválhatja az előfizetést.](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide)
  
   ![figyelmesen olvassa el a feltételeket](./media/directory-delete-howto/delete-terms.png)

7. Most az előfizetés állapota megváltozott, és az előfizetés törlésre van megjelölve. Az előfizetés 72 órával később lép be a **megszüntetett** állapotba.

8. Miután törölt egy előfizetést a címtárban, és 72 óra telt el, újra bejelentkezhet az Azure AD felügyeleti központba, és nem kell elvégeznie a szükséges műveletet, és nincs előfizetés blokkolja a címtár törlését. Képesnek kell lennie az Azure AD-címtár sikeres törlésére.
  
   ![át az előfizetés-ellenőrzésen a törlési képernyőn](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Nekem van egy próba-előfizetés, amely blokkolja a törlés

Vannak [önkiszolgáló regisztrációs termékek,](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) például a Microsoft Power BI, a Rights Management Services, a Microsoft Power Apps vagy a Dynamics 365, az egyes felhasználók az Office 365-ön keresztül regisztrálhatnak, amely egy vendégfelhasználót is létrehoz hitelesítésre az Azure AD-címtárban. Ezek az önkiszolgáló termékek blokkolják a címtár-törléseket, amíg teljesen el nem törlődnek a címtárból, hogy elkerüljék az adatvesztést. Csak az Azure AD-rendszergazda törölheti őket, függetlenül attól, hogy a felhasználó külön-külön regisztrált, vagy a termékhez volt rendelve.

Az önkiszolgáló regisztrációs termékeknek két típusa van a hozzárendelésük módjában: 

* Szervezeti szintű hozzárendelés: Az Azure AD-rendszergazda hozzárendeli a terméket a teljes szervezethez, és a felhasználó aktívan használhatja a szolgáltatást ezzel a szervezeti szintű hozzárendeléssel, még akkor is, ha nincs külön licenccel.
* Felhasználói szintű hozzárendelés: Az önkiszolgáló regisztráció során az egyéni felhasználó lényegében rendszergazda nélkül rendeli hozzá a terméket saját magához. Amint a szervezetet egy rendszergazda kezeli (lásd: [Egy nem felügyelt címtár rendszergazdai átvétele](domains-admin-takeover.md), majd a rendszergazda közvetlenül hozzárendelheti a terméket a felhasználókhoz önkiszolgáló regisztráció nélkül.  

Az önkiszolgáló regisztrációs termék törlésének megkezdésekor a művelet véglegesen törli az adatokat, és eltávolítja a szolgáltatáshoz való összes felhasználói hozzáférést. Az ajánlathoz egyénileg vagy szervezeti szinten hozzárendelt felhasználók ezután le vannak tiltva a bejelentkezésben vagy a meglévő adatok elérésében. Ha az önkiszolgáló regisztrációs termékkel, például a [Microsoft Power BI irányítópultjaival](https://docs.microsoft.com/power-bi/service-export-to-pbix) vagy a Rights Management Services [házirend-konfigurációjával](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy)szeretné megakadályozni az adatvesztést, győződjön meg arról, hogy az adatokról biztonsági másolatot készít, és máshol menti őket.

A jelenleg elérhető önkiszolgáló regisztrációs termékekről és szolgáltatásokról az [Elérhető önkiszolgáló programok](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs)című témakörben talál további információt.

A próbaverziós Office 365-előfizetés lejártakor (a fizetett partner/csp, nagyvállalati szerződés vagy mennyiségi licencelés nélkül) az alábbi táblázatban található. Az Office 365 adatmegőrzési és előfizetési életciklusáról a [Mi történik az adataimmal és a hozzáférésemmel az Office 365 vállalati verziós előfizetésem lejártakor című](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide)témakörben talál.

Termék állapota | Adatok | Az adatokhoz való hozzáférés
------------- | ---- | --------------
Aktív (30 nap a tárgyalásra) | Mindenki számára elérhető adatok | A felhasználók normál hozzáféréssel rendelkeznek az önkiszolgáló regisztrációs termékekhez, fájlokhoz vagy alkalmazásokhoz<br>A rendszergazdák normál hozzáféréssel rendelkeznek a Microsoft 365 felügyeleti központhoz és az erőforrásokhoz
Törölve | Törölt adatok | A felhasználók nem férhetnek hozzá az önkiszolgáló regisztrációs termékhez, fájlokhoz vagy alkalmazásokhoz<br>A rendszergazdák hozzáférhetnek a Microsoft 365 felügyeleti központhoz, hogy más előfizetéseket vásároljanak és kezeljenek

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Hogyan törölhetek egy önkiszolgáló regisztrációs terméket az Azure Portalon?

Az önkiszolgáló regisztrációs termékeket, például a Microsoft Power BI-t vagy az Azure Rights Management Services-t törlési állapotba **helyezheti,** hogy azonnal törölhesse az Azure AD-portálról.

1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) egy olyan fiókkal, amely a szervezet globális rendszergazdája. Ha a kezdeti alapértelmezett tartományt tartalmazó "Contoso" könyvtárat contoso.onmicrosoft.com, jelentkezzen be admin@contoso.onmicrosoft.comegy upn-nel, például .

2. Válassza **a Licencek**lehetőséget, majd az **Önkiszolgáló regisztrációs termékek**lehetőséget. Az összes önkiszolgáló regisztrációs terméket az ülésalapú előfizetésektől elkülönítve láthatja. Válassza ki a véglegesen törölni kívánt terméket. Íme egy példa a Microsoft Power BI-ban:

    ![a felhasználónév elgépelt vagy nem található](./media/directory-delete-howto/licenses-page.png)

3. Válassza a **Törlés** lehetőséget a termék törléséhez és az adatok azonnali és visszavonhatatlan törlésére vonatkozó feltételek elfogadásához. Ez a törlési művelet eltávolítja az összes felhasználót, és eltávolítja a szervezet hozzáférését a termékhez. A törléssel való előrelépéshez kattintson az Igen gombra.  

    ![a felhasználónév elgépelt vagy nem található](./media/directory-delete-howto/delete-product.png)

4. Ha az **Igen**lehetőséget választja, az önkiszolgáló termék törlése megkezdődik. Van egy értesítés, amely tájékoztatja önt a folyamatban lévő törlésről.  

    ![a felhasználónév elgépelt vagy nem található](./media/directory-delete-howto/progress-message.png)

5. Most az önkiszolgáló regisztrációs termék állapota **Törölt**állapotra változott. Az oldal frissítésekor a terméket el kell távolítani az **Önkiszolgáló regisztrációs termékek** lapról.  

    ![a felhasználónév elgépelt vagy nem található](./media/directory-delete-howto/product-deleted.png)

6. Miután törölte az összes terméket, újra bejelentkezhet az Azure AD felügyeleti központba, és nem kell elvégeznie a szükséges műveletet, és nincs olyan termék, amely blokkolja a címtár törlését. Képesnek kell lennie az Azure AD-címtár sikeres törlésére.

    ![a felhasználónév elgépelt vagy nem található](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>További lépések

[Azure Active Directory-dokumentáció](https://docs.microsoft.com/azure/active-directory/)
