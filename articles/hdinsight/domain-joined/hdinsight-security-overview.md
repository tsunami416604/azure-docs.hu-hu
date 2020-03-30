---
title: Az Azure HDInsight vállalati biztonságának áttekintése
description: Ismerje meg a vállalati biztonság biztosításának különböző módszereit az Azure HDInsightban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.date: 03/03/2020
ms.openlocfilehash: 95bfe7d7788133d8548598cb30c8084bf64a977f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78267720"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>Az Azure HDInsight vállalati biztonságának áttekintése

Az Azure HDInsight számos módszert kínál a vállalati biztonsági igények kielégítésére. A legtöbb ilyen megoldás alapértelmezés szerint nincs aktiválva. Ez a rugalmasság lehetővé teszi az Ön számára legfontosabb biztonsági funkciók kiválasztását, és segít elkerülni a nem kívánt funkciók kifizetését. Ez azt is jelenti, hogy az Ön felelőssége annak biztosítása, hogy a megfelelő megoldások engedélyezve legyenek a beállításhoz és a környezethez.

Ez a cikk a biztonsági megoldásokat vizsgálja a biztonsági megoldások felosztásával négy hagyományos biztonsági pillérek: peremhálózati biztonság, hitelesítés, engedélyezés és titkosítás.

Ez a cikk az **Azure HDInsight Enterprise Security Package (ESP)** csomagot is bemutatja, amely Active Directory-alapú hitelesítést, többfelhasználós támogatást és szerepköralapú hozzáférés-vezérlést biztosít a HDInsight-fürtökszámára.

## <a name="enterprise-security-pillars"></a>Vállalati biztonsági pillérek

A vállalati biztonság vizsgálatának egyik módja a biztonsági megoldásokat négy fő csoportra osztja a vezérlés típusa alapján. Ezeket a csoportokat biztonsági pillérnek is nevezik, és a következők: peremterület-biztonság, hitelesítés, engedélyezés és titkosítás.

### <a name="perimeter-security"></a>Kerületi biztonság

A HDInsight perembiztonsága [virtuális hálózatokon](../hdinsight-plan-virtual-network-deployment.md)keresztül érhető el. A vállalati rendszergazda létrehozhat egy fürtöt egy virtuális hálózaton (VNET) belül, és hálózati biztonsági csoportok (NSG) használatával korlátozhatja a virtuális hálózathoz való hozzáférést. Csak a bejövő NSG-szabályokban engedélyezett IP-címek képesek kommunikálni a HDInsight-fürttel. Ez a konfiguráció biztosítja a peremhálózat biztonságát.

A virtuális hálózatban üzembe helyezett összes fürt rendelkezik egy privát végpont, amely feloldja a virtuális hálózaton belül a fürtátjárók privát HTTP-hozzáférésének privát IP-címére.

### <a name="authentication"></a>Hitelesítés

A HDInsight [vállalati biztonsági csomagja](apache-domain-joined-architecture.md) Active Directory-alapú hitelesítést, többfelhasználós támogatást és szerepköralapú hozzáférés-vezérlést biztosít. Az Active Directory-integráció az [Azure Active Directory tartományi szolgáltatások](../../active-directory-domain-services/overview.md)használatával érhető el. Ezekkel a képességekkel létrehozhat egy HDInsight-fürtöt, amely egy felügyelt Active Directory-tartományhoz csatlakozik. Ezután konfigurálhatja a vállalat azon alkalmazottainak listáját, akik hitelesíthetik magukat, és bejelentkezhetnek a fürtbe.

Ezzel a beállítással a vállalati alkalmazottak tartományi hitelesítő adataikkal jelentkezhetnek be a fürtcsomópontokba. A tartományi hitelesítő adatok kal is hitelesíthetik magukat más jóváhagyott végpontokkal, például az Apache Ambari Views, ODBC, JDBC, PowerShell és REST API-kkal a fürtöt.

### <a name="authorization"></a>Engedélyezés

A legtöbb vállalat által követett ajánlott eljárás annak biztosítása, hogy ne minden alkalmazott férjen hozzá az összes vállalati erőforráshoz. Hasonlóképpen a rendszergazda szerepköralapú hozzáférés-vezérlési házirendeket is definiálhat a fürt erőforrásokhoz. Ez csak az ESP-fürtökben érhető el.

A hadoop-rendszergazda konfigurálhatja a szerepköralapú hozzáférés-vezérlést (RBAC) az Apache [Hive](apache-domain-joined-run-hive.md), [HBase](apache-domain-joined-run-hbase.md)és [Kafka](apache-domain-joined-run-kafka.md) biztonságossá tétele érdekében az Apache Ranger-ben lévő bővítmények használatával. Az RBAC-házirendek konfigurálása lehetővé teszi, hogy engedélyeket társítson a szervezetben lévő szerepkörhöz. Ez az absztrakciós réteg megkönnyíti annak biztosítását, hogy a felhasználók csak a munkahelyi feladataik elvégzéséhez szükséges engedélyekkel rendelkezzenek. A Ranger azt is lehetővé teszi, hogy naplózza az alkalmazottak adathozzáférését és a hozzáférés-vezérlési házirendek módosításait.

