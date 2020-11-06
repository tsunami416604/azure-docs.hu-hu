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
ms.openlocfilehash: dd18d69009b9c150c4c12a755e9060cd5dfaccae
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424231"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Windows Stop Error – a 0x0000007E rendszerszál-kivétel nincs kezelve

Ez a cikk olyan problémák megoldását ismerteti, amelyekben a vendég operációs rendszer problémát észlel, és újraindítja az Azure-beli virtuális gépet. Az üzenet azt fogja kimutatni, hogy "rendszerszál-kivételt nem kezeltek".

## <a name="symptoms"></a>Hibajelenségek

Ha [rendszerindítási diagnosztikát](./boot-diagnostics.md) használ a virtuális gép képernyőképének megtekintéséhez, látni fogja, hogy a képernyőképen a Windows újraindítására van szükség a leállítási kód **rendszerszála kivétel nélkül** vagy a hibakód **0x0000007E**.

![A képernyőképen a Windows ragadt a rendszerindítás során a következő üzenet jelenik meg: "a SZÁMÍTÓGÉPe egy problémába ütközött, és újra kell indítani. A rendszer újraindul. " Kód leállítása: "a rendszerszál KIVÉTELe nem kezelt"](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>Ok

Az ok nem határozható meg, amíg meg nem történik a memóriakép-fájl elemzése. Folytassa a memóriakép fájljának összegyűjtését.

## <a name="solution"></a>Megoldás

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
> [Azure-beli virtuális gépek rendszerindítási hibáinak elhárítása](./boot-error-troubleshoot.md)