---
title: A távoli asztal használata Linux rendszeren a Azure Lab Servicesban | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Távoli asztalt a Linux rendszerű virtuális gépekhez a Azure Lab Servicesban található laborban.
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
ms.openlocfilehash: 33b4ed3974c3e4e88e5d74ff31a3b8008ab565e9
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657319"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>A távoli asztal használata Linux rendszerű virtuális gépekhez a Azure Lab Services tantermi laborjában
Ez a cikk bemutatja, hogyan csatlakozhatnak a tanulók a Linux rendszerű virtuális gépekhez (VM) a laborban RDP/SSH használatával. 

Az oktatónak engedélyeznie kell a távoli asztali kapcsolat funkciót ahhoz, hogy a tanulók csatlakozhasson a tantermi labor virtuális géphez. A távoli asztali kapcsolat szolgáltatás engedélyezésével kapcsolatos útmutatásért lásd: a [Távoli asztal használata Linux rendszerű virtuális gépekhez](how-to-enable-remote-desktop-linux.md).

> [!IMPORTANT] 
> A **Távoli asztali kapcsolat** engedélyezése csak a Linux rendszerű gépeken futó **RDP** -portot nyitja meg. Az oktató csatlakozhat a Linux rendszerű géphez az SSH-val az első alkalommal, és telepítheti az RDP-és GUI-csomagokat, így a későbbiekben RDP-kapcsolaton keresztül csatlakozhat a Linux rendszerű géphez. 

## <a name="connect-to-the-student-vm"></a>Kapcsolódás a tanuló virtuális géphez
A tanulók a Linux rendszerű virtuális gépekre is RDP-t, miután a labor tulajdonosa (tanár/professzor) közzéteszi a virtuális gépet a GÉPEN telepített RDP-és GUI-csomagokkal. A lépések a következők: 

1. Ha egy tanuló közvetlenül a Labs-portálra jelentkezik be`https://labs.azure.com`() vagy egy regisztrációs hivatkozás használatával (`https://labs.azure.com/register/<registrationCode>`), akkor a tanuló által elérhetővé tett laborok csempéje megjelenik. 
2. A csempén kattintson a **Start** gombra, ha a virtuális gép le van állítva. 
3. Kattintson a **Csatlakozás** gombra. Két lehetőség jelenik meg a virtuális géphez való kapcsolódáshoz: **SSH** és **Távoli asztal**.

    ![Tanulói virtuális gép – kapcsolatok beállításai](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>Kapcsolat SSH vagy RDP használatával
Ha az **SSH** lehetőséget választja, akkor a következő kapcsolódás a **virtuális géphez** párbeszédpanel jelenik meg:  

![SSH-kapcsolatok karakterlánca](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Kattintson a szövegmező melletti **Másolás** gombra a vágólapra másoláshoz. Mentse az SSH-kapcsolatok sztringjét. Használja ezt a kapcsolati karakterláncot egy SSH-terminálról (például a [Putty](https://www.putty.org/)-ból) a virtuális géphez való kapcsolódáshoz.

Ha az **RDP** beállítást választja, a rendszer letölt egy RDP-fájlt a gépre. Mentse, és nyissa meg a számítógéphez való kapcsolódáshoz. 

## <a name="next-steps"></a>További lépések
A Linux rendszerű virtuális gépekhez készült Távoli asztali kapcsolat funkciónak a tantermi laborban való engedélyezésével kapcsolatban lásd: a [Távoli asztal használatának engedélyezése a linuxos virtuális gépek számára](how-to-enable-remote-desktop-linux.md). 

