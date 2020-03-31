---
title: Virtuális gépek jelszavainak beállítása az Azure Lab Servicesben | Microsoft dokumentumok
description: Ismerje meg, hogyan állíthatja be és állíthatja alaphelyzetbe a virtuális gépek (VM-ek) jelszavait az Azure Lab Services tantermi laborjaiban.
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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 0ff464936025a20cb6925adc7ef6eb44c2fe1f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933811"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>Virtuális gépek készleteinek létrehozása és kezelése 
Ez a cikk a következő feladatok elvégzését mutatja be:

- A virtuális gépek (VM-ek) számának növelése a laborban
- Az összes virtuális gép vagy a kijelölt virtuális gépek indítása 
- Virtuális gépek alaphelyzetbe állítása

## <a name="update-the-lab-capacity"></a>A labor kapacitásának frissítése
A tesztkörnyezet kapacitásának növeléséhez vagy csökkentéséhez (a virtuális gépek száma egy tesztkörnyezetben) az alábbi lépéseket kell tennie:

1. A **Virtuálisgép-készlet** lapon válassza az **Lab &lt;kapacitás: gépek száma&gt; **lehetőséget.
2. Adja meg a laborban a kívánt virtuális gépek új **számát.** Ennek a számnak nagyobbnak vagy egyenlőnek kell lennie a laborban regisztrált felhasználók számával. 
3. Ezután válassza a **Mentés gombot.** 

    ![Az összes indítása gomb](../media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. Ha megnövelte a kapacitást, láthatja a virtuális gép vagy a virtuális gépek létrehozása. Ha nem látja az új virtuális gép a listában, frissítse a lapot. 

    ![Létrehozás alatt áll a virtuális gép](../media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>Virtuális gépek indítása

### <a name="start-ot-stop-all-vms"></a>Az összes virtuális gép leállítása
1. Váltás a **virtuális gép készletének** lapjára. 
2. Válassza az eszköztár **Az összes indítása** lehetőséget. 

    ![Az összes indítása gomb](../media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. Miután az összes virtuális gép elindult, leállíthatja az összes virtuális gépet az eszköztár **Az összes leállítása** gomb kiválasztásával. 

    ![Az összes leállítása gomb](../media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>A kijelölt virtuális gépek indítása
A kijelölt virtuális gépek (egy vagy több) indításának két módja van. Az első módszer az, hogy válassza ki a virtuális gépet vagy a virtuális gépeket a listában, majd válassza a **Start** lehetőséget az eszköztáron. 

A második lehetőség egy vagy több virtuális gép kiválasztása a listában, és a gomb bekapcsolása az **Állapot** oszlopban. 

![A kijelölt virtuális gépek indítása](../media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

Hasonlóképpen leállíthatja egy vagy több virtuális gépet, ha az **Állapot** oszlopban lévő gombot kikapcsolja, vagy az eszköztárOn a **Leállítás** lehetőséget választja. 

> [!NOTE]
> Amikor egy oktató bekapcsolja a tanuló virtuális gép, kvóta a hallgató nem érinti. A felhasználó kvótája megadja, hogy az ütemezett óraidőn kívül hány laboróra érhető el a felhasználó számára. A kvótákról a [Kvóták beállítása felhasználókszámára című](how-to-configure-student-usage.md?#set-quotas-for-users)témakörben talál további információt.

## <a name="reset-vms"></a>Virtuális gépek alaphelyzetbe állítása
Egy vagy több virtuális gép alaphelyzetbe állításához jelölje ki őket a listában, majd kattintson az eszköztár **Alaphelyzetbe állítás a** lehetőségre. 

![Kijelölt virtuális gépek alaphelyzetbe állítása](../media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

A **Virtuális gép(ek) alaphelyzetbe állítása** párbeszédpanelen válassza az **Alaphelyzet**lehetőséget. 

![Virtuális gép alaphelyzetbe állítása párbeszédpanel](../media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)



## <a name="set-password-for-vms"></a>A virtuális gépek jelszavának beállítása
A labor tulajdonosa (tanár) beállíthatja/alaphelyzetbe állíthatja a virtuális gépek jelszavát a labor létrehozásakor (labor készítő varázsló) vagy a **tesztkörnyezet** létrehozása után a sablon lapon. 

### <a name="set-password-at-the-time-of-lab-creation"></a>Jelszó beállítása a labor létrehozásakor
A labor tulajdonosa (tanár) beállíthatja a virtuális gépek jelszavát a laborban a tesztkörnyezet létrehozása varázsló **virtuálisgép hitelesítő adatait** lapon.

![Új laborablak](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

Az ugyanazon a lapon **található Összes virtuális géphez azonos jelszó használata** beállítás engedélyezésével/letiltásával a tanár dönthet úgy, hogy ugyanazt a jelszót használja a laborban lévő összes virtuális géphez, vagy lehetővé teszi a diákok számára, hogy jelszavakat állítsanak be a virtuális gépekhez. Alapértelmezés szerint ez a beállítás engedélyezve van az összes Windows és Linux operációs rendszer képek, kivéve ubuntu. Ha ez a beállítás le van tiltva, a rendszer kéri a diákokat, hogy állítsanak be egy jelszót, amikor először próbálnak csatlakozni a virtuális géphez. 

### <a name="reset-password-later"></a>Jelszó visszaállítása később

1. A **tesztkörnyezet Sablon** lapján válassza a **Jelszó alaphelyzetbe állítása lehetőséget** az eszköztáron. 
1. A **Jelszó alaphelyzetbe állítása** párbeszédpanelen adjon meg egy jelszót, és válassza **a Jelszó alaphelyzetbe állítása lehetőséget.**
    
    ![Jelszó beállítása párbeszédpanel](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>Csatlakozás diákvirtuális gépekhez
A labor létrehozója (oktató/professzor) csatlakozhat egy tanuló virtuális géphez, ha a következő feltételek teljesülnek: 

- A Lab létrehozásakor **az összes virtuális géphez ugyanazt a jelszót** választotta.
- A virtuális gép fut 

 A tanuló virtuális géphez való csatlakozáshoz vigye az egeret a listában a virtuális gépre, és válassza a számítógép gombját.  

![Csatlakozás a tanulói virtuális géphez gombbal](../media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> Amikor a professzor elindítja a virtuális gép, és csatlakozik hozzá, a hallgatói kvóta nem érinti. 

## <a name="export-list-of-virtual-machines-to-a-csv-file"></a>Virtuális gépek listájának exportálása CSV-fájlba

1. Váltson a **Virtuálisgép-készlet** lapra.
2. Válassza a **...** (három pont) lehetőséget az eszköztáron, majd válassza **a CSV exportálása**lehetőséget. 

    ![Virtuális gépek exportálási listája](../media/how-to-export-users-virtual-machines-csv/virtual-machines-export-csv.png)

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a diákok által konfigurálható egyéb diákhasználati lehetőségekről (mint a labor tulajdonosáról), olvassa el a következő cikket: [A diákok használatának konfigurálása](how-to-configure-student-usage.md).

Ha többet szeretne megtudni arról, hogy a diákok hogyan állíthatják alaphelyzetbe a virtuális gépeik jelszavát, olvassa el a [Virtuális gépek jelszavának beállítása vagy alaphelyzetbe állítása az osztálytermi laborokban (diákok) című témakört.](how-to-set-virtual-machine-passwords-student.md)