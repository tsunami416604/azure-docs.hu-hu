---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/30/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 03e10497b033fc3d97fde4cd524b358c05fdc943
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57457933"
---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Azure IaaS virtuális gépek és a felügyelt és nem felügyelt prémium szintű lemezek – gyakori kérdések

Ebben a cikkben megválaszolunk néhány gyakori kérdés az Azure Managed Disks és az Azure prémium szintű SSD-lemezeket.

## <a name="managed-disks"></a>Felügyelt lemezek

**Mi az Azure Managed Disks?**

A felügyelt lemezek funkciója, amely egyszerűbbé teszi az Azure IaaS virtuális gépek Lemezkezelés kezeli a tárfiók-felügyelet az Ön számára. További információkért lásd: a [Managed Disks – áttekintés](../articles/virtual-machines/windows/managed-disks-overview.md).

**Ha egy meglévő VHD-t, amely 80 GB a standard szintű felügyelt lemez készíteni, hogy mennyibe kerül velem?**

80 GB-os VHD-vel létrehozott standard szintű felügyelt lemez a következő elérhető standard lemez méretét, amely egy S10 lemez számít. Az S10 a lemezek díjszabása szerint kell fizetnie. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/storage) olvasható.

**Minden tranzakciós költségek, a standard szintű felügyelt lemezek vannak?**

Igen. Minden egyes tranzakció díjkötelesek. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/storage) olvasható.

**A standard szintű felügyelt lemez kell fizetnem a lemezen lévő adatok tényleges mérete vagy a lemez a kiosztott kapacitás?**

Kell fizetnie a kiosztott kapacitás, a lemez alapján. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/storage) olvasható.

**Hogyan eltér a nem felügyelt lemezek prémium szintű felügyelt lemezek díjszabása?**

A prémium szintű felügyelt lemezek díjszabása megegyezik a nem felügyelt prémium szintű lemezeket.

**Módosítható a tárfiók típusa (Standard vagy prémium), a saját felügyelt lemezek?**

Igen. A tárfiók típusa a felügyelt lemezek módosíthatja az Azure portal, PowerShell vagy az Azure CLI használatával.

**Az Azure storage-fiók egy VHD-fájl használatával hozzon létre egy felügyelt lemezt egy másik előfizetés?**

Igen.

**Az Azure storage-fiók egy VHD-fájl használatával hozzon létre egy felügyelt lemezt egy másik régióban?**

Nem.

**Vannak-e az ügyfelek által használt felügyelt lemezek méretezési korlátozások?**

A felügyelt lemezek használata esetén nem a társított storage-fiókok korlátokat. Viszont a maximális korlátot, 50 000 régiónként és a egy előfizetéssel lemeztípust felügyelt lemezeket.

**Eltarthat egy felügyelt lemezt egy növekményes pillanatképet?**

Nem. A jelenlegi pillanatkép funkció lehetővé teszi a felügyelt lemez teljes másolatával.

**Rendelkezésre állási csoportban lévő virtuális gépek felügyelt és nem felügyelt lemezek kombinációja állhat?**

Nem. A virtuális gépeket egy rendelkezésre állási csoportot az összes felügyelt lemezek vagy minden nem felügyelt lemezeket kell használnia. Rendelkezésre állási csoport létrehozásakor kiválaszthatja a használni kívánt lemezek milyen típusú.

**Managed Disks szolgáltatás alapértelmezett, hogy az Azure Portalon?**

Igen.

**Üres felügyelt lemez hozható létre?**

Igen. Üres lemez hozhat létre. Felügyelt lemez hozható létre egy virtuális Gépet, függetlenül például nélkül csatolása a virtuális gép.

**Mi a támogatott tartalék tartományok száma a rendelkezésre állási értéke, amely a felügyelt lemezeket használ?**

Attól függően, a régió, ahol a rendelkezésre állási csoport által használt felügyelt lemezek a támogatott tartalék tartományok száma, 2 vagy 3.

**Miben különbözik a standard szintű tárfiókot a diagnosztikai beállítása?**

Privát virtuális gép diagnosztikai tárfiók beállítása.

**Milyen típusú szerepköralapú hozzáférés-vezérlés támogatásának Managed Disks esetében érhető el?**

A felügyelt lemezek által támogatott három fő alapértelmezett szerepkörök:

