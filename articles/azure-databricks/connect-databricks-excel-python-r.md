---
title: 'Kapcsolódás Azure Databricks Excel, Python vagy R rendszerből '
description: Ismerje meg, hogyan használható a Simba-illesztőprogram a Azure Databricks Excel, Python vagy R rendszerhez való összekapcsolásához.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: mamccrea
ms.openlocfilehash: f7494d36cf9b16ac6c7a1287a6ff96dd2285c6e2
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "73601950"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Kapcsolódás Azure Databricks Excel, Python vagy R rendszerből

Ebből a cikkből megtudhatja, hogyan használhatja a Databricks ODBC-illesztőt a Azure Databricks Microsoft Excel, Python vagy R nyelvvel való összekapcsolásához. Miután létrehozta a kapcsolatot, Azure Databricks az Excel-, Python-vagy R-ügyfelekről férhet hozzá az adatokhoz. Az ügyfeleket az adatelemzés további elemzésére is használhatja. 

## <a name="prerequisites"></a>Előfeltételek

* Rendelkeznie kell egy Azure Databricks-munkaterülettel, egy Spark-fürttel és a fürthöz társított mintaadatok. Ha még nem rendelkezik ezekkel az előfeltételekkel, hajtsa végre a gyors üzembe helyezési útmutatót [Azure Databricks a Azure Portal használatával](quickstart-create-databricks-workspace-portal.md).

