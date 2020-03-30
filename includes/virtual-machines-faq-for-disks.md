---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/13/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 53bbee6dd75e045c2a7e95c88a0138c9859d12db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80374034"
---
Ez a cikk választ ad az Azure felügyelt lemezekkel és az Azure Premium SSD-lemezekkel kapcsolatos gyakori kérdésekre.

## <a name="managed-disks"></a>Felügyelt lemezek

**Mi az Azure felügyelt lemezek?**

A Felügyelt lemezek olyan szolgáltatás, amely leegyszerűsíti az Azure IaaS virtuális gépek lemezkezelését a tárfiók-kezelés kezelésével. További információt a [Felügyelt lemezek – áttekintés című témakörben talál.](../articles/virtual-machines/windows/managed-disks-overview.md)

**Ha egy 80 GB-os meglévő virtuális merevlemezről hozok létre egy szabványos felügyelt lemezt, az mennyibe fog nekem kerülni?**

A 80 GB-os virtuális merevlemezről létrehozott szabványos felügyelt lemezt a rendszer a következő elérhető szabványos lemezméretként kezeli, amely egy S10-lemez. Az S10 lemezdíjszabása szerint számítjuk fel a díjat. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/storage) olvasható.

**Vannak tranzakciós költségek a szabványos felügyelt lemezekhez?**

Igen. Minden tranzakcióért díjat számítunk fel. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/storage) olvasható.

**Szabványos felügyelt lemez esetén a lemezen lévő adatok tényleges méretéért vagy a lemez kiosztott kapacitásáért kell fizetnem?**

A lemez kiosztott kapacitása alapján számítunk fel díjat. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/storage) olvasható.

**Miben különbözik a prémium szintű felügyelt lemezek díjszabása a nem felügyelt lemezektől?**

A prémium szintű felügyelt lemezek díjszabása megegyezik a nem felügyelt prémium szintű lemezek díjszabása.

**Módosíthatom a felügyelt lemezek tárfiókának típusát (Standard vagy Premium)?**

Igen. Módosíthatja a felügyelt lemezek tárfiók típusát az Azure Portalon, a PowerShellen vagy az Azure CLI-n keresztül.

**Használhatok VHD-fájlt egy Azure-tárfiókban egy felügyelt lemez létrehozásához egy másik előfizetéssel?**

Igen.

**Használhatok VHD-fájlt egy Azure-tárfiókban felügyelt lemez létrehozásához egy másik régióban?**

Nem.

**Vannak-e méretezési korlátozások a felügyelt lemezeket használó ügyfelek számára?**

A felügyelt lemezek kiküszöbölik a tárfiókokhoz társított korlátokat. A maximális korlát azonban régiónként és lemeztípusonként 50 000 felügyelt lemez egy előfizetéshez.

**A rendelkezésre állási csoportban lévő virtuális gépek felügyelt és nem felügyelt lemezek kombinációjából állhatnak?**

Nem. A rendelkezésre állási csoportban lévő virtuális gépeknek az összes felügyelt lemezt vagy az összes nem felügyelt lemezt kell használniuk. Az elérhetőségi csoport létrehozásakor kiválaszthatja, hogy milyen típusú lemezeket szeretne használni.

**A felügyelt lemezek az alapértelmezett beállítás az Azure Portalon?**

Igen.

**Létrehozhatok üres felügyelt lemezt?**

Igen. Létrehozhat üres lemezt. Felügyelt lemez a virtuális géptől függetlenül is létrehozható, például anélkül, hogy egy virtuális géphez csatolná.

**Mi a támogatott tartaléktartomány-számláló a felügyelt lemezeket használó rendelkezésre állási csoporthoz?**

Attól függően, hogy a régió, ahol a rendelkezésre állási csoport, amely a felügyelt lemezek található, a támogatott tartalék tartomány száma 2 vagy 3.

**Hogyan van beállítva a szabványos diagnosztikai tárfiók?**

Beállít egy privát tárfiókot a virtuális gép diagnosztika.

**Milyen típusú szerepköralapú hozzáférés-vezérlési támogatás érhető el a felügyelt lemezekhez?**

A Felügyelt lemezek három fő alapértelmezett szerepkört támogat:

* Tulajdonos: Mindent kezelhet, beleértve a hozzáférést is
* Közreműködő: A hozzáférés kivételével mindent kezelhet
* Olvasó: Mindent megtekinthet, de nem tud módosításokat végrehajtani

**Van mód arra, hogy felügyelt lemezt másolhassak vagy exportálhassak egy magántárfiókba?**

