---
title: Az alkalmazás elérhetőségének javítása az Azure Advisorral
description: Az Azure Advisor használatával javíthatja az Azure-telepítések magas rendelkezésre állását.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 997681ed62fa9985e3122ece22565dbae0e65b53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443108"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Az alkalmazás elérhetőségének javítása az Azure Advisorral

Az Azure Advisor segít biztosítani és javítani az üzleti legkritikusabb alkalmazások folytonosságát. Az Advisor **irányítópultjának Magas rendelkezésre állású** lapján kaphat magas rendelkezésre állási javaslatokat az Advisor-irányítópult magas rendelkezésre állása lapon.

## <a name="ensure-virtual-machine-fault-tolerance"></a>A virtuális gép hibatűrésének biztosítása

Az alkalmazás redundanciájának garantálása érdekében javasoljuk, hogy a virtuális gépeket legalább kettesével foglalja rendelkezésre állási csoportokba. Az Advisor azonosítja azokat a virtuális gépeket, amelyek nem részei egy rendelkezésre állási csoportnak, és azt javasolja, hogy azokat egy rendelkezésre állási csoportba helyezzék át. Ez a konfiguráció biztosítja, hogy egy tervezett vagy nem tervezett karbantartási esemény során legalább egy virtuális gép elérhető legyen, és megfeleljen az Azure virtuális gép SLA-jának. Választhat, hogy hozzon létre egy rendelkezésre állási készlet a virtuális gép, vagy adja hozzá a virtuális gépet egy meglévő rendelkezésre állási csoport.

> [!NOTE]
> Ha úgy dönt, hogy hozzon létre egy rendelkezésre állási csoport, hozzá kell adnia legalább még egy virtuális gép hozzá. Azt javasoljuk, hogy csoportosítson két vagy több virtuális gépet egy rendelkezésre állási csoportba annak érdekében, hogy legalább egy gép elérhető legyen egy kimaradás során.

## <a name="ensure-availability-set-fault-tolerance"></a>A rendelkezésre állási készlet hibatűrésének biztosítása

Az alkalmazás redundanciájának garantálása érdekében javasoljuk, hogy a virtuális gépeket legalább kettesével foglalja rendelkezésre állási csoportokba. Az Advisor azonosítja azokat a rendelkezésre állási csoportokat, amelyek egyetlen virtuális gépet tartalmaznak, és egy vagy több virtuális gép hozzáadását javasolja.Ez a konfiguráció biztosítja, hogy egy tervezett vagy nem tervezett karbantartási esemény során legalább egy virtuális gép elérhető legyen, és megfeleljen az Azure virtuális gép SLA-jának.Dönthet úgy, hogy hozzon létre egy virtuális gépet, vagy egy meglévő virtuális gépet arendelkezésre állási csoporthoz.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>Felügyelt lemezek használata az adatok megbízhatóságának javítása érdekében

Virtuális gépek, amelyek egy rendelkezésre állási csoport ban a lemezek, amelyek megosztják a tárfiókok vagy a tároló méretezési egységek nem rugalmas a készletkimaradások egyetlen tárolási méretezési egység meghibásodása esetén. Az Advisor azonosítja ezeket az elérhetőségi csoportokat, és javasolja az Azure felügyelt lemezekre való áttelepítést. Ez biztosítja, hogy a rendelkezésre állási csoportban lévő különböző virtuális gépek lemezei eléggé elkülönítve legyenek a meghibásodás egyetlen pontjának elkerülése érdekében. 

## <a name="ensure-application-gateway-fault-tolerance"></a>Az alkalmazásátjáró hibatűrése

Ez a javaslat biztosítja az alkalmazásátjárók által működtetett kritikus fontosságú alkalmazások üzletmenet-folytonosságát. Az Advisor azonosítja azokat az alkalmazásátjáró-példányokat, amelyek nincsenek hibatűrésre konfigurálva, és javítási műveleteket javasol, amelyeket végrehajthat. Az Advisor azonosítja a közepes vagy nagy egypéldányos alkalmazásátjárókat, és legalább még egy példány hozzáadását javasolja. Azt is azonosítja az egy- vagy többpéldányos kis alkalmazásátjárók, és javasolja a közepes vagy nagy sk-ek áttelepítése. Az Advisor azt javasolja, hogy ezeket a műveleteket annak érdekében, hogy az alkalmazás átjáró példányai vannak konfigurálva, hogy megfeleljen a jelenlegi SLA-követelmények ezen erőforrások.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>A virtuális gép adatainak védelme a véletlen törléstől

A virtuális gépek biztonsági mentésének beállítása biztosítja az üzleti legkritikusabb adatok rendelkezésre állását, és védelmet nyújt a véletlen törlés vagy sérülés ellen. Az Advisor azonosítja azokat a virtuális gépeket, ahol a biztonsági mentés nincs engedélyezve, és a biztonsági mentés engedélyezését javasolja. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Annak biztosítása, hogy szükség esetén hozzáférhessen az Azure felhőszakértőihez

