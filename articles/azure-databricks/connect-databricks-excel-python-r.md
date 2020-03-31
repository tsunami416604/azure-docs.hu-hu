---
title: 'Csatlakozás Azure Databricks-hez Excelből, Pythonból vagy R-ből '
description: Ismerje meg, hogyan használhatja a Simba-illesztőprogramot az Azure Databricks Excelhez, Pythonhoz vagy R-hez való csatlakoztatásához.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: mamccrea
ms.openlocfilehash: f7494d36cf9b16ac6c7a1287a6ff96dd2285c6e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73601950"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Csatlakozás Azure Databricks-hez Excelből, Pythonból vagy R-ből

Ebben a cikkben megtudhatja, hogyan használhatja a Databricks ODBC-illesztőprogramot az Azure Databricks microsoft Excel, Python vagy R nyelvhez való csatlakoztatásához. Miután létrejött a kapcsolat, az Azure Databricks az Excel, Python vagy R ügyfelek az adatok eléréséhez. Az ügyfelek segítségével tovább elemezheti az adatokat. 

## <a name="prerequisites"></a>Előfeltételek

* Rendelkeznie kell egy Azure Databricks-munkaterülettel, egy Spark-fürttel és a fürthöz társított mintaadatokkal. Ha még nem rendelkezik ezekkel az előfeltételekkel, végezze el a rövid útmutatót a [Spark-feladat futtatása az Azure Databricks az Azure Portalon.](quickstart-create-databricks-workspace-portal.md)

