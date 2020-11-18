---
title: Windows Stop Error – a 0x0000007E rendszerszál-kivétel nincs kezelve
description: Ez a cikk olyan problémák megoldását ismerteti, amelyekben a vendég operációs rendszer problémát észlel, és újraindítja az Azure-beli virtuális gépet. Az üzenet azt fogja kimutatni, hogy "rendszerszál-kivételt nem kezeltek".
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/04/2020
ms.author: v-mibufo
ms.openlocfilehash: 1ce594d9e3ffddf781c61717ae4534f0c7bd40f8
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681890"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Windows Stop Error – a 0x0000007E rendszerszál-kivétel nincs kezelve

Ez a cikk olyan problémák megoldását ismerteti, amelyekben a vendég operációs rendszer (vendég operációs rendszer) problémát észlel, és megkísérli újraindítani az Azure-beli virtuális gépet (VM). A megjelenített hibaüzenet a következő: "A rendszerszál kivétele nem lett kezelve."

## <a name="symptoms"></a>Hibajelenségek

Amikor [rendszerindítási diagnosztika](./boot-diagnostics.md) használatával tekinti meg a virtuális gép kimenetének képernyőképét, látni fogja, hogy a Windows rendszernek újra kell indítania a "rendszerszál-kivétel nem kezelt" kódot vagy a "0x0000007E" hibakódot.

![Képernyőfelvétel: a Windows elakadt a rendszerindítás során a "számítógépe egy problémába ütközött, és újra kell indítani. A rendszer újraindul. " hibaüzenet és a "rendszerszál-kivétel nem kezelt" kód leállítása.](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>Ok

Az ok nem határozható meg, amíg meg nem történik a memóriakép-fájl elemzése. Folytassa a memóriakép fájljának összegyűjtését.

## <a name="solution"></a>Megoldás

A probléma megoldásához először össze kell gyűjtenie az összeomlás memóriakép-fájlját, majd el kell küldenie a fájlt a Microsoft támogatási szolgálatának. A memóriakép-fájl összegyűjtéséhez kövesse a következő két szakasz utasításait.

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
> [Azure-beli virtuális gépek rendszerindítási hibáinak elhárítása](./boot-error-troubleshoot.md)
