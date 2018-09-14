---
title: Zónaredundáns tárolás (ZRS) a magas rendelkezésre állású Azure Storage-alkalmazások |} A Microsoft Docs
description: Zónaredundáns tárolás (ZRS) a magas rendelkezésre állású alkalmazásokat hozhat létre egyszerű módszert kínál. A ZRS szemben az adatközponton belül, és a egy regionális csapásoktól védi.
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 03/20/2018
ms.author: jeking
ms.component: common
ms.openlocfilehash: dfd5058b17a211adf97eb8729158b2b65d7b6975
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45579513"
---
# <a name="zone-redundant-storage-zrs-highly-available-azure-storage-applications"></a>Zónaredundáns tárolás (ZRS): az Azure Storage magas rendelkezésre állású alkalmazások
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>Támogatást és a régiónkénti rendelkezésre állás
A ZRS jelenleg támogatja a standard [általános célú v2 (GPv2)](storage-account-options.md#general-purpose-v2-accounts) fióktípus. A ZRS blokkblobok, lapblobok nem lemez, fájlok, táblák és üzenetsorok érhető el. Ezenkívül az összes, a [Storage Analytics](storage-analytics.md) naplók és [Storage Metrics](storage-enable-and-view-metrics.md)

A ZRS az alábbi régiókban általában érhető el:

- USA keleti régiója
- USA 2. keleti régiója
- USA 2. nyugati régiója
- USA középső régiója
- Észak-Európa
- Nyugat-Európa
- Közép-Franciaország
- Délkelet-Ázsia

A Microsoft továbbra is fennáll, további Azure-régióban ZRS engedélyezéséhez. Ellenőrizze a [Azure szolgáltatási hírek](https://azure.microsoft.com/updates/) oldalon rendszeresen új régiókra vonatkozó információkat.

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>Mi történik, amikor egy zóna nem érhető el?
Az adatok továbbra is rugalmas, ha a zóna nem érhető el. A Microsoft azt javasolja, hogy folytatja-e az átmeneti hibák kezelése, például az exponenciális visszatartási újrapróbálkozási szabályzatok implementálása eljárások követése. A zóna nem érhető el, ha Azure vállalja, hogy hálózati frissítések, például a DNS-repointing. Ezek a frissítések hatással lehet az alkalmazás, mielőtt végzi az adatok eléréséhez.

A ZRS előfordulhat, hogy nem védelem érdekében a regionális katasztrófa, ahol több zónában véglegesen érintett. Ehelyett ZRS kínálja az adatok rugalmassága az ideiglenesen elérhetetlenné válik. A regionális katasztrófák elleni védelem a Microsoft azt javasolja, georedundáns tárolás (GRS) használatával. GRS kapcsolatos további információkért lásd: [georedundáns tárolás (GRS): az Azure Storage-régiók közti replikációs](storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>A ZRS-replikációval alakítása
Még ma használhatja az Azure Portalon vagy a Storage Resource Provider API módosítani a fiók a redundancia típusát, mindaddig, amíg végzi az áttelepítést, vagy az LRS, GRS és RA-GRS. A zrs-t migrálás viszont nem egyértelmű mert magában foglalja a fizikai adatmozgás egy egyetlen tárolási blokkból egy adott régión belül több stamp. 

Két elsődleges lehetősége van az áttelepítéshez, illetve a zrs-t. Saját kezűleg bemásolhatja vagy a meglévő fiókot egy új ZRS-fiókok adatáthelyezést. Az élő áttelepítés is kérheti. A Microsoft javasolja, hogy manuális áttelepítést hajtson végre, mert nem garantált, hogy ha befejezi az élő áttelepítés. Manuális áttelepítés útvonal rugalmasabb, mint az élő áttelepítés nem, és Ön rendelkezik az áttelepítés ütemezését felett biztosít.

A manuális áttelepítéshez, a különböző lehetőségek állnak rendelkezésére:
- Használja a meglévő eszközök például AzCopy, a storage SDK-t, megbízható harmadik féltől származó eszközökkel, és így tovább.
- Ha ismeri, a Hadoop és a HDInsight, csatolhat a szolgáltatáskéréshez mindkét forrás és cél (ZRS) és a fürt fiókját, és nagymértékben párhuzamosíthatja az másolási folyamat a DistCp hasonló használatával
- A Storage SDK-t egy íz kihasználva a saját eszközök létrehozása

Ha azonban manuális áttelepítést néhány alkalmazás állásidőt eredményez, és nem elháríthatók, amely a végén, a Microsoft az élő áttelepítés lehetőséget kínál. Az élő áttelepítés, amely lehetővé teszi, hogy továbbra is egy meglévő tárfiókot használni, amíg az adatok között a forrás- és tárolási stampek telepít át egy helyszíni áttelepítés. Az áttelepítés során továbbra is kell azonos szintű tartósságot és rendelkezésre állási SLA mint normál esetben Önnek.

Az élő áttelepítés kapható bizonyos korlátozások, azonban. Azok az alábbiakban láthatók.

- Közben Microsoft azonnal foglalkozni fog az élő áttelepítési kérelem, nincs garancia arra, hogy ha befejezi az áttelepítést. Ha az adatok a zrs-t egy bizonyos időpontig van szüksége, akkor manuális áttelepítést érdemes megtenni. Általánosságban elmondható, minél több adat van a fiókjában a fog tartani, hogy az adatok áttelepítéséhez. 
- Előfordulhat, hogy csak akkor végezzen az élő áttelepítés egy olyan fiók LRS vagy GRS-replikáció használatával. Ha fiókját használja az RA-GRS, majd meg kell először migrál egy replikációs típust a folytatás előtt. Ez a köztes lépés biztosítja, hogy a másodlagos csak olvasható végpontot, amely az RA-GRS biztosítja a rendszer eltávolítja az áttelepítés előtt.
- A fiók adatokat kell tartalmaznia.
- Csak intra-régió áttelepítéseket támogatja. Ha szeretne migrálni, mint a forrás-fiók más régióban található ZRS-fiókok adatait, végre kell hajtania manuális áttelepítést.
- Csak a standard szintű storage fióktípus esetében támogatottak. A premium storage-fiók nem telepíthetők át.

Élő áttelepítés kérelmek nyissa meg az Azure-támogatás portálon keresztül. A portálról válassza ki a zrs-t az átalakítani kívánt tárfiókot.
1. Kattintson a **új támogatási kérelem**
2. Ellenőrizze az alapokat. Kattintson a **Tovább** gombra. 
3. Az a **probléma** szakaszban 
    - Hagyja üresen, súlyosság – van.
    - Probléma típusa = **adatok Migrálása**
    - Kategória = **áttelepítése a zrs-t egy adott régión belül**
    - Cím = **ZRS fiók áttelepítési** (vagy valami leíró)
    - Részletek = szeretnék kapni, ZRS áttelepítésére [LRS, GRS] ___ a régióban. 
4. Kattintson a **Tovább** gombra.
5. Ellenőrizze, hogy helyes-e a kapcsolattartási adatok panelen a kapcsolattartási adatok.
6. Kattintson a **elküldése**.

A támogatási szolgálatnak lesz felvesszük Önnel a kapcsolatot. Adott személy elérhető bármely segítségnyújtási lehet szükség lesz. 

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>A klasszikus ZRS: Egy örökölt beállítása block blobs redundancia
> [!NOTE]
> A Microsoft kivezetjük, és a migrálása a klasszikus ZRS-fiókok 2021. március 31-ig. További részleteket nyújtanak a klasszikus ZRS-vevőknek elévülése előtt. 
>
> A ZRS válik [általánosan elérhető](#support-coverage-and-regional-availability) egy régióban ügyfelek már nem tudja a klasszikus ZRS-fiókok létrehozása a portálról, az adott régióban. A Microsoft PowerShell és az Azure CLI használatával a klasszikus ZRS-fiókok létrehozásához támogatott mindaddig, amíg a klasszikus ZRS-elavult.

A klasszikus ZRS-aszinkron módon replikálja az adatközpontokon belül egy vagy két régió. Lehetséges, hogy a replika nem lesz elérhető, hacsak a Microsoft nem kezdeményezi a másodlagos példány feladatátvételét. A klasszikus ZRS-áll rendelkezésre, csak a **blokkblobok** a [általános célú V1 (GPv1)](storage-account-options.md#general-purpose-v1-accounts) storage-fiókok. A klasszikus ZRS-fiókokat nem lehet átalakítani LRS- vagy GRS-fiókokká vagy fiókokból, és nem rendelkeznek metrikákkal vagy naplózási képességgel.

A klasszikus ZRS-fiókok nem lehet konvertálni, vagy onnan LRS, GRS vagy RA-GRS. A klasszikus ZRS-fiókok is nem támogatják a metrikákkal vagy naplózási.

A ZRS-fiókja adatait, manuális áttelepítésével kapcsolatban, vagy LRS, klasszikus ZRS, GRS vagy RA-GRS-fiókból, használja az AzCopy, az Azure Storage Explorer, az Azure PowerShell vagy az Azure CLI. A saját migráláshoz az Azure Storage ügyfélkódtáraival egyikét is létrehozható.

## <a name="see-also"></a>Lásd még
- [Azure Storage replication (Azure Storage replikáció)](storage-redundancy.md)
- [Helyileg redundáns tárolás (LRS): az Azure Storage alacsony költségű adatredundancia](storage-redundancy-lrs.md)
- [Georedundáns tárolás (GRS): az Azure Storage-régiók közti replikáció](storage-redundancy-grs.md)