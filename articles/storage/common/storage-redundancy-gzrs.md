---
title: Nagyteljesítményű Azure Storage-alkalmazások kiépítése a Geo-Zone-redundáns tárolással (GZRS) (előzetes verzió) | Microsoft Docs
description: A Geo-Zone-redundáns tárolás (GZRS) a helyi leállású zónák (ZRS-EK) magas rendelkezésre állását a földrajzilag redundáns tárolás (GRS) által biztosított védelmet nyújtja. A GZRS-fiókban lévő adatok az elsődleges régió Azure rendelkezésre állási zónái között replikálódnak, és egy másodlagos földrajzi régióba is replikálódnak a regionális katasztrófák elleni védelem érdekében.
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/13/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 1b5a9eb9f91469e6e25c5d90bfeb4aa0213009bc
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69017295"
---
# <a name="build-highly-available-azure-storage-applications-with-geo-zone-redundant-storage-gzrs-preview"></a>Hosszú rendelkezésre állású Azure Storage-alkalmazások kiépítése a Geo-Zone-redundáns tárolással (GZRS) (előzetes verzió)

A Geo-Zone-redundáns tárolás (GZRS) (előzetes verzió) megtartja a [zóna-redundáns tárolás (ZRS)](storage-redundancy-zrs.md) magas rendelkezésre állását a regionális kimaradások elleni védelemmel, a [földrajzilag REDUNDÁNS tárolás (GRS)](storage-redundancy-grs.md)által biztosított módon. A GZRS-fiókban lévő adatok az elsődleges régió három Azure-beli [rendelkezésre állási zónájában](../../availability-zones/az-overview.md) replikálódnak, és egy másodlagos földrajzi régióba is replikálódnak a regionális katasztrófák elleni védelem érdekében.

GZRS-fiókkal folytathatja az adatok olvasását és írását, ha a rendelkezésre állási zónák elérhetetlenné válnak, vagy nem állíthatók helyre. Emellett az adatai tartósak is maradnak a teljes regionális leállás vagy egy olyan katasztrófa esetén, amelyben az elsődleges régió nem helyreállítható. A GZRS úgy lett kialakítva, hogy legalább 99.99999999999999%-os (16 9) tartósságot biztosítson az objektumok számára egy adott évben. A GZRS ugyanazokat a [skálázhatósági célokat](storage-scalability-targets.md) kínálja, mint a LRS, a ZRS, a GRS vagy az ra-GRS. Engedélyezheti a másodlagos régióban található, olvasási hozzáférésű geo-Zone-redundáns tárolással (RA-GZRS) rendelkező információk olvasási hozzáférését, ha az elsődleges régióban katasztrófa esetén az alkalmazásoknak képesnek kell lenniük az információk olvasására.

A Microsoft a GZRS használatát javasolja az olyan alkalmazásokhoz, amelyek következetességet, tartósságot, magas rendelkezésre állást, kiváló teljesítményt és rugalmasságot igényelnek az inaster helyreállításhoz. A regionális katasztrófa esetén a másodlagos régióhoz való olvasási hozzáférés további biztonsága érdekében engedélyezze az RA-GZRS-t a Storage-fiókjához.

## <a name="about-the-preview"></a>Az előzetes verzió ismertetése

Csak az általános célú v2 tároló-fiókok támogatják a GZRS és az RA-GZRS. További információ a Storage-fiókok típusairól: az [Azure Storage-fiók áttekintése](storage-account-overview.md). A GZRS és RA-GZRS támogatja a blokk blobokat, a nem VHD-lemezeket, a fájlokat, a táblákat és a várólistákat.

A GZRS és az RA-GZRS jelenleg előzetes verzióként érhető el a következő régiókban:

- USA keleti régiója
- Nyugat-Európa

