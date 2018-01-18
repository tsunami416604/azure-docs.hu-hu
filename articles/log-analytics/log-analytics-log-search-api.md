---
title: "Naplóelemzési jelentkezzen search REST API |} Microsoft Docs"
description: "Ez az útmutató egy alapszintű oktatóanyag: hogyan használhatja a Naplóelemzési search REST API-t az Operations Management Suite (OMS) és példák a parancsok használata biztosít."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: b4e9ebe8-80f0-418e-a855-de7954668df7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: bwren
ms.openlocfilehash: 0ca80408f8e8b2dae7ff35d50b3d2c41ae54d3d3
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2018
---
# <a name="log-analytics-log-search-rest-api"></a>A Naplóelemzési jelentkezzen search REST API-n

> [!IMPORTANT]
> Ha a munkaterületet lett frissítve a [új Log Analytics lekérdezési nyelv](log-analytics-log-search-upgrade.md), majd tekintse át a [a naplófájl-keresési API új verziójának dokumentációjában](https://dev.loganalytics.io/).  Az örökölt API egy frissített munkaterület előfordulhat, hogy továbbra is használható, de hamarosan az lesz depracated.  Módosítsa a meglévő megoldásai az új API-val.

Ez az útmutató egy alapszintű oktatóanyag, valamint az, a napló Analytics Search REST API használatát. A Naplóelemzési része az Operations Management Suite (OMS).


## <a name="overview-of-the-log-search-rest-api"></a>A naplófájl-keresési REST API – áttekintés
A napló Analytics Search REST API RESTful, és az Azure Resource Manager API-n keresztül érhető el. Ez a cikk ismerteti az API használatával történő eléréséhez példák [ARMClient](https://github.com/projectkudu/ARMClient), egy nyílt forráskódú parancssori eszköz, amely leegyszerűsíti az Azure Resource Manager API meghívása. A ARMClient használata számos lehetőség a napló Analytics Search API eléréséhez. Lehetősége az Azure PowerShell modul használandó OperationalInsights, amely keresési eléréséhez parancsmagokat tartalmaz. Ezekkel az eszközökkel használhatja az Azure Resource Manager API-hívások indítása az OMS-munkaterület, és rajtuk keresési parancsok végrehajtása. Az API-t kiírja a keresési eredmények JSON formátumban, hogy lehetővé teszi a programozott módon használja a keresési eredmények között számos különböző módja.

Az Azure Resource Managerrel használható keresztül egy [.NET-keretrendszerhez készült](https://msdn.microsoft.com/library/azure/dn910477.aspx) és a [REST API](https://msdn.microsoft.com/library/azure/mt163658.aspx). További információkért tekintse át a csatolt weblapokat.

> [!NOTE]
> Ha egy összesítési paranccsal például `|measure count()` vagy `distinct`, minden hívás kereséséhez lépjen vissza legfeljebb 500 000 rekordot. Keresések, amelyek nem tartalmaznak egy összesítési parancs legfeljebb 5 000 rekordot ad vissza.
>
>

## <a name="basic-log-analytics-search-rest-api-tutorial"></a>Egyszerű napló Analytics Search REST API-oktatóanyag
### <a name="to-use-armclient"></a>ARMClient használata
1. Telepítés [Chocolatey](https://chocolatey.org/), amelyen egy megnyitott forrás Package Manager Windows. Nyisson meg egy parancssort rendszergazdaként, és futtassa a következő parancsot:

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```
2. ARMClient telepítse a következő parancs futtatásával:

    ```
    choco install armclient
    ```

### <a name="to-perform-a-search-using-armclient"></a>A keresések ARMClient végrehajtásához
1. Jelentkezzen be Microsoft-fiókjával vagy a munkahelyi vagy iskolai fiók használatával:

    ```
    armclient login
    ```

    Sikeres bejelentkezés kötve az adott fiókhoz az összes előfizetés listája:

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```
2. Az Operations Management Suite-munkaterület érhető el:

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2015-03-20
    ```

    Sikeres Get hívást volna kimeneti összes munkaterületek előfizetéshez társítva:

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. A keresési változó létrehozása:

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}";
    ```
4. A Keresés az új keresés változó használatával:

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2015-03-20 $mySearch
    ```

## <a name="log-analytics-search-rest-api-reference-examples"></a>Naplófájl Analytics Search REST API referencia példák
A következő példák azt szemléltetik, hogyan használhatja a keresési API-t.

### <a name="search---actionread"></a>Keresési - művelet olvasása
**A minta URL-címe:**

```
    /subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2015-03-20
```

**A kérelem:**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```
A következő táblázat ismerteti a rendelkezésre álló tulajdonságok.

| **Tulajdonság** | **Leírás** |
| --- | --- |
| Felső |A visszaadandó eredmény maximális száma. |
| kiemelés |Megfelelő mezőkben konzolban használt általában előtti és utáni paramétereket tartalmaz |
| előre |Előtagok az adott karakterlánc, a megfelelő mezőket. |
| Post |A megadott karakterlánc hozzáfűzi a megfelelő mezőket. |
| lekérdezés |A keresési lekérdezést, és adja vissza az eredményeket. |
| start |A kívánt eredmény található időszak kezdete. |
| vége |A kívánt eredmény található időszak végén. |

**Válasz:**

```
    {
      "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "__metadata" : {
        "resultType" : "raw",
        "total" : 1455,
        "top" : 150,
        "StartTime" : "2015-02-11T21:09:07.0345815Z",
        "Status" : "Successful",
        "LastUpdated" : "2015-02-11T21:09:07.331463Z",
        "CoreResponses" : [],
        "sort" : [{
          "name" : "TimeGenerated",
          "order" : "desc"
        }],
        "requestTime" : 450
      },
      "value": [{
        "SourceSystem" : "OpsManager",
        "TimeGenerated" : "2015-02-07T14:07:33Z",
        "Source" : "SideBySide",
        "EventLog" : "Application",
        "Computer" : "BAMBAM",
        "EventCategory" : 0,
        "EventLevel" : 1,
        "EventLevelName" : "Error",
        "UserName" : "N/A",
        "EventID" : 78,
        "MG" : "00000000-0000-0000-0000-000000000001",
        "TimeCollected" : "2015-02-07T14:10:04.69Z",
        "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
        "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "Type" : "Event",
        "__metadata" : {
          "Type" : "Event",
          "TimeGenerated" : "2015-02-07T14:07:33Z",
          "highlighting" : {
          "EventLevelName" : ["{[hl]}Error{[/hl]}"]
        }
      }]
    ],
            "start" : "2015-02-04T21:03:29.231Z",
            "end" : "2015-02-11T21:03:29.231Z"
          }
        }
      }]
    }
```

### <a name="searchid---actionread"></a>Keresés / {azonosító} - művelet olvasása
**A kérelem egy mentett keresés tartalma:**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2015-03-20
```

> [!NOTE]
> A search függvény "Folyamatban" állapotú, majd a frissített eredményeit lekérdezési végezhető el ez az API. 6 perc után a a Keresés eredménye így a rendszer eldobja a gyorsítótárból, és HTTP állapotba adja vissza. A kezdeti keresési kérelem azonnal visszaadja a "Sikeres" állapotba, ha az eredmények nem kerülnek a gyorsítótárba, amely az API vissza HTTP állapotba, ha a lekérdezés. Egy 200-as HTTP-eredmény tartalma ugyanabban a formában, a kezdeti keresési kérelem csak a frissített értékekkel.
>
>

### <a name="saved-searches"></a>Mentett keresések
**A kérelem mentett keresések listája:**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2015-03-20
```

Támogatott módszerek: GET PUT törlése

Gyűjtemény módszereket támogatja: beolvasása

A következő táblázat ismerteti a rendelkezésre álló tulajdonságok.

| Tulajdonság | Leírás |
| --- | --- |
| Azonosító |Az egyedi azonosítója. |
| ETag |**Szükséges javítás**. Minden egyes kiszolgáló frissítve. Értéket meg kell egyeznie az aktuális tárolt érték vagy "*" frissítéséhez. 409 régi vagy érvénytelen értéket adott vissza. |
| properties.query |**Szükséges**. A keresési lekérdezés. |
| properties.displayName |**Szükséges**. A lekérdezés felhasználói megjelenítendő nevét. |
| properties.category |**Szükséges**. A lekérdezés felhasználói kategóriát. |

> [!NOTE]
> A Naplóelemzési keresési API jelenleg adja vissza a felhasználó által létrehozott mentett keresések során kérdezi le azt a mentett kereséseket a munkaterületen. Az API nem ad vissza a mentett keresések megoldások által biztosított.
>
>

### <a name="create-saved-searches"></a>Mentett keresések létrehozása
**A kérelem:**

```
    $savedSearchParametersJson = "{'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisismyid?api-version=2015-03-20 $savedSearchParametersJson
```

> [!NOTE]
> A nevet minden mentett keresések, ütemezéseihez és napló Analytics API-val létrehozott kisbetűs kell lennie.

### <a name="delete-saved-searches"></a>Törölje a mentett kereséseket
**A kérelem:**

```
    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20
```

### <a name="update-saved-searches"></a>Mentett keresések frissítése
 **A kérelem:**

```
    $savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="metadata---json-only"></a>Metaadat - JSON csak
Itt módja a mezőket az adatok gyűjtése a munkaterületen az összes napló megjelenítéséhez. Például ha szeretné tudni, hogy ha az esemény típusa van-e a számítógép neve mező, majd a lekérdezés módja egy ellenőrzése.

**Kérelem mezők:**

```
    armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2015-03-20
```

**Válasz:**

```
    {
      "__metadata" : {
        "schema" : {
          "name" : "Example Name",
          "version" : 2
        },
        "resultType" : "schema",
        "requestTime" : 35
      },
      "value" : [{
          "name" : "MG",
          "displayName" : "MG",
          "type" : "Guid",
          "facetable" : true,
          "display" : false,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
        }, {
          "name" : "ManagementGroupName",
          "displayName" : "ManagementGroupName",
          "type" : "String",
          "facetable" : true,
          "display" : true,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
        }
      ]
    }
```

A következő táblázat ismerteti a rendelkezésre álló tulajdonságok.

| **Tulajdonság** | **Leírás** |
| --- | --- |
| név |Mező neve. |
| displayName |A mezőben megjelenítendő nevét. |
| type |A mező értékének típusa. |
| kategorizálható |"Indexed", aktuális kombinációja "tárolt" és "dimenzió" tulajdonságait. |
| Megjelenítése |Aktuális "üzenet megjelenítése" tulajdonság. Értéke TRUE, ha mező a keresési látható. |
| ownerType |Csak a előkészítve IP-címekhez tartozó típusok csökken. |

## <a name="optional-parameters"></a>Választható paraméterek
A következő rendelkezésre álló választható paramétereket ismerteti.

### <a name="highlighting"></a>Kiemelve
A "Kiemelt" paraméter egy nem kötelező paraméter, használhatja a keresés alrendszer kéréséhez tartalmaznak jelölők válaszában.

Ezek a jelölők jelzik kezdetét, és a záró kijelölt szöveg, amely megfelel a keresési lekérdezés megadott feltételek.
Előfordulhat, hogy adja meg a kezdő és záró jelölők kiemelt kifejezés csomagolásához keresés által használt.

**Példa keresési lekérdezés**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```

**A minta eredmény:**

```
    {
        "TimeGenerated":
        "2015-05-18T23:55:59Z", "Source":
        "EventLog": "Application",
        "Computer": "smokedturkey.net",
        "EventCategory": 0,
        "EventLevel":1,
        "EventLevelName":
        "Error"
        "Manager ", "__metadata":
        {
            "Type": "Event",
            "TimeGenerated": "2015-05-18T23:55:59Z",
            "highlighting": {
                "EventLevelName":
                ["{[hl]}Error{[/hl]}"]
            }
        }
    }
```

Figyelje meg, hogy az előző eredmény tartalmazza-e olyan hibaüzenetet, amely a következő előtaggal, és hozzáfűzi.

## <a name="computer-groups"></a>Számítógépcsoportok
Számítógépcsoportok különleges mentett keresések, amely a számítógépek készlettel tért vissza.  A számítógép (csoport) további lekérdezések segítségével az eredményeket a csoport számítógépeinek korlátozza.  A számítógép (csoport), a mentett kereséseket értékkel rendelkező számítógép csoport címkével ellátott lett megvalósítva.

Az alábbiakban látható egy számítógépcsoport mintát választ.

```
    "etag": "W/\"datetime'2016-04-01T13%3A38%3A04.7763203Z'\"",
    "properties": {
        "Category": "My Computer Groups",
        "DisplayName": "My Computer Group",
        "Query": "srv* | Distinct Computer",
        "Tags": [{
            "Name": "Group",
            "Value": "Computer"
          }],
    "Version": 1
    }
```

### <a name="retrieving-computer-groups"></a>Számítógépcsoportok beolvasása
Egy számítógépcsoport lekéréséhez használja a Get metódust csoport azonosítóval.

```
armclient get /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Group ID}`?api-version=2015-03-20
```

### <a name="creating-or-updating-a-computer-group"></a>Létrehozásakor vagy frissítésekor. a számítógép (csoport)
Hozzon létre egy számítógépcsoportot, használja a Put metódust a mentett keresés egyedi azonosítót. Használjon egy meglévő számítógép-csoport azonosítója, ha egy módosul. Ha egy számítógép (csoport) hoz létre a Log Analytics-portálról, majd az azonosító a csoportból, a név jön létre.

A lekérdezés a meghatározása használt számítógépek csoportjára működnek majd megfelelően kell visszaadnia.  Javasoljuk, hogy a lekérdezés leállítása `| Distinct Computer` annak érdekében, hogy a megfelelő adat.

A mentett keresés definition tartalmaznia kell egy címke nevű csoport a számítógép a következő keresésre számítógépcsoportként besorolását értékű.

```
    $etag=Get-Date -Format yyyy-MM-ddThh:mm:ss.msZ
    $groupName="My Computer Group"
    $groupQuery = "Computer=srv* | Distinct Computer"
    $groupCategory="My Computer Groups"
    $groupID = "My Computer Groups | My Computer Group"

    $groupJson = "{'etag': 'W/`"datetime\'" + $etag + "\'`"', 'properties': { 'Category': '" + $groupCategory + "', 'DisplayName':'"  + $groupName + "', 'Query':'" + $groupQuery + "', 'Tags': [{'Name': 'Group', 'Value': 'Computer'}], 'Version':'1'  }"

    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20 $groupJson
```

### <a name="deleting-computer-groups"></a>Számítógép-csoportok törlése
Olyan számítógép-csoport törléséhez használja a Delete metódus csoport azonosítóval.

```
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20
```


## <a name="next-steps"></a>További lépések
* További tudnivalók [keresések jelentkezzen](log-analytics-log-searches.md) egyéni mezőkkel feltétel lekérdezések összeállításához.
