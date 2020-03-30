---
title: Virtuális gépek áttelepítése az Azure Premium Storage szolgáltatásba | Microsoft dokumentumok
description: Telepítse át meglévő virtuális gépeit az Azure Premium Storage-ba. A Prémium szintű storage nagy teljesítményű, alacsony késleltetésű lemeztámogatást nyújt az Azure virtuális gépeken futó I/O-igényű számítási feladatokhoz.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/27/2017
ms.author: rogarana
ms.reviewer: yuemlu
ms.subservice: common
ms.openlocfilehash: 7cb5a335af7093bc217578d57340b03b8b9c08b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75748348"
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Áttelepítés az Azure Premium Storage szolgáltatásba (nem felügyelt lemezek)

> [!NOTE]
> Ez a cikk ismerteti, hogyan telepítheti át a virtuális gép, amely nem felügyelt szabványos lemezeket használ egy nem felügyelt prémium szintű lemezeket használó virtuális gép. Azt javasoljuk, hogy az Azure felügyelt lemezek et használja az új virtuális gépekhez, és konvertálja a korábbi nem felügyelt lemezeket felügyelt lemezekké. A felügyelt lemezek kezelik az alapul szolgáló tárfiókokat, így önnek nem kell. További információt a [Felügyelt lemezek áttekintése című témakörben talál.](../../virtual-machines/windows/managed-disks-overview.md)
>

Az Azure Premium Storage nagy teljesítményű, alacsony késleltetésű lemeztámogatást nyújt az I/O-igényes számítási feladatokat futtató virtuális gépek számára. Ezek a lemezek sebességét és teljesítményét kihasználva az alkalmazás virtuálisgép-lemezeit az Azure Premium Storage-ba migrálja.

Ez az útmutató célja, hogy segítsen az Azure Premium Storage új felhasználóinak jobban felkészülni a jelenlegi rendszerről a prémium szintű storage-ra való zökkenőmentes áttérésre. Az útmutató a folyamat három kulcsfontosságú összetevőjével foglalkozik:

