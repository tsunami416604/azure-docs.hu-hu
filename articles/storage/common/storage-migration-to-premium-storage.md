---
title: "Prémium szintű Azure Storage áttelepítése virtuális gépek |} Microsoft Docs"
description: "Prémium szintű Azure Storage telepíthet át a meglévő virtuális gépekre. Prémium szintű Storage nagy teljesítményű, alacsony késésű támogatása az Azure virtuális gépeken futó I/O-igényes munkaterhelések kínál."
services: storage
documentationcenter: na
author: yuemlu
manager: tadb
editor: tysonn
ms.assetid: 272250b3-fd4e-41d2-8e34-fd8cc341ec87
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: yuemlu
ms.openlocfilehash: 9c2ac737c9f4e13b4e5e4f5dba18c7a697f6e6c3
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Prémium szintű Azure Storage (nem felügyelt lemezek) áttelepítése

> [!NOTE]
> A cikkből megtudhatja, hogyan telepíthet át egy virtuális Gépet, amely egy nem felügyelt prémium lemezeket használó virtuális géphez nem felügyelt standard lemezek használja. Javasoljuk, hogy Azure felügyelt lemezek használatakor az új virtuális gépek, és a korábbi nem felügyelt lemezek átalakítása felügyelt lemezek. Felügyelt lemezek leíró az alapul szolgáló storage-fiókok, nem szükséges. További információkért lásd: a [felügyelt lemezekhez – áttekintés](../../virtual-machines/windows/managed-disks-overview.md).
>

Prémium szintű Storage nagy teljesítményű, alacsony késésű lemez I/O-igényes munkaterhelések futó virtuális gépek támogatása nyújt. Prémium szintű Azure Storage át kell telepítenie az alkalmazás virtuális gépek lemezei is igénybe vehet a sebesség előnyeit, és ezek a lemezek teljesítményét.

Ez az útmutató célja a prémium szintű Azure Storage jobb új felhasználók zökkenőmentes váltásban az aktuális rendszerből prémium szintű Storage előkészítése. Az útmutató foglalkozik a legfontosabb összetevők, a folyamat három:

* [Prémium szintű Storage az áttelepítés tervezése](#plan-the-migration-to-premium-storage)
* [Készítse elő, és másolja át a virtuális merevlemezek (VHD) prémium szintű Storage](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Prémium szintű Storage használata Azure virtuális gép létrehozása](#create-azure-virtual-machine-using-premium-storage)

Prémium szintű Azure Storage át virtuális gépeket más platformokon, vagy meglévő Azure virtuális gépek áttelepítésére Standard tárolási prémium szintű Storage. Ez az útmutató mindkét két forgatókönyv lépéseit ismerteti. Hajtsa végre a forgatókönyvtől függően vonatkozó szakaszában megadott lépéseket.

> [!NOTE]
> A szolgáltatás áttekintése és a prémium szintű Storage, a prémium szintű Storage árképzési található: [nagy teljesítményű tárolást Azure virtuális gépek terheléseihez](../../virtual-machines/windows/premium-storage.md). Azt javasoljuk, hogy minden virtuális gép lemezét, prémium szintű Azure Storage magas IOPS igénylő a legjobb teljesítmény érdekében az alkalmazás áttelepítéséhez. Ha a lemez nem igényli a magas iops értéket, korlátozhatja a költségek megőrizve a szabványos tárolóban, a (merevlemezes HDD) meghajtók SSD-k helyett virtuálisgép-lemez adatokat tárolja.
>

Az áttelepítési folyamat egészében befejezése szükség lehet további műveletek előtt és után az útmutatóban ismertetett lépéseket. Például konfigurálható virtuális hálózatok és a végpontok vagy magának az alkalmazásnak, amely előfordulhat, hogy az alkalmazás bizonyos időre leállítást belül kód módosítása. Ezek a műveletek minden alkalmazáshoz egyedi, és el kell végeznie ezeket az itt ismertetett lépéseket: Ez az útmutató az átállásra teljes prémium szintű Storage, zökkenőmentes lehető együtt.

## <a name="plan-the-migration-to-premium-storage"></a>Prémium szintű Storage az áttelepítés tervezése
Ez a szakasz biztosítja, hogy készen áll az áttelepítési lépéseket a cikkben, és segítséget nyújt a legjobb döntést a virtuális gép és a lemez típusok.

### <a name="prerequisites"></a>Előfeltételek
* Szüksége lesz egy Azure-előfizetés. Ha még nincs fiókja, létrehozhat egy hónapos [ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/) előfizetés, vagy keresse fel [Azure díjszabása](https://azure.microsoft.com/pricing/) további lehetőségekért.
* PowerShell-parancsmagokkal hajtható végre, szüksége lesz a Microsoft Azure PowerShell modul. A telepítési helyre és a telepítésre vonatkozó utasításokért lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).
* Prémium szintű Storage futó Azure virtuális gépek tervezésekor kell használnia a prémium szintű Storage képes a virtuális gépeket. Prémium szintű Storage képes a virtuális gépek a Standard és prémium szintű Storage lemezek is használhatók. Prémium szintű storage lemezek lesz elérhető további VM-típussal a jövőben. További információ az összes elérhető Azure virtuális lemez típusát és méretét: [virtuális gépek méretei](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) és [Felhőszolgáltatások mérete](../../cloud-services/cloud-services-sizes-specs.md).

### <a name="considerations"></a>Megfontolandó szempontok
Egy Azure virtuális gép támogatja a prémium szintű Storage több lemezt csatolni, így az alkalmazások legfeljebb 256 TB-nyi tárhelyre virtuális gépenként. Prémium szintű Storage az alkalmazások egy második lemez adatátviteli sebessége virtuális gépenként a rendkívül alacsony késleltetésű az olvasási műveletek érhet 80000 iops-értéket (bemeneti/kimeneti műveletek száma másodpercenként) virtuális Gépet és 2000 MB. Virtuális gépek és a lemezek számos lehetősége van. Ez a szakasz célja segíteni olyan beállítás, amely a legjobban megfelel a számítási feladatok kereséséhez.

#### <a name="vm-sizes"></a>A virtuális gépek mérete
Az Azure virtuális gép mérete paramétereknek szereplő [virtuális gépek méretei](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Tekintse át a virtuális gépek, amely együttműködik a prémium szintű Storage, és válassza ki a leginkább megfelelő virtuális gép méretét, amely a legjobban megfelel a számítási feladatok teljesítményétől. Győződjön meg arról, hogy nincs elegendő sávszélesség érhető el a virtuális Gépet, a lemez forgalom alapjául.

#### <a name="disk-sizes"></a>Lemezméretek
Öt különböző típusú lemezek, amelyek együtt a virtuális Gépet, és mindegyik rendelkezik-e adott iops-érték és átviteli korlátok. Vegye figyelembe a működés felső korlátjának Ha a lemez kiválasztása a virtuális gép alapján a kapacitás, a teljesítmény, méretezhetőség az alkalmazás igényeinek megfelelően, és csúcs tölti be.

| Prémium szintű lemezek típusa  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Lemezméret           | 128 GB| 512 GB| 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 
| IOPS-érték lemezenként       | 500   | 2300  | 5000           | 7500           | 7500           | 
| Adattovábbítás lemezenként | 100 MB / s | 150 MB / s | 200 MB / s | 250 MB / s | 250 MB / s |

Attól függően, hogy a munkaterhelés annak eldöntése, hogy adatlemeznek a virtuális gép szükséges. Több állandó adatlemezt lehet kapcsolódni a virtuális Gépet. Ha szükséges, a is paritásos a lemezeken, a kapacitás és a kötet teljesítményének növelése érdekében. (Megtudhatja, mi lemez csíkozást [Itt](../../virtual-machines/windows/premium-storage-performance.md#disk-striping).) Ha Ön paritásos prémium szintű Storage adatlemezek használata [tárolóhelyek][4], úgy kell beállítania, egyoszlopos használt lemezek. Ellenkező esetben a csíkozott kötet teljesítménye lehet alacsonyabb, mint a várt forgalom egyenetlen eloszlását miatt a lemezeken. A Linux virtuális gépek használhatják a *mdadm* segédprogram azonos eléréséhez. Cikke [szoftver RAID konfigurálása Linux](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) részleteiről.

#### <a name="storage-account-scalability-targets"></a>Tárfiókra vonatkozó méretezhetőségi célok
Prémium szintű Storage-fiókok a következő méretezhetőségi célok kívül van a [Azure Storage méretezhetőségi és teljesítménycéloknak](storage-scalability-targets.md). Ha az alkalmazás követelményeinek túllépi a méretezhetőségi célok egyetlen tárfiók, építenie az alkalmazást több tárfiókot használni, és az adatok particionálása adott tárfiókok között.

| Teljes kapacitásával | A helyileg redundáns tárolás fiók teljes sávszélesség |
|:--- |:--- |
| Lemez kapacitás: 35TB<br />Pillanatkép-kapacitás: 10 TB |Legfeljebb 50 Gigabit / másodperc, a bejövő + kimenő |

A prémium szintű Storage specifikációk további információkért tekintse meg [méretezhetőséget és a prémium szintű Storage használatakor Performance Targets](../../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets).

#### <a name="disk-caching-policy"></a>Lemez gyorsítótárazási házirend
Alapértelmezés szerint a gyorsítótárazási házirend lemez van *csak olvasható* prémium adatok lemezein, és *írható-olvasható* az a prémium szintű operációsrendszer-lemez csatolva a virtuális gép. A konfigurációs beállítás ajánlott az alkalmazás IOs rendszerhez az optimális teljesítmény eléréséhez. Írási műveleteket vagy a csak írható adatlemezek (köztük SQL Server) tiltsa le a lemezt gyorsítótárazás, hogy az alkalmazás jobb teljesítményt érhet el. A meglévő adatlemezek gyorsítótár beállításait is frissítve [Azure Portal](https://portal.azure.com) vagy a *- HostCaching* paramétere a *Set-AzureDataDisk* parancsmag.

#### <a name="location"></a>Hely
Jelölje ki a helyet, ahol a prémium szintű Azure Storage áll rendelkezésre. Lásd: [Azure-szolgáltatások régiónként](https://azure.microsoft.com/regions/#services) elérhető helyről naprakész tájékoztatást. A virtuális gépeket, hogy a lemezek, a virtuális gép lesz áruházak sok különböző régiókban azok jobb teljesítményt a tárfiók ugyanabban a régióban található.

#### <a name="other-azure-vm-configuration-settings"></a>Egyéb Azure virtuális gép konfigurációs beállításai
Egy Azure virtuális gép létrehozásakor a rendszer kéri, hogy az egyes virtuális gép beállításainak konfigurálása. Ne feledje, hogy néhány beállításainak rögzítése élettartama idején a virtuális gép, amíg módosíthatja, vagy később fel más. Tekintse át a Azure virtuális gép konfigurációs beállítások, és győződjön meg arról, hogy ezeknek a konfigurációja megfelelő a munkaterhelési követelményeinek megfelelően.

### <a name="optimization"></a>Optimalizálás
[Prémium szintű Storage: Nagy teljesítményű kialakítása](../../virtual-machines/windows/premium-storage-performance.md) útmutatást nyújt a prémium szintű Azure Storage használatával nagy teljesítményű alkalmazások létrehozásához. Követheti, hogy az irányelveket, az alkalmazás által használt technológiák alkalmazandó ajánlott eljárások teljesítményének együtt.

## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Készítse elő, és másolja át a virtuális merevlemezek (VHD) prémium szintű Storage
A következő szakaszban talál útmutatást a virtuális merevlemezek a virtuális gép előkészítése és a VHD-k másolása az Azure Storage.

* [1. forgatókönyv: "I vagyok áttelepíti meglévő Azure virtuális gépek a prémium szintű Storage."](#scenario1)
* [2. forgatókönyv: "I vagyok telepít át virtuális gépeket más platformokon a prémium szintű Storage."](#scenario2)

### <a name="prerequisites"></a>Előfeltételek
A virtuális merevlemezeket az áttelepítés előkészítéséhez lesz szüksége:

* Azure-előfizetéssel, egy tárfiókot, és egy tároló az adott storage-fiókot, amelyhez a VHD-t is másolhatja. Vegye figyelembe, hogy a cél tárfiókkal lehet-e a Standard vagy prémium szintű Storage fiók igényektől függően.
* Olyan eszköz, amely a virtuális merevlemez generalize, ha azt tervezi, hogy több Virtuálisgép-példányok készíteni. Például a Windows vagy adatb-sysprep Ubuntu a sysprep.
* Olyan eszköz, amely a VHD-fájl feltöltése a tárfiókba. Lásd: [adatátvitel az AzCopy parancssori segédprogram a](storage-use-azcopy.md) , vagy használjon egy [Azure Tártallózó](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Ez az útmutató ismerteti, másolja a VHD-t az AzCopy eszközzel.

> [!NOTE]
> Ha úgy dönt, a szinkron másolatot beállítást az AzCopy, az optimális teljesítmény érdekében másolja a VHD-való futtatásával az eszközöket egy Azure virtuális Gépen, amely a cél tárfiókkal ugyanabban a régióban. Virtuális merevlemez másolása az Azure virtuális gép egy másik régióban található, a teljesítmény csökkenhet.
>
> Nagy mennyiségű adatot felülírását korlátozott sávszélességű, fontolja meg [az Azure Import/Export szolgáltatás használatával az adatok átviteléhez a Blob Storage](../storage-import-export-service.md); Ez lehetővé teszi, hogy az adatok átvitele a merevlemez egy Azure-adatközpontban szállítási. Az Azure Import/Export szolgáltatás segítségével másolja az adatokat a csak egy standard szintű tárfiókot. Ha az adatokat a standard szintű tárfiókot, használhatja a [másolási Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx) vagy az AzCopy segítségével az adatok átvitele a prémium szintű storage-fiók.
>
> Ügyeljen arra, hogy a Microsoft Azure csak támogatja-e a rögzített méretű VHD-fájlokat. A VHDX-fájlok vagy a dinamikus VHD-k nem támogatottak. Ha egy dinamikus virtuális merevlemez, átalakíthatja rögzített méretű használata a [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) parancsmag.
>
>

### <a name="scenario1"></a>1. forgatókönyv: "I vagyok áttelepíti meglévő Azure virtuális gépek a prémium szintű Storage."
Meglévő Azure virtuális gépeket telepít át, ha a virtuális gép leállítása, készítse elő a / kívánt VHD típusú virtuális merevlemezeket, és másolja a VHD AzCopy vagy a PowerShell használatával.

A virtuális gép kell lennie a tiszta állapotot áttelepítéséhez teljesen le. Lesz a leállás addig, amíg az áttelepítés befejeződött.

#### <a name="step-1-prepare-vhds-for-migration"></a>1. lépés Virtuális merevlemezek Felkészülés az áttelepítésre
Ha a meglévő Azure virtuális gépek áttelepítés prémium szintű Storage, a virtuális merevlemez lehet:

* Általános operációsrendszer-lemezkép elkészítése
* Egy egyedi operációsrendszer-lemez
* Adatlemez

Az alábbiakban azt végezze el a virtuális merevlemez előkészítése 3 forgatókönyvekben.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Több Virtuálisgép-példány létrehozásához használja az operációs rendszer általánosított virtuális Merevlemezt
Ha több általános Azure Virtuálisgép-példány létrehozásához használt virtuális Merevlemezt, először meg kell generalize virtuális Merevlemezt a sysprep segédprogrammal. Ez vonatkozik, amely a helyi virtuális merevlemez vagy a felhőben. Sysprep eltávolítása a VHD-t bármely gépen-specifikus adatait.

> [!IMPORTANT]
> Készítsen pillanatképet, vagy biztonsági mentése a virtuális gép előtt normalizálása azt. A sysprep fut le fog állni, és a Virtuálisgép-példány felszabadítani. A Windows operációs rendszer virtuális Merevlemezt a sysprep kövesse az alábbi lépéseket. Vegye figyelembe, hogy a Sysprep parancsot futtató van szükség, hogy a virtuális gép leállítása. További információ a Sysprep: [Sysprep áttekintése](http://technet.microsoft.com/library/hh825209.aspx) vagy [Sysprep műszaki útmutatója](http://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Nyisson meg egy parancssori ablakot rendszergazdaként.
2. Adja meg a következő parancs futtatásával nyissa meg a Sysprep:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. A rendszer-előkészítő eszközt, jelölje be adja meg a rendszer Out-of-Box élmény (OOBE), jelölje be a Generalize jelölőnégyzetet, válassza ki **leállítási**, és kattintson a **OK**, az alábbi ábrán látható módon. A Sysprep rendszer általánosítja az operációs rendszert, és állítsa le a rendszer.

    ![][1]

Ubuntu virtuális gép használja a sysprep adatb azonos eléréséhez. Lásd: [adatb-sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) további részleteket. További információ a nyílt forráskódú némelyike [Linux Server kiépítés szoftver](http://www.cyberciti.biz/tips/server-provisioning-software.html) más Linux operációs rendszerekhez.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Egyetlen Virtuálisgép-példány létrehozásához használja az operációs rendszer virtuális merevlemez egyedi
Ha a virtuális gép adatokat igénylő futó alkalmazást, nem generalize a VHD-t. Nem általánosított virtuális merevlemez segítségével hozzon létre egyedi Azure Virtuálisgép-példányt. Például ha a tartományvezérlő van a VHD-t, sysprep végrehajtása megkönnyítő hatástalan tartományvezérlőként. Tekintse át a virtuális Gépet, és azokat a sysprep futtatása előtt a virtuális merevlemez normalizálása hatásának futó alkalmazások.

##### <a name="register-data-disk-vhd"></a>Virtuális merevlemez adatlemeze regisztrálása
Ha adatlemezt kell áttelepíteni az Azure-ban, meg kell győződnie arról az adott adatok lemezeket használó virtuális gépek állnak le.

Prémium szintű Azure Storage másolja a VHD-t, és regisztrálhatja azt kiosztott adatok lemezként az alább ismertetett lépéseket követve.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>2. lépés A cél a virtuális merevlemez létrehozása
Hozzon létre egy tárfiókot, a virtuális merevlemezek karbantartásához. A virtuális merevlemezek tárolási helyének megtervezésekor, vegye figyelembe a következő szempontokat:

* A célként prémium szintű storage-fiók.
* A tárfiók helyének meg kell egyeznie a prémium szintű Storage kompatibilis Azure virtuális gépek létrehozhat utolsó szakaszában. Nem átmásolni egy új tárfiókot, vagy a igények alapján ugyanazt a tárfiókot használni szeretne.
* Másolja ki és mentse a tárfiók hívóbetűjét, a cél tárfiókkal a következő szakaszban.

Az adatlemezek esetén dönthet úgy, hogy néhány adatlemezek tartsa egy standard szintű tárfiókot (például lemezek hűtőre tárhellyel rendelkező), de határozottan javasoljuk, hogy az üzemi alkalmazások és szolgáltatások a prémium szintű storage minden adat áthelyezése.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>3. lépés. Másolja a VHD AzCopy vagy a PowerShell használatával
Szüksége lesz a tároló elérési útját és a tárolási fiók kulcs található feldolgozni az alábbi két lehetőség közül. Tároló elérési útja és a tároló kulcsa megtalálható **Azure Portal** > **tárolási**. A tároló URL-címe például a "https://myaccount.blob.core.windows.net/mycontainer/" lesz.

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>1. lehetőség: Az AzCopy (aszinkron másolhatja azokat) egy virtuális merevlemez másolása
Használja az AzCopy, egyszerűen feltöltheti a VHD-t az interneten keresztül. A virtuális merevlemezek méretétől függően ez időt vehet igénybe. Fontos, hogy ellenőrizze a tárfiókok be-és kilépési korlátai, ez a beállítás használata esetén. Lásd: [Azure Storage méretezhetőségi és teljesítménycéloknak](storage-scalability-targets.md) részleteiről.

1. Töltse le és telepítse az AzCopy innen: [az AzCopy legújabb verzióját](http://aka.ms/downloadazcopy)
2. Nyissa meg az Azure PowerShell és a mappában, amelyen telepítve van-e az AzCopy.
3. A következő parancs segítségével másolja a VHD-fájlt a "Forrás" a "Cél".

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Példa:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```

    Az alábbiakban az AzCopy parancs paraméterei leírása:

   * **/ Forrás:  *&lt;forrás&gt;:***  a mappa vagy a tárolási tároló URL-címet, amely a VHD-t tartalmaz.
   * **/ SourceKey:  *&lt;forrás fiókkulcs&gt;:***  a forrás tárfiók Tárfiók kulcsa.
   * **/ Dest:  *&lt;cél&gt;:***  másolja a VHD-fájlt tároló tároló URL-címet.
   * **/ DestKey:  *&lt;cél fiókkulcs&gt;:***  a cél tárfiókkal a Tárfiók kulcsára.
   * **/ Mintát:  *&lt;Fájlnév&gt;:***  adja meg a fájlnevet a virtuális merevlemez másolása.

AzCopy használatával eszköz, lásd: [adatátvitel az AzCopy parancssori segédprogram a](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>2. lehetőség: A PowerShell használatával (Synchronized másolás) virtuális merevlemez másolása
A PowerShell-parancsmaggal a Start-AzureStorageBlobCopy VHD-fájlt is másolhatja. Az Azure PowerShell az alábbi parancs segítségével másolja a VHD-t. Cserélje le a <> értékei a forrás és cél tárfiók megfelelő értékeivel. Ezen parancs használatához rendelkeznie kell a cél tárfiókkal nevezett VHD-k tárolója. Ha a tároló nem létezik, hozzon létre egyet a parancs futtatása előtt.

```powershell
$sourceBlobUri = <source-vhd-uri>

$sourceContext = New-AzureStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

$destinationContext = New-AzureStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext
```

Példa:

```powershell
C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

C:\PS> $sourceContext = New-AzureStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

C:\PS> $destinationContext = New-AzureStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

C:\PS> Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext
```

### <a name="scenario2"></a>2. forgatókönyv: "I vagyok telepít át virtuális gépeket más platformokon a prémium szintű Storage."
Ha az áttelepítés VHD-t a nem - Azure felhőalapú tárolást az Azure-ba, először exportálnia kell a virtuális merevlemez helyi könyvtárba. A teljes forrás könyvtár elérési útja a helyi virtuális merevlemez tároló lesz szüksége van, és az AzCopy segítségével töltse fel az Azure Storage.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>1. lépés A VHD exportálása egy helyi könyvtárba
##### <a name="copy-a-vhd-from-aws"></a>Másolja a VHD-t AWS
1. Ha AWS használ, exportálja a EC2 példány az Amazon S3 gyűjtő virtuális. Az Amazon EC2 példányok telepítse az Amazon EC2 parancssori felület (CLI) eszközt, és a létrehozás-példány-export-tevékenység parancsot a EC2 példány exportálni egy VHD-fájl exportálása Amazon dokumentációjában ismertetett lépéseket követve. Használjon **VHD** a lemez &#95; kép &#95; FORMÁTUM változó futtatásakor a **-példány-export-feladat létrehozása** parancsot. Az exportált VHD-fájl kerül az Amazon S3 gyűjtő jelöl ki, a folyamat során.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Töltse le a VHD-fájlt a S3 gyűjtő. Válassza ki a VHD-fájlt, majd **műveletek** > **letöltése**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Másolja a VHD-t más-Azure felhő
Ha az áttelepítés VHD-t a nem - Azure felhőalapú tárolást az Azure-ba, először exportálnia kell a virtuális merevlemez helyi könyvtárba. Másolja a teljes forrás könyvtár elérési útja a helyi virtuális merevlemez tárolásához.

##### <a name="copy-a-vhd-from-on-premises"></a>Másolja a VHD-t a helyszíni
Ha a VHD-t a helyszíni környezetben telepít, szüksége lesz a virtuális merevlemez tárolásához, a teljes forrás elérési útja. A forrás elérési útja egy helyen vagy kiszolgálómegosztás lehet.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>2. lépés A cél a virtuális merevlemez létrehozása
Hozzon létre egy tárfiókot, a virtuális merevlemezek karbantartásához. A virtuális merevlemezek tárolási helyének megtervezésekor, vegye figyelembe a következő szempontokat:

* A céloldali tárfiók lehet standard vagy prémium szintű storage, attól függően, hogy az alkalmazás követelményeinek.
* A tárfiók régiója meg kell egyeznie a prémium szintű Storage kompatibilis Azure virtuális gépek létrehozhat utolsó szakaszában. Nem átmásolni egy új tárfiókot, vagy a igények alapján ugyanazt a tárfiókot használni szeretne.
* Másolja ki és mentse a tárfiók hívóbetűjét, a cél tárfiókkal a következő szakaszban.

Határozottan javasoljuk, a prémium szintű storage üzemi terhelés minden adat áthelyezése.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>3. lépés A VHD-fájlt feltölti az Azure Storage
Most, hogy a VHD-t a helyi címtárban, AzCopy vagy AzurePowerShell használhatja a .vhd fájl feltöltése az Azure Storage. Mindkét lehetőség itt találhatók:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>1. lehetőség: Azure PowerShell Add-AzureVhd fel kell töltenie a .vhd fájlt használ.

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Példa <Uri> előfordulhat, hogy ***"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"***. Példa <FileInfo> előfordulhat, hogy ***"C:\path\to\upload.vhd"***.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>2. lehetőség: Az AzCopy segítségével a .vhd fájl feltöltése
Használja az AzCopy, egyszerűen feltöltheti a VHD-t az interneten keresztül. A virtuális merevlemezek méretétől függően ez időt vehet igénybe. Fontos, hogy ellenőrizze a tárfiókok be-és kilépési korlátai, ez a beállítás használata esetén. Lásd: [Azure Storage méretezhetőségi és teljesítménycéloknak](storage-scalability-targets.md) részleteiről.

1. Töltse le és telepítse az AzCopy innen: [az AzCopy legújabb verzióját](http://aka.ms/downloadazcopy)
2. Nyissa meg az Azure PowerShell és a mappában, amelyen telepítve van-e az AzCopy.
3. A következő parancs segítségével másolja a VHD-fájlt a "Forrás" a "Cél".

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Példa:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
    ```

    Az alábbiakban az AzCopy parancs paraméterei leírása:

   * **/ Forrás:  *&lt;forrás&gt;:***  a mappa vagy a tárolási tároló URL-címet, amely a VHD-t tartalmaz.
   * **/ SourceKey:  *&lt;forrás fiókkulcs&gt;:***  a forrás tárfiók Tárfiók kulcsa.
   * **/ Dest:  *&lt;cél&gt;:***  másolja a VHD-fájlt tároló tároló URL-címet.
   * **/ DestKey:  *&lt;cél fiókkulcs&gt;:***  a cél tárfiókkal a Tárfiók kulcsára.
   * **/ BlobType: lap:** Megadja, hogy a cél oldalakra vonatkozó blob.
   * **/ Mintát:  *&lt;Fájlnév&gt;:***  adja meg a fájlnevet a virtuális merevlemez másolása.

AzCopy használatával eszköz, lásd: [adatátvitel az AzCopy parancssori segédprogram a](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Más beállításokat a virtuális merevlemez feltöltése
Feltöltheti a virtuális merevlemez a tárfiókhoz, az alábbi eszközök egyikével:

* [Azure Storage Copy Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Az Azure Storage Explorer feltöltése a BLOB](https://azurestorageexplorer.codeplex.com/)
* [Storage Import/Export szolgáltatás REST API-referencia](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> Ajánlott Import/Export szolgáltatás használata, ha becsült a 7 napnál hosszabb idő feltöltése. Használhat [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) becsléséhez adatok méretét és átviteli egység időpontját.
>
> Importálási/exportálási segítségével másolja egy standard szintű tárfiókot. Szüksége lesz a prémium szintű storage-fiókra egy eszköz, például az AzCopy standard tárolási másolja.
>
>

## <a name="create-azure-virtual-machine-using-premium-storage"></a>Prémium szintű Storage használata Azure virtuális gépek létrehozása
Után a virtuális merevlemez töltenek fel vagy másolja a kívánt tárfiókot, kövesse az ebben a szakaszban található a virtuális merevlemez regisztrálja az operációsrendszer-lemezképek, vagy a forgatókönyvtől függően az operációsrendszer-lemez, és a Virtuálisgép-példány készíteni. A virtuális merevlemez adatlemeze csatolható a virtuális gép létrehozása után.
Ez a szakasz végén egy áttelepítési parancsfájlt valósul meg. Ez egyszerű parancsprogram nem felel meg minden forgatókönyvben. A parancsfájl az adott helyzetnek megfelelő frissítésére lehet szükség. Ha ezt a parancsfájlt a forgatókönyv vonatkozik-e, olvassa el alább [A Parancsfájlpéldát áttelepítési](#a-sample-migration-script).

### <a name="checklist"></a>Feladatlista
1. Várja meg, amíg minden, a Másolás VHD lemezek befejeződött.
2. Ellenőrizze, hogy prémium szintű Storage érhető el a régióban végzi az áttelepítést.
3. Döntse el, az új Virtuálisgép-sorozat fog használni. A prémium szintű Storage képes legyen, és a méret kell a rendelkezésre állási régióban függően előfordulhat, és igényei szerint.
4. Döntse el, a pontos használandó Virtuálisgép-méretet. Virtuálisgép-méretet kell lennie, elég nagy legyen rendelkezik adatlemezek számának támogatásához. Például Ha 4 adatlemezek, a virtuális gép 2 vagy több maggal kell rendelkeznie. Fontolja meg is, a feldolgozási kapacitása, memória, és a hálózati sávszélesség igényeinek megfelelően.
5. Prémium szintű Storage-fiók létrehozása a cél régióban. Ez az a fiók, az új virtuális gép használja.
6. Az aktuális virtuális gép adatai lesz szüksége, beleértve a megfelelő VHD-blobok és lemezek listáját rendelkezik.

Készítse elő az állásidő alkalmazását. Egy tiszta az áttelepítés végrehajtásához, akkor állítsa le a feldolgozás az aktuális rendszerben. Csak ezután beszerezheti a konzisztens állapotú. Ez az új platformon is áttelepíthetők. Állásidő időtartama áttelepítéséhez a lemezeken mennyiségétől függ.

> [!NOTE]
> Az Azure Resource Manager virtuális gép speciális VHD lemez létrehozásakor, tekintse meg [sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) erőforrás-kezelő virtuális gépet a meglévő lemezt telepítéséhez.
>
>

### <a name="register-your-vhd"></a>A virtuális merevlemez regisztrálása
A virtuális gép létrehozása az operációs rendszer virtuális merevlemezről vagy adatlemezt csatolni egy új virtuális Gépet, először regisztrálnia kell őket. Kövesse az alábbi lépéseket attól függően, hogy a VHD-forgatókönyv.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Operációs rendszer virtuális merevlemez létrehozása több Azure Virtuálisgép-példányok általánosítva
Miután általánosított virtuális Merevlemezt az operációsrendszer-lemezképek feltöltése a tárfiókba, regisztrálja azt egy **Azure Virtuálisgép-lemezkép** , hogy egy vagy több Virtuálisgép-példányok hozhat létre belőle. A következő PowerShell-parancsmagok segítségével regisztrálja a virtuális merevlemez, egy Azure virtuális gép operációsrendszer-lemezképben. Adja meg a teljes tároló URL-cím, ahol VHD lett másolva.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Másolja ki és mentse az új Azure Virtuálisgép-lemezkép nevét. A fenti példa, hogy a rendszer *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Egyedi, operációs rendszer virtuális merevlemez egyetlen Azure Virtuálisgép-példány létrehozása
Az egyedi az operációs rendszer virtuális merevlemez feltöltése a tárfiókba, után regisztrálja azt egy **Azure operációsrendszer-lemez** , hogy egy Virtuálisgép-példányt hozhat létre belőle. PowerShell-parancsmagok segítségével regisztrálja a VHD-t Azure operációsrendszer-lemezként. Adja meg a teljes tároló URL-cím, ahol VHD lett másolva.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Másolja ki és mentse az új Azure operációsrendszer-lemez neve. A fenti példa, hogy a rendszer *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Adatok lemez virtuális merevlemez csatolva legyen új Azure Virtuálisgép-példányokat
Miután a virtuális merevlemez adatlemeze tárfiókkal töltheti fel, regisztrálja egy Azure-adatlemez, úgy, hogy az új DS-méretek, DSv2-méretek és GS adatsorozat Azure Virtuálisgép-példány csatolható.

PowerShell-parancsmagok segítségével regisztrálja a virtuális merevlemez egy Azure-adatok tárolására. Adja meg a teljes tároló URL-cím, ahol VHD lett másolva.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Másolja ki és mentse az új Azure-adatlemez nevét. A fenti példa, hogy a rendszer *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Prémium szintű Storage képes a virtuális gép létrehozása
Egyszer operációsrendszer-lemezképet, vagy az operációsrendszer-lemez van regisztrálva, új DS-méretek, DSv2-sorozat vagy GS sorozatnak virtuális gép létrehozása. Fogja használni az operációs rendszeri lemezkép vagy operációs rendszer Lemeznév regisztrált. Válassza ki a virtuális gép a prémium szintű Storage rétegtől. Az alábbi példában használjuk a *Standard_DS2* Virtuálisgép-méretet.

> [!NOTE]
> A lemez mérete győződjön meg arról, hogy megegyezzen a kapacitás és teljesítmény követelményeket, valamint az elérhető Azure lemezméret frissítése.
>
>

Kövesse az új virtuális gép létrehozása az alábbi lépésről lépésre PowerShell-parancsmagok. Első lépésként állítsa be a következő általános paramétereket:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

Először hozzon létre egy felhőalapú szolgáltatás, amelyben, amelyen az új virtuális gépek.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

Ezt a forgatókönyvtől függően létrehozni az operációsrendszer-lemezképek vagy az operációs rendszer lemezének regisztrált az Azure Virtuálisgép-példány.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Operációs rendszer virtuális merevlemez létrehozása több Azure Virtuálisgép-példányok általánosítva
Hozzon létre egy vagy több új DS adatsorozat Azure Virtuálisgép-példányára használatával a **Azure operációsrendszer-lemezképek** regisztrált. Adja meg a operációsrendszer-lemezképek nevét a Virtuálisgép-konfiguráció, amikor új virtuális gép létrehozása az alább látható módon.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Egyedi, operációs rendszer virtuális merevlemez egyetlen Azure Virtuálisgép-példány létrehozása
Új létrehozása DS adatsorozat Azure virtuális gép példány a **Azure operációsrendszer-lemez** regisztrált. Adja meg az operációs rendszer lemezének neve a Virtuálisgép-konfiguráció, ha az új virtuális gép létrehozása az alább látható módon.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Adjon meg más Azure Virtuálisgép-adatok, például egy felhőalapú szolgáltatás, régió, tárfiókot, a rendelkezésre állási csoport és gyorsítótárazási házirend. Vegye figyelembe, hogy a Virtuálisgép-példány csak futó operációs rendszer vagy az adatlemezek együtt, a kijelölt felhőalapú szolgáltatás, valamint régió és tárolási fiók kell lenniük és ugyanazon a helyen az alapul szolgáló virtuális merevlemezek lemezek.

### <a name="attach-data-disk"></a>Adatlemez csatolása
Végül Ha adatlemezt VHD regisztrálta, csatolja az új prémium szintű Storage kompatibilis Azure virtuális gépen.

Használja a következő PowerShell-parancsmag adatlemezt csatolni az új virtuális Gépet, és adja meg a gyorsítótárazási házirendet. Az alábbi példában a gyorsítótárazási házirend beállítása *ReadOnly*.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> További lépésekre lehet szükség az alkalmazás, amely támogatja az útmutató nem vonatkoznak.
>
>

### <a name="checking-and-plan-backup"></a>Ellenőrzése és a biztonsági mentés tervezése
Ha az új virtuális gép fut, hozzáférhessenek megegyező bejelentkezési azonosítóval, és jelszó van, mint az eredeti virtuális gép, és győződjön meg arról, hogy minden az elvárásoknak megfelelően működik. Az összes beállítás a csíkozott kötetek, beleértve az új virtuális gép jelen lehet.

Az utolsó lépése az, hogy a biztonsági mentési terv, és az új virtuális gép karbantartási ütemezését az alkalmazás igények alapján.

### <a name="a-sample-migration-script"></a>Egy áttelepítési parancsfájlt
Ha több virtuális gép áttelepítése, automatizálás PowerShell-parancsprogramok hasznos lehet. Az alábbiakban látható egy minta parancsfájlt, amely automatizálja az áttelepítés a virtuális gépek. Megjegyzés: alábbi parancsfájl, amely csak egy példa, és nincsenek a jelenlegi virtuális gép lemezeivel kapcsolatos tett néhány feltételezéseket. A parancsfájl az adott helyzetnek megfelelő frissítésére lehet szükség.

Az Előfeltételek a következők:

* Klasszikus Azure virtuális gépek létrehozásakor.
* A forrás operációs rendszer és a forrás adatok lemezek vannak ugyanabban a tárfiókban és az ugyanabban a tárolóban. Ha az operációs rendszer és a adatok lemezek nem ugyanazon a helyen, AzCopy vagy az Azure PowerShell használatával virtuális merevlemezek másolja át a storage-fiókok és a tárolók. Tekintse meg az előző lépésben: [másolási VHD AzCopy vagy a PowerShell használatával](#copy-vhd-with-azcopy-or-powershell). Ezt a parancsfájlt a forgatókönyvnek megfelelő szerkesztése egy újabb választási lehetőség, de azt javasoljuk, mert az egyszerűbb és gyorsabb AzCopy vagy a PowerShell használatával.

Az automatizálási parancsfájl lejjebb tekinthetők meg. Szöveg cseréje az adatait, és frissítse a parancsfájlt, amellyel felelnek meg az adott forgatókönyv.

> [!NOTE]
> A meglévő parancsfájl használatával nem őrzi meg a hálózati konfigurációt a forrás virtuális gép. Szüksége lesz az ismételt-config a hálózati beállításokat az áttelepített virtuális gépeken.
>
>

```
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
    http://azure.microsoft.com/documentation/articles/powershell-install-configure/
    http://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

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

    # how frequently to report the copy status in sceconds
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
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module verion" -ForegroundColor Yellow
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
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article http://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
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

    # exporting the sourve vm to a configuration file, you can restore the original VM by importing this config file
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
    $sourceStorageKey = (Get-AzureStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzureStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzureStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzureStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzureStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzureStorageContainer -Context $destContext -Name vhds
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
        $destOSVHD = Get-AzureStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting utill the OS disk is released by source VM. This may take up to several minutes."
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
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
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
            $copyState = Get-AzureStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
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

    # Edit this if you want to add more custimization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location
```

#### <a name="optimization"></a>Optimalizálás
Az aktuális Virtuálisgép-konfiguráció kifejezetten az működnek jól Standard lemezek szabható testre. Például a teljesítmény növelése érdekében azáltal, hogy sok csíkozott kötetek használatával. Például helyett 4 lemezek külön-külön a prémium szintű Storage, esetleg képes optimalizálni a költségeket azzal, hogy egyetlen lemezre. Optimalizálás, például a szükséges eseti alapon kell kezelni, és egyéni lépéseket igényelnek az áttelepítés után. Emellett vegye figyelembe, hogy ez a folyamat jól nem feltétlenül alkalmas adatbázisok és a lemez elrendezése, a telepítő definiált függő alkalmazások.

##### <a name="preparation"></a>Előkészítése
1. Az áttelepítéshez egyszerű a korábbi szakaszban leírtak szerint. Optimalizálás végrehajtására kerül sor az új virtuális Gépet az áttelepítés után.
2. Adja meg az új lemez méretét, az optimalizált konfigurálásához szükséges.
3. Határozza meg, és az új lemez paramétereknek aktuális lemezek vagy kötetek hozzárendelését.

##### <a name="execution-steps"></a>Végrehajtási lépések
1. Hozzon létre új lemezek a megfelelő méretek a prémium szintű Storage virtuális Gépre.
2. Bejelentkezés a virtuális gép és az adatok másolása az aktuális kötetről, hogy az új lemezt, amely, hogy a köteten van leképezve. Ehhez a jelenlegi köteteket, amelyeket egy új lemezre van leképezve.
3. Ezután a váltson át az új lemezek beállításainak módosítása, és a régi kötetet leválasztani.

Az alkalmazás a jobb teljesítmény érdekében lemez hangolása, olvassa el [alkalmazások teljesítményének optimalizálása](../../virtual-machines/windows/premium-storage-performance.md#optimizing-application-performance).

### <a name="application-migrations"></a>Alkalmazás-áttelepítések
Adatbázisok és más összetett alkalmazások lehet szükség különleges lépések az alkalmazás-szolgáltató az áttelepítés által definiált konfigurációjának kialakításához. Tekintse meg a megfelelő alkalmazás dokumentációját. Például általában adatbázisok telepíthetők át a biztonsági mentés és visszaállítás.

## <a name="next-steps"></a>További lépések
A virtuális gépek meghatározott forgatókönyvek a következő cikkekben találhat:

* [Az Azure virtuális gépek közötti Storage-fiókok áttelepítése](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Hozzon létre, és a Windows Server VHD feltöltése az Azure-bA.](../../virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Létrehozás és a Linux operációs rendszert tartalmazó virtuális merevlemez feltöltése](../../virtual-machines/linux/classic/create-upload-vhd-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Virtuális gépek áttelepítési Amazon AWS a Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

A következő források további információt az Azure Storage és az Azure virtuális gépek lásd még:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-beli virtuális gépek számítási feladataihoz](../../virtual-machines/windows/premium-storage.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: http://technet.microsoft.com/library/hh831739.aspx
