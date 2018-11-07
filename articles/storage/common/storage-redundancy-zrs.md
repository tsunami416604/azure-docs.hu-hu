---
title: Zónaredundáns tárolás (ZRS) a magas rendelkezésre állású Azure Storage-alkalmazások |} A Microsoft Docs
description: Zónaredundáns tárolás (ZRS) a magas rendelkezésre állású alkalmazásokat hozhat létre egyszerű módszert kínál. A ZRS szemben az adatközponton belül, és a egy regionális csapásoktól védi.
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 10/24/2018
ms.author: jeking
ms.component: common
ms.openlocfilehash: b310c06f508395635976009005dd2c4db2917abc
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218738"
---
# <a name="zone-redundant-storage-zrs-highly-available-azure-storage-applications"></a>Zónaredundáns tárolás (ZRS): az Azure Storage magas rendelkezésre állású alkalmazások
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>Támogatást és a régiónkénti rendelkezésre állás
A ZRS jelenleg a standard szintű, általános célú v2 fiókok típusai támogatja. Storage-fióktípusok kapcsolatos további információkért lásd: [az Azure storage-fiók áttekintése](storage-account-overview.md).

A ZRS blokkblobok, lapblobok nem lemez, fájlok, táblák és üzenetsorok érhető el.

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
Az adatok továbbra is érhető el még akkor is, ha a zóna elérhetetlenné válik. A Microsoft azt javasolja, hogy folytatja-e az átmeneti hibák kezeléséhez eljárások követése. Ezen eljárások közé tartozik az exponenciális visszatartási újrapróbálkozási szabályzatok implementálása.

A zóna nem érhető el, ha Azure vállalja, hogy hálózati frissítések, például a DNS-repointing. Ezek a frissítések hatással lehet az alkalmazás az adatok eléréséhez a frissítések végrehajtása előtt.

