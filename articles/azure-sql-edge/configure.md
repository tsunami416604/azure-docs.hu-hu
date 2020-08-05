---
title: Az Azure SQL Edge konfigurálása (előzetes verzió)
description: Az Azure SQL Edge (előzetes verzió) konfigurálásának ismertetése.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/28/2020
ms.openlocfilehash: 0cb2eed0895c10f649facaa184a5f9f9ea158aa5
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/04/2020
ms.locfileid: "87551982"
---
# <a name="configure-azure-sql-edge-preview"></a>Az Azure SQL Edge konfigurálása (előzetes verzió)

Az Azure SQL Edge a következő két lehetőség egyikének használatával támogatja a konfigurálást:

- Környezeti változók
- A/var/opt/MSSQL mappába helyezett MSSQL. conf fájl

> [!NOTE]
> A környezeti változók beállítása felülbírálja az MSSQL. conf fájlban megadott beállításokat.

## <a name="configure-by-using-environment-variables"></a>Konfigurálás környezeti változók használatával

Az Azure SQL Edge számos különböző környezeti változót tesz elérhetővé, amelyek segítségével konfigurálhatja az SQL Edge-tárolót. Ezek a környezeti változók a SQL Server on Linux számára rendelkezésre álló részhalmazok. SQL Server on Linux környezeti változókról további információért lásd: [környezeti változók](/sql/linux/sql-server-linux-configure-environment-variables/).

Az Azure SQL Edge nem támogatja az alábbi SQL Server on Linux környezeti változót. Ha meg van adva, a rendszer a tároló inicializálása során figyelmen kívül hagyja ezt a környezeti változót.

| Környezeti változó | Leírás |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Rendelkezésre állási csoport engedélyezése. Az **1** érték például engedélyezve van, és a **0** le van tiltva. |

> [!IMPORTANT]
> Az SQL Edge **MSSQL_PID** környezeti változója csak a **prémium** és a **fejlesztői** értéket fogadja el érvényes értékként. Az Azure SQL Edge nem támogatja az inicializálást termékkulcs használatával.

