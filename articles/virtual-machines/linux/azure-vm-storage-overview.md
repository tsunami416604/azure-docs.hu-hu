---
title: "Az Azure Linux virtuális gépek és az Azure Storage |} Microsoft Docs"
description: "Ismerteti az Azure Standard és prémium szintű Storage és a felügyelt és nem felügyelt lemezek Linux virtuális gépekkel."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: d364c69e-0bd1-4f80-9838-bbc0a95af48c
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 2/7/2017
ms.author: rasquill
ms.openlocfilehash: 934580f6fcfdbff6e61626ed685459478559717d
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
---
# <a name="azure-and-linux-vm-storage"></a>Azure és a Linux virtuális gép tárolása
Az Azure Storage az ügyfelek igényeihez alkalmazkodó, modern, a tartósságra, rendelkezésre állásra és méretezhetőségre tervezett alkalmazásokhoz legmegfelelőbb felhőalapú tárolómegoldás.  Mellett lehetővé teszi a fejlesztők számára új forgatókönyvekhez nagyméretű alkalmazások létrehozását, Azure Storage emellett tárolási alapokat az Azure virtuális gépek.

## <a name="managed-disks"></a>Felügyelt lemezek

Azure virtuális gépek is elérhető használatával [Azure kezelt lemezek](../windows/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), amely lehetővé teszi a virtuális gépek létrehozása és kezelése bármely nélkül létrehozásához [Azure Storage-fiókok](../../storage/common/storage-introduction.md) saját kezűleg. Megadhatja, hogy Premium vagy standard szintű tárolást és mekkora a lemeznek kell lennie, és Azure a virtuális gépek lemezei létrehozza. Felügyelt lemezzel rendelkező virtuális gépek számos fontos funkciót, beleértve a rendelkezik:

- Az automatikus méretezhetőség támogatása. Azure hoz létre a lemezeket, és kezeli a mögöttes tároló előfizetésenként legfeljebb 10 000 lemezek támogatásához.
- A rendelkezésre állási készletek megbízhatóbbak. Azure biztosítja, hogy virtuális gépek lemezei el különítve egymástól rendelkezésre állási készletek belül automatikusan.
- Jobb hozzáférés-vezérlés. Felügyelt lemezek teszi közzé a különféle műveletek által vezérelt [átruházásához hozzáférés-vezérlés (RBAC)](../../active-directory/role-based-access-control-what-is.md).