Létrehozhat egy csak olvasható megosztott hozzáférés-aláírási (SAS) URI-t a felügyelt lemezhez, és használhatja a tartalom másolásához egy magántárfiókba vagy a helyszíni tárolóba. Használhatja a SAS URI-t az Azure Portalon, az Azure PowerShellen, az Azure CLI-n vagy az [AzCopy-on keresztül](../articles/storage/common/storage-use-azcopy.md)

**Létrehozhatok másolatot a felügyelt lemezről?**

Az ügyfelek pillanatképet készíthetnek a felügyelt lemezekről, majd a pillanatkép segítségével létrehozhatnak egy másik felügyelt lemezt.

**Továbbra is támogatottak a nem felügyelt lemezek?**

Igen, mind a nem felügyelt, mind a felügyelt lemezek támogatottak. Azt javasoljuk, hogy felügyelt lemezeket használjon az új számítási feladatokhoz, és telepítse át az aktuális számítási feladatokat felügyelt lemezekre.

**Lehet együtt megtalálni a nem felügyelt és felügyelt lemezekugyanazon a virtuális gépen?**

Nem.

**Ha létrehozok egy 128 GB-os lemezt, majd növelem a méretet 130 gibibytes (GiB), fogok fizetni a következő lemez mérete (256 GiB)?**

Igen.

**Létrehozhatok helyileg redundáns tárolást, georedundáns tárolást és zónaredundáns tárolási felügyelt lemezeket?**

Az Azure felügyelt lemezek jelenleg csak helyileg redundáns tárolási felügyelt lemezeket támogat.

**Csökkenthető vagy kicsidíthető a kezelt lemezek?**

Nem. Ez a szolgáltatás jelenleg nem támogatott.

**Megszakíthatom a lemezbérletet?**

Nem. Ez jelenleg nem támogatott, mivel a lemez használat közbeni véletlen törlésének megakadályozására bérlet van jelen.

**Módosíthatom a számítógépnév tulajdonságot, ha egy speciális (nem a Rendszer-előkészítés i eszközzel vagy általánosított) operációs rendszer lemezével létesítenek virtuális gépet?**

Nem. A számítógépnév tulajdonság nem frissíthető. Az új virtuális gép örökli azt a szülő virtuális gép, amely az operációs rendszer lemezének létrehozásához használt. 