Például, a rendszergazda konfigurálhatja az [Apache Ranger](https://ranger.apache.org/) keretrendszert, hogy beállítsa a Hive hozzáférés-vezérlés házirendjét. Ez a funkció biztosítja a sor- és oszlopszintű szűrést (adatmaszkolást), és szűri a jogosulatlan felhasználók bizalmas adatait.

### <a name="auditing"></a>Naplózás

A fürterőforrásokhoz és az adatokhoz való összes hozzáférés naplózása szükséges az erőforrások jogosulatlan vagy nem szándékos elérésének nyomon követéséhez. Ugyanolyan fontos, mint a HDInsight-fürt erőforrásainak védelme a jogosulatlan felhasználókkal szemben és az adatok védelme.

A rendszergazda megtekintheti és jelentheti a HDInsight-fürt erőforrásaihoz és adataihoz való összes hozzáférést. A rendszergazda megtekintheti és jelentheti az Apache Ranger által támogatott végpontokban létrehozott hozzáférés-vezérlési szabályzatok összes módosítását.

Az Apache Ranger és az Ambari naplózási naplóinak eléréséhez és az ssh hozzáférési naplókhoz [engedélyezze](../hdinsight-hadoop-oms-log-analytics-tutorial.md#cluster-auditing) az Azure Monitort, és tekintse meg a naplózási rekordokat biztosító táblákat.

### <a name="encryption"></a>Titkosítás

Az adatok védelme fontos a szervezeti biztonsági és megfelelőségi követelmények teljesítéséhez. A jogosulatlan alkalmazottak adataihoz való hozzáférés korlátozása mellett titkosítania kell azadatokat is.

A HDInsight-fürtök, az Azure Blob storage és az Azure Data Lake Storage Gen1/Gen2 adattárak egyaránt támogatják az inaktív adatok átlátható [kiszolgálóoldali titkosítását.](../../storage/common/storage-service-encryption.md) A Secure HDInsight-fürtök zökkenőmentesen működnek az inaktív adatok kiszolgálóoldali titkosításának képességével.

### <a name="compliance"></a>Megfelelőség

Az Azure megfelelőségi ajánlatai különböző típusú biztosítékokon alapulnak, beleértve a hivatalos tanúsítványokat, tanúsítványokat, érvényesítéseket, engedélyeket és független külső könyvvizsgáló cégek által készített értékeléseket, a szerződéses módosításokat, önértékelés, valamint a Microsoft által készített ügyfél-útmutató dokumentumok. A HDInsight megfelelőségi információkért tekintse meg a [Microsoft Adatvédelmi központot](https://www.microsoft.com/trust-center) és [a Microsoft Azure megfelelőségének áttekintését.](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)

## <a name="shared-responsibility-model"></a>Megosztott felelősség modell

Az alábbi kép összefoglalja a rendszer főbb biztonsági területeit és az egyes biztonsági megoldásokat. Azt is kiemeli, hogy mely biztonsági területek az Ön felelőssége, mint az ügyfél, és mely területek értik a HDInsight-ot, mint szolgáltatót.

![A HDInsight megosztott felelősségi diagramja](./media/hdinsight-security-overview/hdinsight-shared-responsibility.png)

Az alábbi táblázat az egyes biztonsági megoldások hoz az erőforrásokra mutató hivatkozásokat tartalmaz.

| Biztonsági terület | Elérhető megoldások | Felelős fél |
|---|---|---|
| Adathozzáférés biztonsága | Hozzáférés-vezérlési listák konfigurálása Az Azure Data Lake Storage Gen1 és Gen2 [hozzáférés-vezérlési listáinak](../../storage/blobs/data-lake-storage-access-control.md) konfigurálása  | Ügyfél |
|  | Engedélyezze a ["Biztonságos átvitel szükséges"](../../storage/common/storage-require-secure-transfer.md) tulajdonságot a tárfiókokon. | Ügyfél |
|  | [Az Azure Storage tűzfalainak](../../storage/common/storage-network-security.md) és virtuális hálózatainak konfigurálása | Ügyfél |
|  | [Az Azure virtuális hálózati szolgáltatásvégpontjainak](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) konfigurálása a Cosmos DB és az Azure SQL [DB](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) számára | Ügyfél |
|  | Győződjön meg arról, hogy a [TLS-titkosítás](../../storage/common/storage-security-tls.md) engedélyezve van az átvitel alatt álló adatokhoz. | Ügyfél |
|  | [Ügyfél által kezelt kulcsok](../../storage/common/storage-encryption-keys-portal.md) konfigurálása az Azure Storage titkosításához | Ügyfél |
| Alkalmazás- és köztes áruk biztonsága | Integráció az AAD-DS-szel és [a hitelesítés konfigurálása](apache-domain-joined-configure-using-azure-adds.md) | Ügyfél |
|  | [Apache Ranger engedélyezési házirendek](apache-domain-joined-run-hive.md) konfigurálása | Ügyfél |
|  | [Az Azure Monitor-naplók használata](../hdinsight-hadoop-oms-log-analytics-tutorial.md) | Ügyfél |
| Az operációs rendszer biztonsága | Fürtök létrehozása a legújabb biztonságos alapképpel | Ügyfél |
|  | Az [operációs rendszer rendszeres javításának](../hdinsight-os-patching.md) biztosítása | Ügyfél |
| Hálózati biztonság | Virtuális [hálózat](../hdinsight-plan-virtual-network-deployment.md) konfigurálása |
|  | [Bejövő hálózati biztonsági csoport (NSG) szabályainak](../hdinsight-plan-virtual-network-deployment.md#networktraffic) konfigurálása | Ügyfél |
|  | [Kimenő forgalom korlátozásának](../hdinsight-restrict-outbound-traffic.md) konfigurálása tűzfallal | Ügyfél |
| Virtualizált infrastruktúra | N/A | HDInsight (felhőszolgáltató) |
| Fizikai infrastruktúra biztonsága | N/A | HDInsight (felhőszolgáltató) |

## <a name="next-steps"></a>További lépések

* [HDInsight-fürtök tervezése ESP-vel](apache-domain-joined-architecture.md)
* [HDInsight-fürtök konfigurálása ESP-vel](apache-domain-joined-configure.md)
* [HDInsight-fürtök kezelése ESP-vel](apache-domain-joined-manage.md)
