---
title: További tulajdonosok hozzáadása laborhoz Azure Lab Services
description: Ebből a cikkből megtudhatja, hogyan adhat hozzá egy felhasználót tulajdonosként egy laborhoz Azure Lab Servicesban.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6671a3070dae672769eecf59d614d3b75455ef5a
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85445865"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>További tulajdonosok hozzáadása meglévő laborhoz Azure Lab Services
Ez a cikk bemutatja, hogyan adhat hozzá további tulajdonosokat egy meglévő laborhoz a rendszergazdaként.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Felhasználó hozzáadása az olvasó szerepkörhöz a labor-fiókhoz
Ha egy felhasználót további tulajdonosként szeretne hozzáadni egy meglévő laborhoz, először meg kell adnia a felhasználó **olvasási** engedélyeit a labor-fiókban.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben. Keresse meg a **labor Services**elemet, majd válassza ki.
3. Válassza ki a **labor-fiókját** a listából. 
2. A **labor-fiók lapon**válassza a bal oldali menüben a **Access Control (iam)** lehetőséget. 
2. A **hozzáférés-vezérlés (iam)** lapon válassza a **Hozzáadás** lehetőséget az eszköztáron, majd a **szerepkör-hozzárendelés hozzáadása**elemet.

    ![A labor-fiók szerepkör-hozzárendelése ](./media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. A **szerepkör-hozzárendelés hozzáadása** oldalon hajtsa végre a következő lépéseket: 
    1. Válassza ki a **szerepkör** **olvasóját** . 
    2. Válassza ki a felhasználót. 
    3. Kattintson a **Mentés** gombra. 

        ![Felhasználó hozzáadása az olvasó szerepkörhöz a labor-fiókhoz ](./media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>Felhasználó hozzáadása a tulajdonos szerepkörhöz a laborban

1. A labor- **fiók** lapon kattintson a bal oldali menüben a **minden labor** elemre.
2. Válassza ki azt a **labort** , amelyhez hozzá szeretné adni a felhasználót tulajdonosként. 
    
    ![A tesztkörnyezet kiválasztása ](./media/how-to-add-user-lab-owner/select-lab.png)    
3. A **labor** lapon a bal oldali menüben válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
4. A **hozzáférés-vezérlés (iam)** lapon válassza a **Hozzáadás** lehetőséget az eszköztáron, majd a **szerepkör-hozzárendelés hozzáadása**elemet.
5. A **szerepkör-hozzárendelés hozzáadása** oldalon hajtsa végre a következő lépéseket: 
    1. Válassza ki a **szerepkör** **tulajdonosát** . 
    2. Válassza ki a felhasználót. 
    3. Kattintson a **Mentés** gombra. 

## <a name="next-steps"></a>További lépések
Győződjön meg róla, hogy a felhasználó látja a labort a [labor Services portálra](https://labs.azure.com)való bejelentkezéskor.