* [A prémium szintű tárhelyre való áttérés megtervezése](#plan-the-migration-to-premium-storage)
* [Virtuális merevlemezek (VHD-k) előkészítése és másolása a prémium szintű tárhelyre](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Azure virtuális gép létrehozása prémium szintű tárterület használatával](#create-azure-virtual-machine-using-premium-storage)

A virtuális gépeket más platformokról az Azure Premium Storage-ba telepítheti, vagy a meglévő Azure-virtuális gépeket a standard tárból a prémium szintű storage-ba. Ez az útmutató mindkét forgatókönyv lépéseit ismerteti. Kövesse a megfelelő szakaszban megadott lépéseket a forgatókönyvtől függően.

> [!NOTE]
> A prémium szintű SSD-k szolgáltatásáttekintését és díjszabását a következő ben találja: [Válasszon lemeztípust az IaaS virtuális gépekhez](../../virtual-machines/windows/disks-types.md#premium-ssd). Azt javasoljuk, hogy a nagy IOPS-t igénylő virtuálisgép-lemezeket az alkalmazás legjobb teljesítménye érdekében migrálja az Azure Premium Storage-ba. Ha a lemez nem igényel magas IOPS-t, korlátozhatja a költségeket, ha a Standard Storage szolgáltatásban tartja fenn, amely az SSD-k helyett merevlemez-meghajtókon (HDD-ken) tárolja a virtuális gép lemezadatait.
>

Az áttelepítési folyamat teljes befejezése további műveleteket igényelhet az útmutatóban leírt lépések előtt és után is. Ilyen például a virtuális hálózatok vagy végpontok konfigurálása, vagy kódmódosítások végrehajtása magában az alkalmazáson belül, ami némi állásidőt igényelhet az alkalmazásban. Ezek a műveletek egyediek az egyes alkalmazások, és el kell végeznie őket az ebben az útmutatóban megadott lépésekkel együtt, hogy a teljes átállás a prémium szintű storage a lehető legzökkenőmentesebb.

## <a name="plan-for-the-migration-to-premium-storage"></a><a name="plan-the-migration-to-premium-storage"></a>A prémium szintű tárhelyre való áttérés megtervezése
Ez a szakasz biztosítja, hogy készen áll a jelen cikkben ismertetett áttelepítési lépések követésére, és segít a legjobb döntés meghozatalában a virtuális gép és a lemeztípusok esetén.

### <a name="prerequisites"></a>Előfeltételek
* Rendelkeznie kell Azure-előfizetéssel. Ha még nem rendelkezik ilyen, létrehozhat egy egy hónapos ingyenes próba-előfizetést, vagy további lehetőségekért látogasson el az [Azure-díjszabásoldalra.](https://azure.microsoft.com/pricing/) [free trial](https://azure.microsoft.com/pricing/free-trial/)
* A PowerShell-parancsmagok végrehajtásához szüksége lesz a Microsoft Azure PowerShell-modulra. A telepítési helyre és a telepítésre vonatkozó utasításokért lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).
* Ha prémium szintű storage-alapú Azure-virtuális gépeket kíván használni, a prémium szintű storage-kompatibilis virtuális gépeket kell használnia. Standard és prémium szintű storage lemezek prémium szintű storage-kompatibilis virtuális gépekkel is használható. Prémium szintű tárolólemezek lesz elérhető több virtuálisgép-típusok a jövőben. Az összes rendelkezésre álló Azure VM-lemeztípusról és -méretről a [Virtuális gépek méretei](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) és [a Felhőszolgáltatások méretei](../../cloud-services/cloud-services-sizes-specs.md)című témakörben talál további információt.

### <a name="considerations"></a>Megfontolandó szempontok
Az Azure virtuális gép támogatja a több prémium szintű storage-lemez csatlakoztatását, így az alkalmazások virtuális gépenként akár 256 TB tárhellyel is rendelkezhetnek. A prémium szintű storage-kezelővel az alkalmazások 80 000 IOPS-t (bemeneti/kimeneti művelet másodpercenként) érhetnek el virtuális gépenként és 2000 MB/másodperces lemezátviteli-teljesítményt virtuális gépenként, rendkívül alacsony késleltetéssel az olvasási műveletekhez. Számos virtuális gép és lemez lehetőséget kínál. Ez a rész segít megtalálni a számítási feladatoknak leginkább megfelelő lehetőséget.

#### <a name="vm-sizes"></a>A virtuális gépek mérete
Az Azure virtuális gép méretspecifikációi a [virtuális gépek méretei](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ben vannak felsorolva. Tekintse át a prémium szintű storage-tal működő virtuális gépek teljesítményjellemzőit, és válassza ki a számítási feladatoknak leginkább megfelelő virtuális gépméretet. Győződjön meg arról, hogy elegendő sávszélesség áll rendelkezésre a virtuális gép a lemezforgalom növeléséhez.

#### <a name="disk-sizes"></a>Lemezméretek
Öt típusú lemezek, amelyek a virtuális gép, és mindegyik rendelkezik adott IP-k és átviteli korlátok. Vegye figyelembe ezeket a korlátokat, amikor kiválasztja a lemez típusát a virtuális gép igényei nek megfelelően az alkalmazás kapacitás, teljesítmény, méretezhetőség és a csúcs terhelések.

| Prémium szintű lemezek típusa  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Lemezméret           | 128 GB| 512 GB| 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 
| IOPS-érték lemezenként       | 500   | 2300  | 5000           | 7500           | 7500           | 
| Adattovábbítás lemezenként | 100 MB másodpercenként | 150 MB másodpercenként | 200 MB másodpercenként | 250 MB másodpercenként | 250 MB másodpercenként |

A számítási feladatoktól függően határozza meg, hogy szükség van-e további adatlemezekre a virtuális géphez. Több állandó adatlemezt is csatolhat a virtuális géphez. Szükség esetén csíkot szíthat a lemezekközött a kötet kapacitásának és teljesítményének növelése érdekében. (Lásd mi a Disk Striping [itt](../../virtual-machines/windows/premium-storage-performance.md#disk-striping).) Ha a Tárolóhelyek használatával csíkot használ a Prémium szintű tároló [adatlemezei,][4]akkor azt minden használt lemezhez egy oszloppal kell konfigurálnia. Ellenkező esetben a csíkozott kötet általános teljesítménye a vártnál alacsonyabb lehet a lemezek közötti forgalom egyenetlen eloszlása miatt. Linuxos virtuális gépek esetén használhatja a *mdadm* segédprogramot, hogy ugyanazt elérje. A részleteket a [SzoftverRAID konfigurálása Linuxon](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) című cikkben találja.

#### <a name="storage-account-scalability-targets"></a>Tárfiók méretezhetőségi céljai

Prémium szintű storage-fiókok a következő méretezhetőségi célokat rendelkezik. Ha az alkalmazás követelmények meghaladják a méretezhetőségi célok egyetlen tárfiók, hozzon létre az alkalmazást több tárfiókok használatához, és particionálja az adatokat azok között a tárfiókok között.

| Teljes számlakapacitás | Teljes sávszélesség egy helyileg redundáns tárfiókhoz |
|:--- |:--- |
| Lemez kapacitása: 35 TB<br />Pillanatkép kapacitása: 10 TB |Akár 50 gigabit másodpercenként bejövő + kimenő |

A prémium szintű storage-specifikációkkal kapcsolatos további információkért lásd: [Skálázhatósági célok a prémium szintű lapblob storage-fiókok.](../blobs/scalability-targets-premium-page-blobs.md)

#### <a name="disk-caching-policy"></a>Lemezgyorsítótárazási házirend
Alapértelmezés szerint a lemezgyorsítótárazási házirend *írásvédett* az összes prémium szintű adatlemezhez, és a virtuális géphez csatlakoztatott prémium szintű *operációsrendszer-lemez írás-olvasás.* Ez a konfigurációs beállítás ajánlott az alkalmazás IOs optimális teljesítményének eléréséhez. Írási vagy csak írásra alkalmas adatlemezek (például SQL Server naplófájlok) esetén tiltsa le a lemezgyorsítótárazást, hogy jobb alkalmazásteljesítményt érjen el. A meglévő adatlemezek gyorsítótár-beállításai frissíthetők az [Azure Portalon](https://portal.azure.com) vagy a *Set-AzureDataDisk* parancsmag *-HostCaching* paraméterével.

#### <a name="location"></a>Hely
Válasszon egy helyet, ahol az Azure Premium Storage elérhető. Az elérhető helyekről az [Azure-szolgáltatások régiónkénti](https://azure.microsoft.com/regions/#services) naprakész információiért tekintse meg az Azure-szolgáltatások at. A virtuális gép lemezeit tároló storage-fiókkal azonos régióban található virtuális gépek sokkal jobb teljesítményt nyújtanak, mint ha külön régiókban lennének.

#### <a name="other-azure-vm-configuration-settings"></a>Egyéb Azure Virtuálisgép-konfigurációs beállítások
Az Azure-beli virtuális gép létrehozásakor a rendszer megkéri bizonyos virtuális gép beállításainak konfigurálását. Ne feledje, hogy néhány beállítás a virtuális gép élettartama alatt van kijavítva, míg később módosíthatja vagy hozzáadhatmásokat. Tekintse át ezeket az Azure VM konfigurációs beállításokat, és győződjön meg arról, hogy ezek megfelelően vannak konfigurálva a számítási feladatok követelményeinek megfelelően.

### <a name="optimization"></a>Optimalizálás
[Azure Premium Storage: A Design for High Performance](../../virtual-machines/windows/premium-storage-performance.md) útmutatást nyújt a nagy teljesítményű alkalmazások Azure Premium Storage használatával történő létrehozásához. Az irányelveket az alkalmazás által használt technológiákra vonatkozó bevált teljesítményekkel kombinálva követheti.

## <a name="prepare-and-copy-virtual-hard-disks-vhds-to-premium-storage"></a><a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Virtuális merevlemezek (VHD-k) előkészítése és másolása a Prémium szintű tárolóba
A következő szakasz a virtuális gépekről származó virtuális gépek előkészítésére és a Virtuálisszámítógép-adatok Azure Storage-ba másolására vonatkozó irányelveket ismerteti.

* [1. forgatókönyv: "Áttelepítem a meglévő Azure-beli virtuális gépeket az Azure Premium Storage-ba."](#scenario1)
* [2. forgatókönyv: "A virtuális gépeket más platformokról az Azure Premium Storage-ba telepítem át."](#scenario2)

### <a name="prerequisites"></a>Előfeltételek
A virtuális gépek áttelepítésre való előkészítéséhez a következőkre van szükség:

* Egy Azure-előfizetés, egy tárfiók és egy tároló, amelya virtuális merevlemez másolhatja. Vegye figyelembe, hogy a céltárfiók lehet standard vagy prémium szintű tárfiók a követelménytől függően.
* A virtuális merevlemez általánosítására, ha azt tervezi, hogy több virtuálisgép-példányok belőle. Például sysprep for Windows vagy virt-sysprep ubuntu.
* A Virtuális merevlemez-fájl feltöltésének eszköze a Storage-fiókba. Lásd: [Adatátvitel az AzCopy parancssori segédprogrammal,](storage-use-azcopy.md) vagy használhat [azure-tárolókezelőt.](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) Ez az útmutató a virtuális merevlemez nek az AzCopy eszközzel történő másolását ismerteti.

> [!NOTE]
> Ha az AzCopy szinkron másolási beállítását választja, az optimális teljesítmény érdekében másolja a virtuális merevlemezt egy azure-beli virtuális gép egyikének futtatásával, amely ugyanabban a régióban található, mint a céltárfiók. Ha egy virtuális merevlemezt egy másik régióban lévő Azure-gépről másol, a teljesítmény lassabb lehet.
>
> Nagy mennyiségű adat korlátozott sávszélességen történő másolásához fontolja meg [az Azure import/exportálás szolgáltatás használatát az adatok Blob Storage-ba való átviteléhez;](../storage-import-export-service.md) ez lehetővé teszi az adatok átvitelét merevlemez-meghajtók azure-adatközpontba szállításával. Az Azure import/export szolgáltatás segítségével csak egy szabványos tárfiókba másolhat adatokat. Miután az adatok a normál tárfiókban vannak, használhatja a [Blob másolása API-t](https://msdn.microsoft.com/library/azure/dd894037.aspx) vagy az AzCopy-t az adatok prémium szintű tárfiókba való átviteléhez.
>
> Ne feledje, hogy a Microsoft Azure csak a rögzített méretű VHD-fájlokat támogatja. A VHDX-fájlok vagy a dinamikus virtuális merevlemezek nem támogatottak. Ha dinamikus virtuális merevlemeze van, a [Konvertálás-VHD](https://technet.microsoft.com/library/hh848454.aspx) parancsmag segítségével rögzített méretre konvertálhatja.
>
>

### <a name="scenario-1-i-am-migrating-existing-azure-vms-to-azure-premium-storage"></a><a name="scenario1"></a>1. forgatókönyv: "Áttelepítem a meglévő Azure-beli virtuális gépeket az Azure Premium Storage-ba."
Ha meglévő Azure-virtuális gépek áttelepítése, állítsa le a virtuális gépet, készítsen virtuális merevlemezek a kívánt típusú virtuális merevlemez, majd másolja a virtuális merevlemez az AzCopy vagy a PowerShell.

A virtuális gép kell teljesen le a tiszta állapot áttelepítéséhez. Az áttelepítés befejezéséig állásidő áll fenn.

#### <a name="step-1-prepare-vhds-for-migration"></a>1. lépés Vhd-k előkészítése áttelepítésre
Ha meglévő Azure-virtuális gépeket telepít át prémium szintű storage-ba, a virtuális merevlemez a következő lehet:

* Általános operációsrendszer-lemezkép
* Egyedi operációs rendszerlemez
* Adatlemez

Az alábbiakban végighaladunk a 3 forgatókönyvek a virtuális merevlemez előkészítéséhez.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Általános operációsrendszer-virtuális merevlemez használata több virtuálisgép-példány létrehozásához
Ha olyan virtuális merevlemezt tölt fel, amely több általános Azure-virtuálisgép-példány létrehozásához lesz használva, először általánosítania kell a virtuális merevlemezt egy sysprep segédprogrammal. Ez a helyszíni vagy a felhőben lévő virtuális merevlemezre vonatkozik. A Sysprep minden gépspecifikus információt eltávolít a virtuális merevlemezről.

> [!IMPORTANT]
> Készítsen pillanatképet, vagy készítsen biztonsági másolatot a virtuális gépről, mielőtt általánosítana. A sysprep futtatása leáll, és felszabadítja a virtuálisgép-példányt. A Windows operációs rendszer virtuális merevlemezének sysprep előkészítéséhez kövesse az alábbi lépéseket. Vegye figyelembe, hogy a Sysprep parancs futtatásához le kell állítania a virtuális gépet. A Sysprep programról további információt a [Sysprep áttekintése](https://technet.microsoft.com/library/hh825209.aspx) vagy a [Sysprep műszaki útmutató című témakörben talál.](https://technet.microsoft.com/library/cc766049.aspx)
>
>

1. Nyisson meg egy parancssorablakot rendszergazdaként.
2. A Sysprep megnyitásához írja be a következő parancsot:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. A Rendszerelőkészítő eszközben válassza a Rendszer kezdőélményének megadása (OOBE) lehetőséget, jelölje be a Generalize jelölőnégyzetet, válassza a **Leállítás**lehetőséget, majd kattintson az **OK**gombra, ahogy az az alábbi képen látható. A Sysprep általánosítja az operációs rendszert, és leállítja a rendszert.

    ![][1]

Ubuntu virtuális gép esetén használja a virt-sysprep programot, hogy ugyanazt elérje. További részletek [a virt-sysprep témakörben](https://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) talál. Lásd még néhány, a nyílt forráskódú [Linux Server kiépítési szoftver](https://www.cyberciti.biz/tips/server-provisioning-software.html) más Linux operációs rendszerekhez.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Egyetlen virtuálisgép-példány létrehozásához használjon egyedi operációsrendszer-virtuális merevlemezt
Ha egy alkalmazás fut a virtuális gépen, amely megköveteli a gép-specifikus adatokat, ne általánosítsa a virtuális merevlemez. Egy nem általánosított virtuális merevlemez egy egyedi Azure virtuálisgép-példány létrehozásához használható. Ha például a tartományvezérlő a virtuális merevlemezen van, a sysprep végrehajtása érvénytelensé teszi tartományvezérlőként. Tekintse át a virtuális gépen futó alkalmazásokat, és a sysprep futtatásának hatását a virtuális merevlemez általánosítása előtt.

##### <a name="register-data-disk-vhd"></a>VHD adatlemez regisztrálása
Ha adatlemezek et telepíthet át az Azure-ban, győződjön meg arról, hogy az adatlemezeket használó virtuális gépek le vannak állítva.

Kövesse az alábbi lépéseket a virtuális merevlemez Azure Premium Storage-ba másolásához és kiépített adatlemezként való regisztrálásához.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>2. lépés A virtuális merevlemez célhelyének létrehozása
Hozzon létre egy tárfiókot a virtuális gépek karbantartásához. A virtuális gépek tárolásának tervezésekor vegye figyelembe a következő pontokat:

* A cél prémium szintű tárfiók.
* A tárfiók helyének meg kell egyeznie a prémium szintű storage-kompatibilis Azure-virtuális gépek végső szakaszában létre. Másolhat egy új tárfiókba, vagy azt tervezheti, hogy ugyanazt a tárfiókot használja az igényeinek megfelelően.
* Másolja és mentse a tárfiók kulcsát a céltárfiók a következő lépéshez.

Adatlemezek esetén dönthet úgy, hogy bizonyos adatlemezeket egy szabványos tárfiókban (például a hűvösebb tárhellyel rendelkező lemezeken) tárol, de azt javasoljuk, hogy az éles számítási feladatok hoz a prémium szintű tárolás hoz.

#### <a name="step-3-copy-vhd-with-azcopy-or-powershell"></a><a name="copy-vhd-with-azcopy-or-powershell"></a>3. lépés VHD másolása az AzCopy vagy PowerShell segítségével
A két lehetőség bármelyikének feldolgozásához meg kell találnia a tároló elérési útját és a tárfiók kulcsát. A tároló elérési útja és a tárfiók kulcsa megtalálható az **Azure Portal** > **Storage-ban.** A tároló URL-címe a\/"https: /myaccount.blob.core.windows.net/mycontainer/" lesz.

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>1. lehetőség: Virtuális merevlemez másolása az AzCopy segítségével (aszinkron másolat)

Az AzCopy használatával könnyedén feltöltheti a virtuális merevlemezt az interneten keresztül. A Virtuálisgépek méretétől függően ez időbe telhet. Ne felejtse el ellenőrizni a tárfiók be- és kimenő forgalom korlátait, ha ezt a beállítást használja. A részletekért tekintse meg [a méretezhetőségi és teljesítménycélokat](scalability-targets-standard-account.md) a normál tárfiókokhoz.

1. Töltse le és telepítse az AzCopy-t innen: [AzAzCopy legújabb verziója](https://aka.ms/downloadazcopy)
2. Nyissa meg az Azure PowerShellt, és nyissa meg azt a mappát, ahol az AzCopy telepítve van.
3. A következő paranccsal másolja a VHD fájlt a "Forrás" és a "Cél" fájlba.

   ```azcopy
   AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
   ```

    Példa:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```
 
   Az AzCopy parancsban használt paraméterek leírásai:

   * **/Source:** _ &lt;&gt;source :_ A virtuális merevlemezt tartalmazó mappa vagy tárolótároló URL-címének helye.
   * **/SourceKey:** _ &lt;&gt;source-account-key :_ A forrástárfiók tárfiókkulcsa.
   * **/Deszt:** _ &lt;&gt;destination :_ Storage container URL-cím a virtuális merevlemez másolásához.
   * **/DestKey:** _ &lt;dest-account-key&gt;:_ A céltárfiók tárfiókkulcsa.
   * **/Pattern:** _ &lt;fájlnév&gt;:_ Adja meg a másolni kívánt virtuális merevlemez fájlnevét.

Az AzCopy eszköz használatáról az [Adatok átvitele az AzCopy parancssori segédprogrammal](storage-use-azcopy.md)című témakörben talál.

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>2. lehetőség: Virtuális merevlemez másolása powershellnel (szinkronizált másolat)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A VHD-fájlt a Start-AzStorageBlobCopy PowerShell-parancsmag használatával is másolhatja. A vHD másolásához használja a következő parancsot az Azure PowerShellen. Cserélje le a <> értékeket a forrás- és céltárfiók megfelelő értékeire. A parancs használatához rendelkeznie kell egy vhds nevű tárolóval a céltárfiókban. Ha a tároló nem létezik, hozzon létre egyet a parancs futtatása előtt.

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

### <a name="scenario-2-i-am-migrating-vms-from-other-platforms-to-azure-premium-storage"></a><a name="scenario2"></a>2. forgatókönyv: "A virtuális gépeket más platformokról az Azure Premium Storage-ba telepítem át."
Ha a virtuális merevlemezt nem Azure-beli felhőalapú tárolóból az Azure-ba telepíti át, először exportálnia kell a virtuális merevlemezt egy helyi könyvtárba. A helyi címtár teljes forráselérési útja, ahol a virtuális merevlemez tárolják praktikus, majd az AzCopy segítségével töltse fel az Azure Storage-ba.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>1. lépés Virtuális merevlemez exportálása helyi könyvtárba
##### <a name="copy-a-vhd-from-aws"></a>Virtuális merevlemez másolása az AWS-ből
1. Ha AWS-t használ, exportálja az EC2 példányt egy Virtuális merevlemezre egy Amazon S3 gyűjtőben. Kövesse az Amazon dokumentációjában leírt lépéseket az Amazon EC2 példányok exportálásához az Amazon EC2 parancssori felület (CLI) eszköz telepítéséhez, és futtassa a create-instance-export-task parancsot az EC2 példány VHD fájlba való exportálásához. Ügyeljen arra, hogy a **CREATE-instance-export-task** parancs futtatásakor a DISK&#95;IMAGE&#95;FORMAT változóhoz használja a **Virtuális merevlemezt.** Az exportált VHD fájl a folyamat során kijelölt Amazon S3 gyűjtőbe kerül.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Töltse le a VHD fájlt az S3 gyűjtőből. Jelölje ki a VHD-fájlt, majd **a Műveletek** > **letöltése**lehetőséget.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Virtuális merevlemez másolása más, nem Azure-felhőből
Ha a virtuális merevlemezt nem Azure-beli felhőalapú tárolóból az Azure-ba telepíti át, először exportálnia kell a virtuális merevlemezt egy helyi könyvtárba. Másolja a virtuális merevlemezt tároló helyi könyvtár teljes forráselérési útját.

##### <a name="copy-a-vhd-from-on-premises"></a>Virtuális merevlemez másolása a helyszíni környezetből
Ha a virtuális merevlemezt egy helyszíni környezetből telepíti át, a virtuális merevlemez t tároló teljes forráselérési útvonalra lesz szüksége. A forráselérési út lehet kiszolgálói hely vagy fájlmegosztás.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>2. lépés A virtuális merevlemez célhelyének létrehozása
Hozzon létre egy tárfiókot a virtuális gépek karbantartásához. A virtuális gépek tárolásának tervezésekor vegye figyelembe a következő pontokat:

* A céltárfiók lehet standard vagy prémium szintű tárterület az alkalmazás követelményétől függően.
* A tárfiók régiójának meg kell egyeznie a prémium szintű storage-kompatibilis Azure-virtuális gépek végső szakaszában létre. Másolhat egy új tárfiókba, vagy azt tervezheti, hogy ugyanazt a tárfiókot használja az igényeinek megfelelően.
* Másolja és mentse a tárfiók kulcsát a céltárfiók a következő lépéshez.

Azt javasoljuk, hogy az összes adatot az éles számítási feladatok prémium szintű storage használatához.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>3. lépés A virtuális merevlemez feltöltése az Azure Storage szolgáltatásba
Most, hogy a virtuális merevlemez a helyi címtárban, az AzCopy vagy az AzurePowerShell segítségével feltöltheti a .vhd fájlt az Azure Storage-ba. Mindkét lehetőség itt található:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>1. lehetőség: Az Azure PowerShell Add-AzureVhd használata a .vhd fájl feltöltéséhez

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Uri \<> példa lehet **_"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"_**. A \<FileInfo> példa lehet **_a "C:\path\to\upload.vhd"_**.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>2. lehetőség: Az .vhd fájl feltöltése az AzCopy segítségével

Az AzCopy használatával könnyedén feltöltheti a virtuális merevlemezt az interneten keresztül. A Virtuálisgépek méretétől függően ez időbe telhet. Ne felejtse el ellenőrizni a tárfiók be- és kimenő forgalom korlátait, ha ezt a beállítást használja. A részletekért tekintse meg [a méretezhetőségi és teljesítménycélokat](scalability-targets-standard-account.md) a normál tárfiókokhoz.

1. Töltse le és telepítse az AzCopy-t innen: [AzAzCopy legújabb verziója](https://aka.ms/downloadazcopy)
2. Nyissa meg az Azure PowerShellt, és nyissa meg azt a mappát, ahol az AzCopy telepítve van.
3. A következő paranccsal másolja a VHD fájlt a "Forrás" és a "Cél" fájlba.

   ```azcopy
      AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
   ```

   Példa:

   ```azcopy
      AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
   ```

   Az AzCopy parancsban használt paraméterek leírásai:

   * **/Source:** _ &lt;&gt;source :_ A virtuális merevlemezt tartalmazó mappa vagy tárolótároló URL-címének helye.
   * **/SourceKey:** _ &lt;&gt;source-account-key :_ A forrástárfiók tárfiókkulcsa.
   * **/Deszt:** _ &lt;&gt;destination :_ Storage container URL-cím a virtuális merevlemez másolásához.
   * **/DestKey:** _ &lt;dest-account-key&gt;:_ A céltárfiók tárfiókkulcsa.
   * **/BlobType: lap:** Itt adhatja meg, hogy a cél egy lapblob.
   * **/Pattern:** _ &lt;fájlnév&gt;:_ Adja meg a másolni kívánt virtuális merevlemez fájlnevét.

Az AzCopy eszköz használatáról az [Adatok átvitele az AzCopy parancssori segédprogrammal](storage-use-azcopy.md)című témakörben talál.

##### <a name="other-options-for-uploading-a-vhd"></a>A virtuális merevlemez feltöltésének egyéb lehetőségei
A virtuális merevlemezt a tárfiókba is feltöltheti az alábbi eszközök egyikével:

* [Azure Storage Copy Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Az Azure Storage Explorer blobok feltöltése](https://azurestorageexplorer.codeplex.com/)
* [Tárolás importálása/exportálása szolgáltatás REST API-jának](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> Javasoljuk az Importálási/Exportálási szolgáltatás használatát, ha a becsült feltöltési idő 7 napnál hosszabb. [A DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) segítségével megbecsülheti az adatméretből és az átviteli egységből származó időt.
>
> Az importálás/exportálás szabványos tárfiókba másolásra használható. Egy eszköz, például az AzCopy használatával kell másolnia a normál tárból a prémium szintű tárfiókba.
>
>

## <a name="create-azure-vms-using-premium-storage"></a><a name="create-azure-virtual-machine-using-premium-storage"></a>Azure-beli virtuális gépek létrehozása prémium szintű tárterülethasználatával
Miután a virtuális merevlemez feltöltése vagy másolása a kívánt tárfiókba, kövesse az ebben a szakaszban található utasításokat a virtuális merevlemez operációs rendszerképként vagy operációsrendszer-lemezként regisztrálásához a forgatókönyvtől függően, majd hozzon létre belőle egy virtuálisgép-példányt. A Virtuális merevlemez adatlemez a virtuális géphez csatlakoztatható, miután létrehozta.
A szakasz végén egy mintaáttelepítési parancsfájl található. Ez az egyszerű parancsfájl nem felel meg minden forgatókönyvnek. Előfordulhat, hogy frissítenie kell a parancsfájlt, hogy megfeleljen az adott forgatókönyvnek. Ha meg szeretné tudni, hogy ez a parancsfájl vonatkozik-e a forgatókönyvre, olvassa el a [Mintaáttelepítési parancsfájl t.](#a-sample-migration-script)

### <a name="checklist"></a>Ellenőrzőlista
1. Várjon, amíg az összes VHD-lemez másolása befejeződik.
2. Győződjön meg arról, hogy a Prémium szintű tárterület elérhető abban a régióban, ahamelyazáttelepítés korát.
3. Döntse el az új virtuálisgép-sorozatot, amelyet használni fog. Prémium szintű storage-kompatibilisnek kell lennie, és a méretnek a régióban való rendelkezésre állástól és az igényeitől függően kell lennie.
4. Döntse el a pontos virtuális gép méretét fogja használni. A virtuális gép méretének elég nagynak kell lennie ahhoz, hogy támogassa az adatlemezek számát. Például ha 4 adatlemezzel rendelkezik, a virtuális gépnek 2 vagy több maggal kell rendelkeznie. Vegye figyelembe a feldolgozási teljesítmény, a memória és a hálózati sávszélesség igényeit is.
5. Hozzon létre egy prémium szintű tárfiókot a célrégióban. Ezt a fiókot fogja használni az új virtuális géphez.
6. Az aktuális virtuális gép részletei kéznél, beleértve a lemezek listáját és a megfelelő Virtuális merevlemez-blobok.

Készítse elő az alkalmazást az állásidőre. A tiszta áttelepítéshez le kell állítania az összes feldolgozást a jelenlegi rendszerben. Csak akkor lehet kapni, hogy a következetes állapot, amely áttelepíthető az új platformra. Az állásidő időtartama az áttelepítendő lemezeken lévő adatok mennyiségétől függ.

> [!NOTE]
> Ha egy Azure Resource Manager virtuális gép egy speciális virtuális merevlemezről, tekintse meg [ezt a sablont](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) a Resource Manager virtuális gép meglévő lemez használatával történő üzembe helyezéséhez.
>
>

### <a name="register-your-vhd"></a>A virtuális merevlemez regisztrálása
Virtuális gép létrehozásához az operációs rendszer virtuális merevlemezéről, vagy egy adatlemez t egy új virtuális géphez, először regisztrálnia kell őket. Kövesse az alábbi lépéseket a virtuális merevlemez forgatókönyvétől függően.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Általános operációsrendszer-virtuális merevlemez több Azure virtuálisgép-példány létrehozásához
Miután általánosrendszerezett operációsrendszer-rendszer virtuális merevlemez feltöltése a tárfiókba, regisztrálja **azt egy Azure virtuális gép lemezkép,** így létrehozhat egy vagy több virtuálisgép-példányok belőle. A következő PowerShell-parancsmagokkal regisztrálja a virtuális merevlemezt Azure Virtuálisgép-operációs rendszerrendszer-lemezképként. Adja meg a teljes tároló URL-címét, ahová a virtuális merevlemezt másolta.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Másolja és mentse az új Azure virtuális gép lemezkép nevét. A fenti példában az *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Egyedi operációs rendszer virtuális merevlemeze egyetlen Azure virtuálisgép-példány létrehozásához
Miután az egyedi operációsrendszer-virtuális merevlemez feltöltése a tárfiókba, regisztrálja azt **egy Azure operációs rendszer lemeze,** így létrehozhat egy virtuálisgép-példányt belőle. Ezekkel a PowerShell-parancsmagokkal regisztrálhatja a virtuális merevlemezt Azure operációs rendszerbeli lemezként. Adja meg a teljes tároló URL-címét, ahová a virtuális merevlemezt másolta.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Másolja és mentse az új Azure operációsrendszer-lemez nevét. A fenti példában az *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Az új Azure virtuálisgép-példány(ok)hoz csatolandó virtuális merevlemez virtuális merevlemeze
Miután a virtuális merevlemez tanév feltöltése a tárfiókba, regisztrálja azt egy Azure Data Disk, hogy csatolható legyen az új DS sorozat, DSv2 sorozat vagy GS sorozatAzure virtuális gép példány.

Ezekkel a PowerShell-parancsmagokkal regisztrálhatja a virtuális merevlemezt Azure-adatlemezként. Adja meg a teljes tároló URL-címét, ahová a virtuális merevlemezt másolta.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Másolja és mentse az új Azure Data Disk nevét. A fenti példában a *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Prémium szintű storage-kompatibilis virtuális gép létrehozása
Az operációs rendszer lemezképe vagy operációsrendszer-lemez regisztrálása után hozzon létre egy új DS-sorozatú, DSv2-sorozatú vagy GS-sorozatú virtuális gép. A regisztrált operációsrendszer-lemezlemez-nevet fogja használni. Válassza ki a virtuális gép típusát a prémium szintű storage-csomagból. Az alábbi példában a *Standard_DS2* virtuális gép méretét használjuk.

> [!NOTE]
> Frissítse a lemez méretét, hogy megbizonyosodjon arról, hogy megfelel a kapacitás és a teljesítmény követelményeinek, valamint a rendelkezésre álló Azure-lemezméretek.
>
>

Kövesse az alábbi lépésről lépésre a PowerShell-parancsmagokat az új virtuális gép létrehozásához. Először állítsa be a közös paramétereket:

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

Következő, a forgatókönyvtől függően hozza létre az Azure virtuálisgép-példányt az operációs rendszerlemezről vagy az operációs rendszer lemezéről, amely regisztrált.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Általános operációsrendszer-virtuális merevlemez több Azure virtuálisgép-példány létrehozásához
Hozzon létre egy vagy több új DS sorozatú Azure virtuálisgép-példányt a regisztrált **Azure operációs rendszerlemezkép** használatával. Adja meg ezt az operációsrendszer-lemezkép nevét a virtuális gép konfigurációjában új virtuális gép létrehozásakor az alábbiak szerint.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Egyedi operációs rendszer virtuális merevlemeze egyetlen Azure virtuálisgép-példány létrehozásához
Hozzon létre egy új DS-sorozatú Azure virtuálisgép-példányt a regisztrált **Azure operációs rendszerlemez** használatával. Adja meg ezt az operációsrendszer-lemez nevet a virtuális gép konfigurációjában az új virtuális gép létrehozásakor az alábbiak szerint.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Adja meg az Azure virtuális gép egyéb adatait, például a felhőszolgáltatás, a régió, a tárfiók, a rendelkezésre állási készlet és a gyorsítótárazási szabályzat. Vegye figyelembe, hogy a virtuálisgép-példánynak a társított operációs rendszerrel vagy adatlemezekkel együtt kell lennie, így a kiválasztott felhőszolgáltatásnak, régiónak és tárfióknak ugyanazon a helyen kell lennie, mint a lemezek alapjául szolgáló Virtuális merevlemezeknek.

### <a name="attach-data-disk"></a>Adatlemez csatolása
Végül, ha regisztrált adatlemez VHD-k, csatolja őket az új prémium szintű storage-kompatibilis Azure virtuális gép.

A következő PowerShell-parancsmag használatával adatlemezt csatolhat az új virtuális géphez, és megadhatja a gyorsítótárazási házirendet. Példában az alábbi példában a gyorsítótárazási házirend *csak olvasható*.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Előfordulhat, hogy további lépések szükségesek az alkalmazás támogatásához, amelyekre ez az útmutató nem terjed ki.
>
>

### <a name="checking-and-plan-backup"></a>Biztonsági mentés ellenőrzése és megtervezése
Miután az új virtuális gép működik, hozzáférés az azonos bejelentkezési azonosítót és jelszót, mint az eredeti virtuális gép, és ellenőrizze, hogy minden a várt módon működik.Once the new VM is up and running, access it using the same login id and password is as the original VM, and verify that everything is working as expected. Az összes beállítás, beleértve a csíkozott kötetek, jelen lenne az új virtuális gép.

Az utolsó lépés az, hogy az alkalmazás igényeinek megfelelően tervezze meg az új virtuális gép biztonsági mentési és karbantartási ütemezését.

### <a name="a-sample-migration-script"></a><a name="a-sample-migration-script"></a>Mintaáttelepítési parancsfájl
Ha több virtuális gépet kell áttelepítenie, a PowerShell-parancsfájlok automatizálása hasznos lehet. A következőkben egy minta-parancsfájl, amely automatizálja a virtuális gép áttelepítését. Vegye figyelembe, hogy az alábbi parancsfájl csak egy példa, és kevés feltételezések et az aktuális virtuális gép lemezek. Előfordulhat, hogy frissítenie kell a parancsfájlt, hogy megfeleljen az adott forgatókönyvnek.

A feltételezések a következők:

* Klasszikus Azure-virtuális gépeket hoz létre.
* A forrás operációsrendszer-lemezek és a forrás-adatlemezek ugyanabban a tárfiókban és tárolóban vannak. Ha az operációs rendszer lemezek és adatlemezek nem ugyanazon a helyen, az AzCopy vagy az Azure PowerShell segítségével virtuális merevlemezek másolása tárfiókok és tárolók. Tekintse meg az előző lépést: [VHD másolása az AzCopy vagy PowerShell](#copy-vhd-with-azcopy-or-powershell)segítségével. A parancsfájl szerkesztése a forgatókönyvnek megfelel egy másik választás, de javasoljuk az AzCopy vagy a PowerShell használatát, mivel ez egyszerűbb és gyorsabb.

Az automatizálási parancsfájl az alábbiakban érhető el. Cserélje le a szöveget az adatokkal, és frissítse a parancsfájlt az adott forgatókönyvnek megfelelően.

> [!NOTE]
> A meglévő parancsfájl használata nem őrzi meg a forrás virtuális gép hálózati konfigurációját. Újra meg kell egyeznem az áttelepített virtuális gépek hálózati beállításaival.
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

#### <a name="optimization"></a><a name="optimization"></a>Optimalizálás
A jelenlegi virtuális gép konfigurációja testreszabható kifejezetten a standard lemezek megfelelő működéséhez. Például a teljesítmény növelése egy csíkozott köteten lévő lemezek használatával. Például ahelyett, hogy 4 lemezt külön-külön a prémium szintű storage, akkor lehet, hogy optimalizálja a költségeket, ha egyetlen lemezt. Az ilyen optimalizálásokat eseti alapon kell kezelni, és az áttelepítés után egyéni lépéseket kell igényelni. Vegye figyelembe azt is, hogy ez a folyamat nem biztos, hogy működik olyan adatbázisokés alkalmazások esetében, amelyek a telepítésben meghatározott lemezelrendezéstől függenek.

##### <a name="preparation"></a>Előkészítés
1. Végezze el az egyszerű áttelepítést a korábbi szakaszban leírtak szerint. Az áttelepítés után az új virtuális gépen lesz optimalizálás.
2. Adja meg az optimalizált konfigurációhoz szükséges új lemezméreteket.
3. Határozza meg az aktuális lemezek/kötetek hozzárendelését az új lemezspecifikációkhoz.

##### <a name="execution-steps"></a>Végrehajtási lépések
1. Hozza létre az új lemezeket a megfelelő méretű prémium szintű storage virtuális gép.
2. Jelentkezzen be a virtuális gépre, és másolja az adatokat az aktuális kötetről az új lemezre, amely leképezi az adott kötetre. Ezt tegye meg az összes olyan aktuális kötetesetében, amelyet új lemezre kell leképezni.
3. Ezután módosítsa az alkalmazás beállításait az új lemezekre való váltáshoz, és válassza le a régi köteteket.

Az alkalmazás finomhangolása a jobb lemez teljesítmény, kérjük, olvassa el az optimalizálása alkalmazás teljesítményét szakasza [a tervezés a nagy teljesítményű](../../virtual-machines/windows/premium-storage-performance.md) cikket.

### <a name="application-migrations"></a>Alkalmazásáttelepítések
Az adatbázisok és más összetett alkalmazások az alkalmazásszolgáltató által az áttelepítéshez meghatározott speciális lépéseket igényelhetnek. Kérjük, olvassa el a megfelelő alkalmazásdokumentációt. Például az adatbázisok általában biztonsági mentésés visszaállítás útján telepíthetők át.

## <a name="next-steps"></a>További lépések
A következő források a virtuális gépek áttelepítésére vonatkozó konkrét forgatókönyvekhez:

* [Az Azure virtuális gépek áttelepítése a tárfiókok között](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Hozzon létre és töltsön fel egy Windows Server virtuális merevlemezt az Azure-ba.](../../virtual-machines/windows/upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Linuxos virtuális merevlemez létrehozása és feltöltése az Azure-ba](../../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Virtuális gépek áttelepítése az Amazon AWS-ről a Microsoft Azure-ba](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Az Azure Storage és az Azure virtuális gépekről az alábbi forrásokból is megtudhatja:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure virtuális gépek](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [IaaS-alapú virtuális gépek lemeztípusának kiválasztása](../../virtual-machines/windows/disks-types.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: https://technet.microsoft.com/library/hh831739.aspx
