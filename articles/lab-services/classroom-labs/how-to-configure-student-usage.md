---
title: Osztályterem-tesztkörnyezetek az Azure Lab Services használati beállításainak konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja a labor létrehozása a felhasználók számát, azok regisztrálva a labor, szabályozhatja a virtuális Gépet, és további használatához órák száma.
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
ms.date: 11/14/2018
ms.author: spelluru
ms.openlocfilehash: 30c033b487fe58d017080b02c257502f82338164
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51710040"
---
# <a name="configure-usage-settings-and-policies"></a>És használati beállítások és szabályzatok konfigurálása
Ez a cikk bemutatja, hogyan konfigurálhatja a labor létrehozása a felhasználók számát, azok regisztrálva a labor, szabályozhatja a virtuális Gépet, és további használatához órák száma. 


## <a name="specify-the-number-of-users-allowed-into-the-lab"></a>Adja meg a laborkörnyezetbe engedélyezett felhasználók száma

1. Válassza a **Használati szabályzat** lehetőséget. 
2. A **Használati szabályzat** terület beállításaiban adja meg, **hány felhasználó** használhatja a tesztkörnyezetet.
3. Kattintson a **Mentés** gombra. 

    ![Használati szabályzat](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="send-registration-link-to-students"></a>Regisztrációs hivatkozás küldése a diákoknak

1. Váltson a **felhasználók** nézet kiválasztásával **felhasználók** a bal oldali menüben. 
2. Válassza ki **Get-regisztrációs hivatkozást** csempére.

    ![A diákok regisztrációs hivatkozása](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. A **Felhasználói regisztráció** párbeszédablakban kattintson a **Másolás** gombra. A rendszer a vágólapra másolja a hivatkozást. Illessze be egy e-mail-szerkesztőbe, majd küldje el e-mailben a diákoknak. 

    ![A diákok regisztrációs hivatkozása](../media/tutorial-setup-classroom-lab/registration-link.png)
2. A **Felhasználói regisztráció** párbeszédablakban kattintson a **Bezárás** gombra. 
4. Ossza meg a regisztrációs hivatkozást egy diákkal, hogy a diák regisztrálhasson az osztályba. Ha rendelkezik a **korlátozása beállítást** beállítás engedélyezve van, és felhasználók listája szerepel a listában, a következő műveleteket hajthatja végre:
    1. Válassza ki a **e-mail-cím** a felhasználó a listán. 
    2. Megjelenik egy ablak az alapértelmezett e-mail programból, az a **TO** cím kitölti a rendszer. 
    3. Illessze be a **regisztrációs URL-cím** korábban vágólapra másolt. 
    4. Küldés a **e-mail**. 

## <a name="view-users-registered-with-the-lab"></a>A tesztkörnyezetben regisztrált felhasználók megtekintése

Válassza ki **felhasználók** a labor létrehozása a felhasználók listája a bal oldali menü regisztrálva. 

![A labor regisztrált felhasználók listája](../media/how-to-configure-student-usage/users-list.png)

## <a name="set-quotas-per-user"></a>Felhasználónként kvóták beállítása

1. Válassza ki **felhasználók** a bal oldali menüben.
2. Válassza ki **felhasználónként kvóta: korlátlan** az eszköztáron. 
3. Az a **felhasználónként kvóta** lapon jelölje be **óra, a felhasználó használhatja a virtuális gépek számának korlátozásához**. 
4. A **hány órát kíván adni, az egyes felhasználók**, adja meg az órák számát, és válassza ki **mentése**. 

    ![Felhasználónként órák száma](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
5. Most már az eszköztáron látja órák száma: **felhasználónként kvóta: &lt;órák száma&gt;**. 

    ![Kvóta felhasználó szerint](../media/how-to-configure-student-usage/quota-per-user.png)

## <a name="add-users-to-the-lab"></a>Felhasználók hozzáadása a labor létrehozása
Ha rendelkezik a **hozzáférés korlátozása** felhasználók (e-mail cím) beállítás engedélyezve van, adja hozzá a listához.

1. Válassza ki **felhasználók** a bal oldali menüben.
2. Válassza ki **felhasználók hozzáadása** az eszköztáron. 
3. Az a **felhasználók hozzáadása** lap, adja meg a felhasználók e-mail címét, külön sorban, vagy egyetlen sorban, egymástól pontosvesszővel elválasztva. 

    ![Adja hozzá a felhasználó e-mail-címei](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Kattintson a **Mentés** gombra. Láthatja, hogy a felhasználók és azok állapotát (vagy nem regisztrált) a lista e-mail címét. 

    ![Felhasználók listája](../media/how-to-configure-student-usage/users-list-new.png)

### <a name="add-users-by-uploading-a-csv-file"></a>Felhasználók hozzáadása egy CSV-fájl feltöltésével
A felhasználók e-mail-címét tartalmazó CSV-fájl feltöltésével is hozzáadhat felhasználókat.

1. Válassza ki **töltse fel a fürt megosztott kötetei szolgáltatás** az eszköztáron.
2. Válassza ki a felhasználó e-mail-címmel rendelkező CSV-fájlt. Az Excel megnyitásakor minden e-mail-címét egy oszlopot kell megadni. 

## <a name="manage-user-vms"></a>Felhasználói virtuális gépek kezelése
Miután tanulók regisztrálása az Azure Lab Services segítségével a regisztráció társítani a megadott, a tanulók a hozzárendelt virtuális gépeket láthatja a **virtuális gépek** fülre. 

![Diákok rendelt virtuális gépek](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

A következő feladatokat a virtuális gép tanuló teheti meg: 

- Virtuális gép leállítása, ha a virtuális gép fut-e. 
- Virtuális gép elindítása, ha a virtuális gép le van állítva. 
- Csatlakozzon a virtuális géphez. 
- Törölje a virtuális Gépet. 
- Megtekintheti, hogy a felhasználók a virtuális gép felhasznált órák számát. 


## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Rendszergazdaként hozzon létre, és tesztkörnyezetfiókok kezelése](how-to-manage-lab-accounts.md)
- [Labortulajdonosként hozzon létre és laborok kezelése](how-to-manage-classroom-labs.md)
- [Labortulajdonosként állítsa be, és a sablonok közzététele](how-to-create-manage-template.md)
- [Labor felhasználóként osztályterem-tesztkörnyezetek elérése](how-to-use-classroom-lab.md)
