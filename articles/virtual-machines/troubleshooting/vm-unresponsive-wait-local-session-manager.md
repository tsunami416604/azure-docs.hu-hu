---
title: A virtuális gép nem válaszol a helyi munkamenet-kezelő szolgáltatásra való várakozás közben
description: Ez a cikk olyan problémák megoldását ismerteti, amelyekben a vendég operációs rendszer beragadt a helyi munkamenet-kezelőnek a feldolgozás befejezéséhez, miközben Azure-beli virtuális gépet indít el.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/22/2020
ms.author: v-mibufo
ms.openlocfilehash: fc3bd5d2590e969db07e9dffa61b4902ea4604c3
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632546"
---
# <a name="vm-is-unresponsive-while-waiting-for-the-local-session-manager-service"></a>A virtuális gép nem válaszol a helyi munkamenet-kezelő szolgáltatásra való várakozás közben

Ez a cikk olyan problémák megoldásához nyújt útmutatást, amelyekben a vendég operációs rendszer (vendég operációs rendszer) beragadt a helyi munkamenet-kezelőnek a feldolgozás befejezésére, miközben az Azure-beli virtuális gépeket (VM) indítja.

## <a name="symptoms"></a>Hibajelenségek

Ha [rendszerindítási diagnosztika](./boot-diagnostics.md) használatával tekinti meg a virtuális gép kimenetének képernyőképét, láthatja, hogy a képernyőképen a "kis türelmet a helyi munkamenet-kezelő" üzenet jelenik meg.

![Képernyőfelvétel: a Windows Server 2012 R2 beragadt vendég operációs rendszerének a "kis türelmet a helyi munkamenet-kezelőhöz" üzenet jelenik meg.](media/vm-unresponsive-wait-local-session-manager/vm-unresponsive-wait-local-session-manager-1.png)

## <a name="cause"></a>Ok

Több oka lehet annak, hogy a virtuális gép a helyi munkamenet-kezelőre várakozik. Ha a probléma továbbra is fennáll, össze kell gyűjtenie egy memóriaképet az elemzéshez.

## <a name="solution"></a>Megoldás

> [!TIP]
> Ha a virtuális gép nemrég készült biztonsági másolattal rendelkezik, a rendszerindítási probléma megoldásához próbálja meg [visszaállítani a virtuális gépet a biztonsági mentésből](../../backup/backup-azure-arm-restore-vms.md) .

Bizonyos esetekben egyszerűen csak arra vár, hogy a befejezési folyamat megoldja a problémát. Ha a virtuális gép nem válaszol, és a várakozási képernyőn több mint egy óra alatt marad, akkor Gyűjtse össze a memóriaképet, és forduljon a Microsoft ügyfélszolgálatához.

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Az operációsrendszer-lemez csatlakoztatása egy új javítási virtuális géphez

1. A javítási virtuális gép előkészítéséhez kövesse a [virtuális gép javítási parancsainak](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)1-3. lépéseit.
1. Kapcsolódjon a javítási virtuális géphez Távoli asztali kapcsolat használatával.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>A memóriaképfájl megkeresése és támogatási jegy beküldése

1. A virtuális gép javítása területen lépjen a csatlakoztatott operációsrendszer-lemez Windows mappájába. Ha például a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjel *F*, ugorjon a következőre: `F:\Windows` .
1. Keresse meg a *Memory. dmp* fájlt, majd [küldjön el egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a csatolt memóriakép-fájllal.
1. Ha nem sikerül a *Memory. dmp* fájl megkeresése, kövesse az útmutatóban [egy összeomlási memóriakép fájljának előállításához nem maszkolásos megszakítás (NMI) hívásokat](/windows/client-management/generate-kernel-or-complete-crash-dump).

További információ a NMI-hívásokról: [NMI-hívások az Azure soros konzol](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) felhasználói útmutatójában.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure-beli virtuális gépek rendszerindítási hibáinak elhárítása](boot-error-troubleshoot.md)