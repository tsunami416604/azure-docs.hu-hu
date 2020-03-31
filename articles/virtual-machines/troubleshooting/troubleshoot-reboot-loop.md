---
title: Windows újraindítási ciklus egy Azure virtuális gépen | Microsoft dokumentumok
description: A Windows újraindítási ciklusának hibaelhárítása | Microsoft dokumentumok
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/15/2018
ms.author: genli
ms.openlocfilehash: 3fd0a8bf6bacfec5e2be6dfa52ca51e46c7025f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443582"
---
# <a name="windows-reboot-loop-on-an-azure-vm"></a>Windows újraindítási ciklus egy Azure virtuális gép
Ez a cikk a Windows virtuális gépen (VM) a Microsoft Azure-ban tapasztalt újraindítási ciklust ismerteti.

## <a name="symptom"></a>Hibajelenség

Amikor [a rendszerindítási diagnosztika](./boot-diagnostics.md) segítségével leszeretné szerezni a virtuális gép képernyőképeit, megtalálja a virtuális gép indítását, de a rendszerindítási folyamat megszakad, és a folyamat újrakezdődik.

![Kezdőképernyő 1](./media/troubleshoot-reboot-loop/start-screen-1.png)

## <a name="cause"></a>Ok

Az újraindítási ciklus a következő okok miatt következik be:

### <a name="cause-1"></a>1. ok

Van egy külső szolgáltatás, amely kritikusként van megjelölve, és nem indítható el. Ez az operációs rendszer újraindítását eredményezi.

### <a name="cause-2"></a>2. ok

Néhány változtatás történt az operációs rendszeren. Ezek általában egy frissítés telepítéséhez, alkalmazástelepítéshez vagy új házirendhez kapcsolódnak. Előfordulhat, hogy a további részletekért a következő naplókat kell ellenőriznie:

- Eseménynaplók
- CBS.logWindows
- Update.log

### <a name="cause-3"></a>3. ok

A fájlrendszer sérülése okozhatja ezt. Azonban nehéz diagnosztizálni és azonosítani az operációs rendszer sérülését okozó változást.

## <a name="solution"></a>Megoldás

A probléma megoldásához [biztonsági másolatot készíteni az operációs rendszer lemezén](../windows/snapshot-copy-managed-disk.md), és [csatolja az operációs rendszer lemezegy mentési virtuális gép](../windows/troubleshoot-recovery-disks-portal.md), majd kövesse a megoldás beállításait ennek megfelelően, vagy próbálja ki a megoldásokat egyenként.

### <a name="solution-for-cause-1"></a>Megoldás az 1. ok esetén

1. Miután az operációs rendszer lemeze csatlakoztatva van egy működő virtuális géphez, győződjön meg arról, hogy a lemez **online** ként van megjelölve a Lemezkezelés konzolon, és jegyezze fel a **\Windows** mappát tartalmazó partíció meghajtóbetűjelét.

2. Ha a lemez **offline**állapotban van, akkor állítsa **online**ra.

3. Hozzon létre másolatot a **\Windows\System32\config** mappáról arra az esetre, ha a módosítások visszaállítása szükséges.

4. A mentési virtuális gép, nyissa meg a Windows Registry Editor (regedit).

5. Jelölje ki a **HKEY_LOCAL_MACHINE** kulcsot, majd válassza a menü**Fájlbetöltési** **File** > struktúra parancsát.

6. Tallózással keresse meg a RENDSZER fájlt a **\Windows\System32\config** mappában.

7. Válassza a **Megnyitás**lehetőséget, írja be a névhez a **BROKENSYSTEM** parancsot, bontsa ki a **HKEY_LOCAL_MACHINE** kulcsot, majd megjelenik egy TOVÁBBI KULCS, a **BROKENSYSTEM**.

8. Ellenőrizze, hogy melyik ControlSet eszközről indul el a számítógép. A kulcsszámát a következő beállításkulcs ban láthatja.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\Select\Current`

9. Ellenőrizze, hogy melyik a virtuálisgép-ügynök szolgáltatás kritikussága a következő beállításkulcson keresztül.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\RDAgent\ErrorControl`

10. Ha a rendszerleíró kulcs értéke nincs **2-re**állítva, akkor folytassa a következő megoldási lehetőségre.

11. Ha a rendszerleíró kulcs értéke **2**, akkor módosítsa az értéket **2-ről** **1-re**.

12. Ha az alábbi kulcsok bármelyike létezik, és azok értéke **2** vagy **3**, majd módosítsa ezeket az értékeket **1-re:**

    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupCoordinatorSvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupInquirySvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupPluginSvc\ErrorControl`

13. Válassza a **BROKENSYSTEM billentyűt,** majd válassza a menü **Fájleltávolítása** > **hive** parancsát.

14. Válassza le az operációs rendszer lemezét a hibaelhárítási virtuális gépről.

15. Távolítsa el a lemezt a hibaelhárítási virtuális gépből, és várjon körülbelül 2 percet, amíg az Azure kiadja ezt a lemezt.

16. [Hozzon létre egy új virtuális gép az operációs rendszer lemezén](../windows/create-vm-specialized.md).

17. Ha a probléma megoldódott, akkor lehet, hogy újra kell telepítenie az [RDAgent-et](https://blogs.msdn.microsoft.com/mast/2014/04/07/install-the-vm-agent-on-an-existing-azure-vm/) (WaAppAgent.exe).

### <a name="solution-for-cause-2"></a>Megoldás a 2. ok esetén

Állítsa vissza a virtuális gép az utolsó ismert jó konfiguráció, kövesse az [Azure Windows VM indítása a legutolsó helyes konfiguráció](https://support.microsoft.com/help/4016731/)című.

### <a name="solution-for-cause-3"></a>Megoldás az ok 3
>[!NOTE]
>A következő eljárást csak utolsó erőforrásként szabad használni. A regback-ből való visszaállítás visszaállíthatja a számítógéphez való hozzáférést, az operációs rendszer nem tekinthető stabilnak, mivel a struktúra időbélyege és az aktuális nap között adatok vesznek el a beállításjegyzékben. Létre kell hoznia egy új virtuális gép, és az adatok áttelepítése terveket.

1. Miután a lemez t csatolta egy hibaelhárítási virtuális géphez, győződjön meg arról, hogy a lemez **online** ként van megjelölve a Lemezkezelés konzolon.

2. Hozzon létre másolatot a **\Windows\System32\config** mappáról arra az esetre, ha a módosítások visszaállítása szükséges.

3. Másolja a fájlokat a **\Windows\System32\config\regback** mappába, és cserélje le a **\Windows\System32\config** mappában lévő fájlokat.

4. Távolítsa el a lemezt a hibaelhárítási virtuális gépből, és várjon körülbelül 2 percet, amíg az Azure kiadja ezt a lemezt.

5. [Hozzon létre egy új virtuális gép az operációs rendszer lemezén](../windows/create-vm-specialized.md).


