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
ms.date: 04/20/2018
ms.author: spelluru
ms.openlocfilehash: 53494abead5701052f6e08f68b01ccdf1bfa211c
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="manage-lab-accounts-in-azure-lab-services-formerly-azure-devtest-labs"></a>Az Azure labor szolgáltatások (korábbi nevén az Azure DevTest Labs) labor fiókok kezelése
Ez a cikk ismerteti, hogyan lab-fiók létrehozása, minden tesztkörnyezeti fiókok megtekintéséhez vagy labor fiók törlése.

## <a name="create-a-lab-account"></a>Tesztlabor-fiók létrehozása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A főmenüből a bal oldalon, válassza ki a **hozzon létre egy erőforrást**.
3. Keresse meg **labor szolgáltatások** az Azure piactéren, és válassza ki a **labor szolgáltatások** a legördülő listában. 
4. Válassza ki **Lab Sercices (előzetes verzió)** szolgáltatások flitered listáján. 
5. Az a **lab-fiók létrehozása** ablakban válassza ki **létrehozása**.
7. Az a **labor fiók** ablakban, a következő műveleteket hajthatja végre: 
    1. A **labor fióknév**, adjon meg egy nevet. 
    2. Válassza ki a **Azure-előfizetés** a kívánja a tesztlabor-fiók létrehozásához.
    3. A **erőforráscsoport**, jelölje be **hozzon létre új**, és adja meg az erőforráscsoport nevét.
    4. A **hely**, válasszon ki egy helyet/régiót, amelyben a labor fiókot létrehozni kívánt. 
    5. Kattintson a **Létrehozás** gombra. 

        ![Labor fiók ablakának létrehozása](./media/how-to-manage-lab-accounts/lab-account-settings.png)
5. Ha a labor fiók a lap nem látható, válassza ki a **értesítések** gombra, majd **forrást** az értesítések gombjára. 

    ![Labor fiók ablakának létrehozása](./media/how-to-manage-lab-accounts/notification-go-to-resource.png)    
6. Lásd a következő **labor fiók** lap:

    ![Labor fiók lap](./media/how-to-manage-lab-accounts/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Felhasználó hozzáadása a labor létrehozó szerepkör
Nevelők labs, az osztályok létrehozásához az engedély megadásához adja hozzá a labor létrehozó szerepkört:

1. Az a **labor fiók** lapon jelölje be **hozzáférés-vezérlés (IAM)**, és kattintson a **+ Hozzáadás** az eszköztáron. 

    ![Labor fiók lap](./media/tutorial-setup-lab-account/access-control.png)
2. Az a **engedélyek hozzáadása** lapon jelölje be **labor létrehozó** a **szerepkör**, válassza ki a labor Creators szerepkör hozzáadása, és válassza ki a kívánt felhasználót **mentése**. 

    ![Felhasználó hozzáadása a labor létrehozó szerepkör](./media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


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
Ismerkedés az Azure labor Services labor beállításához:

- [Egy osztályteremben tesztkörnyezet beállítása](tutorial-setup-classroom-lab.md)
- [Egy egyéni tesztkörnyezet beállítása](tutorial-create-custom-lab.md)
