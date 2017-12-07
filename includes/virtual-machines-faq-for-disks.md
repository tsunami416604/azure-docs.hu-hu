# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Azure IaaS virtuális gép és a kezelt és kezeletlen premium lemezek kapcsolatos gyakori kérdések

Ebben a cikkben megválaszolunk néhány Azure felügyelt lemezek és a prémium szintű Azure Storage kapcsolatos gyakori kérdésekre.

## <a name="managed-disks"></a>Felügyelt lemezek

**Mi az Azure Managed lemezek?**

Felügyelt lemezek olyan szolgáltatás, amely egyszerűbbé teszi a Lemezkezelés Azure IaaS virtuális gépek kezelése a tárolási fiók kezelése az Ön által. További információkért lásd: a [felügyelt lemezekhez – áttekintés](../articles/virtual-machines/windows/managed-disks-overview.md).

**Ha egy standard szintű felügyelt lemezes I készíteni egy meglévő VHD-t, amely 80 GB, mennyi lesz, amely költségeket, me?**

80 GB-os virtuális merevlemezről létrehozott egy standard szintű felügyelt lemezes a rendszer a következő elérhető standard méretű lemez méretet, amelyet egy S10 lemez. Most a S10 lemez díjszabás alapján számítjuk fel. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/storage) olvasható.

**Vannak-e a standard szintű felügyelt lemez tranzakciós költségeket?**

Igen. Minden tranzakció még fizetnie. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/storage) olvasható.

**Az egy standard szintű felügyelt lemezes I megterheljük a lemezen lévő adatok tényleges mérete, vagy a lemez kiosztott kapacitást?**

A lemez kiosztott kapacitást alapján még fel. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/storage) olvasható.

**Hogyan azonos a nem felügyelt lemezek felügyelt premium lemezek árképzési?**

A felügyelt premium lemezek árképzési megegyezik a nem felügyelt premium lemezek.

**A tárfiók típusa (Standard vagy prémium) a kezelt lemezek is megváltozik?**

Igen. Az Azure-portálon, a PowerShell vagy az Azure parancssori felület használatával módosíthatja a tárfiók típusa a felügyelt lemezek.

**Van mód, hogy lehet másolni, vagy egy felügyelt lemezes exportálása a titkos storage-fiók?**

Igen. A felügyelt lemezek exportálhatja az Azure-portálon, a PowerShell vagy az Azure parancssori felület használatával.

**Az Azure-tárfiók VHD-fájl használatával hozzon létre egy felügyelt lemezes egy másik előfizetést?**

Nem.

**Az Azure-tárfiók VHD-fájl használatával hozzon létre egy felügyelt lemezes egy másik régióban?**

Nem.

**Vannak-e a felügyelt lemezeket használó ügyfelek skálázási korlátozások?**

Kezelt lemezek megszünteti a társított tárfiókok korlátok. Azonban a előfizetésenként felügyelt lemezek számát korlátozódik 2000 alapértelmezés szerint. Ezt a számot növelheti a támogatási hívása.

**Eltarthat egy kezelt lemez növekményes pillanatképet?**

Nem. Az aktuális pillanatkép-készítés révén felügyelt lemezes teljes másolata. Azonban azt tervezi, hogy a jövőben támogatja a növekményes pillanatképek.

**Virtuális gépek rendelkezésre állási csoport által felügyelt és nem kezelt lemezek kombinációja állhat?**

Nem. A virtuális gépek rendelkezésre állási csoportba kell használnia, vagy az összes felügyelt lemeznek, vagy a nem felügyelt összes lemez. Amikor létrehoz egy rendelkezésre állási csoportot, kiválaszthatja a használni kívánt lemezek milyen típusú.

**Felügyelt lemezek van az alapértelmezett beállítás, az Azure-portálon?**

Igen. 

**Hozhat létre egy üres felügyelt lemezes?**

Igen. Üres lemez hozhat létre. Felügyelt lemezes hozhatók létre függetlenül a virtuális gépek, például egy virtuális Géphez való csatlakoztatás nélkül.

**Mi a támogatott tartalék tartományok számának a rendelkezésre állási beállítása felügyelt lemezeket használó?**

Attól függően, hogy a régió, ahol a rendelkezésre állási csoport által felügyelt lemezeket használó a támogatott tartalék tartományok számának, 2 vagy 3.

