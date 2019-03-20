---
title: Több virtuális gépes környezet az Azure Lab Services engedélyezése |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy környezetet egy sablon virtuális gépen az Azure Lab Services osztályterem-tesztkörnyezet több virtuális gép.
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
ms.date: 03/18/2019
ms.author: spelluru
ms.openlocfilehash: 6faf32232c42f863bff52fdfb3c0714aee8e9b88
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58190557"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>Osztályterem-tesztkörnyezet, a virtuális gép sablon belül több virtuális környezet létrehozása
Jelenleg az Azure Lab Services lehetővé teszi, hogy a sablon egy virtuális gép tesztkörnyezetben, és a egy példányt minden, a felhasználó számára elérhetővé tenni. Azonban ha Ön egy oktatási egy informatikai osztály a tűzfalak vagy kiszolgálók beállítása professzor, szükség lehet egy olyan környezetben, amelyben több virtuális gép kommunikálhassanak egymással a hálózaton keresztül a tanulók minden egyes biztosításához.

Beágyazott virtualizálás lehetővé teszi, hogy egy tesztkörnyezet egy sablon virtuális gépen belül több virtuális gépes környezet létrehozása. A sablon közzététele biztosít minden felhasználó a lab-ben több virtuális gépen belül, állítsa be a virtuális gép.

## <a name="what-is-nested-virtualization"></a>Mit jelent a beágyazott virtualizálás?
Beágyazott virtualizálás lehetővé teszi, hogy hozzon létre egy virtuális gép virtuális gépeit. Beágyazott virtualizálás Hyper-V segítségével történik, és csak a Windows virtuális gépeken érhető el.

Beágyazott virtualizálás kapcsolatos további információkért lásd a következő cikkeket:

