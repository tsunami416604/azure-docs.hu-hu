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
ms.openlocfilehash: 705209becff7c8ad20e7d09f056aa1ae1577b7ae
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655148"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Számítógéptermi labs Azure labor Services kezelése 
Ez a cikk ismerteti, hogyan létrehozásához és egy osztályteremben, amikor konfigurálja, minden osztályteremben labs megtekintéséhez, vagy egy osztályteremben, amikor törölni.

## <a name="create-a-classroom-lab"></a>Osztályterem-tesztkörnyezet létrehozása

1. Lépjen az [Azure Lab Services weboldalára](https://labs.azure.com).
2. Az **Új tesztkörnyezet** ablakban tegye a következőket: 
    1. Adja meg az osztályterem-tesztkörnyezet **nevét**. 
    2. Válassza ki az osztályteremben használni kívánt virtuális gép **méretét**.
    3. Válassza ki a virtuális gép létrehozásához használandó **rendszerképet**.
    4. Adja meg a **alapértelmezett hitelesítő adatok** jelentkezzen be a virtuális gépek, a laborban használt.
    7. Kattintson a **Mentés** gombra.

        ![Osztályterem-tesztkörnyezet létrehozása](../media/how-to-manage-classroom-labs/new-lab-window.png)
1. A rendszer megjeleníti a tesztkörnyezet **kezdőlapját**. 
    
    ![Az osztályterem-tesztkörnyezet kezdőlapja](../media/how-to-manage-classroom-labs/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Használati szabályzat konfigurálása

1. Válassza a **Használati szabályzat** lehetőséget. 
2. A **Használati szabályzat** terület beállításaiban adja meg, **hány felhasználó** használhatja a tesztkörnyezetet.
3. Kattintson a **Mentés** gombra. 

    ![Használati szabályzat](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Sablon beállítása
A tesztkörnyezet sablonja egy alapszintű virtuálisgép-rendszerkép, amelyből az összes felhasználó virtuális gépe létrejön. Úgy állítsa be a virtuálisgép-sablont, hogy az pontosan aszerint legyen konfigurálva, amit a tesztkörnyezet felhasználóinak biztosítani kíván. A sablonhoz megadhat egy nevet és egy leírást, amely a tesztkörnyezet felhasználói számára jelenik meg. Beállíthatja a sablon láthatóságát, hogy a tesztkörnyezet felhasználói számára elérhető példányokat hozzon létre a virtuálisgép-sablonról.  

### <a name="set-template-title-and-description"></a>Set-sablon nevét és leírását
1. A **Sablon** szakaszban válassza a sablonhoz tartozó **Szerkesztés** (ceruza ikon) elemet. 
2. A **Felhasználói nézet** ablakban adja meg a sablon **címét**.
3. Adja meg a sablon **leírását**.
4. Kattintson a **Mentés** gombra.

    ![Az osztályterem-tesztkörnyezet leírása](../media/how-to-manage-classroom-labs/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>A sablon példányainak nyilvánossá tétele 
Amint **Nyilvános** értékre állította a sablon láthatóságát, az Azure Lab Services létrehozza a virtuális gépeket a tesztkörnyezetben a sablon használatával. A folyamat során létrehozott virtuális gépek száma megegyezik a tesztkörnyezet felhasználóinak maximális számával, amelyet a tesztkörnyezet használati szabályzatában állíthat be. A virtuális gépek konfigurációja megegyezik a sablonéval.  

1. A **Sablon** szakaszban válassza a **Láthatóság** lehetőséget. 
2. A **Rendelkezésre állás** oldalon válassza a **Nyilvános** lehetőséget.
    
    > [!IMPORTANT]
    > Amint a sablon nyilvánosan elérhetővé válik, a hozzáférhetősége nem módosítható privátra. 
3. Kattintson a **Mentés** gombra.

    ![Rendelkezésre állás](../media/how-to-manage-classroom-labs/public-access.png)

## <a name="send-registration-link-to-students"></a>Regisztrációs hivatkozás küldése a diákoknak

1. Válassza a **Felhasználói regisztráció** csempét.
2. A **Felhasználói regisztráció** párbeszédablakban kattintson a **Másolás** gombra. A rendszer a vágólapra másolja a hivatkozást. Illessze be egy e-mail-szerkesztőbe, majd küldje el e-mailben a diákoknak. 

    ![A diákok regisztrációs hivatkozása](../media/how-to-manage-classroom-labs/registration-link.png)

## <a name="view-all-classroom-labs"></a>Minden osztály labs megtekintése
1. Navigáljon a [Azure labor szolgáltatások portálja](https://labs.azure.com).
2. Ellenőrizze, hogy látható-e a kijelölt labor fiók összes labs-e. 

    ![Minden labs](../media/how-to-manage-classroom-labs/all-labs.png)
3. Felső a legördülő lista használatával jelöljön ki egy másik labor fiókot. Megjelenik a kijelölt labor fiók labs szolgáltatásának ismertetése. 

## <a name="delete-a-classroom-lab"></a>Egy osztályteremben tesztkörnyezet törlése
1. A a labor csempére válassza a három pontra (...) sarokban. 

    ![A tesztkörnyezet kiválasztása](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Válassza a **Törlés** elemet. 

    ![Gomb törlése](../media/how-to-manage-classroom-labs/delete-button.png)
3. Az a **Delete labor** párbeszédpanelen jelölje ki **törlése**. 

    ![Törlése párbeszédpanel](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 

## <a name="next-steps"></a>További lépések
Kezdje meg egy tesztkörnyezet létrehozását az Azure Lab Services használatával:

- [Osztályterem-tesztkörnyezet beállítása](how-to-manage-classroom-labs.md)
- [A tesztkörnyezet beállítása](../tutorial-create-custom-lab.md)
