---
title: "Az Azure Functions (kísérleti) külső tábla kötése"
description: "A külső tábla kötések az Azure Functions használatával"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
ms.openlocfilehash: 8a4358fa67e45d0b7a2df1519d649099b5ef5850
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
---
# <a name="external-table-binding-for-azure-functions-experimental"></a>Az Azure Functions (kísérleti) külső tábla kötése

Ez a cikk azt ismerteti, hogyan táblázatos adatok a Szolgáltatottszoftver-szolgáltatók, köztük a Sharepoint és Dynamics, az Azure Functions használatát. Azure Functions támogatja bemeneti és kimeneti kötések a külső táblákhoz.

> [!IMPORTANT]
> A külső tábla kötés kísérleti, és előfordulhat, hogy soha nem érik el az általánosan rendelkezésre álló (GA) állapotát. Csak az Azure-ban tartalmazza 1.x működik, és nincs tervbe veheti fel az Azure Functions 2.x. A Szolgáltatottszoftver-szolgáltatók adatok elérését igénylő forgatókönyvek esetén érdemes [a logic apps függvényekké hívó](functions-twitter-email.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>API-kapcsolatok

Tábla kötések külső API-kapcsolatok hitelesítéséhez külső Szolgáltatottszoftver-szolgáltatókkal használja ki. 

A kötés hozzárendelésekor hozzon létre egy új API-kapcsolat, vagy belül ugyanabban az erőforráscsoportban meglévő API-kapcsolat használata.

### <a name="available-api-connections-tables"></a>Lehetséges API-kapcsolatok (táblák)

|Összekötő|Eseményindító|Input (Bemenet)|Kimenet|
|:-----|:---:|:---:|:---:|
|[DB2](https://docs.microsoft.com/azure/connectors/connectors-create-api-db2)||x|x
|[Dynamics 365 műveleteihez](https://ax.help.dynamics.com/wiki/install-and-configure-dynamics-365-for-operations-warehousing/)||x|x
|[Dynamics 365](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[Dynamics NAV](https://msdn.microsoft.com/library/gg481835.aspx)||x|x
|[Google Táblázatok](https://docs.microsoft.com/azure/connectors/connectors-create-api-googledrive)||x|x
|[Informix](https://docs.microsoft.com/azure/connectors/connectors-create-api-informix)||x|x
|[Dynamics 365 a Pénzügy](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[MySQL](https://docs.microsoft.com/azure/store-php-create-mysql-database)||x|x
|[Oracle Database](https://docs.microsoft.com/azure/connectors/connectors-create-api-oracledatabase)||x|x
|[Közös adatszolgáltatás](https://docs.microsoft.com/common-data-service/entity-reference/introduction)||x|x
|[Salesforce](https://docs.microsoft.com/azure/connectors/connectors-create-api-salesforce)||x|x
|[SharePoint](https://docs.microsoft.com/azure/connectors/connectors-create-api-sharepointonline)||x|x
|[SQL Server](https://docs.microsoft.com/azure/connectors/connectors-create-api-sqlazure)||x|x
|[Teradata](http://www.teradata.com/products-and-services/azure/products/)||x|x
|UserVoice||x|x
|Zendesk||x|x

> [!NOTE]
> Külső tábla kapcsolatok is használható a [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="creating-an-api-connection-step-by-step"></a>Az API-kapcsolat létrehozása: lépésről lépésre

1. A függvény alkalmazás az Azure portál lapját, válassza ki a plusz jelre (**+**) létrehozza a függvényt.

1. Az a **forgatókönyv** mezőben válassza **kísérleti**.

1. Válassza ki **külső tábla**.

1. Válasszon egy nyelvet.

2. A **külső tábla kapcsolat**, jelöljön ki egy létező kapcsolatot vagy **új**.

1. Új kapcsolat, adja meg a beállításokat, és válassza ki **engedélyezés**.

1. Válassza ki **létrehozása** a függvény létrehozásához.

1. Válassza ki **integrálni > külső tábla**.

1. Állítsa be a kapcsolatot a céloldali tábla használja. Ezeket a beállításokat a Szolgáltatottszoftver-szolgáltatók között változhat. Példák a következő szakaszban szerepelnek.

## <a name="example"></a>Példa

Ebben a példában az azonosító, a Vezetéknév és a Vezetéknév oszlopokat "Ügyfél" nevű tábla kapcsolódik. A kód a Contact entitásokat a táblázat sorolja fel, és a vezeték- és keresztneveket naplózza.

Itt a *function.json* fájlt:

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

A C# parancsfájl kód itt látható:

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

### <a name="sql-server-data-source"></a>SQL Server-adatforrás

Létrehoz egy táblát az SQL Server használata ebben a példában, hogy ez a parancsfájl. `dataSetName`az "alapértelmezett".

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

### <a name="google-sheets-data-source"></a>Google-lapok adatforrás

Ebben a példában a Google Docs használandó tábla létrehozásához hozzon létre egy táblázatot nevű `Contact`. Az összekötő számolótábla megjelenítési név nem használható. A belső nevét (félkövér) kell használható dataSetName, például: `docs.google.com/spreadsheets/d/`  **`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`**  adja hozzá az oszlopnevek `Id`, `LastName`, `FirstName` első sorának, majd töltse fel a következő sorokban adatokat.

### <a name="salesforce"></a>Salesforce

Ez a példa használata Salesforce, `dataSetName` "alapértelmezett".

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájlt.

|Function.JSON tulajdonság | Leírás|
|---------|----------------------|
|**típusa** | meg kell `apiHubTable`. Ez a tulajdonság rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon.|
|**iránya** | meg kell `in`. Ez a tulajdonság rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon. |
|**név** | Esemény-elem funkciókódot jelölő neve. | 
|**kapcsolat**| Az Alkalmazásbeállítás, amely tárolja az API-kapcsolati karakterlánc azonosítja. Az Alkalmazásbeállítás automatikusan létrejön, amikor hozzáadja az API-kapcsolat az integráció felhasználói felületén.|
|**dataSetName**|A tábla olvasni tartalmazó adatkészlet nevét.|
|**Táblanév**|A tábla neve|
|**entityid beállítást**|A tábla kötések üresnek kell lennie.

A táblázatos összekötő adatkészletek biztosít, és minden egyes táblát tartalmaz. Az alapértelmezett adatkészlet neve nem "alapértelmezett". A DataSet adatkészlet és a táblázat a különböző Szolgáltatottszoftver-szolgáltatók címeit az alábbiak:

|Összekötő|Adathalmaz|Tábla|
|:-----|:---|:---| 
|**SharePoint**|Hely|SharePoint-lista
|**SQL**|Adatbázis|Tábla 
|**Google lap**|Számolótábla|Munkalap 
|**Excel**|Excel-fájl|Táblázat 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
