---
title: Az Azure SQL Edge konfigurálása (előzetes verzió)
description: Tudnivalók az Azure SQL Edge konfigurálásáról (előzetes verzió)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: dc2b76a31982a3f72da02348c1a4796212887cb7
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84168250"
---
# <a name="configure-azure-sql-edge-preview"></a>Az Azure SQL Edge konfigurálása (előzetes verzió)

Az Azure SQL Edge a következő két lehetőség egyikének használatával támogatja a konfigurálást:

- Környezeti változók használata.
- Az MSSQL. conf fájl használata a/var/opt/MSSQL mappába helyezve.

> [!NOTE]
> A környezeti változók beállítása felülbírálja az MSSQL. conf fájlban megadott beállításokat.

## <a name="configure-using-environment-variables"></a>Konfigurálás környezeti változók használatával

Az Azure SQL Edge számos különböző környezeti változót tesz elérhetővé, amelyek segítségével konfigurálhatja az SQL Edge-tárolót. Ezek a környezeti változók a SQL Server on Linux számára elérhető környezeti változók részhalmazai. SQL Server on Linux környezeti változókról további információért lásd: [környezeti változók](/sql/linux/sql-server-linux-configure-environment-variables/).

Az Azure SQL Edge nem támogatja az alábbi SQL Server on Linux környezeti változókat. Ha meg van adva, a rendszer figyelmen kívül hagyja ezeket a környezeti változókat a tároló inicializálásakor.

| Környezeti változó | Description |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Rendelkezésre állási csoport engedélyezése. Az "1" érték például engedélyezve van, és a "0" le van tiltva. |

> [!IMPORTANT]
> Az SQL Edge *MSSQL_PID* környezeti változója csak a **prémium** és a **fejlesztői** értéket fogadja el érvényes értékként. Az Azure SQL Edge nem támogatja az inicializálást termékkulcs használatával.

> [!NOTE]
> Az Azure SQL Edge végfelhasználói licencszerződésének letöltéséhez tekintse át a [végfelhasználói](https://go.microsoft.com/fwlink/?linkid=2128283)licencszerződést.

### <a name="specifying-the-environment-variables"></a>A környezeti változók meghatározása

Az SQL Edge környezeti változói az Azure SQL Edge a [Azure Portal](deploy-portal.md)használatával történő telepítésekor adhatók meg. Ez a modul központi telepítésének "környezeti változók" szakaszában vagy a tároló létrehozása lehetőség részeként vehető fel az alább leírtak szerint.

*Beállítás környezeti változók használatával beállítások*

![beállítás környezeti változók listája alapján](media/configure/set-environment-variables.png)

*Beállítás a tároló létrehozási beállításaival*

![beállítás a tároló létrehozási beállításaival](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-using-mssqlconf-file"></a>Konfigurálás az MSSQL. conf fájllal

Az Azure SQL Edge nem tartalmazza az [MSSQL-conf konfigurációs segédprogramot](/sql/linux/sql-server-linux-configure-mssql-conf/) , például a SQL Server on Linuxt, mivel az MSSQL. conf fájlt manuálisan kell konfigurálni, és az SQL Edge-modul/var/opt/MSSQL/mappájához rendelt állandó tárolóeszközre kell helyezni. Ha az Azure Marketplace-en helyezi üzembe az SQL Edge-t, ez a leképezés a tároló létrehozása lehetőség * * csatlakoztatások lehetőségével van megadva.

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

|Beállítás|Description|
|:---|:---|
|**Felhasználói visszajelzés** | Adja meg, hogy SQL Server küld-e visszajelzést a Microsoftnak. |
|**Database Mail profil** | Állítsa be SQL Server on Linux alapértelmezett adatbázis-levelezési profilját. |
|**Magas rendelkezésre állás** | Rendelkezésre állási csoportok engedélyezése. |
|**Microsoft Elosztott tranzakciók koordinátora** | Az MSDTC konfigurálása és hibakeresése Linux rendszeren. Az SQL Edge esetében nem támogatottak az elosztott tranzakciókkal kapcsolatos további konfigurációs beállítások is. További információ ezekről a további konfigurációs lehetőségekről: az [MSDTC konfigurálása](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc) |
|**MLServices végfelhasználói licencszerződései** | Az R-és Python-LICENCSZERZŐDÉSek elfogadása Machine Learning Services csomagokhoz. Csak SQL Server 2019-es verzióra vonatkozik.|
|**outboundnetworkaccess** |Engedélyezze a kimenő hálózati hozzáférést [Machine learning Services](/sql/linux/sql-server-linux-setup-machine-learning/) R-, Python-és Java-bővítményekhez.|

Az alábbi táblázat az SQL Edge-hez használható MSSQL. conf fájlt mutatja be. Az MSSQL. conf fájl formátumával kapcsolatos további információkért lásd az [MSSQL. conf formátumot](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format).

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

## <a name="next-step"></a>Következő lépés

- [Kapcsolódás az Azure SQL Edge-hez](connect.md)
- [Teljes körű IoT-megoldás létrehozása az SQL Edge használatával](tutorial-deploy-azure-resources.md)