**Hol találhatok minta Azure Resource Manager-sablonokat a felügyelt lemezekkel rendelkező virtuális gépek létrehozásához?**
* [Sablonok listája a felügyelt lemezek használatával](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

**Amikor lemezt hoz létre egy blobból, van-e folyamatosan létező kapcsolat az adott forrásblobtal?**

Nem, amikor az új lemez jön létre, hogy egy teljes önálló példányát, hogy a blob abban az időben, és nincs kapcsolat a kettő között. Ha szeretné, miután létrehozta a lemezt, a forrásblob törölhető anélkül, hogy bármilyen módon befolyásolná az újonnan létrehozott lemezt.

**Átnevezhetek egy felügyelt vagy nem felügyelt lemezt a létrehozása után?**

Felügyelt lemezek esetén nem nevezhető át. Azonban átnevezhet egy nem felügyelt lemezt, amíg az jelenleg nincs virtuális merevlemezhez vagy virtuális géphez csatolva.

**Használhatom a GPT-particionálást egy Azure Disken?**

Az 1. Az operációs rendszer lemezeinek az MBR partícióstílust kell használniuk.

[A 2.](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2)

**Milyen lemeztípusok támogatják a pillanatképeket?**

Prémium SSD, standard SSD és szabványos HDD támogatási pillanatképek. E három lemeztípus esetén a pillanatképek minden lemezméretben támogatottak (beleértve a legfeljebb 32 TiB méretű lemezeket is). Az ultralemezek nem támogatják a pillanatképeket.

**Mik azok az Azure-lemezfoglalások?**
A lemezfoglalás egy éves lemezes tárolást biztosít előre, csökkentve ezzel a teljes költséget. Az Azure-lemezfoglalásokkal kapcsolatos részletekért tekintse meg a témáról szóló cikkünket: [Ismerje meg, hogyan alkalmazza a foglalási kedvezményt az Azure Diskre.](../articles/cost-management-billing/reservations/understand-disk-reservations.md)

**Milyen lehetőségeket kínál az Azure lemezfoglalás?**    
Az Azure lemezfoglalás lehetővé teszi prémium szintű SSD-k vásárlását a megadott SK-okban P30 -tól (1 TiB) P80-ig (32 TiB) egy éves időtartamra. Nincs korlátozás a lemezfoglalás megvásárlásához szükséges minimális lemezmennyiségre vonatkozóan. Ezenkívül dönthet úgy is, hogy egyetlen, előzetes vagy havi fizetéssel fizet. A prémium szintű SSD-felügyelt lemezekhez nincs további tranzakciós költség.    

A foglalások lemezek, nem kapacitás formájában történnek. Más szóval, ha egy P80 (32 TiB) lemezt foglal le, egyetlen P80 lemezt kap, akkor nem oszthatja fel az adott foglalást két kisebb P70 (16 TiB) lemezre. Természetesen annyi vagy kevés lemezt foglalhat le, amennyit csak szeretne, beleértve két különálló P70 (16 TiB) lemezt.

**Hogyan történik az Azure lemezfoglalás alkalmazása?**    
A lemezfoglalás a fenntartott virtuálisgép-példányokhoz hasonló modellt követ. A különbség az, hogy a lemezfoglalás nem alkalmazható a különböző sk-ekre, míg a virtuálisgép-példány is. A virtuálisgép-példányokkal kapcsolatos további információkért tekintse meg a Költségek csökkentése az [Azure által fenntartott virtuálisgép-példányokkal](../articles/virtual-machines/linux/prepay-reserved-vm-instances.md) című témakört.     

**Használhatom az Azure-lemezek foglalásán keresztül vásárolt adattárhelyet több régióban?**    
Az Azure-lemezek foglalása egy adott régióhoz és termékváltozathoz (például a P30 USA-keleti régióban 2) történik, ezért nem használható ezeken a konstrukciókon kívül. Bármikor vásárolhat egy további Azure-lemezfoglalást a lemeztárolási igényekhez más régiókban vagy suk-okban.    

**Mi történik, ha lejár az Azure-lemezek foglalása?**    
E-mailes értesítéseket kap 30 nappal a lejárat előtt, majd újra a lejárati dátumon. A foglalás lejárta után a telepített lemezek továbbra is futni fognak, és a legújabb [felosztó-kirovó díjakat számlálják.](https://azure.microsoft.com/pricing/details/managed-disks/)

### <a name="azure-shared-disks"></a>Azure megosztott lemezei

**A megosztott lemezek szolgáltatás nem felügyelt lemezek vagy lapblobok esetén támogatott?**

Nem, csak prémium szintű SSD-felügyelt lemezek esetén támogatott.

**Milyen régiók támogatják a megosztott lemezeket?**

Jelenleg csak az USA nyugati közép-nyugati régiója.

**A megosztott lemezek operációs rendszerként használhatók?**

Nem, a megosztott lemezek csak adatlemezek esetén támogatottak.

**Milyen lemezméretek támogatják a megosztott lemezeket?**

Csak a P15-ös vagy nagyobb prémium szintű SSD-k támogatják a megosztott lemezeket.

**Ha meglévő prémium szintű SSD-vel rendelkezem, engedélyezhetem rajta a megosztott lemezeket?**

A 2019-07-01-es vagy újabb API-verzióval létrehozott összes felügyelt lemez képes megosztott lemezeket engedélyezni. Ehhez le kell választania a lemezt az összes virtuális gépről, amelyhez csatlakoztatva van. Ezután szerkesztsd a `maxShares` tulajdonságot a lemezen.

**Ha már nem szeretnék lemezt használni megosztott módban, hogyan tudom letiltani?**

A lemez leválasztása az összes olyan virtuális gépről, amelyhez csatlakoztatva van. Ezután a lemezen lévő maxShare tulajdonságot 1-re szerkeszti.

**Át tudja méretezni a megosztott lemezt?**

Igen.

**Engedélyezhető az írásgyorsító olyan lemezen, amelyen a megosztott lemezek is engedélyezve vannak?**

Nem.

**Engedélyezhetem az állomásgyorsítótárazást olyan lemezhez, amelyen engedélyezve van a megosztott lemez?**

Az egyetlen támogatott állomás-gyorsítótárazási beállítás a "Nincs".

## <a name="ultra-disks"></a>Ultralemezek

**Mire állítsam be az ultralemez átviteli áteresztődését?**
Ha nem biztos benne, hogy mit állítson be a lemez átviteli teljesítmény, azt javasoljuk, hogy először egy I/O-mérete 16 KiB, és módosítsa a teljesítményt onnan, ahogy az alkalmazás figyelése. A képlet a következő: Átviteli sebesség MBps = # az IOPS * 16 / 1000.

**Én beállítva a lemezt, hogy 40000 IOPS, de én csak látni 12800 IOPS, miért nem látom a teljesítményét a lemez?**
A lemez szabályozása mellett van egy I/o-szabályozás, amely a virtuális gép szintjén kerül kivetésre. Győződjön meg arról, hogy a virtuális gép mérete a lemezeken konfigurált támogatott. A virtuális gép által előírt I/O-korlátozásokkal kapcsolatos részletekért olvassa el [a Windows virtuális gépek mérete az Azure-ban.](../articles/virtual-machines/windows/sizes.md)

**Használhatok gyorsítótárazási szinteket ultralemezzel?**
Nem, az ultralemezek nem támogatják a más lemeztípusokáltal támogatott különböző gyorsítótárazási módszereket. Állítsa a lemez gyorsítótárazását Nincs beállításra.

**Csatlakoztathatok egy ultralemezt a meglévő virtuális gépemhez?**
Lehet, hogy a virtuális gép kell egy régióban, és a rendelkezésre állási zóna pár, amely támogatja az Ultra-lemezek. A részleteket az [ultralemezek első lépésekben](../articles/virtual-machines/windows/disks-enable-ultra-ssd.md) találja.

**Használhatok egy ultralemezt a virtuális gép operációs rendszerlemezeként?**
Nem, az ultralemezek csak adatlemezként támogatottak, és csak 4K natív lemezként támogatottak.

**Konvertálhatok meglévő lemezt ultralemezzé?**
Nem, de az adatokat áttelepítheti egy meglévő lemezről egy ultralemezre. Meglévő lemez ultralemezre való áttelepítéséhez csatolja mindkét lemezt ugyanahhoz a virtuális géphez, és másolja a lemez adatait az egyik lemezről a másikra, vagy használja ki a harmadik féltől származó megoldást az adatok áttelepítéséhez.

**Létrehozhatok pillanatképeket az ultralemezekhez?**
Nem, a pillanatképek még nem érhetők el.

**Elérhető az Azure Backup az ultralemezek hez?**
Nem, az Azure Backup támogatása még nem érhető el.

**Csatlakoztathatok egy ultralemezt egy rendelkezésre állási csoportban futó virtuális géphez?**
Nem, ez még nem támogatott.

**Engedélyezhetem az Azure Site Recovery szolgáltatást a virtuális gépek számára ultralemezek használatával?**
Nem, az Azure Site Recovery még nem támogatott az ultralemezek.

## <a name="uploading-to-a-managed-disk"></a>Feltöltés felügyelt lemezre

**Feltölthetek adatokat egy meglévő felügyelt lemezre?**

Nem, a feltöltés csak a **ReadyToUpload** állapotú új üres lemez létrehozása során használható.

**Hogyan tölthetek fel felügyelt lemezre?**

Hozzon létre egy felügyelt lemezt a [createOption](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#diskcreateoption) tulajdonsága [creationData](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#creationdata) set to "Upload", majd feltöltheti az adatokat.

**Csatlakoztathatok egy lemezt egy virtuális géphez, amíg az feltöltési állapotban van?**

Nem.

**Készíthetek pillanatképet egy manged lemezről feltöltési állapotban?**

Nem.

## <a name="standard-ssd-disks"></a>Szabványos SSD-lemezek

**Mik azok az Azure Standard SSD-lemezek?**
A szabványos SSD-lemezek szabványos lemezek, amelyeket ssd-s adathordozó konzisztens teljesítményre optimalizál, és költséghatékony tárolóként optimalizálnak az alacsonyabb IOPS-szinteken konzisztens teljesítményt igénylő munkaterhelések számára.

<a id="standard-ssds-azure-regions"></a>**Melyek azok a régiók, amelyek jelenleg támogatottak a standard SSD-lemezek számára?**
Az Összes Azure-régió támogatja a standard SSD-lemezeket.

**Elérhető az Azure Backup standard SSD-k használata esetén?**
Igen, az Azure Backup már elérhető.

**Hogyan hozhatok létre szabványos SSD-lemezeket?**
Standard SSD-lemezeket hozhat létre az Azure Resource Manager-sablonokkal, Az SDK-val, a PowerShell-lel vagy a CLI-vel. Az alábbiakban az Erőforrás-kezelő sablonban a szabványos SSD-lemezek létrehozásához szükséges paramétereket talál:

* a Microsoft.Compute *apiVersion* verzióját `2018-04-01` (vagy újabb) kell beállítani.
* *ManagedDisk.storageAccountType* megadása`StandardSSD_LRS`

A következő példa a *properties.storageProfile.osDisk* szakaszt mutatja be egy szabványos SSD-lemezeket használó virtuális géphez:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

A szabványos SSD-lemez sablonnal történő létrehozásáról a [Virtuális gép létrehozása szabványos SSD-adatlemezekkel rendelkező Windows lemezről](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/)című témakörben olvashat.

**Konvertálhatom a meglévő lemezeimet szabványos SSD-vé?**
Igen. Tekintse meg az [Azure felügyelt lemezek tárolási konvertálása a standard prémium szintű, és fordítva](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) a felügyelt lemezek konvertálásának általános irányelveit. A következő értékkel pedig frissítse a lemeztípusát standard SSD-re.
-AccountType StandardSSD_LRS

**Mi az előnye a standard SSD lemezek használata hdd helyett?**
A szabványos SSD-lemezek a HDD-lemezekhez képest jobb késleltetést, konzisztenciát, rendelkezésre állást és megbízhatóságot biztosítanak. Az alkalmazás-munkaterhelések sokkal zökkenőmentesebben futnak a standard SSD-n emiatt. Megjegyzés: A prémium szintű SSD-lemezek az ajánlott megoldás a legtöbb I/O-igényes éles számítási feladatokhoz.

**Használhatom a szabványos SSD-ket nem felügyelt lemezként?**
Nem, a standard SSD-lemezek csak felügyelt lemezként érhetők el.

**A standard SSD-lemezek támogatják az "egypéldányos virtuálisgép-SLA"-t?**
Nem, a standard SSD-k nem rendelkeznek egypéldányos virtuálisgép-SLA-val. Prémium szintű SSD-lemezek használata egypéldányos virtuálisgép-SLA.Use Premium SSD disks for single instance VM SLA.

## <a name="migrate-to-managed-disks"></a>Migrálás felügyelt lemezekre

**Van-e hatása az áttelepítés a felügyelt lemezek teljesítményét?**

Az áttelepítés magában foglalja a lemez áthelyezését az egyik tárolóhelyről a másikra. Ez az adatok háttérmásolatával van elhangolva, amely több órát is igénybe vehet, általában kevesebb, mint 24 óra a lemezekben lévő adatok mennyiségétől függően. Ez idő alatt az alkalmazás a szokásosnál nagyobb olvasási késést tapasztalhat, mivel egyes olvasások átirányíthatók az eredeti helyre, és hosszabb időt vehet igénybe. Ebben az időszakban nincs hatással az írási késésre.  

**Milyen módosításokra van szükség egy már meglévő Azure Backup szolgáltatás konfigurációjában a felügyelt lemezekre való áttelepítés előtt/után?**

Nincs szükség módosításokra.

**Az Azure Backup szolgáltatáson keresztül létrehozott virtuálisgép-biztonsági mentések az áttelepítés folytatása előtt létrejönnek?**

Igen, a biztonsági mentések zökkenőmentesen működnek.

**Milyen módosításokra van szükség egy már meglévő Azure Disks Encryption konfiguráció ban a felügyelt lemezekre való áttelepítés előtt/után?**

Nincs szükség módosításokra.

**Támogatott egy meglévő virtuálisgép-méretezés automatikus áttelepítése a nem felügyelt lemezekről a felügyelt lemezekre?**

Nem. A felügyelt lemezekkel új méretezési csoportot hozhat létre a régi lemezkészletből származó, nem felügyelt lemezekkel rendelkező lemezkép segítségével.

**Létrehozhatok felügyelt lemezt a felügyelt lemezekre való áttelepítés előtt készített lapblob-pillanatképből?**

Nem. A lapblob pillanatképét oldalblobként exportálhatja, majd létrehozhat egy felügyelt lemezt az exportált lapblobból.

**Átvehetem a feladatát az Azure Site Recovery által védett helyszíni gépeimet egy felügyelt lemezekkel rendelkező virtuális gépnek?**

Igen, választhat, hogy feladatátvételt egy felügyelt lemezekkel rendelkező virtuális gép.

**Van-e hatása a migrálás az Azure virtuális gépek által védett Azure Site Recovery az Azure-on keresztül az Azure replikáció?**

Nem. Az Azure Site Recovery Azure az Azure-hoz védelem a felügyelt lemezekkel rendelkező virtuális gépek hez érhető el.

**Áttelepíthetem a nem felügyelt lemezekkel rendelkező virtuális gépeket, amelyek olyan tárfiókokban találhatók, amelyek korábban felügyelt lemezekre vannak titkosítva, vagy amelyeket korábban felügyelt lemezekre titkosítottak?**

Igen

## <a name="managed-disks-and-storage-service-encryption"></a>Felügyelt lemezek és tárolószolgáltatás titkosítása

**Felügyelt lemez létrehozásakor alapértelmezés szerint engedélyezve van az Azure Storage Service Encryption szolgáltatás?**

Igen.

**Alapértelmezés szerint titkosítva van a rendszerindító kötet egy felügyelt lemezen?**

Igen. Alapértelmezés szerint az összes felügyelt lemez titkosítva van, beleértve az operációsrendszer-lemezt is.

**Ki kezeli a titkosítási kulcsokat?**

A Microsoft kezeli a titkosítási kulcsokat.

**Letiltható a storage service encryption a felügyelt lemezeken?**

Nem.

**A storage-szolgáltatás titkosítása csak bizonyos régiókban érhető el?**

Nem. Minden olyan régióban elérhető, ahol a felügyelt lemezek érhetők el. A felügyelt lemezek minden nyilvános régióban és Németországban elérhetők. Kínában is elérhető, azonban csak a Microsoft által felügyelt kulcsok, nem az ügyfél által kezelt kulcsok esetében.

**Hogyan tudom meg, hogy a felügyelt lemez titkosítva van-e?**

Megtudhatja, hogy mikor hozták létre a felügyelt lemezt az Azure Portalon, az Azure CLI-ben és a PowerShellben. Ha az idő 2017.

**Hogyan titkosíthatom a 2017.**

2017. június 10-én a meglévő felügyelt lemezekre írt új adatok automatikusan titkosítva lesznek. Azt is tervezi, hogy titkosítja a meglévő adatokat, és a titkosítás fog történni aszinkron módon a háttérben. Ha most kell titkosítania a meglévő adatokat, hozzon létre egy másolatot a lemezről. Az új lemezek titkosítva lesznek.

* [Felügyelt lemezek másolása az Azure CLI használatával](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Felügyelt lemezek másolása a PowerShell használatával](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**Titkosítottak a felügyelt pillanatképek és a képek?**

Igen. 2017. június 9-e után létrehozott összes felügyelt pillanatkép és kép automatikusan titkosítva lesz. 

**Konvertálhatok olyan virtuális gépeket, amelyek nem felügyelt lemezekkel rendelkeznek, amelyek olyan tárfiókokban találhatók, amelyek korábban felügyelt lemezekre vannak titkosítva, vagy amelyeket korábban felügyelt lemezekre titkosítottak?**

Igen

**Az exportált virtuális merevlemez felügyelt lemezről vagy pillanatképből is titkosítva lesz?**

Nem. De ha egy virtuális merevlemezt exportál egy titkosított felügyelt lemezről vagy pillanatképből egy titkosított tárfiókba, akkor az titkosítva van. 

## <a name="premium-disks-managed-and-unmanaged"></a>Prémium szintű lemezek: Felügyelt és nem felügyelt

**Ha egy virtuális gép olyan méretsorozatot használ, amely támogatja a prémium szintű SSD-lemezeket, például a DSv2-t, csatlakoztathatok prémium és szabványos adatlemezeket is?** 

Igen.

**Csatlakoztathatok prémium és szabványos adatlemezeket is olyan méretsorozathoz, amely nem támogatja a prémium szintű SSD-lemezeket, például a D, Dv2, G vagy F sorozatot?**

Nem. Csak szabványos adatlemezeket csatolhat olyan virtuális gépekhez, amelyek nem használnak prémium szintű SSD-lemezeket támogató méretsorozatot.

**Ha egy 80 GB-os meglévő virtuális merevlemezről hozok létre prémium szintű adatlemezt, mennyibe fog ez kerülni?**

A 80 GB-os Virtuális merevlemezről létrehozott prémium szintű adatlemezeket a rendszer a következő elérhető prémium szintű lemezméretként kezeli, amely egy P10-lemez. A P10 lemezdíjszabása szerint számítjuk fel a díjat.

**Vannak tranzakciós költségek a prémium SSD-lemezek használatához?**

Minden egyes lemezmérethez rögzített költség vonatkozik, amely az IOPS és az átviteli rendszer meghatározott korlátokkal rendelkezik. Az egyéb költségek a kimenő sávszélesség és a pillanatkép kapacitása, ha van ilyen. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/storage) olvasható.

**Milyen korlátok vonatkoznak az IOPS-ra és az átviteli műveletekre, amelyeket a lemezgyorsítótárból kaphatok?**

A ds-sorozatok gyorsítótárának és helyi SSD-jének összesített korlátai magonként 4000 IOPS és magonként 33 MiB. A GS sorozat magonként 5000 IOPS-t és magonként 50 MiB-t kínál.

**A helyi SSD támogatott felügyelt lemezes virtuális gép esetén?**

A helyi SSD egy felügyelt lemez virtuális gép ideiglenes tárolója. Az ideiglenes tárolás nem jár többletköltséggel. Azt javasoljuk, hogy ne használja ezt a helyi SSD-t az alkalmazásadatok tárolására, mert azok nem maradnak meg az Azure Blob storage-ban.

**Vannak-e következményei a TRIM prémium lemezeken való használatára?**

Nincs hátránya a TRIM használata az Azure-lemezeken prémium szintű vagy standard lemezeken.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Új lemezméretek: Felügyelt és nem felügyelt

**Milyen régiók támogatják a felszakítási képességet a megfelelő prémium szintű SSD-lemezmérethez?**

A bursting képesség jelenleg támogatott az Azure Central USA.

**Milyen régiókban támogatott a 4/8/16 GiB felügyelt lemez mérete (P1/P2/P3, E1/E2/E3) ?**

Ezeket az új lemezméreteket jelenleg az Azure Central US támogatja.

**A P1/P2/P3 lemezméretek nem felügyelt lemezek vagy lapblobok esetén támogatottak?**

Nem, csak prémium szintű SSD-felügyelt lemezeken támogatott. 

**Az E1/E2/E3 lemezméretek nem felügyelt lemezek vagy lapblobok esetén támogatottak?**

Nem, a szabványos SSD-felügyelt lemezek bármilyen méretű nem használható a nem felügyelt lemezek vagy lapblobok.

**Mi a legnagyobb felügyelt lemezméret támogatott operációs rendszer és adatlemezek?**

Az Azure által az operációs rendszer lemezén támogatott partíciótípus a fő rendszertöltő rekord (MBR). Az MBR formátum legfeljebb 2 TiB méretű. Az Azure által az operációs rendszer lemezeihez támogatott legnagyobb méret a 2 TiB. Az Azure legfeljebb 32 TiB-t támogat a felügyelt adatlemezekhez.

**Mi a legnagyobb nem felügyelt lemezméret, amelyet az operációs rendszer és az adatlemezek támogatnak?**

Az Azure által az operációs rendszer lemezén támogatott partíciótípus a fő rendszertöltő rekord (MBR). Az MBR formátum legfeljebb 2 TiB méretű. Az Azure által az operációs rendszer nem felügyelt lemezéhez a legnagyobb mérete a 2 TiB. Az Azure legfeljebb 4 TiB-t támogat a nem felügyelt lemezekhez.

**Mi a támogatott legnagyobb lapblob-méret?**

Az Azure által támogatott legnagyobb lapblob-méret 8 TiB (8191 GiB). A virtuális géphez adatként vagy operációs rendszerlemezként csatlakoztatott maximális lapblob-méret 4 TiB (4095 GiB).

**Az Azure-eszközök új verzióját kell használnom az 1 TiB-nél nagyobb lemezek létrehozásához, csatolásához, átméretezéséhez és feltöltéséhez?**

Nem kell frissítenie a meglévő Azure-eszközöket az 1 TiB-nél nagyobb lemezek létrehozásához, csatolásához vagy átméretezéséhez. A vhd-fájl feltöltéséhez közvetlenül az Azure-ból, mint egy lap blob vagy nem felügyelt lemez, az alább felsorolt legújabb eszközkészletek et kell használnia. Csak 8 TiB-es Virtuális merevlemez-feltöltést támogatunk.

|Azure-eszközök      | Támogatott verziók                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | 4.1.0-s verziószám: 2017 júniusi kiadás vagy újabb verzió|
|Azure CLI v1     | Verziószám 0.10.13: 2017 májusi kiadás vagy újabb verzió|
|Azure CLI v2     | 2.0.12-es verziószám: 2017.|
|AzCopy              | Version number 6.1.0: June 2017 release or later|

**A P4 és a P6 lemezméretek támogatottak a nem felügyelt lemezek vagy lapblobok esetében?**

P4 (32 GiB) és P6 (64 GiB) lemezméretek nem támogatottak, mint az alapértelmezett lemezrétegek nem felügyelt lemezek és lapblobok. Explicit módon be kell [állítania a Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) p4-re és P6-ra, hogy a lemez leképezve legyen ezekre a szintekre. Ha 32 GiB-nél kisebb lemezmérettel vagy tartalomhosszal rendelkező nem felügyelt lemezt vagy lapblobot telepít, vagy 32 GiB és 64 GiB között a Blob-szint beállítása nélkül, akkor továbbra is 500 IOPS és 100 MiB/s, valamint a leképezett tarifacsomag p10-en fog leszállni.

**Ha a meglévő prémium szintű felügyelt lemez kevesebb, mint 64 GiB jött létre, mielőtt a kis lemez engedélyezve lett (körülbelül június 15, 2017), hogyan kerül számlázásra?**

A 64 GiB-nál kisebb meglévő kis prémium lemezek számlázása továbbra is a P10-es tarifacsomag szerint történik.

**Hogyan válthatok a 64 GiB-nál kisebb prémium szintű lemezek lemezrétege közül a P10-ről a P4-re vagy a P6-ra?**

Pillanatképet készíthet a kis lemezekről, majd létrehozhat egy lemezt, amely automatikusan átvált a tarifacsomagp4-re vagy P6-ra a kiosztott méret alapján.

**Át méretezhető-e a meglévő felügyelt lemezek 4 tebibájtnál (TiB) kevesebbről újonnan bevezetett lemezméretre 32 TiB-ig?**

Igen.

**Melyek az Azure Backup és az Azure Site Recovery szolgáltatás által támogatott legnagyobb lemezméretek?**

Az Azure Backup által támogatott legnagyobb lemezméret a 32 TiB (4 TiB a titkosított lemezek esetében). Az Azure Site Recovery által támogatott legnagyobb lemezméret 8 TiB. A 32 TiB-ig nem támogatott nagyobb lemezek támogatása még nem érhető el az Azure Site Recovery szolgáltatásban.

**Melyek az ajánlott virtuális gép méretek nagyobb lemezméretek (>4 TiB) a standard SSD és standard HDD lemezek optimális lemez IOPS és sávszélesség?**

A standard SSD és standard HDD nagy lemezméretek (>4 TiB) 500 IOPS és 60 MiB/s-ot meghaladó lemezátviteli Javasoljuk, hogy a teljesítmény optimalizálása érdekében telepítsen egy új virtuális gépet az alábbi virtuális gépméretek egyikéről: B-sorozat, DSv2 sorozat, Dsv3 sorozat, ESv3 sorozatú, Fs-sorozat, Fsv2-sorozat, M-sorozat, GS-sorozat, NCv2 sorozatú, NCv3 sorozatú vagy Ls sorozatú virtuális gépek. Nagy lemezek csatlakoztatása a meglévő virtuális gépekhez vagy virtuális gépekhez, amelyek nem használják a fenti ajánlott méreteket, alacsonyabb teljesítményt tapasztalhatnak.

**Hogyan frissíthetem a lemezeket (>4 TiB), amelyek a nagyobb lemezméretek előnézete során lettek telepítve, hogy a nagyobb IOPS & sávszélességet kaphassa katakombáknál?**

Leállíthatja és elindíthatja a lemezhez csatlakoztatott virtuális gépét, vagy leválaszthatja és újra csatlakoztathatja a lemezt. A nagyobb lemezméretek teljesítménycéljait növelték mind a prémium szintű SSD-k, mind a normál SSD-k esetében a GA-nál.

**Milyen régiókban támogatott a 8 TiB, 16 TiB és 32 TiB felügyelt lemezmérete?**

A 8 TiB, 16 TiB és 32 TiB lemezsEK a globális Azure, a Microsoft Azure Government és az Azure China 21Vianet minden régiójában támogatottak.

**Támogatjuk-e az állomásgyorsítótárazás engedélyezését minden lemezméretben?**

4 TiB-nál kisebb méretű lemezen támogatjuk a ReadOnly és az Olvasás/írás állomásos gyorsítótárazást. A 4 TiB-nél nagyobb lemezméretek esetén nem támogatjuk a Nincs beállításbeállítási beállítást. Azt javasoljuk, hogy a kisebb lemezméretek gyorsítótárazását vegye figyelembe, ahol a virtuális gépgyorsítótárába gyorsítótárazott adatokkal jobb teljesítménynövekedést észlelhet.

## <a name="what-if-my-question-isnt-answered-here"></a>Mi van, ha a kérdésemre nem kap választ?

Ha kérdése nem szerepel itt, tudassa velünk, és mi segítünk megtalálni a választ. A cikk végén a megjegyzésekben feltehet egy kérdést. Ha kapcsolatba szeretne lépni az Azure Storage-csapattal és a közösség más tagjaival ebben a cikkben, használja az MSDN [Azure Storage fórumot.](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)

Funkciók igényléséhez küldje el kéréseit és ötleteit az [Azure Storage visszajelzési fórumába.](https://feedback.azure.com/forums/217298-storage)
