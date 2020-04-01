---
title: Tesztkörnyezetfiók beállítása az Azure Lab Services szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan állíthat be egy tesztkörnyezet-fiókot az Azure Lab Services használatával, adhat hozzá egy laborkészítőt, és hogyan adhatja meg a labsiak által a laborokban használandó Marketplace-lemezképeket.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: dba6a4c07691f3d7ec88d8b889e68d6ac7116f07
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239447"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Oktatóanyag: Tesztkörnyezetfiók beállítása az Azure Lab Services szolgáltatással
Az Azure Lab Services szolgáltatásban a tesztkörnyezetfiók központi fiókként szolgál a vállalat összes tesztkörnyezetének felügyeletéhez. A tesztkörnyezetfiókban engedélyeket adhat másoknak a tesztkörnyezetek létrehozására, és szabályzatokat állíthat be, amelyek a tesztkörnyezetfiók alá tartozó összes tesztkörnyezetben érvényesek. Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre laborfiókot. 

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Tesztkörnyezetfiók létrehozása
> * Felhasználó hozzáadása a Tesztkörnyezet-létrehozó szerepkörhöz

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="create-a-lab-account"></a>Tesztkörnyezetfiók létrehozása
A következő lépések bemutatják, hogyan használhatja az Azure Portalt tesztkörnyezetfiók létrehozására az Azure Lab Services szolgáltatásban. 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza a bal oldali menü **Minden szolgáltatás lehetőséget.** Válassza a **DevOps** elemet **a Kategóriák közül.** Ezután válassza a **Lab Services lehetőséget.** Ha a Lab`*`Services **(Csillag)** lehetőséget választja a Lab Services mellett, az a bal oldali menü **KEDVENCEK** szakaszába kerül. A következő alkalommal válassza lab **szolgáltatások** alatt **KEDVENCEK**.

    ![Minden szolgáltatás -> Lab Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. A **Lab Services** lapon válassza a **Hozzáadás** lehetőséget az eszköztáron, vagy kattintson a lap **Laborfiók létrehozása** gombjára. 

    ![Válassza a Hozzáadás lehetőséget a Laborfiókok lapon](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. A **Laborfiók létrehozása** lap **Alapjai** lapján hajtsa végre a következő műveleteket: 
    1. Írjon be egy nevet a **Tesztkörnyezetfiók neve** mezőbe. 
    2. Válassza ki azt az **Azure-előfizetést**, amelyben a tesztkörnyezetfiókot létre szeretné hozni.
    3. Az **Erőforráscsoport csoportban**jelöljön ki egy meglévő erőforráscsoportot, vagy válassza **az Új létrehozása**lehetőséget, és adja meg az erőforráscsoport nevét.
    4. A **Hely területen**válassza ki azt a helyet/régiót, ahol létre szeretné hozni a laborfiókot. 

        ![Laborfiók - alapismeretek lap](../media/tutorial-setup-lab-account/lab-account-basics-page.png)
    5. Válassza az **Áttekintés + létrehozás** lehetőséget.
    6. Tekintse át az összegzést, és válassza a **Létrehozás gombot.** 

        ![Véleményezés + létrehozás -> létrehozás](../media/tutorial-setup-lab-account/create-button.png)    
5. Amikor a központi telepítés befejeződött, bontsa ki a **Következő lépéseket**, és válassza az Ugrás **az erőforrásra**lehetőséget. 

    ![Ugrás a tesztkörnyezet-fiók lapra](../media/tutorial-setup-lab-account/go-to-lab-account.png)
6. Erősítse meg, hogy megjelenik a **Laborfiók** lap. 

    ![Tesztkörnyezetfiók-oldal](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Felhasználó hozzáadása a Tesztkörnyezet-létrehozó szerepkörhöz
A felhasználónak **Tesztkörnyezet-létrehozó** szerepkörrel kell rendelkeznie a tesztkörnyezetfiókban ahhoz, hogy létrehozhasson egy osztályterem-tesztkörnyezetet. Ha engedélyt szeretne adni az oktatóknak arra, hogy laborokat hozzanak létre az osztályaikszámára, adja hozzá őket a **Lab Creator** szerepkörhöz:

> [!NOTE]
> A tesztkörnyezetfiók létrehozásához használt fiók automatikusan hozzáadódik ehhez a szerepkörhöz. Ha ugyanazt a felhasználói fiókot szeretné használni az oktatóanyagban egy tantermi labor létrehozásához, hagyja ki ezt a lépést. 

1. A **Laborfiók** lapon válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget, válassza a **+ Hozzáadás lehetőséget** az eszköztáron, majd kattintson a **+ Szerepkör-hozzárendelés hozzáadása gombra** az eszköztáron. 

    ![Hozzáférés-vezérlés -> Szerepkör-hozzárendelés hozzáadása gomb](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. A **Szerepkör-hozzárendelés hozzáadása** lapon válassza a Lab Creator for Role **(Laborkészítő** **szerepkörhöz**) lehetőséget, jelölje ki a Lab Alkotók szerepkörhöz hozzáadni kívánt felhasználót, és válassza a **Mentés gombot.** 

    ![Laborkészítő hozzáadása](../media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban létrehozott egy tesztkörnyezetfiókot. Ha meg szeretné tudni, hogyan hozhat létre tantermi labort professzorként, továbbadad a következő oktatóanyaghoz:

> [!div class="nextstepaction"]
> [Osztályterem-tesztkörnyezet beállítása](tutorial-setup-classroom-lab.md)

