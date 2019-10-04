---
title: Hosszú rendelkezésre állású Azure Storage-alkalmazások létrehozása a ZRS-alapú tároláshoz | Microsoft Docs
description: A Zone-redundáns tárolás (ZRS) egyszerű módszert kínál a magasan elérhető alkalmazások kiépítésére. A ZRS megvédi az adatközpontban fellépő hardveres hibákat, és bizonyos regionális katasztrófák ellen.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: a343601ec126549926cfd4035d901862c0a585a8
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673097"
---
# <a name="zone-redundant-storage-zrs-for-building-highly-available-azure-storage-applications"></a>Zone-redundáns tárolás (ZRS) a magasan elérhető Azure Storage-alkalmazások létrehozásához

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>A lefedettség és a regionális rendelkezésre állás támogatása

A ZRS jelenleg a standard általános célú v2-és FileStorage-típusok használatát támogatja. További információ a Storage-fiókok típusairól: az [Azure Storage-fiók áttekintése](storage-account-overview.md).

A ZRS a Blobok, a nem lemezes Blobok, a fájlok, a táblák és a várólisták számára érhető el.

Az általános célú v2-fiókok esetében a ZRS általánosan elérhető a következő régiókban:

- Délkelet-Ázsia
- Nyugat-Európa
- Észak-Európa
- Közép-Franciaország
- Kelet-Japán
- Az Egyesült Királyság déli régiója
- USA középső régiója
- USA keleti régiója
- USA 2. keleti régiója
- USA 2. nyugati régiója

A FileStorage-fiókok esetében a ZRS általánosan elérhető a következő régiókban:

- Nyugat-Európa

