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
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: 562ad0e5e6088c2fbadc853779b7533c5398e079
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2019
ms.locfileid: "71176650"
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
> A **Távoli asztali kapcsolat** engedélyezése csak a Linux rendszerű gépeken futó **RDP** -portot nyitja meg. Ha az RDP már telepítve van és konfigurálva van a virtuális gép rendszerképén (például: Ubuntu Data Science Virtual Machine-rendszerkép) a tanulók a virtuális gépeket RDP-kapcsolaton keresztül is csatlakozhatnak a további lépések követése nélkül.
> Ha a virtuálisgép-rendszerképben nincs telepítve és konfigurálva az RDP, először az SSH-val kell csatlakoznia a Linux rendszerű géphez, és telepítenie kell az RDP-és GUI-csomagokat, hogy a tanulók később is csatlakozhassanak a Linux-számítógéphez. Ezt követően közzéteheti a képet, hogy a tanulók RDP-be tudják állítani a tanulói Linux virtuális gépeket.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
A távoli asztali kapcsolat jelenleg a következő operációs rendszereken támogatott:

- openSUSE LEAP 42,3
- CentOS-alapú 7,5
- Debian 9 "stretch"
- Ubuntu Server 16,04 LTS

## <a name="teachers-connecting-to-the-template-vm-using-rdp"></a>Az RDP használatával a sablon virtuális géphez csatlakozó tanárok
A tanároknak először SSH-val kell csatlakozniuk a sablon virtuális géphez, és telepíteniük kell az RDP-és GUI-csomagokat. Ezután a tanárok a következő lépésekkel csatlakozhatnak a linuxos virtuális gépekhez RDP használatával: 

A sablon virtuális géphez való kapcsolódáshoz **Távoli asztal** lehetőség jelenik meg a labor létrehozásakor. 

![Kapcsolódás sablonhoz RDP használatával a létrehozás időpontjában](../media/how-to-enable-remote-desktop-linux/connect-at-creation.png)

A labor kezdőlapján megjelenik a **Távoli asztal** lehetőség a tesztkörnyezet létrehozása után, a sablon virtuális gépe pedig elindult. Ha még nem indult el, indítsa el a sablon virtuális gépet. 

![Kapcsolódás sablonhoz RDP-kapcsolaton keresztül a tesztkörnyezet létrehozása után](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 

A virtuális géphez SSH vagy RDP használatával történő csatlakozásról további információért lásd: [csatlakozás SSH vagy RDP használatával] ((#connect-SSH-vagy-RDP-kapcsolat). 

## <a name="teachers-connecting-to-a-student-vm-using-rdp"></a>Tanuló virtuális géphez RDP használatával csatlakozó tanárok
A tanár/professzor csatlakozhat egy tanuló virtuális géphez, ha átvált a **Virtual Machines** nézetre, és kiválasztja a **Kapcsolódás** ikont. Ezt megelőzően a tanároknak **közzé kell tenniük** a sablon rendszerképét az arra telepített RDP-és GUI-csomagokkal. 

![Tanuló virtuális géphez csatlakozó tanárok](../media/how-to-enable-remote-desktop-linux/teacher-connect-to-student-vm.png)

A virtuális géphez SSH vagy RDP használatával történő csatlakozásról további információért lásd: [csatlakozás SSH vagy RDP használatával] ((#connect-SSH-vagy-RDP-kapcsolat). 

## <a name="connect-using-ssh-or-rdp"></a>Kapcsolat SSH vagy RDP használatával
Ha az **SSH** lehetőséget választja, akkor a következő kapcsolódás a **virtuális géphez** párbeszédpanel jelenik meg:  

![SSH-kapcsolatok karakterlánca](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Kattintson a szövegmező melletti **Másolás** gombra a vágólapra másoláshoz. Mentse az SSH-kapcsolatok sztringjét. Használja ezt a kapcsolati karakterláncot egy SSH-terminálról (például a [Putty](https://www.putty.org/)-ból) a virtuális géphez való kapcsolódáshoz.

Ha az **RDP** beállítást választja, a rendszer letölt egy RDP-fájlt a gépre. Mentse, és nyissa meg a számítógéphez való kapcsolódáshoz. 

## <a name="next-steps"></a>További lépések
Miután egy oktató engedélyezte a távoli asztali kapcsolat funkciót, a diákok RDP/SSH-n keresztül csatlakozhatnak a virtuális gépekhez. További információ: a [Távoli asztal használata Linux rendszerű virtuális gépekhez egy osztályterem laborban](how-to-use-remote-desktop-linux-student.md). 