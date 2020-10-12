---
title: Az Azure-beli virtuális gép nem válaszol, miközben biztonsági házirendet alkalmaz a rendszerre.
description: Ez a cikk azokat a problémákat ismerteti, amelyekkel elháríthatja a betöltési képernyőt, ha a virtuális gép nem válaszol, amikor biztonsági házirendet alkalmaz egy Azure-beli virtuális gépen lévő rendszerre.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5629
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.author: v-mibufo
ms.openlocfilehash: 6b50bffd1a44c0cf53f15650f5ff4d938f45df4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84908195"
---
# <a name="azure-vm-is-unresponsive-while-applying-security-policy-to-the-system"></a>Az Azure-beli virtuális gép nem válaszol, miközben biztonsági házirendet alkalmaz a rendszerre.

Ez a cikk azokat a problémákat ismerteti, amelyekkel az operációs rendszer leáll, és nem válaszol, miközben egy Azure-beli virtuális gépen alkalmazza a biztonsági házirendet.

## <a name="symptoms"></a>Hibajelenségek

Ha [rendszerindítási diagnosztika](boot-diagnostics.md) használatával tekinti meg a virtuális gép képernyőképét, látni fogja, hogy a képernyőképen az operációs rendszer elakadt az üzenet indításakor:

> "Biztonsági házirend alkalmazása a rendszeren".

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy.png" alt-text="A Windows Server 2012 R2 indítási képernyőjének képernyőképe megakadt.":::

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy-2.png" alt-text="A Windows Server 2012 R2 indítási képernyőjének képernyőképe megakadt.":::

## <a name="cause"></a>Ok

A probléma számos lehetséges oka lehet. Addig nem fogja tudni a forrást, amíg a memóriakép elemzése nem történik meg.

## <a name="resolution"></a>Feloldás

### <a name="process-overview"></a>Folyamat áttekintése

1. [Javítási virtuális gép létrehozása és elérése](#create-and-access-a-repair-vm)
2. [Soros konzol és memóriakép-gyűjtemény engedélyezése](#enable-serial-console-and-memory-dump-collection)
3. [A virtuális gép újraépítése](#rebuild-the-vm)
4. [A memóriakép fájljának összegyűjtése](#collect-the-memory-dump-file)

### <a name="create-and-access-a-repair-vm"></a>Javítási virtuális gép létrehozása és elérése

1. A virtuálisgép- [javítási parancsok 1-3-es lépéseit](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) használva készítse elő a javítási virtuális gépet.
2. Távoli asztali kapcsolat kapcsolódjon a javítási virtuális géphez.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Soros konzol és memóriakép-gyűjtemény engedélyezése

A memóriakép-gyűjtés és a soros konzol engedélyezéséhez futtassa a következő parancsfájlt:

1. Nyisson meg egy rendszergazda jogú parancssor-munkamenetet (Futtatás rendszergazdaként).
2. Sorolja fel a BCD-tároló adatait, és határozza meg a rendszerindítási betöltő azonosítóját, amelyet a következő lépésben fog használni.

     1. 1. generációs virtuális gépek esetén írja be a következő parancsot, és jegyezze fel a felsorolt azonosítót:

        ```console
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```

        A parancsban cserélje le a \<BOOT PARTITON> betűt a partíció betűjelére a csatlakoztatott lemezen, amely tartalmazza a rendszerindító mappát.

        :::image type="content" source="media/unresponsive-vm-apply-security-policy/store-data.png" alt-text="A Windows Server 2012 R2 indítási képernyőjének képernyőképe megakadt." /v NMICrashDump /t REG_DWORD /d 1 /f
        ```

        Sérült operációsrendszer-lemez eltávolítása:

        ```console
        REG UNLOAD HKLM\BROKENSYSTEM
        ```

### <a name="rebuild-the-vm"></a>A virtuális gép újraépítése

A virtuális gép újraösszeállításához használja [a virtuális gép javítási parancsainak 5. lépését](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .

### <a name="collect-the-memory-dump-file"></a>A memóriakép fájljának összegyűjtése

A probléma megoldásához először össze kell gyűjtenie az összeomláshoz tartozó memóriakép-fájlt, és kapcsolatba kell lépnie az ügyfélszolgálattal a memóriakép fájljával. A memóriakép-fájl összegyűjtéséhez kövesse az alábbi lépéseket:

1. Csatlakoztassa az operációsrendszer-lemezt egy új javítási virtuális géphez:

    - Egy új javítási virtuális gép előkészítéséhez használja [a virtuális gép javítási parancsainak 1-3. lépéseit](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .
    - Távoli asztali kapcsolat kapcsolódjon a javítási virtuális géphez.

2. Keresse meg a memóriaképfájlt, és küldjön egy támogatási jegyet:

    - A javítás virtuális gépen nyissa meg a Windows mappát a csatolt operációsrendszer-lemezen. Ha a csatolt operációsrendszer-lemez meghajtóbetűjele az `F`, akkor lépjen ide: `F:\Windows`.
    - Keresse meg a Memory. dmp fájlt, majd [küldjön el egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a memóriakép fájljával.
    - Ha nem sikerül megkeresni a Memory. dmp fájlt, előfordulhat, hogy [nem maszkolt megszakítási (NMI) hívásokat kíván használni a soros konzolon](serial-console-windows.md#use-the-serial-console-for-nmi-calls) . Az [összeomlási memóriakép fájljának NMI-hívásokkal történő létrehozásához](/windows/client-management/generate-kernel-or-complete-crash-dump)kövesse az útmutatót.

## <a name="next-steps"></a>További lépések

Ha problémák merülnek fel a helyi felhasználók és csoportok alkalmazása során, tekintse meg [a virtuális gép nem válaszol csoportházirend helyi felhasználók és csoportok házirend alkalmazásakor](unresponsive-vm-apply-group-policy.md)