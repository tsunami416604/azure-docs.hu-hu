---
title: Windows leállási hiba – „nincs memória” állapot
description: Ez a cikk olyan problémák megoldását ismerteti, amelyekben a Windows nem indul el, és megjeleníti a "memória nélküli állapot" állapotot.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: d29c7e49-4578-43c8-b829-831da4e48932
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 33b4c59e14301e496d0eddafa7bdfdf201b7aa29
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87005905"
---
# <a name="windows-stop-error---status-no-memory"></a>Windows leállási hiba – „nincs memória” állapot

Ez a cikk olyan problémák megoldását ismerteti, amelyekben a Windows nem indul el, és megjeleníti az állapotot vagy a hibakódot #0xC0000017, más néven "nincs memória" állapotot.

## <a name="symptom"></a>Hibajelenség

Ha [rendszerindítási diagnosztikát](./boot-diagnostics.md) használ a virtuális gép (VM) képernyőképének megtekintéséhez, látni fogja, hogy a képernyőkép a következő hibakódot jeleníti meg: `0xC0000017` . A futtatott Windows-verziótól függően előfordulhat, hogy ez a kód a **Windows rendszerindítási kezelőjében** vagy a **helyreállítási képernyőn**jelenik meg.

   **Windows rendszerindítási kezelő**

   ![A Windows rendszerindítási kezelője azt jelzi, hogy a Windows nem indult el. A közelmúltbeli hardver-vagy szoftver-módosítás oka lehet ". Görgetéssel megtekintheti a "0xC0000017" állapotkódot, valamint azt is, hogy "az alkalmazást vagy az operációs rendszert nem sikerült betölteni, mert egy szükséges fájl hiányzik vagy hibákat tartalmaz".](./media/troubleshoot-windows-stop-error/1.png)

   **Helyreállítási képernyő**
 
   ![A helyreállítási képernyő, amely azt jelzi, hogy "a SZÁMÍTÓGÉPét/eszközét javítani kell. Nem áll rendelkezésre elegendő memória a Ramdisk-eszköz létrehozásához. A "0xC0000017" hibakódot is látnia kell.](./media/troubleshoot-windows-stop-error/2.png)

## <a name="cause"></a>Ok

Az operációs rendszer lemeze teljes, túl töredezett, vagy az operációs rendszer (OS) nem fér hozzá a memóriához vagy az oldal fájljához, vagy mindkettőhöz.

## <a name="solution"></a>Megoldás

### <a name="process-overview"></a>Folyamat áttekintése:

1. Javítási virtuális gép létrehozása és elérése
1. Szabadítson fel lemezterületet a lemezen
1. Rossz memória törlése a BCD-tárolóból
1. A lapozófájl visszaállítása az alapértelmezett helyre
1. Soros konzol és memóriakép-gyűjtemény engedélyezése
1. A virtuális gép újraépítése

> [!NOTE]
> Ha ezt a hibát tapasztalja, a vendég operációs rendszer nem működik. A probléma megoldásához offline módban hibaelhárítást hajthat végre.

### <a name="create-and-access-a-repair-vm"></a>Javítási virtuális gép létrehozása és elérése

1. A virtuálisgép- [javítási parancsok 1-3-es lépéseit](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) használva készítse elő a javítási virtuális gépet.
1. A Távoli asztali kapcsolat kapcsolódása a javítási virtuális géphez.

### <a name="for-generation-2-vms-assign-a-letter-to-the-extensible-firmware-interface-efi-partition"></a>A 2. generációs virtuális gépekhez rendeljen egy betűt a Extensible Firmware Interface (EFI) partícióhoz:

Ha 2. generációs virtuális gépet használ, előfordulhat, hogy a csatlakoztatott lemez EFI-partíciója nem rendelkezik a hozzá tartozó betűvel. A hibaelhárítási útmutató folytatása előtt az alábbi lépéseket kell követnie ahhoz, hogy egy betűt rendeljen a partícióhoz.

1. A Windows Search szolgáltatásban írja be `diskmgmt` és nyissa meg a **Lemezkezelés konzolt**.
1. Azonosítsa a javítási virtuális géphez csatolt sérült lemezt. Általában ez a lemez szerepel a konzolon, és a legmagasabb numerikus értékkel rendelkezik.
1. Vegye figyelembe, hogy abban a lemezen van egy olyan partíció, amely az **EFI rendszerpartíciót**tárolja, amelyhez nincs hozzárendelve Letter (például *F:* meghajtó). Ha az összes partíció hozzá van rendelve, akkor kihagyhatja, hogy szabadítson fel lemezterületet a lemezen. Ellenkező esetben továbbra is rendeljen egy betűt a lemezhez.

   ![A Lemezkezelés konzol, a mellékelt lemez: "2. lemez", valamint a nem hozzárendelt partíció, amely 100 MB, és az "EFI rendszerpartíció".](./media/troubleshoot-windows-stop-error/3.png)

1. Nyisson meg egy rendszergazdai jogú parancssort rendszergazdaként, és írja be a parancsot a `diskpart` **DiskPart** eszköz elindításához.
1. Írja be a következő parancsokat:

   ```
   list disk
   sel disk <NUMBER OF THE ATTACHED DISK>
   list partition
   sel partition <NUMBER OF THE SYSTEM (EFI) PARTION>
   assign
   ```
   
   - A parancsban cserélje le a `<NUMBER OF THE ATTACHED DISK>` értéket a 2. lépésben azonosított lemezre.
   - A parancsban cserélje le az `<NUMBER OF THE SYSTEM PARTION>` értéket az EFI rendszerpartíciójának partíciós számára. Ehhez a partícióhoz még nincs betűjel rendelve, de a **rendszernek** típusnak kell lennie, és körülbelül 100 MB méretűnek kell lennie.

   > [!NOTE]
   > A Lemezkezelés konzolon a DISKPART eszközben felsorolt partíciók összehasonlításával hasznos lehet meghatározni, hogy melyik partíció-szám felel meg a csatlakoztatott lemez EFI rendszerpartíciójának.

1. Zárja be a parancssori ablakot.

### <a name="free-up-space-on-the-disk"></a>Szabadítson fel lemezterületet a lemezen

Most, hogy a hibás lemez csatlakoztatva van a javítási virtuális géphez, ellenőrizze, hogy az adott lemezen lévő operációs rendszer elegendő lemezterülettel rendelkezik-e a megfelelő működéshez. 

1. Ellenőrizze, hogy a lemez megtelt-e, és kattintson a jobb gombbal a csatlakoztatott lemez meghajtóján, majd válassza a **Tulajdonságok parancsot**.
1. Ha a lemez **300 MB-nál kevesebb szabad területtel**rendelkezik, [bontsa ki azt legfeljebb 1 TB](../windows/expand-os-disk.md)-ra a PowerShell használatával.
1. Ha a lemez mérete **1 TB**, akkor el kell végeznie a lemez karbantartását. Lemezterület felszabadításához használhatja a Lemezkarbantartó [eszközt](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) .
1. Nyisson meg egy rendszergazda jogú parancssort (Futtatás rendszergazdaként), és végezze el a detöredezettséget a meghajtón:

   ``
   defrag <LETTER ASSIGNED TO THE OS DISK>: /u /x /g
   ``
   
   - A töredezettség szintjétől függően a detöredezettség eltarthat egy óráig.
   - A parancsban cserélje le az `<LETTER ASSIGNED TO THE OS DISK>` operációs rendszer lemezének betűjelét (például *F:* meghajtó).

### <a name="clean-out-bad-memory-from-the-boot-configuration-data-bcd-store"></a>Rossz memória tisztítása a rendszerindítási konfigurációs adatok (BCD) tárolóból

1. Nyisson meg egy rendszergazda jogú parancssort (Futtatás rendszergazdaként).
1. A következő paranccsal kérdezheti le a helytelen memória-jelzők rendszerindítási konfigurációs fájlját:

   ``
   bcdedit /store <LETTER ASSIGNED TO THE OS DISK>:\boot\bcd /enum {badmemory}
   ``
   
   - A parancsban cserélje le az `<LETTER ASSIGNED TO THE OS DISK>` operációs rendszer lemezének betűjelét (például *F:* meghajtó).

1. Ha a lekérdezés nem hibás memória-blokkokat jelenít meg, ugorjon a következő feladatra. Ellenkező esetben folytassa a 4. lépéssel.
1. Ha a rendszer hibás memória-blokkokat azonosít, ezek a blokkok megakadályozzák a Ramdisk létrehozását, és a következő paranccsal kell törölni:

   ``
   bcdedit /store <LETTER ASSIGNED TO THE OS DISK>:\boot\bcd /deletevalue {badmemory} badmemorylist
   ``
   
   - A parancsban cserélje le az `<LETTER ASSIGNED TO THE OS DISK>` operációs rendszer lemezének betűjelét (például *F:* meghajtó).

### <a name="restore-the-page-file-to-its-default-location"></a>A lapozófájl visszaállítása az alapértelmezett helyre

A lapozófájl olyan adatait tárolja, amelyeket a számítógép véletlenszerű hozzáférésű memóriája (RAM) nem tárolhat túlcsordulás/biztonsági mentés formájában. Lehetséges, hogy a fájl egy virtuális merevlemezen található, és nem a temp meghajtó, hanem az alapértelmezett Azure-hely. Ha az értéke igaz, előfordulhat, hogy a fájl nem érhető el, és vissza kell állítani az alapértelmezett helyre.

A lépések elvégzése előtt hozzon létre egy másolatot a **\Windows\System32\Config** mappáról egy kifogástalan állapotú lemezen. Ez a lépés biztosítja, hogy a képes legyen visszavonni a nemkívánatos módosításokat. Fontos rendszerfájlokon dolgozik, ezért ez az elővigyázatosság kifejezetten ajánlott.

1. A Windows Search szolgáltatásban írja be a **Regedit parancsot** , és nyissa meg a Beállításszerkesztő alkalmazást.
1. A Rendszerleíróadatbázis-szerkesztőben jelölje ki a **HKEY_LOCAL_MACHINE** kulcsot, és válassza a **fájl > betöltés struktúra..** . lehetőséget a menüből.

   ![A Beállításszerkesztő Load kaptár menüjében.](./media/troubleshoot-windows-stop-error/4.png)

1. A struktúra betöltése párbeszédpanelen válassza a **\windows\system32\config\SYSTEM** elemet, majd kattintson a Megnyitás gombra.
   1. Meg kell adnia egy nevet, amelyet **BROKENSYSTEM**kell megadnia. Ez a név segít az érintett struktúrák megkülönböztetésében a hibaelhárítás során.
   1. A **HKEY_LOCAL_MACHINE** kibontásával megtekintheti a hozzáadott új BROKENSYSTEM kulcsot.
1. A Beállításszerkesztő segítségével határozza meg, hogy a gép melyik ControlSet induljon el.
   1. Navigáljon **HKEY_LOCAL_MACHINE >> BROKENSYSTEM >> válassza a lehetőséget**.
   1. A felsorolt kulcsokban jegyezze fel az aktuális adatértéket. Ha például ez az érték **1** vagy **0x00000001 (1)**, akkor a vezérlőelem beállítása ControlSet001 lesz.
1. Győződjön meg arról a helyről, ahol a lapozófájl-létrehozás konfigurálva van.
   1. Amíg HKEY_LOCAL_MACHINE \BROKENSYSTEM, bontsa ki a 4. lépésben azonosított ControlSet-számnak megfelelő könyvtárat (például **ControlSet001**).
   1. Navigáljon a **vezérlés >> a munkamenet-kezelő >> a memória kezelése** elemre, és jegyezze fel a **ExistingPageFiles** helyét.
   1. Ennek a kulcsnak az ideiglenes meghajtó alapértelmezett Azure-beli helyén kell lennie. Ha nincs ott, és egy másik helyen lévő VHD-fájl, például az adatlemez-meghajtó vagy az operációsrendszer-meghajtó, akkor azt törölni kell.
   1. Tallózással keresse meg a helyet a Fájlkezelőben, majd törölje a **pagefile.sys** fájlt.

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>A soros konzol és a memóriakép gyűjteményének engedélyezése

**Ajánlott**: a virtuális gép újraépítése előtt engedélyezze a soros konzol és a memóriakép gyűjteményét a következő parancsfájl futtatásával:

A memóriakép-gyűjtés és a soros konzol engedélyezéséhez futtassa a következő parancsfájlt:

1. Nyisson meg egy rendszergazda jogú parancssor-munkamenetet.
1. Sorolja fel a BCD-tároló adatait, és határozza meg a rendszerindítási betöltő azonosítóját, amelyet a következő lépésben fog használni.

   1. 1. generációs virtuális gépek esetén írja be a következő parancsot, és jegyezze fel a felsorolt azonosítót:
   
      ``
      bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
      ``
   
   - A parancsban cserélje le a `<BOOT PARTITON>` betűt a partíció betűjelére a csatlakoztatott lemezen, amely tartalmazza a rendszerindító mappát.

      ![A BCD-tárolónak egy 1. generációs virtuális gépen való listázásának kimenete, amely a Windows rendszerindítási sorozatszám alatt található.](./media/troubleshoot-windows-stop-error/5.png)

   1. A 2. generációs virtuális gépek esetében írja be a következő parancsot, és jegyezze fel a felsorolt azonosítót:
   
      ``
      bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum
      ``
   
   - A parancsban cserélje le az `<LETTER OF THE EFI SYSTEM PARTITION>` EFI rendszerpartíció betűjét.
   - Hasznos lehet a Lemezkezelés konzol elindítása az **EFI rendszerpartícióként**címkézett megfelelő rendszerpartíció azonosításához.
   - Az azonosító lehet egyedi GUID, vagy lehet az alapértelmezett **Csizmadia**.

1. Futtassa a következő parancsokat a soros konzol engedélyezéséhez:

   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
   
   - A parancsban cserélje le a helyére a `<VOLUME LETTER WHERE THE BCD FOLDER IS>` BCD-mappa betűjét.
   - A parancsban cserélje le `<BOOT LOADER IDENTIFIER>` az parancsot az előző lépésben megtalált azonosítóra.

1. Győződjön meg arról, hogy az operációsrendszer-lemez szabad területe nagyobb, mint a virtuális gép memóriájának mérete (RAM).

   Ha nincs elég hely az operációsrendszer-lemezen, akkor módosítsa a memóriakép fájljának helyét, és ezt a helyet a virtuális géphez csatolt, elegendő szabad területtel rendelkező adatlemezre kell hivatkoznia. A hely módosításához cserélje le a **% systemroot%** betűjelet az adatlemez meghajtóbetűjelére (például **: F:** meghajtó) a következő parancsokban.

   Az operációs rendszer memóriaképének engedélyezéséhez javasolt konfiguráció:

   **Töltse be a beállításjegyzék-struktúrát a hibás operációsrendszer-lemezről:**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
   ```

   **Engedélyezés a ControlSet001:**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Engedélyezés a ControlSet002:**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Sérült operációsrendszer-lemez eltávolítása:**

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>A virtuális gép újraépítése

A virtuális gép újraépítéséhez használja [a virtuális gép javítási parancsainak 5. lépését](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .
