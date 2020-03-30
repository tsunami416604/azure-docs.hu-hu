---
title: Távoli asztal használata Linuxhoz az Azure Lab Services ben | Microsoft dokumentumok
description: Ismerje meg, hogyan használhatja a távoli asztal linuxos virtuális gépek egy laborban az Azure Lab Services.
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
ms.openlocfilehash: 0f45af2730b05998fc82212c63778c89bb16b6ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73585067"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Távoli asztali számítógép használata Linuxos virtuális gépekhez az Azure Lab Services tantermi laborjában
Ez a cikk bemutatja, hogyan csatlakozhatnak a diákok egy Linux virtuális géphez (VM) egy laborban az RDP/SSH használatával. 

Az oktatónak engedélyeznie kell a távoli asztali kapcsolat funkciót, mielőtt a diákok csatlakozhatnának az osztálytermi labor virtuális gépéhez. A távoli asztali kapcsolat funkciójának engedélyezésével kapcsolatos tudnivalókról a [Távoli asztal linuxos virtuális gépekhez való engedélyezése című témakörben találhatók.](how-to-enable-remote-desktop-linux.md)

> [!IMPORTANT] 
> A **távoli asztali kapcsolat** engedélyezése csak linuxos gépeken nyitja meg az **RDP-portot.** Az oktató először csatlakozhat a Linux-géphez SSH használatával, és rdp- és GUI-csomagokat telepíthet, hogy később rdp használatával csatlakozhasson a Linux-géphez. 

## <a name="connect-to-the-student-vm"></a>Csatlakozás a tanuló virtuális géphez
A diákok rdp-ben a Linux-virtuális gépek után a labor tulajdonosa (tanár/professzor) **közzéteszi** a sablon virtuális gép RDP és GUI csomagok telepítve a gépen. A lépések a következők: 

1. Amikor egy diák közvetlenül bejelentkezik`https://labs.azure.com`a Labs portálra`https://labs.azure.com/register/<registrationCode>`( ) vagy regisztrációs link ( használatával), megjelenik egy csempe minden olyan laborhoz, amelyhez a tanuló hozzáférhet. 
2. A csempén kapcsolja be a gombot a virtuális gép elindításához, ha leállított állapotban van. 
3. Kattintson a **Csatlakozás** gombra. A virtuális géphez való csatlakozáskét lehetősége látható: **az SSH** és a **Távoli asztal**.

    ![Student VM - csatlakozási lehetőségek](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>Csatlakozás SSH vagy RDP használatával
Ha az **SSH** lehetőséget választja, a következő **Csatlakozás a virtuális géphez** párbeszédpanel jelenik meg:  

![SSH kapcsolati karakterlánc](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

A szövegdoboz melletti **Másolás** gombra kattintva másolja azt a vágólapra. Mentse az SSH kapcsolati karakterláncot. Használja ezt a kapcsolati karakterláncot egy SSH terminálról (például [a Putty)-ról](https://www.putty.org/)a virtuális géphez való csatlakozáshoz.

Ha az **RDP** lehetőséget választja, a rendszer egy RDP-fájlt tölt le a számítógépre. Mentse el és nyissa meg a készülékhez való csatlakozáshoz. 

## <a name="next-steps"></a>További lépések
A Linuxos virtuális gépek távoli asztali csatlakozási funkciójának tantermi laborban történő engedélyezéséről a [Távoli asztal linuxos virtuális gépekhez való engedélyezése](how-to-enable-remote-desktop-linux.md)című témakörben olvashat. 

