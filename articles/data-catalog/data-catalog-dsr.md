---
title: "Azure Data Catalog támogatott adatforrások |} Microsoft Docs"
description: "Ez a cikk felsorolja a jelenleg támogatott adatforrások előírásainak."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: jstevens
editor: 
tags: 
ms.assetid: fd4345ca-2ed8-4c5e-9c4b-f954be2fc9f9
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: d6867c73bc6ea3c238cceef8a68466d451f3365c
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="supported-data-sources-in-azure-data-catalog"></a>Az Azure Data Catalog támogatott adatforrások

Egy nyilvános API-t vagy a kattintson a metaadatok közzététele-egyszer regisztrációs eszköz, vagy közvetlenül az Azure Data Catalog az információk manuális megadásával webes portál. Az alábbi táblázat foglalja össze az egyes ma a katalógusban, és a közzétételi lehetőségeket által támogatott összes adatforrás. A külső adatok eszközök, amelyek az egyes adatforrások is elindíthatja a "Megnyitás a következőben" portal tapasztalatunkat is listában megtalálhatók. A második tábla minden egyes adatforrás kapcsolat tulajdonság olyan további technikai meghatározást tartalmaz.


## <a name="list-of-supported-data-sources"></a>A támogatott adatforrások listája

<table>
    <tr>
       <td><b>Adatforrás-objektum</b></td>
       <td><b>API</b></td>
       <td><b>Kézi bevitele</b></td>
       <td><b>Regisztrációs eszköz</b></td>
       <td><b>Nyissa meg az eszközök</b></td>
       <td><b>Megjegyzések</b></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store-könyvtár</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store-fájl</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure Blob Storage</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure Storage-címtár</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure Storage-tábla</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>
    <tr>
      <td>HDFS könyvtár</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>HDFS-fájl</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Hive tábla</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Hive nézete</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>MySQL-tábla</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Az Excel, a Power bi-ban</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>MySQL-nézet</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Az Excel, a Power bi-ban</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Oracle Database tábla</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Az Excel, a Power bi-ban</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Oracle Database megtekintése</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Az Excel, a Power bi-ban</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Más (általános eszköz)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure SQL Data Warehouse-tábla</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Az Excel, a Power BI-ban az SQL Server data Tools összetevővel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Az SQL Data Warehouse megtekintése</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Az Excel, a Power BI-ban az SQL Server data Tools összetevővel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services dimenzió</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Az Excel, a Power bi-ban</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Az SQL Server Analysis Services KPI</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Az Excel, a Power bi-ban</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services mérték</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Az Excel, a Power bi-ban</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services tábla</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Az Excel, a Power bi-ban</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services jelentés</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Böngésző</font></td>
      <td><font size=2>Csak natív üzemmódú kiszolgálók esetén. SharePoint-módban nem támogatott.</font></td>
    </tr>
    <tr>
      <td>SQL Server tábla</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Az Excel, a Power BI-ban az SQL Server data Tools összetevővel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server megtekintése</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Az Excel, a Power BI-ban az SQL Server data Tools összetevővel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Teradata tábla</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Teradata megtekintése</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SAP HANA-nézet</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>DB2 tábla</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>DB2 megtekintése</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Rendszer-fájl</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>FTP-könyvtár</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>FTP-fájl</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>HTTP-jelentés</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>HTTP-végpont</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>HTTP-fájl</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>OData entitáskészlet</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Az OData-funkció</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>PostgreSQL tábla</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>PostgreSQL megtekintése</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SAP HANA-nézet</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td> Salesforce-objektum</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SharePoint-lista </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Az Azure Cosmos DB gyűjtemény</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Általános ODBC tábla</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Általános ODBC megtekintése</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Cassandra tábla</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>Általános eszközként ODBC közzététele</font></td>
    </tr>
    <tr>
      <td>Cassandra megtekintése</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>Általános eszközként ODBC közzététele</font></td>
    </tr>
    <tr>
      <td>Sybase tábla</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Sybase megtekintése</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>MongoDB tábla</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>Általános eszközként ODBC közzététele</font></td>
    </tr>
    <tr>
      <td>MongoDB megtekintése</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>Általános eszközként ODBC közzététele</font></td>
    </tr>
