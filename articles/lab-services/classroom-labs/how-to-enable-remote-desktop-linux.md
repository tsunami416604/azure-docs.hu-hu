---
title: A távoli asztal engedélyezése az Azure Lab Services Linux rendszeren |} A Microsoft Docs
description: 'Útmutató: Linux rendszerű virtuális gépek az Azure Lab Services szolgáltatásban létrehozott tesztkörnyezet távoli asztal engedélyezése.'
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
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 28f963ab807cbd679b7efe8b034ab91f2893198e
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58659456"
---
# <a name="enable-and-use-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Engedélyezze, és a távoli asztal használata Linux rendszerű virtuális gépek az Azure Lab Services szolgáltatásban létrehozott tesztkörnyezet
Ez a cikk bemutatja, hogyan lehet a következő feladatokat végezheti el:

- Linux rendszerű virtuális Géphez tartozó távoli asztal engedélyezése
- Oktatói hogyan csatlakozhat a virtuális gép keresztül a távoli asztali kapcsolat (RDP) sablont.
- Hogyan a tanulók a tanulói virtuális gép RDP-n keresztül kapcsolódni

## <a name="enable-remote-desktop-for-linux-vm"></a>Linux rendszerű virtuális Géphez tartozó távoli asztal engedélyezése
Labor létrehozása során, engedélyezheti a tanárok **távoli asztali kapcsolat** a **Linux** lemezképek. A **távoli asztali kapcsolat engedélyezése** beállítás jelenik meg, ha a sablon egy Linux-rendszerképek van kiválasztva. Ha ez a beállítás engedélyezve van, az oktatók csatlakozhat sablon virtuális gép és a diákok virtuális gépek (távoli asztali verzió) RDP-n keresztül. 

![Egy Linux-rendszerképek a távoli asztali kapcsolat engedélyezése](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

> [!IMPORTANT] 
> Engedélyezése **távoli asztali kapcsolat** csak megnyílik a **RDP** portot Linux rendszerű gépeken. Ön, mint a tanári a Linux-gép először SSH használatával csatlakozhat, és az RDP és a grafikus felhasználói Felülettel csomagok telepítése, úgy, hogy a Linux-gép később RDP használatával csatlakozhat. Ezután az **közzététele** a lemezképet, hogy a diákok a tanulói Linux rendszerű virtuális gépekhez az RDP is. 

A távoli asztali kapcsolat jelenleg a következő operációs rendszereken támogatott:

- openSUSE Leap 42.3
- CentOS-alapú 7.5
- Debian 9 "Stretch"
- Ubuntu Server 16.04 LTS

## <a name="teachers-connecting-to-the-template-vm-using-rdp"></a>A sablon virtuális gép csatlakozik az oktatók RDP-vel
Az oktatók kapcsolódnia kell a virtuális gép sablon először SSH-val, majd telepíteni rá RDP és a GUI-csomagokat. A tanárok ezt követően használhatja az alábbi lépéseket a Linux rendszerű virtuális gépek RDP-vel csatlakozni: 

Megjelenik a **a távoli asztal** lehetőség csatlakozni, hogy a Virtuálisgép-sablon a tesztkörnyezet létrehozása idején. 

![Sablon RDP-n keresztül kapcsolódni a létrehozás időpontjában](../media/how-to-enable-remote-desktop-linux/connect-at-creation.png)

Megjelenik a **a távoli asztal** lehetőséget, a labor kezdőlapja a labor létrehozása után, majd a sablon virtuális gép elindul. Indítsa el a Virtuálisgép-sablon, ha nincs már elindult. 

![A labor létrehozása után a sablon RDP-n keresztül kapcsolódni](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 

Amikor kiválasztja a **RDP** lehetőség, letölt egy RDP-fájlt. Megnyitás a Linux rendszerű géphez való csatlakozásra. 

## <a name="teachers-connecting-to-a-student-vm-using-rdp"></a>Csatlakozhat a virtuális gép tanuló tanárok RDP-vel
A labor tulajdonosa (oktatói/professzor) tud csatlakozni egy diák virtuális gép átvált a a **virtuális gépek** megtekintéséhez, majd válassza ki a **csatlakozás** ikonra. Az oktatók kell **közzététele** RDP és a grafikus felhasználói Felülettel csomagokkal telepítve van a sablon lemezképe. 

![Az oktatók a tanulói virtuális gép csatlakoztatása](../media/how-to-enable-remote-desktop-linux/teacher-connect-to-student-vm.png)

## <a name="students-connecting-to-the-student-vm"></a>Csatlakozás a virtuális gép tanulói diákoknak
Tanulói is a Linux rendszerű virtuális gépekhez az RDP a labor tulajdonosa (oktatói/professzor) után **közzéteszi** a sablon csomagok RDP és a grafikus felhasználói Felülettel rendelkező virtuális gép telepítve van a gépen. A lépések a következők: 

1. Amikor egy tanuló jelentkezik be a Labs portálon közvetlenül (`http://labs.azure.com`) vagy egy regisztrációs hivatkozással (`http://labs.azure.com/register/<registrationCode>`), egy csempe hozzáfér minden labor a tanulói számára jelenik meg. 
2. A csempére, válassza ki a **Start** Ha a virtuális gép le van állítva. 
3. Kattintson a **Csatlakozás** gombra. Ez a művelet a gép be RDP-fájljának letöltése. Mentse, és nyissa meg a Linux-gép RDP-n keresztül kapcsolódni. 

    ![VM - tanulói RDP letöltése](../media/how-to-enable-remote-desktop-linux/student-rdp-download.png)

    Továbbra is csatlakozhat a Linux rendszerű virtuális gép SSH használatával. Válassza ki **... három (pont)**  az SSH-beállítás megtekintéséhez. 
    
    ![Tanulói VM - SSH](../media/how-to-enable-remote-desktop-linux/student-ssh.png)

    Másolja ki és mentse az SSH-kapcsolati karakterlánc található a **csatlakozhat a virtuális gép** párbeszédpanel bezárásához. Használja ezt a kapcsolati karakterláncot egy SSH-terminálból (például [Putty](https://www.putty.org/)) a virtuális gép csatlakozni. 

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Rendszergazdaként hozzon létre, és tesztkörnyezetfiókok kezelése](how-to-manage-lab-accounts.md)
- [Labortulajdonosként hozzon létre és laborok kezelése](how-to-manage-classroom-labs.md)
- [Labortulajdonosként állítsa be, és a sablonok közzététele](how-to-create-manage-template.md)
- [Labor felhasználóként osztályterem-tesztkörnyezetek elérése](how-to-use-classroom-lab.md)

