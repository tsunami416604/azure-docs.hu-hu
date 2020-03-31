---
title: További tulajdonosok hozzáadása egy laborhoz az Azure Lab Servicesben
description: Ez a cikk bemutatja, hogyan adhat hozzá egy rendszergazda tulajdonosként egy felhasználót az Azure Lab Services egyik laborjához.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443516"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>További tulajdonosok hozzáadása egy meglévő laborhoz az Azure Lab Servicesben
Ez a cikk bemutatja, hogyan, rendszergazdaként, további tulajdonosok at adhat hozzá egy meglévő tesztkörnyezethez.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Felhasználó hozzáadása a tesztkörnyezet-fiók olvasói szerepköréhez
Ha egy felhasználót további tulajdonosként szeretne hozzáadni egy meglévő tesztkörnyezethez, először **olvasási** engedélyeket kell adnia a tesztkörnyezet-fiókhoz.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza a bal oldali menü **Minden szolgáltatás lehetőséget.** Keresse meg a **Lab Services**elemet, majd jelölje ki.
3. Válassza ki a **laborfiókot** a listából. 
2. A bal oldali menüben válassza a **Hozzáférés-vezérlés (IAM)** elemet a **Laborfiók lapon.** 
2. A **Hozzáférés-vezérlés (IAM)** lapon válassza a **Hozzáadás** gombot az eszköztáron, majd a **Szerepkör-hozzárendelés hozzáadása**lehetőséget.

    ![Szerepkör-hozzárendelés a laborfiókhoz ](../media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. A **Szerepkör-hozzárendelés hozzáadása** lapon tegye a következő lépéseket: 
    1. Válassza az **Olvasó** lehetőséget a **szerepkörhöz.** 
    2. Válassza ki a felhasználót. 
    3. Kattintson a **Mentés** gombra. 

        ![Felhasználó hozzáadása a tesztkörnyezet-fiók olvasói szerepköréhez ](../media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>Felhasználó hozzáadása a tesztkörnyezet tulajdonosi szerepköréhez

1. A **Lab-fiók** lapon válassza a bal oldali menü **Minden laborja** lehetőséget.
2. Válassza ki azt a **tesztkörnyezetet,** amelyhez felhasználót szeretne tulajdonosként hozzáadni. 
    
    ![A tesztkörnyezet kiválasztása ](../media/how-to-add-user-lab-owner/select-lab.png)    
3. A **Labor** lapon válassza a bal oldali menü **Hozzáférés-vezérlés (IAM)** parancsát.
4. A **Hozzáférés-vezérlés (IAM)** lapon válassza a **Hozzáadás** gombot az eszköztáron, majd a **Szerepkör-hozzárendelés hozzáadása**lehetőséget.
5. A **Szerepkör-hozzárendelés hozzáadása** lapon tegye a következő lépéseket: 
    1. Válassza a **tulajdonos** lehetőséget a **szerepkörhöz.** 
    2. Válassza ki a felhasználót. 
    3. Kattintson a **Mentés** gombra. 

## <a name="next-steps"></a>További lépések
Győződjön meg arról, hogy a felhasználó látja a labort, amikor bejelentkezik a [Lab Services portálra.](https://labs.azure.com)