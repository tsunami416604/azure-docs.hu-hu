---
title: Kapcsolódás a virtuális gépekhez egy böngészőben – Azure | Microsoft Docs
description: Megtudhatja, hogyan csatlakozhat a virtuális gépekhez egy böngészőben.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 1bb5d979ff7c9fe6e2afec4c7ebd81649b4488e3
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581232"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Kapcsolódás a virtuális gépekhez egy böngészőben 

A DevTest Labs integrálható az [Azure Bastion](https://docs.microsoft.com/azure/bastion/)szolgáltatással, amely lehetővé teszi a virtuális gépekhez való kapcsolódást egy böngészőben. A funkció a DevTest Labs szolgáltatásban való engedélyezésével kapcsolatos további információkért lásd: [a böngészőalapú kapcsolatok engedélyezése a labor virtuális gépeken](enable-browser-connection-lab-virtual-machines.md).

Ha a *böngésző kapcsolódása* engedélyezve van, a labor-felhasználók egy böngészőben érhetik el a virtuális gépeket.  


## <a name="create-a-lab-virtual-machine"></a>Tesztkörnyezet virtuális gép létrehozása

Először létre kell hoznia a labor virtuális gépet egy olyan VNet belül, amelyen a Bastion konfigurálva van. A virtuális gépek létrehozásakor a **Speciális beállítások** lapon választhatja ki a VNet.

![Virtuális gép létrehozása](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Virtuális gép elindítása böngészőben

A virtuális gép létrehozása után elindíthatja azt egy böngészőben, ha a *böngésző csatlakoztatva* gombra kattint, és beírja a számítógép felhasználónevét és jelszavát.  

![Indítás böngészőben](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>További lépések

[Virtuális gép hozzáadása laborhoz Azure DevTest Labs](devtest-lab-add-vm.md)