- [Beágyazott virtualizálás az Azure-ban](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Egy Azure virtuális Gépen a beágyazott virtualizálás engedélyezése](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>Beágyazott virtualizálás használata az Azure Lab Services
A fontos lépések a következők:

1. Hozzon létre egy **nagy** méretű **Windows** sablon gép a tesztkörnyezethez. 
2. Csatlakozzon hozzá, és [beágyazott virtualizálás engedélyezése](../../virtual-machines/windows/nested-virtualization.md).


Az alábbi eljárás a részletes lépéseket biztosít: 

1. Tesztkörnyezetfiók létrehozása, ha még nincs ilyen. Útmutatásért lásd: [oktatóanyag: Az Azure Lab Services tesztkörnyezetfiók beállítása](tutorial-setup-lab-account.md).
2. Lépjen az [Azure Lab Services weboldalára](https://labs.azure.com). 
3. Válassza a **Bejelentkezés** lehetőséget, és adja meg a hitelesítő adatait. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is. 
4. Az **Új tesztkörnyezet** ablakban tegye a következőket: 
    1. Adjon egy **nevet** a tesztkörnyezetnek. 
    2. Adja meg a maximális **virtuális gépek száma** a tesztkörnyezetben. Növelheti vagy decreate a virtuális gépek száma, a tesztkörnyezet létrehozása után, vagy egy meglévő lab-ben. További információkért lásd: [a labor virtuális gépek számának frissítése](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)
    6. Kattintson a **Mentés** gombra.

        ![Osztályterem-tesztkörnyezet létrehozása](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. A **virtuális gép specifikációinak kiválasztására** szolgáló lapon hajtsa végre a következőket:
    1. Válassza ki **nagy** méretét, a virtuális gépek (VM) hozhatók létre a tesztkörnyezetben. Jelenleg csak a nagy méretű támogatja a beágyazott virtualizálás.
    2. Válassza ki a virtuálisgép-lemezkép, amely egy **Windows-lemezkép**. Beágyazott virtualizálás csak akkor használható Windows-gépeken. 
    3. Kattintson a **Tovább** gombra.

        ![Virtuális gép specifikációinak megadása](../media/how-to-enable-multi-vm-environment/large-windows-vm.png)    
5. A **Hitelesítő adatok beállítása** oldalon adja meg az alapértelmezett hitelesítő adatokat a tesztkörnyezet összes virtuális gépéhez. 
    1. Adja meg a **felhasználónevet** a tesztkörnyezet összes virtuális gépéhez.
    2. Adja meg a felhasználó **jelszavát**. 

        > [!IMPORTANT]
        > Jegyezze fel a felhasználónevet és a jelszót, mert többször nem fognak megjelenni.
    3. Kattintson a **Létrehozás** gombra. 

        ![Hitelesítő adatok beállítása](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. A **Sablon konfigurálása** oldalon látható a tesztkörnyezet létrehozási folyamatának állapota. A sablon létrehozása a tesztkörnyezetben akár 20 percig is eltarthat. 

    ![Sablon konfigurálása](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Miután a sablon konfigurálása befejeződött, az alábbi oldal jelenik meg: 

    ![Sablon konfigurálása oldal a folyamat befejeződése után](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Az a **konfigurálása sablon** lapon jelölje be **Connect** szeretne csatlakozni a virtuális gép konfigurálásához a beágyazott virtualizálás sablont. Beállíthatja úgy is később a a varázsló lépéseinek befejezése után. 
9. A sablon virtuális gépen állítsa be a beágyazott virtualizálás, és több virtuális gépet a virtuális hálózat konfigurálása. A részletes részletes tudnivalókért lásd: [egy Azure virtuális Gépen a beágyazott virtualizálás engedélyezése](../../virtual-machines/windows/nested-virtualization.md). A következő lépések rövid összefoglalás: 
    1. Engedélyezze a Hyper-V szolgáltatást, a sablon virtuális gépen.
    2. A beágyazott virtuális gépek internet-kapcsolattal egy belső virtuális hálózat beállítása
    3. A Hyper-V kezelője segítségével a virtuális gépek létrehozása
    4. IP-címet hozzárendelni a virtuális gépekhez
10. Válassza ki a **Tovább** gombot a sablon oldalán. 
11. A **Sablon közzététele** oldalon tegye az alábbiakat. 
    1. A sablon azonnali közzétételének, és válassza ki **közzététel**.  

        > [!WARNING]
        > Közzététel után a lépés nem vonható vissza. 
    2. Későbbi közzétételhez kattintson a **Mentés későbbre** gombra. A varázsló futtatása után közzéteheti a virtuálisgép-sablont. Ha tudni szeretné, hogyan konfigurálhatja és teheti közzé a sablont a varázsló futtatása után, tekintse át a [sablon közzétételével](how-to-create-manage-template.md#publish-the-template-vm) foglalkozó szakaszt az [osztálytermi tesztkörnyezetek kezelését](how-to-manage-classroom-labs.md) ismertető cikkben.

        ![Sablon közzététele](../media/how-to-enable-multi-vm-environment/publish-template-page.png)
11. Nyomon követheti, hogy **hol tart a sablon közzététele**. Ez a folyamat akár egy órát is igénybe vehet. 

    ![Sablon közzétételének folyamata](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Miután a sablon közzététele sikeresen befejeződött, az alábbi oldalt fogja látni. Válassza a **Done** (Kész) lehetőséget.

    ![Sablon sikeres közzététele](../media/tutorial-setup-classroom-lab/publish-success.png)
1. A rendszer megjeleníti a tesztkörnyezet **irányítópultját**. 
    
    ![Az osztályterem-tesztkörnyezet irányítópultja](../media/how-to-enable-multi-vm-environment/dashboard.png)


## <a name="next-steps"></a>További lépések

Most a minden felhasználó egyetlen virtuális gép, amely tartalmazza a benne található egy több virtuális gépes környezet beolvasása. Megtudhatja, hogyan felhasználók hozzáadása a labor és regisztrációs hivatkozást küldeni, tekintse meg a következő cikket: [Felhasználók hozzáadása a labor](tutorial-setup-classroom-lab.md#add-users-to-the-lab).