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
ms.date: 04/20/2018
ms.author: spelluru
ms.openlocfilehash: 17544275f921486529518e37eb171cd0b4f26791
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="manage-classroom-labs-in-azure-lab-services-formerly-azure-devtest-labs"></a>Az Azure labor szolgáltatások (korábbi nevén az Azure DevTest Labs) osztályteremben labs kezelése
Ez a cikk ismerteti, hogyan létrehozásához és egy osztályteremben, amikor konfigurálja, minden osztályteremben labs megtekintéséhez, vagy egy osztályteremben, amikor törölni.

## <a name="create-a-classroom-lab"></a>Számítógéptermi labor létrehozása

1. Navigáljon a [Azure labor Services webhelyére](https://labs.azure.com).
2. Az a **új labor** ablakban, a következő műveleteket hajthatja végre: 
    1. Adjon meg egy **neve** a osztályteremben labor. 
    2. Válassza ki a **mérete** alkalmazással tervezi használni az osztály a virtuális gép.
    3. Válassza ki a **kép** használata a virtuális gép létrehozásához.
    4. Adja meg a **alapértelmezett hitelesítő adatok** jelentkezzen be a virtuális gépek, a laborban használt.
    7. Kattintson a **Mentés** gombra.

        ![Számítógéptermi labor létrehozása](./media/how-to-manage-classroom-labs/new-lab-window.png)
1. Megjelenik a **kezdőlap** a labor. 
    
    ![Számítógéptermi labor kezdőlap](./media/how-to-manage-classroom-labs/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Használati szabályzat konfigurálása

1. Válassza ki **használati házirendet**. 
2. A a **használati házirendet**, beállítások, adja meg a **felhasználók száma,** a labor használata engedélyezett.
3. Kattintson a **Mentés** gombra. 

    ![Használati szabályzat](./media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Állítsa be a sablon
A sablon egy tesztkörnyezetben származik, amely minden felhasználó virtuális gépek jönnek létre. Állítson be a sablon virtuális gépet, hogy pontosan mi szeretne biztosítani a labor felhasználóknak konfigurálja azokat. Adjon nevet és leírást a labor felhasználók láthatják-sablon, és a Láthatóság megadása a Virtuálisgép-sablon példányainak elérhetővé a labor felhasználók számára a "Public" értékre.  

## <a name="set-template-title-and-description"></a>Set-sablon nevét és leírását
1. Az a **sablon** szakaszban jelölje be **szerkesztése** (ceruza ikonra) a sablonhoz. 
2. A a **felhasználó** ablak, írja be a **cím** a sablonhoz.
3. Adja meg **leírás** a sablonhoz.
4. Kattintson a **Mentés** gombra.

    ![Számítógéptermi labor leírása](./media/how-to-manage-classroom-labs/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>A sablon példányai nyilvánosságra 
Ha egyszer már megadta, hogy látható-e a kívánt sablont **nyilvános**, Azure labor szolgáltatások hoz létre virtuális gépeket a laborban a sablon használatával. Ez a folyamat során létrehozott virtuális gépek száma ugyanazok, mint a laborkörnyezetbe, amelyen beállíthatja azt a tesztkörnyezet használati házirendje felhasználók maximális számát. Az összes virtuális gép sablonként megegyező konfigurációval rendelkezik.  

1. Válassza ki **látható** a a **sablon** szakasz. 
2. Az a **rendelkezésre állási** lapon jelölje be **nyilvános**.
    
    > [!IMPORTANT]
    > Egy sablon nyilvánosan elérhetővé válik, a hozzáférése a titkos nem módosítható. 
3. Kattintson a **Mentés** gombra.

    ![Rendelkezésre állás](./media/how-to-manage-classroom-labs/public-access.png)

## <a name="send-registration-link-to-students"></a>Regisztrációs hivatkozás küldése diákoknak

1. Válassza ki **felhasználói regisztráció** csempére.
2. Az a **felhasználói regisztráció** párbeszédpanelen jelölje ki a **másolási** gombra. A hivatkozást a vágólapra másolódik. Illessze be egy e-mail-szerkesztőt, és egy e-mailt küldeni a student. 

    ![Student regisztrációs hivatkozás](./media/how-to-manage-classroom-labs/registration-link.png)

## <a name="view-all-classroom-labs"></a>Minden osztály labs megtekintése
1. Navigáljon a [Azure labor szolgáltatások portálja](https://labs.azure.com).
2. Ellenőrizze, hogy látható-e a kijelölt labor fiók összes labs-e. 

    ![Minden labs](./media/how-to-manage-classroom-labs/all-labs.png)
3. Felső a legördülő lista használatával jelöljön ki egy másik labor fiókot. Megjelenik a kijelölt labor fiók labs szolgáltatásának ismertetése. 

## <a name="delete-a-classroom-lab"></a>Egy osztályteremben tesztkörnyezet törlése
1. A a labor csempére válassza a három pontra (...) sarokban. 

    ![Válassza ki a labor](./media/how-to-manage-classroom-labs/select-three-dots.png)
2. Válassza a **Törlés** elemet. 

    ![Gomb törlése](./media/how-to-manage-classroom-labs/delete-button.png)
3. Az a **Delete labor** párbeszédpanelen jelölje ki **törlése**. 

    ![Törlése párbeszédpanel](./media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 

## <a name="next-steps"></a>További lépések
Ismerkedés az Azure labor Services labor beállításához:

- [Egy osztályteremben tesztkörnyezet beállítása](how-to-manage-classroom-labs.md)
- [Egy egyéni tesztkörnyezet beállítása](tutorial-create-custom-lab.md)
