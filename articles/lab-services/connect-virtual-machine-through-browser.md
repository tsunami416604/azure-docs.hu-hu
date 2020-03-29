---
title: Csatlakozás virtuális gépekhez böngészőn keresztül - Azure | Microsoft dokumentumok
description: Ismerje meg, hogyan csatlakozhat a virtuális gépekhez egy böngészőn keresztül.
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
ms.date: 12/09/2019
ms.author: takamath
ms.openlocfilehash: 4549ee0f50c06040b70307c96b5ca96cd4162214
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974289"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Csatlakozás a virtuális gépekhez böngészőn keresztül 

A DevTest Labs integrálható [az Azure Bastion](https://docs.microsoft.com/azure/bastion/)szolgáltatással, amely lehetővé teszi, hogy egy böngészőn keresztül csatlakozzon a virtuális gépekhez. A szolgáltatás DevTest Labs ben való engedélyezéséről a [Böngészőkapcsolat engedélyezése laboralapú virtuális gépeken](enable-browser-connection-lab-virtual-machines.md)című témakörben talál további információt.

Ha a *böngészőcsatlakozás* engedélyezve van, a laborfelhasználók böngészőn keresztül érhetik el a virtuális gépeket.  

## <a name="create-a-lab-virtual-machine"></a>Labor virtuális gép létrehozása

Először létre kell hoznia a tesztkörnyezet virtuális gép egy virtuális hálózaton belül, amely bástya konfigurálva van. A létrehozott második **alhálózatot** válassza ki, ne az AzureBastionSubnet-et. A virtuális gépek létrehozása során a Speciális beállítások lapon választhat ki egy virtuális **hálózatot.**

![Virtuális gép létrehozása](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Virtuális gép indítása böngészőben

A virtuális gép létrehozása után elindíthatja azt a böngészőben, ha a *Böngésző csatlakozás* gombjára kattint, és megadja a számítógép felhasználónevét és jelszavát.  

![Indítás böngészőben](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Következő lépések

[Virtuális gép hozzáadása egy laborhoz az Azure DevTest Labsben](devtest-lab-add-vm.md)
