---
title: Az Azure labor szolgáltatásokban labor fiókok kezelése |} Microsoft Docs
description: 'Útmutató: tesztlabor-fiók létrehozása, minden tesztkörnyezeti fiókok megtekintéséhez vagy az Azure-előfizetés labor fiók törlése.'
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 6039ea482b0968d48fc21ff3dfec82a2ff0db43d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715326"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Az Azure labor szolgáltatásokban labor fiókok kezelése 
Az Azure labor szolgáltatásban a labor számítógépfiókja osztályteremben labs például kezelt labs tárolója. A rendszergazda állít be egy tesztlabor fiókot az Azure labor Services, és hozzáférést biztosít a labor tulajdonosai, akik labs hozhat létre a fiókot. Ez a cikk ismerteti, hogyan lab-fiók létrehozása, minden tesztkörnyezeti fiókok megtekintéséhez vagy labor fiók törlése.

## <a name="create-a-lab-account"></a>Tesztkörnyezetfiók létrehozása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali főmenüben válassza az **Erőforrás létrehozása** lehetőséget.
3. Keressen a **Lab Services** szövegre az Azure Marketplace-en, és válassza a **Lab Services** alkalmazást a legördülő listából. 
4. Válassza a **Lab Services (előzetes verzió)** lehetőséget a szűrt szolgáltatáslistában. 
5. A **Tesztkörnyezetfiók létrehozása** ablakban válassza a **Létrehozás** elemet.
7. A **Tesztkörnyezetfiók** ablakban tegye a következőket: 
    1. Írjon be egy nevet a **Tesztkörnyezetfiók neve** mezőbe. 
    2. Válassza ki azt az **Azure-előfizetést**, amelyben a tesztkörnyezetfiókot létre szeretné hozni.
    3. Az **Erőforráscsoport** esetében válassza az **Új létrehozása** lehetőséget, majd adjon nevet az új erőforráscsoportnak.
    4. A **Helyszín** esetében válassza ki a helyszínt/régiót, ahol létre szeretné hozni a tesztkörnyezetfiókot. 
    5. Kattintson a **Létrehozás** gombra. 

        ![Tesztkörnyezetfiók létrehozása ablak](../media/how-to-manage-lab-accounts/lab-account-settings.png)
5. Ha a tesztkörnyezetfiók oldala nem látható, kattintson az **Értesítések** gombra, majd az **Ugrás ide: erőforrás** gombra az értesítésekben. 

    ![Tesztkörnyezetfiók létrehozása ablak](../media/how-to-manage-lab-accounts/notification-go-to-resource.png)    
6. Megjelenik az alábbi **tesztkörnyezetfiók** oldala:

    ![Tesztkörnyezetfiók-oldal](../media/how-to-manage-lab-accounts/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Felhasználó hozzáadása a Tesztkörnyezet-létrehozó szerepkörhöz
Ha engedélyt kíván ad az oktatóknak, hogy létrehozzák a tesztkörnyezeteket a tanóráikhoz, adja hozzá őket a Tesztkörnyezet-létrehozó szerepkörhöz:

1. A **Tesztkörnyezetfiók** oldalon válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget, majd kattintson az eszköztár **+ Hozzáadás** elemére. 

    ![Tesztkörnyezetfiók-oldal](../media/tutorial-setup-lab-account/access-control.png)
2. Az **Engedélyek hozzáadása** oldal **Szerepkör** területén válassza a **Tesztkörnyezet-létrehozó** lehetőséget, válassza ki a Tesztkörnyezet-létrehozó szerepkörhöz hozzáadni kívánt felhasználót, majd kattintson a **Mentés** gombra. 

    ![Felhasználó hozzáadása a Tesztkörnyezet-létrehozó szerepkörhöz](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


## <a name="view-lab-accounts"></a>Labor számlák megtekintése
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **összes erőforrás** a menüből. 
3. Válassza ki **labor szolgáltatások** a a **típus**. 
    Előfizetés, az erőforráscsoport, a helyek és a címkék alapján is szűrheti. 

## <a name="delete-a-lab-account"></a>A tesztlabor-fiók törlése
Kövesse az utasításokat a fenti szakaszban leírt tesztkörnyezet fiókok listában jeleníti meg. A következő utasításokat követve labor fiók törlése: 

1. Válassza ki a **labor fiók** , hogy törölni szeretné. 
2. Válassza ki **törlése** az eszköztáron. 
3. Típus **Igen** megerősítést kér.
4. Válassza a **Törlés** elemet. 

## <a name="next-steps"></a>További lépések
Kezdje meg egy tesztkörnyezet létrehozását az Azure Lab Services használatával:

- [Osztályterem-tesztkörnyezet beállítása](tutorial-setup-classroom-lab.md)
- [A tesztkörnyezet beállítása](../tutorial-create-custom-lab.md)
