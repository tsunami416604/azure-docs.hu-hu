---
title: Számítógéptermi labs Azure labor szolgáltatások kezelése |} Microsoft Docs
description: Megtudhatja, hogyan hozza létre és egy osztályteremben, amikor konfigurálja, és tekintse meg az összes a osztályteremben labs, a regisztráció lab-felhasználó a hivatkozásra, vagy egy tesztkörnyezetet törlése fájlmegosztás.
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
ms.openlocfilehash: e388045e839c19c68ad2c00f31d74c73e107872c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="manage-classroom-labs-in-azure-lab-services-formerly-azure-devtest-labs"></a>Az Azure labor szolgáltatások (korábbi nevén az Azure DevTest Labs) osztályteremben labs kezelése
Ez a cikk ismerteti, hogyan létrehozásához és egy osztályteremben, amikor konfigurálja, minden osztályteremben labs megtekintéséhez, vagy egy osztályteremben, amikor törölni.

## <a name="create-a-classroom-lab"></a>Osztályterem-tesztkörnyezet létrehozása

1. Lépjen az [Azure Lab Services weboldalára](https://labs.azure.com).
2. Az **Új tesztkörnyezet** ablakban tegye a következőket: 
    1. Adja meg az osztályterem-tesztkörnyezet **nevét**. 
    2. Válassza ki az osztályteremben használni kívánt virtuális gép **méretét**.
    3. Válassza ki a virtuális gép létrehozásához használandó **rendszerképet**.
    4. Adja meg a **alapértelmezett hitelesítő adatok** jelentkezzen be a virtuális gépek, a laborban használt.
    7. Kattintson a **Mentés** gombra.

        ![Osztályterem-tesztkörnyezet létrehozása](./media/how-to-manage-classroom-labs/new-lab-window.png)
1. A rendszer megjeleníti a tesztkörnyezet **kezdőlapját**. 
    
    ![Az osztályterem-tesztkörnyezet kezdőlapja](./media/how-to-manage-classroom-labs/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Használati szabályzat konfigurálása

1. Válassza a **Használati szabályzat** lehetőséget. 
2. A **Használati szabályzat** terület beállításaiban adja meg, **hány felhasználó** használhatja a tesztkörnyezetet.
3. Kattintson a **Mentés** gombra. 

    ![Használati szabályzat](./media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Sablon beállítása
Egy tesztkörnyezetben sablon egy alapszintű virtuálisgép-lemezkép, ahol minden felhasználó virtuális gépek jönnek létre. Úgy állítsa be a virtuálisgép-sablont, hogy az pontosan aszerint legyen konfigurálva, amit a tesztkörnyezet felhasználóinak biztosítani kíván. Megadhatja a nevet és leírást a labor felhasználók láthatják-sablon. A Virtuálisgép-sablon példányainak labor felhasználók számára történő elérhetővé nyilvános látható-e a sablon értéke.  

### <a name="set-template-title-and-description"></a>Set-sablon nevét és leírását
1. A **Sablon** szakaszban válassza a sablonhoz tartozó **Szerkesztés** (ceruza ikon) elemet. 
2. A **Felhasználói nézet** ablakban adja meg a sablon **címét**.
3. Adja meg a sablon **leírását**.
4. Kattintson a **Mentés** gombra.

    ![Az osztályterem-tesztkörnyezet leírása](./media/how-to-manage-classroom-labs/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>A sablon példányainak nyilvánossá tétele 
Amint **Nyilvános** értékre állította a sablon láthatóságát, az Azure Lab Services létrehozza a virtuális gépeket a tesztkörnyezetben a sablon használatával. Ez a folyamat során létrehozott virtuális gépek száma nem ugyanaz, mint a laborkörnyezetbe, amelyen beállíthatja azt a tesztkörnyezet használati házirendje felhasználók maximális számát. A virtuális gépek konfigurációja megegyezik a sablonéval.  

1. A **Sablon** szakaszban válassza a **Láthatóság** lehetőséget. 
2. A **Rendelkezésre állás** oldalon válassza a **Nyilvános** lehetőséget.
    
    > [!IMPORTANT]
    > Amint a sablon nyilvánosan elérhetővé válik, a hozzáférhetősége nem módosítható privátra. 
3. Kattintson a **Mentés** gombra.

    ![Rendelkezésre állás](./media/how-to-manage-classroom-labs/public-access.png)

## <a name="send-registration-link-to-students"></a>Regisztrációs hivatkozás küldése a diákoknak

1. Válassza a **Felhasználói regisztráció** csempét.
2. A **Felhasználói regisztráció** párbeszédablakban kattintson a **Másolás** gombra. A rendszer a vágólapra másolja a hivatkozást. Illessze be egy e-mail-szerkesztőbe, majd küldje el e-mailben a diákoknak. 

    ![A diákok regisztrációs hivatkozása](./media/how-to-manage-classroom-labs/registration-link.png)

## <a name="view-all-classroom-labs"></a>Minden osztály labs megtekintése
1. Navigáljon a [Azure labor szolgáltatások portálja](https://labs.azure.com).
2. Ellenőrizze, hogy látható-e a kijelölt labor fiók összes labs-e. 

    ![Minden labs](./media/how-to-manage-classroom-labs/all-labs.png)
3. Felső a legördülő lista használatával jelöljön ki egy másik labor fiókot. Megjelenik a kijelölt labor fiók labs szolgáltatásának ismertetése. 

## <a name="delete-a-classroom-lab"></a>Egy osztályteremben tesztkörnyezet törlése
1. A a labor csempére válassza a három pontra (...) sarokban. 

    ![A tesztkörnyezet kiválasztása](./media/how-to-manage-classroom-labs/select-three-dots.png)
2. Válassza a **Törlés** elemet. 

    ![Gomb törlése](./media/how-to-manage-classroom-labs/delete-button.png)
3. Az a **Delete labor** párbeszédpanelen jelölje ki **törlése**. 

    ![Törlése párbeszédpanel](./media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 

## <a name="next-steps"></a>További lépések
Kezdje meg egy tesztkörnyezet létrehozását az Azure Lab Services használatával:

- [Osztályterem-tesztkörnyezet beállítása](how-to-manage-classroom-labs.md)
- [Egyéni tesztkörnyezet beállítása](tutorial-create-custom-lab.md)
