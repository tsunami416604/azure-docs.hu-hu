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
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: 834674eb63af75088434db0f614b11c7a36e7adf
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55964814"
---
# <a name="configure-usage-settings-and-policies"></a>És használati beállítások és szabályzatok konfigurálása
Ez a cikk ismerteti a felhasználók hozzáadása a tesztkörnyezethez, azok regisztrálva a labor, szabályozhatja a virtuális Gépet, és további használatához órák száma. 


## <a name="add-users-to-the-lab"></a>Felhasználók hozzáadása a labor létrehozása
Ha rendelkezik a **hozzáférés korlátozása** felhasználók (e-mail cím) beállítás engedélyezve van, adja hozzá a listához.

1. Válassza ki **felhasználók** a bal oldali menüben.
2. Válassza ki **felhasználók hozzáadása** az eszköztáron. 

    ![Felhasználók gomb hozzáadása](../media/how-to-configure-student-usage/add-users-button.png)
1. Az a **felhasználók hozzáadása** lap, adja meg a felhasználók e-mail címét, külön sorban, vagy egyetlen sorban, egymástól pontosvesszővel elválasztva. 

    ![Adja hozzá a felhasználó e-mail-címei](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Kattintson a **Mentés** gombra. Láthatja, hogy a felhasználók és azok állapotát (vagy nem regisztrált) a lista e-mail címét. 

    ![Felhasználók listája](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="send-registration-link-to-students"></a>Regisztrációs hivatkozás küldése a diákoknak
Az alábbi eljárás lépéseit egy regisztrációs hivatkozás küldése felhasználóknak rendelkezik. Ha a **hozzáférés korlátozása** a tesztkörnyezethez engedélyezve van, csak a felhasználók a listán szereplő felhasználók rögzítheti a regisztráció hivatkozásra a tesztkörnyezethez. 

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

![A labor regisztrált felhasználók listája](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-per-user"></a>Felhasználónként kvóták beállítása
Felhasználónként kvóták az alábbi lépéseket követve állíthatja be: 

1. Válassza ki **felhasználók** a bal oldali menüben.
2. Válassza ki **felhasználónként kvóta: korlátlan** az eszköztáron. 
3. Az a **felhasználónként kvóta** lapra, jelölje be az alábbi lehetőségek közül: 
    1. **Nincs**. Felhasználók a saját virtuális gépek csak az ütemezett időszakban, vagy ha a labor tulajdonos kapcsolja be a virtuális gépek a számukra.
    2. **Korlátlan (alapértelmezett)**. Felhasználók használhatják a virtuális gépeik időbeli korlátozások nélkül.
    3. **Adja meg a felhasználónként órák száma**. Óra (az alább megadott) mellett a megadott időpont a meghatározott számú virtuális gépeik felhasználók használhatják. Ha ezt a lehetőséget választja, adja meg a **órák száma** a szövegmezőben. 

        ![Felhasználónként órák száma](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
    4. Kattintson a **Mentés** gombra. 
5. Módosított értéke most már az eszköztáron látható: **Felhasználói kvóta: &lt;órák száma&gt;**. 

    ![Kvóta felhasználó szerint](../media/how-to-configure-student-usage/quota-per-user.png)

> [!IMPORTANT]
> A [ütemezett futtatási idő a virtuális gépek](how-to-create-schedules.md) nem számítanak bele a kvóta növeléséhez, a felhasználó számára engedélyezett. A kvóta pedig egy diák foglalkozik a virtuális gépek idő ütemezése kívül alkalommal. 

### <a name="add-users-by-uploading-a-csv-file"></a>Felhasználók hozzáadása egy CSV-fájl feltöltésével
A felhasználók e-mail-címét tartalmazó CSV-fájl feltöltésével is hozzáadhat felhasználókat.

1. Hozzon létre egy CSV-fájlt egy oszlop felhasználók e-mail címét.

    ![Kvóta felhasználó szerint](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. Az a **felhasználók** a labor létrehozása, válassza a lap **töltse fel a fürt megosztott kötetei szolgáltatás** az eszköztáron.

    ![Töltse fel a fürt megosztott kötetei szolgáltatás gomb](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Válassza ki a felhasználó e-mail-címmel rendelkező CSV-fájlt. Ha bejelöli **nyílt** válassza ki a CSV-fájlt, lásd a következő **felhasználók hozzáadása** ablak. Az e-mail-címek listájából a CSV-fájlból az e-mail-címmel rendelkező ki van töltve. 

    ![Feltöltve a CSV-fájlból az e-mail-címmel rendelkező felhasználók ablak hozzáadása](../media/how-to-configure-student-usage/add-users-window.png)
4. Válassza ki **mentése** a a **felhasználók hozzáadása** ablak. 
5. Győződjön meg arról, hogy a felhasználók a listán szereplő felhasználók. 

    ![A hozzáadott felhasználók listája](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="manage-user-vms"></a>Felhasználói virtuális gépek kezelése
Miután tanulók regisztrálása az Azure Lab Services segítségével a regisztráció társítani a megadott, a tanulók a hozzárendelt virtuális gépeket láthatja a **virtuális gépek** fülre. 

![Diákok rendelt virtuális gépek](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

A következő feladatokat a virtuális gép tanuló teheti meg: 

- Virtuális gép leállítása, ha a virtuális gép fut-e. 
- Virtuális gép elindítása, ha a virtuális gép le van állítva. 
- Csatlakozzon a virtuális géphez. 
- Törölje a virtuális Gépet. 
- Megtekintheti, hogy a felhasználók a virtuális gép felhasznált órák számát. 

## <a name="update-number-of-virtual-machines-in-lab"></a>A lab-ben a virtuális gépek számának frissítése
Ha frissíteni szeretné a laborkörnyezetben található virtuális gépek száma, az alábbi lépéseket a **virtuális gépek** lap:

1. Válassza ki **virtuális gépek** a bal oldali menüben. 
2. Válassza ki **labor kapacitás: &lt;szám&gt; gépet** az eszköztáron. 
3. Adja meg a **szám** a virtuális gépek.
4. Kattintson a **Mentés** gombra.

    ![A lab-ben a virtuális gépek](../media/how-to-configure-student-usage/number-virtual-machines.png)


## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Rendszergazdaként hozzon létre, és tesztkörnyezetfiókok kezelése](how-to-manage-lab-accounts.md)
- [Labortulajdonosként hozzon létre és laborok kezelése](how-to-manage-classroom-labs.md)
- [Labortulajdonosként állítsa be, és a sablonok közzététele](how-to-create-manage-template.md)
- [Labor felhasználóként osztályterem-tesztkörnyezetek elérése](how-to-use-classroom-lab.md)
