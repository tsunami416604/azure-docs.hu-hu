---
title: Virtuális gépek migrálása az Azure Premium Storageba | Microsoft Docs
description: Telepítse át a meglévő virtuális gépeket az Azure Premium Storageba. A Premium Storage nagy teljesítményű, kis késleltetésű lemezes támogatást biztosít az Azure-Virtual Machines futó I/O-igényes számítási feladatokhoz.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/27/2017
ms.author: rogarana
ms.reviewer: yuemlu
ms.subservice: common
ms.openlocfilehash: 7cb5a335af7093bc217578d57340b03b8b9c08b3
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748348"
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Áttelepítés az Azure Premium Storageba (nem felügyelt lemezekre)

> [!NOTE]
> Ez a cikk azt ismerteti, hogyan telepítheti át a nem felügyelt standard lemezeket használó virtuális gépeket a nem felügyelt prémium szintű lemezeket használó virtuális gépekre. Azt javasoljuk, hogy az Azure Managed Disks-t az új virtuális gépekhez használja, és az előző nem felügyelt lemezeket felügyelt lemezekre alakítsa át. Managed Disks kezeli a mögöttes Storage-fiókokat, hogy ne kelljen. További információkért tekintse meg a [Managed Disks áttekintését](../../virtual-machines/windows/managed-disks-overview.md).
>

Az Azure Premium Storage nagy teljesítményű, kis késleltetésű lemezes támogatást biztosít az I/O-igényes számítási feladatokat futtató virtuális gépek számára. Igénybe veheti a lemezek sebességét és teljesítményét azáltal, hogy az alkalmazás virtuálisgép-lemezeit áttelepíti az Azure Premium Storageba.

Ennek az útmutatónak a célja, hogy segítse az Azure új felhasználóinak Premium Storage a zökkenőmentes áttérést a jelenlegi rendszerről a Premium Storagera. Az útmutató a folyamat három fő összetevőjével foglalkozik:

