---
title: Az Azure Active Directory-bérlő címtárát törlése |} A Microsoft Docs
description: Azt ismerteti, hogyan készíti elő az Azure AD-bérlő címtár törlésre
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/07/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 30e5a147e1cde80c1ab80b1080aad4317a2de968
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197721"
---
# <a name="delete-an-azure-active-directory-tenant"></a>Az Azure Active Directory-bérlő törlése

A bérlő törlése esetén a bérlőn található összes erőforrás is törlődik. Elő kell készítenie a bérlő törlése előtt az összes kapcsolódó erőforrás termékeikkel. Csak egy Azure Active Directory (Azure AD) globális rendszergazda törölheti az Azure AD-bérlő a portálról.

## <a name="prepare-the-tenant-for-deletion"></a>A bérlő előkészítése törléshez

Egy bérlő az Azure AD, amíg nem adja át, több ellenőrzést nem törölhető. Az ellenőrzések csökkentse annak veszélyét, hogy egy bérlő törlése kedvezőtlen hatással van a bejelentkezni az Office 365 vagy a hozzáférés az Azure-erőforrások, például a felhasználói hozzáférés. Például ha egy előfizetést társított tenant véletlenül törölnek, majd felhasználók nem férhetnek hozzá az adott előfizetéshez tartozó Azure-erőforrások. A következő ismerteti azokat a feltételeket, amelyeket a rendszer ellenőrzi a:

* Kivéve egy globális rendszergazda, aki a bérlőt a bérlő egyik felhasználó sem lehet. A bérlő törlése előtt az összes többi felhasználót törölni kell. Ha a helyszíni felhasználók szinkronizálása, majd szinkronizálást ki kell kapcsolni, és a felhasználók törölni kell a felhőalapú bérlőre, az Azure portal vagy Azure PowerShell-parancsmagok használatával. 
* A bérlő nincs alkalmazás lehet. Az alkalmazásokat a bérlő törlése előtt el kell távolítani.
* Nem a multi-factor authentication-szolgáltatók a bérlőhöz kapcsolva lehet.
* Lehetnek például a Microsoft Azure, Office 365, a Microsoft Online Services-előfizetést, vagy az tenanthoz társított Azure AD Premium. Például ha egy alapértelmezett bérlőt az Azure-ban az Ön számára készült, nem törölhető ezt a bérlőt, ha az Azure-előfizetés továbbra is ezt a bérlőt a hitelesítéshez támaszkodik. Hasonlóképpen egy másik felhasználó társított egy előfizetést, ha egy bérlő nem törölhető. 

## <a name="delete-an-azure-ad-tenant"></a>Az Azure AD-bérlő törlése