**Hogyan van a standard szintű tárfiók beállítása diagnosztikai?**

A Virtuálisgép-diagnosztika titkos tárolási fiók beállítása. A jövőben tervezzük diagnosztika váltani kezelt lemezeken is.

**Milyen típusú szerepköralapú hozzáférés-vezérlés támogatásának felügyelt lemezek érhető el?**

Felügyelt lemezek által támogatott három fő alapértelmezett szerepkörök:

* Tulajdonos: Mindent felügyelhetnek, beleértve a hozzáférést
* Társszerző: Hozzáférés kivételével mindent felügyelhetnek
* Olvasó: Mindent megtekinthetnek, de nem módosítható

**Van mód, hogy lehet másolni, vagy egy felügyelt lemezes exportálása a titkos storage-fiók?**

A csak olvasható közös hozzáférésű jogosultságkód URI lekérése a felügyelt lemezes és a segítségével a saját tárolási fiók vagy a helyszíni tárolási másolja.

**A felügyelt lemezes másolatát hozhat létre?**

Ügyfelek pillanatkép készítése a felügyelt lemezek és a pillanatkép segítségével hozzon létre egy másik kezelt lemezre.

**Nem felügyelt lemezek továbbra is támogatottak?**

Igen. Nem felügyelt és a felügyelt támogatjuk. Javasoljuk, hogy új munkaterhelések esetén használja a felügyelt lemezeit, és az aktuális munkaterheléseket telepít át kezelt lemezek.


**Ha megkezdtem 128 GB-os lemez létrehozása, és növelje a 130 GB-os méret, I megterheljük a következő lemez méretét (512 GB)?**

Igen.

**Létrehozhatok helyileg redundáns tárolás, a georedundáns tárolás és zónaredundáns tárolás által kezelt lemezeken?**

Azure-lemezeket felügyelt jelenleg csak a helyileg redundáns tárolás felügyelt lemezeit támogatja.

**Csökkenhessen vagy a felügyelt lemezek downsize?**

Nem. Ez a funkció jelenleg nem támogatott. 

**Módosítható a számítógép neve tulajdonság amikor egy speciális (nem hozta létre a rendszer-előkészítő eszközzel vagy általánosított) operációsrendszer-lemez a virtuális gép létrehozásához használt?**

Nem. A számítógép name tulajdonság nem frissíthető. Az új virtuális gép örökli a szülő virtuális gép, amelyen az operációsrendszer-lemez létrehozásához használt. 

