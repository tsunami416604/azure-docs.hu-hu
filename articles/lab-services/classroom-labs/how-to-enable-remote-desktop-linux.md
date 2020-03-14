---
title: A távoli asztali Linux használatának engedélyezése a Azure Lab Servicesban | Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti a Távoli asztalt a Linux rendszerű virtuális gépek számára a Azure Lab Servicesban található laborban.
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: cb9a3e2b9ddcd0f74bfa4978f0bc3f4eb0688257
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270861"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>A távoli asztal használatának engedélyezése a Linux rendszerű virtuális gépek számára a Azure Lab Services tesztkörnyezetben
Ez a cikk bemutatja, hogyan végezheti el a következő feladatokat:

- Távoli asztal engedélyezése Linux rendszerű virtuális géphez
- Hogyan csatlakozhat a tanár a sablon virtuális géphez Távoli asztali kapcsolat (RDP) használatával.

## <a name="enable-remote-desktop-for-linux-vm"></a>Távoli asztal engedélyezése Linux rendszerű virtuális géphez
A laborok létrehozása során a tanárok a **Linux** -lemezképek **Távoli asztali kapcsolatát** is engedélyezhetik. A **Távoli asztali kapcsolat engedélyezése** beállítás akkor jelenik meg, ha a sablonhoz Linux-rendszerkép van kiválasztva. Ha ez a beállítás engedélyezve van, a tanárok RDP-kapcsolaton keresztül csatlakozhatnak a sablonhoz a virtuális gépekhez és a tanuló virtuális gépekhez (Távoli asztal). 

![Távoli asztali kapcsolat engedélyezése Linux-rendszerképekhez](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

A **Távoli asztali kapcsolati üzenet engedélyezése** párbeszédpanelen válassza a **Folytatás a távoli asztal**lehetőséget. 

![Távoli asztali kapcsolat engedélyezése Linux-rendszerképekhez](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> A **Távoli asztali kapcsolat** engedélyezése csak a Linux rendszerű gépeken futó **RDP** -portot nyitja meg. Ha az RDP már telepítve van és konfigurálva van a virtuálisgép-rendszerképben (például Ubuntu Data Science Virtual Machine-rendszerkép), akkor a tanulók RDP-kapcsolaton keresztül is csatlakozhatnak a virtuális gépekhez anélkül, hogy további lépéseket kellene megadniuk.
> 
> Ha a virtuálisgép-rendszerképben nincs telepítve és konfigurálva az RDP, először az SSH-val kell csatlakoznia a Linux rendszerű géphez, és telepítenie kell az RDP-és GUI-csomagokat, hogy a tanulók később is csatlakozhassanak a Linux-számítógéphez. További információkért lásd: [Távoli asztal telepítése és konfigurálása egy linuxos virtuális géphez való kapcsolódáshoz az Azure-ban](../../virtual-machines/linux/use-remote-desktop.md). Ezt követően közzéteheti a képet, hogy a tanulók RDP-be tudják állítani a tanulói Linux virtuális gépeket. 

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
A távoli asztali kapcsolat jelenleg a következő operációs rendszereken támogatott:

- openSUSE LEAP 42,3
- CentOS-alapú 7,5
- Debian 9 "stretch"
- Ubuntu Server 16,04 LTS

## <a name="connect-to-the-template-vm"></a>Csatlakozás a virtuálisgép-sablonhoz 
A tanároknak először SSH-val kell csatlakozniuk a sablon virtuális géphez, és telepíteniük kell az RDP-és GUI-csomagokat. Ezután a tanárok RDP használatával csatlakozhatnak a sablon virtuális géphez: 

1. Ha a **sablon testreszabása** elemet látja az eszköztáron, válassza ki azt. Ezután válassza a **Continue (folytatás** ) lehetőséget a **sablon testreszabása** párbeszédpanelen. Ez a művelet elindítja a sablon virtuális gépet.  

    ![Sablon testreszabása](../media/how-to-enable-remote-desktop-linux/customize-template.png)
2. A sablon virtuális gépe elindítása után kiválaszthatja a **sablon összekapcsolása** lehetőséget, majd az eszköztáron **keresztül csatlakozhat SSH-n keresztül** . 

    ![Kapcsolódás sablonhoz RDP-kapcsolaton keresztül a tesztkörnyezet létrehozása után](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
3. Megjelenik a következő **Kapcsolódás a virtuális géphez** párbeszédpanel. Kattintson a szövegmező melletti **Másolás** gombra a vágólapra másoláshoz. Mentse az SSH-kapcsolatok sztringjét. Használja ezt a kapcsolati karakterláncot egy SSH-terminálról (például a [Putty](https://www.putty.org/)-ból) a virtuális géphez való kapcsolódáshoz.
 
    ![SSH-kapcsolatok karakterlánca](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)
4. RDP-és GUI-csomagok telepítése, hogy a tanulók a Linux rendszerű gépeket később RDP-kapcsolaton keresztül csatlakozhassanak. További információkért lásd: [Távoli asztal telepítése és konfigurálása egy linuxos virtuális géphez való kapcsolódáshoz az Azure-ban](../../virtual-machines/linux/use-remote-desktop.md). Ezt követően közzéteheti a képet, hogy a tanulók RDP-be tudják állítani a tanulói Linux virtuális gépeket.
5. A csomagok telepítése után a **Kapcsolódás sablonhoz** lehetőséget használhatja az eszköztáron, majd a **Kapcsolódás RDP** -kapcsolaton keresztül lehetőségre kattintva csatlakozhat a sablon virtuális géphez RDP-kapcsolaton keresztül. Mentse az RDP-fájlt, és használja arra, hogy RDP-kapcsolaton keresztül csatlakozzon a sablon virtuális géphez. 

## <a name="next-steps"></a>Következő lépések
Miután egy oktató engedélyezte a távoli asztali kapcsolat funkciót, a diákok RDP/SSH-n keresztül csatlakozhatnak a virtuális gépekhez. További információ: a [Távoli asztal használata Linux rendszerű virtuális gépekhez egy osztályterem laborban](how-to-use-remote-desktop-linux-student.md). 