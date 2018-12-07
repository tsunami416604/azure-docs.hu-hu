---
title: 'Csatlakozás az Azure Databricks a Excel-, Python vagy R '
description: Ismerje meg, hogyan csatlakozhat a Simba illesztőprogram Azure Databricks Excel-, Python vagy R.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: mamccrea
ms.openlocfilehash: c57550a8b683ad8f184884374c4f09216417fc40
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52995622"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Csatlakozás az Azure Databricks a Excel-, Python vagy R

Ebből a cikkből elsajátíthatja a Databricks ODBC-illesztő használatával csatlakozzon az Azure Databricks a Microsoft Excel-, Python, vagy az R nyelvvel. A kapcsolat létrejötte után elérheti az adatokat az Azure Databricksben az Excel-, Python vagy R-ügyfelekről. Használhatja az ügyfelek adatainak további elemzéséhez. 

## <a name="prerequisites"></a>Előfeltételek

* Azure Databricks-munkaterületet, Spark-fürt és a fürthöz társított mintaadatokat kell rendelkeznie. Ha Ön még nem rendelkezik ezeket az előfeltételeket, hajtsa végre a rövid útmutató: [Spark-feladatok futtatása Azure databricksen az Azure portal használatával](quickstart-create-databricks-workspace-portal.md).

