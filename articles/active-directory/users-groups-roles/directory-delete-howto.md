---
title: Azure AD-címtár törlése – Azure Active Directory | Microsoft Docs
description: Elmagyarázza, hogyan lehet előkészíteni egy Azure AD-címtárat törlésre, beleértve az önkiszolgáló címtárakat is
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "73961816"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Könyvtár törlése Azure Active Directory

Az Azure AD-címtár törlésekor a rendszer a címtárban található összes erőforrást is törli. Készítse elő a szervezetét, hogy a törlés előtt minimalizálja a hozzájuk tartozó erőforrásokat. Csak egy Azure Active Directory (Azure AD) globális rendszergazda törölhet egy Azure AD-címtárat a portálról.

## <a name="prepare-the-directory"></a>A könyvtár előkészítése

Nem törölhet címtárat az Azure AD-ben, amíg át nem halad több ellenőrzés. Ezek az ellenőrzések csökkentik annak a kockázatát, hogy az Azure AD-címtár törlése negatív hatással van a felhasználói hozzáférésre, például az Office 365-be való bejelentkezésre vagy az Azure-beli erőforrásokhoz való hozzáférésre. Ha például egy előfizetéshez társított könyvtárat véletlenül törölnek, akkor a felhasználók nem férhetnek hozzá az adott előfizetéshez tartozó Azure-erőforrásokhoz. A rendszer a következő feltételek teljesülését ellenőrzi:

* A címtárban nem lehetnek felhasználók, kivéve egy globális rendszergazda, aki törli a könyvtárat. A címtár törlése előtt az összes többi felhasználót törölni kell. Ha a felhasználók szinkronizálása a helyszíni rendszerből történik, akkor először ki kell kapcsolni a szinkronizálást, és a felhasználóknak a Azure Portal vagy Azure PowerShell parancsmagokkal kell törölniük a Felhőbeli könyvtárban.
* A címtárban nem lehetnek alkalmazások. A címtár törlése előtt minden alkalmazást el kell távolítani.
* A címtárhoz nem köthető multi-Factor Authentication-szolgáltatók.
* Törölni kell minden, a címtárhoz társított Microsoft Online Services-előfizetést, például a Microsoft Azure-t, az Office 365-öt vagy a Prémium szintű Azure AD-t. Ha például az alapértelmezett címtár az Azure-ban lett létrehozva, nem törölheti azt mindaddig, amíg Azure-előfizetésének hitelesítése továbbra is ezen a címtáron alapul. Hasonlóképpen olyan címtárat sem törölhet, amelyhez egy másik felhasználó előfizetést társított.

## <a name="delete-the-directory"></a>A könyvtár törlése

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a szervezet globális rendszergazdája.

2. Válassza a **Azure Active Directory**lehetőséget.

3. Váltson arra a könyvtárra, amelyet törölni szeretne.
  
   ![A szervezet megerősítése a törlés előtt](./media/directory-delete-howto/delete-directory-command.png)

4. Válassza a **könyvtár törlése**lehetőséget.
  
   ![Válassza ki a szervezet törlésére szolgáló parancsot](./media/directory-delete-howto/delete-directory-list.png)

5. Ha a címtár nem ad át egy vagy több ellenőrzést, a rendszer egy hivatkozást tartalmaz, amely további információkat tartalmaz a továbbításáról. Az összes ellenőrzés átadása után válassza a **Törlés** lehetőséget a folyamat befejezéséhez.

## <a name="if-you-cant-delete-the-directory"></a>Ha nem tudja törölni a könyvtárat

Ha az Azure AD-címtárat konfigurálta, akkor lehet, hogy aktiválta a licenc-alapú előfizetéseket a szervezete számára, mint például a prémium szintű Azure AD P2, az Office 365 Business Premium vagy a Enterprise Mobility + Security E5. A véletlen adatvesztés elkerülése érdekében nem törölhet könyvtárat, amíg az előfizetések teljes mértékben nem törlődnek. Az előfizetéseket el kell **helyezni a címtár** törlésének engedélyezéséhez. Egy **lejárt** vagy **megszakított** előfizetés a **letiltott** állapotba kerül, és a végső szakasz a **kiépített** állapot.

