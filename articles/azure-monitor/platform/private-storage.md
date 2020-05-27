---
title: Felhasználói tulajdonban lévő Storage-fiókok a naplók betöltéséhez
description: A saját Storage-fiók használatával beolvashatja a naplózási adatait a Azure Monitor Log Analytics munkaterületére.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/20/2020
ms.openlocfilehash: 7213cb10936fc1c2117b2c5c3fc32a6bfea02d30
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/24/2020
ms.locfileid: "83816779"
---
# <a name="customer-owned-storage-accounts-for-log-ingestion-in-azure-monitor"></a>Felhasználói tulajdonban lévő Storage-fiókok a naplózás betöltéséhez Azure Monitor

A Azure Monitor egyes adattípusok, például az [egyéni naplók](data-sources-custom-logs.md) és néhány [Azure-napló](azure-storage-iis-table.md)betöltési folyamatában a Storage-fiókokat használja. A betöltési folyamat során a rendszer először egy Storage-fiókba küldik a naplókat, és később bekerül Log Analyticsba vagy Application Insightsba. Ha a betöltés során szeretné szabályozni az adatait, a szolgáltatás által felügyelt tároló helyett saját Storage-fiókokat is használhat. A saját Storage-fiók használata lehetővé teszi a naplók hozzáférésének, tartalmának, titkosításának és megőrzésének szabályozását a betöltés során. Erre a saját tárhely vagy BYOS használata esetén van szükség. 

A BYOS igénylő forgatókönyvek esetében a hálózati elkülönítés privát hivatkozásokon keresztül történik. VNet használatakor a hálózat elkülönítése gyakran követelmény, és a nyilvános internethez való hozzáférés korlátozott. Ilyen esetekben a Azure Monitor a szolgáltatás tárhelyének betöltése teljesen le van tiltva, vagy helytelen gyakorlatnak minősül. Ehelyett a naplókat az ügyfél által birtokolt Storage-fiókon belül kell beírni a VNet, vagy egyszerűen elérhetővé kell tenni őket.

Egy másik forgatókönyv a naplók titkosítása az ügyfél által felügyelt kulcsokkal (CMK). Az ügyfelek a naplókat tároló fürtökön található CMK használatával titkosítják a naplózott adataikat. Ugyanezt a kulcsot is használhatja a naplók titkosítására a betöltési folyamat során.

## <a name="data-types-supported"></a>Támogatott adattípusok

A Storage-fiókból betöltött adattípusok közé tartoznak a következők. Az ilyen típusú adatok betöltésével kapcsolatos további információkért lásd: [adatgyűjtés az Azure Diagnostics bővítményből Azure monitor naplókba](azure-storage-iis-table.md) .

| Típus | Tábla adatai |
|:-----|:------------------|
| IIS-naplók | BLOB: wad-IIS-LogFiles|
|Windows-eseménynaplók | Tábla: WADWindowsEventLogsTable |
| Rendszernapló | Tábla: LinuxsyslogVer2v0 |
| Windows ETW-naplók | Tábla: WADETWEventTable|
| Service Fabric | Tábla: WADServiceFabricSystemEventTable <br/> WADServiceFabricReliableActorEventTable<br/> WADServiceFabricReliableServicEventTable |
| Egyéni naplók | n.a. |
| Azure Security Center Watson-memóriakép fájljai | n.a.|  

## <a name="storage-account-requirements"></a>Storage-fiókra vonatkozó követelmények 
A Storage-fióknak a következő követelményeknek kell megfelelnie:

- Elérhető a VNet található erőforrások számára, amelyek naplókat írnak a tárolóba.
- Ugyanazon a régión kell lennie, mint a munkaterületnek, amelyhez kapcsolódik.
- Explicit módon engedélyezhető Log Analytics a naplófájlok olvasásához a Storage-fiókból, ha a *megbízható MS-szolgáltatások engedélyezése lehetőségre kattint a Storage-fiók eléréséhez*.

## <a name="process-to-configure-customer-owned-storage"></a>Az ügyfél által birtokolt tároló konfigurálásának folyamata
A saját Storage-fiók használatának alapszintű folyamata a következő:

1. Hozzon létre egy Storage-fiókot, vagy válasszon ki egy meglévő fiókot.
2. A Storage-fiók összekapcsolása egy Log Analytics munkaterülettel.
3. Kezelje a tárterületet a terhelésének és megtartásának áttekintésével, hogy biztosan a várt módon működjön.

A hivatkozások létrehozásához és eltávolításához csak a REST API használható. Az egyes folyamatokhoz szükséges adott API-kérés részleteit az alábbi részben találja.

## <a name="api-request-values"></a>API-kérelmek értékei

#### <a name="datasourcetype"></a>dataSourceType 

- AzureWatson – ezt az értéket használja Azure Security Center Azure Watson-memóriakép fájljaihoz.
- CustomLogs – használja ezt az értéket a következő adattípusokhoz:
  - Egyéni naplók
  - IIS-naplók
  - Események (Windows)
  - Syslog (Linux)
  - ETW-naplók
  - Események Service Fabric
  - Értékelési adatértékek  

#### <a name="storage_account_resource_id"></a>storage_account_resource_id
Ez az érték a következő struktúrát használja:

```
subscriptions/{subscriptionId}/resourcesGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName1}
```



## <a name="get-current-links"></a>Aktuális hivatkozások beolvasása