Üzleti legkritikusabb számítási feladatok futtatásakor fontos, hogy szükség esetén hozzáférjen a technikai támogatáshoz. Az Advisor azonosítja azokat a potenciális, üzleti legkritikusabb előfizetéseket, amelyek nem tartalmaznak technikai támogatást a támogatási csomagjukban, és javasolja a technikai támogatást is tartalmazó beállításra való frissítést.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Azure Service Health-riasztások létrehozása, amelyek értesítést kapnak, ha az Azure-problémák érintik Önt

Azt javasoljuk, hogy az Azure Service Health-riasztások beállítása értesítést kapjon, ha az Azure-szolgáltatási problémák érintik Önt. [Az Azure Service Health](https://azure.microsoft.com/features/service-health/) egy ingyenes szolgáltatás, amely személyre szabott útmutatást és támogatást nyújt, ha egy Azure-szolgáltatás problémája érinti. Az Advisor azonosítja azokat az előfizetéseket, amelyeknem rendelkeznek konfigurált riasztásokkal, és javasolja, hogy hozzon létre egyet.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>A Traffic Manager végpontjainak konfigurálása rugalmasságra

Traffic Manager-profilok több végpont élménye magasabb rendelkezésre állás, ha egy adott végpont sikertelen. A végpontok különböző régiókban való elhelyezése tovább javítja a szolgáltatás megbízhatóságát. Az Advisor azonosítja a traffic manger-profilokat, ahol csak egy végpont van, és azt javasolja, hogy legalább még egy végpontot adjon hozzá egy másik régióban.

Ha egy Traffic Manager-profil ban lévő, közelségi útválasztásra konfigurált összes végpont ugyanabban a régióban van, más régiókfelhasználói kapcsolati késéseket tapasztalhatnak. Végpont hozzáadása vagy áthelyezése egy másik régióba javítja az általános teljesítményt, és jobb rendelkezésre állást biztosít, ha egy régió összes végpontja sikertelen. Az Advisor azonosítja a közelség-útválasztáshoz konfigurált Traffic Manager-profilokat, ahol az összes végpont ugyanabban a régióban található. Azt javasolja, hozzáadása vagy áthelyezése egy végpont egy másik Azure-régióban.

Ha egy Traffic Manager-profil földrajzi útválasztásra van konfigurálva, akkor a forgalom meghatározott régiók alapján végpontok felé lesz irányítva. Ha egy régió meghibásodik, nincs előre definiált feladatátvétel. Miután egy végpont, ahol a regionális csoportosítás van beállítva, hogy "All (World)" elkerüli a forgalom megszakad, és javítja a szolgáltatás rendelkezésre állását. Az Advisor azonosítja a földrajzi útválasztáshoz konfigurált Traffic Manager-profilokat, ahol nincs olyan végpont, amely a regionális csoportosítást "Minden (világ)" néven konfigurálta volna. Azt javasolja, hogy módosítsa a konfigurációt, hogy egy végpont "All (World).

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Az Azure Storage-fiók helyreállítható törlése használata az adatok véletlen felülírása vagy törlése után történő mentéséhez és helyreállításához

Engedélyezze a [helyreállítható törlést](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) a tárfiókban, hogy a törölt blobok végleges enyelegős állapotba váltsanak, ahelyett, hogy véglegesen törölnének. Az adatok felülírásakor a rendszer létrehoz egy helyreállítható módon törölt pillanatképet a felülírt adatok állapotának mentéséhez. A helyreállítható törlés lehetővé teszi a helyreállítást, ha véletlen törlés vagy felülírás van. Az Advisor azonosítja azokat az Azure Storage-fiókokat, amelyeken nincs engedélyezve a helyreállítható törlés, és javasolja, hogy engedélyezze azt.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>A VPN-átjáró konfigurálása aktív-aktív kapcsolatrugalmassághoz

Aktív-aktív konfigurációban a VPN-átjáró mindkét példánya S2S VPN-alagutakat hoz létre a helyszíni VPN-eszközhöz. Ha egy tervezett karbantartási esemény vagy nem tervezett esemény történik egy átjárópéldány, a forgalom automatikusan átvált a másik aktív IPsec-alagútra. Az Azure Advisor azonosítja azokat a VPN-átjárókat, amelyek nincsenek aktív-aktívként konfigurálva, és azt javasolja, hogy konfigurálja őket a magas rendelkezésre álláshoz.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Éles VPN-átjárók használata az éles számítási feladatok futtatásához

Az Azure Advisor ellenőrzi az alapvető termékváltozatú VPN-átjárókat, és azt javasolja, hogy inkább éles termékváltozatot használjon. Az alaptermékváltozat fejlesztési és tesztelési célokra készült. Az éles termékverziók nagyobb számú alagutat, BGP-támogatást, aktív-aktív konfigurációs beállításokat, egyéni Ipsec/IKE házirendet, valamint nagyobb stabilitást és rendelkezésre állást kínálnak.

## <a name="repair-invalid-log-alert-rules"></a>Érvénytelen naplóriasztási szabályok javítása

Az Azure Advisor észleli azokat a riasztási szabályokat, amelyek érvénytelen lekérdezéseket a feltétel szakaszban megadott. A naplóriasztási szabályok az Azure Monitorban hozhatók létre, és elemzési lekérdezések adott időközönkénti futtatására szolgálnak. A lekérdezés eredményei határozzák meg, hogy egy riasztást aktiválni kell-e. Az elemzési lekérdezések idővel érvénytelenné válhatnak a hivatkozott erőforrásokban, táblákban vagy parancsokban bekövetkező változások miatt. Az Advisor azt javasolja, hogy javítsa ki a lekérdezést a riasztási szabályban, hogy megakadályozza az automatikus letiltását, és biztosítsa az erőforrások figyelésének az Azure-ban való lefedettségét. [További információ a riasztási szabályok hibaelhárításáról](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Konzisztens indexelési mód konfigurálása a Cosmos DB-gyűjteményben

A Lusta indexelési móddal konfigurált Azure Cosmos DB-tárolók hatással lehetnek a lekérdezési eredmények frissességére. Az Advisor észleli az így konfigurált tárolókat, és konzisztens módba való váltást javasol. [További információ a Cosmos DB indexelési szabályzatairól](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Azure Cosmos DB-tárolók konfigurálása partíciókulccsal

Az Azure Advisor azonosítja az Azure Cosmos DB nem particionált gyűjtemények, amelyek megközelítik a kiépített tárolási kvótát. Azt javasolja, hogy ezeket a gyűjteményeket egy partíciókulcs-definícióval rendelkező új gyűjteményekbe migrálja, hogy a szolgáltatás automatikusan kioszthassa őket. [További információ a partíciókulcs kiválasztásáról](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Az Azure Cosmos DB .NET SDK frissítése a legújabb verzióra a Nugetről

Az Azure Advisor azonosítja a .NET SDK régi verzióit használó Azure Cosmos DB-fiókokat, és javasolja a legújabb nugeti verzióra való frissítést a legújabb javítások, teljesítményfejlesztések és új funkciók képességeihez. [További információ a Cosmos DB .NET SDK-ról](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Az Azure Cosmos DB Java SDK frissítése a legújabb verzióra a Mavenről

Az Azure Advisor azonosítja a Java SDK régi verzióit használó Azure Cosmos DB-fiókokat, és javasolja a legújabb maveni verzióra való frissítést a legújabb javítások, teljesítményfejlesztések és új funkciók képességeihez. [További információ a Cosmos DB Java SDK-ról](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Az Azure Cosmos DB Spark-összekötő frissítése a legújabb verzióra a Mavenről

Az Azure Advisor azonosítja az Azure Cosmos DB-fiókokat, amelyek a Cosmos DB Spark-összekötő régi verzióit használják, és javasoljuk, hogy frissítsen a Legújabb verzióra a Mavenből a legújabb javítások, teljesítményfejlesztések és új funkciók képességeihez. [További információ a Cosmos DB Spark-összekötőről](https://aka.ms/cosmosdb/spark-connector)

## <a name="enable-virtual-machine-replication"></a>Virtuális gépek replikálásának engedélyezése
Azok a virtuális gépek, amelyeknem rendelkeznek replikációval egy másik régióban, nem ellenállóak a regionális kimaradásokkal szemben. A virtuális gépek replikálása csökkenti a káros üzleti hatások at az Azure-régió kimaradás a káros üzleti hatások. Az Advisor észleli azokat a virtuális gépeket, amelyek nem rendelkeznek engedélyezve a replikációval, és javasolja a replikáció engedélyezését, hogy kimaradás esetén gyorsan meghozhassa a virtuális gépeket egy távoli Azure-régióban. [További információ a virtuális gépek replikációjáról](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>A magas rendelkezésre állásra vonatkozó ajánlások elérése az Advisorban

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), majd nyissa meg [az Advisor](https://aka.ms/azureadvisordashboard)t.

2.  Az Advisor irányítópultján kattintson a **Magas rendelkezésre állás fülre.**

## <a name="next-steps"></a>További lépések

További információ az Advisor-ajánlásokról:
* [Bevezetés az Azure Advisorba](advisor-overview.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Tanácsadói költségajánlások](advisor-cost-recommendations.md)
* [Tanácsadó teljesítményre vonatkozó ajánlásai](advisor-performance-recommendations.md)
* [Tanácsadó biztonsági ajánlásai](advisor-security-recommendations.md)
* [Az Advisor Operational Excellence ajánlásai](advisor-operational-excellence-recommendations.md)
