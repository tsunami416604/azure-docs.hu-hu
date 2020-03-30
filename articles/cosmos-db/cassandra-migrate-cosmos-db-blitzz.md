---
title: Adatok áttelepítése a Cassandrandrából az Azure Cosmos DB Cassandra API-ba a Blitzz használatával
description: Ismerje meg, hogyan telepítheti át az adatokat az Apache Cassandra adatbázisból az Azure Cosmos DB Cassandra API-ba a Blitzz használatával.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 746232a85e326f08d44cf2dbe0a4ae718c7cc312
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69984367"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Adatok áttelepítése a Cassandrandrából az Azure Cosmos DB Cassandra API-fiókba a Blitzz használatával

Cassandra API az Azure Cosmos DB vált egy nagyszerű választás a vállalati számítási feladatok futó Apache Cassandra különböző okok miatt, mint például: 

* **Nincs általános terhelés az irányítás és a figyelés terén:** Kiküszöböli a számos beállítás kezelését és figyelését az operációs rendszeren, a JVM-fájlokon és a yaml-fájlokon és azok interakcióiban.

* **Jelentős költségmegtakarítás:** Az Azure Cosmos DB költségmegtakarítást, amely tartalmazza a virtuális gépek költségét, a sávszélességet és a vonatkozó licenceket. Emellett nem kell kezelnie az adatközpontokat, a kiszolgálókat, az SSD-tárolást, a hálózati és az áramköltségeket. 

* **Meglévő kód és eszközök használatának képessége:** Az Azure Cosmos DB a meglévő Cassandra SDK-kkal és eszközökkel biztosít vezetékes protokollszintű kompatibilitást. A kompatibilitásnak köszönhetően meglévő kódbázisát minimális változtatásokkal az Azure Cosmos DB Cassandra API-val is használhatja.