* [A Premium Storagere való Migrálás megtervezése](#plan-the-migration-to-premium-storage)
* [Virtuális merevlemezek (VHD-k) előkészítése és másolása Premium Storagere](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Azure-beli virtuális gép létrehozása Premium Storage használatával](#create-azure-virtual-machine-using-premium-storage)

Áttelepítheti a virtuális gépeket más platformokról az Azure-ba Premium Storage vagy áttelepítheti a meglévő Azure-beli virtuális gépeket a standard szintű tárolóból Premium Storage Ez az útmutató két forgatókönyv lépéseit ismerteti. A forgatókönyvtől függően kövesse a vonatkozó szakaszban leírt lépéseket.

> [!NOTE]
> A prémium szintű SSD-k funkcióinak áttekintését és díjszabását itt találja: [válassza ki a IaaS virtuális gépekhez tartozó lemez típusát](../../virtual-machines/windows/disks-types.md#premium-ssd). Javasoljuk, hogy az alkalmazás legjobb teljesítményének érdekében az Azure-Premium Storage magas IOPS igénylő virtuálisgép-lemez áttelepítését. Ha a lemez nem igényel magas IOPS, a költségeket a standard szintű tárolóban is korlátozhatja, amely a virtuális gépek lemezes adatait merevlemez-meghajtókon (HDD-k) tárolja SSD-k helyett.
>

Az áttelepítési folyamat teljes egészében az útmutatóban ismertetett lépések előtt és után további műveleteket is igényelhet. Ilyenek például a virtuális hálózatok vagy végpontok konfigurálása, illetve a kód módosítása az alkalmazáson belül, ami bizonyos állásidőt igényelhet az alkalmazásban. Ezek a műveletek minden alkalmazás esetében egyediek, és ezeket a jelen útmutatóban ismertetett lépéseket követve a lehető legzökkenőmentesebb teheti a teljes átállást Premium Storage.

## <a name="plan-the-migration-to-premium-storage"></a>A Premium Storagere való Migrálás megtervezése
Ez a szakasz gondoskodik arról, hogy készen álljon a cikk áttelepítési lépéseinek követésére, és segít a legjobb döntést hozni a virtuális gépek és a lemezek típusairól.

### <a name="prerequisites"></a>Előfeltételek
* A szolgáltatáshoz Azure-előfizetés szükséges. Ha még nem rendelkezik ilyennel, a további lehetőségekért hozzon létre egy hónapos [ingyenes próbaverziós](https://azure.microsoft.com/pricing/free-trial/) előfizetést, vagy látogasson el az [Azure díjszabására](https://azure.microsoft.com/pricing/) .
* A PowerShell-parancsmagok végrehajtásához szüksége lesz a Microsoft Azure PowerShell modulra. A telepítési helyre és a telepítésre vonatkozó utasításokért lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).
* Ha Premium Storageon futó Azure-beli virtuális gépeket szeretne használni, akkor a Premium Storage-kompatibilis virtuális gépeket kell használnia. A Premium Storage-kompatibilis virtuális gépekkel szabványos és Premium Storage lemezeket is használhat. A Premium Storage-lemezek a jövőben több virtuálisgép-típussal lesznek elérhetők. Az összes elérhető Azure-beli virtuálisgép-lemez típusával és méretével kapcsolatos további információkért lásd: [virtuális gépek](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) és [méretek mérete Cloud Services számára](../../cloud-services/cloud-services-sizes-specs.md).

### <a name="considerations"></a>Megfontolandó szempontok
Az Azure-beli virtuális gépek több Premium Storage lemez csatlakoztatását is lehetővé teszik, így az alkalmazások virtuális gépenként akár 256 TB tárhellyel rendelkezhetnek. Az Premium Storage segítségével az alkalmazások másodpercenként 80 000 IOPS-t (bemeneti/kimeneti műveletek másodpercenként) és 2000 MB/s sebességű átviteli sebességet érhetnek el a virtuális gépen, rendkívül alacsony késéssel az olvasási műveletekhez. A különböző virtuális gépek és lemezek számos lehetőséggel rendelkeznek. Ez a szakasz segítséget nyújt a számítási feladatokhoz legjobban illő lehetőségek megtalálásában.

#### <a name="vm-sizes"></a>Virtuálisgép-méretek
Az Azure-beli virtuális gép méretének specifikációi a [virtuális gépek méretei](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)szerint vannak felsorolva. Tekintse át a Premium Storaget használó virtuális gépek teljesítményének jellemzőit, és válassza ki a legmegfelelőbb virtuálisgép-méretet, amely legjobban megfelel a számítási feladatnak. Győződjön meg arról, hogy elegendő sávszélesség áll rendelkezésre a virtuális gépen a lemez forgalmának elvégzéséhez.

#### <a name="disk-sizes"></a>Lemezméretek
A virtuális géppel öt típusú lemezt lehet használni, és mindegyikhez egyedi IOPs és átviteli sebességre vonatkozó korlátozások tartoznak. Vegye figyelembe ezeket a korlátokat, amikor az alkalmazás igényeinek megfelelően kiválasztja a lemez típusát a kapacitás, a teljesítmény, a méretezhetőség és a maximális terhelés tekintetében.

| Prémium szintű lemezek típusa  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Lemezméret           | 128 GB| 512 GB| 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 
| Percenkénti IO-műveletek lemezenként       | 500   | 2300  | 5000           | 7500           | 7500           | 
| Adattovábbítás lemezenként | 100 MB/s | 150 MB/másodperc | 200 MB/másodperc | 250 MB/másodperc | 250 MB/másodperc |

A számítási feladattól függően állapítsa meg, hogy szükség van-e további adatlemezekre a virtuális géphez. A virtuális géphez több állandó adatlemez is csatolható. Ha szükséges, a lemezeket a kötetek kapacitásának és teljesítményének növelésével is elvégezheti. (Lásd: mi a lemez csíkozása [.)](../../virtual-machines/windows/premium-storage-performance.md#disk-striping) Ha a [tárolóhelyek][4]használatával szalagos Premium Storage adatlemezeket, akkor minden egyes használt lemezhez egy oszloppal kell konfigurálnia. Ellenkező esetben a csíkozott kötet általános teljesítménye a vártnál kevesebb lehet, mert a forgalom a lemezeken nem egyenletes eloszlású. Linux rendszerű virtuális gépek esetén a *mdadm* segédprogram használatával is elérheti ugyanezt. A részletekért tekintse [meg a szoftveres RAID konfigurálása Linuxon](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) című cikket.

#### <a name="storage-account-scalability-targets"></a>A Storage-fiók skálázhatósági céljai

Premium Storage fiókok a következő skálázhatósági célokkal rendelkeznek. Ha az alkalmazásra vonatkozó követelmények meghaladják egy adott Storage-fiók skálázhatósági céljait, az alkalmazást több Storage-fiók használatára kell felépíteni, és ezeket a tárolási fiókokba particionálni.

| Teljes fiók kapacitása | Teljes sávszélesség egy helyileg redundáns Storage-fiókhoz |
|:--- |:--- |
| Lemez kapacitása: 35TB<br />Pillanatkép kapacitása: 10 TB |Akár 50 Gigabit másodpercenként a bejövő és kimenő forgalomhoz |

A Premium Storage specifikációkkal kapcsolatos további információkért lásd: [a prémium szintű blob Storage-fiókok méretezhetőségi célpontjai](../blobs/scalability-targets-premium-page-blobs.md).

#### <a name="disk-caching-policy"></a>Lemezes gyorsítótárazási házirend
Alapértelmezés szerint a lemezes gyorsítótárazási házirend a prémium szintű adatlemezek esetében *csak olvasható* , és a virtuális géphez csatlakoztatott prémium operációsrendszer-lemezre írható *írás* . Ez a konfigurációs beállítás ajánlott az alkalmazás IOs-es optimális teljesítményének eléréséhez. A írható vagy írható adatlemezek (például SQL Server naplófájlok) esetében tiltsa le a lemezes gyorsítótárazást, hogy jobban elérhető legyen az alkalmazás teljesítménye. A meglévő adatlemezek gyorsítótár-beállításai a *set-AzureDataDisk* parancsmag [Azure Portal](https://portal.azure.com) vagy a *-HostCaching* paraméterének használatával frissíthetők.

#### <a name="location"></a>Földrajzi egység
Válasszon egy helyet, ahol az Azure Premium Storage elérhető. Tekintse meg az [Azure-szolgáltatások régiónként](https://azure.microsoft.com/regions/#services) az elérhető helyszínekről szóló naprakész információkat. A virtuális gép lemezeit tároló Storage-fiókkal azonos régióban lévő virtuális gépek sokkal jobb teljesítményt biztosítanak, mint ha különálló régiókban találhatók.

#### <a name="other-azure-vm-configuration-settings"></a>Egyéb Azure-beli virtuális gép konfigurációs beállításai
Egy Azure-beli virtuális gép létrehozásakor a rendszer kérni fogja, hogy konfigurálja a virtuális gépek beállításait. Ne feledje, hogy a virtuális gép élettartama során néhány beállítás rögzített, míg később módosíthatja vagy hozzáadhatja a többi beállítást. Tekintse át ezeket az Azure-beli virtuális gépek konfigurációs beállításait, és győződjön meg arról, hogy ezek megfelelően vannak konfigurálva a számítási feladatok követelményeinek megfelelően.

### <a name="optimization"></a>Optimalizálás
[Azure Premium Storage: a nagy teljesítmény kialakításához](../../virtual-machines/windows/premium-storage-performance.md) útmutatást nyújt a nagy teljesítményű alkalmazások létrehozásához az Azure Premium Storage használatával. Az irányelveket az alkalmazás által használt technológiákra vonatkozó ajánlott eljárások és teljesítmények együttes használatával követheti nyomon.

## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Virtuális merevlemezek (VHD-k) előkészítése és másolása Premium Storagere
A következő szakasz útmutatást nyújt a VHD-k virtuális gépről történő előkészítéséhez és a VHD-k Azure Storage-ba történő másolásához.

* [1. forgatókönyv: "a meglévő Azure-beli virtuális gépek áttelepítése az Azure Premium Storageba."](#scenario1)
* [2. forgatókönyv: "a virtuális gépek áttelepítése más platformokról az Azure Premium Storageba."](#scenario2)

### <a name="prerequisites"></a>Előfeltételek
A VHD-k áttelepítésre való előkészítéséhez a következőkre lesz szüksége:

* Egy Azure-előfizetést, egy Storage-fiókot és egy olyan tárolót, amely a virtuális merevlemez másolására használható. Vegye figyelembe, hogy a célként megadott Storage-fiók a követelménytől függően szabványos vagy Premium Storage fiók lehet.
* Egy eszköz, amely általánosítja a VHD-t, ha több virtuálisgép-példányt szeretne létrehozni belőle. Például: Sysprep for Windows vagy virt-Sysprep for Ubuntu.
* Egy eszköz, amellyel feltöltheti a VHD-fájlt a Storage-fiókba. Lásd: [adatok átvitele a AzCopy parancssori segédprogrammal](storage-use-azcopy.md) vagy [Azure Storage Explorer](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)használata. Ez az útmutató a virtuális merevlemez másolását ismerteti a AzCopy eszköz használatával.

> [!NOTE]
> Ha a szinkron másolás lehetőséget választja a AzCopy, akkor az optimális teljesítmény érdekében másolja a virtuális merevlemezt egy olyan Azure-beli virtuális gépről, amely ugyanabban a régióban található, mint a célként megadott Storage-fiók. Ha a virtuális merevlemezt egy másik régióban lévő Azure-beli virtuális gépről másolja, a teljesítmény lassabb lehet.
>
> Nagy mennyiségű adat korlátozott sávszélességre való másolásához érdemes lehet [Az Azure import/export szolgáltatást használni az adatok blob Storageba való átviteléhez](../storage-import-export-service.md). Ez lehetővé teszi az adatok átvitelét a merevlemez-meghajtók Azure-adatközpontba való szállításával. Az Azure import/export szolgáltatással csak a standard szintű Storage-fiókba másolhatók az adatsorok. Ha az adatok szerepelnek a standard Storage-fiókban, a [Másolás blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx) vagy a AzCopy használatával viheti át az adatait a Premium Storage-fiókjába.
>
> Vegye figyelembe, hogy Microsoft Azure csak a rögzített méretű VHD-fájlokat támogatja. A VHDX-fájlok vagy a dinamikus VHD-k nem támogatottak. Ha dinamikus virtuális merevlemezt használ, a [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) parancsmaggal konvertálhatja a rögzített méretűre.
>
>

### <a name="scenario1"></a>1. forgatókönyv: "a meglévő Azure-beli virtuális gépek áttelepítése az Azure Premium Storageba."
Ha meglévő Azure-beli virtuális gépeket telepít át, állítsa le a virtuális gépet, készítse elő a VHD-ket a kívánt virtuális merevlemez típusától, majd másolja a VHD-t a AzCopy vagy a PowerShell-lel.

A virtuális gépet teljesen le kell állítani a tiszta állapot áttelepítéséhez. Az áttelepítés befejezése után a rendszer leállást hajt végre.

#### <a name="step-1-prepare-vhds-for-migration"></a>1\. lépés Virtuális merevlemezek előkészítése áttelepítésre
Ha meglévő Azure-beli virtuális gépeket telepít át Premium Storagere, a VHD-je a következő lehet:

* Általános operációsrendszer-rendszerkép
* Az operációs rendszer egyedi lemeze
* Adatlemez

Az alábbiakban áttekintjük a virtuális merevlemez előkészítésének 3 forgatókönyvét.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Általánosított operációs rendszer virtuális merevlemezének használata több virtuálisgép-példány létrehozásához
Ha olyan virtuális merevlemezt tölt fel, amely több általános Azure-beli virtuálisgép-példány létrehozásához lesz felhasználva, először a VHD-t kell általánosítani a Sysprep segédprogram használatával. Ez a helyszínen vagy a felhőben található virtuális merevlemezre vonatkozik. A Sysprep eltávolítja a virtuálisgép-specifikus adatokat a VHD-ből.

> [!IMPORTANT]
> Az általánosítás előtt készítsen pillanatképet vagy készítsen biztonsági másolatot a virtuális gépről. A Sysprep futtatása leállítja és felszabadítja a virtuálisgép-példányt. Kövesse az alábbi lépéseket a Sysprep a Windows operációs rendszer virtuális merevlemezén. Vegye figyelembe, hogy a Sysprep parancs futtatásához le kell állítania a virtuális gépet. További információ a Sysprep eszközről: a [Sysprep áttekintése](https://technet.microsoft.com/library/hh825209.aspx) vagy a [Sysprep műszaki útmutatója](https://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Nyisson meg egy parancssori ablakot rendszergazdaként.
2. A Sysprep megnyitásához írja be a következő parancsot:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. A rendszer-előkészítő eszközben jelölje be a rendszerindítási folyamat (OOBE) megadása elemet, jelölje be az általánosítás jelölőnégyzetet, válassza a **Leállítás**lehetőséget, majd kattintson az **OK**gombra az alábbi képen látható módon. A Sysprep általánosítja az operációs rendszert, és leállítja a rendszert.

    ![][1]

Ubuntu rendszerű virtuális gép esetén a virt-Sysprep használatával is elérheti ugyanezt. További részletekért lásd: [virt-Sysprep](https://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) . Lásd még a nyílt forráskódú Linux- [kiszolgáló kiépítési szoftverét](https://www.cyberciti.biz/tips/server-provisioning-software.html) más Linux operációs rendszerekhez.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Egyedi operációs rendszer virtuális merevlemezének használata egyetlen virtuálisgép-példány létrehozásához
Ha a virtuális gépen olyan alkalmazás fut, amelyhez a gép specifikusan szükséges, ne általánosítsa a VHD-t. A nem általánosított virtuális merevlemezek egyedi Azure VM-példányok létrehozására használhatók. Ha például a virtuális merevlemezen van tartományvezérlő, a Sysprep végrehajtása a tartományvezérlőként hatástalan lesz. Tekintse át a virtuális gépen futó alkalmazásokat, és a virtuális merevlemez általánosítása előtt a Sysprep futtatásának következményeit.

##### <a name="register-data-disk-vhd"></a>Adatlemez VHD-fájljának regisztrálása
Ha az Azure-ban adatlemezeket szeretne áttelepíteni, győződjön meg arról, hogy az ezeket az adatlemezeket használó virtuális gépek le vannak állítva.

Az alábbi lépéseket követve másolja a VHD-t az Azure Premium Storageba, és regisztrálja kiépített adatlemezként.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>2\. lépés A virtuális merevlemez célhelyének létrehozása
Hozzon létre egy Storage-fiókot a virtuális merevlemezek karbantartásához. A virtuális merevlemezek tárolási helyének megtervezése során vegye figyelembe a következő szempontokat:

* A cél Premium Storage-fiók.
* A Storage-fiók helyének meg kell egyeznie Premium Storage a végső szakaszban létrehozandó Azure-beli virtuális gépekkel. Egy új Storage-fiókba másolhatja, vagy megtervezheti, hogy az igényeinek megfelelően ugyanazt a Storage-fiókot használja.
* Másolja és mentse a cél Storage-fiókhoz tartozó Storage-fiók kulcsát a következő szakaszra.

Adatlemezek esetén dönthet úgy, hogy egy standard Storage-fiókban tárolja az adatlemezeket (például a hidegebb tárolóval rendelkező lemezeket), de javasoljuk, hogy az éles számítási feladatokhoz az összes adatát a Premium Storage használatára helyezze át.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>3. lépés. Virtuális merevlemez másolása a AzCopy vagy a PowerShell használatával
A két lehetőség egyikének feldolgozásához meg kell találnia a tároló elérési útját és a Storage-fiók kulcsát. A tároló elérési útja és a Storage-fiók kulcsa megtalálható az **Azure Portal** > **Storage**szolgáltatásban. A tároló URL-címe "https:\//myaccount.blob.core.windows.net/mycontainer/" lesz.

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>1\. lehetőség: virtuális merevlemez másolása AzCopy (aszinkron másolással)

A AzCopy használatával könnyedén feltöltheti a virtuális merevlemezt az interneten keresztül. A virtuális merevlemezek méretétől függően ez időt is igénybe vehet. Ha ezt a beállítást használja, ne felejtse el megnézni a Storage-fiók bejövő/kimenő korlátait. A részletekért tekintse [meg a standard Storage-fiókok méretezhetőségi és teljesítménybeli céljait](scalability-targets-standard-account.md) .

1. Töltse le és telepítse a AzCopy-t innen: [a AzCopy legújabb verziója](https://aka.ms/downloadazcopy)
2. Nyissa meg Azure PowerShellt, és keresse meg azt a mappát, ahol a AzCopy telepítve van.
3. A következő paranccsal másolja át a VHD-fájlt a "forrás" értékről a "cél" értékre.

   ```azcopy
   AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
   ```

    Példa:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```
 
   Itt láthatók a AzCopy parancsban használt paraméterek leírása:

   * **/Source:** _&lt;forrás&gt;:_ a virtuális merevlemezt tartalmazó mappa vagy tárolási tároló URL-címe.
   * **/SourceKey:** _&lt;forrás-fiók-kulcs&gt;:_ a forrás Storage-fiókhoz tartozó Storage-fiók kulcsa.
   * **/Dest:** _&lt;cél&gt;:_ a tároló URL-címe a virtuális merevlemez másolásához.
   * **/DestKey:** _&lt;megcélzott-account-Key&gt;:_ a cél Storage-fiókhoz tartozó Storage-fiók kulcsa.
   * **/Pattern:** _&lt;file-Name&gt;:_ adja meg a másolandó vhd-fájl nevét.

További információ a AzCopy eszköz használatáról: [adatok átvitele a AzCopy parancssori segédprogrammal](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>2\. lehetőség: virtuális merevlemez másolása PowerShell-lel (szinkronizált másolat)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A VHD-fájlt a Start-AzStorageBlobCopy PowerShell-parancsmag használatával is másolhatja. A virtuális merevlemez másolásához használja az alábbi parancsot Azure PowerShell. Cserélje le a < > értékeit a forrás és a cél Storage-fiók megfelelő értékeire. A parancs használatához rendelkeznie kell egy VHD nevű tárolóval a cél Storage-fiókban. Ha a tároló nem létezik, hozzon létre egyet a parancs futtatása előtt.

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

### <a name="scenario2"></a>2. forgatókönyv: "a virtuális gépek áttelepítése más platformokról az Azure Premium Storageba."
Ha a virtuális merevlemezt nem Azure-beli felhőalapú tárterületről az Azure-ba telepíti át, először exportálnia kell a virtuális merevlemezt egy helyi könyvtárba. Adja meg a helyi könyvtár teljes forrásának elérési útját, ahol a VHD tárolása hasznos, majd a AzCopy használatával töltse fel az Azure Storage-ba.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>1\. lépés VHD exportálása helyi könyvtárba
##### <a name="copy-a-vhd-from-aws"></a>Virtuális merevlemez másolása AWS-ből
1. Ha AWS-t használ, exportálja a EC2 példányt egy virtuális merevlemezre egy Amazon S3-gyűjtőben. Kövesse az Amazon EC2-példányok exportálásának Amazon-dokumentációjában leírt lépéseket az Amazon EC2 parancssori felület (CLI) eszköz telepítéséhez, és futtassa a Create-instance-export-Task parancsot a EC2-példány VHD-fájlba való exportálásához. A **create-instance-export-Task** parancs&#95;futtatásakor ügyeljen arra, hogy a&#95;lemezkép formázása változóhoz a **VHD** -t használja. Az exportált VHD-fájl a folyamat során kijelölt Amazon S3 gyűjtőben lesz mentve.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Töltse le a VHD-fájlt az S3 gyűjtőből. Válassza ki a VHD-fájlt, majd a **műveletek** > a **Letöltés**lehetőséget.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Virtuális merevlemez másolása más nem Azure-beli felhőből
Ha a virtuális merevlemezt nem Azure-beli felhőalapú tárterületről az Azure-ba telepíti át, először exportálnia kell a virtuális merevlemezt egy helyi könyvtárba. Másolja a virtuális merevlemezt tároló helyi könyvtár teljes forrásának elérési útját.

##### <a name="copy-a-vhd-from-on-premises"></a>Virtuális merevlemez másolása a helyszíni rendszerből
Ha a virtuális merevlemezt egy helyszíni környezetből telepíti át, szüksége lesz a teljes forrás elérési útra, ahol a VHD tárolódik. A forrás elérési útja lehet kiszolgáló helye vagy fájlmegosztás.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>2\. lépés A virtuális merevlemez célhelyének létrehozása
Hozzon létre egy Storage-fiókot a virtuális merevlemezek karbantartásához. A virtuális merevlemezek tárolási helyének megtervezése során vegye figyelembe a következő szempontokat:

* A célként megadott Storage-fiók a standard vagy a Premium Storage lehet az alkalmazásra vonatkozó követelménytől függően.
* A Storage-fiók régiójának meg kell egyeznie Premium Storage a végső szakaszban létrehozandó Azure-beli virtuális gépekkel. Egy új Storage-fiókba másolhatja, vagy megtervezheti, hogy az igényeinek megfelelően ugyanazt a Storage-fiókot használja.
* Másolja és mentse a cél Storage-fiókhoz tartozó Storage-fiók kulcsát a következő szakaszra.

Javasoljuk, hogy az éles számítási feladatokhoz az összes adatát a Premium Storage használatára helyezze át.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>3\. lépés A VHD feltöltése az Azure Storage-ba
Most, hogy a virtuális merevlemez a helyi könyvtárban van, a AzCopy vagy a AzurePowerShell használatával feltöltheti a. vhd-fájlt az Azure Storage-ba. Mindkét lehetőséget itt találja:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>1\. lehetőség: az Azure PowerShell Add-AzureVhd használatával töltse fel a. vhd fájlt.

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

\<URI-> például **_"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"_** lehet. \<FileInfo-> például **_"C:\path\to\upload.vhd"_** lehet.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>2\. lehetőség: a AzCopy használata a. vhd fájl feltöltéséhez

A AzCopy használatával könnyedén feltöltheti a virtuális merevlemezt az interneten keresztül. A virtuális merevlemezek méretétől függően ez időt is igénybe vehet. Ha ezt a beállítást használja, ne felejtse el megnézni a Storage-fiók bejövő/kimenő korlátait. A részletekért tekintse [meg a standard Storage-fiókok méretezhetőségi és teljesítménybeli céljait](scalability-targets-standard-account.md) .

1. Töltse le és telepítse a AzCopy-t innen: [a AzCopy legújabb verziója](https://aka.ms/downloadazcopy)
2. Nyissa meg Azure PowerShellt, és keresse meg azt a mappát, ahol a AzCopy telepítve van.
3. A következő paranccsal másolja át a VHD-fájlt a "forrás" értékről a "cél" értékre.

   ```azcopy
      AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
   ```

   Példa:

   ```azcopy
      AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
   ```

   Itt láthatók a AzCopy parancsban használt paraméterek leírása:

   * **/Source:** _&lt;forrás&gt;:_ a virtuális merevlemezt tartalmazó mappa vagy tárolási tároló URL-címe.
   * **/SourceKey:** _&lt;forrás-fiók-kulcs&gt;:_ a forrás Storage-fiókhoz tartozó Storage-fiók kulcsa.
   * **/Dest:** _&lt;cél&gt;:_ a tároló URL-címe a virtuális merevlemez másolásához.
   * **/DestKey:** _&lt;megcélzott-account-Key&gt;:_ a cél Storage-fiókhoz tartozó Storage-fiók kulcsa.
   * **/BlobType: oldal:** Megadja, hogy a cél egy oldal blobja.
   * **/Pattern:** _&lt;file-Name&gt;:_ adja meg a másolandó vhd-fájl nevét.

További információ a AzCopy eszköz használatáról: [adatok átvitele a AzCopy parancssori segédprogrammal](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>A virtuális merevlemezek feltöltésének egyéb lehetőségei
A VHD-t a következő módon is feltöltheti a Storage-fiókjába:

* [Azure Storage másolási blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Blobok feltöltése Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)
* [Tároló importálási/exportálási szolgáltatásának REST API referenciája](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> Az import/export szolgáltatást javasoljuk, ha a becsült feltöltési idő hosszabb 7 napnál. A [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) segítségével megbecsülheti az időt az adatok méretétől és az adatátviteli egységtől.
>
> Az importálási/exportálási szolgáltatás a standard Storage-fiókba való másolásra használható. A standard Storage-ból prémium szintű Storage-fiókra kell másolnia egy olyan eszközzel, mint az AzCopy.
>
>

## <a name="create-azure-virtual-machine-using-premium-storage"></a>Azure-beli virtuális gépek létrehozása Premium Storage használatával
Miután feltöltötte vagy átmásolta a VHD-t a kívánt Storage-fiókba, kövesse az ebben a szakaszban található utasításokat a virtuális merevlemez operációsrendszer-lemezképként vagy operációsrendszer-lemezként való regisztrálásához a forgatókönyvtől függően, majd hozzon létre egy VM-példányt. Az adatlemez virtuális merevlemeze a létrehozás után csatlakoztatható a virtuális géphez.
A szakasz végén egy minta áttelepítési parancsfájl is elérhető. Ez az egyszerű parancsfájl nem felel meg az összes forgatókönyvnek. Előfordulhat, hogy frissítenie kell a parancsfájlt, hogy az megfeleljen az adott forgatókönyvnek. Ha szeretné megtekinteni, hogy a parancsfájl a forgatókönyvre vonatkozik-e, tekintse meg az alábbi [példa áttelepítési parancsfájlt](#a-sample-migration-script).

### <a name="checklist"></a>Ellenőrzőlista
1. Várjon, amíg a VHD-lemezek másolása befejeződött.
2. Győződjön meg arról, hogy Premium Storage elérhető az áttelepíteni kívánt régióban.
3. Döntse el, hogy milyen új virtuálisgép-sorozatot fog használni. Premium Storage képesnek kell lennie, és a méretnek a régió rendelkezésre állása és igényei alapján kell lennie.
4. Döntse el, hogy pontosan melyik virtuálisgép-méretet fogja használni. A virtuális gép méretének elég nagynak kell lennie az adatlemezek számának támogatásához. Például Ha 4 adatlemezzel rendelkezik, a virtuális gépnek 2 vagy több magot kell tartalmaznia. Emellett érdemes lehet a teljesítmény, a memória és a hálózati sávszélesség igényének feldolgozására is.
5. Hozzon létre egy Premium Storage fiókot a célként megadott régióban. Ezt a fiókot fogja használni az új virtuális géphez.
6. A virtuális gép aktuális részletei kéznél legyenek, beleértve a lemezek listáját és a megfelelő VHD-blobokat.

Készítse elő az alkalmazást állásidőre. A tiszta áttelepítés végrehajtásához le kell állítania az összes feldolgozást az aktuális rendszeren. Ezt a lehetőséget csak akkor érheti el, ha konzisztens állapotba kerül, amelyet áttelepíthet az új platformra. Az állásidő időtartama az áttelepíteni kívánt lemezeken lévő adatok mennyiségétől függ.

> [!NOTE]
> Ha Azure Resource Manager virtuális gépet hoz létre egy speciális VHD-lemezről, tekintse meg [ezt a sablont](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) a Resource Manager-alapú virtuális gép meglévő lemez használatával történő üzembe helyezéséhez.
>
>

### <a name="register-your-vhd"></a>A virtuális merevlemez regisztrálása
Ha virtuális gépet szeretne létrehozni az operációs rendszer VHD-ről, vagy egy adatlemezt szeretne csatlakoztatni egy új virtuális géphez, először regisztrálnia kell őket. A VHD-forgatókönyvtől függően kövesse az alábbi lépéseket.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Általánosított operációs rendszer VHD-je több Azure VM-példány létrehozásához
Az általános operációsrendszer-rendszerkép VHD-fájljának a Storage-fiókba való feltöltése után regisztrálja Azure-beli virtuálisgép- **képként** , hogy létre tudja hozni egy vagy több virtuálisgép-példányt. Használja a következő PowerShell-parancsmagokat a virtuális merevlemez Azure-beli VM operációsrendszer-rendszerképként való regisztrálásához. Adja meg a teljes tároló URL-címét, ahová a VHD-t átmásolták.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Másolja és mentse az új Azure VM-rendszerkép nevét. A fenti példában ez a *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Egyedi operációs rendszer virtuális merevlemeze egyetlen Azure virtuálisgép-példány létrehozásához
Miután feltöltötte az egyedi operációs rendszer VHD-jét a Storage-fiókba, regisztrálja azt **Azure operációsrendszer-lemezként** , hogy létre lehessen hozni BELŐLE egy VM-példányt. Ezekkel a PowerShell-parancsmagokkal regisztrálhatja a VHD-t Azure operációsrendszer-lemezként. Adja meg a teljes tároló URL-címét, ahová a VHD-t átmásolták.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Másolja és mentse az új Azure operációsrendszer-lemez nevét. A fenti példában ez a *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Adatlemez virtuális merevlemeze, amely az új Azure VM-példány (ok) hoz csatlakoztatható
Miután feltöltötte az adatlemez VHD-jét a Storage-fiókba, regisztrálja Azure-adatlemezként, hogy az új DS-sorozathoz, a DSv2-sorozathoz vagy a GS Series Azure VM-példányhoz legyen csatolva.

Ezekkel a PowerShell-parancsmagokkal regisztrálhatja a VHD-t Azure-adatlemezként. Adja meg a teljes tároló URL-címét, ahová a VHD-t átmásolták.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Másolja és mentse az új Azure-adatlemez nevét. A fenti példában ez a *adatlemez*.

### <a name="create-a-premium-storage-capable-vm"></a>Premium Storage-kompatibilis virtuális gép létrehozása
Az operációs rendszer lemezképének vagy operációsrendszer-lemezének regisztrálása után hozzon létre egy új DS-sorozatú, DSv2 vagy GS sorozatú virtuális gépet. A regisztrált operációs rendszer lemezképét vagy az operációs rendszer lemezének nevét fogja használni. Válassza ki a virtuális gép típusát a Premium Storage szintjéből. Az alábbi példában az *Standard_DS2* virtuális gép méretét használjuk.

> [!NOTE]
> Frissítse a lemez méretét, hogy biztosan megfeleljen a kapacitásának és teljesítményének, valamint az elérhető Azure-lemez méretének.
>
>

Az új virtuális gép létrehozásához kövesse az alábbi PowerShell-parancsmagok lépéseit. Először állítsa be az általános paramétereket:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

Először hozzon létre egy felhőalapú szolgáltatást, amelyben az új virtuális gépeket fogja üzemeltetni.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

Ezután a forgatókönyvtől függően hozza létre az Azure VM-példányt a regisztrált operációsrendszer-lemezképből vagy operációsrendszer-lemezből.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Általánosított operációs rendszer VHD-je több Azure VM-példány létrehozásához
Hozzon létre egy vagy több új DS sorozatú Azure VM-példányt a regisztrált **Azure operációsrendszer-rendszerkép** használatával. Az új virtuális gép létrehozásakor adja meg az operációs rendszer rendszerképének nevét a virtuális gép konfigurációjában az alább látható módon.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Egyedi operációs rendszer virtuális merevlemeze egyetlen Azure virtuálisgép-példány létrehozásához
Hozzon létre egy új DS Series Azure VM-példányt a regisztrált **Azure operációsrendszer-lemezzel** . Adja meg az operációsrendszer-lemez nevét a virtuális gép konfigurációjában az új virtuális gép létrehozásakor az alábbi ábrán látható módon.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Adja meg az egyéb Azure-beli virtuálisgép-információkat, például a felhőalapú szolgáltatást, a régiót, a tárolási fiókot, a rendelkezésre állási készletet és a gyorsítótárazási házirendet Vegye figyelembe, hogy a virtuálisgép-példánynak a társított operációs rendszer vagy adatlemezek együttes elhelyezésével kell rendelkeznie, ezért a kiválasztott felhőalapú szolgáltatás, a régió és a Storage-fióknak ugyanazon a helyen kell lennie, mint a lemezek mögöttes VHD-fájljainak.

### <a name="attach-data-disk"></a>Adatlemez csatolása
Végül, ha regisztrálta az adatlemezek virtuális merevlemezeit, csatolja őket az új Premium Storage képes Azure-beli virtuális géphez.

A következő PowerShell-parancsmag használatával csatlakoztassa az adatlemezt az új virtuális géphez, és határozza meg a gyorsítótárazási házirendet. Az alábbi példában a gyorsítótárazási házirend *írásvédett*értékre van állítva.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Előfordulhat, hogy a jelen útmutatóban nem szereplő alkalmazás támogatásához további lépések szükségesek.
>
>

### <a name="checking-and-plan-backup"></a>Biztonsági mentés ellenőrzése és tervezése
Ha az új virtuális gép működik, és a felhasználó ugyanazzal a bejelentkezési azonosítóval és jelszóval fér hozzá, akkor az eredeti virtuális gép, és ellenőrizze, hogy minden a várt módon működik-e. Az új virtuális gépen az összes beállítás, a csíkozott kötetek is szerepelnek.

Az utolsó lépés az új virtuális gép biztonsági mentési és karbantartási ütemtervének megtervezése az alkalmazás igényei alapján.

### <a name="a-sample-migration-script"></a>Minta áttelepítési parancsfájl
Ha több virtuális gépet is át szeretne telepíteni, akkor a PowerShell-parancsfájlok használatával történő automatizálás hasznos lehet. A következő példa egy olyan parancsfájlt mutat be, amely automatizálja a virtuális gép áttelepítését. Vegye figyelembe, hogy az alábbi szkript csak példaként szolgál, és az aktuális virtuálisgép-lemezekkel kapcsolatos feltételezések is vannak. Előfordulhat, hogy frissítenie kell a parancsfájlt, hogy az megfeleljen az adott forgatókönyvnek.

A feltételezések a következők:

* Klasszikus Azure-beli virtuális gépeket hoz létre.
* A forrás operációsrendszer-lemezek és a forrásoldali adatlemezek ugyanabban a Storage-fiókban és azonos tárolóban találhatók. Ha az operációsrendszer-lemezek és adatlemezek nem ugyanazon a helyen találhatók, akkor a AzCopy vagy a Azure PowerShell használatával másolhat virtuális merevlemezeket a Storage-fiókokon és a tárolókban. Tekintse meg az előző lépést: [virtuális merevlemez másolása a AzCopy vagy a PowerShell használatával](#copy-vhd-with-azcopy-or-powershell). Ha úgy szerkeszti ezt a parancsfájlt, hogy az megfeleljen a forgatókönyvnek, akkor azt javasoljuk, hogy a AzCopy vagy a PowerShellt használja, mivel az egyszerűbb és gyorsabb.

Az Automation-szkriptet alább találja. Cserélje le a szöveget az adataira, és frissítse a szkriptet az adott forgatókönyvnek megfelelően.

> [!NOTE]
> A meglévő parancsfájl használata nem őrzi meg a forrásként szolgáló virtuális gép hálózati konfigurációját. Újra kell konfigurálnia az áttelepített virtuális gépek hálózati beállításait.
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
Előfordulhat, hogy a jelenlegi virtuálisgép-konfiguráció kifejezetten a standard szintű lemezekkel való működésre van szabva. Például a teljesítmény növeléséhez egy csíkozott kötet számos lemezének használatával. Például ahelyett, hogy 4 lemezt kellene külön használni a Premium Storageon, a költségeket egyetlen lemezzel is optimalizálhatja. Az ehhez hasonló optimalizálásokat eseti alapon kell kezelni, és az áttelepítés után egyéni lépéseket kell megkövetelni. Azt is vegye figyelembe, hogy ez a folyamat nem működik megfelelően a telepítőben definiált lemez-elrendezéstől függő adatbázisok és alkalmazások esetében.

##### <a name="preparation"></a>Előkészítés
1. Fejezze be az egyszerű áttelepítést az előző szakaszban leírtak szerint. A rendszer az áttelepítés után az új virtuális gépen hajtja végre az optimalizálást.
2. Adja meg az optimalizált konfigurációhoz szükséges új méretű lemezeket.
3. Határozza meg az aktuális lemezek/kötetek leképezését az új lemezre vonatkozó specifikációknak megfelelően.

##### <a name="execution-steps"></a>Végrehajtási lépések
1. Hozza létre az új lemezeket a megfelelő méretekkel a Premium Storage virtuális gépen.
2. Jelentkezzen be a virtuális gépre, és másolja az adatokat az aktuális kötetről az adott kötetre leképező új lemezre. Ezt az összes olyan aktuális kötet esetében tegye, amely új lemezre kell leképezni.
3. Ezután módosítsa az alkalmazás beállításait úgy, hogy az új lemezekre váltson, és válassza le a régi köteteket.

Az alkalmazás a jobb teljesítmény érdekében történő hangolásához tekintse meg az alkalmazások teljesítményének optimalizálása című szakaszt a [nagy teljesítményű cikk kialakításához](../../virtual-machines/windows/premium-storage-performance.md) .

### <a name="application-migrations"></a>Alkalmazások áttelepítése
Az adatbázisok és más összetett alkalmazások az alkalmazás szolgáltatója által az áttelepítés során meghatározott speciális lépéseket igényelhetnek. Tekintse meg a megfelelő alkalmazás dokumentációját. Például az adatbázisok általában biztonsági mentéssel és visszaállítással is áttelepíthetők.

## <a name="next-steps"></a>Következő lépések
A virtuális gépek áttelepítésére vonatkozó konkrét forgatókönyvek esetében tekintse meg a következő forrásokat:

* [Azure-Virtual Machines migrálása a Storage-fiókok között](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Windows Server rendszerű virtuális merevlemez létrehozása és feltöltése az Azure-ba.](../../virtual-machines/windows/upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Linuxos virtuális merevlemez létrehozása és feltöltése az Azure-ba](../../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Virtual Machines migrálása az Amazon AWS-ből a Microsoft Azureba](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Az Azure Storage szolgáltatással és az Azure Virtual Machinesával kapcsolatos további információkért tekintse meg a következő forrásokat is:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Válassza ki a IaaS virtuális gépekhez tartozó lemez típusát](../../virtual-machines/windows/disks-types.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: https://technet.microsoft.com/library/hh831739.aspx
