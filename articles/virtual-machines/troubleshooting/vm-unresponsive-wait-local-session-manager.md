---
title: A virtuális gép nem válaszol a helyi munkamenet-kezelőre való várakozás közben
description: Ez a cikk olyan problémák megoldását ismerteti, amelyekben a vendég operációs rendszer beragadt, miközben a helyi munkamenet-kezelő betölti a feldolgozást egy Azure-beli virtuális gép indításakor.
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
ms.openlocfilehash: f2f0177b5fe8bb97773d297319f6c9196d8178d2
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536234"
---
# <a name="vm-is-unresponsive-while-waiting-for-the-local-session-manager"></a>A virtuális gép nem válaszol a helyi munkamenet-kezelőre való várakozás közben

Ez a cikk olyan problémák megoldását ismerteti, amelyekben a vendég operációs rendszer beragadt a helyi munkamenet-kezelőnek a feldolgozás befejezésére az Azure-beli virtuális gépek (VM) indítása során.

## <a name="symptoms"></a>Hibajelenségek

Ha [rendszerindítási diagnosztikát](./boot-diagnostics.md) használ a virtuális gép képernyőképének megtekintéséhez, látni fogja, hogy a képernyőképen megjelenik a következő üzenet: " *várjon a helyi munkamenet-kezelőre* "

![A képernyőképen látható, hogy a vendég operációs rendszer a Windows Server 2012 R2-ben a "Várakozás a helyi munkamenet-kezelőhöz" üzenettel együtt beragadt.](media/vm-unresponsive-wait-local-session-manager/vm-unresponsive-wait-local-session-manager-1.png)

## <a name="cause"></a>Ok

Több oka is lehet annak, hogy a virtuális gép várakozik a helyi munkamenet-kezelőre. Ha a helyi munkamenet-kezelőre való várakozás állandó probléma, az elemzéshez egy memóriaképet kell gyűjtenie.

## <a name="solution"></a>Megoldás

Bizonyos esetekben egyszerűen elég hosszú ideig várni, amíg a folyamat befejeződik, feloldja a problémát. Ha a virtuális gép nem válaszol, és a várakozási képernyőn több mint egy óra alatt marad, akkor Gyűjtse össze a memóriaképet, és forduljon a Microsoft ügyfélszolgálatához.

### <a name="collect-the-memory-dump-file"></a>A memóriakép fájljának összegyűjtése

A probléma megoldásához először össze kell gyűjtenie az összeomlás memóriakép-fájlját, majd az ügyfélszolgálathoz kell fordulnia a memóriakép fájljával. A memóriakép-fájl összegyűjtéséhez kövesse az alábbi lépéseket:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Az operációsrendszer-lemez csatlakoztatása egy új javítási virtuális géphez

1. A javítási virtuális gép előkészítéséhez kövesse [a virtuális gép javítási parancsainak 1-3. lépéseit](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) .
2. Kapcsolódjon a javítási virtuális géphez Távoli asztali kapcsolat használatával.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>A memóriaképfájl megkeresése és támogatási jegy beküldése

1. A virtuális gép javítása területen lépjen a csatlakoztatott operációsrendszer-lemez Windows mappájába. Ha a csatolt operációsrendszer-lemezhez rendelt illesztőprogram-betűjel *F* -ként van megjelölve, akkor a következőt kell megadnia: `F:\Windows` .
2. Keresse meg a **Memory. dmp** fájlt, majd [küldjön el egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a memóriakép fájljával.
3. Ha nem sikerül a **Memory. dmp** fájl megkeresése, kövesse az útmutatót az [összeomlási memóriakép fájljának a nem maszkolásos megszakítás (NMI) hívásokkal történő létrehozásához](/windows/client-management/generate-kernel-or-complete-crash-dump).

A NMI-hívásokkal kapcsolatos további információkért tekintse meg a soros konzol felhasználói útmutatójában a [nem szerializálható megszakítások (NMI) hívásait](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) .

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure-beli virtuális gépek rendszerindítási hibáinak elhárítása](boot-error-troubleshoot.md)