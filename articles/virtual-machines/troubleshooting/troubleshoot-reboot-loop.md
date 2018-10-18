---
title: Windows-beli virtuális gépen hurok újraindítás |} A Microsoft Docs
description: Ismerje meg, hogyan háríthatók el a Windows újraindítási hurokba került |} A Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/15/2018
ms.author: genli
ms.openlocfilehash: 693f28c04be5cd0acf1d5face2630a3f6d62328c
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49381015"
---
# <a name="windows-reboot-loop-on-an-azure-vm"></a>Egy Azure-beli virtuális gépen Windows újraindítási hurokba került
Ez a cikk ismerteti az újraindítási hurokba került, előfordulhat, a Windows virtuális gép (VM) a Microsoft Azure-ban.

## <a name="symptom"></a>Jelenség

Ha használ [rendszerindítási diagnosztika](./boot-diagnostics.md) a képernyőképek csak egy virtuális gép lekéréséhez található indítja a virtuális gép, de a rendszerindítási folyamat első megszakad, és a folyamat indulásával keresztül.

![Indítsa el az 1. képernyő](./media/troubleshoot-reboot-loop/start-screen-1.png)

## <a name="cause"></a>Ok

Az újraindítási hurokba került akkor fordul elő, a következő okok miatt:

### <a name="cause-1"></a>OK: 1

Egy külső szolgáltatás, amely meg van jelölve, a kritikus fontosságú, és nem indul el. Ez azt eredményezi, hogy újraindítja az operációs rendszer.

### <a name="cause-2"></a>OK 2

Egyes módosítások történtek az operációs rendszer. Általában ezek kapcsolódnak egy frissítés telepítésének, alkalmazástelepítést vagy egy új házirendet. Előfordulhat, hogy ellenőrizze a további részleteket a következő naplók kapcsolódnak:

- Eseménynaplók
- CBS.logWindows
- Update.log

### <a name="cause-3"></a>OK: 3

Fájlrendszer sérülése ennek oka az lehet. Azonban meglehetősen nehéz diagnosztizálása és a módosítás az operációs rendszer sérülését okozó azonosítása.

## <a name="solution"></a>Megoldás

Ez a probléma megoldásához [készítsen biztonsági másolatot az operációsrendszer-lemez](../windows/snapshot-copy-managed-disk.md), és [az operációsrendszer-lemez csatolása a virtuális gép mentési](../windows/troubleshoot-recovery-disks-portal.md), majd hajtsa végre a megoldási lehetőségeket az ennek megfelelően, vagy próbálja ki a megoldásokat, egyenként.

### <a name="solution-for-cause-1"></a>1 OK megoldás

1. Miután az operációsrendszer-lemez csatolva van egy működő virtuális Gépet, győződjön meg arról, hogy a lemez megjelölt **Online** a Lemezkezelés konzol, és jegyezze fel a tartalmazó partíció meghajtóbetűjelét a **\Windows** mappát.

2. Ha a lemez **Offline**, majd beállíthatja azt a **Online**.

3. Másolatot készít a **\Windows\System32\config** mappát abban az esetben a módosítások a visszaállítás van szükség.

4. Virtuális gép mentési nyissa meg a Windows beállításjegyzék-szerkesztővel (regedit).

5. Válassza ki a **HKEY_LOCAL_MACHINE** billentyűt, majd **fájl** > **a struktúra betöltése** a menüből.

6. Keresse meg a rendszer-fájlt a **\Windows\System32\config** mappát.

7. Válassza ki **nyílt**, típus **BROKENSYSTEM** nevét, bontsa ki a **HKEY_LOCAL_MACHINE** kulcsot, és aztán megjelenik egy további nevű kulcsot **BROKENSYSTEM** .

8. Ellenőrizze, melyik ControlSet a indítja a számítógépet. Látni fogja a következő beállításkulcsot a kulcsok száma.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\Select\Current`

9. Ellenőrizze, amely a VM agent szolgáltatást a következő beállításkulcs használatával a kritikusság.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\RDAgent\ErrorControl`

10. Ha a beállításkulcs értéke nincs beállítva **2**, majd nyissa meg a következő megoldás.

11. Ha a beállításkulcs értéke **2**, majd módosítsa az értéket **2** való **1**.

12. Ha bármely, a következő kulcs létezik, és értéket kell **2** vagy **3**, majd módosítsa ezeket az értékeket a **1** ennek megfelelően:

  - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupCoordinatorSvc\ErrorControl`
  - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupInquirySvc\ErrorControl`
  - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupPluginSvc\ErrorControl`

13. Válassza ki a **BROKENSYSTEM** billentyűt, majd **fájl** > **a struktúra betöltése** a menüből.

14. Válassza le az operációsrendszer-lemezt a hibaelhárító virtuális gépről.

15. Távolítsa el a lemezt a hibaelhárító virtuális gépről, és várjon körülbelül 2 percet, az Azure-hoz, a lemez felszabadítása érdekében.

16. [Hozzon létre egy új virtuális Gépet az operációsrendszer-lemezről](../windows/create-vm-specialized.md).

17. Ha a probléma kijavítása, akkor előfordulhat, hogy újra kell telepítenie a [RDAgent](https://blogs.msdn.microsoft.com/mast/2014/04/07/install-the-vm-agent-on-an-existing-azure-vm/) (WaAppAgent.exe).

### <a name="solution-for-cause-2"></a>Megoldás ok 2

A virtuális gép visszaállítása az utolsó ismert helyes konfigurációra, kövesse a [Azure Windows virtuális gép elindítása a legutolsó helyes konfiguráció](https://support.microsoft.com/help/4016731/).

### <a name="solution-for-cause-3"></a>Megoldás ok 3

1. Miután a lemezt a hibaelhárító virtuális Géphez van csatlakoztatva, győződjön meg arról, hogy a lemez megjelölt **Online** a Lemezkezelés konzol.

2. Másolatot készít a **\Windows\System32\config** mappát abban az esetben a módosítások a visszaállítás van szükség.

3. Másolja a fájlokat a a **\Windows\System32\config\regback** mappát, és cserélje le a fájlokat a **\Windows\System32\config** mappát.

4. Távolítsa el a lemezt a hibaelhárító virtuális gépről, és várjon körülbelül 2 percet, az Azure-hoz, a lemez felszabadítása érdekében.

5. [Hozzon létre egy új virtuális Gépet az operációsrendszer-lemezről](../windows/create-vm-specialized.md).

>[!NOTE]
>Az alábbi eljárás csak utolsó erőforrásként használható. Regback történő visszaállítását visszaállítása lehetséges, hogy a gép hozzáférést, amíg az operációs rendszer nem számít stabil, mivel a beállításjegyzékben a hive időbélyegét és az aktuális nap között elveszett adatok. Kell egy új virtuális Gépet hozhat létre, és adja meg a csomagok az adatok áttelepítéséhez.