A Microsoft továbbra is engedélyezi a ZRS további Azure-régiókban. Az új régiókkal kapcsolatos információkért olvassa el rendszeresen az [Azure szolgáltatás frissítéseinek](https://azure.microsoft.com/updates/) oldalát.

**Ismert korlátozások**

- Az archiválási szint jelenleg nem támogatott a ZRS-fiókokon. További részletekért tekintse meg az [Azure Blob Storage: gyakori, ritka elérésű és archív hozzáférési szintet](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) .
- A felügyelt lemezek nem támogatják a ZRS. A standard SSD-Managed Disks pillanatképeit és lemezképeit standard HDD tárolóban tárolhatja, és [választhat a LRS és a ZRS lehetőségek](https://azure.microsoft.com/pricing/details/managed-disks/)közül.

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>Mi történik, ha egy zóna elérhetetlenné válik?

Az adatok továbbra is elérhetők olvasási és írási műveletekhez, még akkor is, ha egy zóna elérhetetlenné válik. A Microsoft azt javasolja, hogy folytassa az átmeneti hibák kezelésére szolgáló eljárások követésével. Ezek a gyakorlatok az újrapróbálkozási szabályzatok exponenciális biztonsági mentéssel történő megvalósítását foglalják magukban.

Ha egy zóna nem érhető el, az Azure hálózati frissítéseket végez, például a DNS-átállítást. Ezek a frissítések hatással lehetnek az alkalmazásra, ha a frissítések befejeződése előtt hozzáfér az adataihoz.

Előfordulhat, hogy a ZRS nem nyújt védelmet olyan regionális katasztrófák ellen, ahol a rendszer több zónát tartósan érint. Ehelyett a ZRS rugalmasságot biztosít az adatai számára, ha átmenetileg elérhetetlenné válik. A regionális katasztrófák elleni védelem érdekében a Microsoft a Geo-redundáns tárolást (GRS) javasolja. A GRS kapcsolatos további információkért lásd [: Geo-redundáns tárolás (GRS): Régiók közötti replikáció az Azure Storage](storage-redundancy-grs.md)-ban.

## <a name="converting-to-zrs-replication"></a>Átalakítás ZRS replikálásra

A LRS, GRS és RA-GRS-re való Migrálás egyszerű. A fiók redundancia-típusának módosításához használja a Azure Portal vagy a Storage erőforrás-szolgáltató API-ját. Az Azure ezeket az adatait ennek megfelelően replikálja. 

Az ZRS-re történő áttelepítéshez egy másik stratégia szükséges. A ZRS migrálása magában foglalja az adatok fizikai áthelyezését egyetlen tárolási bélyegzőből egy adott régióban található több bélyegre.

Két elsődleges lehetőség van a ZRS való áttelepítésre: 

- Az adatokat átmásolhatja vagy áthelyezheti manuálisan egy új ZRS-fiókba a meglévő fiókból.
- Igényeljen élő migrálást.

> [!IMPORTANT]
> A prémium szintű fájlmegosztás jelenleg nem támogatja az élő áttelepítést. Az adatok manuális másolása és áthelyezése jelenleg nem támogatott.

Ha egy adott időpontig szükséges a migrálás, fontolja meg manuális migrálás végrehajtását. A manuális migrálás az élő migrálásnál nagyobb rugalmasságot biztosít. A manuális migrálás során Ön szabja meg az időzítést.

Manuális áttelepítés végrehajtásához a következő lehetőségek közül választhat:
- Olyan meglévő eszközöket használhat, mint például a AzCopy, az egyik Azure Storage ügyféloldali kódtára vagy a megbízható külső eszközök.
- Ha már ismeri a Hadoop vagy a HDInsight, csatolja a forrás és a cél (ZRS) fiókot a fürthöz. Ezután integrálással az adatmásolási folyamatot egy olyan eszközzel, mint a DistCp.
- Saját eszközkészletet hozhat létre az egyik Azure Storage ügyféloldali kódtár használatával.

A manuális áttelepítés az alkalmazás leállását eredményezheti. Ha az alkalmazás magas rendelkezésre állást igényel, a Microsoft élő migrálási lehetőséget is biztosít. Az élő migrálás egy állásidő nélküli, helyszíni migrálás. 

Az élő migrálás során használhatja a tárfiókját, miközben az adatok a forrásról a cél tárolási bélyegre való migrálása zajlik. A migrálási folyamat során a tartós állapot és a rendelkezésreállási SLA szintje a szokásossal megegyező.

Tartsa szem előtt az alábbi korlátozásokat az élő áttelepítéssel kapcsolatban:

- Bár a Microsoft azonnal kezeli az élő migrálási kérelmet, a migrálás befejezésének időpontjára nem vállal garanciát. Ha egy adott időpontig szükséges az adatok ZRS-re való migrálása, a Microsoft a manuális migrálás végrehajtását javasolja. Minél több adat található a fiókban, általában annál tovább tart az adatok migrálása. 
- Az élő áttelepítés csak LRS vagy GRS replikálást használó Storage-fiókok esetén támogatott. Ha a fiókja RA-GRS-t használ, előbb módosítania kell a fiók replikálási típusát a LRS vagy a GRS értékre a továbblépés előtt. Ez a közbenső lépés eltávolítja az RA-GRS által az áttelepítés előtt megadott másodlagos írásvédett végpontot.
- A fióknak adatait kell tartalmaznia.
- Az adatátvitelt csak ugyanazon a régión belül végezheti el. Ha olyan ZRS-fiókba kívánja áttelepíteni az adatait, amely eltér a forrásoldali fióktól, akkor manuális áttelepítést kell végeznie.
- Csak a standard szintű Storage-fiókok támogatják az élő áttelepítést. A Premium Storage-fiókokat manuálisan kell áttelepíteni.
- A ZRS-ről LRS, GRS vagy RA-GRS-re való élő áttelepítés nem támogatott. Az új vagy meglévő Storage-fiókba manuálisan kell áthelyeznie az adatátvitelt.
- A felügyelt lemezek csak a LRS számára érhetők el, és nem telepíthetők át a ZRS. A standard SSD-Managed Disks pillanatképeit és lemezképeit standard HDD tárolóban tárolhatja, és [választhat a LRS és a ZRS lehetőségek](https://azure.microsoft.com/pricing/details/managed-disks/)közül. A rendelkezésre állási csoportokkal való integrációról az [Azure Managed Disks bemutatása](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets)című témakörben olvashat.
- Az archív adatokkal rendelkező LRS-vagy GRS-fiókok nem telepíthetők át a ZRS.

Az [Azure-támogatási portálon](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)keresztül is kérhet élő áttelepítést. A portálon válassza ki azt a Storage-fiókot, amelyet ZRS szeretne alakítani.
1. **Új támogatási kérelem** kiválasztása
2. A fiókadatok alapján végezze el az **alapvető** tudnivalókat. A **szolgáltatás** szakaszban válassza a **Storage-fiókok kezelése** elemet, majd a ZRS konvertálni kívánt erőforrást. 
3. Kattintson a **Tovább** gombra. 
4. A **probléma** szakasz a következő értékeket határozza meg: 
    - **Súlyosság**: Hagyja meg az alapértelmezett értéket.
    - **Probléma típusa**: Válassza **Az adatáttelepítés**lehetőséget.
    - **Kategória**: Válassza **az áttelepítés a ZRS**lehetőséget.
    - **Cím**: Írjon be egy leíró címet, például: ZRS- **fiók áttelepítése**.
    - **Részletek**: Írja be a **részletek** mezőbe a további részleteket, például a következőt: szeretnék áttérni a ZRS a (z) [LRS, GRS] webhelyről a \_ @ no__t-2 régióban. 
5. Kattintson a **Tovább** gombra.
6. A kapcsolattartási **adatok** panelen ellenőrizze, hogy helyesek-e a kapcsolattartási adatok.
7. Kattintson a **Létrehozás** gombra.

A támogatási személy felveszi Önnel a kapcsolatot, és segítséget nyújt a szükséges segítségért.

## <a name="live-migration-to-zrs-faq"></a>Élő áttelepítés ZRS – gyakori kérdések

**Tervezem az áttelepítés során felmerülő állásidőt?**

Az áttelepítés nem okozta állásidőt. Az élő áttelepítés során továbbra is használhatja a Storage-fiókot, miközben az adatokat áttelepíti a forrás-és a cél tárolási bélyegzők között. A migrálási folyamat során a tartós állapot és a rendelkezésreállási SLA szintje a szokásossal megegyező.

**Az áttelepítéssel kapcsolatos adatvesztés történt?**

Nincs adatvesztés társítva az áttelepítéshez. A migrálási folyamat során a tartós állapot és a rendelkezésreállási SLA szintje a szokásossal megegyező.

**Az alkalmazás (ok) hoz szükséges frissítések az áttelepítés befejeződése után?**

Az áttelepítés befejezése után a fiók (ok) replikációs típusa a "Zone-redundáns Storage (ZRS)" értékre változik. A szolgáltatási végpontok, a hozzáférési kulcsok, az SAS és az egyéb fiókok konfigurációs beállításai változatlanok maradnak, és érintetlenek maradnak.

**Igényelhetek élő áttelepítést az általános célú v1-fiók (ok) ZRS?**

A ZRS csak az általános célú v2-fiókokat támogatja, ezért mielőtt beküld egy élő áttelepítésre vonatkozó kérést a ZRS, ügyeljen arra, hogy a fiók (oka) t az általános célú v2-re frissítse. További részletekért tekintse meg az [Azure Storage-fiók áttekintése](https://docs.microsoft.com/azure/storage/common/storage-account-overview) című témakört, és [frissítsen egy általános célú v2 Storage-fiókra](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) .

**Megkérhetem az olvasási hozzáférésű geo-redundáns tárolási (RA-GRS) fiók (ok) ZRS való élő áttelepítését?**

Mielőtt elküld egy élő áttelepítésre vonatkozó kérelmet a ZRS, győződjön meg arról, hogy az alkalmazás (ok) vagy a munkaterhelések már nem igényelnek hozzáférést a másodlagos írásvédett végponthoz, és módosítsa a Storage-fiók (ok) replikációs típusát a Geo-redundáns tárolóra (GRS). További részletekért lásd a [replikációs stratégia módosítása](https://docs.microsoft.com/azure/storage/common/storage-redundancy#changing-replication-strategy) című témakört.

**Kérhetek élő áttelepítést a Storage-fiókom (k) ZRS egy másik régióba?**

Ha olyan ZRS-fiókba kívánja áttelepíteni az adatait, amely eltér a forrás fiók régiójától, akkor manuális áttelepítést kell végeznie.

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>Klasszikus ZRS: Egy örökölt lehetőség a Blobok redundancia érdekében
> [!NOTE]
> A Microsoft a ZRS klasszikus fiókjait a 2021. március 31-én fogja érvényteleníteni és áttelepíteni. További részletek a klasszikus ügyfelek ZRS az elavultság előtt. 
>
> Ha a ZRS [általánosan elérhetővé](#support-coverage-and-regional-availability) válik egy régióban, az ügyfelek nem tudják létrehozni a klasszikus ZRS-fiókokat az adott régióban található portálról. Ha a klasszikus ZRS elavult, a Microsoft PowerShell és az Azure CLI használatával hozhat létre klasszikus ZRS-fiókokat.

A klasszikus ZRS aszinkron módon replikálja az adatközpontokat egy-két régión belül. Előfordulhat, hogy a replikált adatértékek nem állnak rendelkezésre, kivéve, ha a Microsoft feladatátvételt kezdeményez a másodlagosnak. A klasszikus ZRS-fiók nem alakítható át LRS, GRS vagy RA-GRS típusúra vagy onnan. A klasszikus ZRS-fiókok nem támogatják a metrikákat és a naplózást is.

A klasszikus ZRS csak az általános célú v1-(GPv1-) Storage-fiókokban lévő **blokkos Blobok** esetén érhető el. További információ a tárfiókokról: [Az Azure-tárfiókok áttekintése](storage-account-overview.md).

A ZRS-fiók adatainak manuális áttelepítéséhez egy LRS, a klasszikus, a GRS vagy az RA-GRS fiókból a következő eszközök egyikét használja: AzCopy, Azure Storage Explorer, Azure PowerShell vagy Azure CLI. Saját áttelepítési megoldást is létrehozhat az Azure Storage ügyféloldali kódtárainak egyikével.

A ZRS klasszikus fiók (ok) ZRS is frissítheti a portálon, vagy Azure PowerShell vagy Azure CLI-t használhat azokban a régiókban, ahol a ZRS elérhető. A Azure Portal ZRS való frissítéséhez navigáljon a fiók **konfiguráció** szakaszára, és válassza a **frissítés**lehetőséget:

![A ZRS klasszikus frissítése ZRS a portálon](media/storage-redundancy-zrs/portal-zrs-classic-upgrade.png)

Ha az ZRS-re szeretne frissíteni a PowerShell-lel, hívja meg a következő parancsot:
```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

Ha a CLI-vel szeretne frissíteni a ZRS, hívja meg a következő parancsot:
```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

## <a name="see-also"></a>Lásd még
- [Azure Storage replication (Azure Storage replikáció)](storage-redundancy.md)
- [Helyileg redundáns tárolás (LRS): Alacsony költséghatékonyságú adatredundancia az Azure Storage szolgáltatásban](storage-redundancy-lrs.md)
- [Geo-redundáns tárolás (GRS): Régiók közötti replikáció az Azure Storage-ban](storage-redundancy-grs.md)