### <a name="get-linked-storage-accounts-for-a-specific-data-source-type"></a>Társított Storage-fiókok beolvasása adott adatforrás-típushoz

#### <a name="api-request"></a>API-kérelem

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

#### <a name="response"></a>Válasz 

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs",
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
    "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
    "name": "CustomLogs",
    "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```

### <a name="get-all-linked-storage-accounts"></a>Az összes társított Storage-fiók beolvasása

#### <a name="api-request"></a>API-kérelem

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts?api-version=2019-08-01-preview  
```

#### <a name="response"></a>Válasz

```json
{
    [
        {
            "properties":
            {
                "dataSourceType": "CustomLogs",
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_1>",
                    "<storage_account_resource_id_2>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
            "name": "CustomLogs",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        },
        {
            "properties":
            {
                "dataSourceType": " AzureWatson "
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_3>",
                    "<storage_account_resource_id_4>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/AzureWatson",
            "name": "AzureWatson",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        }
    ]
}
```

## <a name="create-or-modify-a-link"></a>Hivatkozás létrehozása vagy módosítása

Miután összekapcsolta a Storage-fiókot egy munkaterülettel, Log Analytics a szolgáltatás tulajdonában lévő Storage-fiók helyett elkezdi használni. Egyszerre regisztrálhatja a Storage-fiókok listáját, és ugyanazt a Storage-fiókot használhatja több munkaterülethez is.

Ha a munkaterület a VNet kívül is kezeli a VNet-erőforrásokat és-erőforrásokat, győződjön meg arról, hogy nem utasítja el az internetről érkező forgalmat. A tárterületnek ugyanazokkal a beállításokkal kell rendelkeznie, mint a munkaterülete, és elérhetővé kell tenni a VNet kívüli erőforrások számára. 

### <a name="api-request"></a>API-kérelem

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

### <a name="payload"></a>Adattartalom

```json
{
    "properties":
    {
        "storageAccountIds  " : 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    }
}
```

### <a name="response"></a>Válasz

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs"
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
"id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
"name": "CustomLogs",
"type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```


## <a name="unlink-a-storage-account"></a>Storage-fiók csatolásának megszüntetése
Ha úgy dönt, hogy leállítja egy Storage-fiók használatát a betöltéshez, vagy lecseréli a használni kívánt munkaterületet, akkor a tárterületet a munkaterületről kell leválasztani.

Az összes Storage-fiók egy munkaterületről való leválasztása azt jelenti, hogy a betöltés megkísérli a szolgáltatás által felügyelt Storage-fiókokra támaszkodni. Ha az ügynökök korlátozott hozzáférésű VNet futnak, a betöltés várhatóan sikertelen lesz. A munkaterületnek rendelkeznie kell egy társított Storage-fiókkal, amely elérhető a figyelt erőforrásokból.

A Storage-fiók törlése előtt győződjön meg arról, hogy a benne található összes adatot betöltötte a munkaterületre. Elővigyázatosságból gondoskodjon arról, hogy a Storage-fiók elérhető legyen egy másik tárterület összekapcsolása után. Csak akkor törölje, ha az összes tartalmat betöltötte, és megtekintheti az új adatokat az újonnan csatlakoztatott Storage-fiókba.


### <a name="api-request"></a>API-kérelem
```
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

## <a name="replace-a-storage-account"></a>Storage-fiók cseréje

A betöltéshez használt Storage-fiók cseréjéhez először hozzon létre egy hivatkozást egy új Storage-fiókhoz. A naplózási ügynökök megkapják a frissített konfigurációt, és megkezdik az adatok küldését az új tárhelyre is.

Ezután a régi Storage-fiók leválasztása után az ügynökök abbahagyják az írást az eltávolított fiókba. A betöltési folyamat megtartja az adatoknak a fiókból való beolvasását mindaddig, amíg az összes adatot be nem tölti. Addig ne törölje a Storage-fiókot, amíg meg nem jelenik az összes napló betöltése.

Az ügynök konfigurációja néhány perc múlva frissülni fog, és az új tárterületre vált.

## <a name="manage-storage-account"></a>Storage-fiók kezelése

### <a name="load"></a>Betöltés

A Storage-fiókok bizonyos terhelésű olvasási és írási kérelmeket képesek kezelni a kérelmek szabályozásának megkezdése előtt. A szabályozás befolyásolja a naplók betöltéséhez szükséges időt, és az adatvesztéshez vezethet. Ha a tárterület túl van terhelve, regisztráljon további Storage-fiókokat, és ossza meg egymás között a terhelést. 

### <a name="related-charges"></a>Kapcsolódó díjak

A Storage-fiókokat a tárolt adatmennyiség, a tárolási típusok és a redundancia típusa alapján számítjuk fel. Részletekért lásd: a [Blobok díjszabásának](https://azure.microsoft.com/pricing/details/storage/blobs/) és a [Table Storage díjszabásának](https://azure.microsoft.com/pricing/details/storage/tables/)blokkolása.

Ha a munkaterület regisztrált Storage-fiókja egy másik régióban található, a kimenő forgalomért a [sávszélesség díjszabása](https://azure.microsoft.com/pricing/details/bandwidth/)alapján számítunk fel díjat.



## <a name="next-steps"></a>További lépések

- A privát hivatkozások beállításával kapcsolatos további információkért lásd: [az Azure privát hivatkozás használata a hálózatok biztonságos csatlakoztatásához Azure Monitor} (Private-link-security.md)
