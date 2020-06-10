---
title: Az alkalmazás rendelkezésre állásának javítása Azure Advisor
description: Az Azure-beli üzemelő példányok magas rendelkezésre állásának javításához használja a Azure Advisor.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: bed092a51b5a4aba1dfa64c17f5ed3d6f72212da
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84658465"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Az alkalmazás rendelkezésre állásának javítása Azure Advisor

Azure Advisor segít az üzleti szempontból kritikus fontosságú alkalmazások folytonosságának biztosításában és tökéletesítésében. A magas rendelkezésre állású javaslatokat az Advisor irányítópultjának **magas rendelkezésre állás** lapján érheti el.

## <a name="ensure-virtual-machine-fault-tolerance"></a>A virtuális gép hibatűrésének biztosítása

Az alkalmazás redundanciájának garantálása érdekében javasoljuk, hogy a virtuális gépeket legalább kettesével foglalja rendelkezésre állási csoportokba. Az Advisor azonosítja azokat a virtuális gépeket, amelyek nem részei egy rendelkezésre állási csoportnak, és azt javasolja, hogy egy rendelkezésre állási csoportba helyezze őket. Ez a konfiguráció biztosítja, hogy a tervezett vagy nem tervezett karbantartási események során legalább egy virtuális gép elérhető legyen, és megfelel az Azure-beli virtuális gép SLA-nak. Dönthet úgy is, hogy létrehoz egy rendelkezésre állási készletet a virtuális géphez, vagy hozzáadja a virtuális gépet egy meglévő rendelkezésre állási csoporthoz.

> [!NOTE]
> Ha úgy dönt, hogy létrehoz egy rendelkezésre állási készletet, legalább egy virtuális gépet hozzá kell adnia. Javasoljuk, hogy egy rendelkezésre állási csoportban két vagy több virtuális gépet csoportosítson, hogy legalább egy gép elérhető legyen a leállás során.

## <a name="ensure-availability-set-fault-tolerance"></a>Rendelkezésre állási csoport hibatűrésének biztosítása

Az alkalmazás redundanciájának garantálása érdekében javasoljuk, hogy a virtuális gépeket legalább kettesével foglalja rendelkezésre állási csoportokba. Az Advisor egyetlen virtuális gépet tartalmazó rendelkezésre állási csoportokat azonosít, és egy vagy több virtuális gép hozzáadását javasolja.Ez a konfiguráció biztosítja, hogy a tervezett vagy nem tervezett karbantartási események során legalább egy virtuális gép elérhető legyen, és megfelel az Azure-beli virtuális gép SLA-nak.Dönthet úgy, hogy létrehoz egy virtuális gépet, vagy egy meglévő virtuális gépet ad hozzá a rendelkezésre állási csoporthoz.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>Felügyelt lemezek használata az adatok megbízhatóságának javítása érdekében

Azok a virtuális gépek, amelyek olyan rendelkezésre állási csoportban találhatók, amelyekben a Storage-fiókokat vagy a tárolási méretezési egységeket osztják meg, nem rugalmasak egyetlen tárolási skálázási egység meghibásodása esetén a leállás során. Az Advisor azonosítja ezeket a rendelkezésre állási csoportokat, és javaslatot tesz az Azure Managed Disks-ra való áttérésre. Ezzel biztosíthatja, hogy a rendelkezésre állási csoport különböző virtuális gépei lemezei elég elszigeteltek legyenek, hogy elkerülje az adott meghibásodási pontot. 

## <a name="known-issue-with-check-point-network-virtual-appliance-image-version"></a>A Check Point hálózati virtuális berendezés rendszerkép-verziójával kapcsolatos ismert hiba

Az Advisor képes azonosítani, hogy a virtuális gép futtathatja-e a következő, a platform karbantartási művelete esetén elveszített, hálózati kapcsolattal rendelkező ellenőrzési pont lemezképét. Az Advisor javaslata segítséget nyújt a probléma megoldására szolgáló rendszerkép újabb verziójára való frissítéshez. Ez biztosítja az üzletmenet folytonosságát a jobb hálózati kapcsolaton keresztül.

## <a name="ensure-application-gateway-fault-tolerance"></a>Az Application Gateway hibatűrésének biztosítása