* Tulajdonos: Mindent felügyelhetnek, beleértve a hozzáférést
* Közreműködői: Hozzáférés kivételével mindent felügyelhetnek
* Olvasó: Aki mindent megtekinthet, de nem végezhet módosításokat

**Van olyan módon, hogy e másolja, vagy egy felügyelt lemez exportálása egy privát tárfiókba?**

Csak olvasható közös hozzáférésű jogosultságkód (SAS) URI-t a felügyelt lemez létrehozása, és másolja bele a privát tárolójába partner vagy a helyszíni tárolási használatával. Az SAS URI-t az Azure portal, az Azure PowerShell-lel, az Azure CLI használatával is használhatja, vagy [AzCopy](../articles/storage/common/storage-use-azcopy.md)

**Hozhat létre saját felügyelt lemez egy példányát?**

Ügyfelek is pillanatkép készítése a felügyelt lemezeket, majd a pillanatkép egy másik felügyelt lemez létrehozása.

**Nem felügyelt lemezek továbbra is támogatottak?**

Igen, támogatja a nem felügyelt, és a felügyelt lemezek támogatottak. Azt javasoljuk, hogy az új számítási feladatok a felügyelt lemezek használata, és az aktuális számítási feladatok migrálása felügyelt lemezekre.

**Is szeretnék kerüljenek a nem felügyelt és felügyelt lemezek ugyanazon a virtuális Gépen?**

Nem.

**Ha hozzon létre egy 128 GB-os lemezt, és ezután a 130 GIB-ra méretének növeléséhez, kell fizetnem az a következő lemez mérete (256 GB)?**

Igen.

**Hozhatok létre helyileg redundáns tárolás, georedundáns tárolás és a felügyelt lemezek zónaredundáns tárolás?**

Az Azure Managed Disks jelenleg csak a helyileg redundáns tárolás, felügyelt lemezeit támogatja.

**Csökkenhessen vagy a felügyelt lemezek becsléseim?**

Nem. Ez a funkció jelenleg nem támogatott. 

**Képes a címbérlet is felosztása a lemezen lévő?**

Nem. Ez jelenleg nem támogatott, a címbérlet megtalálható véletlen törlés megakadályozása, ha a lemez van használatban.

**Módosíthatom a számítógép neve tulajdonság amikor egy specializált (nem a rendszer-előkészítő eszközzel létrehozott vagy általánosítva) operációsrendszer-lemez segítségével egy virtuális gép?**

Nem. A számítógép neve tulajdonság nem frissíthető. Az új virtuális gép örökli a szülő virtuális Géphez, amelyek az operációsrendszer-lemez létrehozásához használt. 

