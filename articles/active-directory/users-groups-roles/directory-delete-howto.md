---
title: Azure AD-szervezet (bérlő) törlése – Azure Active Directory | Microsoft Docs
description: Ismerteti, hogyan lehet előkészíteni egy Azure AD-szervezetet (bérlőt) törlésre, beleértve az önkiszolgáló szervezeteket
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 05/21/2020
ms.author: curtand
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d21e0358e2226f93a6c7088906d1146e5e2cf740
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84729030"
---
# <a name="delete-a-tenant-in-azure-active-directory"></a>Bérlő törlése Azure Active Directory

Ha egy Azure AD-szervezetet (bérlőt) töröl, a szervezetben található összes erőforrás is törlődik. Készítse elő a szervezetét, hogy a törlés előtt minimalizálja a hozzájuk tartozó erőforrásokat. Csak egy Azure Active Directory (Azure AD) globális rendszergazda törölhet egy Azure AD-szervezetet a portálról.

## <a name="prepare-the-organization"></a>A szervezet előkészítése

Nem törölhet szervezetet az Azure AD-ben, amíg át nem halad több ellenőrzés. Ezek az ellenőrzések csökkentik annak a kockázatát, hogy az Azure AD-szervezet törlése negatív hatással van a felhasználói hozzáférésre, például az Office 365-be való bejelentkezésre vagy az Azure-beli erőforrásokhoz való hozzáférésre. Ha például az előfizetéshez társított szervezet véletlenül törölve van, akkor a felhasználók nem férhetnek hozzá az adott előfizetéshez tartozó Azure-erőforrásokhoz. A rendszer a következő feltételek teljesülését ellenőrzi:

* Az Azure AD-szervezetben (bérlőben) nem lehetnek felhasználók, kivéve az egyik globális rendszergazda, aki a szervezetet törölni szeretné. A szervezet törlése előtt minden más felhasználót törölni kell. Ha a felhasználók szinkronizálása a helyszíni rendszerből történik, akkor először ki kell kapcsolni a szinkronizálást, és a felhasználókat a Azure Portal vagy Azure PowerShell parancsmagokkal kell törölni a felhőalapú szervezetekben.
* A szervezetben nem szerepelhetnek alkalmazások. A szervezet törlése előtt minden alkalmazást el kell távolítani.
* A szervezethez társított multi-Factor Authentication szolgáltatók közül nem lehet.
* Semmilyen Microsoft Online Services-szolgáltatáshoz (például Microsoft Azure, Office 365 vagy a szervezethez társított prémium szintű Azure AD) nem lehet előfizetést fizetni. Ha például egy alapértelmezett Azure AD-szervezet lett létrehozva az Azure-ban, akkor nem törölheti ezt a szervezetet, ha az Azure-előfizetése továbbra is ezen a szervezeten alapul a hitelesítéshez. Hasonlóképpen nem törölheti a szervezetet, ha egy másik felhasználó társított egy előfizetést.

## <a name="delete-the-organization"></a>A szervezet törlése

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a szervezet globális rendszergazdája.

2. Válassza a **Azure Active Directory**lehetőséget.

3. Váltson a törölni kívánt szervezetre.
  
   ![A szervezet megerősítése a törlés előtt](./media/directory-delete-howto/delete-directory-command.png)

4. Válassza a **bérlő törlése**lehetőséget.
  
   ![Válassza ki a szervezet törlésére szolgáló parancsot](./media/directory-delete-howto/delete-directory-list.png)

5. Ha a szervezete nem ad át egy vagy több ellenőrzést, a rendszer egy hivatkozást tartalmaz, amely további információkat tartalmaz a továbbításáról. Az összes ellenőrzés átadása után válassza a **Törlés** lehetőséget a folyamat befejezéséhez.

## <a name="if-you-cant-delete-the-organization"></a>Ha nem tudja törölni a szervezetet

Az Azure AD-szervezet konfigurálását követően aktiválhatja az olyan licenc-alapú előfizetéseket is, mint a prémium szintű Azure AD P2, az Office 365 Business Premium vagy a Enterprise Mobility + Security E5. A véletlen adatvesztés elkerülése érdekében nem törölhet szervezeteket, amíg az előfizetések teljes mértékben nem törlődnek. Az előfizetéseket el kell **helyezni a szervezet** törlésének engedélyezéséhez. Egy **lejárt** vagy **megszakított** előfizetés a **letiltott** állapotba kerül, és a végső szakasz a **kiépített** állapot.