* Töltse le a Databricks ODBC-illesztőt a [Databricks-illesztőprogram letöltési oldaláról](https://databricks.com/spark/odbc-driver-download). Telepítse a 32 bites vagy a 64 bites verziót attól függően, hogy melyik alkalmazásból szeretne csatlakozni a Azure Databrickshoz. Például az Excelből való kapcsolódáshoz telepítse az illesztőprogram 32 bites verzióját. Az R és a Python használatával való kapcsolódáshoz telepítse az illesztőprogram 64 bites verzióját.

* Hozzon létre egy személyes hozzáférési jogkivonatot a Databricks-ben. Útmutatásért lásd a [jogkivonat-kezelés](/azure/databricks/dev-tools/api/latest/authentication)című témakört.

## <a name="set-up-a-dsn"></a>DSN beállítása

Az adatforrás neve (DSN) az adott adatforrásra vonatkozó információkat tartalmazza. Egy ODBC-illesztőprogramnak szüksége van erre az ADATFORRÁSNEVET egy adatforráshoz való kapcsolódáshoz. Ebben a szakaszban egy olyan DSN-t állít be, amely a Databricks ODBC-illesztővel való Azure Databricks kapcsolódáshoz használható olyan ügyfelekhez, mint a Microsoft Excel, a Python vagy az R.

1. A Azure Databricks munkaterületen navigáljon a Databricks-fürthöz.

    ![Databricks-fürt megnyitása](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "Databricks-fürt megnyitása")

2. A **konfiguráció** lapon kattintson a **JDBC/ODBC** lapra, és másolja a **kiszolgálói állomásnév** és a **http elérési útja**értékeit. Ezekre az értékekre szüksége lesz a cikk lépéseinek elvégzéséhez.

    ![Databricks-konfiguráció beolvasása](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Databricks-konfiguráció beolvasása")

3. A számítógépen az alkalmazástól függően indítsa el az **ODBC adatforrások** alkalmazást (32 bites vagy 64 bites). Az Excelből való kapcsolódáshoz használja a 32 bites verziót. Az R és a Python használatával való kapcsolódáshoz használja a 64 bites verziót.

    ![ODBC elindítása](./media/connect-databricks-excel-python-r/launch-odbc-app.png "ODBC-alkalmazás elindítása")

4. A **felhasználói DSN** lapon kattintson a **Hozzáadás**gombra. Az **új adatforrás létrehozása** párbeszédpanelen válassza a **Simba Spark ODBC-illesztőt**, majd kattintson a **Befejezés**gombra.

    ![ODBC elindítása](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "ODBC-alkalmazás elindítása")

5. A **Simba Spark ODBC-illesztő** párbeszédpanelen adja meg a következő értékeket:

    ![DSN konfigurálása](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "DSN konfigurálása")

    A következő táblázat a párbeszédpanelen megadható értékekkel kapcsolatos információkat tartalmazza.
    
    |Mező  | Érték  |
    |---------|---------|
    |**Adatforrás neve**     | Adja meg az adatforrás nevét.        |
    |**Állomás (ok)**     | Adja meg azt az értéket, amelyet a Databricks munkaterületről másolt a *kiszolgálói állomásnévhez*.        |
    |**Port**     | Adja meg a *443*értéket.        |
    |**Hitelesítési** > **mechanizmus**     | Válassza ki *a felhasználónevet és a jelszót*.        |
    |**Felhasználónév**     | Adja meg a *tokent*.        |
    |**Jelszó**     | Adja meg a Databricks munkaterületről másolt jogkivonat értékét. |
    
    Hajtsa végre a következő további lépéseket a DSN beállítása párbeszédpanelen.
    
    * Kattintson a **http-beállítások**elemre. A megnyíló párbeszédpanelen illessze be a Databricks-munkaterületről másolt *http-elérési út* értékét. Kattintson az **OK** gombra.
    * Kattintson az **SSL-beállítások**elemre. A megnyíló párbeszédpanelen jelölje be az **SSL engedélyezése** jelölőnégyzetet. Kattintson az **OK** gombra.
    * Kattintson a **teszt** gombra a Azure Databrickshoz való kapcsolódás ellenőrzéséhez. A konfiguráció mentéséhez kattintson **az OK** gombra.
    * Az **ODBC-adatforrás rendszergazdája** párbeszédpanelen kattintson az **OK**gombra.

Most már beállította az ADATFORRÁSNEVET. A következő részekben ezt az ADATFORRÁSNEVET használja az Excel-, Python-vagy R-Azure Databrickshoz való kapcsolódáshoz.

## <a name="connect-from-microsoft-excel"></a>A Microsoft Excelben való kapcsolat

Ebben a szakaszban a korábban létrehozott DSN használatával kéri le a Azure Databricks adatait a Microsoft Excelben. Mielőtt elkezdené, győződjön meg arról, hogy a Microsoft Excel telepítve van a számítógépen. Használhatja az Excel próbaverzióját a [Microsoft Excel próbaverziós hivatkozásával](https://products.office.com/excel).

1. Nyisson meg egy üres munkafüzetet a Microsoft Excelben. Az **adatok** menüszalagon kattintson az **adatok lekérése**elemre. Kattintson **a más forrásokból** lehetőségre, majd **az ODBC**elemre.

    ![ODBC elindítása az Excelből](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "ODBC elindítása az Excelből")

2. Az **ODBC-ből** párbeszédpanelen válassza ki a korábban létrehozott DSN-t, majd kattintson **az OK**gombra.

    ![ADATFORRÁS kiválasztása](./media/connect-databricks-excel-python-r/excel-select-dsn.png "ADATFORRÁS kiválasztása")

3. Ha a rendszer hitelesítő adatokat kér, a Felhasználónév mezőben adja meg a **tokent**. A Password (jelszó) mezőben adja meg a Databricks munkaterületről lekért jogkivonat-értéket.

    ![Hitelesítő adatok megadása a Databricks](./media/connect-databricks-excel-python-r/excel-databricks-token.png "ADATFORRÁS kiválasztása")

4. A navigátor ablakban válassza ki azt a Databricks, amelyet az Excelbe szeretne betölteni, majd kattintson a **Betöltés**elemre. 

    ![DTA betöltése az Excelbe](./media/connect-databricks-excel-python-r/excel-load-data.png "DTA betöltése az Excelbe")

Miután az Excel-munkafüzetben található adatokkal rendelkezik, elemzési műveleteket végezhet rajta.

## <a name="connect-from-r"></a>Kapcsolat az R-ből

> [!NOTE]
> Ez a szakasz arról nyújt tájékoztatást, hogyan integrálható az asztali számítógépeken futó R Studio-ügyfél Azure Databricks használatával. Az R Studio Azure Databricks-fürtön való használatáról az [r Studio on Azure Databricks](/azure/databricks/spark/latest/sparkr/rstudio)című témakörben talál útmutatást.

Ebben a szakaszban egy R Language IDE-t használ a Azure Databricks elérhetővé tételéhez. Mielőtt elkezdené, a számítógépen telepítve kell lennie a következőknek.

* IDE az R nyelvhez. Ez a cikk az asztali RStudio használatát ismerteti. Az [R Studio letöltésével](https://www.rstudio.com/products/rstudio/download/)telepítheti.
* Ha IDE-ként használja az RStudio for Desktopot, telepítse a Microsoft R- [https://aka.ms/rclient/](https://aka.ms/rclient/)ügyfelet is. 

Nyissa meg a RStudio, és hajtsa végre a következő lépéseket:

- Hivatkozzon a `RODBC` csomagra. Ez lehetővé teszi, hogy a korábban létrehozott DSN használatával kapcsolódjon Azure Databrickshoz.
- Hozzon létre egy kapcsolatot az DSN használatával.
- Futtasson egy SQL-lekérdezést a Azure Databricks található adatelemen. A következő kódrészletben *radio_sample_data* egy olyan tábla, amely már létezik a Azure Databricksban.
- Hajtson végre néhány műveletet a lekérdezésen a kimenet ellenőrzéséhez. 

A következő kódrészlet hajtja végre ezeket a feladatokat:

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

## <a name="connect-from-python"></a>Kapcsolat a Pythonból

Ebben a szakaszban egy Python IDE-t (például TÉTLEN) használ a Azure Databricks elérhetővé tételéhez. Mielőtt elkezdené, végezze el a következő előfeltételeket:

* Telepítse [a Pythont innen.](https://www.python.org/downloads/) Ha telepíti a Pythont ebből a hivatkozásból, az inaktív állapotba kerül.

* A számítógépen a parancssorból telepítse a `pyodbc` csomagot. Futtassa az alábbi parancsot:

      pip install pyodbc

Nyissa meg a TÉTLEN műveletet, és hajtsa végre a következő lépéseket:

- Importálja `pyodbc` a csomagot. Ez lehetővé teszi, hogy a korábban létrehozott DSN használatával kapcsolódjon Azure Databrickshoz.
- Hozzon létre egy kapcsolatot a korábban létrehozott DSN használatával.
-  Futtasson egy SQL-lekérdezést a létrehozott kapcsolatban. A következő kódrészletben *radio_sample_data* egy olyan tábla, amely már létezik a Azure Databricksban.
- Hajtson végre műveleteket a lekérdezésen a kimenet ellenőrzéséhez.

A következő kódrészlet hajtja végre ezeket a feladatokat:

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

* Az adatok Azure Databricksba való importálásának forrásairól a [Azure Databricks adatforrásaiban](/azure/databricks/data/data-sources/index) talál további információt.