1. Jelentkezzen be a [Azure AD felügyeleti központ](https://aad.portal.azure.com) egy olyan fiókkal, amely a globális rendszergazdai a bérlőhöz.

2. Válassza az **Azure Active Directory** elemet.

3. Kapcsoló törölni kívánt bérlőhöz.
  
  ![gomb könyvtár törlése](./media/directory-delete-howto/delete-directory-command.png)

4. Válassza ki **Delete directory**.
  
  ![gomb könyvtár törlése](./media/directory-delete-howto/delete-directory-list.png)

5. Ha a bérlő nem felel meg egy vagy több ellenőrzés, hogyan adhatók át a további információkra mutató hivatkozást tartalmazó már megadott. Után át kell adnia az összes ellenőrzés, jelölje be az **törlése** a folyamat befejezéséhez.

## <a name="i-have-an-expired-subscription-but-i-cant-delete-the-tenant"></a>Lejárt az előfizetésem van, de nem lehet törölni a bérlő

Ha konfigurálta az Azure Active Directory-bérlővel, előfordulhat, hogy is aktiválta előfizetések licenc-alapú Azure Active Directory Premium P2, Office 365 vállalati prémium vagy Enterprise Mobility + Security E5 hasonlóan a szervezet számára. Ezeket az előfizetéseket könyvtár törlése letiltása, amíg a teljes törlés, véletlen adatvesztést. Az előfizetések kell lennie egy **megszüntetett** lehetővé teszi, hogy a bérlő törlése. Egy **lejárt** vagy **megszakított** előfizetés áthelyezi a **letiltott** állapotát, és az utolsó fáziséit a **Deprovisoned** állapota. 

Mire számítson, ha az Office 365 próba-előfizetés lejárt (nem fizetős Partner/CSP, nagyvállalati szerződéssel vagy mennyiségi licencelési beleértve) tekintse meg az alábbi táblázat. Az Office 365 adatok megőrzésére és az előfizetés életciklusának további információkért lásd: [mi történik az adatokkal és a hozzáférés, ha az Office 365 üzleti előfizetési befejeződik?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

Előfizetés állapota | Adatok | Adatokhoz való hozzáférés
----- | ----- | -----
Aktív (próbaverzió 30 nap)  | Az összes elérhető adat    | <li>Felhasználók normális férhetnek hozzá az Office 365-fájlok vagy alkalmazások<li>Rendszergazdák rendelkeznek Office 365 felügyeleti központ és az erőforrásokhoz való hozzáférési 
Lejárt (30 nap)   | Az összes elérhető adat    | <li>Felhasználók normális férhetnek hozzá az Office 365-fájlok vagy alkalmazások<li>Rendszergazdák rendelkeznek Office 365 felügyeleti központ és az erőforrásokhoz való hozzáférési
Le van tiltva (30 nap) | Csak rendszergazda számára elérhető adatok  | <li>Felhasználók nem férhetnek hozzá, az Office 365-fájlok vagy alkalmazások<li>A rendszergazdák az Office 365 felügyeleti központ eléréséhez, de nem szükséges licencek kiosztása vagy felhasználók frissítése
Felfüggesztést (30 nap után le van tiltva) | Törölt adatok (Ha nincs más szolgáltatások is automatikusan törölve) | <li>Felhasználók nem férhetnek hozzá, az Office 365-fájlok vagy alkalmazások<li>A rendszergazdák hozzáférhetnek az Office 365 felügyeleti központban vásárolhat és más előfizetések kezelése

Az előfizetés helyezheti egy **Deprovisoned** állapot használatával a Microsoft Store for Business felügyeleti központban 3 nap múlva törlődnek. Ez a funkció hamarosan elérhető lesz az Office 365 felügyeleti központban.

1. Jelentkezzen be a [Microsoft Store for Business felügyeleti központban](https://businessstore.microsoft.com/manage/) egy olyan fiókkal, amely a bérlő globális rendszergazdája lesz. Ha a "Contoso" bérlőt, amelyhez a contoso.onmicrosoft.com-ot a kezdeti alapértelmezett tartomány törölni próbál, jelentkezzen be egy egyszerű felhasználónév például admin@contoso.onmicrosoft.com.

2. Nyissa meg a **kezelés** lapra, és válassza **termékek és szolgáltatások**, majd válassza ki az előfizetést, szakítsa meg és válassza ki a kívánt **törlése**.
  
  ![Előfizetés törlése a hivatkozás törlése](./media/directory-delete-howto/delete-command.png)
  
3. Válassza ki **előfizetés törlése** fogadja el a feltételeket és kikötéseket, és törölni az előfizetést. Összes adat véglegesen törlődik a három napon belül. Ha meggondolja magát az előfizetés a következő három napban újraaktiválhatja.
  
  ![feltételek és kikötések](./media/directory-delete-howto/delete-terms.png)

4. Most az előfizetés állapota megváltozott, az előfizetés törlésre van kijelölve. Az előfizetés beírja a **megszüntetett** állapot 72 óra múlva.

5. Után a bérlő, a törölt egy előfizetést, és 72 óra telt, jelentkezhet be az Azure AD felügyeleti központ biztonsági újra, és ott kell lennie nincs szükség művelet, és nem blokkolja-e a bérlő törlése előfizetések. Sikerült törölni az Azure AD-bérlő tudni kell lennie.
  
  ![át, törlés képernyő előfizetés ellenőrzése](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="next-steps"></a>További lépések
[Az Azure Active Directory-dokumentáció](https://docs.microsoft.com/azure/active-directory/)
