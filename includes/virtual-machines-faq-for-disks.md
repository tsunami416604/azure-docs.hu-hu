---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 66964e4ed0877cc47dd7d2b5f3c6a62f4fa006b1
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37348184"
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
* Olvasó:, Aki mindent megtekinthet, de nem végezhet módosításokat

**Van olyan módon, hogy e másolja, vagy egy felügyelt lemez exportálása egy privát tárfiókba?**

Csak olvasható közös hozzáférésű jogosultságkód (SAS) URI-t a felügyelt lemez létrehozása, és másolja bele a privát tárolójába partner vagy a helyszíni tárolási használatával. Az SAS URI-t az Azure portal, az Azure PowerShell-lel, az Azure CLI használatával is használhatja, vagy [AzCopy](../articles/storage/common/storage-use-azcopy.md)

**Hozhat létre saját felügyelt lemez egy példányát?**

Ügyfelek is pillanatkép készítése a felügyelt lemezeket, majd a pillanatkép egy másik felügyelt lemez létrehozása.

**Nem felügyelt lemezek továbbra is támogatottak?**

Igen, támogatja a nem felügyelt, és a felügyelt lemezek támogatottak. Azt javasoljuk, hogy az új számítási feladatok a felügyelt lemezek használata, és az aktuális számítási feladatok migrálása felügyelt lemezekre.


**Ha hozzon létre egy 128 GB-os lemezt, és növelje a 130 GB méretű, kell fizetnem az a következő lemez mérete (256 GB)?**

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

## <a name="standard-ssd-disks-preview"></a>Standard SSD-lemezeket (előzetes verzió)

**Mik az Azure Standard SSD-lemez?**
Standard SSD-lemez IOPS alacsonyabb szinten egységes teljesítményt igénylő számítási feladatok költséghatékony tárolására optimalizált SSD adathordozóján által támogatott standard szintű lemezek. Előzetes verzióban érhetők el a korlátozott számú régióban, és korlátozott kezelhetőségi (Resource Manager-sablonok keresztül érhető el).

<a id="standard-ssds-azure-regions"></a>**Mik a Standard SSD-lemez (előzetes verzió) jelenleg támogatott régiókat?**
* Észak-Európa
* Közép-Franciaország
* USA 2. keleti régiója
* USA középső régiója
* Közép-Kanada
* Kelet-Ázsia
* Korea déli régiója
* Kelet-Ausztrália

**Hogyan hozhatok létre Standard SSD-lemez?**
Jelenleg standard szintű SSD-lemezeket az Azure Resource Manager-sablonok használatával is létrehozhat. Az alábbiakban Standard SSD-lemez létrehozása a Resource Manager-sablon a szükséges paraméterek:

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
Igen. Tekintse meg [átalakítása az Azure disks storage felügyelt standard prémium szintre, és ez fordítva is igaz](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/convert-disk-storage) kapcsolatos konvertálása a Managed Disks az általános útmutatást. És a Standard SSD-re frissíteni a lemez típusát a következő értéket használja.
-AccountType StandardSSD_LRS

**Standard SSD-k használhatok a nem felügyelt lemezként?**
Standard SSD-lemez nem felügyelt lemezként csak érhetők el.

**Standard SSD-lemez támogatják a "egypéldányos virtuális gép SLA"?**
Standard SSD-k nem, nem kell egypéldányos virtuális gép SLA-t. Prémium szintű SSD-lemezeket egypéldányos virtuális gép SLA-t használja.

## <a name="migrate-to-managed-disks"></a>Migrálás felügyelt lemezekre 

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

## <a name="premium-disks-managed-and-unmanaged"></a>Prémium szintű lemezek: felügyelt és nem felügyelt

**Ha egy virtuális Gépet használ, amely támogatja a prémium szintű SSD-lemezeket, például a DSv2 méret sorozatán hozzácsatlakoztathatok prémium és standard szintű lemezek is?** 

Igen.

**Hozzácsatlakoztathatok prémium és standard szintű lemezek is egy mérete sorozat, amely nem támogatja a prémium szintű SSD-lemezeket, mint a D-, a Dv2-, G, vagy F sorozat?**

Nem. Csak a standard szintű adatlemezeket csatlakoztathat egy mérete sorozat, amely támogatja a prémium szintű SSD-lemezeket nem használó virtuális gépek.

**Ha prémium szintű adatlemez készíteni egy meglévő VHD-t, amely 80 GB volt, hogy mennyibe kerül?**

80 GB-os VHD-vel létrehozott prémium adatlemezt a következő elérhető prémium szintű lemez méretét, amely P10 lemez számít. A P10 lemez díjszabása szerint kell fizetnie.

**Tranzakciós költségek prémium szintű SSD-lemez használatára vannak?**

