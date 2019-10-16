---
title: Több virtuális gépre kiterjedő környezetek engedélyezése Azure Lab Servicesban | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre környezetet több virtuális géppel egy sablonban lévő virtuális gépen a Azure Lab Services egy osztályterem laborjában.
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
ms.date: 10/13/2019
ms.author: spelluru
ms.openlocfilehash: 9a86ba803f899e78b2ba9640e6cc317966969e64
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332316"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>Környezet létrehozása több virtuális géppel egy tantermi laborban lévő virtuális gépen belül
Jelenleg Azure Lab Services lehetővé teszi egy sablonban lévő virtuális gép beállítását egy laborban, és az egyes felhasználók számára egyetlen másolatot készíthet. Ha azonban Ön olyan professzor, amely a tűzfalak vagy kiszolgálók beállítását mutatja be, előfordulhat, hogy minden tanulót egy olyan környezettel kell megadnia, amelyben több virtuális gép is tud kommunikálni egymással egy hálózaton keresztül.

A beágyazott virtualizálás lehetővé teszi, hogy több virtuális gépre kiterjedő környezetet hozzon létre a labor sablonjának virtuális gépén. A sablon közzétételével minden felhasználó számára elérhetővé válik a laborban egy virtuális gép, amelyben több virtuális gép van beállítva.

## <a name="what-is-nested-virtualization"></a>Mi az a beágyazott virtualizálás?
A beágyazott virtualizálás lehetővé teszi, hogy virtuális gépeket hozzon létre egy virtuális gépen belül. A beágyazott virtualizálás a Hyper-V-n keresztül történik, és csak Windows rendszerű virtuális gépeken érhető el.

A beágyazott virtualizálás szolgáltatással kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Beágyazott virtualizálás az Azure-ban](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Beágyazott virtualizálás engedélyezése Azure-beli virtuális gépen](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>Beágyazott virtualizálás használata Azure Lab Services
A fontos lépések a következők:

1. Hozzon létre egy **nagy** méretű **Windows** -sablonos gépet a laborhoz. 
2. Kapcsolódjon hozzá, és [engedélyezze a beágyazott virtualizálás](../../virtual-machines/windows/nested-virtualization.md)szolgáltatást.


A következő eljárás részletesen ismerteti a lépéseket: 

1. Hozzon létre egy labor-fiókot, ha még nem rendelkezik ilyennel. Útmutatásért lásd [: oktatóanyag: labor-fiók beállítása Azure Lab Servicesokkal](tutorial-setup-lab-account.md).
1. Lépjen az [Azure Lab Services weboldalára](https://labs.azure.com). Vegye figyelembe, hogy az Internet Explorer 11 még nem támogatott. 
2. Válassza a **Bejelentkezés** lehetőséget, és adja meg a hitelesítő adatait. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is. 
3. Válassza az **új Labor**elemet. 
    
    ![Osztályterem-tesztkörnyezet létrehozása](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Az **Új tesztkörnyezet** ablakban tegye a következőket: 
    1. Adjon egy **nevet** a tesztkörnyezetnek. 
    2. Válassza a **nagy (beágyazott virtualizálás)** vagy a **közepes (beágyazott virtualizálás)** lehetőséget a **virtuális gép méretének**kiválasztásához.
    6. Válassza ki a használni kívánt Windows- **rendszerképet** . A beágyazott virtualizálás csak Windows rendszerű gépeken érhető el. 
    4. Ezután válassza a **Tovább** lehetőséget. 

        ![Osztályterem-tesztkörnyezet létrehozása](../media/how-to-enable-multi-vm-environment/new-lab-window.png)
    1. A **virtuális gép hitelesítő adatai** lapon a tesztkörnyezet összes virtuális gépe alapértelmezett hitelesítő adatait adhatja meg. Adja meg a felhasználó **nevét** és **jelszavát** , majd kattintson a **tovább**gombra.  

        ![Új tesztkörnyezet ablak](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Jegyezze fel a felhasználónevet és a jelszót, mert többször nem fognak megjelenni.
    3. A **labor-házirendek** lapon adja meg, hogy az egyes felhasználók számára hány óra legyen kiválasztva (az**egyes felhasználókra vonatkozó kvóta**) a tesztkörnyezet ütemezett idején kívül, majd válassza a **Befejezés**gombot. 

        ![Kvóta az egyes felhasználók számára](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Az alábbi képernyő jelenik meg, amely a sablon virtuális gépek létrehozásának állapotát jeleníti meg. A sablon létrehozása a tesztkörnyezetben akár 20 percig is eltarthat. 

    ![A sablon virtuális gép létrehozási állapota](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
1. A **sablon** lapon válassza a **sablon testreszabása** lehetőséget az eszköztáron. 

    ![Sablon testreszabása gomb](../media/how-to-create-manage-template/customize-template-button.png)
2. A **sablon testreszabása** párbeszédpanelen válassza a **Folytatás**lehetőséget. A sablon elindítása és a módosítások elvégzése után már nem lesz ugyanaz a beállítás, mint a virtuális gépek a felhasználók számára legutóbb közzétéve. A sablon módosításait a rendszer addig nem jeleníti meg a felhasználók meglévő virtuális gépein, amíg újra nem teszi közzé.

    ![Testreszabás párbeszédpanel](../media/how-to-create-manage-template/customize-template-dialog.png)
1. A beágyazott virtualizálás konfigurálásához kattintson a **Kapcsolódás sablonhoz** gombra az eszköztáron, hogy csatlakozhasson a sablon virtuális géphez, és kövesse az utasításokat. Ha ez egy Windows rendszerű gép, megjelenik egy lehetőség az RDP-fájl letöltéséhez. 

    ![Csatlakozás a virtuálisgép-sablonhoz](../media/how-to-create-manage-template/connect-template-vm.png) 
9. A sablon virtuális gépen belül állítsa be a beágyazott virtualizálás szolgáltatást, és konfigurálja a virtuális hálózatot több virtuális géppel. Részletes útmutatásért lásd: [beágyazott virtualizálás engedélyezése egy Azure-beli virtuális gépen](../../virtual-machines/windows/nested-virtualization.md). A lépések rövid összefoglalása: 
    1. Engedélyezze a Hyper-V szolgáltatást a sablon virtuális gépén.
    2. Belső virtuális hálózat beállítása internetkapcsolattal a beágyazott virtuális gépekhez
    3. Virtuális gépek létrehozása a Hyper-V kezelőjével
    4. IP-cím társítása a virtuális gépekhez
10. A **sablon** lapon válassza a **Közzététel** lehetőséget az eszköztáron. 

    ![Sablon közzététele gomb](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Közzététel után a lépés nem vonható vissza. 
8. A **sablon közzététele** lapon adja meg a laborban létrehozni kívánt virtuális gépek számát, majd válassza a **Közzététel**lehetőséget. 

    ![Sablon közzététele – virtuális gépek száma](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. A sablon **közzétételének állapota** az oldalon látható. Ez a folyamat akár egy órát is igénybe vehet. 

    ![Sablon közzétételének folyamata](../media/tutorial-setup-classroom-lab/publish-template-progress.png)


## <a name="next-steps"></a>Következő lépések

Most minden felhasználó egyetlen virtuális gépet kap, amely tartalmaz egy több virtuális gépre kiterjedő környezetet. Ha szeretné megtudni, hogyan adhat hozzá felhasználókat a laborhoz, és hogyan küldhet hozzájuk regisztrációs hivatkozást, tekintse meg a következő cikket: [felhasználók hozzáadása a laborhoz](tutorial-setup-classroom-lab.md#add-users-to-the-lab).