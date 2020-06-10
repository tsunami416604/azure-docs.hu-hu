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
ms.date: 05/19/2020
ms.openlocfilehash: c38bb6100665cc9456b66608660bdca520b934c6
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636240"
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
|**MLServices végfelhasználói licencszerződései** | Az R-és Python-LICENCSZERZŐDÉSek elfogadása Azure Machine Learning csomagokhoz. Csak SQL Server 2019-es verzióra vonatkozik.|
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

## <a name="next-steps"></a>Következő lépések

- [Kapcsolódás az Azure SQL Edge-hez](connect.md)
- [Teljes körű IoT-megoldás kiépítése az SQL Edge használatával](tutorial-deploy-azure-resources.md)
