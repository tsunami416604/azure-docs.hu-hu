---
title: Adatok áttelepítése az Azure Cosmos DB Cassandra API-fiókba a Striim használatával
description: Ismerje meg, hogyan használhatja a Striim segítségével az oracle-adatbázisból egy Azure Cosmos DB Cassandra API-fiókba történő áttelepítését.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: af99f369245d6006d0d4784e572020b820e406a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71266045"
---
# <a name="migrate-data-to-azure-cosmos-db-cassandra-api-account-using-striim"></a>Adatok áttelepítése az Azure Cosmos DB Cassandra API-fiókba a Striim használatával

A Striim-lemezkép az Azure piactéren folyamatos, valós idejű adatmozgást kínál az adatraktárakból és adatbázisokból az Azure-ba. Az adatok áthelyezése közben in-line denormalizációt, adatátalakítást, valós idejű elemzéseket és adatjelentési forgatókönyveket hajthat végre. A Striim segítségével egyszerűen áthelyezheti a vállalati adatokat az Azure Cosmos DB Cassandra API-ba. Az Azure olyan piactéri ajánlatot kínál, amely megkönnyíti a Striim üzembe helyezését és az adatok áttelepítését az Azure Cosmos DB-ba. 

Ez a cikk bemutatja, hogyan használhatja a Striim segítségével az adatok áttelepítését egy **Oracle-adatbázisból** egy **Azure Cosmos DB Cassandra API-fiókba.**

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik [Azure-előfizetéssel](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* A helyszínen futó Oracle-adatbázis, amelyben bizonyos adatok találhatók.

## <a name="deploy-the-striim-marketplace-solution"></a>A Striim piactéri megoldás üzembe helyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza **az Erőforrás létrehozása lehetőséget,** és keressen a **Striim** az Azure piactéren. Válassza az első lehetőséget, és hozza létre a **lehetőséget.**

   ![Striim piactéri elem keresése](./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png)

1. Ezután adja meg a Striim-példány konfigurációs tulajdonságait. A Striim-környezet egy virtuális gépben van telepítve. Az **Alapokablakban** adja meg a **virtuális gép felhasználónevét**, **vm jelszót** (ezt a jelszót az SSH a virtuális gép). Válassza ki **az előfizetést**, **az erőforráscsoportot**és **a helyadatokat,** ahol telepíteni szeretné a Striim-et. Ha elkészült, válassza **az OK gombot.**

   ![Striim alapbeállításainak konfigurálása](./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png)


1. A **Striim fürt beállítások** ablaktáblán válassza ki a Striim-telepítés típusát és a virtuális gép méretét.

   |Beállítás | Érték | Leírás |
   | ---| ---| ---|
   |Striim telepítési típus |Különálló | A Striim **önálló** vagy **fürttelepítési** típusokban futtatható. Az önálló mód egyetlen virtuális gépen telepíti a Striim-kiszolgálót, és az adatkötettől függően kiválaszthatja a virtuális gépek méretét. A fürtmód két vagy több, a kiválasztott méretű virtuális gépre telepíti a Striim-kiszolgálót. A 2-nél több csomóns fürtkörnyezetek automatikusan magas rendelkezésre állást és feladatátvételt biztosítanak.</br></br> Ebben az oktatóanyagban kiválaszthatja az Önálló lehetőséget. Használja az alapértelmezett "Standard_F4s" méretű virtuális gép. | 
   | A Striim-fürt neve|    <Striim_cluster_Name>|  A Striim-fürt neve.|
   | Striim fürt jelszava|   <Striim_cluster_password>|  A fürt jelszava.|

   Miután kitöltötte az űrlapot, a folytatáshoz válassza az **OK gombot.**

1. A **Striim hozzáférési beállítások** ablaktáblán konfigurálja a **nyilvános IP-címet** (válassza ki az alapértelmezett értékeket), **a Striim tartománynevét**, **a Rendszergazdai jelszót,** amelyet a Striim felhasználói felületére való bejelentkezéshez használni szeretne. Konfiguráljon virtuális hálózatot és alhálózatot (válassza ki az alapértelmezett értékeket). A részletek kitöltése után a folytatáshoz válassza az **OK gombot.**

   ![Striim hozzáférési beállítások](./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png)

1. Az Azure ellenőrzi a központi telepítést, és gondoskodik arról, hogy minden jól néz ki; az ellenőrzés néhány percet vesz igénybe. Az ellenőrzés befejezése után válassza az **OK gombot.**
  
1. Végül tekintse át a használati feltételeket, és válassza a **Create lehetőséget** a Striim-példány létrehozásához. 

## <a name="configure-the-source-database"></a>A forrásadatbázis konfigurálása

Ebben a szakaszban az Oracle adatbázist konfigurálja az adatok mozgatásának forrásaként.  Az Oracle-hez való csatlakozáshoz szüksége lesz az [Oracle JDBC illesztőprogramra.](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) A módosítások at a forrás Oracle adatbázisból történő olvasásához használhatja a [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) vagy az [XStream API-kat.](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647) Az Oracle JDBC illesztőprogramnak jelen kell lennie a Striim Java osztályában az Oracle adatbázisból származó adatok olvasásához, írásához vagy megőrzéséhez.

Töltse le az [ojdbc8.jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) illesztőprogramot a helyi gépre. Később telepíti a Striim-fürtbe.

## <a name="configure-target-database"></a>Céladatbázis konfigurálása

Ebben a szakaszban az Azure Cosmos DB Cassandra API-fiókot fogja konfigurálni az adatáthelyezés célként.

1. Hozzon létre egy [Azure Cosmos DB Cassandra API-fiókot](create-cassandra-dotnet.md#create-a-database-account) az Azure Portalon.

1. Nyissa meg az Azure Cosmos-fiók **Adatkezelő** ablaktábláját. Új tároló létrehozásához válassza az **Új tábla** lehetőséget. Tegyük fel, hogy *termékeket* telepít át, és *adatokat rendel* az Oracle-adatbázisból az Azure Cosmos DB-be. Hozzon létre egy új Keyspace nevű **StriimDemo** egy Orders tárolóval. A tároló **kiépítése 1000 több mint abúza (ebben**a példában 1000 több mint egy példányban, de a számítási feladatok becsült átviteli keresztül) és **a /ORDER_ID** elsődleges kulcsként. Ezek az értékek a forrásadatoktól függően eltérőek lehetnek. 

   ![Cassandra API-fiók létrehozása](./media/cosmosdb-cassandra-api-migrate-data-striim/create-cassandra-api-account.png)

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Az Oracle konfigurálása az Azure Cosmos DB adatfolyamra

1. Térjünk vissza Striimhez. Mielőtt kapcsolatba lépne a Striimmel, telepítse a korábban letöltött Oracle JDBC illesztőprogramot.

1. Keresse meg az Azure Portalon üzembe helyezett Striim-példányt. Válassza a **Csatlakozás** gombot a felső menüsorban, és az **SSH** lapon másolja az URL-címet a **Bejelentkezés ben a VM helyi fiók** használatával mezőben.

   ![Az SSH URL-címének beszereznie](./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png)

1. Nyisson meg egy új terminálablakot, és futtassa az Azure Portalról másolt SSH-parancsot. Ez a cikk a MacOS terminálját használja, a Hasonló utasításokat a PuTTY vagy egy másik SSH-ügyfél használatával követheti a Windows-gépen. Amikor a rendszer kéri, írja be az **igen** értéket a folytatáshoz, és adja meg az előző lépésben a virtuális géphez beállított **jelszót.**

   ![Csatlakozás a Striim virtuális géphez](./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png)

1. Most nyisson meg egy új terminállapot a korábban letöltött **ojdbc8.jar** fájl másolásához. A következő SCP-paranccsal másolja a jar fájlt a helyi gépről az Azure-ban futó Striim-példány tmp mappájába:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   ![A Jar fájl másolása a helymeghatározó gépről a Striim-be](./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png)

1. Ezután keresse vissza az ablakot, ahol nem SSH a Striim példány és login, mint sudo. Helyezze át az **ojdbc8.jar** fájlt a **/tmp** könyvtárból a Striim példány **lib** könyvtárába a következő parancsokkal:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   ![A Jar fájl áthelyezése a lib mappába](./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png)


1. Ugyanabból a terminálablakból indítsa újra a Striim-kiszolgálót a következő parancsok végrehajtásával:

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Striim egy perc ig tart, hogy beindítsa. Ha meg szeretné tekinteni az állapotot, futtassa a következő parancsot: 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. Most lépjen vissza az Azure-ba, és másolja a Striim virtuális gép nyilvános IP-címét. 

   ![Striim VM IP-cím másolása](./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png)

1. A Striim webes felhasználói felületére való navigáláshoz nyisson meg egy új lapot a böngészőben, és másolja a nyilvános IP-címet, majd a következő t. 9080. Jelentkezzen be a **rendszergazdai** felhasználónév, valamint az Azure Portalon megadott rendszergazdai jelszó használatával.

   ![Bejelentkezés a Striim be](./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png)

1. Most megérkezik Striim honlapjára. Három különböző ablaktábla létezik : **Irányítópultok,** **alkalmazások**és **SourcePreview**. Az Irányítópultok ablaktábla lehetővé teszi az adatok valós idejű áthelyezését és megjelenítését. Az Alkalmazások ablaktábla tartalmazza a streamelési adatfolyamatokat vagy az adatfolyamokat. Az oldal jobb oldalán található a SourcePreview, ahol megtekintheti az adatokelőnézetet, mielőtt áthelyezi őket.

1. Válassza az **Alkalmazások** ablaktáblát, mert egyelőre erre az ablaktáblára összpontosítunk. Számos mintaalkalmazás létezik, amelyek segítségével megismerheti a Striim-et, azonban ebben a cikkben saját ot hozhat létre. Válassza az **Alkalmazás hozzáadása** gombot a jobb felső sarokban.

   ![A Striim alkalmazás hozzáadása](./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png)

1. A Striim-alkalmazások többféleképpen is létrehozható. Ebben a forgatókönyvben válassza a **Kezdő nulláról** lehetőséget.

   ![Az alkalmazás indítása teljesen újjá](./media/cosmosdb-cassandra-api-migrate-data-striim/start-app-from-scratch.png)

1. Adjon egy rövid nevet az alkalmazás, valami ilyesmi **oraToCosmosDB** és válassza **a Mentés**lehetőséget .

   ![Új alkalmazás létrehozása](./media/cosmosdb-cassandra-api-migrate-data-striim/create-new-application.png)

1. Meg fogja érkeznie a Flow Designer, ahol húzza ki a dobozból összekötők létrehozásához a streamelési alkalmazásokat. Írja be az **Oracle** kifejezést a keresősávba, húzza az **Oracle CDC-forrást** az alkalmazás vászonjára.  

   ![Oracle CDC forrás](./media/cosmosdb-cassandra-api-migrate-data-striim/oracle-cdc-source.png)

1. Adja meg az Oracle-példány forráskonfigurációs tulajdonságait. A forrásnév csak egy elnevezési konvenció a Striim alkalmazáshoz, használhat egy nevet, például **src_onPremOracle.** Adjon meg más adatokat is, például az adapter típusát, a kapcsolat URL-címét, felhasználónevét, jelszavát, táblanevét. A folytatáshoz válassza a **Mentés** gombot.

   ![Forrásparaméterek konfigurálása](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-source-parameters.png)

1. Most kattintson a hullám ikonja az adatfolyam a cél Azure Cosmos DB-példány csatlakoztatásához. 

   ![Csatlakozás a célhoz](./media/cosmosdb-cassandra-api-migrate-data-striim/connect-to-target.png)

1. A cél konfigurálása előtt győződjön meg arról, [hogy baltimore-i főtanúsítványt adott hozzá a Striim Java-környezetéhez.](/azure/java/java-sdk-add-certificate-ca-store#to-add-a-root-certificate-to-the-cacerts-store)

1. Adja meg a cél Azure Cosmos DB-példány konfigurációs tulajdonságait, és válassza a **Mentés** lehetőséget a folytatáshoz. Itt vannak a legfontosabb paramétereket megjegyezni:

   * **Adapter** – A **DatabaseWriter**használata . Az Azure Cosmos DB Cassandra API-ba íráskor a DatabaseWriter szükséges. A Cassandra driver 3.6.0 a Striim-mel van csomagolva. Ha a DatabaseWriter meghaladja az Azure Cosmos-tárolóban kiépített rúmia konkretinusok száma, az alkalmazás összeomlik.

   * **Kapcsolat URL-címe** – Adja meg az Azure Cosmos DB JDBC kapcsolat URL-címét. Az URL formátuma`jdbc:cassandra://<contactpoint>:10350/<databaseName>?SSL=true`

   * **Felhasználónév** – Adja meg az Azure Cosmos-fiók nevét.
   
   * **Jelszó** – Adja meg az Azure Cosmos-fiók elsődleges kulcsát.

   * **Táblák** – A céltábláknak elsődleges kulcsokkal kell rendelkezniük, és az elsődleges kulcsok nem frissíthetők.

   ![Céltulajdonságok konfigurálása](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters1.png)

   ![Céltulajdonságok konfigurálása](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters2.png)

1. Most pedig lefuttatjuk a Striim alkalmazást. A felső menüsorban válassza a **Létrehozva**lehetőséget, majd **az Alkalmazás telepítése**lehetőséget. A központi telepítési ablakban megadhatja, hogy az alkalmazás bizonyos részeit a központi telepítési topológia meghatározott részein kívánja-e futtatni. Mivel az Azure-on keresztül egyszerű központi telepítéstopológiában futunk, az alapértelmezett beállítást fogjuk használni.

   ![Az alkalmazás üzembe helyezése](./media/cosmosdb-cassandra-api-migrate-data-striim/deploy-the-app.png)


1. Most, megyünk előre, és előnézet az adatfolyamot, hogy az adatok áramlik át a Striim. Kattintson a hullám ikonra, és kattintson a mellette lévő szem ikonra. Üzembe helyezés után megtekintheti az adatfolyamot, hogy az adatok átfolynak. Válassza ki a **hullám** ikont és a mellette lévő **szemgolyót.** Válassza a legfelső menüsor **Telepített** gombját, majd az **Alkalmazás indítása**parancsot.

   ![Az alkalmazás indítása](./media/cosmosdb-cassandra-api-migrate-data-striim/start-the-app.png)

1. A **CDC(Change Data Capture)** olvasó használatával a Striim csak új módosításokat vesz fel az adatbázisban. Ha a forrástáblákon keresztül adatáramlik, látni fogja azt. Mivel azonban ez egy mintatábla, a forrás, amely nem kapcsolódik semmilyen alkalmazáshoz. Ha mintaadat-generátort használ, események láncolatát szúrhatja be az Oracle-adatbázisba.

1. Látni fogja, hogy az adatok a Striim platformon keresztül áramlanak. Striim felveszi a táblához társított összes metaadatot is, ami hasznos az adatok figyeléséhez és annak biztosításához, hogy az adatok a megfelelő célra kerüljön.

   ![A CDC-folyamat beállítása](./media/cosmosdb-cassandra-api-migrate-data-striim/setup-cdc-pipeline.png)

1. Végül jelentkezzen be az Azure-ba, és keresse meg az Azure Cosmos-fiókját. Frissítse az Adatkezelőt, és láthatja, hogy az adatok megérkeztek. 

A Striim-megoldás azure-beli használatával folyamatosan áttelepítheti az adatokat az Azure Cosmos DB-be különböző forrásokból, például oracle, Cassandra, MongoDB és további más forrásokból az Azure Cosmos DB-ba. Ha többet szeretne megtudni, kérjük, látogasson el a [Striim weboldalára](https://www.striim.com/), töltse le a [Striim ingyenes 30 napos próbaverzióját,](https://go2.striim.com/download-free-trial)és az áttelepítési útvonal Striim-kel történő beállításakor nyújtson be [támogatási kérelmet.](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>További lépések

* Ha adatokat telepít át az Azure Cosmos DB SQL API-ba, olvassa el, [hogyan telepítheti át az adatokat a Cassandra API-fiókba a Striim használatával](cosmosdb-sql-api-migrate-data-striim.md)

* [Az adatok figyelése és hibakeresése az Azure Cosmos DB-mérőszámokkal](use-metrics.md)