> [!NOTE]
> Töltse le az Azure SQL Edge-hez készült [Microsoft szoftverlicenc-feltételeit](https://go.microsoft.com/fwlink/?linkid=2128283) .

### <a name="specify-the-environment-variables"></a>Környezeti változók meghatározása

Az SQL Edge környezeti változóinak megadása, amikor a [Azure Portalon](deploy-portal.md)keresztül telepíti a szolgáltatást. A modul központi telepítésének **környezeti változók** szakaszában vagy a **tároló létrehozása lehetőség**részeként is hozzáadhatja őket.

Értékek hozzáadása **környezeti változókban**.

![Beállítás környezeti változók listája alapján](media/configure/set-environment-variables.png)

Értékek hozzáadása a **tároló létrehozási beállításaiban**.

![Beállítás a tároló létrehozása lehetőség használatával](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-by-using-an-mssqlconf-file"></a>Konfigurálás az MSSQL. conf fájl használatával

Az Azure SQL Edge nem tartalmazza az [MSSQL-conf konfigurációs segédprogramot](/sql/linux/sql-server-linux-configure-mssql-conf/) , mint például a SQL Server on Linux. Manuálisan kell konfigurálnia az MSSQL. conf fájlt, és az SQL Edge-modul/var/opt/MSSQL/mappájára leképezett állandó tárolóban kell elhelyeznie. Ha az SQL Edge-t az Azure Marketplace-ről telepíti, ez a leképezés a **tároló létrehozási beállításai**között a **csatlakoztatások** lehetőséggel van megadva.

```json
    {
        "Mounts": [
          {
            "Type": "volume",
            "Source": "sqlvolume",
            "Target": "/var/opt/mssql"
          }
        ]
      }
    }
```

A következő MSSQL. conf beállítások nem alkalmazhatók az SQL Edge-re:

|Beállítás|Leírás|
|:---|:---|
|**Felhasználói visszajelzés** | Válassza ki, hogy SQL Server küldjön-e visszajelzést a Microsoftnak. |
|**Adatbázisbeli levelezési profil** | Állítsa be SQL Server on Linux alapértelmezett adatbázis-levelezési profilját. |
|**Magas rendelkezésre állás** | Rendelkezésre állási csoportok engedélyezése. |
|**Microsoft Elosztott tranzakciók koordinátora** | Az MSDTC konfigurálása és hibakeresése Linux rendszeren. További elosztott tranzakciókkal kapcsolatos konfigurációs beállítások nem támogatottak az SQL Edge esetében. További információ ezekről a további konfigurációs lehetőségekről: az [MSDTC konfigurálása](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc). |
|**A ML-szolgáltatások végfelhasználói licencszerződései** | Az R-és Python-LICENCSZERZŐDÉSek elfogadása Azure Machine Learning csomagokhoz. Csak SQL Server 2019-es verzióra vonatkozik.|
|**outboundnetworkaccess** |Engedélyezze a kimenő hálózati hozzáférést [Machine learning Services](/sql/linux/sql-server-linux-setup-machine-learning/) R-, Python-és Java-bővítményekhez.|

Az alábbi minta MSSQL. conf fájl az SQL Edge esetében működik. Az MSSQL. conf fájl formátumával kapcsolatos további információkért lásd az [MSSQL. conf formátumot](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format).

```ini
[EULA]
accepteula = Y

[coredump]
captureminiandfull = true
coredumptype = full

[filelocation]
defaultbackupdir = /var/opt/mssql/backup/
defaultdatadir = /var/opt/mssql/data/
defaultdumpdir = /var/opt/mssql/data/
defaultlogdir = /var/opt/mssql/log/

[language]
lcid = 1033

[memory]
memorylimitmb = 6144

[sqlagent]
errorlogfile = /var/opt/mssql/log/sqlagentlog.log
errorlogginglevel = 7

[traceflag]
traceflag0 = 3604
traceflag1 = 3605
traceflag2 = 1204
```

## <a name="run-azure-sql-edge-as-non-root-user"></a>Az Azure SQL Edge futtatása nem gyökérszintű felhasználóként

Az Azure SQL Edge CTP 2.2-től kezdve az SQL Edge-tárolók nem gyökérszintű felhasználóval vagy csoporttal futhatnak. Ha az Azure piactéren helyezi üzembe a programot, kivéve, ha egy másik felhasználó/csoport van megadva, az SQL Edge-tárolók az MSSQL (nem gyökérszintű) felhasználóként kezdődnek. Ha másik nem root felhasználót szeretne megadni az üzembe helyezés során, adja hozzá a `*"User": "<name|uid>[:<group|gid>]"*` kulcs-érték párokat a tároló létrehozási beállításai között. Az alábbi példában az SQL Edge úgy van konfigurálva, hogy felhasználóként induljon el `*IoTAdmin*` .

```json
{
    ..
    ..
    ..
    "User": "IoTAdmin",
    "Env": [
        "MSSQL_AGENT_ENABLED=TRUE",
        "ClientTransportType=AMQP_TCP_Only",
        "MSSQL_PID=Premium"
    ]
}
```

Annak engedélyezéséhez, hogy a nem legfelső szintű felhasználó hozzáférjen a csatlakoztatott köteteken található adatbázis-fájlokhoz, győződjön meg arról, hogy a tárolóban futtatott felhasználó/csoport olvasási & írási engedéllyel rendelkezik az állandó fájl tárolásához. Az alábbi példában a nem root felhasználót a fájlok tulajdonosaként user_id 10001-es értékre állítjuk. 

```bash
chown -R 10001:0 <database file dir>
```

### <a name="upgrading-from-earlier-ctp-releases"></a>Frissítés korábbi CTP-kiadásokból

Az Azure SQL Edge korábbi CTP-je úgy lett konfigurálva, hogy legfelső szintű felhasználóként fusson. A korábbi CTP-ről való frissítéskor a következő lehetőségek érhetők el:

- Továbbra is használhatja a root felhasználót – a root felhasználó használatának folytatásához adja hozzá a `*"User": "0:0"*` kulcs-érték párokat a tároló létrehozása lehetőség alatt.
- Az alapértelmezett MSSQL-felhasználó használata – az alapértelmezett MSSQL-felhasználó használata, kövesse az alábbi lépéseket
  - Adjon hozzá egy MSSQL nevű felhasználót a Docker-gazdagépen. Az alábbi példában egy 10001-es AZONOSÍTÓJÚ felhasználói MSSQL-t adunk hozzá. Ezt a felhasználót a rendszer a legfelső szintű csoportba is felveszi.
    ```bash
    sudo useradd -M -s /bin/bash -u 10001 -g 0 mssql
    ```
  - Módosítsa az engedélyt arra a könyvtárra/csatlakoztatási kötetre, ahol az adatbázisfájl található 
    ```bash
    sudo chgrp -R 0 /var/lib/docker/volumes/kafka_sqldata/
    sudo chmod -R g=u /var/lib/docker/volumes/kafka_sqldata/
    ```
- Másik nem gyökérszintű felhasználói fiók használata – másik nem gyökérszintű felhasználói fiók használata
  - A tároló-létrehozási beállítások módosításával adja meg a `*"User": "user_name | user_id*` kulcs-érték párokat a tároló létrehozása lehetőség alatt. Cserélje le user_name vagy user_id a Docker-gazdagépről származó tényleges user_name vagy user_idra. 
  - Módosítsa a könyvtár/csatlakoztatási kötet engedélyeit.



## <a name="next-steps"></a>Következő lépések

- [Kapcsolódás az Azure SQL Edge-hez](connect.md)
- [Teljes körű IoT-megoldás kiépítése az SQL Edge használatával](tutorial-deploy-azure-resources.md)