</table>

Ha további segítségre, küldje el a szolgáltatás kérelmet a [Azure Data Catalog fórum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).


## <a name="data-source-reference-specification"></a>Adatforrás hivatkozás megadása
> [!NOTE]
> A **DSL struktúra** oszlop a következő táblázat felsorolja a csak a csatlakozási tulajdonságokat az "address" tulajdonságcsomag Azure Data Catalog által használt. Ez azt jelenti, hogy a "address" tulajdonságcsomag tartalmazhat az adatforrás, amely az Azure Data Catalog továbbra is fennáll, de nem használ más kapcsolat tulajdonságai.

<table>
    <tr>
       <td><b>Adatforrás típusa</b></td>
       <td><b>Eszköz típusa</b></td>
       <td><b>Objektumtípus</b></td>
       <td><b>DSL struktúra<b></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>Tároló</td>
      <td>Data Lake</td>
      <td>
        <font size=2>Protokoll: webhdfs <br>Hitelesítési: {alapvető, oauth} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-címe</font>
      </td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>Tábla</td>
      <td>Könyvtár, fájl</td>
      <td>
        <font size=2>Protokoll: webhdfs <br>Hitelesítési: {alapvető, oauth} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-címe</font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Tároló</td>
      <td>Tároló</td>
      <td>
        <font size=2>Protokoll: azure-blobot <br>Hitelesítési: {azure-access-kulcs} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;tartomány <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fiók <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;tároló</font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Tábla</td>
      <td>A BLOB-, könyvtár</td>
      <td>
        <font size=2>Protokoll: azure-blobot <br>Hitelesítési: {azure-access-kulcs} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;tartomány <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fiók <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;tároló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;név</font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Tároló</td>
      <td>Tároló</td>
      <td>
        <font size=2>Protokoll: azure-táblák <br>Hitelesítési: {azure-access-kulcs} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;tartomány <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fiók</font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Tábla</td>
      <td>Tábla</td>
      <td>
        <font size=2>Protokoll: azure-táblák <br>Hitelesítési: {azure-access-kulcs} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;tartomány <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fiók <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;név</font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Tároló</td>
      <td>Virtuális fürt</td>
      <td>
        <font size=2>Protokoll: cosmos <br>Hitelesítési: {alapvető, windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-címe</font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Tábla</td>
      <td>Az adatfolyam, az adatfolyam be van állítva, nézet</td>
      <td>
        <font size=2>Protokoll: cosmos <br>Hitelesítési: {alapvető, windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-címe</font>
      </td>
    </tr>
    <tr>
      <td>Datazen</td>
      <td>Tároló</td>
      <td>Helykiszolgáló</td>
      <td>
        <font size=2>Protokoll: http <br>Hitelesítési: {none, basic, a windows, a oauth} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-címe</font>
      </td>
    </tr>
    <tr>
      <td>Datazen</td>
      <td>Jelentés</td>
      <td>A jelentés, az irányítópult</td>
      <td>
        <font size=2>Protokoll: http <br>Hitelesítési: {none, basic, a windows, a oauth} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-címe</font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>Tároló</td>
      <td>Adatbázis</td>
      <td>
        <font size=2>Protokoll: db2 <br>Hitelesítési: {alapvető, windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis</font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>Tábla</td>
      <td>A tábla, nézet</td>
      <td>
        <font size=2>Protokoll: db2 <br>Hitelesítési: {alapvető, windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektum <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;séma</font>
      </td>
    </tr>
    <tr>
      <td>Fájlrendszer</td>
      <td>Tábla</td>
      <td>Fájl</td>
      <td>
        <font size=2>Protokoll: fájl <br>Hitelesítési: {none, basic, a windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;elérési út</font>
      </td>
    </tr>
    <tr>
      <td>FTP</td>
      <td>Tábla</td>
      <td>Könyvtár, fájl</td>
      <td>
        <font size=2>Protokoll: ftp <br>Hitelesítési: {none, basic, a windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-címe</font>
      </td>
    </tr>
    <tr>
      <td>Hadoop elosztott fájlrendszerhez</td>
      <td>Tároló</td>
      <td>Fürt</td>
      <td>
        <font size=2>Protokoll: webhdfs <br>Hitelesítési: {alapvető, oauth} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-címe</font>
      </td>
    </tr>
    <tr>
      <td>Hadoop elosztott fájlrendszerhez</td>
      <td>Tábla</td>
      <td>Könyvtár, fájl</td>
      <td>
        <font size=2>Protokoll: webhdfs <br>Hitelesítési: {alapvető, oauth} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-címe</font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Tároló</td>
      <td>Adatbázis</td>
      <td>
        <font size=2>Protokoll: struktúra <br>Hitelesítési: {HDInsight, basic, a felhasználónevet, nincs} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serverProtocol: {hive2}</font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Tábla</td>
      <td>A tábla, nézet</td>
      <td>
        <font size=2>Protokoll: struktúra <br>Hitelesítési: {HDInsight, basic, a felhasználónevet, nincs} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektum <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serverProtocol: {hive2}</font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Tároló</td>
      <td>Helykiszolgáló</td>
      <td>
        <font size=2>Protokoll: http <br>Hitelesítési: {none, basic, a windows, a oauth} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-címe</font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Jelentés</td>
      <td>A jelentés, az irányítópult</td>
      <td>
        <font size=2>Protokoll: http <br>Hitelesítési: {none, basic, a windows, a oauth} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-címe</font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Tábla</td>
      <td>Végpont, fájl</td>
      <td>
        <font size=2>Protokoll: http <br>Hitelesítési: {none, basic, a windows, a oauth} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-címe</font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Tároló</td>
      <td>Adatbázis</td>
      <td>
        <font size=2>Protokoll: mysql <br>Hitelesítési: {protokollt, a windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis</font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Tábla</td>
      <td>A tábla, nézet</td>
      <td>
        <font size=2>Protokoll: mysql <br>Hitelesítési: {protokollt, a windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektum</font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Tároló</td>
      <td>Entitás tároló</td>
      <td>
        <font size=2>Protokoll: odata <br>Hitelesítési: {none, basic, a windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-címe</font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Tábla</td>
      <td>Entitáskészlet, a függvény</td>
      <td>
        <font size=2>Protokoll: odata <br>Hitelesítési: {none, basic, a windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-címe <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;erőforrás</font>
      </td>
    </tr>
    <tr>
      <td>Oracle Database</td>
      <td>Tároló</td>
      <td>Adatbázis</td>
      <td>
        <font size=2>Protokoll: oracle <br>Hitelesítési: {protokollt, a windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis</font>
      </td>
    </tr>
    <tr>
      <td>Oracle Database</td>
      <td>Tábla</td>
      <td>A tábla, nézet</td>
      <td>
        <font size=2>Protokoll: oracle <br>Hitelesítési: {protokollt, a windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;séma <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektum</font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Tároló</td>
      <td>Adatbázis</td>
      <td>
        <font size=2>Protokoll: postgresql <br>Hitelesítési: {alapvető, windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis</font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Tábla</td>
      <td>A tábla, nézet</td>
      <td>
        <font size=2>Protokoll: postgresql <br>Hitelesítési: {alapvető, windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;séma <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektum</font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Tároló</td>
      <td>Helykiszolgáló</td>
      <td>
        <font size=2>Protokoll: http <br>Hitelesítési: {none, basic, a windows, a oauth} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-címe</font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Jelentés</td>
      <td>A jelentés, az irányítópult</td>
      <td>
        <font size=2>Protokoll: http <br>Hitelesítési: {none, basic, a windows, a oauth} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-címe</font>
      </td>
    </tr>
    <tr>
      <td>A Power Query</td>
      <td>Tábla</td>
      <td>Adatok adategyesítési</td>
      <td>
        <font size=2>Protokoll: power-lekérdezés <br>Hitelesítési: {oauth} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-címe</font>
      </td>
    </tr>
    <tr>
      <td>Salesforce</td>
      <td>Tábla</td>
      <td>Objektum</td>
      <td>
        <font size=2>Protokoll: salesforce-com <br>Hitelesítési: {alapvető, windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;loginServer <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;osztály <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;itemName</font>
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>Tároló</td>
      <td>Kiszolgáló</td>
      <td>
        <font size=2>Protokoll: sap hana-sql <br>Hitelesítési: {protokollt, a windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló</font>
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>Tábla</td>
      <td>Nézet</td>
      <td>
        <font size=2>Protokoll: sap hana-sql <br>Hitelesítési: {protokollt, a windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;séma <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektum</font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>Tábla</td>
      <td>Lista</td>
      <td>
        <font size=2>Protokoll: sharepoint-lista <br>Hitelesítési: {alapvető, windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-címe</font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Parancs</td>
      <td>Tárolt eljárás</td>
      <td>
        <font size=2>Protokoll: TDS-adatfolyam <br>Hitelesítési: {protokollt, a windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;séma <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektum</font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>TableValuedFunction</td>
      <td>Táblázat értékű függvényben</td>
      <td>
        <font size=2>Protokoll: TDS-adatfolyam <br>Hitelesítési: {protokollt, a windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;séma <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektum</font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Tároló</td>
      <td>Adatbázis</td>
      <td>
        <font size=2>Protokoll: TDS-adatfolyam <br>Hitelesítési: {protokollt, a windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis</font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Tábla</td>
      <td>A tábla, nézet</td>
      <td>
        <font size=2>Protokoll: TDS-adatfolyam <br>Hitelesítési: {protokollt, a windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;séma <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektum</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Parancs</td>
      <td>Tárolt eljárás</td>
      <td>
        <font size=2>Protokoll: TDS-adatfolyam <br>Hitelesítési: {protokollt, a windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;séma <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektum</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>Táblázat értékű függvényben</td>
      <td>
        <font size=2>Protokoll: TDS-adatfolyam <br>Hitelesítési: {protokollt, a windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;séma <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektum</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Tároló</td>
      <td>Adatbázis</td>
      <td>
        <font size=2>Protokoll: TDS-adatfolyam <br>Hitelesítési: {protokollt, a windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Tábla</td>
      <td>A tábla, nézet</td>
      <td>
        <font size=2>Protokoll: TDS-adatfolyam <br>Hitelesítési: {protokollt, a windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;séma <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektum</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services többdimenziós</td>
      <td>Tároló</td>
      <td>Modell</td>
      <td>
        <font size=2>Protokoll: analysis-szolgáltatások <br>Hitelesítési: {windows, a basic, a névtelen, nincs} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modell</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services többdimenziós</td>
      <td>KPI-T</td>
      <td>KPI-T</td>
      <td>
        <font size=2>Protokoll: analysis-szolgáltatások <br>Hitelesítési: {windows, a basic, a névtelen, nincs} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modell <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektum <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Objektumtípus: {KPI}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services többdimenziós</td>
      <td>mértékcsoport</td>
      <td>mértékcsoport</td>
      <td>
        <font size=2>Protokoll: analysis-szolgáltatások <br>Hitelesítési: {windows, a basic, a névtelen, nincs} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modell <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektum <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Objektumtípus: {Measure}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services többdimenziós</td>
      <td>Tábla</td>
      <td>Dimenzió</td>
      <td>
        <font size=2>Protokoll: analysis-szolgáltatások <br>Hitelesítési: {windows, a basic, a névtelen, nincs} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modell <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektum <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Objektumtípus: {dimenzió}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services táblázatos</td>
      <td>Tároló</td>
      <td>Modell</td>
      <td>
        <font size=2>Protokoll: analysis-szolgáltatások <br>Hitelesítési: {windows, a basic, a névtelen, nincs} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modell</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services táblázatos</td>
      <td>KPI-T</td>
      <td>KPI-T</td>
      <td>
        <font size=2>Protokoll: analysis-szolgáltatások <br>Hitelesítési: {windows, a basic, a névtelen, nincs} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modell <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektum <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Objektumtípus: {KPI}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services táblázatos</td>
      <td>mértékcsoport</td>
      <td>mértékcsoport</td>
      <td>
        <font size=2>Protokoll: analysis-szolgáltatások <br>Hitelesítési: {windows, a basic, a névtelen, nincs} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modell <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektum <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Objektumtípus: {Measure}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services táblázatos</td>
      <td>Tábla</td>
      <td>Tábla</td>
      <td>
        <font size=2>Protokoll: analysis-szolgáltatások <br>Hitelesítési: {windows, a basic, a névtelen, nincs} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modell <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektum <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Objektumtípus: {TABLE táblázat}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services szoftverben</td>
      <td>Tároló</td>
      <td>Kiszolgáló</td>
      <td>
        <font size=2>Protokoll: jelentési szolgáltatások <br>Hitelesítési: {windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;verzió: {ReportingService2010}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services szoftverben</td>
      <td>Jelentés</td>
      <td>Jelentés</td>
      <td>
        <font size=2>Protokoll: jelentési szolgáltatások <br>Hitelesítési: {windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;elérési út <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;verzió: {ReportingService2010}</font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Tároló</td>
      <td>Adatbázis</td>
      <td>
        <font size=2>Protokoll: teradata rendszerhez <br>Hitelesítési: {protokollt, a windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis</font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Tábla</td>
      <td>A tábla, nézet</td>
      <td>
        <font size=2>Protokoll: teradata rendszerhez <br>Hitelesítési: {protokollt, a windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektum</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Tároló</td>
      <td>Modell</td>
      <td>
        <font size="2">Protokoll: mssql-mds <br>Hitelesítési: {windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-címe <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modell <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;verzió</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Tábla</td>
      <td>Entitás</td>
      <td>
        <font size="2">Protokoll: mssql-mds <br>Hitelesítési: {windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-címe <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modell <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;verzió <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;entitás</font>
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>Tároló</td>
      <td>Adatbázis</td>
      <td>
        <font size=2>Protokoll: dokumentum-adatbázis <br>Hitelesítési: {azure-access-kulcs} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-címe <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis</font>
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>Gyűjtemény</td>
      <td>Gyűjtemény</td>
      <td>
        <font size=2>Protokoll: dokumentum-adatbázis <br>Hitelesítési: {azure-access-kulcs} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL-címe <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;gyűjtemény</font>
      </td>
    </tr>
    <tr>
      <td>Általános ODBC</td>
      <td>Tároló</td>
      <td>Adatbázis</td>
      <td>
        <font size=2>Protokoll: odbc <br>Hitelesítési: {alapvető, windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;beállítások <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis</font>
      </td>
    </tr>
    <tr>
      <td>Általános ODBC</td>
      <td>Tábla</td>
      <td>A tábla, nézet</td>
      <td>
        <font size=2>Protokoll: odbc <br>Hitelesítési: {alapvető, windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;beállítások <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektum <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;séma</font>
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>Tároló</td>
      <td>Adatbázis</td>
      <td>
        <font size=2>protokoll: sybase <br>Hitelesítési: {alapvető, windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis</font>
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>Tábla</td>
      <td>A tábla, nézet</td>
      <td>
        <font size=2>protokoll: sybase <br>Hitelesítési: {alapvető, windows} <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;kiszolgáló <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;adatbázis <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;séma <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektum</font>
      </td>
    </tr>
    <tr>
      <td>Más (egyik sem)</td>
      <td>\*</td>
      <td>\*</td>
      <td>
        <font size=2>Protokoll: általános-eszköz <br>Cím: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;assetId</font>
      </td>
    </tr>
</table>
