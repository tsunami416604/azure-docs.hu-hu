---
title: További tulajdonosok hozzáadása laborhoz Azure Lab Services
description: Ebből a cikkből megtudhatja, hogyan adhat hozzá egy felhasználót tulajdonosként egy laborhoz Azure Lab Servicesban.
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 9302cadf0b900c9faac382bad86b51cea7d828fb
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443516"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>További tulajdonosok hozzáadása meglévő laborhoz Azure Lab Services
Ez a cikk bemutatja, hogyan adhat hozzá további tulajdonosokat egy meglévő laborhoz a rendszergazdaként.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Felhasználó hozzáadása az olvasó szerepkörhöz a labor-fiókhoz
Ha egy felhasználót további tulajdonosként szeretne hozzáadni egy meglévő laborhoz, először meg kell adnia a felhasználó **olvasási** engedélyeit a labor-fiókban.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
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