A következő táblázat ismerteti, hogy mire számíthat, ha a próbaverziós Office 365-előfizetés lejár (nem tartalmazza a fizetős partner/CSP, Nagyvállalati Szerződés vagy mennyiségi licencelés). Az Office 365 adatmegőrzési és-előfizetési életciklusával kapcsolatos további információkért lásd: [Mi történik az adatok és a hozzáférés, amikor az office 365 for Business-előfizetés lejár?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Előfizetés állapota | Adatok | Hozzáférés az adatkezeléshez
----- | ----- | -----
Aktív (30 nap próbaverzió esetén) | Az összes elérhető | A felhasználók az Office 365-fájlokhoz vagy-alkalmazásokhoz normál hozzáféréssel rendelkeznek<br>A rendszergazdáknak normális hozzáférésük van Microsoft 365 felügyeleti központhoz és erőforrásokhoz 
Lejárt (30 nap) | Az összes elérhető| A felhasználók az Office 365-fájlokhoz vagy-alkalmazásokhoz normál hozzáféréssel rendelkeznek<br>A rendszergazdáknak normális hozzáférésük van Microsoft 365 felügyeleti központhoz és erőforrásokhoz
Letiltva (30 nap) | Csak a rendszergazda számára elérhető adathozzáférés | A felhasználók nem férhetnek hozzá az Office 365-fájlokhoz vagy-alkalmazásokhoz<br>A rendszergazdák hozzáférhetnek a Microsoft 365 felügyeleti központhoz, de nem rendelhetnek hozzá licenceket vagy frissíthetnek felhasználókat
Kiépítve (30 nappal a letiltottak után) | Az adattörlés (automatikusan törlődik, ha nincs más szolgáltatás használatban) | A felhasználók nem férhetnek hozzá az Office 365-fájlokhoz vagy-alkalmazásokhoz<br>A rendszergazdák hozzáférhetnek a Microsoft 365 felügyeleti központhoz más előfizetések megvásárlásához és kezeléséhez

## <a name="delete-a-subscription"></a>Előfizetés törlése

A **kiépített** állapotba egy előfizetést a Microsoft 365 felügyeleti központot használva három napon belül törölhet.

1. Jelentkezzen be a [Microsoft 365 felügyeleti központba](https://admin.microsoft.com) egy olyan fiókkal, amely a szervezet globális rendszergazdája. Ha törölni szeretné a "contoso" könyvtárat, amely a kezdeti alapértelmezett tartományi contoso.onmicrosoft.com rendelkezik, jelentkezzen be egy egyszerű felhasználónévvel, például admin@contoso.onmicrosoft.com:.

2. Tekintse meg az új Microsoft 365 felügyeleti központ előzetes verzióját, és győződjön meg arról, hogy az **új felügyeleti központ** váltása engedélyezve van.

   ![Tekintse meg az új M365 felügyeleti központ felületét](./media/directory-delete-howto/preview-toggle.png)

3. Az új felügyeleti központ engedélyezése után le kell mondania egy előfizetést, mielőtt törölni lehetne. Válassza a **számlázás** lehetőséget, válassza a **termékek & szolgáltatások**elemet, majd válassza az **előfizetés megszakítása** lehetőséget a megszakítani kívánt előfizetéshez. Egy visszajelzési oldalra kerül.

   ![Válassza ki a törölni kívánt előfizetést](./media/directory-delete-howto/cancel-choose-subscription.png)

4. Fejezze be a visszajelzési űrlapot, és válassza az **előfizetés megszüntetése** lehetőséget az előfizetés megszakításához.

   ![Megszakított parancs az előfizetés előnézetében](./media/directory-delete-howto/cancel-command.png)

5. Most már törölheti is az előfizetést. Válassza **a Törlés lehetőséget** a törölni kívánt előfizetéshez. Ha nem találja az előfizetést a **termékek & szolgáltatások** lapon, győződjön meg arról, hogy az **előfizetés állapota** **mind**értékre van állítva.

   ![Előfizetés törlésére szolgáló hivatkozás törlése](./media/directory-delete-howto/delete-command.png)

6. Válassza az **előfizetés törlése** lehetőséget az előfizetés törléséhez és a használati feltételek elfogadásához. Az összes adatfeldolgozás három napon belül véglegesen törölve lesz. Ha meggondolja magát, újra [aktiválhatja az előfizetést](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide) a három napos időszakban.
  
   ![a feltételek és kikötések körültekintő beolvasása](./media/directory-delete-howto/delete-terms.png)

7. Az előfizetés állapota most módosult, és az előfizetés törlésre van megjelölve. Az előfizetés a **kiépített** állapotot 72 órával később írja be.

8. Miután törölte az előfizetést a címtárban, és 72 óra telt el, újra bejelentkezhet az Azure AD felügyeleti központba, és nem lehet szükséges művelet, és nincs olyan előfizetés, amely blokkolja a címtár törlését. Az Azure AD-címtárat sikeresen törölni kell.
  
   ![előfizetés továbbításának engedélyezése a törlési képernyőn](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Olyan próbaverziós előfizetésem van, amely blokkolja a törlést

Az [önkiszolgáló regisztrációs termékek](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) , például a Microsoft Power bi, a Rights Management Services, a Microsoft Power apps vagy a Dynamics 365, az egyes felhasználók regisztrálhatnak az Office 365-on keresztül, amely az Azure ad-címtárban is létrehoz egy vendég felhasználót a hitelesítéshez. Ezek az önkiszolgáló termékek az adatvesztés elkerülése érdekében letiltják a címtár törlését, amíg azok nem törlődnek teljesen a címtárból. Ezeket csak az Azure AD-rendszergazda törölheti, függetlenül attól, hogy a felhasználó regisztrálta-e a terméket.

Az önkiszolgáló regisztrációs termékek két típusa van a hozzárendelésük módjában: 

* Szervezeti szintű hozzárendelés: az Azure AD-rendszergazda hozzárendeli a terméket a teljes szervezethez, és a felhasználó aktívan használhatja a szolgáltatást ezzel a szervezeti szintű hozzárendeléssel, még akkor is, ha nem rendelkezik külön licenccel.
* Felhasználói szintű hozzárendelés: az önkiszolgáló regisztráció során egy egyéni felhasználó lényegében a terméket rendszergazda nélkül rendeli hozzá önmagához. Ha a szervezetet egy rendszergazda felügyeli (lásd: nem [felügyelt címtár rendszergazdai átvétele](domains-admin-takeover.md), akkor a rendszergazda közvetlenül is hozzárendelheti a terméket a felhasználókhoz önkiszolgáló regisztráció nélkül.  

Az önkiszolgáló regisztrációs termék törlésének megkezdése után a művelet véglegesen törli az információkat, és eltávolítja az összes felhasználói hozzáférést a szolgáltatáshoz. Minden olyan felhasználó, aki egyénileg vagy a szervezet szintjén lett hozzárendelve, a rendszer letiltja a bejelentkezést vagy a meglévő adatok elérését. Ha szeretné megakadályozni az adatvesztést az önkiszolgáló regisztrációs termékkel, például a [Microsoft Power bi irányítópultokkal](https://docs.microsoft.com/power-bi/service-export-to-pbix) vagy [Rights Management Services házirend-konfigurációval](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy), győződjön meg arról, hogy az adatbiztonsági mentés és a Mentés máshol megtörtént.

A jelenleg elérhető önkiszolgáló regisztrációs termékekkel és szolgáltatásokkal kapcsolatos további információkért lásd a [rendelkezésre álló](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs)önkiszolgáló programok című témakört.

A következő táblázat ismerteti, hogy mire számíthat, ha a próbaverziós Office 365-előfizetés lejár (nem tartalmazza a fizetős partner/CSP, Nagyvállalati Szerződés vagy mennyiségi licencelés). Az Office 365 adatmegőrzési és-előfizetési életciklusával kapcsolatos további információkért lásd: [Mi történik az adatok és a hozzáférés, amikor az office 365 for Business-előfizetés lejár?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide).

Termék állapota | Adatok | Hozzáférés az adatkezeléshez
------------- | ---- | --------------
Aktív (30 nap próbaverzió esetén) | Az összes elérhető | A felhasználók normál hozzáféréssel rendelkeznek az önkiszolgáló regisztrációs termékekhez, fájlokhoz vagy alkalmazásokhoz<br>A rendszergazdáknak normális hozzáférésük van Microsoft 365 felügyeleti központhoz és erőforrásokhoz
Törölve | Az adattörlés | A felhasználók nem férhetnek hozzá az önkiszolgáló regisztrációs termékekhez, fájlokhoz és alkalmazásokhoz<br>A rendszergazdák hozzáférhetnek a Microsoft 365 felügyeleti központhoz más előfizetések megvásárlásához és kezeléséhez

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Hogyan törölhetek önkiszolgáló regisztrációs terméket a Azure Portal?

Az Azure AD-portálon azonnal törölheti az önkiszolgáló regisztrációs terméket, például a Microsoft Power BI vagy **az azure** Rights Management Services szolgáltatást.

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) egy olyan fiókkal, amely a szervezet globális rendszergazdája. Ha törölni szeretné a "contoso" könyvtárat, amely a kezdeti alapértelmezett tartományi contoso.onmicrosoft.com rendelkezik, jelentkezzen be egy egyszerű felhasználónévvel, például admin@contoso.onmicrosoft.com:.

2. Válassza a **licencek**lehetőséget, majd válassza az **önkiszolgáló regisztrációs termékek**lehetőséget. Az összes önkiszolgáló regisztrációs terméket külön tekintheti meg az ülésen alapuló előfizetések között. Válassza ki a véglegesen törölni kívánt terméket. Íme egy példa a Microsoft Power BIban:

    ![a Felhasználónév típusa helytelen, vagy nem található](./media/directory-delete-howto/licenses-page.png)

3. Válassza a **Törlés** lehetőséget a termék törléséhez, és fogadja el azokat a feltételeket, amelyek azonnal és visszavonhatatlanul törlődnek. Ez a törlési művelet törli az összes felhasználót, és eltávolítja a szervezet hozzáférését a termékhez. Az Igen gombra kattintva lépjen tovább a törlésre.  

    ![a Felhasználónév típusa helytelen, vagy nem található](./media/directory-delete-howto/delete-product.png)

4. Ha az **Igen**lehetőséget választja, a rendszer kezdeményezi az önkiszolgáló termék törlését. Értesítés jelenik meg arról, hogy a törlés folyamatban van.  

    ![a Felhasználónév típusa helytelen, vagy nem található](./media/directory-delete-howto/progress-message.png)

5. Az önkiszolgáló bejelentkezési termék állapota most már **törölve**lett. Az oldal frissítésekor a terméket el kell távolítani az **önkiszolgáló regisztrációs termékek** oldaláról.  

    ![a Felhasználónév típusa helytelen, vagy nem található](./media/directory-delete-howto/product-deleted.png)

6. Miután törölte az összes terméket, újra bejelentkezhet az Azure AD felügyeleti központba, és nem lehet szükséges művelet, és nincs olyan termék, amely blokkolja a címtár törlését. Az Azure AD-címtárat sikeresen törölni kell.

    ![a Felhasználónév típusa helytelen, vagy nem található](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>További lépések

[Az Azure Active Directory dokumentációja](https://docs.microsoft.com/azure/active-directory/)