A ZRS előfordulhat, hogy nem védelem érdekében a regionális katasztrófa, ahol több zónában véglegesen érintett. Ehelyett ZRS kínálja az adatok rugalmassága az ideiglenesen elérhetetlenné válik. A regionális katasztrófák elleni védelem a Microsoft azt javasolja, georedundáns tárolás (GRS) használatával. GRS kapcsolatos további információkért lásd: [georedundáns tárolás (GRS): az Azure Storage-régiók közti replikációs](storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>A ZRS-replikációval alakítása
Áttelepítés vagy onnan LRS, GRS és RA-GRS rendkívül egyszerű. Az Azure portal vagy a Storage Resource Provider API használatával módosíthatja a fiók a redundancia típusát. Az Azure ezután replikálja az adatokat annak megfelelően. 

Adatok áttelepítése, illetve a zrs-t egy másik stratégia van szükség. A ZRS áttelepítésébe tartozik egy adott régión belül több stamp egyetlen tárolóblokk az adatok tényleges mozgatását.

Két elsődleges lehetőség van az áttelepítéshez, illetve a ZRS: 

- Manuálisan másolja, vagy egy meglévő fiókot egy új ZRS-fiókok adatáthelyezést.
- Az élő áttelepítés kérelmet.

A Microsoft javasolja, hogy a manuális áttelepítést hajtson végre. Manuális áttelepítést, mint az élő áttelepítés nagyobb rugalmasságot nyújt. A manuális áttelepítést tartsa kézben a dolgokat ütemezését.

A manuális áttelepítéshez, lehetősége van:
- Az AzCopy, egy Azure Storage ügyfélkódtáraival vagy megbízható harmadik féltől származó eszközökkel, mint a meglévő eszközök használata.
- Ha ismeri, a Hadoop és a HDInsight, a forrás- és cél (ZRS) fiók a fürtön. Az egy eszköz, például a DistCp a másolási folyamat ezután párhuzamosíthatja.
- A saját eszközök használata az Azure Storage ügyfélkódtáraival egyik hozhat létre.

Manuális áttelepítést alkalmazás állásidőt eredményezhet. Az alkalmazás magas rendelkezésre állást igényel, ha a Microsoft az élő áttelepítés lehetőséget is kínál. Az élő áttelepítés egy helyi áttelepítést. 

Az élő áttelepítés során a tárfiók során a forrás- és tárolási stampek között telepít át az adatokat is használhatja. Az áttelepítési folyamat során rendelkezik azonos szintű tartósságot és rendelkezésre állási SLA szerint általában megteheti.

Tartsa szem előtt az élő áttelepítés a következő korlátozásokkal:

- A Microsoft kezeli az élő áttelepítésre vonatkozó kérelem azonnal, amíg nincs garancia arra, hogy ha befejezi az élő áttelepítés. Van szüksége egy bizonyos időpontig ZRS át az adatokat, akkor a Microsoft azt javasolja, hogy inkább hajtson végre manuális áttelepítést. Általánosságban elmondható, minél több adat van a fiókjában, annál tovább tart, hogy az adatok áttelepítéséhez. 
- Az élő áttelepítés csak olyan LRS vagy GRS-replikáció tárfiókok esetén támogatott. Ha a fiókot használja az RA-GRS, majd először módosítani szeretné a fiók replikációs típusának LRS vagy GRS a folytatás előtt. Ez a köztes lépés eltávolítja a másodlagos csak olvasható végpont RA-GRS biztosítja az áttelepítés előtt.
- A fiók adatokat kell tartalmaznia.
- Csak áttelepítheti az adatokat egy régión belül. Ha szeretne migrálni, mint a forrás-fiók más régióban található ZRS-fiókok adatait, végre kell hajtania manuális áttelepítést.
- Csak a standard szintű storage fióktípus esetében támogatja az élő áttelepítés. Premium storage-fiókok manuálisan kell áttelepíteni.

Kérheti az élő áttelepítés révén a [Azure-támogatási portál](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). A portálról válassza ki a tárfiókot, ZRS az átalakítani kívánt.
1. Válassza ki **új támogatási kérelem**
2. Végezze el a **alapjai** a fiókadatok alapján. Az a **szolgáltatás** szakaszban jelölje be **Tárfiók-felügyelet** és a ZRS átalakítása kívánt erőforrást. 
3. Kattintson a **Tovább** gombra. 
4. Adja meg a következő értékeket a **probléma** szakaszban: 
    - **Súlyosság**: hagyja változatlanul az alapértelmezett értéket,-van.
    - **Probléma típusa**: válasszon **adatáttelepítés**.
    - **Kategória**: válasszon **áttelepítése a zrs-t egy adott régión belül**.
    - **Cím**: Adjon meg egy leíró címet, például **ZRS fiók áttelepítési**.
    - **Részletek**: írja be a további részletek a **részletek** mezőbe, például szeretnék áttelepítésére [LRS, GRS] ZRS ___ a régióban. 
5. Kattintson a **Tovább** gombra.
6. Győződjön meg arról, hogy a kapcsolattartási adatokat a megfelelő a **kapcsolattartási adatok** panelen.
7. Kattintson a **Létrehozás** gombra.

Egy támogatási szakember fog kapcsolatba lépni Önnel, és segítséget kell megadni. 

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>A klasszikus ZRS: Egy örökölt beállítása block blobs redundancia
> [!NOTE]
> A Microsoft kivezetjük, és a migrálása a klasszikus ZRS-fiókok 2021. március 31-ig. További részleteket nyújtanak a klasszikus ZRS-vevőknek elévülése előtt. 
>
> Miután ZRS válik [általánosan elérhető](#support-coverage-and-regional-availability) a régiót, az ügyfelek nem tudják a klasszikus ZRS-fiókok létrehozása a portálról, az adott régióban. Lehetőség a Microsoft PowerShell és az Azure CLI használatával a klasszikus ZRS-fiókok létrehozásához, mindaddig, amíg a klasszikus ZRS-elavult.

A klasszikus ZRS-aszinkron módon replikálja az adatközpontokon belül egy vagy két régió. A replikált adatokat nem lehet elérhető, kivéve, ha a Microsoft kezdeményezi a másodlagos. A klasszikus ZRS-fiókokat nem lehet konvertálni, vagy onnan LRS, GRS vagy RA-GRS. A klasszikus ZRS-fiókok is nem támogatják a metrikák és naplózás.

A klasszikus ZRS-áll rendelkezésre, csak a **blokkblobok** az általános célú V1 (GPv1) tárfiókok. További információ a tárfiókokról: [Az Azure-tárfiókok áttekintése](storage-account-overview.md).

A ZRS-fiókja adatait, manuális áttelepítésével kapcsolatban, vagy LRS, klasszikus ZRS, GRS vagy RA-GRS-fiókból, használja a következő eszközök egyikét: AzCopy, az Azure Storage Explorer, az Azure PowerShell vagy az Azure CLI. A saját migráláshoz az Azure Storage ügyfélkódtáraival egyikét is létrehozható.

## <a name="see-also"></a>Lásd még
- [Azure Storage replication (Azure Storage replikáció)](storage-redundancy.md)
- [Helyileg redundáns tárolás (LRS): az Azure Storage alacsony költségű adatredundancia](storage-redundancy-lrs.md)
- [Georedundáns tárolás (GRS): az Azure Storage-régiók közti replikáció](storage-redundancy-grs.md)