Ez a javaslat biztosítja az alkalmazás-átjárók által működtetett, kritikus fontosságú alkalmazások üzletmenet-folytonosságát. Az Advisor olyan Application Gateway-példányokat azonosít, amelyek nincsenek beállítva a hibatűréshez, és az elvégezhető szervizelési műveleteket javasolják. Az Advisor egy közepes vagy nagyméretű egypéldányos Application Gateway-átjárót azonosít, és legalább egy példány hozzáadását javasolja. Emellett az egy-vagy többpéldányos kisméretű Application Gateway-átjárókat is azonosít, és a közepes vagy nagy méretű SKU-ra való áttérést javasolja. Az Advisor javasolja ezeket a műveleteket annak biztosításához, hogy az Application Gateway-példányok megfeleljenek az adott erőforrásokra vonatkozó SLA-követelményeknek.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Virtuális gépek adatainak védelme véletlen törléssel

A virtuális gép biztonsági mentésének beállítása biztosítja az üzleti szempontból kritikus fontosságú adatmennyiség rendelkezésre állását, és védelmet nyújt a véletlen törlés vagy sérülés ellen. Az Advisor azonosítja azokat a virtuális gépeket, amelyeken a biztonsági mentés nincs engedélyezve, és a biztonsági mentés engedélyezését javasolja. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Ellenőrizze, hogy van-e hozzáférése az Azure Cloud experthez, amikor szüksége van rá

Üzleti szempontból kritikus fontosságú számítási feladatok futtatásakor fontos, hogy szükség esetén hozzáférhessen a technikai támogatáshoz. Az Advisor olyan potenciális üzleti szempontból kritikus előfizetéseket azonosít, amelyek nem rendelkeznek technikai támogatással a támogatási csomagban, és javaslatot tesz arra, hogy a technikai támogatást tartalmazó lehetőségre frissítsen.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Azure Service Health riasztások létrehozása, amelyekről értesítést kap, ha az Azure-problémák érintik Önt