* Töltse le a Databricks ODBC illesztőprogramot a [Databricks illesztőprogram letöltési oldaláról.](https://databricks.com/spark/odbc-driver-download) Telepítse a 32 bites vagy 64 bites verziót attól függően, hogy az alkalmazás honnan szeretne csatlakozni az Azure Databricks-hez. Ha például az Excelből szeretne csatlakozni, telepítse az illesztőprogram 32 bites verzióját. Az R és Python csatlakozáshoz telepítse az illesztőprogram 64 bites verzióját.

* Hozzon létre egy személyes hozzáférési jogkivonatot a Databricks-ben. További információt a [Token-kezelés .](/azure/databricks/dev-tools/api/latest/authentication)

## <a name="set-up-a-dsn"></a>DSN beállítása

Az adatforrás neve (DSN) egy adott adatforrás adatait tartalmazza. Egy ODBC-illesztőprogramnak szüksége van erre a dsn-re az adatforráshoz való csatlakozáshoz. Ebben a szakaszban egy dsn, amely a Databricks ODBC-illesztőprogrammal használható azure Databricks-hez való csatlakozáshoz olyan ügyfelekről, például a Microsoft Excel, python vagy R.

1. Az Azure Databricks munkaterületről keresse meg a Databricks-fürt.

    ![Nyílt Databricks-fürt](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "Nyílt Databricks-fürt")

2. A **Konfiguráció** lapon kattintson a **JDBC/ODBC** fülre, és másolja a **Kiszolgálóállomás** és a **HTTP elérési út értékeit.** Ezekre az értékekre a cikkben ismertetett lépések végrehajtásához szükséges.

    ![Databricks-konfiguráció beszereznie](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Databricks-konfiguráció beszereznie")

3. A számítógépen indítsa el az **ODBC Adatforrás alkalmazást** (32 vagy 64 bites) az alkalmazástól függően. Ha az Excelprogramból szeretne csatlakozni, használja a 32 bites verziót. Az R és python csatlakozáshoz használja a 64 bites verziót.

    ![ODBC indítása](./media/connect-databricks-excel-python-r/launch-odbc-app.png "ODBC alkalmazás indítása")

4. A **Felhasználói dsn (ÉÉ)** lap **Hozzáadás gombjára.** Az **Új adatforrás létrehozása** párbeszédpanelen jelölje ki a **Simba Spark ODBC illesztőprogramot,** majd kattintson a **Befejezés gombra.**

    ![ODBC indítása](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "ODBC alkalmazás indítása")

5. A **Simba Spark ODBC illesztőprogram** párbeszédpanelen adja meg a következő értékeket:

    ![A DSN konfigurálása](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "A DSN konfigurálása")

    Az alábbi táblázat a párbeszédpanelen megadandó értékekről tartalmaz tájékoztatást.
    
    |Mező  | Érték  |
    |---------|---------|
    |**Adatforrás neve**     | Adja meg az adatforrás nevét.        |
    |**Állomás(ok)**     | Adja meg a *Kiszolgálóállomásnév*Databricks munkaterületéről másolt értéket.        |
    |**Port**     | Írja be *a 443-as értéket.*        |
    |**Hitelesítési** > **mechanizmus**     | Válassza *a Felhasználónév és jelszó*lehetőséget.        |
    |**Felhasználónév**     | Írja be *a token*.        |
    |**Jelszó**     | Adja meg a Databricks munkaterületről másolt token értéket. |
    
    Hajtsa végre az alábbi további lépéseket a DSN beállítási párbeszédpanelén.
    
    * Kattintson **a HTTP-beállítások gombra.** A megnyíló párbeszédpanelen illessze be a Databricks-munkaterületről másolt *HTTP-elérési út* értékét. Kattintson az **OK** gombra.
    * Kattintson **az SSL-beállítások gombra.** A megnyíló párbeszédpanelen jelölje be az **SSL engedélyezése** jelölőnégyzetet. Kattintson az **OK** gombra.
    * Kattintson a **Tesztelés** az Azure Databricks kapcsolat teszteléséhez. A konfiguráció mentéséhez kattintson az **OK** gombra.
    * Az **ODBC adatforrás-felügyelő** párbeszédpanelen kattintson az **OK**gombra.

Most már beállította a DSN-t. A következő szakaszokban ezzel a DSN-lel csatlakozhat az Azure Databricks Excel, Python vagy R.

## <a name="connect-from-microsoft-excel"></a>Csatlakozás a Microsoft Excelből

Ebben a szakaszban adatokat az Azure Databricks a Microsoft Excel a korábban létrehozott dsn használatával. Mielőtt elkezdené, győződjön meg arról, hogy a Microsoft Excel telepítve van a számítógépen. Az Excel próbaverzióját a [Microsoft Excel próbaverziójából is használhatjuk.](https://products.office.com/excel)

1. Nyissunk meg egy üres munkafüzetet a Microsoft Excelben. Az **Adatok** menüszalagon kattintson az **Adatok beszedése gombra.** Kattintson **a Más forrásokból,** majd az **ODBC forrásból**parancsra.

    ![Az ODBC indítása az Excelből](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "Az ODBC indítása az Excelből")

2. Az **ODBC-től** párbeszédpanelen jelölje ki a korábban létrehozott dsn-t, majd kattintson az **OK**gombra.

    ![DSN kiválasztása](./media/connect-databricks-excel-python-r/excel-select-dsn.png "DSN kiválasztása")

3. Ha a rendszer hitelesítő adatokat kér, a felhasználónévhez írja be a **jogkivonatot.** A jelszó, adja meg a token értékét, amely a Databricks munkaterületről beolvasott.

    ![Hitelesítő adatok megadása databricks](./media/connect-databricks-excel-python-r/excel-databricks-token.png "DSN kiválasztása")

4. A navigátor ablakban jelölje ki az Excelbe betölteni kívánt táblát a Databricks alkalmazásban, majd kattintson a **Betöltés gombra.** 

    ![A dta betöltése az Excelbe](./media/connect-databricks-excel-python-r/excel-load-data.png "A dta betöltése az Excelbe")

Miután az Excel-munkafüzetben lévő adatokat elvégezte, analitikus műveleteket hajthat végre rajta.

## <a name="connect-from-r"></a>Csatlakozás az R-ről

> [!NOTE]
> Ez a szakasz az asztalon futó R Studio-ügyfél Azure Databricks használatával való integrálásáról nyújt tájékoztatást. Az R Studio azure Databricks-fürtön való használatáról az [R Studio az Azure Databricks szolgáltatásban](/azure/databricks/spark/latest/sparkr/rstudio)című témakörben talál útmutatást.

Ebben a szakaszban egy R nyelvű IDE-t használ az Azure Databricks-ben elérhető adatok hivatkozásához. Mielőtt elkezdené, a számítógépen telepítve kell lennie a következőknek.

* Ide az R nyelvhez. Ez a cikk az RStudio for Desktop alkalmazást használja. Telepítheti azt [R Studio letöltés](https://www.rstudio.com/products/rstudio/download/).
* Ha az RStudio for Desktop-ot használja IDE-ként, telepítse a Microsoft R client programot is a alkalmazásból. [https://aka.ms/rclient/](https://aka.ms/rclient/) 

Nyissa meg az RStudio alkalmazást, és tegye a következő lépéseket:

- Hivatkozzon `RODBC` a csomagra. Ez lehetővé teszi, hogy az Azure Databricks-hez a korábban létrehozott dsn használatával csatlakozzon.
- Hozzon létre kapcsolatot a dsn használatával.
- SQL-lekérdezést futtataz adatokat az Azure Databricks. A következő kódrészletben *radio_sample_data* egy olyan tábla, amely már létezik az Azure Databricks.
- Hajtson végre bizonyos műveleteket a lekérdezésen a kimenet ellenőrzéséhez. 

A következő kódrészlet hajtja végre a következő feladatokat:

    # reference the 'RODBC' package
    require(RODBC)
    
    # establish a connection using the DSN you created earlier
    conn <- odbcConnect("<ENTER DSN NAME HERE>")
    
    # run a SQL query using the connection you created
    res <- sqlQuery(conn, "SELECT * FROM radio_sample_data")
    
    # print out the column names in the query output
    names(res) 
        
    # print out the number of rows in the query output
    nrow (res)

## <a name="connect-from-python"></a>Csatlakozás pythonból

Ebben a szakaszban egy Python IDE-t (például IDLE) használ az Azure Databricks-ben elérhető adatok hivatkozásához. Mielőtt elkezdené, hajtsa végre az alábbi előfeltételeket:

* Telepítse python [innen](https://www.python.org/downloads/). A Python ezen a hivatkozásról történő telepítése az IDLE-t is telepíti.

* A számítógép parancssorából telepítse `pyodbc` a csomagot. Futtassa az alábbi parancsot:

      pip install pyodbc

Nyissa meg az IDLE-t, és tegye a következő lépéseket:

- Importálja `pyodbc` a csomagot. Ez lehetővé teszi, hogy az Azure Databricks-hez a korábban létrehozott dsn használatával csatlakozzon.
- Hozzon létre kapcsolatot a korábban létrehozott dsn használatával.
-  Sql-lekérdezés futtatása a létrehozott kapcsolat használatával. A következő kódrészletben *radio_sample_data* egy olyan tábla, amely már létezik az Azure Databricks.
- A kimenet ellenőrzéséhez hajtson végre műveleteket a lekérdezésen.

A következő kódrészlet hajtja végre a következő feladatokat:

```python
# import the `pyodbc` package:
import pyodbc

# establish a connection using the DSN you created earlier
conn = pyodbc.connect("DSN=<ENTER DSN NAME HERE>", autocommit=True)

# run a SQL query using the connection you created
cursor = conn.cursor()
cursor.execute("SELECT * FROM radio_sample_data")

# print the rows retrieved by the query.
for row in cursor.fetchall():
    print(row)
```

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni a forrásokról, amelyekből adatokat importálhat az Azure Databricks-be, olvassa el [az Azure Databricks adatforrásai](/azure/databricks/data/data-sources/index)


