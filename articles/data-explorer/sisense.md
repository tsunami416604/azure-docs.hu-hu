---
title: Azure Data Explorer használatával Sisense adatainak megjelenítése
description: Ebben a cikkben megtudhatja, hogyan állítsa be az Azure Data Explorer adatforrásként Sisense, és megjelenítheti az adatokat.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: f0840b90e1036c23fa58d94515bfeb035299c07f
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358183"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>Az Azure Data Explorer Sisense adatainak megjelenítése

Sisense egy üzleti intelligencia platform, amely lehetővé teszi, hogy analytics olyan alkalmazások fordítása, interaktív felhasználói élményt. Az üzletiintelligencia- és szoftver jelentése irányítópult lehetővé teszi eléréséhez, és kombinálhatja az adatokat néhány kattintással. Források strukturált és strukturálatlan adatokhoz csatlakozhat, a minimális parancsfájlok és a kódolási több forrásból táblák és interaktív webes irányítópultokat és jelentéseket hozhat létre. Ebből a cikkből megismerheti, hogyan beállítása az Azure Data Explorer adatforrásként Sisense, és a egy minta-fürtből származó adatokat jelenítheti meg lesz.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk a következőkre lesz szüksége:

* [Töltse le és telepítse a Sisense alkalmazás](https://documentation.sisense.com/latest/getting-started/download-install.htm) 

* Hozzon létre egy fürtöt és a StormEvents mintaadatokat tartalmazó adatbázis. További információkért lásd: [a rövid útmutató: Hozzon létre egy Azure Data Explorer fürt és az adatbázis](create-cluster-database-portal.md) és [mintaadatok betöltése az Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>Csatlakozás az Azure Data Explorer JDBC-összekötő segítségével Sisense irányítópultokon

1. Töltse le, és másolja az alábbi jar-fájlok, a legújabb verziókat *... \Sisense\DataConnectors\jdbcdrivers\adx* 

    * az aktiválás-1.1.jar
    * adal4j-1.6.0.jar
    * commons-codec-1.10.jar
    * commons-collections4-4.1.jar
    * commons-lang3-3.5.jar
    * gson-2.8.0.jar
    * jcip-annotations-1.0-1.jar
    * json-smart-1.3.1.jar
    * lang-tag-1.4.4.jar
    * mail-1.4.7.jar
    * mssql-jdbc-7.2.1.jre8.jar
    * nimbus-jose-jwt-7.0.1.jar
    * oauth2-oidc-sdk-5.24.1.jar
    * slf4j-api-1.7.21.jar
    
1. Nyissa meg **Sisense** alkalmazást.
1. Válassza ki **adatok** lapot, és válasszon **+ ElastiCube** egy új ElastiCube modell létrehozásához.
    
    ![Válassza ki a ElastiCube](media/sisense/data-select-elasticube.png)

1. A **hozzáadása új ElastiCube modell**, nevezze el a ElastiCube modell és **mentése**.
   
    ![Új ElastiCube modell hozzáadása](media/sisense/add-new-elasticube-model.png)

1. Válassza ki **+ adatbázis**.

    ![Adatok kijelölése gomb](media/sisense/select-data.png)

1. A **kiválasztása összekötő** lapra, válassza ki **általános JDBC** összekötő.

    ![Válassza ki a JDBC-összekötő](media/sisense/select-connector.png)

1. Az a **Connect** lapra, végezze el a következő mezőket a **általános JDBC** összekötő, és válassza ki **tovább**.

    ![JDBC-összekötő-beállítások](media/sisense/jdbc-connector.png)

    |Mező |Leírás |
    |---------|---------|
    |Kapcsolati sztring     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |JDBC JARs mappa  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |Illesztőprogram-osztály neve    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |Felhasználónév   |    AAD-felhasználónév     |
    |Jelszó     |   AAD felhasználói jelszó      |

1. Az a **adatok válassza** fülre, keressen rá **adatbázis választása** , válassza ki a megfelelő adatbázist, amelyhez engedéllyel rendelkezik. Ebben a példában válassza *Teszt1*.

    ![adatbázis kiválasztása](media/sisense/select-database.png)

1. A *tesztelése* (adatbázisnév) panelen:
    1. Válassza ki a tábla neve a táblázat előnézetét, és tekintse meg a tábla oszlopneveit. Eltávolíthatja a szükségtelen oszlopok.
    1. Jelölje be a jelölőnégyzetet, válassza ki, hogy a táblázat a kapcsolódó tábla. 
    1. Válassza a **Done** (Kész) lehetőséget.

    ![Tábla kiválasztása](media/sisense/select-table-see-columns.png)    

1. Válassza ki **összeállítása** az adatkészlet létrehozásához. 

    * Az a **hozhat létre** ablakban válassza **összeállítása**.

      ![Build ablak](media/sisense/build-window.png)

    * Várjon, amíg a teljes, és adja meg a kiépítési folyamat éppen **létrehozása sikerült**.

      ![Sikeres létrehozás](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>Sisense irányítópultok létrehozása

1. A **Analytics** lapon jelölje be **+**  >  **új irányítópult** irányítópultokat létrehozni ehhez az adatkészlethez.

    ![Új irányítópult](media/sisense/new-dashboard.png)

1. Válasszon egy irányítópultot, és válassza ki **létrehozás**. 

    ![irányítópult létrehozása](media/sisense/create-dashboard.png)

1. Alatt **új Widget**válassza **+ válassza ki az adatok** hozhat létre egy új widget. 

    ![Mezők hozzáadása StormEvents irányítópult](media/sisense/storm-dashboard-add-field.png)  

1. Válassza ki **+ további adatok hozzáadása** további oszlopokat is hozzáadhat a gráfhoz. 

    ![További adatok hozzáadása a diagramhoz](media/sisense/add-more-data.png)

1. Válassza ki **+ Widget** más widgetek létrehozása. Fogd és vidd widgetek az irányítópulton átrendezéséhez.

    ![A Storm irányítópultja](media/sisense/final-dashboard.png)

Most már a vizuális elemzési adatait, további irányítópultok létrehozása és átalakíthatja az adatokat ahhoz, hogy az befolyásolná az üzleti információkká.

## <a name="next-steps"></a>További lépések

* [Lekérdezések írása az Azure Data Explorerhez](write-queries.md)

