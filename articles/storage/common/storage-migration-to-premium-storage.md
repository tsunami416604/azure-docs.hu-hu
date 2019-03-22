---
title: Az Azure Premium Storage virtuális gépek áttelepítése |} A Microsoft Docs
description: A meglévő virtuális gépek áttelepítése az Azure Premium Storage. A Premium Storage nagy teljesítményű, kis késleltetésű lemeztámogatás I/O-igényes számítási feladatokhoz az Azure Virtual machines szolgáltatásban futó kínál.
services: storage
author: yuemlu
ms.service: storage
ms.topic: article
ms.date: 06/27/2017
ms.author: yuemlu
ms.subservice: common
ms.openlocfilehash: fd72e2a75c00c30fdc5497e0d88e9c83dc5fcad8
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317357"
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>(Nem felügyelt lemezek) az Azure Premium Storage-ba való migrálás

> [!NOTE]
> Ez a cikk ismerteti, hogyan telepítheti át a prémium nem felügyelt lemezeket használó virtuális gép standard nem felügyelt lemezeket használó virtuális gép. Azt javasoljuk, hogy az új virtuális gépek az Azure Managed Disks szolgáltatást, és a korábbi nem felügyelt lemezek konvertálása felügyelt lemezeket. Felügyelt lemezek leíró a mögöttes tárfiókokat, így nem kell. További információkért tekintse meg a [Managed Disks szolgáltatás áttekintése](../../virtual-machines/windows/managed-disks-overview.md).
>

Az Azure Premium Storage nagy teljesítményű, kis késleltetésű lemeztámogatás I/O-igényes számítási feladatokat futtató virtuális gépekhez tesz lehetővé. A sebesség előnyeit, és ezek a lemezek teljesítményét is igénybe vehet a Virtuálisgép-lemezek az alkalmazás az Azure Premium Storage-ba való migrálás.

A jelen útmutató célja, hogy új felhasználók az Azure Premium Storage nagyobb való zökkenőmentes váltásban a jelenlegi rendszer prémium szintű Storage előkészítése. Útmutató a címek közül a legfontosabb összetevők, a folyamat három:

