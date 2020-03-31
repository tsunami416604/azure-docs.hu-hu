---
title: Az Azure Data Explorer adatainak megjelenítése a Sisense használatával
description: Ebből a cikkből megtudhatja, hogyan állíthatja be az Azure Data Explorert a Sisense adatforrásaként, és hogyan jelenítheti meg az adatokat.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: f0840b90e1036c23fa58d94515bfeb035299c07f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66358183"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>Az Azure Data Explorer adatainak megjelenítése a Sisense-ben

Sisense egy elemzési üzleti intelligencia platform, amely lehetővé teszi, hogy olyan elemző alkalmazásokat, amelyek rendkívül interaktív felhasználói élményt. Az üzleti intelligencia és az irányítópult-jelentéskészítő szoftver lehetővé teszi az adatok elérését és kombinálását néhány kattintással. Strukturált és strukturálatlan adatforrásokhoz csatlakozhat, minimális parancsfájl-kódolással és kódolással több forrásból származó táblákat illeszthet össze, valamint interaktív webes irányítópultokat és jelentéseket hozhat létre. Ebben a cikkben megtudhatja, hogyan állíthatja be az Azure Data Explorert a Sisense adatforrásaként, és hogyan jelenítheti meg a mintafürtből származó adatokat.

## <a name="prerequisites"></a>Előfeltételek

A cikk befejezéséhez a következőkre van szükség:

* [A Sisense alkalmazás letöltése és telepítése](https://documentation.sisense.com/latest/getting-started/download-install.htm) 

* Hozzon létre egy fürtöt és adatbázist, amely tartalmazza a StormEvents mintaadatokat. További információ: [Gyorsútmutató: Azure Data Explorer-fürt és-adatbázis létrehozása,](create-cluster-database-portal.md) valamint [mintaadatok betöltése az Azure Data Explorerbe című témakörben.](ingest-sample-data.md)

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>Csatlakozás Sisense-irányítópultokhoz az Azure Data Explorer JDBC-összekötőjével

1. Töltse le és másolja a legújabb verzióit a következő jar fájlokat *.. \Sisense\DataConnectors\jdbcdrivers\adx* 

    * aktiválás-1.1.jar
    * adal4j-1.6.0.jar
    * commons-codec-1.10.jar
    * commons-gyűjtemények4-4.1.jar
    * közhely-lang3-3.5.jar
    * gson-2.8.0.jar
    * jcip-annotációk-1.0-1.jar
    * json-smart-1.3.1.jar
    * lang-tag-1.4.4.jar
    * mail-1.4.7.jar
    * mssql-jdbc-7.2.1.jre8.jar
    * nimbus-jose-jwt-7.0.1.jar
    * oauth2-oidc-sdk-5.24.1.jar
    * slf4j-api-1.7.21.jar
    
1. Nyissa meg **a Sisense** alkalmazást.
1. Új ElastiCube modell létrehozásához válassza az **Adatok** fület, és válassza a **+ElastiCube** billentyűt.
    
    ![Válassza az ElastiCube lehetőséget](media/sisense/data-select-elasticube.png)

1. Az **Új ElasztiCube modell hozzáadása területen**nevezze el az ElastiCube modellt, és mentse a **fájlt.**
   
    ![Új ElastiCube modell hozzáadása](media/sisense/add-new-elasticube-model.png)

1. Válassza a **+ Adatok**lehetőséget.

    ![Adatok kijelölése gomb](media/sisense/select-data.png)

1. Az **Összekötő kiválasztása** lapon válassza az Általános **JDBC-összekötő** lehetőséget.

    ![JDBC-csatlakozó kiválasztása](media/sisense/select-connector.png)

1. A **Csatlakozás** lapon töltse ki az **Általános JDBC-összekötő** következő mezőit, és válassza a **Tovább**gombot.

    ![JDBC összekötő beállításai](media/sisense/jdbc-connector.png)

    |Mező |Leírás |
    |---------|---------|
    |Kapcsolati karakterlánc     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |JDBC JARs mappa  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |A járművezető osztályának neve    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |Felhasználónév   |    AAD felhasználónév     |
    |Jelszó     |   AAD felhasználói jelszó      |

1. Az **Adatok kiválasztása** lapon keressen rá a **Keresés: Adatbázis kiválasztása** elemre, és jelölje ki azt az adatbázist, amelyhez engedélyes van. Ebben a példában válassza a *test1*lehetőséget.

    ![adatbázis kiválasztása](media/sisense/select-database.png)

1. A *test* tesztablaktáblában:
    1. Jelölje ki a táblázat nevét a tábla előnézetének megtekintéséhez és a táblázat oszlopnevének megtekintéséhez. Eltávolíthatja a felesleges oszlopokat.
    1. Jelölje be a megfelelő tábla jelölőnégyzetét a tábla kijelöléséhez. 
    1. Válassza a **Done** (Kész) lehetőséget.

    ![tábla kiválasztása](media/sisense/select-table-see-columns.png)    

1. Válassza **a Build** lehetőséget az adatkészlet létrehozásához. 

    * A **Build** ablakban válassza a **Build**lehetőséget.

      ![Összeállítási ablak](media/sisense/build-window.png)

    * Várjon, amíg a létrehozási folyamat befejeződik, majd válassza **a Sikeres összeállítás**lehetőséget.

      ![A build sikerült](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>Sisense-irányítópultok létrehozása

1. Az **Analytics** lapon **+**  > válassza az **Új irányítópult** lehetőséget, ha irányítópultokat szeretne létrehozni ezen az adatkészleten.

    ![Új irányítópult](media/sisense/new-dashboard.png)

1. Válasszon egy irányítópultot, és válassza a **Létrehozás lehetőséget.** 

    ![Irányítópult létrehozása](media/sisense/create-dashboard.png)

1. Az **Új widget csoportban**válassza **a + Adatok kiválasztása lehetőséget** új widget létrehozásához. 

    ![Mezők hozzáadása a StormEvents irányítópulthoz](media/sisense/storm-dashboard-add-field.png)  

1. Válassza **a + További adatok hozzáadása** lehetőséget, ha további oszlopokat szeretne hozzáadni a diagramhoz. 

    ![További adatok hozzáadása a diagramhoz](media/sisense/add-more-data.png)

1. Válassza a **+ Widget lehetőséget** egy másik widget létrehozásához. Az irányítópult átrendezéséhez húzza a widgeteket.

    ![A Storm irányítópultja](media/sisense/final-dashboard.png)

Most már vizuális elemzéssel is megismerheti az adatokat, további irányítópultokat hozhat létre, és az adatokat használható elemzési adatokká alakíthatja, hogy hatással legyen a vállalkozására.

## <a name="next-steps"></a>További lépések

* [Lekérdezések írása az Azure Data Explorerhez](write-queries.md)