A következő táblázat ismerteti, hogy mire számíthat, ha a próbaverziós Office 365-előfizetés lejár (nem tartalmazza a fizetős partner/CSP, Nagyvállalati Szerződés vagy mennyiségi licencelés). Az Office 365 adatmegőrzési és-előfizetési életciklusával kapcsolatos további információkért lásd: [Mi történik az adatok és a hozzáférés, amikor az office 365 for Business-előfizetés lejár?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Előfizetés állapota | Adatok | Hozzáférés az adatkezeléshez
----- | ----- | -----
Aktív (30 nap próbaverzió esetén) | Az összes elérhető | A felhasználók az Office 365-fájlokhoz vagy-alkalmazásokhoz normál hozzáféréssel rendelkeznek<br>A rendszergazdáknak normális hozzáférésük van Microsoft 365 felügyeleti központhoz és erőforrásokhoz 
Lejárt (30 nap) | Az összes elérhető| A felhasználók az Office 365-fájlokhoz vagy-alkalmazásokhoz normál hozzáféréssel rendelkeznek<br>A rendszergazdáknak normális hozzáférésük van Microsoft 365 felügyeleti központhoz és erőforrásokhoz
Letiltva (30 nap) | Csak a rendszergazda számára elérhető adathozzáférés | A felhasználók nem férhetnek hozzá az Office 365-fájlokhoz vagy-alkalmazásokhoz<br>A rendszergazdák hozzáférhetnek a Microsoft 365 felügyeleti központhoz, de nem rendelhetnek hozzá licenceket vagy frissíthetnek felhasználókat
Kiépítve (30 nappal a letiltottak után) | Az adattörlés (automatikusan törlődik, ha nincs más szolgáltatás használatban) | A felhasználók nem férhetnek hozzá az Office 365-fájlokhoz vagy-alkalmazásokhoz<br>A rendszergazdák hozzáférhetnek a Microsoft 365 felügyeleti központhoz más előfizetések megvásárlásához és kezeléséhez

## <a name="delete-a-subscription"></a>Előfizetés törlése

A **kiépített** állapotba egy előfizetést a Microsoft 365 felügyeleti központot használva három napon belül törölhet.

1. Jelentkezzen be a [Microsoft 365 felügyeleti központba](https://admin.microsoft.com) egy olyan fiókkal, amely a szervezet globális rendszergazdája. Ha törölni szeretné a "contoso" nevű szervezetet, amely a kezdeti alapértelmezett tartományi contoso.onmicrosoft.com rendelkezik, jelentkezzen be egy egyszerű felhasználónévvel, például: admin@contoso.onmicrosoft.com .

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

8. Miután törölte az előfizetést a munkahelyén, és 72 óra telt el, újra bejelentkezhet az Azure AD felügyeleti központba, és nem feltétlenül szükséges művelet, és nincs olyan előfizetés, amely blokkolja a szervezet törlését. Az Azure AD-szervezetet sikeresen törölni kell.
  
   ![előfizetés továbbításának engedélyezése a törlési képernyőn](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>Olyan próbaverziós előfizetésem van, amely blokkolja a törlést

Az [önkiszolgáló regisztrációs termékek](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) , például a Microsoft Power bi, a Rights Management szolgáltatások, a Microsoft Power apps vagy a Dynamics 365, az egyes felhasználók regisztrálhatnak az Office 365-on keresztül, ami szintén létrehoz egy vendég felhasználót az Azure ad-szervezetben való hitelesítéshez. Ezek az önkiszolgáló termékek letiltják a címtár törlését, amíg a termékek teljes mértékben törlődnek a szervezetből, így elkerülhető az adatvesztés. Ezeket csak az Azure AD-rendszergazda törölheti, függetlenül attól, hogy a felhasználó regisztrálta-e a terméket.

Az önkiszolgáló regisztrációs termékek két típusa van a hozzárendelésük módjában: 

* Szervezeti szintű hozzárendelés: az Azure AD-rendszergazda hozzárendeli a terméket a teljes szervezethez, és a felhasználó aktívan használhatja a szolgáltatást ezzel a szervezeti szintű hozzárendeléssel, még akkor is, ha nem rendelkezik külön licenccel.
* Felhasználói szintű hozzárendelés: az önkiszolgáló regisztráció során egy egyéni felhasználó lényegében a terméket rendszergazda nélkül rendeli hozzá önmagához. Ha a szervezetet egy rendszergazda felügyeli (lásd: nem [felügyelt szervezet rendszergazdai átvétele](domains-admin-takeover.md), a rendszergazda közvetlenül is hozzárendelheti a terméket a felhasználókhoz önkiszolgáló regisztráció nélkül.  

Az önkiszolgáló regisztrációs termék törlésének megkezdése után a művelet véglegesen törli az információkat, és eltávolítja az összes felhasználói hozzáférést a szolgáltatáshoz. Minden olyan felhasználó, aki egyénileg vagy a szervezet szintjén lett hozzárendelve, a rendszer letiltja a bejelentkezést vagy a meglévő adatok elérését. Ha szeretné megakadályozni az adatvesztést az önkiszolgáló regisztrációs termékkel, például a [Microsoft Power bi irányítópultokkal](https://docs.microsoft.com/power-bi/service-export-to-pbix) vagy [Rights Management Services házirend-konfigurációval](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy), győződjön meg arról, hogy az adatbiztonsági mentés és a Mentés máshol megtörtént.

A jelenleg elérhető önkiszolgáló regisztrációs termékekkel és szolgáltatásokkal kapcsolatos további információkért lásd a [rendelkezésre álló](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs)önkiszolgáló programok című témakört.

A következő táblázat ismerteti, hogy mire számíthat, ha a próbaverziós Office 365-előfizetés lejár (nem tartalmazza a fizetős partner/CSP, Nagyvállalati Szerződés vagy mennyiségi licencelés). Az Office 365 adatmegőrzési és-előfizetési életciklusával kapcsolatos további információkért lásd: [Mi történik az adatok és a hozzáférés, amikor az office 365 for Business-előfizetés lejár?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide).

Termék állapota | Adatok | Hozzáférés az adatkezeléshez
------------- | ---- | --------------
Aktív (30 nap próbaverzió esetén) | Az összes elérhető | A felhasználók normál hozzáféréssel rendelkeznek az önkiszolgáló regisztrációs termékekhez, fájlokhoz vagy alkalmazásokhoz<br>A rendszergazdáknak normális hozzáférésük van Microsoft 365 felügyeleti központhoz és erőforrásokhoz
Törölve | Az adattörlés | A felhasználók nem férhetnek hozzá az önkiszolgáló regisztrációs termékekhez, fájlokhoz és alkalmazásokhoz<br>A rendszergazdák hozzáférhetnek a Microsoft 365 felügyeleti központhoz más előfizetések megvásárlásához és kezeléséhez

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Hogyan törölhetek önkiszolgáló regisztrációs terméket a Azure Portal?

Az Azure AD-portálon azonnal törölheti az önkiszolgáló regisztrációs terméket, például a Microsoft Power BI vagy **az azure** Rights Management Services szolgáltatást.

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) egy olyan fiókkal, amely a szervezet globális rendszergazdája. Ha törölni szeretné a "contoso" nevű szervezetet, amely a kezdeti alapértelmezett tartományi contoso.onmicrosoft.com rendelkezik, jelentkezzen be egy egyszerű felhasználónévvel, például: admin@contoso.onmicrosoft.com .

2. Válassza a **licencek**lehetőséget, majd válassza az **önkiszolgáló regisztrációs termékek**lehetőséget. Az összes önkiszolgáló regisztrációs terméket külön tekintheti meg az ülésen alapuló előfizetések között. Válassza ki a véglegesen törölni kívánt terméket. Íme egy példa a Microsoft Power BIban:

    ![a Felhasználónév típusa helytelen, vagy nem található](./media/directory-delete-howto/licenses-page.png)

3. Válassza a **Törlés** lehetőséget a termék törléséhez, és fogadja el azokat a feltételeket, amelyek azonnal és visszavonhatatlanul törlődnek. Ez a törlési művelet törli az összes felhasználót, és eltávolítja a szervezet hozzáférését a termékhez. Az Igen gombra kattintva lépjen tovább a törlésre.  

    ![a Felhasználónév típusa helytelen, vagy nem található](./media/directory-delete-howto/delete-product.png)

4. Ha az **Igen**lehetőséget választja, a rendszer kezdeményezi az önkiszolgáló termék törlését. Értesítés jelenik meg arról, hogy a törlés folyamatban van.  

    ![a Felhasználónév típusa helytelen, vagy nem található](./media/directory-delete-howto/progress-message.png)

5. Az önkiszolgáló bejelentkezési termék állapota most már **törölve**lett. Az oldal frissítésekor a terméket el kell távolítani az **önkiszolgáló regisztrációs termékek** oldaláról.  

    ![a Felhasználónév típusa helytelen, vagy nem található](./media/directory-delete-howto/product-deleted.png)

6. Miután törölte az összes terméket, újra bejelentkezhet az Azure AD felügyeleti központba, és nem feltétlenül szükséges művelet, és nem blokkolja a szervezet törlését. Az Azure AD-szervezetet sikeresen törölni kell.

    ![a Felhasználónév típusa helytelen, vagy nem található](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>További lépések

[Azure Active Directory-dokumentáció](https://docs.microsoft.com/azure/active-directory/)
