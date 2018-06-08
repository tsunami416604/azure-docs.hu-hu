---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: rogara
ms.service: storage
ms.topic: include
ms.date: 04/09/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f64645db782b055e1c544f257149411f29fc99d7
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "34806315"
---
## <a name="about-vhds"></a>Tudnivalók a VHD-kről
Az Azure-ban használt VHD-k .vhd fájlok, amelyek lapblobként vannak tárolva egy standard vagy prémium szintű Azure-tárfiókban. A lapblobokkal kapcsolatos további részletekért tekintse meg [a blokkblobokat és a lapblobokat bemutató cikket](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/). A prémium szintű tárolással kapcsolatos részletekért tekintse meg [a nagy teljesítményű Premium Storage szolgáltatással és az Azure virtuális gépekkel kapcsolatos cikket](../articles/virtual-machines/windows/premium-storage.md).

Az Azure támogatja a rögzített lemezes VHD formátumot. A rögzített formátum a logikai lemezt lineárisan helyezi el a fájlon belül, így az X lemezeltolás az X blobeltolásnál van tárolva. A blob végén egy kis lábléc írja le a VHD tulajdonságait. A rögzített formátumú hulladékok gyakran, mert legtöbb lemez nagy nem használt tartományok a bennük foglalt lemezterület. Az Azure azonban egy ritka formátumban tárolja a .vhd fájlokat, így a rögzített és a dinamikus lemezek előnyei egyaránt kihasználhatók. További információkért lásd: [Ismerkedés a virtuális merevlemezek](https://technet.microsoft.com/library/dd979539.aspx).

Az Azure-lemezek vagy képek létrehozása forrásként használni kívánt VHD-fájlokat írásvédettek, kivéve a .vhd fájlokat feltölteni, vagy másolja az Azure storage (amely lehet olvasási és írási vagy olvasási) a felhasználó által. Amikor létrehoz egy lemezen vagy lemezképen, Azure másolatot készít a forrás .vhd-fájlokat. Ezek a másolatok csak olvashatók vagy olvashatók és írhatók is lehetnek a VHD használatától függően.

Amikor egy virtuális gépet hoz létre egy rendszerképből, az Azure létrehoz egy lemezt a virtuális gép számára, amely a forrás .vhd fájl másolata. A véletlen törlés elleni védelem érdekében az Azure egy bérlést helyez minden olyan forrás .vhd fájlra, amelyet egy rendszerkép, egy operációsrendszer-lemez vagy egy adatlemez létrehozásához használtak.

Mielőtt a forrás .vhd fájlt törölhetné, el kell távolítania a bérlést, ehhez pedig törölnie kell a lemezt vagy a rendszerképet. Ha törölni szeretne egy virtuális gép által operációsrendszer-lemezként használt .vhd fájlt, egyszerre is törölheti a virtuális gépet, az operációsrendszer-lemezt és a forrás .vhd fájlt a virtuális gép és az összes hozzá tartozó lemez törlésével. Egy adatlemez forrásaként használt .vhd fájl törléséhez azonban több lépést is el kell végezni, egy megadott sorrendben. Először válassza le a lemezt a virtuális gépről, majd törölje a lemezt, és végül törölje a .vhd fájlt.

> [!WARNING]
> Ha töröl egy forrás .vhd fájlt a tárolóból, vagy törli a saját tárfiókját, a Microsoft nem tudja visszaállítani az adatait.

## <a name="types-of-disks"></a>Lemeztípusok 
Az Azure Disks 99,999%-os elérhetőséggel büszkélkedhet. Azure-lemezeket következetesen szállított vállalati szintű tartósságot, és egy iparágvezető Annualized hibaaránya %.

Nincsenek három teljesítmény szintek közül választhat a lemezek--Premium SSD lemezek, a Standard SSD (előzetes verzió) és a szabványos HDD-tárolási létrehozásakor tárolására. Emellett lemezek--nem felügyelt, és a felügyelt két típusa van.

### <a name="standard-hdd-disks"></a>Standard HDD-lemezek
Standard HDD lemezek HDD üzemelnek, és költséghatékony tárolás biztosításához. Standard HDD-tárolási replikálható helyileg egy adatközpontban, vagy georedundáns az elsődleges és másodlagos adatközpontokban. Tároló-replikációval kapcsolatos további információkért lásd: [Azure Storage replikációs](../articles/storage/common/storage-redundancy.md). 

Standard HDD lemezek használatával kapcsolatos további információkért lásd: [standard szintű tárolást és a lemezek](../articles/virtual-machines/windows/standard-storage.md).

### <a name="standard-ssd-disks-preview"></a>Standard SSD-lemezek (előzetes verzió)
Standard SSD-lemezek munkaterhelések ugyanolyan típusú szabványos HDD lemezként meg, de egységesebb teljesítmény és megbízhatóbb a HDD tervezték. Standard SSD-lemezek elemek prémium SSD és a szabványos merevlemez lemezek űrlaphoz egy költséghatékony megoldás az olyan alkalmazások, például a webkiszolgálók, a lemezek magas iops értéket nem igénylő alkalmas kombinálása. Ahol az rendelkezésre áll, a Standard SSD lemezek a munkaterhelések többségéhez ajánlott telepítési beállítást. Standard SSD-lemezek csak felügyelt lemezként, és miközben a kép csak állnak rendelkezésre a [válassza ki a régiók](../articles/virtual-machines/windows/faq-for-disks.md) és a helyileg redundáns tárolás (LRS) rugalmasság típusa.

### <a name="premium-ssd-disks"></a>Premium SSD-lemezek 
Premium SSD lemezek SSD meghajtókon üzemelnek, és nagy teljesítményű, alacsony késésű lemez I/O-igényes munkaterhelések futó virtuális gépeket támogatását biztosítja. Általában van szükség, amelyek tartalmazzák az "s". az adatsorozat neve prémium SSD-lemezek is használhatók. Például a Dv3-sorozat van, és a Dsv3-sorozat, a Dsv3-sorozat használható prémium SSD lemezek.  További információk: [Premium Storage](../articles/virtual-machines/windows/premium-storage.md).

### <a name="unmanaged-disks"></a>Nem felügyelt lemezek
A nem felügyelt lemez a virtuális gépek által használt hagyományos lemeztípus. Ezek a lemezek, a saját storage-fiók létrehozása, és adjon meg, hogy a tárfiókot, a lemez létrehozásakor. Ellenőrizze, hogy túl sok be nem ugyanazt a tárfiókot, mert sikerült túllépi a [méretezhetőségi célok](../articles/storage/common/storage-scalability-targets.md) a tárfiók (20 000 iops-érték, például), ami azt eredményezi, hogy a virtuális gépek szabályozva. Nem felügyelt lemezek esetén Önnek kell felmérnie, hogyan maximalizálhatja egy vagy több tárfiók kihasználtságát, hogy a virtuális gépeiből a lehető legjobb teljesítményt hozza ki.

### <a name="managed-disks"></a>Felügyelt lemezek 
A felügyelt lemezek a háttérben végzik a tárfiók létrehozását és kezelését, hogy Önnek ne kelljen foglalkoznia a tárfiók skálázási korlátaival. Egyszerűen csak adja meg a lemez méretét és teljesítményszintjét (Standard vagy prémium), és az Azure létrehozza és felügyeli a lemezt Ön helyett. Nem kell a használt tárterület miatt aggódnia, amikor lemezeket ad hozzá, vagy fel-/leskálázza a virtuális gépet. 

Az egyéni rendszerképeit Azure-régiónként egyetlen tárfiókban is felügyelheti, és a használatukkal több száz virtuális gépet hozhat létre egy adott előfizetésben. A felügyelt lemezekkel kapcsolatos további információért tekintse meg [a felügyelt lemezek áttekintésével kapcsolatos cikket](../articles/virtual-machines/windows/managed-disks-overview.md).

Javasoljuk, hogy az új virtuális gépeihez használja az Azure Managed Disks szolgáltatást, és a korábbi, nem felügyelt lemezeit is alakítsa felügyeltté, hogy kihasználhassa a Managed Disks által kínált számos szolgáltatást.

### <a name="disk-comparison"></a>Lemezek összehasonlítása
A következő táblázat összehasonlítása szabványos HDD, szabványos SSD és a nem felügyelt és felügyelt prémium SSD segítségével eldöntheti, mit kell használni.

|    | Prémium szintű Azure-lemez |Az Azure szabványos SSD lemez (előzetes verzió)| Az Azure szabványos HDD-lemez 
|--- | ------------------ | ------------------------------- | ----------------------- 
| Lemez típusa | SSD-k | SSD-k | HDD-k  
| Áttekintés  | SSD-alapú, nagy teljesítményű, kis késleltetésű lemeztámogatás a nagy adatátviteli teljesítményt igénylő számítási feladatokat vagy az üzletmenet szempontjából kritikus fontosságú éles környezeteket futtató virtuális gépek számára |Egységesebb teljesítménye és megbízhatósága HDD-nál. Alacsony – IOPS munkaterhelések optimalizálva| Gyakori hozzáférés költséghatékony lemez HDD-alapú
| Forgatókönyv  | Éles, teljesítményérzékeny számítási feladatok |Webkiszolgálók, a könnyebb használt vállalati alkalmazások és a fejlesztési és tesztelési célú| Biztonsági mentés, a nem kritikus, gyakori hozzáférés 
| Lemezméret | P4: GiB 32 (csak a felügyelt lemezek)<br>P6: 64 GiB (csak a felügyelt lemezek)<br>P10: 128 GiB<br>P15: 256 GiB (csak a felügyelt lemezek)<br>P20: 512 GiB<br>P30: 1024 GiB<br>P40: 2048 GiB<br>P50: 4095 GiB |Csak által kezelt lemezeken:<br>E10: 128 GiB<br>E15: 256 GiB<br>E20: 512 GiB<br>E30: 1024 GiB<br>E40: 2048 GiB<br>E50: 4095 GiB | Nem felügyelt lemezek: 1 GiB – 4 TiB (4095 GB) <br><br>Managed Disks:<br> S4: 32 GiB <br>S6: 64 GiB <br>S10: 128 GiB <br>S15: 256 GiB <br>S20: 512 GiB <br>S30: 1024 GiB <br>S40: 2048 GiB<br>S50: 4095 GiB
| Lemezenkénti maximális átviteli sebesség | 250 MiB/s | Legfeljebb 60 MiB/s | Legfeljebb 60 MiB/s 
| Lemezenkénti maximális IOPS-érték | 7500 IOPS | Legfeljebb 500 IOPS | Legfeljebb 500 IOPS 