**Hol találok az Azure Resource Manager-sablonok minta felügyelt lemezekkel rendelkező virtuális gépek létrehozásához?**
* [A felügyelt lemezek használatával sablonok listáját.](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

**Egy blob egy lemezt hoz létre, amikor nincs semmilyen folyamatosan meglévő kapcsolatot, hogy a forrás blob?**

Nem, ha az új lemez jön létre, hogy a blob teljes önálló másolatát ekkor és a kettő között nincs kapcsolat. Ha szeretné a lemez létrehozása után a forrásblob törölheti működésének megzavarása nélkül megtesztelheti az újonnan létrehozott lemez bármilyen módon.

**Is átnevezek egy felügyelt vagy nem felügyelt lemez létrehozása után?**

Felügyelt lemezek nem nevezhető át őket. Azonban nem felügyelt lemez is átnevezése, mindaddig, amíg azt nem jelenleg csatlakozik egy virtuális Merevlemezt vagy virtuális Gépet.

**Használható az Azure-lemezek particionálása GPT?**

A GPT particionálás csak az adatlemezeket, nem az operációsrendszer-lemezek használható. Operációsrendszer-lemezek a fő rendszertöltő rekord partíció stílusa kell használnia.

## <a name="standard-ssd-disks"></a>Standard SSD-lemez

**Mik az Azure Standard SSD-lemez?**
Standard SSD-lemez IOPS alacsonyabb szinten egységes teljesítményt igénylő számítási feladatok költséghatékony tárolására optimalizált SSD adathordozóján által támogatott standard szintű lemezek.

<a id="standard-ssds-azure-regions"></a>**Mik a Standard SSD-lemez jelenleg támogatott régiókat?**
Minden Azure-régióban mostantól támogatja a Standard SSD-lemezeket.

**Az Azure Backup Standard SSD-k használata esetén érhető el?**
Igen, az Azure Backup már elérhető.

**Hogyan hozhatok létre Standard SSD-lemez?**
Standard SSD-lemez Azure Resource Manager-sablonok, SDK-t, a PowerShell vagy a parancssori felület használatával is létrehozhat. Az alábbiakban Standard SSD-lemez létrehozása a Resource Manager-sablon a szükséges paraméterek:

* *API-verzió* a Microsoft.Compute kell beállítani: `2018-04-01` (vagy újabb)
* Adja meg *managedDisk.storageAccountType* , `StandardSSD_LRS`

A következő példa bemutatja a *properties.storageProfile.osDisk* Standard SSD-lemezeket használó virtuális gép a következő szakaszban:

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

Standard SSD-lemez létrehozása sablon alapján, a teljes sablont példa: [egy Windows-rendszerképből Standard SSD-Adatlemezekkel rendelkező virtuális gép létrehozása](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

**Átválthatok saját meglévő lemezek Standard SSD-re?**
Igen. Tekintse meg [átalakítása az Azure disks storage felügyelt standard prémium szintre, és ez fordítva is igaz](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) kapcsolatos konvertálása a Managed Disks az általános útmutatást. És a Standard SSD-re frissíteni a lemez típusát a következő értéket használja.
-AccountType StandardSSD_LRS

**Mi az az előnye, hogy Standard SSD-lemezek használata helyett HDD?**
Standard SSD-lemez nyújthat nagyobb késést, konzisztencia, rendelkezésre állás és megbízhatóság HDD lemezek képest. Alkalmazások és szolgáltatások zökkenőmentes rengeteg további a standard szintű SSD, amely miatt. Vegye figyelembe, hogy prémium szintű SSD-lemezeket az ajánlott megoldás a legtöbb i/o-igényes éles számítási feladathoz. 

**Standard SSD-k használhatok a nem felügyelt lemezként?**
Standard SSD-lemez nem felügyelt lemezként csak érhetők el.

**Standard SSD-lemez támogatják a "egypéldányos virtuális gép SLA"?**
Standard SSD-k nem, nem kell egypéldányos virtuális gép SLA-t. Prémium szintű SSD-lemezeket egypéldányos virtuális gép SLA-t használja.

## <a name="migrate-to-managed-disks"></a>Migrálás felügyelt lemezekre

**A migrálás a Managed Disks teljesítményére hatással van?**

Áttelepítésébe tartozik a lemez tárolási egyik helyről egy másikra mozgása. Ez az előkészített keresztül adatokat, amelyek befejezéséhez, általában több óráig is tarthat kevesebb, mint 24 órában a lemezeken lévő adatok mennyiségétől függően háttérben futó példányát. Ebben az időszakban az alkalmazás tapasztalhatnak magasabb, mint a szokásos olvasási késés, valamint a bizonyos olvasási is első átirányítja az eredeti helyre is hosszabb időt vesz igénybe. Ez nincs hatással az írási késés ebben az időszakban.  

**Milyen változtatásokra van szükség, egy már meglévő Azure Backup szolgáltatás konfigurációs előtti/utáni migrálás a Managed Disks szolgáltatásba?**

Nincs módosításokra szükség.

**Azure Backup szolgáltatás az áttelepítés előtt létrehozott virtuális gép biztonsági mentések továbbra is működni fog?**

Igen, biztonsági mentések problémamentesen működik.

**Milyen változtatásokra van szükség, egy már meglévő Azure-lemezek titkosítása konfigurációs előtti/utáni migrálás a Managed Disks szolgáltatásba?**

Nincs módosításokra szükség.

**Az egy meglévő virtuálisgép-méretezési csoport automatikus áttelepítése a Managed Disks szolgáltatásba támogatott nem felügyeltről felügyelt csoportok?**

Nem. Létrehozhat egy új méretezési csoportot a Managed Disks nem felügyelt lemezek a régi méretezési csoportból a lemezkép használatával.

**Létrehozhatok egy felügyelt lemezt egy oldal blob pillanatkép a Managed Disks szolgáltatásba való migrálás előtt a?**

Nem. Oldal blob-pillanatkép exportálása lapblobként, és majd az exportált lapblob létrehozása egy felügyelt lemezt.

**Is lehet feladatátvételt beállítani a helyszíni gépek felügyelt lemezekkel rendelkező virtuális géphez az Azure Site Recovery által védett?**

Igen, kiválaszthatja a felügyelt lemezekkel rendelkező virtuális gép feladatátvételéhez.

**Az Azure-beli virtuális gépek replikálása Azure-bA az Azure Site Recovery által védett áttelepítés hatással van?**

Igen. Felügyelt lemezekkel rendelkező virtuális gépek Azure-bA védelmét az Azure Site Recovery jelenleg csak egy nyilvános előzetesként elérhető.

**Migrálhatók, amelyek vagy a korábban titkosított lemezessé tárfiókokban lévő nem felügyelt lemezekkel rendelkező virtuális gépek?**

Igen

## <a name="managed-disks-and-storage-service-encryption"></a>A felügyelt lemezek és a Storage Service Encryption szolgáltatással

**Az Azure Storage Service Encryption alapértelmezés szerint engedélyezve van egy felügyelt lemez létrehozásakor?**

Igen.

**Akik kezeli a titkosítási kulcsokat?**

A Microsoft felügyeli, a titkosítási kulcsokat.

**Képes a Storage Service Encryption is letiltja a felügyelt lemezek?**

Nem.

**Érhető el a Storage Service Encryption csak bizonyos régiókban?**

Nem. Érhető el az összes olyan régióban, ha a Managed Disks rendelkezésre állnak. A felügyelt lemezek az összes nyilvános régióban, és Németországban érhető el. Azt is érhető el Kínában, azonban csak az a Microsoft által felügyelt kulcsok, nem felhasználó által kezelt kulcsok.

**Hol találhatom meg, ha saját felügyelt lemez titkosítva van?**

Az idő, amikor létre lett hozva egy felügyelt lemezt az Azure Portalon, az Azure CLI és PowerShell talál. Ha az időpont 2017. június 9-es, a lemez titkosítva.

**Hogyan titkosíthatja a 2017. június 10. előtt létrehozott lemezek?**

2017. június 10. meglévő felügyelt lemezekre írt új adat automatikusan titkosítva lesznek. Is tervezzük meglévő adatok titkosításához, és a titkosítás a háttérben aszinkron módon történik. Most már meglévő adatokat kell titkosítani, ha a lemez másolatának létrehozása. Új lemez titkosítva lesz.

* [A felügyelt lemezek másolása az Azure CLI használatával](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [A felügyelt lemezek másolása a PowerShell használatával](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**A felügyelt pillanatképekre és rendszerképekre titkosítva vannak?**

Igen. Minden felügyelt pillanatképek és lemezképek létrehozott 2017. június 9-es, a rendszer automatikusan titkosítja. 

**Áttérhetek virtuális gépek, amelyek vagy a korábban titkosított lemezessé tárfiókokban lévő nem felügyelt lemezekkel rendelkező?**

Igen

**Felügyelt lemez vagy pillanatkép egy exportált VHD-t is titkosítva lesznek?**

Nem. Ha exportál egy virtuális Merevlemezt egy titkosított tárfiókba egy titkosított, de a felügyelt lemez vagy pillanatkép, majd titkosítva van. 

## <a name="premium-disks-managed-and-unmanaged"></a>Prémium szintű lemezek: Felügyelt és nem felügyelt

**Ha egy virtuális Gépet használ, amely támogatja a prémium szintű SSD-lemezeket, például a DSv2 méret sorozatán hozzácsatlakoztathatok prémium és standard szintű lemezek is?** 

Igen.

**Hozzácsatlakoztathatok prémium és standard szintű lemezek is egy mérete sorozat, amely nem támogatja a prémium szintű SSD-lemezeket, mint a D-, a Dv2-, G, vagy F sorozat?**

Nem. Csak a standard szintű adatlemezeket csatlakoztathat egy mérete sorozat, amely támogatja a prémium szintű SSD-lemezeket nem használó virtuális gépek.

**Ha prémium szintű adatlemez készíteni egy meglévő VHD-t, amely 80 GB volt, hogy mennyibe kerül?**

80 GB-os VHD-vel létrehozott prémium adatlemezt a következő elérhető prémium szintű lemez méretét, amely P10 lemez számít. A P10 lemez díjszabása szerint kell fizetnie.

**Tranzakciós költségek prémium szintű SSD-lemez használatára vannak?**

Nincs minden lemez méretét, amelyet az adott korlátok kiosztott IOPS és átviteli sebesség meg az állandó költség. Az egyéb díjakat kimenő sávszélesség és a pillanatkép-kapacitás, ha van ilyen. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/storage) olvasható.

**Mik azok az IOPS és átviteli sebességet, amelyet a lemez gyorsítótárból szoftverei korlátai?**

A gyorsítótár összesített korlátok és helyi SSD-DS-sorozat: a magonként 4000 IOPS és magonként másodpercenként 33 MiB. A GS sorozat magonként 5000 IOPS és magonként másodpercenként 50 MiB érhetők el.

**Helyi SSD-Managed Disks Virtuálisgép támogatott?**

Helyi SSD a Managed Disks Virtuálisgép mellékelt ideiglenes tárhely. Ott van nem kapcsolódik további költség az ideiglenes tároláshoz. Azt javasoljuk, hogy nem használja a helyi SSD az alkalmazásadatok tárolására, mert ez nem maradnak meg az Azure Blob storage-ban.

**Vannak-e bármilyen következményekkel TRIM a prémium szintű lemezek használatát?**

Nincs nem hátulütője TRIM vagy prémium szintű Azure-lemezek vagy a standard szintű lemezek használatához.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Új lemez mérete: Felügyelt és nem felügyelt

**Mi az a legnagyobb felügyelt lemez méretét a támogatott operációs rendszer és az adatlemezek?**

A partíció típusa, amely az Azure támogatja az operációsrendszer-lemez a fő rendszerindító rekord (MBR). A fő rendszertöltő rekord formátum támogatja a lemez mérete legfeljebb 2 Tib-ra. A legnagyobb, amely az Azure támogatja az operációsrendszer-lemez mérete 2 Tib-ra. Az Azure felügyelt adatlemezek akár 32 TiB támogatja. Felügyelt lemez mérete nagyobb, mint 4 Tib-ra vannak előzetes verzióban érhető el. Azokat a további információk: a [blogbejegyzés](https://aka.ms/azure-large-disk-32TB-preview-blog).

**Mi az a legnagyobb támogatott operációs rendszer és az adatlemezek nem felügyelt lemez mérete?**

A partíció típusa, amely az Azure támogatja az operációsrendszer-lemez a fő rendszerindító rekord (MBR). A fő rendszertöltő rekord formátum támogatja a lemez mérete legfeljebb 2 Tib-ra. A legnagyobb Azure támogatja a nem felügyelt operációsrendszer-lemez mérete 2 Tib-ra. Az Azure data nem felügyelt lemezek akár 4 Tib-ra támogatja.

**Mi az a legnagyobb lapblob méretét, amely támogatott?**

A legnagyobb lapblob méretét, amely támogatja az Azure 8 Tib-ra (8,191 GiB). Maximális lapblob méretét, amikor a virtuális Géphez csatolt adatok vagy operációsrendszer-lemezek 4 Tib-ra (4095 GiB).

**Kell létrehozása, csatlakoztatása, átméretezése és 1 TiB-nál nagyobb lemezek feltöltése az Azure-eszközök új verziójának használatával?**

Nem kell frissíteni a meglévő Azure-eszközök létrehozása, csatlakoztatása vagy 1 TiB-nál nagyobb lemezek átméretezése. A VHD-fájl feltöltése a helyszínről az Azure lapblob vagy nem felügyelt lemez, szüksége használhatja az alább felsorolt legújabb eszköz csoportokat. Csak támogatjuk a VHD feltöltése legfeljebb 8 Tib-ra.

|Azure-eszközök      | Támogatott verziók                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | A verziószám 4.1.0 verzióra: 2017 június kiadás vagy újabb|
|Azure CLI v1     | A verziószám 0.10.13: 2017 május kiadás vagy újabb|
|Azure CLI v2     | 2.0.12-es verziószám: 2017 július kiadás vagy újabb|
|AzCopy           | A verziószám 6.1.0: 2017 június kiadás vagy újabb|

**Támogatottak a P4 és a P6 szintű lemezméretek nem felügyelt lemezek és lapblobok esetében?**

P4 (32 GiB) és P6 (64 GiB) lemezméretek nem támogatottak az alapértelmezett lemez réteget nem felügyelt lemezek és lapblobok. Kell külön [a Blobszint beállítása](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) a lemezt, ezek a rétegek leképezve a P4 és a P6. Ha telepít egy nem felügyelt lemez vagy lap blob, a lemez mérete vagy a tartalom hossza kisebb, mint 32 GIB-ra vagy 64 GIB-ra a Blobszint beállítása nélkül 32 GiB közötti, továbbra is a P10 500 IOPS és 100 MiB/s és a csatlakoztatott tarifacsomag kerül.

**Ha a meglévő prémium szintű felügyelt lemez kevesebb, mint 64 GiB lett létrehozva, mielőtt a kis méretű lemez (körülbelül 2017. június 15.) engedélyezve lett, hogyan számlázzuk?**

Meglévő kis méretű prémium szintű lemezek kisebb, mint 64 GiB továbbra is a P10 tarifacsomag szerint számlázzuk.

**Hogyan válthatok kis méretű prémium szintű lemezek szintjének lemez kevesebb, mint 64 GiB P10 a P4 és a P6?**

Pillanatkép készítése a kis méretű lemezek, és hozzon létre egy lemezt automatikusan átvált a tarifacsomag P4 vagy P6 kiépített mérete alapján.

**Ön átméretezhető méretek meglévő felügyelt lemezeket kevesebb mint 4 Tib-ra való új újonnan bevezetett lemezméretek legfeljebb 32 Tib-ra?**

Új felügyelt lemezek méretei 8 Tib-ra, 16 TiB és 32 TiB jelenleg előzetes verzióban érhető el. Nem még támogatjuk a meglévő lemezméretek, az új lemezméret átméretezése.

**Mi az a legnagyobb lemezméret az Azure Backup és az Azure Site Recovery szolgáltatás által támogatott?**

A legnagyobb lemez méretét, az Azure Backup és az Azure Site Recovery szolgáltatás által támogatott 4 Tib-ra.

**Mik azok a virtuális gép ajánlott mérete a nagyméretű lemezek (> 4TiB) optimalizált Standard SSD és HDD standard szintű lemezek elérése érdekében a lemez IOPS és sávszélesség?**

Standard SSD és Standard HDD nagy lemezméretek, az adatátviteli sebességet eléréséhez (> 4 TB-os) és 500 iops-érték 60 MiB/s, használjon egy, a következő Virtuálisgép-méretek optimalizálhatja a teljesítményt: A B sorozat, DSv2-sorozat, a Dsv3-sorozat, az ESv3 sorozatú Fs-sorozat, Fsv2-sorozat, M-sorozat, GS-sorozat, NCv2 sorozat, az NCv3 sorozatú vagy Ls-sorozat virtuális gépei.

**Mely régiók csak a felügyelt lemezek mérete nagyobb, mint a támogatott 4 Tib-ra?**

Az előzetes verziója a felügyelt lemezek méretei túli 4 Tib-ra az Azure éles régiót kivéve Gov, Kínában, és Németországban támogatottak. 

**Hogy támogatják-állomás gyorsítótárazásának engedélyezése az újabb lemezméretek a?**

Lemezméret kisebb 4TiB állomás gyorsítótárazása az írásvédett és az olvasási/írási támogatjuk. Adatlemez-méretet a több mint 4 Tib-ra, nem támogatjuk a gyorsítótár-beállítás none. Azt javasoljuk, hogy kisebb lemezméretet, ahol várható és jobb teljesítményt boost figyelje a virtuális géphez gyorsítótárazott adatok gyorsítótárazásának kihasználva.

## <a name="what-if-my-question-isnt-answered-here"></a>Mi történik, ha kérdésem itt nem választ?

Ha a kérdés nem szerepel a listán, ossza meg velünk, és választ találjon nyújtunk segítséget. Ez a cikk végén található kérdés felteheti a megjegyzések. Az Azure Storage csapata és a Közösség többi tagjával, ez a cikk kapcsolatos érhet el, használja az MSDN [Azure Storage-fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).

Kérelem funkciók, a kérelmek és a ötlet segíthet megtenni a [Azure Storage-visszajelzési fórumon](https://feedback.azure.com/forums/217298-storage).
