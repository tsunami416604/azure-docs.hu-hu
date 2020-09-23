---
title: Erőforrás-leltár, használati adatok, metrikák és naplók feltöltése a Azure Monitorba
description: Erőforrás-leltár, használati adatok, metrikák és naplók feltöltése a Azure Monitorba
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ac6ffd2b5bf48079db6a0cd261dbe2535e1821ac
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936040"
---
# <a name="upload-resource-inventory-usage-data-metrics-and-logs-to-azure-monitor"></a>Erőforrás-leltár, használati adatok, metrikák és naplók feltöltése a Azure Monitorba

Az Azure arc-adatszolgáltatásokkal a metrikákat és naplókat feltöltheti Azure Monitorba, így összesítheti és elemezheti a metrikákat, a naplókat, a riasztásokat, értesítéseket küldhet, vagy automatizált *műveleteket indíthat el* . Az adatoknak a Azure Monitorba való küldése lehetővé teszi, hogy a figyelést és a naplózott adatokat a webhelyről és az adatok hosszú távú tárolására is lehetővé tegye a fejlett analitikai adatok számára.  Ha több olyan hellyel rendelkezik, amelyeken Azure arc-adatszolgáltatások vannak, akkor a Azure Monitor központi helyként használhatja a naplók és metrikák összegyűjtéséhez a különböző webhelyeken.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>Előkészületek

A naplók és a metrikák feltöltési forgatókönyvének engedélyezéséhez néhány egyszeri telepítési lépés szükséges:

1) Hozzon létre egy egyszerű szolgáltatásnév/Azure Active Directory alkalmazást, amely tartalmazza az ügyfél-hozzáférési titok létrehozását, és rendelje hozzá az egyszerű szolgáltatásnevet a "figyelési metrika közzétevője" szerepkörhöz azon előfizetés (ok) ban, ahol az adatbázis-példány erőforrásai találhatók.
2) Hozzon létre egy log Analytics-munkaterületet, és adja meg a kulcsokat, és állítsa be a környezeti változók információit.

Az első tétel a metrikák feltöltéséhez szükséges, a második pedig a naplók feltöltéséhez szükséges.

Ezeket a parancsokat követve hozza létre a mérőszámok feltöltési egyszerű szolgáltatását, és rendelje hozzá a "figyelés mérőszámok közzétevője" és a "közreműködői" szerepkörökhöz, hogy az egyszerű szolgáltatásnév feltöltse a metrikákat, és hogyan végezhet létrehozási és feltöltési műveleteket.

## <a name="create-service-principal-and-assign-roles"></a>Egyszerű szolgáltatásnév létrehozása és szerepkörök társítása

Az alábbi parancsokkal hozza létre a metrikák feltöltésére szolgáló egyszerű szolgáltatásnevet, és rendelje hozzá a "figyelési mérőszámok közzétevője" szerepkörhöz:

Egyszerű szolgáltatásnév létrehozásához futtassa a következő parancsot:

> [!NOTE]
> Egy egyszerű szolgáltatásnév létrehozásához [bizonyos engedélyek szükségesek az Azure-ban](/active-directory/develop/howto-create-service-principal-portal#required-permissions).

```console
az ad sp create-for-rbac --name <a name you choose>

#Example:
#az ad sp create-for-rbac --name azure-arc-metrics
```

Példa a kimenetre:

```console
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

A appId és a bérlői értékek mentése egy környezeti változóba későbbi használatra:

```console
#PowerShell

$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Linux/macOS

export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Example (using Linux):
export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
```

Futtassa ezt a parancsot az egyszerű szolgáltatásnév hozzárendeléséhez az előfizetésben, ahol az adatbázis-példány erőforrásai találhatók:

```console
az role assignment create --assignee <appId value from output above> --role 'Monitoring Metrics Publisher' --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Monitoring Metrics Publisher' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

Példa a kimenetre:

```console
{
  "canDelegate": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>Log Analytics-munkaterület létrehozása

Ezután hajtsa végre ezeket a parancsokat egy Log Analytics munkaterület létrehozásához, és állítsa be a hozzáférési információkat környezeti változókba.

> [!NOTE]
> Hagyja ki ezt a lépést, ha már rendelkezik munkaterülettel.

```console
az monitor log-analytics workspace create --resource-group <resource group name> --name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --name MyLogsWorkpace
```

Példa a kimenetre:

```console
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
  "location": "eastus",
  "name": "user-logworkspace",
  "portalUrl": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "user-arc-demo",
  "retentionInDays": 30,
  "sku": {
    "lastSkuUpdate": "Thu, 30 Jul 2020 22:37:53 GMT",
    "maxCapacityReservationLevel": 3000,
    "name": "pergb2018"
  },
  "source": "Azure",
  "tags": null,
  "type": "Microsoft.OperationalInsights/workspaces"
}
```

## <a name="assign-id-and-shared-key-to-environment-variables"></a>AZONOSÍTÓ és megosztott kulcs társítása környezeti változókhoz

Mentse a Vevőkód (munkaterület-azonosítót) környezeti változóként, amelyet később szeretne használni:

```console
#PowerShell
$Env:WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Linux/macOS
export WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Example (using Linux)
#export WORKSPACE_ID='d6abb435-2626-4df1-b887-445fe44a4123'
```

Ez a parancs a log Analytics-munkaterülethez való kapcsolódáshoz szükséges hozzáférési kulcsokat fogja kinyomtatni:

```console
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --name MyLogsWorkpace
```

Példa a kimenetre:

```console
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

