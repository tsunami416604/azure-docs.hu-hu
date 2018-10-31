---
title: Külső tábla kötésének az Azure Functions szolgáltatáshoz (kísérleti funkció)
description: Külső tábla kötések használata az Azure Functions szolgáltatásban
services: functions
author: craigshoemaker
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: cshoe
ms.openlocfilehash: 5566ea8b21682c5023afc4af357e1a80e1d37384
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50248471"
---
# <a name="external-table-binding-for-azure-functions-experimental"></a>Külső tábla kötésének az Azure Functions szolgáltatáshoz (kísérleti funkció)

Ez a cikk ismerteti, hogyan dolgozhat tabulált SaaS-szolgáltatók, például a Sharepoint és a Dynamics, az Azure Functions szolgáltatásban. Az Azure Functions támogatja a bemeneti és kimeneti kötések külső táblák esetén.

> [!IMPORTANT]
> A külső tábla kötésének kísérleti, és előfordulhat, hogy soha nem érik el az általánosan elérhető (GA) állapotát. Csak az Azure-ban benne lesz 1.x, és nem is tervezzük az Azure Functions hozzáadandó 2.x. Az SaaS-szolgáltatók adatokhoz való hozzáférést igénylő forgatókönyvek esetén fontolja meg [logikai alkalmazásokat, amelyek hívásokat indítani olyan funkciók](functions-twitter-email.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>API-kapcsolatok

Táblakötések használhatja a külső API-kapcsolatok hitelesítéséhez a külső SaaS-szolgáltatók. 

Kötés hozzárendelésekor hozzon létre egy új API-kapcsolat, vagy egy meglévő API-kapcsolat belül ugyanazt az erőforráscsoportot használja.

### <a name="available-api-connections-tables"></a>Elérhető API-kapcsolatok (táblák)

|Összekötő|Eseményindító|Input (Bemenet)|Kimenet|
|:-----|:---:|:---:|:---:|
|[DB2](https://docs.microsoft.com/azure/connectors/connectors-create-api-db2)||x|x
|[Dynamics 365 for Operations](https://ax.help.dynamics.com/wiki/install-and-configure-dynamics-365-for-operations-warehousing/)||x|x
|[Dynamics 365](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[Dynamics NAV-hoz](https://msdn.microsoft.com/library/gg481835.aspx)||x|x
|[Google Táblázatok](https://docs.microsoft.com/azure/connectors/connectors-create-api-googledrive)||x|x
|[Informix](https://docs.microsoft.com/azure/connectors/connectors-create-api-informix)||x|x
|[Dynamics 365 for Financials](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[MySQL](https://docs.microsoft.com/azure/store-php-create-mysql-database)||x|x
|[Oracle Database](https://docs.microsoft.com/azure/connectors/connectors-create-api-oracledatabase)||x|x
|[Common Data Service](https://docs.microsoft.com/common-data-service/entity-reference/introduction)||x|x
|[Salesforce](https://docs.microsoft.com/azure/connectors/connectors-create-api-salesforce)||x|x
|[SharePoint](https://docs.microsoft.com/azure/connectors/connectors-create-api-sharepointonline)||x|x
|[SQL Server](https://docs.microsoft.com/azure/connectors/connectors-create-api-sqlazure)||x|x
|[Teradata](http://www.teradata.com/products-and-services/azure/products/)||x|x
|UserVoice||x|x
|Zendesk||x|x

> [!NOTE]
> Külső tábla kapcsolatok is használható a [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="creating-an-api-connection-step-by-step"></a>Egy API-kapcsolat létrehozása: lépésről lépésre

1. Az Azure portal oldalán a függvényalkalmazás számára, válassza a plusz jelre (**+**) függvény létrehozásához.

1. Az a **forgatókönyv** jelölje ki **kísérleti**.

1. Válassza ki **külső tábla**.

1. Válasszon egy nyelvet.

2. A **külső táblával létesített kapcsolat**, válasszon ki egy meglévő kapcsolatot, vagy válasszon **új**.

1. Egy új kapcsolatot, adja meg a beállításokat, és válassza ki **engedélyezés**.

1. Válassza ki **létrehozás** a függvény létrehozásához.

1. Válassza ki **integrálása > külső tábla**.

1. Konfigurálja a kapcsolatot a céloldali tábla használatához. Ezek a beállítások között SaaS-szolgáltatók függ. Példák a következő szakaszban szerepelnek.

## <a name="example"></a>Példa

Ebben a példában az "Ügyfél" nevű azonosítója, Vezetéknév és utónév oszlopokkal rendelkező tábla csatlakozik. A kód a tábla Contact entitásokat sorolja fel, és naplózza az első és utolsó neve.

Íme a *function.json* fájlt:

```json
{
  "bindings": [
    {
      "type": "manualTrigger",
      "direction": "in",
      "name": "input"
    },
    {
      "type": "apiHubTable",
      "direction": "in",
      "name": "table",
      "connection": "ConnectionAppSettingsKey",
      "dataSetName": "default",
      "tableName": "Contact",
      "entityId": "",
    }
  ],
  "disabled": false
}
```

Íme a C#-szkriptkódot:

```cs
#r "Microsoft.Azure.ApiHub.Sdk"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.ApiHub;

//Variable name must match column type
//Variable type is dynamically bound to the incoming data
public class Contact
{
    public string Id { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
}

public static async Task Run(string input, ITable<Contact> table, TraceWriter log)
{
    //Iterate over every value in the source table
    ContinuationToken continuationToken = null;
    do
    {   
        //retrieve table values
        var contactsSegment = await table.ListEntitiesAsync(
            continuationToken: continuationToken);

        foreach (var contact in contactsSegment.Items)
        {   
            log.Info(string.Format("{0} {1}", contact.FirstName, contact.LastName));
        }

        continuationToken = contactsSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

### <a name="sql-server-data-source"></a>Az SQL Server-adatforrás

Hozzon létre egy táblát az SQL Server használata ebben a példában, Íme egy parancsfájlt. `dataSetName` az "alapértelmezett".

```sql
CREATE TABLE Contact
(
    Id int NOT NULL,
    LastName varchar(20) NOT NULL,
    FirstName varchar(20) NOT NULL,
    CONSTRAINT PK_Contact_Id PRIMARY KEY (Id)
)
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (1, 'Bitt', 'Prad') 
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (2, 'Glooney', 'Ceorge') 
GO
```

### <a name="google-sheets-data-source"></a>Google táblázatok-adatforrás

Az ebben a példában a Google Docs használandó tábla létrehozásához hozzon létre egy táblázatot a nevű `Contact`. Az összekötő a számolótábla megjelenített név nem használható. A belső neve (félkövér) kell használható dataSetName, például: `docs.google.com/spreadsheets/d/` **`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`** adja hozzá az oszlopok neveit `Id`, `LastName`, `FirstName` az első sorba, majd adatok feltöltése a további sorokat.

### <a name="salesforce"></a>Salesforce

A Salesforce-szal, az ebben a példában használandó `dataSetName` "alapértelmezett".

## <a name="configuration"></a>Konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt.

|Function.JSON tulajdonság | Leírás|
|---------|----------------------|
|**type** | Meg kell `apiHubTable`. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon.|
|**direction** | Meg kell `in`. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon. |
|**name** | A függvénykód esemény elemet képviselő változó neve. | 
|**kapcsolat**| Azonosítja a Alkalmazásbeállítás, amely tárolja az API kapcsolati karakterláncát. Az integráció felhasználói felület az API-kapcsolat hozzáadásakor a rendszer automatikusan létrehozza az alkalmazásbeállítást.|
|**dataSetName**|Az adatkészlet, amely tartalmazza az beolvasni a tábla neve.|
|**Táblanév**|A tábla neve|
|**entityId**|Táblakötések üresnek kell lennie.

Egy táblázatos összekötő adatkészletek biztosít, és minden egyes adatkészlethez külön táblát tartalmaz. Az alapértelmezett adatkészlet neve nem "alapértelmezett". Egy adatkészlet és a egy táblázat a különböző SaaS-szolgáltatók címeit az alábbiak:

|Összekötő|Adathalmaz|Tábla|
|:-----|:---|:---| 
|**SharePoint**|Hely|SharePoint-lista
|**SQL**|Adatbázis|Tábla 
|**Google-táblázatban**|Számolótábla|Munkalap 
|**Excel**|Excel-fájl|Táblázat 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
