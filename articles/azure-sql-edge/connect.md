---
title: Az Azure SQL Edge (előzetes verzió) összekötése és lekérdezése
description: Útmutató az Azure SQL Edge csatlakoztatásához és lekérdezéséhez (előzetes verzió)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e7034c3f664eeba802341510b109ba9cc57845a8
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235131"
---
# <a name="connect-and-query-azure-sql-edge-preview"></a>Az Azure SQL Edge (előzetes verzió) összekötése és lekérdezése

Az Azure SQL Edge-tároló üzembe helyezése után az alábbi helyekről csatlakozhat az SQL Database-motorhoz.

- A tárolón belül
- Egy másik, ugyanazon a gazdagépen futó Docker-tárolóból.
- A gazdagépről
- A hálózat bármely más ügyfélszámítógépéről.

## <a name="tools-to-connect-to-azure-sql-edge"></a>Eszközök az Azure SQL Edge-hez való kapcsolódáshoz

Az Azure SQL Edge-példánnyal létesített kapcsolatok az alább említett általános eszközök bármelyikéről elhelyezhetők.

* a [Sqlcmd](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) -Sqlcmd ügyféleszközök már szerepelnek az Azure SQL Edge-tároló rendszerképében. Ha egy futó tárolóhoz egy interaktív bash-rendszerhéj csatlakozik, az eszközöket helyileg is futtathatja.
* [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode)

Ha egy hálózati gépről szeretne csatlakozni egy Azure SQL Edge-adatbázis-motorhoz, a következőkre lesz szüksége:

- *A gazdagép IP-címe vagy hálózati neve* – ez az a gazdagép, ahol az Azure SQL Edge-tároló fut.
- *Azure SQL Edge Container Host port-hozzárendelés* – a Docker-tároló portjához tartozó port hozzárendelése a gazdagép egy portjához. A tárolóban az SQL Edge mindig a 1433-es portra van leképezve. Ez az Azure SQL Edge-telepítés részeként módosítható. A portszám módosításához frissítse az SQL Edge-modulhoz tartozó "tároló-létrehozási beállítások" kifejezést Azure IoT Edge. Az alább megadott példában a tárolón a 1433-es port a gazdagép 1600-es portjára van leképezve.

    ```JSON
    {
        "PortBindings": {
          "1433/tcp": [
            {
              "HostPort": "1600"
            }
          ]
        }
    }
    ```

- *Rendszergazdai jelszó az SQL Edge-példányhoz* – ez a **SA_PASSWORD** környezeti változóhoz megadott érték az SQL Edge-telepítés során.

## <a name="connecting-to-the-database-engine-from-within-the-container"></a>Csatlakozás az adatbázis-motorhoz a tárolón belülről

A [SQL Server parancssori eszközöket](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) az Azure SQL Edge-tároló rendszerképe tartalmazza. Ha a tárolóhoz egy interaktív parancssort csatol, az eszközöket helyileg is futtathatja.

1. A `docker exec -it` paranccsal interaktív bash-rendszerhéj indítható el a futó tárolóban. A következő példában `e69e056c702d` a tároló azonosítója szerepel.

    ```bash
    docker exec -it <Azure SQL Edge container id or name> /bin/bash
    ```

    > [!TIP]
    > Nem mindig kell megadnia a teljes tároló azonosítóját. Csak annyi karaktert kell megadnia, hogy egyedileg azonosítsa azt. Így ebben a példában elég lehet `e6` `e69` a teljes azonosító helyett használni, vagy nem.

2. A tárolón belül a helyi kapcsolat Sqlcmd. A Sqlcmd alapértelmezés szerint nem található az elérési úton, ezért meg kell adnia a teljes elérési utat.

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. Ha a Sqlcmd befejeződött, írja be a következőt: `exit` .

4. Ha elkészült az interaktív parancssorral, írja be a következőt: `exit` . Az interaktív bash-rendszerhéjból való kilépés után a tároló továbbra is fut.

## <a name="connect-to-sql-edge-from-another-container-on-the-same-host"></a>Kapcsolódás az SQL Edge-hez egy másik tárolóból ugyanazon a gazdagépen

Mivel az ugyanazon a gazdagépen futó két tároló ugyanazon a Docker-hálózaton található, könnyen elérhetők a tároló neve és a szolgáltatás portjának címe alapján. Ha például egy másik Python-modulból (tárolóból) csatlakozik az SQL Edge-példányhoz ugyanazon a gazdagépen, az alábbihoz hasonló kapcsolati karakterláncot használhat. Az alábbi példa feltételezi, hogy az SQL Edge az alapértelmezett port figyelésére van konfigurálva.

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-sql-edge-from-another-network-machine"></a>Kapcsolódás az SQL Edge-hez egy másik hálózati gépről

Ha a hálózat egy másik számítógépéről szeretne csatlakozni az SQL Edge-példányhoz, akkor a Docker-gazdagép IP-címét és azt a gazda portot kell használnia, amelyhez az SQL Edge-tároló hozzá van rendelve. Ha például a Docker-gazdagép IP-címe * xxx.xxx.xxx.xxx, és az SQL Edge tároló a *1600*-es gazdagép-portra van leképezve, akkor az SQL Edge-példány kiszolgálójának címe **xxx. xxx. xxx. xxx, 1600**. A frissített Python-szkript a következő lenne

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

Ha a Windows rendszerű gépen futó SQL Server Management Studio használatával szeretne csatlakozni az SQL Edge-példányhoz, tekintse meg a [SQL Server Management Studio](https://docs.microsoft.com/sql/linux/sql-server-linux-manage-ssms).

Ha egy SQL Edge-példányhoz szeretne csatlakozni a Visual Studio Code használatával Windows, Mac vagy Linux rendszerű gépen, tekintse át a [Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode)-ot.

Ha Windows, Mac vagy Linux rendszerű gépen lévő Azure Data Studio használatával szeretne csatlakozni az SQL Edge-példányhoz, tekintse meg a [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-server).

## <a name="see-also"></a>Lásd még

[Kapcsolat és lekérdezés](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[SQL Server-eszközök telepítése Linux rendszeren](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools)