Javasoljuk, hogy Azure Service Health riasztások beállításával értesítést kapjon, ha az Azure-szolgáltatással kapcsolatos problémák érintik Önt. A [Azure Service Health](https://azure.microsoft.com/features/service-health/) egy ingyenes szolgáltatás, amely személyre szabott útmutatást és támogatást nyújt, ha egy Azure-szolgáltatással kapcsolatos probléma jelentkezik. Az Advisor olyan előfizetéseket azonosít, amelyek nincsenek konfigurálva riasztások, és azt javasolja, hogy hozzon létre egyet.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Traffic Manager végpontok konfigurálása a rugalmasság érdekében

Ha egy adott végpont meghibásodik, Traffic Manager több végponttal rendelkező profilok magasabb rendelkezésre állással rendelkeznek. A végpontok különböző régiókban való elhelyezése tovább javítja a szolgáltatás megbízhatóságát. Az Advisor olyan Traffic Manger-profilokat azonosít, ahol csak egy végpont található, és legalább egy végpont hozzáadását javasolja egy másik régióban.

Ha egy Traffic Manager profilban található összes végpont ugyanazon a régióban található, a más régiókban lévő felhasználók kapcsolati késéseket tapasztalhatnak. A végpontok egy másik régióba való felvétele vagy áthelyezése javítja a teljes teljesítményt, és jobb rendelkezésre állást biztosít, ha az egyik régióban lévő összes végpont meghibásodik. Az Advisor azonosítja Traffic Manager a közelségi útválasztáshoz konfigurált profilokat, ahol az összes végpont ugyanabban a régióban található. Azt javasolja, hogy egy végpontot egy másik Azure-régióhoz adjon hozzá vagy helyezzen át.

Ha egy Traffic Manager-profil a földrajzi útválasztáshoz van konfigurálva, akkor a rendszer a forgalmat a meghatározott régiók alapján irányítja a végpontokhoz. Ha egy régió meghibásodik, nincs előre definiált feladatátvétel. Ha olyan végponttal rendelkezik, amelyben a regionális csoportosítás az "All (világ)" értékre van konfigurálva, a rendszer elkerüli a forgalmat, és javítja a szolgáltatás rendelkezésre állását. Az Advisor azonosítja a földrajzi útválasztáshoz konfigurált Traffic Manager-profilokat, ahol nincs olyan végpont konfigurálva, amelynek a regionális csoportosítása "All (világ)". Azt javasolja, hogy módosítsa a konfigurációt úgy, hogy az "All (világ)" végpontot hozzon.

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Az adatok mentése és helyreállítása a véletlen felülírás vagy törlés után a Soft delete használatával az Azure Storage-fiókban

Engedélyezze a helyreállítható [törlést](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) a Storage-fiókban, hogy a törölt Blobok a véglegesen törölt állapotba kerüljenek a végleges törlés helyett. Az adatok felülírásakor a rendszer létrehoz egy helyreállítható módon törölt pillanatképet a felülírt adatok állapotának mentéséhez. A Soft delete használata lehetővé teszi a helyreállítást, ha véletlen törlés vagy felülírás történik. Az Advisor olyan Azure Storage-fiókokat azonosít, amelyeken nincs engedélyezve a helyreállított törlés, és azt javasolja, hogy engedélyezze azt.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>VPN-átjáró konfigurálása aktív-aktív kapcsolati rugalmasságra

Az aktív-aktív konfigurációban a VPN-átjáró mindkét példánya S2S VPN-alagutakat hoz létre a helyszíni VPN-eszközhöz. Ha egy tervezett karbantartási esemény vagy nem tervezett esemény egy átjáró-példányra történik, a rendszer automatikusan átváltja a forgalmat a másik aktív IPsec-alagútra. Azure Advisor azonosítja azokat a VPN-átjárókat, amelyek nincsenek aktív-aktívként konfigurálva, és javasoljuk, hogy a magas rendelkezésre állás érdekében konfigurálja azokat.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Éles környezetben működő VPN-átjárók használata az éles számítási feladatok futtatásához

Azure Advisor a rendszer minden olyan VPN-átjárót keres, amely alapszintű SKU, és azt javasolja, hogy ehelyett éles SKU-t használjon. Az alapszintű SKU fejlesztési és tesztelési célokra lett tervezve. Az üzemi SKU-kal nagyobb számú alagút, BGP-támogatás, aktív-aktív konfigurációs beállítások, egyéni IPSec/IKE-házirend, valamint nagyobb stabilitás és rendelkezésre állás van.

## <a name="repair-invalid-log-alert-rules"></a>Érvénytelen naplózási riasztási szabályok javítása

Azure Advisor felismeri azokat a riasztási szabályokat, amelyek érvénytelen lekérdezésekkel rendelkeznek a feltételek szakaszban. A naplóriasztási szabályok az Azure Monitorban hozhatók létre, és elemzési lekérdezések adott időközönkénti futtatására szolgálnak. A lekérdezés eredményei határozzák meg, hogy egy riasztást aktiválni kell-e. Az elemzési lekérdezések idővel érvénytelenné válhatnak a hivatkozott erőforrásokban, táblákban vagy parancsokban bekövetkező változások miatt. Az Advisor azt ajánlja, hogy javítsa ki a lekérdezést a riasztási szabályban, hogy megakadályozza az automatikus letiltását, és biztosítsa az Azure-ban lévő erőforrások lefedettségének figyelését. [További információ a riasztási szabályok hibaelhárításáról](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Konzisztens indexelési mód konfigurálása a Cosmos DB-gyűjteményen

Azure Cosmos DB lusta indexelési móddal konfigurált tárolók befolyásolhatják a lekérdezési eredmények frissességét. Az Advisor felismeri az így konfigurált tárolókat, és azt javasolja, hogy konzisztens módba váltson. [További információ a szabályzatok indexeléséről Cosmos DB](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Azure Cosmos DB-tárolók konfigurálása partíciókulccsal

Azure Advisor azonosíthatja Azure Cosmos DB nem particionált gyűjteményeket, amelyek megközelítik a kiépített tárolási kvótát. Javasoljuk, hogy ezeket a gyűjteményeket a partíciós kulcs definíciójának megfelelően áttelepíti új gyűjteményekre, hogy a szolgáltatás automatikusan kibővítse őket. [További információ a partíciós kulcs kiválasztásáról](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Az Azure Cosmos DB .NET SDK frissítése a legújabb verzióra a Nugetről

A Azure Advisor Azure Cosmos DB azonosítja a .NET SDK régi verzióit használó fiókokat, és javasolja, hogy a legújabb javításokra, teljesítmény-fejlesztésekre és új funkciókra vonatkozó Nuget a legújabb verzióra frissítsen. [További információ a Cosmos DB .NET SDK-ról](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Az Azure Cosmos DB Java SDK frissítése a legújabb verzióra a Mavenről

A Azure Advisor Azure Cosmos DB azonosítja a Java SDK régi verzióit használó fiókokat, és javasolja, hogy a legújabb verzióra frissítsen a Maven legújabb verziójára, a teljesítmény fejlesztésére és az új funkciókra. [További információ a Cosmos DB Java SDK-ról](https://aka.ms/cosmosdb/sql-api-sdk-async-java)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Az Azure Cosmos DB Spark-összekötő frissítése a legújabb verzióra a Mavenről

A Azure Advisor azonosítja a Cosmos DB Spark-összekötő régi verzióit használó Azure Cosmos DB-fiókokat, és javasolja a Maven legújabb verzióra való frissítését a legújabb javításokkal, a teljesítménnyel kapcsolatos fejlesztésekkel és az új funkciókkal. [További információ a Cosmos DB Spark-összekötőről](https://aka.ms/cosmosdb/spark-connector)

## <a name="upgrade-recommendation-for-deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Frissítési javaslat a Kafka 1,1-es verziójának elavulttá tételéhez a HDInsight 4,0 Kafka-fürtben

2020. július 1-től kezdve az ügyfelek nem hozhatnak létre új Kafka-fürtöket a Kafka 1.1 használatával a HDInsight 4.0-ban. A meglévő fürtök a jelenlegi állapotukban futnak tovább, Microsoft-támogatás nélkül. Fontolja meg, hogy a HDInsight 4.0-ban átvált a Kafka 2.1-re 2020. június 30-ig, a rendszerhasználat/támogatás esetleges megszakításának elkerülése érdekében.

## <a name="upgrade-recommendation-for-deprecation-of-older-spark-versions-in-hdinsight-spark-cluster"></a>Frissítési javaslat a régebbi Spark-verziók elavult HDInsight Spark-fürtben való elavulttá tételéhez

2020. július 1-től kezdve az ügyfelek nem hozhatnak létre új Spark-fürtöket a Spark 2.1 és 2.2 használatával a HDInsight 3.6-ban, illetve a Spark 2.3 használatával a HDInsight 4.0-ban. A meglévő fürtök a jelenlegi állapotukban futnak tovább, Microsoft-támogatás nélkül. ",

## <a name="enable-virtual-machine-replication"></a>Virtuális gépek replikálásának engedélyezése
Azok a virtuális gépek, amelyek nem rendelkeznek egy másik régióba való replikálással, nem rugalmasak a regionális kimaradások esetén. A virtuális gépek replikálása csökkenti az Azure-régió meghibásodása során fellépő hátrányos üzleti hatásokat. Az Advisor felismeri azokat a virtuális gépeket, amelyeken nincs engedélyezve a replikáció, és a replikáció engedélyezését javasolja, hogy a leállás esetén gyorsan üzembe lehessen hozni a virtuális gépeket egy távoli Azure-régióban. [További információ a virtuális gépek replikálásáról](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Magas rendelkezésre állási javaslatok elérése az Advisorban

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), majd nyissa meg az [Advisor alkalmazást](https://aka.ms/azureadvisordashboard).

2.  Az Advisor Irányítópultján kattintson a **magas rendelkezésre állás** fülre.

## <a name="next-steps"></a>Következő lépések

Az Advisor ajánlásaival kapcsolatos további információkért lásd:
* [Az Advisor bemutatása](advisor-overview.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Advisor – Cost-javaslatok](advisor-cost-recommendations.md)
* [Az Advisor teljesítményével kapcsolatos javaslatok](advisor-performance-recommendations.md)
* [Advisor biztonsági javaslatok](advisor-security-recommendations.md)
* [Advisor működési kiválósági javaslatok](advisor-operational-excellence-recommendations.md)
