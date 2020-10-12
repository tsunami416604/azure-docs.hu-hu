---
title: A Windows rendszerindítási kezelőjével kapcsolatos hiba – 0xC0000225 "állapot nem található"
description: Az Azure-beli virtuális gépen 0xC0000225 hibakódokkal kapcsolatos problémák elhárításának lépései.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: c17899a4-b270-4725-9530-0dcd829b178c
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: 3677d67f55cfccdc80245b2ec870ffa76b0a1940
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87088665"
---
# <a name="troubleshoot-windows-boot-manager-error----0xc0000225-status-not-found"></a>A Windows rendszerindítási kezelőjével kapcsolatos hiba – 0xC0000225 "állapot nem található"
 
Ez a cikk a hibák megoldásának lépéseit ismerteti, amikor egy Azure-beli virtuális gépen hibakód 0xC0000225 történik. Ez a hiba azt jelzi, hogy az állapot vagy az objektum nem található.

## <a name="symptoms"></a>Hibajelenségek

Amikor [rendszerindítási diagnosztika](./boot-diagnostics.md) használatával tekinti meg a virtuális gép képernyőképét, látni fogja, hogy a képernyőképen a Windows nem tudott elindítani hibát az állapotkód *0xc0000225*.

A hibakódhoz társított fájl tájékoztatja, hogy milyen lépéseket kell tennie a probléma megoldásához. Keresse meg a **fájl:** szakasz szövegét, és határozza meg a megfelelő műveletet.

### <a name="drivers-os-related-or-third-party"></a>Illesztőprogramok, operációs rendszerhez kapcsolódó vagy harmadik fél