* Töltse le a Databricks ODBC-illesztőprogramot az [Databricks illesztőprogram letöltési oldal](https://databricks.com/spark/odbc-driver-download). Az alkalmazástól függően 32 bites vagy 64 bites verzióját telepítse, ahonnan csak szeretné az Azure Databricks csatlakozni. Csatlakozhat az Excelből, például az illesztőprogram 32 bites verzióját telepítse. Csatlakozás az R és Python, telepítse az illesztőprogram 64 bites verzióját.

* Állítsa be a személyes hozzáférési tokent a Databricksben. Útmutatásért lásd: [felügyeleti Token](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="set-up-a-dsn"></a>Állítsa be az Adatbázisnevet

Egy adatforrás neve (DSN) egy adott adatforrásra vonatkozó információkat tartalmazza. ODBC-illesztő a DSN adatforráshoz csatlakozni kell. Ebben a szakaszban állítsa be, hogy az ügyfelek számára, mint például a Microsoft Excelt, Python vagy R. csatlakozni az Azure Databricks a Databricks ODBC-illesztő használható Adatbázisnevet

1. Az Azure Databricks-munkaterület keresse meg a Databricks-fürtön.

    ![Nyissa meg Databricks-fürt](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "nyissa meg a Databricks-fürt")

2. Alatt a **konfigurációs** lapra, majd a **JDBC/ODBC** fület, és másolja a tartozó értékeket **kiszolgáló állomásnevét** és **HTTP elérési útja**. Ezek az értékek a jelen cikkben ismertetett lépések végrehajtásához szüksége.

    ![Databricks-konfiguráció beolvasása](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Databricks első konfiguráció")

3. A számítógépen indítsa el a **ODBC adatforrások** alkalmazás (32 bites vagy 64 bites) az alkalmazástól függően. Csatlakozás az Excelből, 32 bites verzióját használja. Csatlakozás az R és Python, 64 bites verzióját használja.

    ![Indítsa el a ODBC](./media/connect-databricks-excel-python-r/launch-odbc-app.png "ODBC-alkalmazás indítása")

4. Alatt a **felhasználói DSN** lapra, majd **Hozzáadás**. Az a **új adatforrás létrehozása** párbeszédpanelen válassza ki a **Simba Spark ODBC-illesztő**, és kattintson a **Befejezés**.

    ![Indítsa el a ODBC](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "ODBC-alkalmazás indítása")

5. Az a **Simba Spark ODBC-illesztő** párbeszédpanelen adja meg a következő értékeket:

    ![Konfigurálja a DSN](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "DSN konfigurálása")

    Az alábbi táblázat az értékeket a párbeszédpanelen adja meg az információkat.
    
    |Mező  | Érték  |
    |---------|---------|
    |**Adatforrás neve**     | Adja meg az adatforrás nevét.        |
    |**Host(s)**     | Adja meg a Databricks-munkaterület kimásolt érték *kiszolgáló állomásnevét*.        |
    |**Port**     | Adja meg *443-as*.        |
    |**Hitelesítési** > **mechanizmus**     | Válassza ki *felhasználónevet és jelszót*.        |
    |**Felhasználónév**     | Adja meg *token*.        |
    |**Jelszó**     | A Databricks-munkaterület másolt jogkivonat értéket írja. |
    
    Hajtsa végre az alábbi kiegészítő lépéseket a DSN-beállítások párbeszédpanel.
    
    * Kattintson a **HTTP-beállítások**. A megnyíló párbeszédpanelen illessze be az értéket, a *HTTP elérési útja* Databricks-munkaterület kimásolt. Kattintson az **OK** gombra.
    * Kattintson a **SSL-beállítások**. A megnyíló párbeszédpanelen válassza ki a **SSL engedélyezése** jelölőnégyzetet. Kattintson az **OK** gombra.
    * Kattintson a **tesztelése** az Azure databricks a kapcsolat teszteléséhez. Kattintson a **OK** a konfiguráció mentéséhez.
    * Az a **ODBC Data Source Administrator** párbeszédpanelen kattintson a **OK**.

Most már a DSN-beállítása. A következő szakaszokban használhatja a DSN csatlakozni az Azure databricks Excel-, Python vagy R.

## <a name="connect-from-microsoft-excel"></a>Csatlakozás a Microsoft Excel

Ebben a szakaszban, kér le adatot az Azure Databricks a Microsoft Excel formátumba a korábban létrehozott Adatforrásnevet használ. Mielőtt elkezdené, győződjön meg arról, hogy a számítógépen telepített Microsoft Excel. Használhatja az Excel próbaverzióját [próbaverzió a Microsoft Excel-hivatkozás](https://products.office.com/excel).

1. Nyissa meg egy üres munkafüzetet a Microsoft Excelben. Az a **adatok** menüszalagra, majd **adatok lekérése**. Kattintson a **egyéb forrásokból származó** majd **származó ODBC**.

    ![Indítsa el az Excelből ODBC](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "indítsa el az Excelből ODBC")

2. Az a **származó ODBC** párbeszédpanelen válassza ki a korábban létrehozott Adatforrásnevet, majd kattintson **OK**.

    ![Válassza ki a DSN](./media/connect-databricks-excel-python-r/excel-select-dsn.png "DSN kiválasztása")

3. Ha a rendszer kéri a hitelesítő adatokat, a felhasználó nevét adja meg **token**. A jelszó adja meg a token értékét, a Databricks-munkaterület lekért.

    ![Adja meg a hitelesítő adatokat a Databricks](./media/connect-databricks-excel-python-r/excel-databricks-token.png "DSN kiválasztása")

4. A navigációs ablakban válassza ki a táblát a Databricksben betöltése az Excel, és kattintson a kívánt **betöltése**. 

    ![A dta betöltése az Excel](./media/connect-databricks-excel-python-r/excel-load-data.png "terhelés dta Excelbe")

Miután az adatok az Excel-munkafüzetben, rajta elemzési műveleteket hajthat végre.

## <a name="connect-from-r"></a>Csatlakozás az R-hez

> [!NOTE]
> Ez a szakasz információt nyújt az R Studio ügyfél az Azure Databricks asztali számítógépeken futó integrálható az. Az R Studio használata az Azure Databricks-fürt magát az utasításokért lásd: [R Studiót az Azure databricks szolgáltatásban](https://docs.azuredatabricks.net/spark/latest/sparkr/rstudio.html).

Ebben a szakaszban egy R nyelvvel IDE referenciaadatok elérhető az Azure Databricks használatával. Mielőtt hozzákezd, rendelkeznie kell a következőknek telepítve a számítógépen.

* Egy ide-vel R nyelvvel. Ez a cikk az RStudio Desktop használja. Telepítheti a [R Studio letöltési](https://www.rstudio.com/products/rstudio/download/).
* Az RStudio Desktop-t használja az IDE, ha is telepítheti a Microsoft R Client [ https://aka.ms/rclient/ ](https://aka.ms/rclient/). 

Nyissa meg az RStudio, és kövesse az alábbi lépéseket:

- Referencia a `RODBC` csomagot. Ez lehetővé teszi, hogy kapcsolódjon az Azure Databricks használatával a korábban létrehozott Adatforrásnevet.
- A DSN kapcsolatot létesíteni.
- SQL-lekérdezést futtathat az adatok az Azure Databricksben. Az alábbi kódrészletben *radio_sample_data* egy táblázatot, amely már létezik az Azure Databricksben.
- Hajtsa végre a lekérdezést, hogy a kimenet ellenőrzése az egyes műveletek. 

A következő kódrészletet az alábbi feladatokat hajtja végre:

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

## <a name="connect-from-python"></a>Csatlakozás a Pythonnal

Ebben a szakaszban a referenciaadatok elérhető az Azure Databricksben (például a TÉTLEN) Python IDE használatával. Mielőtt elkezdené, hajtsa végre a következő előfeltételek vonatkoznak:

* Telepítse a Pythont a [Itt](https://www.python.org/downloads/). Is erre a hivatkozásra a Python telepítése telepíti a TÉTLEN.

* Egy parancssorból a számítógépen, telepítse a `pyodbc` csomagot. Futtassa az alábbi parancsot:

      pip install pyodbc

Nyissa meg a TÉTLEN, és kövesse az alábbi lépéseket:

- Importálás a `pyodbc` csomagot. Ez lehetővé teszi, hogy kapcsolódjon az Azure Databricks használatával a korábban létrehozott Adatforrásnevet.
- A korábban létrehozott DSN kapcsolatot létesíteni.
-  Egy SQL-lekérdezés futtatásához a kapcsolatot hozott létre. Az alábbi kódrészletben *radio_sample_data* egy táblázatot, amely már létezik az Azure Databricksben.
- Hajtsa végre a lekérdezést, hogy a kimenet ellenőrzése műveleteket.

A következő kódrészletet az alábbi feladatokat hajtja végre:

```python
# import the `pyodbc` package:
import pyodbc

# establish a connection using the DSN you created earlier
conn = pyodbc.connect("DSN=<ENTER DSN NAME HERE>", autocommit = True)

# run a SQL query using the connection you created
cursor = conn.cursor()
cursor.execute("SELECT * FROM radio_sample_data")

# print the rows retrieved by the query.
for row in cursor.fetchall():
    print(row)

```

## <a name="next-steps"></a>További lépések

* Importálhat adatokat az Azure Databricksbe forrásainak kapcsolatos további információkért lásd: [Azure Databricks-adatforrások](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#)


