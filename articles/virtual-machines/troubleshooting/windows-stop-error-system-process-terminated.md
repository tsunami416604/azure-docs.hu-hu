---
title: Windows leállítási hiba állapota rendszerfolyamat megszakítva
description: Ez a cikk olyan problémák megoldását ismerteti, amelyekben az operációs rendszer megtapasztalja a leállítási hiba 0Xc000021a "jelű, ami megakadályozza az Azure-beli virtuális gépek indítását.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 037d0858-4611-4707-bd46-cc8085d011ed
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: v-mibufo
ms.openlocfilehash: b07033f96402edc24edd51de57661603e57472bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91344819"
---
# <a name="windows-stop-error---0xc000021a-status-system-process-terminated"></a>Windows Stop Error – 0Xc000021a "jelű állapot rendszerfolyamata megszakítva

Ez a cikk a hibák elhárításához szükséges lépéseket ismerteti, amelyekben az operációs rendszer (OS) a leállási hiba 0Xc000021a "jelű, amely egy Azure-beli virtuális gépet (VM) tart a rendszerindítás során.

## <a name="symptom"></a>Hibajelenség

Ha [rendszerindítási diagnosztikát](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) használ a virtuális gép képernyőképének megtekintéséhez, a képernyőképen az az üzenet jelenik meg, hogy az operációs rendszer hibát észlelt a rendszerindítás során, a következő üzenettel:

**A számítógép hibát észlelt, és újra kell indítania. Most gyűjtünk néhány hibaüzenetet, majd újraindíthatjuk. (# #% kész) Ha további információra van szüksége, később is megkeresheti a következő hibaüzenetet: 0Xc000021a "jelű**.

  ![Az 1. ábra a hibakódot jeleníti meg, #0xC000021A a "a számítógép egy problémába ütközött, és újra kell indítani. Most gyűjtünk néhány hibaüzenetet, majd újra lehet indítani. "](./media/windows-stop-error-system-process-terminated/1-pc-problem-restart.png)

## <a name="cause"></a>Ok

A 0Xc000021a "jelű hiba azt jelenti, **STATUS_SYSTEM_PROCESS_TERMINATED**.

Ez a hiba akkor fordul elő, ha egy kritikus folyamat, például a WinLogon (winlogon.exe) vagy az ügyfél-kiszolgáló Run-Time alrendszer (csrss.exe) meghiúsul. Ha a kernel észleli, hogy valamelyik szolgáltatás leállt, a **leállítási 0xC000021A "jelű** hibát jelez. Ennek a hibának több oka lehet, többek között a következők:

- Nem egyező rendszerfájlok lettek telepítve.
- Nem sikerült telepíteni A szervizcsomagot vagy a KB-os frissítést.
- A merevlemezek visszaállítására szolgáló biztonságimásolat-készítő program nem tudta megfelelően visszaállítani a használatban lévő fájlokat.
- Nem kompatibilis külső gyártótól származó program lett telepítve.

## <a name="solution"></a>Megoldás

### <a name="collect-the-memory-dump-file"></a>A memóriakép fájljának összegyűjtése

A probléma megoldásához elemezni kell az összeomlási memóriaképet. Gyűjtse össze a memóriakép fájlját az összeomláshoz, és forduljon az ügyfélszolgálathoz. A memóriakép-fájl összegyűjtéséhez kövesse az alábbi lépéseket:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Az operációsrendszer-lemez csatlakoztatása egy új javítási virtuális géphez

1.  A virtuálisgép- [javítási parancsok](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) 1-3-es lépéseit használva készítse elő a javítási virtuális gépet.
2.  A **Távoli asztali kapcsolat**használatával kapcsolódjon a javítási virtuális géphez.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>A memóriaképfájl megkeresése és támogatási jegy beküldése

1.  A javítás virtuális gépen nyissa meg a Windows mappát a csatolt operációsrendszer-lemezen. Ha a csatlakoztatott operációsrendszer-lemezhez hozzárendelt illesztőprogram betűjele F, nyissa meg a F:\Windows.
2.  Keresse meg a Memory. dmp fájlt, majd [küldjön el egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a memóriakép fájljával.
3.  Ha nem sikerül megkeresni a Memory. dmp fájlt, előfordulhat, hogy [nem maszkolt megszakítási (NMI) hívásokat kíván használni a soros konzolon](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) . A vonatkozó útmutató szerint [létrehozhat egy összeomlási memóriaképfájlt az NMI-hívásokkal](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump).

## <a name="next-steps"></a>Következő lépések

- További hibaelhárítási információkért lásd: [gyakori rendszerindítási hibák elhárítása](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-error-troubleshoot) vagy Windows rendszerű [virtuális gép hibaelhárítása az operációsrendszer-lemez egy helyreállítási virtuális géphez való csatolásával](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-windows). Azt is Ismerkedjen meg, [hogyan használható a rendszerindítási diagnosztika a virtuális gépek hibakereséséhez](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics).
- További információ a Resource Manager használatáról: [Azure Resource Manager Overview (áttekintés](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)).
- Ha nem tud csatlakozni a virtuális géphez, tekintse meg [Az Azure-beli virtuális gép RDP-kapcsolatainak hibakeresését](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection)ismertető témakört.