Az adatbázis-munkaterhelések áttelepítése különböző platformokról a másikra különböző módokon. [A Blitzz](https://www.blitzz.io) egy olyan eszköz, amely biztonságos és megbízható módot kínál a különböző adatbázisokból az Azure Cosmos DB-re történő nulla állásidő-áttelepítés végrehajtására. Ez a cikk ismerteti az adatok áttelepítése az Apache Cassandra adatbázisból az Azure Cosmos DB Cassandra API Blitzz használatával szükséges lépéseket.

## <a name="benefits-using-blitzz-for-migration"></a>Előnyök a Blitzz használata a migrációhoz

A Blitzz áttelepítési megoldása lépésről lépésre közelíti meg az összetett működési munkaterhelések áttelepítését. A blitzz nulla állásidő-áttelepítési tervének legfontosabb szempontjai a következők:

* Az üzleti logika (táblák, indexek, nézetek) automatikus áttelepítését kínálja az Apache Cassandra adatbázisból az Azure Cosmos DB-be. Nem kell manuálisan létrehoznia a sémákat.

* A Blitzz nagy mennyiségű és párhuzamos adatbázis-replikációt kínál. Lehetővé teszi, hogy mind a forrás-, mind a célplatformok szinkronban legyenek az áttelepítés során a Change-Data-Capture (CDC) nevű technikával. A CDC használatával Blitzz folyamatosan lekéri a változások adatfolyamát a forrásadatbázisból(Apache Cassandra), és alkalmazza azt a céladatbázisra (Azure Cosmos DB).

* Hibatűrő, és pontosan az adatok kézbesítését garantálja még a rendszer hardver- vagy szoftverhibája esetén is.

* Az adatokat az átvitel során számos biztonsági módszerrel biztosítja, például SSL, titkosítás.

## <a name="steps-to-migrate-data"></a>Az adatok áttelepítése lépései

Ez a szakasz ismerteti a Blitzz beállításához szükséges lépéseket, és áttelepíti az adatokat az Apache Cassandra adatbázisból az Azure Cosmos DB-be.

1. A Blitzz replikáns telepítését hozó számítógépről adjon hozzá egy biztonsági tanúsítványt. Ezt a tanúsítványt a Blitzz replikáns a megadott Azure Cosmos DB-fiókkal létesített SSL-kapcsolat létrehozásához. A tanúsítványt a következő lépésekkel veheti fel:

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. Lehet kapni a Blitzz telepítés és a bináris fájlokat sem kér egy demo a [Blitzz honlapján](https://www.blitzz.io). Másik lehetőségként [e-mailt](mailto:success@blitzz.io) is küldhet a csapatnak.

   ![Blitzz replikáns eszköz letöltés](./media/cassandra-migrate-cosmos-db-blitzz/blitzz-replicant-download.png)

   ![Blitzz replikáns fájlok](./media/cassandra-migrate-cosmos-db-blitzz/replicant-files.png)

1. A CLI terminálról állítsa be a forrásadatbázis konfigurációját. Nyissa meg a **`vi conf/conn/cassandra.yml`** konfigurációs fájlt a paranccsal, és adjon hozzá egy vesszővel tagolt listát a Cassandra csomópontok IP-címeiről, a portszámról, a felhasználónévről, a jelszóról és minden egyéb szükséges adatról. Az alábbi példa a konfigurációs fájl tartalmára mutat be:

   ```bash
   type: CASSANDRA
  
   host: 172.17.0.2
   port: 9042

   username: 'cassandra'
   password: 'cassandra'

   max-connections: 30

   ```

   ![Cassandra kapcsolatszerkesztő megnyitása](./media/cassandra-migrate-cosmos-db-blitzz/open-connection-editor-cassandra.png)

   ![Cassandra kapcsolat konfigurációja](./media/cassandra-migrate-cosmos-db-blitzz/cassandra-connection-configuration.png)

   A konfigurációs adatok kitöltése után mentse és zárja be a fájlt.

1. Szükség esetén beállíthatja a forrásadatbázis-szűrőfájlt. A szűrőfájl határozza meg, hogy mely sémákat vagy táblákat kell áttelepíteni. Nyissa meg a **`vi filter/cassandra_filter.yml`** konfigurációs fájlt a paranccsal, és adja meg a következő konfigurációs adatokat:

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```

   Az adatbázis-szűrő részleteinek kitöltése után mentse és zárja be a fájlt.

1. Ezután beállítja a céladatbázis konfigurációját. A konfiguráció definiálása előtt [hozzon létre egy Azure Cosmos DB Cassandra API-fiókot,](create-cassandra-dotnet.md#create-a-database-account) majd hozzon létre egy Keyspace-t és egy táblát az áttelepített adatok tárolására. Mivel az Azure Cosmos DB-ben az Apache Cassandra-ról cassandra API-ra vált, használhatja ugyanazt a partíciókulcsot, amelyet az Apache cassandra használt.

1. Az adatok áttelepítése előtt növelje a tároló átviteli átaputét az alkalmazás gyors áttelepítéséhez szükséges mennyiségre. Például növelheti az átviteli 100000 Több rekts. Az átviteli kábel méretezése az áttelepítés megkezdése előtt segít az adatok rövidebb idő alatt történő áttelepítésében.

   ![Az Azure Cosmos-tároló méretezése az egész](./media/cassandra-migrate-cosmos-db-blitzz/scale-throughput.png)

   Csökkentse az átviteli szintet az áttelepítés befejezése után. Az egyes műveletekhez szükséges tárolt adatok és a felelős információk mennyisége alapján megbecsülheti az adatáttelepítés után szükséges átviteli forgalmat. Ha többet szeretne megtudni a szükséges rúpiák becsléséhez, olvassa [el az átviteli kapacitás kiépítése a tárolókon és adatbázisokon](set-throughput.md) és a Becslés [iRU/s az Azure Cosmos DB kapacitástervező](estimate-ru-with-capacity-planner.md) cikkek használatával.

1. Az Azure Cosmos-fiók **kapcsolati pontjának, portjának, felhasználónevének**és **elsődleges jelszavának** beszerezése a **Kapcsolati karakterlánc** ablaktáblából. Ezeket az értékeket a konfigurációs fájlban fogja használni.

1. A CLI terminálról állítsa be a céladatbázis konfigurációját. Nyissa meg a **`vi conf/conn/cosmosdb.yml`** konfigurációs fájlt a paranccsal, és adjon hozzá egy vesszővel tagolt listát a gazdagép URI-járól, a portszámáról, a felhasználónevéről, a jelszaváról és más szükséges paraméterekről. A következő példa a konfigurációs fájl tartalmát mutatja be:

   ```bash
   type: COSMOSDB

   host: '<Azure Cosmos account’s Contact point>'
   port: 10350

   username: 'blitzzdemo'
   password: '<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   ```

1. Ezután telepítse át az adatokat a Blitzz használatával. A Blizz replikáns teljes **vagy** **pillanatkép** módban futtatható:

   * **Teljes mód** – Ebben a módban a replikáns továbbra is fut az áttelepítés után, és figyeli a változásokat a forrás Apache Cassandra rendszer. Ha bármilyen változást észlel, azok replikálódik a cél Azure Cosmos-fiók valós időben.

   * **Pillanatkép mód** – Ebben a módban sémaáttelepítést és egyszeri adatreplikációt hajthat végre. Ez a beállítás nem támogatja a valós idejű replikációt.

   A fenti két mód használatával az áttelepítés nulla állásidővel hajtható végre. 

1. Az adatok áttelepítéséhez futtassa a következő parancsot a Blitzz replikáns CLI terminálról:

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing
   ```

   A replikáns felhasználói felület a replikáció előrehaladását mutatja. Miután a séma áttelepítési és pillanatkép-művelet befejeződött, a folyamat 100%. Az áttelepítés befejezése után ellenőrizheti az adatokat a cél Azure Cosmos-adatbázisban.

   ![Cassandra adatáttelepítési kimenet](./media/cassandra-migrate-cosmos-db-blitzz/cassandra-data-migration-output.png)


1. Mivel teljes módot használt az áttelepítéshez, olyan műveleteket hajthat végre, mint például az Apache Cassandra forrásadatbázisában lévő adatok beszúrása, frissítése vagy törlése. Később ellenőrizze, hogy azok replikálva valós időben a cél Azure Cosmos-adatbázisban. Az áttelepítés után győződjön meg arról, hogy csökkentse az Azure Cosmos-tárolóhoz konfigurált átviteli.

1. A replikáns bármely ponton leállíthatja, és újraindíthatja a **--resume** kapcsolóval. A replikáció attól a ponttól folytatódik, amelyet az adatok konzisztenciájának veszélyeztetése nélkül leállított. A következő parancs bemutatja, hogyan kell használni a folytatáskapcsolót.

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing --resume
   ```

Ha többet szeretne megtudni az adatok célhoz való áttelepítéséről, a valós idejű áttelepítésről, olvassa el a [Blitzz replikáns bemutatóját.](https://www.youtube.com/watch?v=fsUhF9LUZmM)

## <a name="next-steps"></a>További lépések

* [Átviteli sebesség kiosztása tárolókra és adatbázisokra](set-throughput.md) 
* [A partíciós kulcs – gyakorlati tanácsok](partitioning-overview.md#choose-partitionkey)
* [Ru/s becslése az Azure Cosmos DB kapacitástervező](estimate-ru-with-capacity-planner.md) i.