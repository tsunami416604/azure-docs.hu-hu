---
title: Munkaterületek létrehozása a saját alkalmazások portálok számára Azure Active Directoryban | Microsoft Docs
description: Az alkalmazás-munkaterületek segítségével testre szabhatja az alkalmazások lapjait a végfelhasználók számára az alkalmazások egyszerűbb felhasználói élménye érdekében. Az alkalmazások csoportokba rendezhetők külön lapokkal.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a986c2032fc41bbd34230c5b0ead745f88b000f
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199827"
---
# <a name="create-workspaces-on-the-my-apps-preview-portal"></a>Munkaterületek létrehozása a saját alkalmazások (előzetes verzió) portálon

A felhasználók a My apps (előzetes verzió) portálon megtekinthetik és elindíthatják azokat a felhőalapú alkalmazásokat, amelyekhez hozzáféréssel rendelkeznek. Alapértelmezés szerint az összes felhasználó számára elérhető alkalmazás egyetlen oldalon van felsorolva. Ha prémium szintű Azure AD P1 vagy P2 licenccel rendelkezik, hogy jobban meg tudja szervezni ezt a lapot a felhasználók számára, beállíthat munkaterületeket. A munkaterületen csoportosíthatja a kapcsolódó alkalmazásokat (például feladat-szerepkör, feladat vagy projekt), és külön lapon jelenítheti meg őket. A munkaterületek alapvetően egy szűrőt alkalmaznak azokra az alkalmazásokra, amelyeket a felhasználó már elérhet, így a felhasználó csak azokat az alkalmazásokat látja, amelyek hozzá vannak rendelve a munkaterülethez.

> [!NOTE]
> Ez a cikk azt ismerteti, hogyan engedélyezheti és hozhat létre munkaterületeket a rendszergazda. További információ a saját alkalmazások portál és munkaterületek használatáról: a [munkaterületek elérése és használata](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces).

## <a name="enable-my-apps-preview-features"></a>Saját alkalmazások előnézeti funkcióinak engedélyezése

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be felhasználói rendszergazdaként vagy globális rendszergazdaként.

2. Lépjen **Azure Active Directory** > **felhasználói beállítások menüpontra**.

3. A **felhasználói**szolgáltatások előzetesei területen válassza a **felhasználói szolgáltatás előnézeti beállításainak kezelése**lehetőséget.

4. A **felhasználók a saját alkalmazások előnézeti funkcióit használhatják**a következő lehetőségek közül:
   * **Kiválasztott** – egy adott csoport előnézeti funkcióinak engedélyezése. A **csoport kiválasztása** lehetőség kiválasztásával válassza ki azt a csoportot, amelyhez engedélyezni kívánja az előzetes verziójú funkciókat.  
   * **All** – az összes felhasználó előzetes verziójú funkcióinak engedélyezése.

   ![Felhasználói előnézet funkciói](media/access-panel-workspaces/user-preview-features.png)

> [!NOTE]
> A saját alkalmazások portál megnyitásához a felhasználók használhatják a `https://myapps.microsoft.com` vagy a testre szabott hivatkozást a szervezethez, például `https://myapps.microsoft.com/contoso.com`. Ha a felhasználók nem lettek átirányítva a saját alkalmazások előzetes verziójára, a felhasználóknak meg kell próbálnia `https://myapplications.microsoft.com` vagy `https://myapplications.microsoft.com/contoso.com`.

## <a name="create-a-workspace"></a>Munkaterületek létrehozása

Munkaterület létrehozásához prémium szintű Azure AD P1 vagy P2 licenccel kell rendelkeznie.

1. Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be rendszergazdaként egy prémium szintű Azure ad P1 vagy P2 licenccel.

2. Nyissa meg **Azure Active Directory** > **vállalati alkalmazások**lehetőséget.

3. A **kezelés**területen válassza a **munkaterületek (előzetes verzió)** lehetőséget.

4. Válassza az **Új munkaterület**lehetőséget. Az **Új munkaterület** lapon adja meg a munkaterület **nevét** (azt javasoljuk, hogy ne használja a "Munkaterület" kifejezést a névben. Ezután adjon meg egy **leírást**.

   ![Új munkaterület létrehozása](media/access-panel-workspaces/new-workspace.png)

5. Válassza a **felülvizsgálat + létrehozás**lehetőséget. Megjelenik az új munkaterület tulajdonságai.

6. Válassza az **alkalmazások** fület. Az **Alkalmazások hozzáadása**területen válassza ki az összes olyan alkalmazást, amelyet hozzá szeretne adni a munkaterülethez, vagy használja a **keresőmezőt** az alkalmazások kereséséhez. 

   ![Alkalmazások hozzáadása a munkaterülethez](media/access-panel-workspaces/add-applications.png)

7. Válassza a **Hozzáadás** lehetőséget. Megjelenik a kiválasztott alkalmazások listája. A fel és le nyilak használatával módosíthatja a listában szereplő alkalmazások sorrendjét.

   ![A munkaterületen lévő alkalmazások listája](media/access-panel-workspaces/add-applications-list.png)

8. Válassza a **felhasználók és csoportok** lapot. Felhasználó vagy csoport hozzáadásához válassza a **felhasználó hozzáadása**elemet. 

9. A **Tagok kiválasztása** lapon válassza ki azokat a felhasználókat vagy csoportokat, amelyekhez hozzá szeretné rendelni a munkaterületet. Vagy használja a **keresőmezőt** a felhasználók vagy csoportok kereséséhez.

   ![Felhasználók és csoportok társítása a munkaterülethez](media/access-panel-workspaces/add-users-and-groups.png)

10. Ha végzett a felhasználók és csoportok kijelölésével, válassza a **kiválasztás**lehetőséget.

11. Ha módosítani szeretné a felhasználó szerepkörét **olvasási hozzáféréssel** a **tulajdonoshoz** , vagy fordítva, kattintson az aktuális szerepkörre, és válasszon ki egy új szerepkört.

    ![Szerepkörök társítása felhasználókhoz és csoportokhoz](media/access-panel-workspaces/users-groups-list-role.png)

## <a name="view-audit-logs"></a>Auditnaplók megtekintése

A naplók rögzítik az alkalmazás-munkaterületek műveleteit, beleértve a munkaterület-létrehozás végfelhasználói műveleteit is. Az alkalmazásokból a következő események jönnek létre:

* Munkaterület létrehozása
* Munkaterület szerkesztése
* Munkaterület törlése
* Alkalmazás elindítása (végfelhasználó)
* Önkiszolgáló alkalmazás hozzáadása (végfelhasználó)
* Önkiszolgáló alkalmazás törlése (végfelhasználó)

A naplókat a [Azure Portalban](https://portal.azure.com) érheti el. ehhez válassza **Azure Active Directory** > **vállalati alkalmazások** > **naplók** lehetőséget a tevékenység szakaszban. A **szolgáltatás**területen válassza **a saját alkalmazások**lehetőséget.

   ![Szerepkörök társítása felhasználókhoz és csoportokhoz](media/access-panel-workspaces/audit-log-myapps.png)


## <a name="next-steps"></a>Következő lépések
[A Azure Active Directory alkalmazások végfelhasználói élményei](end-user-experiences.md)