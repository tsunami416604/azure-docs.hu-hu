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
ms.openlocfilehash: 8ccad0698ea6560dd183cacc71f5f3a644e8220c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588105"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>A távoli asztal használata Linux rendszerű virtuális gépekhez a Azure Lab Services tantermi laborjában
Ez a cikk bemutatja, hogyan csatlakozhatnak a tanulók a Linux rendszerű virtuális gépekhez (VM) a laborban RDP/SSH használatával. 

Az oktatónak engedélyeznie kell a távoli asztali kapcsolat funkciót ahhoz, hogy a tanulók csatlakozhasson a tantermi labor virtuális géphez. A távoli asztali kapcsolat szolgáltatás engedélyezésével kapcsolatos útmutatásért lásd: a [Távoli asztal használata Linux rendszerű virtuális gépekhez](how-to-enable-remote-desktop-linux.md).

> [!IMPORTANT] 
> A **Távoli asztali kapcsolat** engedélyezése csak a Linux rendszerű gépeken futó **RDP** -portot nyitja meg. Az oktató csatlakozhat a Linux rendszerű géphez az SSH-val az első alkalommal, és telepítheti az RDP-és GUI-csomagokat, így a későbbiekben RDP-kapcsolaton keresztül csatlakozhat a Linux rendszerű géphez. 

## <a name="connect-to-the-student-vm"></a>Kapcsolódás a tanuló virtuális géphez
A diákok RDP-t telepíthetnek a linuxos virtuális gépekre, miután a labor tulajdonosa (pedagógus) **közzéteszi** a virtuális gépet a GÉPEN telepített RDP-és GUI-csomagokkal. A lépések a következők: 

1. Ha egy tanuló közvetlenül a Labs-portálra jelentkezik be ( `https://labs.azure.com` ) vagy egy regisztrációs hivatkozás használatával ( `https://labs.azure.com/register/<registrationCode>` ), akkor a tanuló által elérhetővé tett laborok csempéje megjelenik. 
2. A csempén állítsa be a gombot a virtuális gép elindításához, ha leállított állapotban van. 
3. Kattintson a **Csatlakozás** gombra. Két lehetőség jelenik meg a virtuális géphez való kapcsolódáshoz: **SSH** és **Távoli asztal**.

    ![Tanulói virtuális gép – kapcsolatok beállításai](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>Kapcsolat SSH vagy RDP használatával
Ha az **SSH** lehetőséget választja, akkor a következő kapcsolódás a **virtuális géphez** párbeszédpanel jelenik meg:  

![SSH-kapcsolatok karakterlánca](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Kattintson a szövegmező melletti **Másolás** gombra a vágólapra másoláshoz. Mentse az SSH-kapcsolatok sztringjét. Használja ezt a kapcsolati karakterláncot egy SSH-terminálról (például a [Putty](https://www.putty.org/)-ból) a virtuális géphez való kapcsolódáshoz.

Ha az **RDP** beállítást választja, a rendszer letölt egy RDP-fájlt a gépre. Mentse, és nyissa meg a számítógéphez való kapcsolódáshoz. 

## <a name="next-steps"></a>További lépések
A Linux rendszerű virtuális gépekhez készült Távoli asztali kapcsolat funkciónak a tantermi laborban való engedélyezésével kapcsolatban lásd: a [Távoli asztal használatának engedélyezése a linuxos virtuális gépek számára](how-to-enable-remote-desktop-linux.md). 