Mentse az elsődleges kulcsot egy környezeti változóban később használni kívánt módon:

```console
#PowerShell:
$Env:WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Linux/macOS:
export WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Example (using Linux):
export WORKSPACE_SHARED_KEY='JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123=='

```

## <a name="set-final-environment-variables-and-confirm"></a>Állítsa be a végső környezeti változókat, és erősítse meg

Az SPN-szolgáltató URL-címének beállítása környezeti változóban:

```console
#PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'

#Linux/macOS:
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

Győződjön meg arról, hogy az összes szükséges környezeti változó be van állítva, ha szeretné:

```console
#PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY

#Linux/macOS
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

## <a name="upload-metrics-to-azure-monitor"></a>Metrikák feltöltése a Azure Monitorba

Az Azure SQL felügyelt példányaihoz tartozó mérőszámok feltöltéséhez és a Azure Database for PostgreSQL nagy kapacitású-kiszolgálócsoportok futtatásához a következő CLI-parancsok használhatók:

Ez az összes metrikát a megadott fájlba exportálja:

```console
azdata arc dc export -t metrics --path metrics.json
```

Ez az Azure Monitorba tölti fel a metrikákat:

```console
azdata arc dc upload --path metrics.json
```

## <a name="view-the-metrics-in-the-portal"></a>A metrikák megtekintése a Portalon

A metrikák feltöltése után képes lesz megjeleníteni az ábrázolásukat az Azure Portalról.

A metrikák a portálon való megtekintéséhez használja ezt a speciális hivatkozást a portál megnyitásához: ezt <https://portal.azure.com> követően keresse meg az adatbázis-példány nevét a keresősáv alatt:

Megtekintheti a CPU-kihasználtságot az Áttekintés oldalon, vagy ha részletesebb mérőszámokra van szüksége, kattintson a bal oldali navigációs panelen található mérőszámokra.

Válassza az SQL Server elemet metrikus névtérként:

Válassza ki a megjeleníteni kívánt mérőszámot (több lehetőség is kiválasztható):

A gyakoriság módosítása az elmúlt 30 percre:

> [!NOTE]
> Csak az elmúlt 30 percben tölthetők fel mérőszámok. Azure Monitor elutasítja a 30 percnél régebbi metrikákat.

## <a name="upload-logs-to-azure-monitor"></a>Naplók feltöltése az Azure Monitorba

 Az Azure SQL felügyelt példányaihoz tartozó naplók feltöltéséhez és a Azure Database for PostgreSQL nagy kapacitású-kiszolgálócsoportok futtatásához futtassa a következő CLI-parancsokat:

Ez az összes naplót a megadott fájlba exportálja:

```console
azdata arc dc export -t logs --path logs.json
```

Ez a naplókat az Azure monitor log Analytics-munkaterületére tölti fel:

```console
azdata arc dc upload --path logs.json
```

## <a name="view-your-logs-in-azure-portal"></a>Naplók megtekintése Azure Portal

A feltöltött naplókat a következő módszerrel tudja lekérdezni a naplólekérdezés-kezelővel:

1. Nyissa meg a Azure Portal, majd keresse meg a munkaterületet név szerint a keresősáv tetején, majd jelölje ki
2. Kattintson a bal oldali panelen lévő Naplók elemre
3. Kattintson az első lépések elemre (vagy a Első lépések oldalon található hivatkozásokra kattintva további információkat tudhat meg a Log Analyticsról, ha új)
4. Ha első alkalommal szeretne többet megtudni a Log Analyticsről, kövesse az oktatóanyagot.
5. Bontsa ki a táblázatok listájának alján lévő Egyéni naplók elemet, és látni fog egy „sql_instance_logs_CL” nevű táblázatot.
6. Kattintson a táblázat neve melletti „szem” ikonra
7. Kattintson a „Megtekintés a lekérdezésszerkesztőben” gombra
8. Megnyílik egy lekérdezés a lekérdezésszerkesztőben, amely a naplóban foglalt 10 legfrissebb eseményt jeleníti meg
9. Innen kísérletezhet a naplók lekérdezésszerkesztővel való lekérdezésével, riasztások beállításával stb.

## <a name="automating-metrics-and-logs-uploads-optional"></a>Metrikák és naplók feltöltésének automatizálása (nem kötelező)

Ha folyamatosan szeretné feltölteni a metrikákat és a naplókat, létrehozhat egy parancsfájlt, és néhány percenként futtathatja azt egy időzítőn.  Az alábbi példa a feltöltések automatizálását mutatja be egy linuxos rendszerhéj-parancsfájl használatával.

A kedvenc szöveg/kód szerkesztőjében adja hozzá a következőt a fájlhoz, és mentse parancsfájlként végrehajtható fájlként, például. sh (Linux/Mac) vagy. cmd,. bat,. ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

A parancsfájl végrehajtható fájljának elkészítése

```console
chmod +x myuploadscript.sh
```

Futtassa a szkriptet 2 percenként:

```console
watch -n 120 ./myuploadscript.sh
```

Olyan Feladatütemezőt is használhat, mint például a cron vagy a Windows Feladatütemező, vagy egy Orchestrator, például a Ansible, a Puppet vagy a Chef.
