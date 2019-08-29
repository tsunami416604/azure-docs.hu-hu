---
title: Windows újraindítási hurok egy Azure-beli virtuális gépen | Microsoft Docs
description: Útmutató a Windows újraindítási ciklusának hibakereséséhez | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/15/2018
ms.author: genli
ms.openlocfilehash: e4715225e56e50502348040fa501cbfd76bd5c9f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103384"
---
# <a name="windows-reboot-loop-on-an-azure-vm"></a>Windows újraindítási hurok Azure-beli virtuális gépen
Ez a cikk a Windows rendszerű virtuális gépeken (VM) tapasztalható újraindítási hurkot ismerteti Microsoft Azureban.

## <a name="symptom"></a>Jelenség

Ha rendszerindítási [diagnosztikát](./boot-diagnostics.md) használ egy virtuális gép képernyőképének beszerzéséhez, a rendszer elindítja a virtuális gépet, de a rendszerindítási folyamat megszakad, és a folyamat elindul.

![1\. kezdőképernyő](./media/troubleshoot-reboot-loop/start-screen-1.png)

## <a name="cause"></a>Ok

Az újraindítási hurok a következő okok miatt fordul elő:

### <a name="cause-1"></a>OK: 1

Létezik egy harmadik féltől származó szolgáltatás, amely kritikusként van megjelölve, és nem indítható el. Ez az operációs rendszer újraindítását okozza.

### <a name="cause-2"></a>OK 2

Néhány módosítás történt az operációs rendszeren. Ezek általában a frissítés telepítésével, az alkalmazások telepítésével vagy egy új házirenddel kapcsolatosak. További részletekért tekintse meg a következő naplókat:

- Eseménynaplók
- CBS.logWindows
- Update.log

### <a name="cause-3"></a>3\. ok

A fájlrendszer sérülése ezt okozhatta. Azonban nehéz diagnosztizálni és azonosítani az operációs rendszer sérülését okozó változást.

## <a name="solution"></a>Megoldás

A probléma megoldásához végezze el [az operációsrendszer-lemez biztonsági mentését](../windows/snapshot-copy-managed-disk.md), és [csatlakoztassa az operációsrendszer-lemezt egy mentési virtuális géphez](../windows/troubleshoot-recovery-disks-portal.md), majd kövesse a megoldás beállításait, vagy próbálja meg egyenként a megoldásokat.

### <a name="solution-for-cause-1"></a>Megoldás az 1. ok esetén

1. Miután az operációsrendszer-lemez csatlakoztatva van egy működő virtuális géphez, ellenőrizze, hogy a lemez **online** állapotban van-e megjelölve a Lemezkezelés konzolon, és jegyezze fel a **\Windows** mappát tároló partíció meghajtóbetűjelét.

2. Ha a lemez **Offline**értékre van beállítva, akkor állítsa **online**állapotba.

3. Hozzon létre egy másolatot a **\Windows\System32\config** mappából abban az esetben, ha a módosítások visszaállítására van szükség.

4. A mentési virtuális gépen nyissa meg a Windows rendszerleíróadatbázis-szerkesztőt (Regedit).

5. Válassza ki a **HKEY_LOCAL_MACHINE** kulcsot, majd válassza a **fájl** > **betöltése struktúra** lehetőséget a menüből.

6. Keresse meg a rendszerfájlt a **\Windows\System32\config** mappában.

7. Válassza a **Megnyitás**elemet, írja be a **BROKENSYSTEM** nevet, bontsa ki a **HKEY_LOCAL_MACHINE** kulcsot, majd megjelenik egy **BROKENSYSTEM**nevű további kulcs.

8. Győződjön meg arról, hogy a számítógép melyik ControlSet indul el. A kulcs számát a következő beállításkulcs fogja látni.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\Select\Current`

9. Győződjön meg arról, hogy a virtuálisgép-ügynök szolgáltatás kritikus fontosságú a következő beállításkulcs használatával.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\RDAgent\ErrorControl`

10. Ha a beállításkulcs értéke nem **2**, akkor folytassa a következő enyhítéssel.

11. Ha a beállításkulcs értéke **2**, akkor módosítsa az értéket **2** – **1**értékre.

12. Ha a következő kulcsok bármelyike létezik, és a **2** . vagy **3**. értékkel rendelkezik, majd módosítsa ezeket az értékeket **1** ennek megfelelően:

    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupCoordinatorSvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupInquirySvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupPluginSvc\ErrorControl`

13. Válassza ki a **BROKENSYSTEM** kulcsot, majd válassza a **fájl** > **betöltése struktúra** lehetőséget a menüből.

14. Válassza le az operációsrendszer-lemezt a hibaelhárítási virtuális gépről.

15. Távolítsa el a lemezt a hibaelhárítási virtuális gépről, és várjon 2 percet, amíg az Azure kiadja ezt a lemezt.

16. [Hozzon létre egy új virtuális gépet az operációsrendszer-lemezről](../windows/create-vm-specialized.md).

17. Ha a probléma kijavítva van, előfordulhat, hogy újra kell telepítenie a [RDAgent](https://blogs.msdn.microsoft.com/mast/2014/04/07/install-the-vm-agent-on-an-existing-azure-vm/) (WaAppAgent. exe).

### <a name="solution-for-cause-2"></a>Megoldás a 2. ok esetén

Állítsa vissza a virtuális gépet az utolsó ismert helyes konfigurációra, kövesse az [Azure Windows rendszerű virtuális gép indítása az utolsó ismert jó konfigurációval](https://support.microsoft.com/help/4016731/)című témakör lépéseit.

### <a name="solution-for-cause-3"></a>3\. ok megoldás
>[!NOTE]
>A következő eljárást csak utolsó erőforrásként kell használni. Míg a Regback-re való visszaállítás visszaállíthatja a számítógép elérését, az operációs rendszer nem tekinthető stabilnak, mivel a beállításjegyzékben a struktúra és az aktuális nap időbélyege között elveszett adatok. Létre kell hoznia egy új virtuális gépet, és el kell végeznie az adatmigrálás terveit.

1. Miután a lemez csatlakoztatva van egy hibaelhárítási virtuális géphez, ellenőrizze, hogy a lemez **online** állapotban van-e megjelölve a Lemezkezelés konzolon.

2. Hozzon létre egy másolatot a **\Windows\System32\config** mappából abban az esetben, ha a módosítások visszaállítására van szükség.

3. Másolja a fájlokat a **\Windows\System32\config\regback** mappába, és cserélje le a fájlokat a **\Windows\System32\config** mappába.

4. Távolítsa el a lemezt a hibaelhárítási virtuális gépről, és várjon 2 percet, amíg az Azure kiadja ezt a lemezt.

5. [Hozzon létre egy új virtuális gépet az operációsrendszer-lemezről](../windows/create-vm-specialized.md).


