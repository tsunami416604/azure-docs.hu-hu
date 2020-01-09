---
title: Felhasználó hozzáadása labor tulajdonosként Azure Lab Services
description: Ez a cikk bemutatja, hogyan adhat hozzá felhasználót tulajdonosként egy laborhoz.
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
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: 9e007ee6c95843b7e8ef9d7a701237034811489e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480852"
---
# <a name="how-to-add-a-user-as-an-owner-of-a-classroom-lab-in-azure-lab-services"></a>Felhasználó hozzáadása osztálytermi labor tulajdonosaként Azure Lab Services
Ebből a cikkből megtudhatja, hogyan adhat hozzá egy felhasználót egy labor tulajdonosaként a Azure Lab Services-ben.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Felhasználó hozzáadása az olvasó szerepkörhöz a labor-fiókhoz
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben. Keresse meg a **labor Services**elemet, majd válassza ki.
3. Válassza ki a **labor-fiókját** a listából. 
2. A **labor-fiók lapon**válassza a bal oldali menüben a **Access Control (iam)** lehetőséget. 
2. A **hozzáférés-vezérlés (iam)** lapon válassza a **Hozzáadás** lehetőséget az eszköztáron, majd a **szerepkör-hozzárendelés hozzáadása**elemet.

    ![A labor-fiók szerepkör-hozzárendelése ](../media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. A **szerepkör-hozzárendelés hozzáadása** oldalon hajtsa végre a következő lépéseket: 
    1. Válassza ki a **szerepkör** **olvasóját** . 
    2. Válassza ki a felhasználót. 
    3. Kattintson a **Mentés** gombra. 

        ![Felhasználó hozzáadása az olvasó szerepkörhöz a labor-fiókhoz ](../media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>Felhasználó hozzáadása a tulajdonos szerepkörhöz a laborban

1. A labor- **fiók** lapon kattintson a bal oldali menüben a **minden labor** elemre.
2. Válassza ki azt a **labort** , amelyhez hozzá szeretné adni a felhasználót tulajdonosként. 
    
    ![A tesztkörnyezet kiválasztása ](../media/how-to-add-user-lab-owner/select-lab.png)    
3. A **labor** lapon a bal oldali menüben válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
4. A **hozzáférés-vezérlés (iam)** lapon válassza a **Hozzáadás** lehetőséget az eszköztáron, majd a **szerepkör-hozzárendelés hozzáadása**elemet.
5. A **szerepkör-hozzárendelés hozzáadása** oldalon hajtsa végre a következő lépéseket: 
    1. Válassza ki a **szerepkör** **tulajdonosát** . 
    2. Válassza ki a felhasználót. 
    3. Kattintson a **Mentés** gombra. 

## <a name="next-steps"></a>Következő lépések
Győződjön meg róla, hogy a felhasználó látja a labort a [labor Services portálra](https://labs.azure.com)való bejelentkezéskor.