A Microsoft továbbra is engedélyezi a GZRS és az RA-GZRS további Azure-régiókban. A támogatott régiókkal kapcsolatos információkért olvassa el rendszeresen az [Azure szolgáltatás frissítéseinek](https://azure.microsoft.com/updates/) oldalát.

Az előzetes verzió díjszabásával kapcsolatos információkért tekintse meg a [Blobok](https://azure.microsoft.com/pricing/details/storage/blobs), [fájlok](https://azure.microsoft.com/pricing/details/storage/files/), [várólisták](https://azure.microsoft.com/pricing/details/storage/queues/)és [táblák](https://azure.microsoft.com/pricing/details/storage/tables/)GZRS előzetes verziójának díjszabását.

> [!IMPORTANT]
> A Microsoft az előzetes verziójú funkciók használatát javasolja az éles számítási feladatokhoz.

## <a name="how-gzrs-and-ra-gzrs-work"></a>A GZRS és az RA-GZRS működése

Ha az GZRS vagy RA-GZRS engedélyezett Storage-fiókba írja az adatírást, a rendszer az elsődleges régióban szinkron módon replikálja az adategységeket az első három rendelkezésre állási zónában. A rendszer aszinkron módon replikálja az adatmennyiséget egy második, több száz mérfölddel távolabbi régióba. Ha az adatbevitel a másodlagos régióba történik, a [helyileg redundáns tárolás (LRS)](storage-redundancy-lrs.md)használatával a régión belül szinkron módon háromszor is replikálható.

> [!IMPORTANT]
> Az aszinkron replikáció az adatírások és az elsődleges régió közötti késleltetés, valamint a másodlagos régióba való replikálás időpontját jelenti. Regionális katasztrófa esetén előfordulhat, hogy a másodlagos régióba még nem replikált módosítások elvesznek, ha az adatok nem állíthatók helyre az elsődleges régióból.

A Storage-fiók létrehozásakor meg kell adnia, hogy a rendszer hogyan replikálja az adott fiókban lévő adatkészleteket, és megadja az adott fiók elsődleges régióját is. Egy földrajzilag replikált fiók párosított másodlagos régiója az elsődleges régió alapján van meghatározva, és nem módosítható. Az Azure által támogatott régiók naprakész információit az üzletmenet folytonossága és a [vész-helyreállítási (BCDR) című témakörben tekintheti meg: Azure párosított régiók](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). A Storage-fiókok GZRS vagy RA-GZRS használatával történő létrehozásával kapcsolatos információkért lásd: [Storage-fiók létrehozása](storage-quickstart-create-account.md).

### <a name="use-ra-gzrs-for-high-availability"></a>Az RA-GZRS használata magas rendelkezésre álláshoz

Ha engedélyezi az RA-GZRS-t a Storage-fiókjához, az adatok beolvashatók a másodlagos végpontból, valamint a Storage-fiók elsődleges végpontján. A másodlagos végpont hozzáfűzi az utótagot *– a másodlagos* nevet a fiók nevéhez. Ha például az blob Service `myaccount.blob.core.windows.net`elsődleges végpontja, akkor a másodlagos `myaccount-secondary.blob.core.windows.net`végpont. A Storage-fiókhoz tartozó hozzáférési kulcsok mind az elsődleges, mind a másodlagos végpont esetében azonosak.

Ha regionális kimaradás esetén szeretné kihasználni az RA-GZRS-t, előre meg kell terveznie az alkalmazást, hogy kezelni tudja ezt a forgatókönyvet. Az alkalmazásnak az elsődleges végpontra kell olvasnia és írnia, de a másodlagos végpont használatára kell váltania abban az esetben, ha az elsődleges régió elérhetetlenné válik. Az RA-GZRS használatával történő magas rendelkezésre állás kialakításával kapcsolatos útmutatásért lásd: magas rendelkezésre állású [Alkalmazások tervezése ra-GZRS vagy ra-GRS](https://docs.microsoft.com/en-us/azure/storage/common/storage-designing-ha-apps-with-ragrs).

Mivel a rendszer aszinkron módon replikálja az adatfájlokat a másodlagos régióba, a másodlagos régió gyakran az elsődleges régió mögött van. Annak megállapításához, hogy mely írási műveletek lettek replikálva a másodlagos régióba, az alkalmazás ellenőrizze a Storage-fiók utolsó szinkronizálásának idejét. Az elsődleges régióba az utolsó szinkronizálás időpontja előtt írt összes írási műveletet sikeresen replikálták a másodlagos régióba, ami azt jelenti, hogy elérhetők a másodlagos helyről való olvasáshoz. Az elsődleges régióba az utolsó szinkronizálás időpontja után írt írási műveletek nem replikálódtak a másodlagos régióra, ami azt jelenti, hogy nem lesznek elérhetők olvasási műveletekhez.

A **Legutóbbi szinkronizálási idő** tulajdonság értékét a Azure PowerShell, az Azure CLI vagy az Azure Storage ügyféloldali kódtárai használatával kérdezheti le. Az **utolsó szinkronizálás időpontja** tulajdonság egy GMT dátum/idő érték.

Az RA-GZRS teljesítményével és méretezhetőségével kapcsolatos további útmutatásért tekintse meg a [Microsoft Azure Storage teljesítmény-és méretezhetőségi ellenőrzőlistát](storage-performance-checklist.md).

### <a name="availability-zone-outages"></a>Rendelkezésre állási zóna kimaradásai

Abban az esetben, ha az elsődleges régióban egy rendelkezésre állási zónát érintő hiba történt, az alkalmazások zökkenőmentesen továbbra is olvashatók és írhatók a Storage-fiókba az adott régió egyéb rendelkezésre állási zónái alapján. A Microsoft azt javasolja, hogy a GZRS vagy a ZRS használatakor folytassa az átmeneti hibák kezelésére vonatkozó eljárások követését. Ezek a gyakorlatok az újrapróbálkozási szabályzatok exponenciális biztonsági mentéssel történő megvalósítását foglalják magukban.

Ha egy rendelkezésre állási zóna elérhetetlenné válik, az Azure hálózati frissítéseket végez, például a DNS-re. Ezek a frissítések hatással lehetnek az alkalmazásra, ha a frissítések befejeződése előtt fér hozzá az adatokhoz.

### <a name="regional-outages"></a>Regionális kimaradások

Ha a hiba a teljes elsődleges régióra hatással van, akkor a Microsoft először megkísérli visszaállítani az elsődleges régiót. Ha a visszaállítás nem lehetséges, a Microsoft feladatátvételt hajt végre a másodlagos régióba, így a másodlagos régió lesz az új elsődleges régió. Ha a Storage-fiókhoz engedélyezve van az RA-GZRS, akkor a forgatókönyvhöz tervezett alkalmazások a másodlagos régióból is beolvashatók a feladatátvételre való várakozás közben. Ha a Storage-fióknak nincs engedélyezve az RA-GZRS, akkor az alkalmazások nem lesznek képesek a másodlagosról olvasni, amíg a feladatátvétel be nem fejeződik.

> [!NOTE]
> A GZRS és az RA-GZRS jelenleg csak előzetes verzióban érhető el az USA 2. keleti régiójában. A felhasználó által felügyelt fiók feladatátvétele (előzetes verzió) még nem érhető el az USA 2. keleti régiójában, így az ügyfelek jelenleg nem kezelhetik a GZRS és az RA-GZRS fiókkal rendelkező fiók feladatátvételi eseményeit. Az előzetes verzióban a Microsoft minden olyan feladatátvételi eseményt kezel, amely hatással van a GZRS és az RA-GZRS fiókokra.

Mivel az adatreplikáció a másodlagos régióba aszinkron módon történik, az elsődleges régiót érintő hibák adatvesztést okozhatnak, ha az elsődleges régió nem állítható helyre. Az elsődleges régióba irányuló legutóbbi írások és a másodlagos régió utolsó írása közötti intervallum a helyreállítási pont célkitűzése (RPO). A RPO azt az időpontot jelöli, ameddig az Adathelyreállítás visszaállítható. Az Azure Storage szolgáltatásnak jellemzően 15 percnél rövidebb RPO van, bár jelenleg nem áll rendelkezésre SLA, hogy mennyi ideig tart az adatreplikálás a másodlagos régióba.

A helyreállítási időre vonatkozó célkitűzés (RTO) azt méri, hogy mennyi ideig tart a feladatátvétel végrehajtása, és a Storage-fiók online állapotba kerül. Ez a mérték azt jelzi, hogy az Azure milyen időt igényel a feladatátvétel végrehajtásához, ha módosítja az elsődleges DNS-bejegyzéseket, hogy azok a másodlagos helyre mutassanak.

## <a name="migrate-a-storage-account-to-gzrs-or-ra-gzrs"></a>Storage-fiók migrálása GZRS vagy RA-GZRS

Bármely meglévő Storage-fiókot áttelepíthet GZRS vagy RA-GZRS. A meglévő ZRS-fiókról GZRS vagy RA-GZRS-re való Migrálás egyszerű, miközben az áttelepítés egy LRS, GRS vagy RA-GRS-fiókból történik. Az alábbi szakaszok azt ismertetik, hogyan lehet az áttelepítést mindkét esetben.

### <a name="migrating-from-a-zrs-account"></a>Áttelepítés ZRS-fiókból

Meglévő ZRS-fiók RA-GZRS alakításához használja a [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) parancsmagot a fiók SKU-jának módosításához. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -SkuName "Standard_RAGZRS"
```

### <a name="migrating-from-an-lrs-grs-or-ra-grs-account"></a>Áttelepítés LRS, GRS vagy RA-GRS fiókból

A GZRS vagy RA-GZRS két lehetőség közül választhat a LRS, GRS vagy RA-GRS fiókból:

- Egy meglévő fiókból manuálisan másolhatja vagy áthelyezheti az adatait egy új GZRS vagy RA-GZRS fiókba.
- Élő áttelepítést is igényelhet.

#### <a name="perform-a-manual-migration"></a>Manuális áttelepítés végrehajtása

Ha az áttelepítést egy adott dátummal kell végrehajtania, érdemes lehet manuális áttelepítést végeznie. A manuális áttelepítés nagyobb rugalmasságot biztosít, mint az élő áttelepítés. A manuális áttelepítéssel szabályozhatja az időzítést.

Ha egy meglévő fiókból manuálisan szeretné áttelepíteni az adatait egy GZRS vagy RA-GZRS-fiókba, használjon olyan eszközt, amely képes az adatok hatékony másolására. Néhány példa:

- Használjon olyan segédprogramot, mint a AzCopy vagy egy megbízható, harmadik féltől származó eszköz. További információ a AzCopy: Ismerkedés [a AzCopy](storage-use-azcopy-v10.md)szolgáltatással.
- Ha már ismeri a Hadoop vagy a HDInsight, csatolja a forrás és a cél Storage-fiókot a fürthöz. Ezután integrálással az adatmásolási folyamatot egy olyan eszközzel, mint a DistCp.
- Saját eszközkészletet hozhat létre az egyik Azure Storage ügyféloldali kódtár használatával.

#### <a name="perform-a-live-migration"></a>Élő áttelepítés végrehajtása

A manuális áttelepítés az alkalmazás leállását eredményezheti. Ha az alkalmazás magas rendelkezésre állást igényel, a Microsoft élő áttelepítési lehetőséget is biztosít. Az élő áttelepítés egy helyben történő áttelepítés állásidő nélkül.

Az élő áttelepítés során használhatja a Storage-fiókot, miközben az adatait áttelepíti a forrás-és a cél Storage-fiókok között. Az élő áttelepítési folyamat során a fiókja továbbra is teljesíti az SLA-t a tartósság és a rendelkezésre állás érdekében. Az élő áttelepítés nem okoz állásidőt vagy adatvesztést.

Csak az általános célú v2-fiókok támogatják a GZRS/RA-GZRS, ezért az élő áttelepítésre vonatkozó kérelem elküldése előtt a GZRS/RA-GZRS-re kell frissítenie a fiókját az általános célú v2-re. További információ: [Azure Storage-fiók áttekintése](https://docs.microsoft.com/azure/storage/common/storage-account-overview) és [frissítés egy általános célú v2 Storage-fiókra](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).

Az áttelepítés befejezése után a rendszer frissíti a Storage-fiók replikálási beállítását a **geo-Zone-redundáns tárolóra (GZRS)** vagy az **olvasási hozzáférésű földrajzi zónára redundáns tárterületre (ra-GZRS)** . A szolgáltatási végpontok, a hozzáférési kulcsok, a közös hozzáférésű aláírások (SAS) és az egyéb fiókok konfigurációs beállításai változatlanok maradnak, és érintetlenek maradnak.

Tartsa szem előtt az alábbi korlátozásokat az élő áttelepítéssel kapcsolatban:

- Bár a Microsoft azonnal kezeli az élő migrálási kérelmet, a migrálás befejezésének időpontjára nem vállal garanciát. Ha szüksége van az adatok GZRS vagy RA-GZRS való áttelepítésére egy adott napon keresztül, a Microsoft azt javasolja, hogy ehelyett manuális áttelepítést végezzen. Minél több adat található a fiókban, általában annál tovább tart az adatok migrálása.
- A fióknak adatait kell tartalmaznia.
- Az adatátvitelt csak ugyanazon a régión belül végezheti el.
- Csak a standard szintű Storage-fiókok támogatják az élő áttelepítést. A Premium Storage-fiókokat manuálisan kell áttelepíteni.
- Az élő áttelepítés GZRS vagy RA-GZRS-fiókból LRS, GRS vagy RA-GRS fiókra nem támogatott. Az új vagy meglévő Storage-fiókba manuálisan kell áthelyeznie az adatátvitelt.
- Az RA-GRS élő áttelepítést igényelhet az RA-GZRS. Az RA-GRS-ről a GZRS-re való Migrálás azonban nem támogatott. Ebben az esetben élő áttelepítést kell kérnie RA-GZRS, majd manuálisan át kell alakítania a Storage-fiókot a GZRS használatára.
- A Managed Disks csak a LRS támogatja, és nem telepíthető át GZRS vagy RA-GZRS-re. A rendelkezésre állási csoportokkal való integrációval kapcsolatban lásd: [Bevezetés az Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets)szolgáltatásba.
- A standard SSD-Managed Disks pillanatképeit és lemezképeit standard HDD tárolóban tárolhatja [, és választhat a LRS, a ZRS, a GZRS és az ra-GZRS lehetőségek közül](https://azure.microsoft.com/pricing/details/managed-disks/).
- A nagyméretű fájlmegosztást tartalmazó fiókok GZRS nem támogatottak.

Élő áttelepítés igényléséhez használja a [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). A portálon válassza ki a GZRS vagy RA-GZRS áttelepíteni kívánt Storage-fiókot, és kövesse az alábbi utasításokat:

1. Válassza az **új támogatási kérelem**lehetőséget.
2. A fiókadatok alapján végezze el az **alapvető** tudnivalókat. A **szolgáltatás** szakaszban válassza a **Storage-fiókok kezelése** lehetőséget, és adja meg az áttelepíteni kívánt fiókot.
3. Válassza a **tovább**lehetőséget.
4. A **probléma** szakasz a következő értékeket határozza meg:
    - **Súlyosság**: Hagyja meg az alapértelmezett értéket.
    - **Probléma típusa**: Válassza **Az adatáttelepítés**lehetőséget.
    - **Kategória**: Válassza az **áttelepítés (ra-) GZRS egy régión belül**lehetőséget.
    - **Cím**: Írjon be egy leíró címet, például: **(ra-) GZRS fiók áttelepítése**.
    - **Részletek**: Írja be a további részleteket a **részletek** mezőbe, például: "szeretnék áttérni a GZRS a (z) [LRS, GRS] \_ helyről a \_ régióban." vagy "szeretnék migrálni az ra-GZRS-re a \_ \_ (z) [LRS, ra-GRS] régióban."
5. Válassza a **tovább**lehetőséget.
6. A kapcsolattartási **adatok** panelen ellenőrizze, hogy helyesek-e a kapcsolattartási adatok.
7. Válassza a **Létrehozás**lehetőséget.

A támogatási képviselő felveszi Önnel a kapcsolatot a segítség biztosításához.

## <a name="see-also"></a>Lásd még

- [Azure Storage replication (Azure Storage replikáció)](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
- [Helyileg redundáns tárolás (LRS): Alacsony költséghatékonyságú adatredundancia az Azure Storage szolgáltatásban](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)
- [Zóna – redundáns tárolás (ZRS): Magasan elérhető Azure Storage-alkalmazások](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) 