Nincs minden lemez méretét, amelyet az adott korlátok kiosztott IOPS és átviteli sebesség meg az állandó költség. Az egyéb díjakat kimenő sávszélesség és a pillanatkép-kapacitás, ha van ilyen. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/storage) olvasható.

**Mik azok az IOPS és átviteli sebességet, amelyet a lemez gyorsítótárból szoftverei korlátai?**

A gyorsítótár összesített korlátok és helyi SSD-DS-sorozat: a magonként 4000 IOPS és 33 MB / s magonként. A GS sorozat magonként 5000 IOPS és az 50 MB / s magonként érhetők el.

**Helyi SSD-Managed Disks Virtuálisgép támogatott?**

Helyi SSD a Managed Disks Virtuálisgép mellékelt ideiglenes tárhely. Ott van nem kapcsolódik további költség az ideiglenes tároláshoz. Azt javasoljuk, hogy nem használja a helyi SSD az alkalmazásadatok tárolására, mert ez nem maradnak meg az Azure Blob storage-ban.

**Vannak-e bármilyen következményekkel TRIM a prémium szintű lemezek használatát?**

Nincs nem hátulütője TRIM vagy prémium szintű Azure-lemezek vagy a standard szintű lemezek használatához.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Új lemez méretek: felügyelt és nem felügyelt

**Mi az a legnagyobb lemez méretét a támogatott operációs rendszer és az adatlemezeket?**

A partíció típusa, amely az Azure támogatja az operációsrendszer-lemez a fő rendszerindító rekord (MBR). A fő rendszertöltő rekord formátumot támogatja egy lemezt méret 2 TB-ig. A legnagyobb, amely az Azure támogatja az operációsrendszer-lemez mérete 2 TB. Az Azure az adatlemezek akár 4 TB-os támogatja. 

**Mi az a legnagyobb lapblob méretét, amely támogatott?**

A legnagyobb lapblob méretét, amely támogatja az Azure esetében 8 TB (8,191 GB). A maxmium lap blog, amikor a virtuális Géphez csatolt adatok vagy operációsrendszer-lemezek mérete 4 TB-ig (4095 GB).

**Kell létrehozása, csatlakoztatása, átméretezése és 1 TB-nál nagyobb lemezek feltöltése az Azure-eszközök új verziójának használatával?**

Nem kell frissíteni a meglévő Azure-eszközök létrehozása, csatlakoztatása és 1 TB-nál nagyobb lemezek átméretezése. A VHD-fájl feltöltése a helyszínről az Azure lapblob vagy nem felügyelt lemez, használhatja a legújabb eszköz csoportokat kell:

|Azure-eszközök      | Támogatott verziók                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Verziószám 4.1.0: 2017 június kiadás vagy újabb|
|Azure CLI v1     | Verziószám 0.10.13: 2017 május kiadás vagy újabb|
|AzCopy           | Verziószám 6.1.0: 2017 június kiadás vagy újabb|

Az Azure CLI-vel v2 és az Azure Storage Explorer támogatása hamarosan elérhető lesz. 

**Támogatottak a P4 és a P6 szintű lemezméretek nem felügyelt lemezek és lapblobok esetében?**

Nem. P4 (32 GB) és P6 (64 GB-os) lemez mérete csak a felügyelt lemezek támogatottak. Nem felügyelt lemezek és lapblobok támogatása hamarosan elérhető lesz.

**Ha a meglévő prémium szintű felügyelt lemez kevesebb, mint 64 GB-os lett létrehozva, mielőtt a kis méretű lemez (körülbelül 2017. június 15.) engedélyezve lett, hogyan számlázzuk?**

Meglévő kis méretű prémium szintű lemezek kisebb, mint 64 GB-os továbbra is a P10 tarifacsomag szerint számlázzuk. 

**Hogyan válthatok a P4 és a P6 a P10-es 64 GB-nál kisebb kis méretű prémium szintű lemezek lemez szintjét?**

Pillanatkép készítése a kis méretű lemezek, és hozzon létre egy lemezt automatikusan átvált a tarifacsomag P4 vagy P6 kiépített mérete alapján. 


## <a name="what-if-my-question-isnt-answered-here"></a>Mi történik, ha kérdésem itt nem választ?

Ha a kérdés nem szerepel a listán, ossza meg velünk, és választ találjon nyújtunk segítséget. Ez a cikk végén található kérdés felteheti a megjegyzések. Az Azure Storage csapata és a Közösség többi tagjával, ez a cikk kapcsolatos érhet el, használja az MSDN [Azure Storage-fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).

Kérelem funkciók, a kérelmek és a ötlet segíthet megtenni a [Azure Storage-visszajelzési fórumon](https://feedback.azure.com/forums/217298-storage).