Felügyelt lemezek árképzési eltér attól az adott nem felügyelt lemezek. Ez az információ, lásd: [árak és számlázás felügyelt lemezek](../windows/managed-disks-overview.md#pricing-and-billing).

Átalakíthatja a meglévő virtuális gépek, amelyek nem felügyelt lemezek segítségével felügyelt lemezek, amelyek [az virtuális gép átalakítása](/cli/azure/vm#convert). További információkért lásd: [átalakítása nem felügyelt lemezekből Linux virtuális gép Azure felügyelt lemezek](convert-unmanaged-to-managed-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Egy nem felügyelt lemezt nem konvertálható felügyelt lemezre, ha a nem kezelt lemez, vagy bármikor már, használatával titkosított tárfiókokban [Azure Storage szolgáltatás titkosítási (SSE)](../../storage/common/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Az alábbi lépéseket a átalakítása nem felügyelt lemezek vannak, illetve, hogy egy titkosított tárfiókban lévő adatok találhatók:

- Másolja a virtuális merevlemez (VHD) [az tárolási blob másolási start](/cli/azure/storage/blob/copy#start) , amely soha nem engedélyezett az Azure Storage szolgáltatás titkosítási tárfiókba.
- Hozzon létre egy virtuális Gépet, amely felügyelt lemezt használ, és adja meg, hogy a VHD-fájl létrehozása során [az virtuális gép létrehozása](/cli/azure/vm#create), vagy
- Csatlakoztassa a másolt VHD [az méretű lemez csatolása](/cli/azure/vm/disk#attach) felügyelt lemezzel egy futó virtuális géphez.


## <a name="azure-storage-standard-and-premium"></a>Az Azure Storage: Standard és Premium
Azure virtuális gépek – hogy lemezekkel felügyelt vagy nem felügyelt--építhetők tárolólemezek standard vagy prémium szintű storage lemezek esetén. Amikor a portál segítségével válassza ki a virtuális gép akkor kell váltani egy legördülő lista a a **alapjai** képernyő standard és a prémium szintű lemezek megtekintéséhez. Amikor váltható SSD, csak az engedélyezett virtuális gépek megjelenik, prémium szintű storage összes üzemelnek SSD meghajtók.  Amikor váltható HDD, a standard tárolási alkalmas virtuális gépeken üzemelnek forgó lemezmeghajtók jelennek meg, hogy együtt prémium szintű tároló virtuális gépek által támogatott SSD.

A virtuális gép létrehozásakor a `azure-cli` választhat standard és premium a Virtuálisgép-méretet keresztül kiválasztásakor a `-z` vagy `--vm-size` cli jelzőt.

## <a name="creating-a-vm-with-a-managed-disk"></a>A felügyelt lemezes virtuális gép létrehozása

Az alábbi példa is Azure CLI 2.0 szükséges [telepítése Itt](/cli/azure/install-azure-cli).

Először hozzon létre egy erőforráscsoportot az erőforrások kezelése az [az csoport létrehozása](/cli/azure/group#create):

```azurecli
az group create --location westus --name myResourceGroup
```

Most létrehozza a virtuális Géphez a [az virtuális gép létrehozása](/cli/azure/vm#create). Adjon meg egy egyedi `--public-ip-address-dns-name` argumentum, mint a `mypublicdns` valószínűleg használatban van.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --public-ip-address-dns-name mypublicdns
```

Az előző példában egy virtuális Gépet egy standard szintű tárfiókot felügyelt lemezt hoz létre. A prémium szintű storage-fiókok használatához vegye fel a `--storage-sku Premium_LRS` argumentum, az alábbi példához hasonló:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --public-ip-address-dns-name mypublicdns \
    --storage-sku Premium_LRS
```

## <a name="standard-storage"></a>Standard szintű Storage
Az Azure standard szintű tárolót az alapértelmezett tároló típusa szerinti.  Standard szintű tárolót ugyanakkor továbbra is nem performant költséghatékony.  

## <a name="premium-storage"></a>Prémium szintű Storage
Prémium szintű Storage nagy teljesítményű, alacsony késésű lemez I/O-igényes munkaterhelések futó virtuális gépek támogatása nyújt. Prémium szintű Storage használó virtuális gép (VM) lemezek (SSD) adatait tárolják. Az alkalmazás virtuális gépek lemezei telepítheti át a prémium szintű Azure Storage hogy kihasználják a sebesség és a lemezek teljesítményét.

Prémium szintű storage szolgáltatások:

* Prémium szintű Storage lemezek: a prémium szintű Storage támogatja a DS, DSv2 és GS adatsorozat Azure virtuális gépekhez csatolt virtuális gépek lemezei.
* Prémium oldalakra vonatkozó Blob: Prémium szintű Storage támogatja az Azure Lapblobokat, állandó lemezek tárolására az Azure virtuális gépek (VM) használt.
* Prémium szintű helyileg redundáns tárolás: A prémium szintű Storage-fiókok csak helyileg redundáns tárolás (LRS) a replikációs beállításként támogatja és tartja az adatok három másolatot egyetlen régión belül.
* [Prémium szintű Storage](../windows/premium-storage.md)

## <a name="premium-storage-supported-vms"></a>Prémium szintű Storage támogatott virtuális gépek
Prémium szintű Storage támogatja a DS-méretek, DSv2-méretek, GS sorozatnak és Fs-sorozat Azure virtuális gépek (VM). A virtuális gépek támogatott prémium szintű Storage Standard és prémium szintű storage lemezek is használhatók. De Virtuálisgép-sorozat, amely nem kompatibilis a prémium szintű Storage nem használhatja a prémium szintű Storage lemezek.

Az alábbiakban a prémium szintű Storage jelenleg érvényesítve Linux Terjesztésekről.

| Disztribúció | Verzió | Támogatott Kernel |
| --- | --- | --- |
| Ubuntu |12.04 |3.2.0-75.110+ |
| Ubuntu |14.04 |3.13.0-44.73+ |
| Debian |7.x, 8.x |3.16.7-ckt4-1+ |
| SLES |SLES 12 |3.12.36-38.1+ |
| SLES |SLES 11 SP4 |3.0.101-0.63.1+ |
| CoreOS |584.0.0+ |3.18.4+ |
| Centos |6.5, 6.6, 6.7, 7.0, 7.1 |3.10.0-229.1.2.el7+ |
| RHEL |6.8+, 7.2+ | |

## <a name="azure-file-storage"></a>Az Azure File storage
Az Azure File storage kínál a felhőben, szabványos SMB protokollt használó fájlmegosztások. Az Azure Files áttelepítheti az Azure-bA fájlkiszolgálókon vállalati alkalmazások. Az Azure-ban futó alkalmazások könnyen csatlakoztathatnak fájlmegosztások a Linux operációs rendszert futtató Azure virtuális gépeken. És a legújabb kiadásra frissüljön a File storage, is csatlakoztathatja egy fájlmegosztást, amely támogatja az SMB 3.0 helyszíni alkalmazásból.  Mivel a fájlmegosztások SMB-megosztások, szabványos fájlrendszere API-k használatával is elérhet.

A File storage Blob, Table és Queue tárolóként ugyanazt a technológiát épül, ezért a File storage biztosít a rendelkezésre állási, tartóssága, méretezhetőség és az Azure storage platform be van építve a földrajzi redundancia. Fájl tárolási teljesítmény célozza meg, és korlátokat kapcsolatos részletekért lásd: Azure Storage méretezhetőségi és teljesítménycéloknak.

* [Az Azure File Storage használata Linuxszal](../../storage/files/storage-how-to-use-files-linux.md)

## <a name="hot-storage"></a>Gyakran használt adatok
Az Azure gyakran használt adatok tárolási rétege gyakran használt adatok tárolására van optimalizálva.  Gyakran használt adatok a blob-tárolóinak alapértelmezett tárolási típusa.

## <a name="cool-storage"></a>Ritkán használt adatok
Az Azure ritkán használt adatok tárolási rétege, amely ritkán használt, hosszú élettartamú adatok tárolására van optimalizálva. Példák az alkalmazási helyzetekre a ritkán használt adatok közé tartoznak a biztonsági mentések, médiatartalmak, tudományos adatok, megfelelőségi és archiválási adatok. Ritkán használt adatokat általában tökéletes jelöltségét ellenőrző ritkán használt adatok.

|  | Gyakran használt adatok tárolási rétege | Ritkán használt adatok tárolási rétege |
|:--- |:---:|:---:|
| Rendelkezésre állás |99.9% |99% |
| Rendelkezésre állás (RA-GRS olvasások) |99.99% |99.9% |
| Használati díjak |Magasabb tárolási költségek |Alacsonyabb tárolási költségek |
| Alacsonyabb hozzáférési |Magasabb hozzáférési | |
| és tranzakciós költségek |és tranzakciós költségek | |

## <a name="redundancy"></a>Redundancia
A Microsoft Azure-tárfiókban lévő adatokat mindig replikálódik a tartósság és magas rendelkezésre állás, az Azure Storage SLA még átmeneti hardverhibák esetén állásuk értekezlet biztosításához.

Amikor tárfiókot hoz létre, ki kell választania a következő replikációs lehetőségek egyikét:

* Helyileg redundáns tárolás (LRS)
* Zónaredundáns tárolás (ZRS)
* Georedundáns tárolás (GRS)
* Írásvédett georedundáns tárolás (RA-GRS)

### <a name="locally-redundant-storage"></a>Helyileg redundáns tárolás
Helyileg redundáns tárolás (LRS) replikálja az adatokat abban a régióban, amelyben létrehozta a tárfiókot. Maximális tartósságot, szemben a tárfiókban lévő adatokat kérelmek rendszer háromszor replikálja. Ezek a három replikák külön tartalék tartományok és a frissítési tartományok találhatók.  Kérelem sikeresen beolvasása csak egyszer minden három replikákra lett írva.

### <a name="zone-redundant-storage"></a>Zónaredundáns tárolás
Zónaredundáns tárolás (ZRS) keresztül két vagy három intézményben, egy vagy két régióban LRS-nél nagyobb tartósságot biztosít az adatok replikálásra. Ha a tárfiók a ZRS engedélyezve van, az adatok tartós egyik létesítmények hibája esetén is.

### <a name="geo-redundant-storage"></a>Georedundáns tárolás
Georedundáns tárolás (GRS) replikálja az adatokat egy másodlagos régióban, amelyek több száz miles elhagyja az elsődleges régióban van. Ha a tárfiók Georedundáns engedélyezve van, az adatok akkor tartós teljes regionális kimaradás vagy az elsődleges régióban nincs helyreállítható katasztrófa esetén is.

### <a name="read-access-geo-redundant-storage"></a>Írásvédett georedundáns tárolás
Írásvédett georedundáns tárolás (RA-GRS) rendelkezésre állási a tárfiók maximalizálja a csak olvasási hozzáféréssel a másodlagos helyen mellett két régióban is GRS biztosítja a replikációs adatok megadásával. Abban az esetben, ha az adatokat az elsődleges régióban elérhetetlenné válik, az alkalmazás lehet adatokat olvasni a másodlagos régióba.

A részletes bemutatója a redundancia lásd az Azure storage:

* [Azure Storage replication (Azure Storage replikáció)](../../storage/common/storage-redundancy.md)

## <a name="scalability"></a>Méretezhetőség
Az Azure Storage nagymértékben méretezhető, így a tudományos és üzleti elemzések, vagy a médiaalkalmazások big data forgatókönyvéhez szükséges több száz terabájtos adatkészleteket is tárolhatja és feldolgozhatja. Ugyanakkor a kisvállalkozások webhelyeihez szükséges kis mennyiségű adatokat is tárolhatja itt. Ha csökkennek az igényei, csak a tárolt adatmennyiség után kell fizetnie. Az Azure Storage jelenleg több tízbillió egyéni ügyfélobjektumot tárol, és másodpercenként átlagosan több millió lekérést szolgál ki.

Standard szintű storage-fiókok: egy standard szintű tárfiók van egy 20 000 IOPS maximális teljes kérelmek aránya. A Standard szintű tárfiókokon az összes virtuálisgép-lemezen lévő teljes IOPS nem léphet túl ezen a korláton.

Prémium szintű storage-fiókok: A prémium szintű tárfiók van egy 50 GB/s maximális teljes átviteli sebesség mértéke. Az összes virtuálisgép-lemezen lévő teljes átvitel nem lépheti túl ezt a korlátot.

## <a name="availability"></a>Rendelkezésre állás
Garantáljuk, hogy az idő legalább 99,99%-ában (a lassú elérési szint esetében 99,9%-ában) sikeresen feldolgozzuk a georedundáns írásvédett társzolgáltatási (RA-GRS) fiókokból érkező adatolvasási kérelmeket, feltéve, hogy az elsődleges régióból történő sikertelen adatolvasási kísérletek megismétlődnek a másodlagos régióban.

Garantáljuk, hogy az idő legalább 99,9%-ában (a lassú elérési szint esetében 99,9%-ában) sikeresen feldolgozzuk a helyileg redundáns társzolgáltatási (LRS), a zónaredundáns társzolgáltatási (ZRS) és a georedundáns társzolgáltatási (GRS) fiókokból érkező adatolvasási kérelmeket.

Garantáljuk, hogy az idő legalább 99,9%-ában (a lassú elérési szint esetében 99%-ában) sikeresen feldolgozzuk a helyileg redundáns társzolgáltatási (LRS), a zónaredundáns társzolgáltatási (ZRS), a georedundáns társzolgáltatási (GRS) és a georedundáns írásvédett társzolgáltatási (RA-GRS) fiókokba történő adatírást.

* [Tárolás az Azure SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_1/)

## <a name="regions"></a>Régiók
Azure általánosan elérhető világszerte 30 régiókban, és bejelentette 4 további régiókhoz tervek. Az Azure számára elsődleges fontosságú a földrajzi terjeszkedés, hiszen ezáltal segíthetünk ügyfeleinknek teljesítményük növelésében és biztosíthatunk igényeiknek megfelelő helyen adattárolási szolgáltatást.  Németország van azures legújabb régió elindításához.

A Microsoft Cloud németországi lehetővé teszi egy eltérő lehetőség már elérhető a Microsoft Cloud services Európa, nagyobb teret hagynak a innováció és magas szabályozott partnerek gazdasági növekedés és ügyfelek létrehozása a Németországi, a Európai Unió (EU) és az Európai szabad kereskedelmi társítás (EFTA).

Ezek új adatközpontokban Magdeburgban és Frankfurt, ügyféladatok egy adatok adatkezelő T-rendszerek nemzetközi, egy független német vállalati és német Telekom leányvállalata felügyelete alatt áll. A Microsoft kereskedelmi felhőszolgáltatások ezek adatközpontokban igazodnia kell német adatok kezelése szabályozásainak megfelelően, és hogyan és hol adatok feldolgozása a további lehetőségeket biztosít.

* [Azure-régiók térkép](https://azure.microsoft.com/regions/)

## <a name="security"></a>Biztonság
Az Azure Storage biztonsági képességeket, amelyek együtt lehetővé teszik a fejlesztők számára a biztonságos alkalmazások széles választékát nyújtja. A tárfiók magát a szerepköralapú hozzáférés-vezérlés és az Azure Active Directory használatával kell biztonságossá. A védett adatok az alkalmazás és az Azure közötti átvitel során ügyféloldali titkosítás, HTTPS és SMB 3.0-s. Adatok állítható be automatikusan titkosítja a tárhelyen, Azure Storage szolgáltatás titkosítási (SSE) használatával. Virtuális gépek által használt operációsrendszer- és adatlemezek állítható be Azure Disk Encryption titkosítja. Meghatalmazott hozzáférést biztosít az Azure Storage adatobjektumok megosztott hozzáférési aláírásokkal használatával engedélyezhetők.

### <a name="management-plane-security"></a>Felügyeleti Vezérlősík biztonsága
A felügyeleti vezérlősík a tárfiók kezelésére szolgáló erőforrások áll. Ebben a szakaszban lesz döntésről bővebben az Azure Resource Manager telepítési modell és a szerepköralapú hozzáférés-vezérlést (RBAC) használata a storage-fiókok való hozzáférés vezérlése érdekében. A tárfiókok kulcsait, és hogyan generálja újra őket kezelése is előadás.

### <a name="data-plane-security"></a>Adatbiztonság Vezérlősík
Ebben a szakaszban megnézzük, amely hozzáférést biztosít a tényleges adatok objektumok tárfiókba blobok, fájlok, üzenetsorokat és táblákat, például megosztott hozzáférési aláírásokkal és hozzáférési házirendek tárolja. Bemutatjuk a szolgáltatásiszint-SAS és a fiók szintű SAS. Azt is megtudhatja, hogyan korlátozhatja az egy adott IP-cím (vagy az IP-címek), hogyan legyen korlátozva a HTTPS protokoll és egy közös hozzáférésű Jogosultságkód visszavonása lejár való várakozás nélkül.

## <a name="encryption-in-transit"></a>Az átvitel során titkosítás
Ez a szakasz tárgyalja biztosítani az adatokat, ha azt át virtuális gépbe vagy onnan Azure Storage. Ajánlott a HTTPS és a titkosítás, az Azure fájlmegosztások SMB 3.0 által használt használatáról lesz döntésről. Most elindítjuk ügyféloldali titkosítás, mely lehetővé teszi az adatok titkosítását az ügyfélalkalmazás továbbított a tárolóba és az adatok visszafejtéséhez után tárolási kivitt egy pillantást is.

## <a name="encryption-at-rest"></a>Aktívan titkosítása
Az előadás Storage Service Encryption (SSE), és hogyan engedélyezi azt egy tárfiókot, ami azt eredményezi, a blokkblobokat, lapblobokat, és hozzáfűző blobok Azure tárhelyen, ha az automatikusan titkosított. Hogyan használja az Azure Disk Encryption, és megismerkedhet az alapvető különbséget és az adatok titkosítása és SSE ügyféloldali titkosítás és esetek is fog keresni. Röviden: a FIPS előírásainak való megfelelést az USA fog keresni Kormánya számítógépek.

* [Az Azure Storage biztonsági útmutató](../../storage/common/storage-security-guide.md)

## <a name="temporary-disk"></a>Ideiglenes lemez
Minden virtuális gép ideiglenes lemezt tartalmaz. Az ideiglenes lemezre rövid távú tárolás biztosít az alkalmazások és folyamatok, és csak az adatok, például az oldal vagy swap fájlok tárolására szolgál. Az ideiglenes lemezen lévő adatok elveszhetnek során egy [karbantartási esemény](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) és mikor meg [újratelepíteni a virtuális gépek](redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). A virtuális gép a szabványos újraindításkor az ideiglenes meghajtón található adatokat kell megőrizni.

A Linux virtuális gépeken, a lemez általában **/dev/sdb** formázott és a csatlakoztatott **/mnt** az Azure Linux ügynök. Változik a ideiglenes lemez méretét, a virtuális gép méretétől függ. További információkért lásd: [Linux virtuális gépek méretei](sizes.md).

Hogyan Azure használja az ideiglenes lemez a további információkért lásd: [az ideiglenes meghajtón a Microsoft Azure virtuális gépeken ismertetése](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="cost-savings"></a>Költségmegtakarítások
* [Tárolási költségek](https://azure.microsoft.com/pricing/details/storage/)
* [A tárolási költségeket Számológép](https://azure.microsoft.com/pricing/calculator/?service=storage)

## <a name="storage-limits"></a>Tárolási korlátai
* [Storage szolgáltatásra vonatkozó korlátozások](../../azure-subscription-service-limits.md#storage-limits)