* [A Premium Storage az áttelepítés megtervezése](#plan-the-migration-to-premium-storage)
* [Készítse elő, és másolja a virtuális merevlemezeket (VHD) prémium szintű Storage](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Premium Storage tárolást használ az Azure virtuális gép létrehozása](#create-azure-virtual-machine-using-premium-storage)

Más platformokon, a virtuális gépek áttelepítése az Azure Premium Storage, vagy meglévő Azure virtuális gépek migrálása Standard Storage prémium szintű Storage. Ez az útmutató mindkét két forgatókönyv lépéseit ismerteti. A forgatókönyvtől függően vonatkozó részében meghatározott lépésekkel.

> [!NOTE]
> A funkciók áttekintését és a prémium szintű SSD díjszabás találja: [Válassza ki a lemez típusát az IaaS virtuális gépek](../../virtual-machines/windows/disks-types.md#premium-ssd). Azt javasoljuk, hogy minden virtuálisgép-lemez magas iops-t az Azure Premium Storage igénylő a legjobb teljesítmény érdekében az alkalmazás migrálása. Ha a lemez nincs szükség a magas iops-érték, megőrizve a standard szintű tárolóban, amely tárolja a virtuális gép lemez adatait a (merevlemezes HDD) meghajtók helyett SSD-k korlátozhatja költségeket.
>

Ebben az esetben az áttelepítési folyamat befejezése szükség lehet további műveletek előtt és után a jelen útmutatóban ismertetett lépéseket. Ilyenek például a virtuális hálózatok vagy végpontok konfigurálása vagy kódmódosítások belül magát az alkalmazást, amelyre szükség lehet némi állásidőt jelent az alkalmazásban. Ezek a műveletek minden alkalmazáshoz egyedi, és együtt a váltásban a teljes prémium szintű Storage, amennyire csak lehetséges, zökkenőmentes útmutatóban ismertetett lépéseket kell végeznie őket.

## <a name="plan-the-migration-to-premium-storage"></a>A Premium Storage az áttelepítés megtervezése
Ez a szakasz biztosítja, hogy ez a cikk az áttelepítési lépéseket készen áll, és segítséget nyújt ahhoz, hogy a legjobb döntés VM és a lemez típusa.

### <a name="prerequisites"></a>Előfeltételek
* Szüksége lesz egy Azure-előfizetést. Ha még nincs fiókja, létrehozhat egy hónapos [az ingyenes próbaidőszak](https://azure.microsoft.com/pricing/free-trial/) előfizetést, vagy keresse fel [Azure díjszabását](https://azure.microsoft.com/pricing/) a további lehetőségeket.
* PowerShell-parancsmagok végrehajtásához szüksége lesz a Microsoft Azure PowerShell-modult. A telepítési helyre és a telepítésre vonatkozó utasításokért lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).
* Ha azt tervezi, használja az Azure-beli virtuális gépek Premium Storage futó, kell használnia a Premium Storage képes a virtuális gépeket. A Premium Storage képes a virtuális gépek Standard és prémium szintű Storage-lemez is használható. Prémium szintű tárolólemezeket a jövőben további VM-típusokkal elérhető lesz. Az Azure-beli Virtuálisgép-lemez elérhető típusú és méretű szoftverprojekten további információkért lásd: [virtuális gépek méretei](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) és [méretű felhőszolgáltatások](../../cloud-services/cloud-services-sizes-specs.md).

### <a name="considerations"></a>Megfontolandó szempontok
Egy Azure virtuális gép támogatja, több Premium Storage-lemez csatolása az, hogy az alkalmazásokat akár 256 TB tárhelyet is rendelkezhet. A Premium Storage az alkalmazások a rendkívül alacsony késésű olvasási műveletek mellett virtuális gépenként s adatátviteli sebességet érhet 80 000 iops-t (bemeneti/kimeneti műveletek száma másodpercenként) / virtuális gép és 2000 MB. Lemezek és virtuális gépek különböző lehetőségek állnak rendelkezésére. Ez a szakasz azt, hogy az a számítási feladathoz leginkább megfelelő opció.

#### <a name="vm-sizes"></a>A virtuális gépek mérete
Az Azure virtuális gép mérete előírások felsorolt [virtuális gépek méretei](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Tekintse át a teljesítményt nyújt, amely a Premium Storage működnek, és válassza ki a leginkább megfelelő Virtuálisgép-méretet a számítási feladathoz leginkább megfelelő virtuális gépek. Győződjön meg arról, hogy van-e elegendő sávszélesség érhető el a meghajtó a lemez forgalmat a virtuális gép.

#### <a name="disk-sizes"></a>Lemezméretek
A virtuális gép használható lemezek öt típusa van, mindegyik adott IOPs és átviteli sebesség korlátok. Figyelembe ezeket a korlátokat kiválasztása a lemez típusát a virtuális gép kapacitását, teljesítmény, méretezhetőség tekintetében az alkalmazás igényeinek megfelelően, és csúcs tölti be.

| Prémium szintű lemezek típusa  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Lemezméret           | 128 GB| 512 GB| 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 
| IOPS-érték lemezenként       | 500   | 2300  | 5000           | 7500           | 7500           | 
| Adattovábbítás lemezenként | 100 MB / s | 150 MB / s | 200 MB / s | 250 MB / s | 250 MB / s |

Attól függően, a számítási feladatok határozza meg, ha további adatlemezek szükségesek a virtuális gép. Több állandó adatlemezeket csatlakoztathat a virtuális gép. Szükség esetén meg is stripe-kapacitás és a kötet teljesítménye növelése érdekében a lemezek között. (Megtudhatja, mit tartogat a lemezt csíkozást [Itt](../../virtual-machines/windows/premium-storage-performance.md#disk-striping).) Ha a stripe-e a Premium Storage adatlemezek használata [tárolóhelyek][4], minden egyes használt lemezek egy oszlopot kell konfigurálnia. Ellenkező esetben a csíkozott kötet általános teljesítménye lehet alacsonyabb, mint a várt forgalom eloszlása egyenletlen miatt a lemezeket. Linux rendszerű virtuális gépekhez is használhatja a *mdadm* ugyanennek segédprogramot. A cikkben [szoftver RAID konfigurálása linuxon](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) részleteiről.

#### <a name="storage-account-scalability-targets"></a>Skálázási célértékei
Premium Storage-fiókok rendelkezik a következő teljesítménycélokat mellett a [Azure Storage méretezhetőségi és Teljesítménycéljai](storage-scalability-targets.md). Ha az alkalmazás követelményeinek meghaladják a skálázhatósági célokat, az egy tárfiókban, több tárfiók használata az alkalmazás létrehozása, és az adatok particionálása az adott tárfiókon keresztül.

| Teljes számla kapacitás | Helyileg redundáns tárfiókok teljes sávszélesség |
|:--- |:--- |
| Lemez kapacitása: 35TB<br />Pillanatkép-kapacitás: 10 TB |Legfeljebb 50 Gigabit / másodperc bejövő + kimenő |

A Premium Storage-specifikációk további információkért tekintse meg [Azure Storage méretezhetőségi és teljesítménycéljai](storage-scalability-targets.md#premium-storage-account-scale-limits).

#### <a name="disk-caching-policy"></a>Lemez gyorsítótárazási házirend
Alapértelmezés szerint a lemez gyorsítótárazási házirend a *csak olvasható* minden a prémium szintű adatlemezek esetén és *olvasási és írási* a prémium szintű operációsrendszer-lemez a virtuális Géphez csatlakoztatva. Ezt a konfigurációs beállítást az optimális teljesítmény érdekében az alkalmazás IOs-hez javasolt. Írási vagy csak írási adatlemezek (például az SQL Server-naplófájlok) tiltsa le a lemezek gyorsítótárazása, így jobb alkalmazásteljesítményt érhet el. A gyorsítótár beállításait a meglévő adatlemezek paranccsal frissíthető [az Azure Portal](https://portal.azure.com) vagy a *- HostCaching* paraméterében a *Set-AzureDataDisk* parancsmagot.

#### <a name="location"></a>Hely
Válasszon ki egy helyet, ahol az Azure Premium Storage érhető el. Lásd: [Azure-szolgáltatások régió szerint](https://azure.microsoft.com/regions/#services) naprakész információk az elérhető helyek. A virtuális gépek és a Storage-fiókot, hogy tárolja a virtuális gép lemezei ad sokkal jobb teljesítményt, mint ha külön régióban vannak ugyanabban a régióban található.

#### <a name="other-azure-vm-configuration-settings"></a>Más Azure-beli Virtuálisgép-konfigurációs beállítások
Egy Azure virtuális gép létrehozásakor meg kell adnia az egyes virtuális gép beállításainak konfigurálása. Ne feledje, hogy néhány beállítás rögzítettek a virtuális gép teljes élettartama során módosíthatja, vagy később hozzáadhat mások. Tekintse át ezeket az Azure virtuális gép konfigurációs beállításait, és győződjön meg arról, hogy ezek is megfelelően konfigurálta a munkaterhelés követelményeinek megfelelően.

### <a name="optimization"></a>Optimalizálás
[Azure Premium Storage: Nagy teljesítményű rendszer tervezése](../../virtual-machines/windows/premium-storage-performance.md) hasznos útmutatást ad az Azure Premium Storage nagy teljesítményű alkalmazások készítéséhez. Az irányelveket, az alkalmazása által használt technológiák alkalmazandó ajánlott eljárások teljesítményének kombinálva is követheti.

## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Készítse elő, és másolja a virtuális merevlemezeket (VHD) prémium szintű Storage
A következő szakaszban talál útmutatást előkészítése virtuális merevlemezek a virtuális gépről, és másolja a VHD-k az Azure Storage.

* [1. forgatókönyv: "Tudom vagyok áttelepítése meglévő Azure virtuális gépek az Azure Premium Storage."](#scenario1)
* [2. forgatókönyv: "Tudom vagyok-ről virtuális gépek más platformokon az Azure Premium Storage."](#scenario2)

### <a name="prerequisites"></a>Előfeltételek
Áttelepítés a virtuális merevlemezek előkészítéséhez lesz szüksége:

* Azure-előfizetéssel, egy tárfiókot és egy tárolót a storage-fiókot, amelyhez a VHD-t is másolhatja az. Vegye figyelembe, hogy a cél tárfiók is igényektől függően Standard vagy prémium szintű Storage-fiókot.
* A virtuális merevlemez általánosítani, ha azt tervezi, hogy hozzon létre több Virtuálisgép-példányok, az eszköz. Például a Windows-vagy ubuntu rendszerre készült virt-sysprep sysprep.
* Egy eszköz a VHD-fájl feltöltése a Storage-fiókba. Lásd: [adatátvitel az AzCopy parancssori segédprogrammal](storage-use-azcopy.md) vagy használjon egy [az Azure storage explorer](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Ez az útmutató ismerteti, másolja a VHD-t az AzCopy eszközzel.

> [!NOTE]
> Ha úgy dönt, hogy szinkron másolatot beállítást az AzCopy, az optimális teljesítmény érdekében másolja a VHD-t az eszközöket és a cél tárfiók ugyanabban a régióban lévő Azure virtuális Gépeken való futtatásával. Virtuális merevlemez másolása egy másik régióban lévő Azure virtuális gépből, akkor a teljesítmény lassabb lehet.
>
> A sávszélesség korlátozott keresztül másolja a nagy mennyiségű adatot, érdemes lehet [az Azure Import/Export szolgáltatás használata az adatok átviteléhez a Blob Storage](../storage-import-export-service.md); Ez lehetővé teszi, hogy az adatátvitel merevlemezes meghajtók egy Azure-adatközpontban való szállításával. Az Azure Import/Export szolgáltatás használatával másolja az adatokat a csak a standard szintű tárfiókot. Amint az adatok a standard szintű tárfiókja, használhatja a [másolás Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx) vagy az adatok átvitelét a prémium szintű storage-fiókba az AzCopy.
>
> Vegye figyelembe, hogy csak a Microsoft Azure támogatja a rögzített méretű VHD-fájlokat. A VHDX-fájlok vagy a dinamikus VHD-k nem támogatottak. Ha egy dinamikus VHD-t, akkor átalakíthatja a rögzített méretű használatával a [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) parancsmagot.
>
>

### <a name="scenario1"></a>1. forgatókönyv: "Tudom vagyok áttelepítése meglévő Azure virtuális gépek az Azure Premium Storage."
Meglévő Azure virtuális gépeket telepít át, ha a virtuális gép leállítása, készítse elő a VHD-k száma a kívánt virtuális merevlemez típusát, és másolja a VHD-AzCopy vagy a PowerShell használatával.

A virtuális gép kell lennie a teljes mértékben lefelé áttelepíteni a tiszta állapotot. Lesz használható az áttelepítés befejeződéséig.

#### <a name="step-1-prepare-vhds-for-migration"></a>1. lépés Virtuális merevlemezek előkészítése az áttelepítésre
Ha a meglévő Azure virtuális gépeket telepít át a Premium Storage, a VHD-t lehet:

* Általánosított operációs rendszer lemezképének elkészítése
* Egy egyedi operációsrendszer-lemez
* Adatlemez

Az alábbiakban azt végig a virtuális merevlemez előkészítésére vonatkozó 3 forgatókönyvekben.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Operációs rendszer általánosított virtuális merevlemez használatával hozzon létre több Virtuálisgép-példányok
Több általános Azure-beli Virtuálisgép-példányok létrehozásához használt virtuális Merevlemezt tölt fel, ha először meg kell generalize virtuális Merevlemezt a sysprep segédprogrammal. Ez vonatkozik a virtuális merevlemez, amely a helyszínen vagy a felhőben. A Sysprep minden olyan információk távolít el a VHD-t.

> [!IMPORTANT]
> Pillanatkép készítése vagy biztonsági másolatot készíteni a virtuális gép általánosítása azt megelőzően. A sysprep futtatása leáll, és a Virtuálisgép-példány felszabadítása. Kövesse az alábbi lépéseket a sysprep a Windows rendszert tartalmazó virtuális Merevlemezt. Vegye figyelembe, hogy a Sysprep parancs futtatása van szükség, hogy állítsa le a virtuális gépet. A Sysprep kapcsolatos további információkért lásd: [Sysprep áttekintése](https://technet.microsoft.com/library/hh825209.aspx) vagy [technikai útmutató a Sysprep](https://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Nyisson meg egy parancssori ablakot rendszergazdaként.
2. Adja meg a Sysprep nyissa meg a következő parancsot:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. A rendszer-előkészítő eszköz válassza ki a rendszer adja meg Out-of-Box élmény (OOBE), jelölje be a Generalize jelölőnégyzetet, válassza ki **leállítási**, és kattintson a **OK**, ahogy az alábbi képen látható. A Sysprep fog általánosítja az operációs rendszert, és állítsa le a rendszer.

    ![][1]

Egy Ubuntu virtuális gép ugyanennek a virt-Sysprep eszközt használja. Lásd: [virt-sysprep](https://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) további részletekért. Lásd még a nyílt forráskódú némelyike [szoftverek Linux-kiszolgáló üzembe helyezésének](https://www.cyberciti.biz/tips/server-provisioning-software.html) más Linux operációs rendszerekhez.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Operációs rendszer virtuális merevlemez egyedi használatával egyetlen Virtuálisgép-példány létrehozása
Ha a gép adott adatokra van szüksége a virtuális gépen futó alkalmazásokhoz, nem általánossá tétele a virtuális Merevlemezt. Nem általánosított virtuális merevlemez egyedi Azure-beli Virtuálisgép-példány létrehozása használható. Például tartományvezérlő van a VHD-t, ha a sysprep végrehajtása teszi hatástalan tartományvezérlőként. Tekintse át a virtuális gép és a rajtuk a sysprep futtatása előtt a virtuális merevlemez általánosítása hatását futó alkalmazások.

##### <a name="register-data-disk-vhd"></a>Virtuális merevlemez adatlemez regisztrálása
Ha az adatlemezeket az Azure-ban kell áttelepíteni, biztosítsa, állítsa le az alábbi adatok lemezeket használó virtuális gépeket.

Másolja a VHD-t az Azure Premium Storage és a kiépített adatlemezként regisztrálása alább leírt lépésekkel.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>2. lépés A cél a virtuális merevlemez létrehozása
Hozzon létre egy storage-fiók karbantartása a virtuális merevlemezeket. A virtuális merevlemezek tárolási helyének megtervezésekor, vegye figyelembe a következőket:

* A cél Premium storage-fiók.
* A tárfiók helye ugyanaz, mint a prémium szintű Storage képes Azure virtuális gépeken létrehozza az utolsó szakaszban kell lennie. Sikerült másolja egy új tárfiókot, vagy a csomag használatára, szükség szerint ugyanazt a tárfiókot.
* Másolja és mentse a cél tárfiók tárfiók-kulcsot a következő szakaszra.

Adatlemezek esetén dönthet úgy, hogy ne néhány adatlemezt a standard szintű tárfiók (például lemezek, amelyek rendelkeznek a hűtőre vonatkozó tárolási), de erősen javasoljuk, hogy minden adat a prémium szintű storage használata éles számítási feladatok áthelyezése.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>3. lépés. Másolja a VHD-AzCopy vagy a PowerShell használatával
Meg kell keresnie feldolgozni a két lehetőség közül választhat a tároló elérési útja és a tárfiók kulcsára. Tároló elérési útja és a tárfiók kulcsát található **az Azure Portal** > **tárolási**. A tároló URL-címe lesz, például a "https:\//myaccount.blob.core.windows.net/mycontainer/".

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Option 1: Másolja a VHD-t az Azcopyval (aszinkron példány)
AzCopy használatával könnyedén tölthet fel a VHD-t az interneten keresztül. A VHD méretétől függően ez időt vehet igénybe. Fontos, hogy ellenőrizze a tárfiókok bejövő/kimenő forgalom korlátai, ez a beállítás használatakor. Lásd: [Azure Storage méretezhetőségi és Teljesítménycéljai](storage-scalability-targets.md) részleteiről.

1. Töltse le és telepítse az AzCopy innen: [Az AzCopy legújabb verzióját](https://aka.ms/downloadazcopy)
2. Nyissa meg az Azure PowerShell-lel, és nyissa meg a mappát, ahol telepítve van-e az AzCopy.
3. A következő parancs használatával másolja a VHD-fájlt a "Forrás", "Cél".

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Példa:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```

    Az alábbiakban az AzCopy-parancsban használt paraméterek leírását:

   * **/ Forrás:  *&lt;forrás&gt;:*** A mappa vagy a storage tárolót tartalmazó URL-cím a virtuális merevlemez helye.
   * **/ SourceKey:  *&lt;forrás-fiókkulcs&gt;:*** A forrás tárfiókban tárfiókkulcs.
   * **/ Cél:  *&lt;cél&gt;:*** Tárolási tároló URL-cím a virtuális merevlemez másolása.
   * **/ DestKey:  *&lt;dest fiókkulcs&gt;:*** A cél tárfiók tárfiók kulcsa.
   * **/ Minta:  *&lt;Fájlnév&gt;:*** Adja meg a virtuális merevlemez másolása a fájl nevét.

Eszköz AzCopy használatával kapcsolatos részletekért, lásd: [adatátvitel az AzCopy parancssori segédprogrammal](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Option 2: Másolja a VHD-t a PowerShell-lel (Synchronized példány)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Másolhatja a VHD-fájl Start-AzStorageBlobCopy PowerShell-parancsmag segítségével is. Az Azure PowerShell-lel az alábbi parancs használatával másolja a VHD-t. <> Szereplő értékeket cserélje le a forrás- és storage-fiókból tartozó értékek. Használja ezt a parancsot, a cél tárfiók VHD-ken nevű tárolóban kell rendelkeznie. Ha a tároló nem létezik, hozzon létre egyet a parancs futtatása előtt.

```powershell
$sourceBlobUri = <source-vhd-uri>

$sourceContext = New-AzStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

$destinationContext = New-AzStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

Start-AzStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext
```

Példa:

```powershell
C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

C:\PS> $sourceContext = New-AzStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

C:\PS> $destinationContext = New-AzStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

C:\PS> Start-AzStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext
```

### <a name="scenario2"></a>2. forgatókönyv: "Tudom vagyok-ről virtuális gépek más platformokon az Azure Premium Storage."
Ha az áttelepítés VHD-t a nem – az Azure felhőalapú tárolás az Azure-ba, először exportálnia kell a virtuális Merevlemezt egy helyi könyvtárba. A teljes forrásának elérési útvonala a helyi könyvtárban virtuális Merevlemezt tároló praktikus rendelkezik, és az AzCopy segítségével töltse fel az Azure Storage.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>1. lépés Exportálja a virtuális Merevlemezt egy helyi könyvtárba
##### <a name="copy-a-vhd-from-aws"></a>Másolja a VHD-t az AWS-től
1. AWS használja, ha exportálja az EC2-példánynak az Amazon S3 gyűjtőt a virtuális merevlemez. Az Exportálás Amazon EC2-példányokon telepítse az Amazon EC2-é parancssori felület (CLI) eszközt, és futtassa a create-példány-export-tevékenység parancsot az EC2-példány exportálni egy VHD-fájl az Amazon dokumentációjában leírt lépésekkel. Ügyeljen arra, hogy **VHD** a lemez&#95;kép&#95;formátum változó futtatásakor a **-példány-export-feladat létrehozása** parancsot. Az exportált VHD-fájlt a Amazon S3 gyűjtőt jelöl ki, hogy a folyamat során a rendszer menti.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Töltse le a virtuális merevlemez fájlját az S3 gyűjtőt. Válassza ki a VHD-fájlt, majd **műveletek** > **letöltése**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Másolja a VHD-t más-Azure felhőben
Ha az áttelepítés VHD-t a nem – az Azure felhőalapú tárolás az Azure-ba, először exportálnia kell a virtuális Merevlemezt egy helyi könyvtárba. Másolja a teljes forrásának elérési útvonala a helyi könyvtárban, ahol a virtuális merevlemez található.

##### <a name="copy-a-vhd-from-on-premises"></a>A helyi virtuális merevlemez másolása
Virtuális merevlemez telepít át egy helyszíni környezetből, ha szüksége lesz a virtuális Merevlemezt tároló teljes forrásútvonalát. A forrás elérési útja lehet egy kiszolgáló helye vagy a fájlmegosztásnak.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>2. lépés A cél a virtuális merevlemez létrehozása
Hozzon létre egy storage-fiók karbantartása a virtuális merevlemezeket. A virtuális merevlemezek tárolási helyének megtervezésekor, vegye figyelembe a következőket:

* A célként megadott tárfiók lehet standard vagy prémium szintű storage alkalmazás igényektől függően.
* A tárfiókok régiója ugyanaz, mint a prémium szintű Storage képes Azure virtuális gépeken létrehozza az utolsó szakaszban kell lennie. Sikerült másolja egy új tárfiókot, vagy a csomag használatára, szükség szerint ugyanazt a tárfiókot.
* Másolja és mentse a cél tárfiók tárfiók-kulcsot a következő szakaszra.

Erősen ajánlott, a prémium szintű storage használata éles számítási feladatok minden adat áthelyezése.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>3. lépés A virtuális merevlemez feltöltése az Azure Storage
Most, hogy a VHD-t a helyi könyvtárban, az AzCopy vagy AzurePowerShell használhatja a .vhd fájl feltöltése az Azure Storage. Két lehetőség áll rendelkezésre a itt:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Option 1: Azure PowerShell Add-AzureVhd segítségével a .vhd-fájl feltöltése

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Példa <Uri> lehet ***"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"***. Példa <FileInfo> lehet ***"C:\path\to\upload.vhd"***.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Option 2: A .vhd-fájl feltöltése AzCopy használatával
AzCopy használatával könnyedén tölthet fel a VHD-t az interneten keresztül. A VHD méretétől függően ez időt vehet igénybe. Fontos, hogy ellenőrizze a tárfiókok bejövő/kimenő forgalom korlátai, ez a beállítás használatakor. Lásd: [Azure Storage méretezhetőségi és Teljesítménycéljai](storage-scalability-targets.md) részleteiről.

1. Töltse le és telepítse az AzCopy innen: [Az AzCopy legújabb verzióját](https://aka.ms/downloadazcopy)
2. Nyissa meg az Azure PowerShell-lel, és nyissa meg a mappát, ahol telepítve van-e az AzCopy.
3. A következő parancs használatával másolja a VHD-fájlt a "Forrás", "Cél".

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Példa:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
    ```

    Az alábbiakban az AzCopy-parancsban használt paraméterek leírását:

   * **/ Forrás:  *&lt;forrás&gt;:*** A mappa vagy a storage tárolót tartalmazó URL-cím a virtuális merevlemez helye.
   * **/ SourceKey:  *&lt;forrás-fiókkulcs&gt;:*** A forrás tárfiókban tárfiókkulcs.
   * **/ Cél:  *&lt;cél&gt;:*** Tárolási tároló URL-cím a virtuális merevlemez másolása.
   * **/ DestKey:  *&lt;dest fiókkulcs&gt;:*** A cél tárfiók tárfiók kulcsa.
   * **/ BlobType: lap:** Megadja, hogy a cél egy lapblob.
   * **/ Minta:  *&lt;Fájlnév&gt;:*** Adja meg a virtuális merevlemez másolása a fájl nevét.

Eszköz AzCopy használatával kapcsolatos részletekért, lásd: [adatátvitel az AzCopy parancssori segédprogrammal](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Más beállításokat a virtuális merevlemez feltöltése
Emellett feltölthet egy virtuális Merevlemezt a tárfiókba, a következő módszerek egyikével:

* [Az Azure Storage Blob másolásához API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Az Azure Storage Explorer Blobok feltöltése](https://azurestorageexplorer.codeplex.com/)
* [Storage Import/Export szolgáltatás REST API-referencia](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> Import/Export szolgáltatás használatát, ha a 7 napnál hosszabb ideje feltöltése, becsült javasoljuk. Használhat [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) megbecsülni az adatok mérete és átviteli egység időpontját.
>
> Importálási/exportálási átmásolása a standard szintű tárfiók is használható. Szüksége lesz egy eszköz, például az AzCopy használata a premium storage-fiók átmásolása standard storage-ból.
>
>

## <a name="create-azure-virtual-machine-using-premium-storage"></a>Hozzon létre az Azure virtuális gépek Premium Storage szolgáltatással
Miután a virtuális merevlemez van feltöltve, vagy egy a kívánt tárfiókot, kövesse a történő regisztrálása a VHD-t operációsrendszer-képet, vagy a forgatókönyvtől függően operációsrendszer-lemez, és hozzon létre egy Virtuálisgép-példány belőle. A virtuális merevlemez adatlemez csatolható a virtuális gép létrehozása után.
Ez a szakasz végén egy mintaszkriptet áttelepítési biztosítunk. Ez egyszerű szkript nem egyezik meg az összes forgatókönyv. Előfordulhat, hogy frissíteni szeretné a parancsfájlt az adott forgatókönyvnek megfelelően. Ha ez a szkript a forgatókönyvre vonatkozik, olvassa el alább [egy mintául szolgáló Migrálási parancsfájl](#a-sample-migration-script).

### <a name="checklist"></a>Ellenőrzőlista
1. Várja meg, amíg az összes másolása a VHD lemezek számára befejeződött.
2. Ellenőrizze, hogy prémium szintű Storage elérhető végzi az áttelepítést a régióban.
3. Döntse el, az új Virtuálisgép-sorozatok fog használni. Egy prémium szintű Storage képes a kell lennie, és mérete kell lehet attól függően, a rendelkezésre állási régióban, és saját igényei szerint.
4. Döntse el, a pontos használandó Virtuálisgép-méretet. Virtuálisgép-méretet kell lennie, elég nagy legyen támogatja a több adatlemez van. Például Ha 4 adatlemezeket, a virtuális gép 2 vagy több mag kell rendelkeznie. Emellett érdemes lehet a feldolgozási teljesítményt, memóriát és hálózati sávszélességet kell.
5. Prémium szintű Storage-fiók létrehozása a célrégióban. Ez az a fiók, az új virtuális gép fog használni.
6. Rendelkezik a kényelmes, beleértve a lemezek és a megfelelő VHD-blobok listáját az aktuális virtuális gép adatait.

Az alkalmazás előkészítése az állásidő. Egy tiszta az áttelepítés végrehajtásához, akkor az összes feldolgozó leállítása a jelenlegi rendszerben. Csak ezután beszerezheti azt áttelepítheti az új platformra a konzisztens állapotba. Állásidő időtartama áttelepíteni a lemezeken lévő adatok mennyisége függ.

> [!NOTE]
> Ha egy Azure Resource Manager virtuális gép létrehozása speciális lemezről a VHD esetén olvassa el [ezzel a sablonnal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) a meglévő lemezt használó Resource Manager virtuális gép üzembe helyezése.
>
>

### <a name="register-your-vhd"></a>Regisztrálja a virtuális merevlemez
Virtuális gép létrehozása az operációs rendszer virtuális merevlemezből adatlemez csatolása egy új virtuális géphez, vagy először regisztrálnia kell őket. A virtuális merevlemez forgatókönyvtől függően az alábbi lépéseket követve.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Általános operációs rendszer virtuális Merevlemezből, több Azure-beli Virtuálisgép-példányok létrehozásához
Általános operációsrendszer-lemezkép VHD töltenek fel a tárfiók, miután regisztrálja, egy **Azure Virtuálisgép-lemezkép** úgy, hogy egy vagy több Virtuálisgép-példányok hozhat létre belőle. A következő PowerShell-parancsmagok segítségével regisztrálja a VHD-t egy Azure virtuális gép operációsrendszer-lemezképben. Adja meg a teljes körű URL-címet, ahol VHD-t másolta.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Másolja és mentse az új Azure Virtuálisgép-lemezkép neve. A fenti példában, a *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Egyedi operációs rendszer VHD egyetlen Azure-beli Virtuálisgép-példány létrehozása
Miután a tárfiók egyedi virtuális Rendszerlemez töltenek fel, regisztrálja, egy **Azure operációsrendszer-lemez** úgy, hogy, létrehozhat egy Virtuálisgép-példánnyal. Ezek a PowerShell-parancsmagok segítségével regisztrálja a VHD-t Azure operációsrendszer-lemezként. Adja meg a teljes körű URL-címet, ahol VHD-t másolta.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Másolja és mentse az új Azure-OS lemez nevét. A fenti példában, a *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Adatlemez virtuális Merevlemeze új Azure-beli Virtuálisgép-példány van csatlakoztatva
Miután a virtuális merevlemez adatlemez tárfiók töltenek fel, regisztrálja egy Azure-adatlemez, úgy, hogy az új DS-sorozat, DSv2 sorozat vagy GS sorozatú Azure-beli Virtuálisgép-példány csatolható.

Ezek a PowerShell-parancsmagok segítségével regisztrálja a VHD-t egy Azure-adatlemez. Adja meg a teljes körű URL-címet, ahol VHD-t másolta.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Másolja és mentse az új Azure-adatlemez neve. A fenti példában, a *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>A Premium Storage képes a virtuális gép létrehozása
Miután az operációsrendszer-képet vagy operációsrendszer-lemez van regisztrálva, hozzon létre egy új DS, DSv2-sorozat vagy GS sorozatú virtuális Gépet. Fogja használni az operációs rendszeri lemezkép vagy operációs rendszer lemezének neve, amely regisztrálta. Válassza ki a virtuális gép a Premium Storage szintről. Az alábbi példában használjuk a *Standard_DS2* Virtuálisgép-méretet.

> [!NOTE]
> Frissítse a lemez méretét, hogy megegyezzen a kapacitás és teljesítmény-követelmények és a rendelkezésre álló Azure lemezméretet.
>
>

Kövesse az új virtuális gép létrehozása a következő lépésről lépésre PowerShell-parancsmagok. Először állítsa be a következő általános paramétereket:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

Először hozzon létre egy felhőalapú szolgáltatás, amely üzemeltetni kívánt az új virtuális gépeket.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

Ezután a forgatókönyvtől függően hozzon létre az Azure-beli Virtuálisgép-példány a operációsrendszer-lemezkép vagy operációsrendszer-lemez, amely regisztrálta.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Általános operációs rendszer virtuális Merevlemezből, több Azure-beli Virtuálisgép-példányok létrehozásához
A egy vagy több új DS sorozatú Azure-beli Virtuálisgép-példányok létrehozásához használja a **Azure operációsrendszer-lemezkép** regisztrált. Új virtuális gép létrehozásakor, ahogy az alábbi, adja meg az operációs rendszer lemezképének nevét a Virtuálisgép-konfigurációt.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Egyedi operációs rendszer VHD egyetlen Azure-beli Virtuálisgép-példány létrehozása
Hozzon létre egy új DS sorozatú Azure virtuális gép példány a **Azure operációsrendszer-lemez** regisztrált. Az új virtuális gép létrehozásakor, ahogy az alábbi, adja meg az operációs rendszer lemezének neve a Virtuálisgép-konfigurációt.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Adjon meg más Azure-beli Virtuálisgép-adatokat, például a egy felhőszolgáltatás, a régió, a tárfiókot, a rendelkezésre állási csoport és a gyorsítótárazási házirend. Vegye figyelembe, hogy a Virtuálisgép-példány ugyanott kell lennie társított operációs rendszerrel vagy adatlemezeket, így a kiválasztott felhő, régió és a tárolási fiók összes kell ezeket a lemezeket a mögöttes VHD-ként megegyező helyen.

### <a name="attach-data-disk"></a>Adatlemez csatolása
Végül ha a regisztráció adatlemez virtuális merevlemezek csatolja őket az új prémium szintű tárolás képes Azure virtuális Gépen.

Adatlemez csatolása az új virtuális gépre, és adja meg a gyorsítótárazási házirend használja a következő PowerShell-parancsmagot. Az alábbi példában a gyorsítótárazási házirend értéke *ReadOnly*.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Előfordulhat, hogy további lépéseket, amely az alkalmazása támogatásához szükséges ez az útmutató nem vonatkoznak.
>
>

### <a name="checking-and-plan-backup"></a>Ellenőrzése és a biztonsági mentés tervezése
Miután az új virtuális gép működik-e, elérni az ugyanazon bejelentkezési azonosító használatával, és jelszó van, mint az eredeti virtuális gép, és ellenőrizze, hogy minden a várt módon működik. Az összes beállítás a csíkozott kötetek, beleértve az új virtuális gép jelen lehet.

Az utolsó lépés az, hogy a biztonsági mentési terv, és az új virtuális gép karbantartási ütemezés alapján az alkalmazás igényeinek megfelelően.

### <a name="a-sample-migration-script"></a>A példaszkript áttelepítése
Ha több virtuális gép áttelepítése, automation PowerShell-parancsprogramokkal hasznos lehet. Következő egy minta parancsfájlt, amely automatizálja a virtuális gépek a migrálás. Megjegyzés: alábbi szkriptet, amely csak egy példa, és nincsenek néhány előzetesen kalkulált aránnyal az aktuális Virtuálisgép-lemezeket. Előfordulhat, hogy frissíteni szeretné a parancsfájlt az adott forgatókönyvnek megfelelően.

Előfeltételek a következők:

* Klasszikus Azure-beli virtuális gépek létrehozásakor.
* A forrás operációsrendszer-lemezek és a forrás az adatlemezeket is ugyanazt a tárfiókot, és ugyanazt a tárolót. Ha az operációsrendszer-lemez és adatlemezek nem ugyanazon a helyen, az AzCopy vagy az Azure PowerShell használatával VHD-k másolja át a storage-fiókok és a tárolók. Tekintse meg az előző lépésből: [Másolja a VHD-AzCopy vagy a PowerShell használatával](#copy-vhd-with-azcopy-or-powershell). Ezt a szkriptet a forgatókönyv megfelelnek szerkesztését másik lehetőség, de javasoljuk, hogy könnyebben és gyorsabban, mert az AzCopy vagy a PowerShell használatával.

Az automation-szkript lejjebb találja. Szöveg cserélje le az adatokat, és frissítse a szkriptet az adott forgatókönyvnek megfelelően.

> [!NOTE]
> A meglévő parancsfájl használatával nem őrzi meg a forrás virtuális gép hálózati konfigurációját. Meg kell re-config a hálózati beállításokat az áttelepített virtuális gépeken.
>
>

```powershell
    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a VM from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source VM to the new storage account.
    And then it will create a new VM from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement, but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED "AS IS" WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location "Southeast Asia"

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    https://azure.microsoft.com/documentation/articles/powershell-install-configure/
    https://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in seconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module version" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article https://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you'd like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the VM manually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the source vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires deleting the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`t[Warning] You chose to copy data disks only. Moving VM requires removing the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. This is an irreversible action. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting until the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
            Start-Sleep -Seconds 10
            $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a VM from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more customization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location
```

#### <a name="optimization"></a>Optimalizálás
Az aktuális virtuális gép konfigurációjának kifejezetten a jól működnek a standard szintű lemezek szabható testre. Például sok lemezzel csíkozott kötetek használatával a teljesítmény növelése érdekében. Például ahelyett 4 lemezeket külön-külön a Premium Storage, előfordulhat, hogy kell egyetlen kellene optimalizálhatja a költségeket. Optimalizálási lehetőségek, például ezt kell eseti alapon kell kezelni, és egyéni lépések szükségesek a migrálás után. Továbbá vegye figyelembe, hogy ez a folyamat jól nem feltétlenül alkalmas adatbázisokat és alkalmazásokat, amelyek a lemezelrendezést a telepítő meghatározott függenek.

##### <a name="preparation"></a>Előkészítés
1. Az egyszerű áttelepítés elvégzéséhez, a korábbi szakaszban leírtak szerint. Optimalizálás az áttelepítés után az új virtuális gépen történik.
2. Adja meg az új lemezméretek, az optimalizált konfigurálásához szükséges.
3. Határozza meg az új lemez specifikációknak aktuális lemezek vagy kötetek leképezését.

##### <a name="execution-steps"></a>Végrehajtási lépések
1. Hozzon létre az új lemezt a Premium Storage virtuális gépek a megfelelő méretű.
2. Jelentkezzen be a virtuális gép, másolja az adatokat az új lemezhez, amely leképezi a kötetet a jelenlegi kötetről. Ehhez le kell képeznie egy új lemezt az aktuális kötetek esetén.
3. Ezután módosítsa az alkalmazás beállításait, váltson át az új lemezeket, és a régi kötetet leválasztani.

Alkalmazás teljesítményének optimalizálása szakaszt az olvassa el az alkalmazást a nagyobb teljesítményű lemezeket finomhangoláshoz, a [magas teljesítmény-központú tervezés](../../virtual-machines/windows/premium-storage-performance.md) cikk.

### <a name="application-migrations"></a>Alkalmazások áttelepítése
Adatbázisok és egyéb összetett alkalmazásokat lehet szükség különleges lépések szerint az alkalmazás szolgáltatója az áttelepítés. Olvassa el a megfelelő alkalmazás dokumentációban. Például általában adatbázisok biztonsági másolat használatával telepíthetők át, és állítsa vissza.

## <a name="next-steps"></a>További lépések
Bizonyos forgatókönyvek esetén a virtuális gépek áttelepítése a következő forrásanyagokban talál:

* [Az Azure Virtual Machines, Storage-fiókok közötti migrálása](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Hozzon létre, és a Windows Server VHD feltöltése az Azure-bA.](../../virtual-machines/windows/upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Létrehozásával és a egy Linux VHD feltöltése az Azure-bA](../../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Virtuális gépek áttelepítése az Amazon AWS, a Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Azt is ellenőrizze, tudjon meg többet az Azure Storage és az Azure Virtual Machines az alábbi forrásanyagokat:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Válassza ki a lemez típusát IaaS virtuális gépekhez](../../virtual-machines/windows/disks-types.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: https://technet.microsoft.com/library/hh831739.aspx