Ha a fájl jelen van, de az illesztőprogramra hivatkozik (ahogy az látható), vagy az operációs rendszerhez kapcsolódó vagy harmadik fél, kövesse a [rendszerfájl javítása](#repair-the-system-file)szakasz lépéseit.
 
A következő képen a Windows rendszerindítási kezelő állapota "a Windows nem indult el. Lehetséges, hogy a legutóbbi hardver-vagy szoftver-módosítás oka. " A képen a "0xc0000225", a **fájl:** as `\windows\System32\drivers\atapi.sys` és az **info:** "az operációs rendszer nem tölthető be, mert a kritikus rendszerillesztő hiányzik vagy hibákat tartalmaz."

![Nem sikerült elindítani a Windows rendszerindítási kezelő állapotait. A közelmúltbeli hardver-vagy szoftver-módosítás oka lehet. Emellett a "0xc0000225", a "\windows\System32\drivers\atapi.sys" és az "információ" szakaszban szereplő állapotot is megjeleníti: "az operációs rendszer nem tölthető be, mert a kritikus rendszerillesztő hiányzik vagy hibákat tartalmaz."](./media/troubleshoot-boot-error-status-not-found/1.png)

### <a name="no-file"></a>Nincs fájl

Ha az állapotkód megjelenik, de nem jelenik meg fájl, kövesse az [OSDEVICE változó hozzáadása](#add-the-osdevice-variable)című szakasz lépéseit.

A következő képen a Windows rendszerindítási kezelő állapota "a Windows nem indult el. Lehetséges, hogy a legutóbbi hardver-vagy szoftver-módosítás oka. " A képen az "0xc0000225" és az "info" állapot jelenik meg **:** "a rendszerindítás kiválasztása meghiúsult, mert egy szükséges eszköz nem érhető el."

![Nem sikerült elindítani a Windows rendszerindítási kezelő állapotait. A közelmúltbeli hardver-vagy szoftver-módosítás oka lehet. Azt is megjeleníti, hogy az állapot "0xc0000225", az információ szakaszban pedig a következőképpen jelenik meg: "a rendszerindítás nem sikerült, mert a szükséges eszköz nem érhető el."](./media/troubleshoot-boot-error-status-not-found/2.png)

### <a name="registry-file"></a>Beállításjegyzék-fájl

Ha a beállításjegyzék bármely fájljára (például \WINDOWS\SYSTEM32\CONFIG\SYSTEM) hivatkozik, kövesse a [támogatási jegy létrehozása](#contact-support)című szakasz lépéseit.
 
A következő képen a Windows rendszerindítási kezelő állapota "a Windows nem indult el. Lehetséges, hogy a legutóbbi hardver-vagy szoftver-módosítás oka. " A képen az állapot "0xc0000225", a fájl pedig a következő `\windows\System32\config\system` **:** "az operációs rendszer nem tölthető be, mert a beállításjegyzék-fájl hiányzik vagy hibákat tartalmaz."

![Nem sikerült elindítani a Windows rendszerindítási kezelő állapotait. A közelmúltbeli hardver-vagy szoftver-módosítás oka lehet. Emellett a "0xc0000225", a "\windows\System32\config\system" és az "info" szakaszban szereplő állapotot is megjeleníti: "az operációs rendszer nem tölthető be, mert a beállításjegyzék-fájl hiányzik vagy hibákat tartalmaz."](./media/troubleshoot-boot-error-status-not-found/3.png)

A következő képen a helyreállítási képernyő állapota "a számítógép/eszköz kijavítása szükséges. Nem sikerült betölteni az operációs rendszert, mert a beállításjegyzékbeli fájl hiányzik vagy hibákat tartalmaz. " A képen a hibakód a következő: "0xc0000225" és a fájl `\windows\System32\config\system` .

![A helyreállítási képernyő állapota "a számítógép/eszköz kijavítása szükséges. Az operációs rendszer nem tölthető be, mert a beállításjegyzékbeli fájl hiányzik vagy hibákat tartalmaz. A hibakódot a "0xc0000225" és a "\windows\System32\config\system" nevű fájl is mutatja.](./media/troubleshoot-boot-error-status-not-found/4.png)

## <a name="causes"></a>Okok

### <a name="missing-binary"></a>Hiányzó bináris fájl

Előfordulhat, hogy a rendszer **(. sys)** fájljában hiányzó vagy sérült bináris fájlt észlelt.

### <a name="bcd-corruption-or-improper-vhd-migration"></a>BCD-sérülés vagy nem megfelelő VHD-áttelepítés

Ebben az esetben vagy a **rendszerindítási konfigurációs adatok (BCD)** sérült, vagy a **virtuális merevlemez (VHD)** át lett telepítve a helyszíni környezetből, de nem volt megfelelően előkészített. Ennek eredményeképpen a **OSDEVICE** változó hiányzik, és hozzá kell adni.

### <a name="registry-hive-corruption"></a>Beállításjegyzék-struktúra sérülése

A beállításjegyzék-struktúra sérülésének oka a következő lehet:

- A struktúra sikertelen
- A kaptár csatlakoztatása, de üres
- A struktúra nem zárult megfelelően
## <a name="solution"></a>Megoldás

### <a name="process-overview"></a>Folyamat áttekintése

1. Hozzon létre és nyissa meg a javítási virtuális gépet.
1. Válasszon megoldást:
   - [A rendszerfájl javítása](#repair-the-system-file)
   - [A OSDevice változó hozzáadása](#add-the-osdevice-variable)
   - [Támogatási jegy létrehozása](#contact-support)
1. A soros konzol és a memóriakép gyűjteményének engedélyezése.
1. Hozza létre újra a virtuális gépet.

### <a name="create-and-access-a-repair-vm"></a>Javítási virtuális gép létrehozása és elérése

1. A virtuálisgép- [javítási parancsok](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) 1-3-es lépéseit használva készítse elő a javítási virtuális gépet.
1. A Távoli asztali kapcsolat használatával kapcsolódjon a javítási virtuális géphez.

### <a name="select-a-solution"></a>Megoldás kiválasztása

1. Nyisson meg egy rendszergazda jogú parancssort.
1. A korábban azonosított tünet alapján kövesse a megfelelő megoldás lépéseit. Kihagyhatja a többi megoldás lépéseit, mivel azok nem lesznek érvényesek a problémájára:

- [A rendszerfájl javítása](#repair-the-system-file)
- [A OSDevice változó hozzáadása](#add-the-osdevice-variable)
- [Támogatási jegy létrehozása](#contact-support)

### <a name="repair-the-system-file"></a>A rendszerfájl javítása

1. A csatolt virtuális merevlemez használatával navigáljon a virtuális gép (VM) képernyőképén látható bináris fájl helyére.
1. Kattintson a jobb gombbal a fájlra, válassza a **Tulajdonságok parancsot**, majd válassza a **részletek** lapot a fájl információinak megtekintéséhez.
   1. Jegyezze fel a fájl verzióját, ahogy az alábbi képen is látható:

      ![A "cng.sys" fájlhoz tartozó Tulajdonságok ablak, a fájl verziószáma kiemelve.](./media/troubleshoot-boot-error-status-not-found/5.png)

1. Nevezze át a fájlt **< BINARY.SYS >. Old**névre, és cserélje le **< BINARY.SYS >** a fájl nevére.

   A fenti lépésben szereplő rendszerképet a fájl **cng.sys** átnevezi **cng.sys. old névre.**

   > [!NOTE]
   > Ha megpróbálja átnevezni a fájlt, és "a fájl sérült, és nem olvasható" üzenetet kap, forduljon a [támogatási szolgálathoz](https://azure.microsoft.com/support/create-ticket/), mivel ez a megoldás nem fog működni.

1. Most, hogy a sérült fájl átnevezve lett, javítsa ki a fájlt a belső tárházból való visszaállításával.
   1. Nyisson meg egy **cmd** -munkamenetet.
   1. Navigáljon a **\windows\winsxs**.

   1. A szakasz elején található bináris fájl kereséséhez használja a következő parancsot:

      `dir <BINARY WITH ".SYS" EXTENSION>  /s`

      Ez a parancs felsorolja a fájl összes olyan verzióját, amelyen a gép található, így megadhatja az adott összetevő elérési útját.
      
      Például a **dir cng.sys** átnevezi a **dir parancsot cng.sys/s**

   1. Válassza ki a fájl legújabb verzióját a listáról (vagy bármely más előnyben részesített), és másolja a fájlt a **Windows\System32** mappába az előző elérési út és a következő parancs használatával:

      `copy <drive>:\Windows\WinSxS\<DIRECTORY WHERE FILE IS>\<BINARY WITH ".SYS" EXTENSION> <DRIVE>:\Windows\System32\Drivers\`

      > [!NOTE]
      > Ha a legújabb bináris fájl nem működött, próbálkozzon egy olyan verzióval, amely az egyik, vagy amelyről biztos, hogy rendelkezik egy stabil fájllal, például egy, a javítás előtti verzióval.

      Ha például a keresett bináris fájl **cmimcext.sys**, akkor a hibás meghajtó az **F:** meghajtó, és a legújabb verzióra való keresés futtatásakor a következő képet fogja látni, ahol a parancssorban található lekérdezés `dir cmim* /s` megkeresi a cmimcext.sys fájl legújabb verzióját.

      ![A "dir cmim */s" parancs parancssorában található lekérdezés a cmimcext.sys fájl legújabb verziójának megkereséséhez.](./media/troubleshoot-boot-error-status-not-found/6.png)

      A fenti ábrán a lekérdezés a **C:** helyen lett elvégezve, míg a meghajtó betűjele a hibás meghajtó, **F:**, amely a javítási virtuális gépen ADATlemezként csatolt operációsrendszer-lemez.

      A fájl másolásához az eredményül kapott parancs a következő lesz: `copy F:\Windows\WinSxS\amd64_xxxxxx\cmimcext.sys F:\Windows\System32\Drivers` .

A feladat befejezése után folytassa [a soros konzol és a memóriakép-gyűjtemény engedélyezésével](#enable-the-serial-console-and-memory-dump-collection).

### <a name="add-the-osdevice-variable"></a>A OSDEVICE változó hozzáadása

Gyűjtse össze az aktuális rendszerindítási telepítési adatokat, és jegyezze fel az azonosítót az aktív partíción. Ezt az információt a virtuális gép generációjának utasításait követve adja hozzá a **OSDEVICE** változóhoz.

Ha ez az adatgyűjtés hibát jelez, amikor nincs **\boot\bcd** -fájl, akkor használja a [rendszerfájl kijavítása](#repair-the-system-file) című témakör utasításait. 

1. 1. generációs virtuális gépek esetén nyisson meg egy rendszergazda jogú parancssort rendszergazdaként, és írja be a következő parancsot:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /enum`

   Ez a rendszerkép egy 1. generációs virtuális gépen a Windows rendszerindítási betöltőt jeleníti meg, az azonosító attribútum kiemelve. A Kiemelt azonosító attribútum egyedi alfanumerikus karakterláncot jelenít meg.

   ![A Windows rendszerindító betöltő az 1. generációs virtuális gépen jelenik meg, a Kiemelt azonosító attribútummal. A Kiemelt azonosító attribútum egyedi alfanumerikus karakterláncot jelenít meg.](./media/troubleshoot-boot-error-status-not-found/7.png)

   Jegyezze fel a Windows rendszerindító betöltő azonosítóját, amelynek az elérési útja **\windows\system32\winload.exe**.

1. A 2. generációs virtuális gépek esetében ellenőrizze, hogy az operációsrendszer-lemez online állapotban van-e, és hogy a partíciós meghajtó betűjele hozzá van-e rendelve. Ha a rendszer ellenőrizte ezt, a rendszerindítási telepítési adatokat gyűjti.
   1. A **Windows Search**mezőbe írja be a **Lemezkezelés** kifejezést, és nyissa meg a Lemezkezelés konzolt. Ezzel a konzollal azonosíthatja a javítási virtuális gépen csatolt lemez számát, valamint a BCD-tárolót tartalmazó Extensible Firmware Interface (EFI) partíciót.

   Az alábbi ábrán a 2. lemez a javítási virtuális géphez csatolt lemez száma. A rendszerkép az EFI rendszerpartíciót is megjeleníti a 2. lemezen, amely 100 MB méretű, és nem rendelkezik hozzárendelt betűvel.

   ![A 2. lemez a javítási virtuális géphez csatolt lemezként jelenik meg. Emellett az EFI rendszerpartíciót is megjeleníti a 2. lemezen, amely 100 MB-os, és nem rendelkezik a hozzá tartozó betűvel.](./media/troubleshoot-boot-error-status-not-found/8.png)

   1. Nyisson meg egy rendszergazdai jogú parancssort rendszergazdaként, és írja be a következő parancsokat:
      1. Nyissa meg a **DiskPart eszközt** a parancs használatával `diskpart` .
      1. Sorolja fel az összes lemezt, majd válassza ki az előző lépésben azonosított csatolt lemezt:
      
         ```
         list disk
         sel disk <DISK #>
         ```

         Az alábbi képen egy lemez listázásának és kiválasztásának eredményei láthatók. A 0. lemez (127 GB/online), az 1. lemez (32 GB/online) és a 2. lemez (127 GB/online) szerepel a felsorolásban, a 2. lemez kiválasztásával a parancs használatával `sel disk 2` .

         ![A lista eredményei és a lemez kiválasztása. 0. lemez (127 GB | Online), 1. lemez (32 GB | Online) és 2. lemez (127 GB | Online) a felsorolásban a 2. lemez van kiválasztva.](./media/troubleshoot-boot-error-status-not-found/9.png)

      1. Sorolja fel a partíciókat, és válassza ki az előző lépésben azonosított EFI rendszerpartíciót:
      
         ```
         list partition
         sel partition <PARTITION #>
         ```

         Az alábbi képen a partíciók listázásának és kiválasztásának eredményei láthatók. Az 1. partíció (fenntartott/16 MB), a 2. partíció (System/100MB) és a 3. partíció (elsődleges/126 GB) szerepel a felsorolásban, a 2. partíció a parancs használatával van kiválasztva `sel part 2` .

         ![A listaelemek és a partíciók kiválasztásának eredménye. Az 1. partíció (foglalt | 16 MB), a 2. partíció (System | 100MB) és a 3. partíció (elsődleges | 126 GB) szerepel a felsorolásban, a 2. partíció kiválasztásával.](./media/troubleshoot-boot-error-status-not-found/10.png)

      1. Rendeljen egy betűt az EFI-partícióhoz a parancs használatával `assign` .

         Az alábbi ábrán a `assign` parancs és az új meghajtórendszer **(F:)** is megtekinthető a fájlkezelőben.

         ![A assign parancs és az új meghajtórendszer (F:) a Fájlkezelőben tekinthetők meg.](./media/troubleshoot-boot-error-status-not-found/11.png)

      1. A következő paranccsal sorolja fel a BCD-tárolót:
      
         `bcdedit /store <LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum`

         Az alábbi ábrán a Windows rendszerindítási betöltő egy 2. generációs virtuális gépen található, amelynek az azonosító attribútuma ki van emelve. A Kiemelt azonosító attribútum értéke **{default}**.

         ![A Windows rendszerindító betöltő a Kiemelt azonosító attribútummal rendelkező 2. generációs virtuális gépen jelenik meg. Az azonosító attribútum kiemelve értékként az alapértelmezett értéket jeleníti meg.](./media/troubleshoot-boot-error-status-not-found/12.png)

         Jegyezze fel a Windows rendszerindító betöltő azonosítóját, amelynek az elérési útja **\windows\system32\winload.EFI**.

1. Figyelje meg, hogy az aktív partíción az OSDEVICE változó hiányzik:

   ![A Windows rendszerindítási kezelő és a Windows rendszerbetöltő attribútumai a parancssorban vannak felsorolva, és az operációs rendszer eszköz attribútuma hiányzik.](./media/troubleshoot-boot-error-status-not-found/13.png)
   
   Ebben a rendszerképben a Windows rendszerindító-kezelő és a Windows rendszerindító betöltő attribútumai szerepelnek a parancssorban, de a OSDEVICE attribútum hiányzik.

1. Adja hozzá a OSDEVICE változót az alábbi információk alapján:

   Az egypartíciós operációsrendszer-lemezekhez adja hozzá a következőt: `BOOT` .

   > [!NOTE]
   > A rendszerindításra alkalmas mappa ugyanazon a partíción lesz, mint a Windows mappa **\Windows mappája**.
   > - Az 1. generációs virtuális gépek rendszerindító mappája **(\boot\bcd mappa)**.
   > - A 2. generációs virtuális gépek rendszerindító mappája **EFI\Microsoft\boot\bcd**.

   1. generációs virtuális gépek esetén írja be a következő parancsot:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   A 2. generációs virtuális gépek esetében adja meg a következő parancsot:

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   Több partíciós operációsrendszer-lemez esetén adja hozzá a következőt: `PARTITION=<LETTER OF WINDOWS FOLDER>:` .

   > [!NOTE]
   > A rendszerindításra alkalmas mappa valószínűleg egy másik partíción lesz, mint a Windows mappa **\Windows mappája**.
   > - Az 1. generációs virtuális gépek rendszerindító mappája **(\boot\bcd mappa)**.
   > - A 2. generációs virtuális gépek rendszerindító mappája **EFI\Microsoft\boot\bcd**.

   1. generációs virtuális gépek esetén írja be a következő parancsot:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

   A 2. generációs virtuális gépek esetében adja meg a következő parancsot:

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {< IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

A feladat befejezése után folytassa [a soros konzol és a memóriakép-gyűjtemény engedélyezésével](#enable-the-serial-console-and-memory-dump-collection).

### <a name="contact-support"></a>Kapcsolatfelvétel a támogatási szolgáltatással

A **beállításjegyzék-fájl** hibája megoldással rendelkezik, de további segítségért [létre kell hoznia egy támogatási jegyet](https://azure.microsoft.com/support/create-ticket/) .

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>A soros konzol és a memóriakép gyűjteményének engedélyezése

**Ajánlott**: a virtuális gép újraépítése előtt engedélyezze a soros konzol és a memóriakép gyűjteményét a következő parancsfájl futtatásával:

1. Nyisson meg egy rendszergazda jogú parancssor-munkamenetet.
1. Futtassa az alábbi parancsot:

   **A soros konzol engedélyezése**:
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Győződjön meg arról, hogy az operációsrendszer-lemez szabad területe nagyobb, mint a virtuális gép memóriájának mérete (RAM).

   Ha nincs elég hely az operációsrendszer-lemezen, akkor módosítsa a memóriakép fájljának helyét, és ezt a helyet a virtuális géphez csatolt, elegendő szabad területtel rendelkező adatlemezre kell hivatkoznia. A hely módosításához cserélje le a **% systemroot%** betűjelet az adatlemez meghajtóbetűjelére (pl **. F:**) a következő parancsokban.

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
