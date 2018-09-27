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
ms.openlocfilehash: 26c3e8a096a3ee7bc5146759f8de62e5c1c7fed1
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47389854"
---
## <a name="about-vhds"></a>Tudnivalók a VHD-kről

Az Azure-ban használt VHD-k .vhd fájlok, amelyek lapblobként vannak tárolva egy standard vagy prémium szintű Azure-tárfiókban. A lapblobokkal kapcsolatos további részletekért tekintse meg [a blokkblobokat és a lapblobokat bemutató cikket](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/). A prémium szintű tárolással kapcsolatos részletekért tekintse meg [a nagy teljesítményű Premium Storage szolgáltatással és az Azure virtuális gépekkel kapcsolatos cikket](../articles/virtual-machines/windows/premium-storage.md).

Az Azure támogatja a rögzített lemezes VHD formátumot. A rögzített formátum a logikai lemezt lineárisan helyezi el a fájlon belül, így az X lemezeltolás az X blobeltolásnál van tárolva. A blob végén egy kis lábléc írja le a VHD tulajdonságait. Gyakran előfordul a rögzített formátumú hulladékok lemezterület-mert legtöbb lemezen nagy méretű kihasználatlan tartományok őket. Az Azure azonban egy ritka formátumban tárolja a .vhd fájlokat, így a rögzített és a dinamikus lemezek előnyei egyaránt kihasználhatók. További információkért lásd: [– első lépések a virtuális merevlemezek](https://technet.microsoft.com/library/dd979539.aspx).

Az Azure-ban, amely forrásként lemezek vagy rendszerképek létrehozásához használni kívánt VHD-fájlokat írásvédettek, kivéve a .vhd fájlokat feltölteni, vagy a felhasználó (amely lehet olvasási és írási vagy a csak olvasható) az Azure storage-be másolni. Lemez vagy rendszerkép létrehozásakor az Azure másolatot készít a forrás .vhd kiterjesztésű fájlt. Ezek a másolatok csak olvashatók vagy olvashatók és írhatók is lehetnek a VHD használatától függően.

Amikor egy virtuális gépet hoz létre egy rendszerképből, az Azure létrehoz egy lemezt a virtuális gép számára, amely a forrás .vhd fájl másolata. A véletlen törlés elleni védelem érdekében az Azure egy bérlést helyez minden olyan forrás .vhd fájlra, amelyet egy rendszerkép, egy operációsrendszer-lemez vagy egy adatlemez létrehozásához használtak.

Mielőtt a forrás .vhd fájlt törölhetné, el kell távolítania a bérlést, ehhez pedig törölnie kell a lemezt vagy a rendszerképet. Ha törölni szeretne egy virtuális gép által operációsrendszer-lemezként használt .vhd fájlt, egyszerre is törölheti a virtuális gépet, az operációsrendszer-lemezt és a forrás .vhd fájlt a virtuális gép és az összes hozzá tartozó lemez törlésével. Egy adatlemez forrásaként használt .vhd fájl törléséhez azonban több lépést is el kell végezni, egy megadott sorrendben. Először válassza le a lemezt a virtuális gépről, majd törölje a lemezt, és végül törölje a .vhd fájlt.

> [!WARNING]
> Ha töröl egy forrás .vhd fájlt a tárolóból, vagy törli a saját tárfiókját, a Microsoft nem tudja visszaállítani az adatait.

## <a name="types-of-disks"></a>Lemeztípusok

Az Azure Disks 99,999%-os elérhetőséggel büszkélkedhet. Az Azure Disks következetesen szállított nagyvállalati szintű tartósság érdekében az iparágvezető nulla % Annualized hibaaránya.

Nincsenek három teljesítményszint, amelyek közül választhat a lemezek prémium szintű SSD-lemezeket (előzetes verzió) Standard SSD és HDD Standard Storage létrehozásakor. Ezenkívül nem felügyelt és felügyelt két típusa van.

### <a name="standard-hdd-disks"></a>A standard szintű HDD-lemezek

A standard szintű HDD-lemezek meghajtókra készül, és költséghatékony tárolás biztosításához. HDD standard storage replikálható helyileg egy adatközpontban, vagy lehet georedundáns az elsődleges és másodlagos adatközpontok. Storage-replikációval kapcsolatos további információkért lásd: [Azure Storage replikáció](../articles/storage/common/storage-redundancy.md). 

Standard HDD-lemezekkel kapcsolatos további információkért lásd: [Standard Storage és a lemezek](../articles/virtual-machines/windows/standard-storage.md).

### <a name="standard-ssd-disks"></a>Standard SSD-lemez

Standard SSD-lemezeket úgy tervezték, hogy a cím szabványos HDD lemezként számítási feladatok azonos típusú, de egységesebb teljesítményéről és megbízhatóságáról, mint a HDD nyújtani. Standard SSD-lemez összevonása elemeinek prémium szintű SSD és Standard HDD lemezek az űrlap egy költséghatékony megoldás a legmegfelelőbb az olyan alkalmazások, mint a webkiszolgálók, amelyeket nem kell a lemez magas iops-érték. Ha elérhetők, a Standard SSD-lemez a legtöbb számítási feladatokhoz ajánlott üzembe helyezési lehetőséget. Standard SSD-lemezeket, a Managed Disks az összes régióban elérhető, de jelenleg csak a helyileg redundáns tárolás (LRS) rugalmassági típussal.

### <a name="premium-ssd-disks"></a>Prémium szintű SSD-lemez

Prémium szintű SSD-lemezeket élvezik SSD-k, és lemeztámogatást biztosít nagy teljesítményű, kis késleltetésű I/O-igényes számítási feladatokat futtató virtuális gépek számára. Általában prémium szintű SSD-lemez is használható,-mérettel, amelyek tartalmazzák az adatsorozat neve a "s". Például a Dv3-sorozat van, és a Dsv3-sorozat, a Dsv3-sorozat a prémium szintű SSD-lemez is használható.  További információk: [Premium Storage](../articles/virtual-machines/windows/premium-storage.md).

### <a name="unmanaged-disks"></a>Nem felügyelt lemezek

A nem felügyelt lemez a virtuális gépek által használt hagyományos lemeztípus. Az ilyen lemezeket hozzon létre a saját tárfiókját, és adja meg a tárfiók a lemez létrehozásakor. Győződjön meg arról, hogy ne helyezzen túl sok lemez ugyanazt a tárfiókot, a, mert azzal túllépheti a [teljesítménycélokat](../articles/storage/common/storage-scalability-targets.md) a tárfiók (20 000 IOPS, a példában), a virtuális gépeket szabályozás alatt áll. Nem felügyelt lemezek esetén Önnek kell felmérnie, hogyan maximalizálhatja egy vagy több tárfiók kihasználtságát, hogy a virtuális gépeiből a lehető legjobb teljesítményt hozza ki.

### <a name="managed-disks"></a>Felügyelt lemezek

A felügyelt lemezek a háttérben végzik a tárfiók létrehozását és kezelését, hogy Önnek ne kelljen foglalkoznia a tárfiók skálázási korlátaival. Egyszerűen csak adja meg a lemez méretét és teljesítményszintjét (Standard vagy prémium), és az Azure létrehozza és felügyeli a lemezt Ön helyett. Nem kell a használt tárterület miatt aggódnia, amikor lemezeket ad hozzá, vagy fel-/leskálázza a virtuális gépet.

Az egyéni rendszerképeit Azure-régiónként egyetlen tárfiókban is felügyelheti, és a használatukkal több száz virtuális gépet hozhat létre egy adott előfizetésben. A felügyelt lemezekkel kapcsolatos további információért tekintse meg [a felügyelt lemezek áttekintésével kapcsolatos cikket](../articles/virtual-machines/windows/managed-disks-overview.md).

Javasoljuk, hogy az új virtuális gépeihez használja az Azure Managed Disks szolgáltatást, és a korábbi, nem felügyelt lemezeit is alakítsa felügyeltté, hogy kihasználhassa a Managed Disks által kínált számos szolgáltatást.

### <a name="disk-comparison"></a>Lemezek összehasonlítása

Az alábbi táblázat összehasonlítja a standard szintű HDD, Standard SSD és a nem felügyelt és felügyelt lemezek prémium szintű SSD segít eldönteni, hogy melyiket érdemes használni. Csillaggal szintben méretek jelenleg előzetes verzióban érhető el.

|    | Prémium szintű Azure-lemez |Az Azure Standard SSD-lemez | Az Azure standard szintű HDD-lemez
|--- | ------------------ | ------------------------------- | -----------------------
| Lemez típusa | SSD-k | SSD-k | HDD-k  
| Áttekintés  | SSD-alapú, nagy teljesítményű, kis késleltetésű lemeztámogatás a nagy adatátviteli teljesítményt igénylő számítási feladatokat vagy az üzletmenet szempontjából kritikus fontosságú éles környezeteket futtató virtuális gépek számára |Egységes teljesítményéről és megbízhatóságáról, mint a HDD. Alacsony iops-értékű munkaterhelésekhez optimalizálva| HDD-alapú költséghatékony lemezt gyakori eléréshez
| Forgatókönyv  | Éles, teljesítményérzékeny számítási feladatok |A webkiszolgálók, kisebb terhelésű vállalati alkalmazások és a fejlesztés + tesztelés| Biztonsági mentés, a nem kritikus, ritka hozzáférésű eszközéhez
| Lemezméret | P4: 32 GiB (csak felügyelt lemezek esetén)<br>P6: 64 GiB (csak felügyelt lemezek esetén)<br>Alatt P10: 128 GB<br>P15: 256 GiB (csak felügyelt lemezek esetén)<br>Között P20: 512 GB<br>Között P30: 1024 GB<br>Között P40: 2048 GB<br>P50: 4095 GiB<br>A P60: 8192 GiB * (8 TiB)<br>P70: 16 384 GB * (16 TiB)<br>P80: – 32 767 GiB * (32 TiB) |Csak felügyelt lemezek:<br>E10: 128 GIB-ra<br>E15: 256 GiB<br>E20: 512 GIB-ra<br>E30: 1024 GiB<br>E40: 2048 GiB<br>E50: 4095 GiB<br>E60: futtathat 8192 GiB * (8 TiB)<br>E70: 16 384 GB * (16 TiB)<br> E80: – 32 767 GiB * (32 TiB) | Nem felügyelt lemezek: 1 GB – 4 Tib-ra (4095 GiB) <br><br>Managed Disks:<br> S4: 32 GIB-ra <br>S6: 64 GiB <br>S10: 128 GIB-ra <br>S15: 256 GiB <br>S20: 512 GIB-ra <br>S30: 1024 GiB <br>S40: 2048 GiB<br>S50: 4095 GiB<br>S60: futtathat 8192 GiB * (8 TiB)<br>S70: 16 384 GB * (16 TiB)<br>S80: 32,384 GiB * (32 TiB)
| Lemezenkénti maximális átviteli sebesség | P4: 25 MiB/s<br> P6: 50 MiB/s<br> P10: 100 MiB/s<br> P15: 200 MiB/s<br> P20: 150 MiB/s<br> P30: 200 MiB/s<br> P40-P50: 250 MiB/s<br> A P60: 480 MiB/s<br> P70-P80: 750 MiB/s | E10-E50: akár 60 MiB/s<br> E60: akár 300 MiB/s *<br> E70-E80: 500 MiB/s *| S4 – S50: Upt o 60 MiB/s<br> S60: akár 300 MiB/s *<br> S70-S80: akár 500 MiB/s *
| Lemezenkénti maximális IOPS-érték | P4: 120 IOPS<br> P6: 240 IOPS<br> P10: 500 IOPS<br> P15: 1100-AS IOPS-ÉRTÉK<br> P20: 2300 IOPS<br> P30: 5000 IOPS<br> P40-P50: 7500 IOPS<br> A P60: 12,500 IOPS *<br> P70: 15 000 IOPS *<br> P80: 20 000 IOPS * | E10-E50: legfeljebb 500 IOPS<br> E60: 1300 IOPS legfeljebb *<br> E70-E80: legfeljebb 2000 IOPS * | S4 – S50: legfeljebb 500 IOPS<br> S60: 1300 IOPS legfeljebb *<br> S70-S80: legfeljebb 2000 IOPS *