**Hol találnak a minta Azure Resource Manager-sablonok felügyelt lemezzel rendelkező virtuális gépek létrehozásához?**
* [Felügyelt lemezekkel sablonok listája](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

## <a name="migrate-to-managed-disks"></a>Migrálás felügyelt lemezekre 

**Milyen kell módosítania a meglévő Azure Backup szolgáltatás konfigurációs előtt vagy után az áttelepítés lemezek felügyelt?**

Nincs módosításokra szükség. 

**Azure biztonsági mentési szolgáltatás az áttelepítés előtt létrehozott, a virtuális gép biztonsági másolatok továbbra is működnek majd?**

Igen, biztonsági mentések zökkenőmentesen működjön.

**Mit kell módosítania a már meglévő Azure-lemezeket titkosítási konfigurációs előtt vagy után az áttelepítés lemezek felügyelt?**

Nincs módosításokra szükség. 

**Nem felügyelt lemezekből rendszerről való automatikus áttelepítéshez, egy meglévő VM skálázási készletek (VMSS)-hoz támogatott felügyelt lemezek?**

Nem. Létrehozhat egy új VMSS felügyelt lemezeket a régi VMSS a lemezkép használatával nem kezelt lemezek. 

**Hozható létre egy felügyelt lemezt egy lap blob pillanatképből felügyelt lemezek áttelepítése előtt?**

Nem. Lap blob pillanatkép oldalakra vonatkozó blob exportálni, és majd hozhat létre egy felügyelt lemezt az exportált oldalakra vonatkozó blob. 

**Tudom a feladatátvétel a helyszíni gépeket felügyelt lemezzel rendelkező virtuális gép az Azure Site Recovery által védett?**

Igen, ha szeretné felügyelt lemezzel rendelkező virtuális gép feladatátvétele.

**Bármely Azure hely helyreállítás (ASR) által védett Azure az Azure-bA replikációval Azure virtuális gépeken az áttelepítés hatása van?**

Igen. Az ASR Azure az Azure-bA védelme nem támogatott felügyelt lemezzel rendelkező virtuális gépekhez. Ugrás CY2018 végén támogatnak. 

**Áttelepíthetem a nem felügyelt a storage-fiókok vagy kezelt lemezek korábban titkosított lévő lemezzel rendelkező virtuális gépek?**

Igen

## <a name="managed-disks-and-storage-service-encryption"></a>Lemezek és a Storage szolgáltatás titkosítási felügyelt 

**Azure Storage szolgáltatás titkosítási alapértelmezés szerint engedélyezve van egy felügyelt lemezes létrehozásakor?**

Igen.

**A titkosítási kulcsok kezelő?**

A Microsoft kezeli a titkosítási kulcsokat.

**Képes-e letiltása Storage szolgáltatás titkosítási a felügyelt lemezek?**

Nem.

**Érhető Storage szolgáltatás titkosítási csak meghatározott régióiba?**

Nem. Érhető el minden olyan régióban, amennyiben rendelkezésre áll-e felügyelt lemezek. Felügyelt lemezek minden nyilvános régiók és Németországban érhető el.

**Hogyan tudhatom meg titkosítottak, ha a felügyelt lemezes?**

A felügyelt lemezes az Azure-portálon az Azure CLI és PowerShell létrehozásának ideje talál. Ha az időpont után 2017. június 9., a lemez titkosítva. 

**Hogyan titkosíthatja a meglévő lemezek 2017. június 10. előtt létrehozott?**

Től 2017. június 10. a meglévő felügyelt lemezekre írt új adatokat automatikusan titkosítva. Azt is tervezi, hogy a meglévő adatok titkosítása, és a titkosítás aszinkron módon történik a háttérben. Ha meglévő adatokat most titkosítani kell, hozzon létre egy másolatot a lemezen. Új lemez titkosítva legyen.

* [Felügyelt lemezeket másolja az Azure parancssori felület használatával](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Felügyelt lemezeket másolja a PowerShell használatával](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**Felügyelt pillanatfelvételek és lemezképek titkosítva van?**

Igen. Az összes kezelt pillanatképeket és lemezképek létrehozása után 2017. június 9., automatikusan titkosítva vannak. 

**Is virtuális gépek is átalakítása nem felügyelt a storage-fiókok vagy kezelt lemezek korábban titkosított lévő lemezek?**

Igen

**A felügyelt lemezes vagy a pillanatképet egy exportált VHD-t is titkosítva lesznek?**

Nem. De ha egy virtuális Merevlemezt egy titkosított tárfiókba történő exportálását egy titkosított kezelt lemez vagy a pillanatképet, majd titkosítva van. 

## <a name="premium-disks-managed-and-unmanaged"></a>Prémium szintű lemezekhez: felügyelt és nem felügyelt

**Egy virtuális gép használ, amely támogatja a prémium szintű Storage, például egy DSv2 mérete több premium és a szabványos adatlemezek csatolható?** 

Igen.

**Csatolható prémium és standard adatlemezek is, amely nem támogatja a prémium szintű Storage, például a D, Dv2, G vagy F adatsorozat mérete több?**

Nem. Csak a standard adatlemezek csatolhat a virtuális gépek, amelyek nem használják, amely támogatja a prémium szintű Storage mérete több.

**Ha egy meglévő virtuális merevlemezről, de a 80 GB prémium adatlemezt hozok létre, mennyire, amely ára?**

80 GB-os virtuális merevlemezről létrehozott prémium adatlemezt a rendszer a következő érhető el prémium szintű lemez méretet, amelyet P10 lemez. Most a P10 lemez díjszabás alapján számítjuk fel.

**Vannak-e a prémium szintű Storage tranzakciós költségek?**

Nincs minden lemez méretét, amelyet adott határral kiosztott iops-érték és az átvitel meg egy rögzített költséget. Az egyéb költségekkel kimenő sávszélesség és a pillanatkép-kapacitást, ha van ilyen. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/storage) olvasható.

**Az iops-érték és a teljesítményt, amelyek szeretnék lekérheti a lemezgyorsítótár korlátai**

A kombinált korlátozhatja a gyorsítótár és a DS-ben több helyi SSD is core 4000 iops-értéket core másodpercenként 33 MB. A GS adatsorozat 5000 iops teljesítményt nyújtanak core és 50 MB / s / core kínál.

**A lemezek felügyelt virtuális gépek támogatja a helyi SSD?**

A helyi SSD az ideiglenes tároló, a lemezek felügyelt virtuális gépek részét képező. Nincs ingyenesen a ideiglenes tárolására. Azt javasoljuk, hogy nem használja a helyi SSD az alkalmazásadatok tárolására, mert azt az Azure Blob storage nem megőrzött.

**Vannak-e bármilyen következményekkel VÁGÁSHOZ a premium lemezek használatát?**

Nincs vágás használatához Azure-lemezeket vagy Premium vagy a standard lemezek hátránya van.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Új lemezméret: felügyelt és nem felügyelt

**Mi az a legnagyobb lemez méretét a támogatott operációs rendszer és az adatlemezek?**

A partíció típusa, amely Azure támogatja az operációsrendszer-lemez a fő rendszerindító rekord (MBR). Egy lemezt a fő rendszertöltő rekord formátum támogatja legfeljebb 2 TB-os méret. A legnagyobb, amely Azure támogatja az operációsrendszer-lemez mérete 2 TB. Azure akár 4 TB adatlemezek támogatja. 

**Mi az a legnagyobb blob mérete támogatott?**

A legnagyobb blob mérete, amely támogatja az Azure rendszeren 8 TB (8,191 GB). Nagyobb, mint 4 TB-os (4095 GB) egy virtuális Géphez csatlakozik, adatok vagy az operációs rendszer lemezek lapblobokat nem támogatottak.

**Kell létrehozni, csatolja, átméretezése és 1 TB-nál nagyobb lemezek feltöltése Azure eszközök új verziójának segítségével?**

Nem kell frissíteni a meglévő Azure-eszközök létrehozására, csatolja, és 1 TB-nál nagyobb lemezek átméretezése. Töltse fel a VHD-fájl a helyszíni közvetlenül az Azure oldalakra vonatkozó blob vagy nem felügyelt lemezt, kell használnia az eszköz legújabb beállítása:

|Azure-eszközök      | Támogatott verziók                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | 4.1.0 verziószám: június 2017 kiadás vagy újabb verzió|
|Az Azure CLI 1-es verzió     | Verziószám 0.10.13: Előfordulhat, hogy 2017 kiadás vagy újabb verzió|
|AzCopy           | 6.1.0 verziószám: június 2017 kiadás vagy újabb verzió|

Az Azure parancssori felület v2 és Azure Tártallózó támogatása hamarosan elérhető. 

**Nem felügyelt lemezek vagy lapblobokat támogatott P4 és P6 mérete?**

Nem. P4 (32 GB) és P6 (64 GB-os) lemezméret csak felügyelt lemezek támogatottak. Nem felügyelt lemezek és a lapblobokat támogatása hamarosan elérhető.

**Ha a meglévő prémium szintű felügyelt lemez kevesebb, mint 64 GB-os jött létre a kisebb lemezt (körülbelül 2017. június 15.) engedélyezése előtt, hogyan azt számlázása?**

Meglévő kis premium lemezek kisebb, mint 64 GB-os továbbra is fizetnie kell ezért a P10 tarifacsomag alapján. 

**Hogyan válthat a lemez réteg P4 vagy P6 P10 a 64 GB-nál kisebb kis premium lemezek?**

Pillanatkép készítése a kis kapacitású lemezeket, és ezután automatikusan Váltás az árképzési szint P4 vagy a kiosztott mérete alapján P6 lemez létrehozása. 


## <a name="what-if-my-question-isnt-answered-here"></a>Mi történik, ha a fentiekben itt nem választ?

Ha kérdését itt nem látható, ossza meg velünk, és segítünk talált választ. A megjegyzések is közzétesz egy kérdést, ez a cikk végén. Az Azure Storage csapat és a Közösség más tagjaival kapcsolatos cikkben bevonásához, használja az MSDN [Azure Storage fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).

Kérelem szolgáltatások, a kérelmek és ötleteket a a [Azure Storage-visszajelzési fórumon](https://feedback.azure.com/forums/217298-storage).
