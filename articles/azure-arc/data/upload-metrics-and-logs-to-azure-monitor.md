---
title: Használati adatok, metrikák és naplók feltöltése a Azure Monitorba
description: Az erőforrás-leltár, a használati adatok, a metrikák és a naplók feltöltése a Azure Monitorba
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7c8e92604cc6188d17411a266f8b27db55c8fbad
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317276"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>Használati adatok, metrikák és naplók feltöltése a Azure Monitorba

A monitorozás az Azure arc-kompatibilis adatszolgáltatások által biztosított számos beépített funkció egyike. 

## <a name="upload-usage-data"></a>Használati adatok feltöltése

A következő kétlépéses módon töltheti fel az Azure-ba a használati adatokat, például a leltárt és az erőforrás-használatot:

1. Exportálja a használati adatokat a ```azdata export``` paranccsal a következő módon:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the export command
   azdata arc dc export --type usage --path usage.json
   ```
   Ez a parancs létrehoz egy `usage.json` fájlt az összes Azure arc-kompatibilis adaterőforrással, például az SQL által felügyelt példányokkal és a PostgreSQL nagy kapacitású-példányokkal, amelyek az adatvezérlőn jönnek létre.

2. A használati adatok feltöltése a ```azdata upload``` parancs használatával

   > [!NOTE]
   > Várjon legalább 24 órát az Azure arc-adatkezelő létrehozása után a feltöltés futtatása előtt

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the upload command
   azdata arc dc upload --path usage.json
   ```

## <a name="upload-metrics-and-logs"></a>Metrikák és naplók feltöltése

Az Azure arc-adatszolgáltatásokkal a metrikákat és naplókat feltöltheti Azure Monitorba, így összesítheti és elemezheti a metrikákat, a naplókat, a riasztásokat, értesítéseket küldhet, vagy automatizált műveleteket indíthat el. 

Az adatoknak a Azure Monitorba való küldése lehetővé teszi, hogy a figyelést és a naplózott adatokat a telephelyen kívülről is tárolja, és az adatok hosszú távú tárolását is lehetővé tegye a speciális elemzésekhez.

Ha több olyan hellyel rendelkezik, amelyeken Azure arc-adatszolgáltatások vannak, akkor a Azure Monitor központi helyként használhatja a naplók és metrikák összegyűjtéséhez a különböző webhelyeken.

### <a name="before-you-begin"></a>Előkészületek

A naplók és a metrikák feltöltési forgatókönyvének engedélyezéséhez néhány egyszeri telepítési lépés szükséges:

1. Hozzon létre egy egyszerű szolgáltatásnév/Azure Active Directory alkalmazást, amely tartalmazza az ügyfél-hozzáférési titok létrehozását, és rendelje hozzá az egyszerű szolgáltatásnevet a "figyelési metrika közzétevője" szerepkörhöz azon előfizetés (ok) ban, ahol az adatbázis-példány erőforrásai találhatók.
2. Hozzon létre egy log Analytics-munkaterületet, és adja meg a kulcsokat, és állítsa be a környezeti változók információit.

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

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

A appId és a bérlő értékeit egy környezeti változóban mentheti későbbi használatra. 

A appId és a bérlői értékek PowerShell-lel való mentéséhez kövesse az alábbi példát:

```powershell
$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'
```

Linux vagy macOS rendszeren a appId és a bérlői értékeket is mentheti a következő példával:

   ```console
   export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

   #Example (using Linux):
   export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
   export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
   export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
   ```

Futtassa ezt a parancsot az egyszerű szolgáltatásnév hozzárendeléséhez az előfizetésben, ahol az adatbázis-példány erőforrásai találhatók:


> [!NOTE]
> Windows-környezetből való futtatáskor dupla idézőjeleket kell használnia a szerepkörök neveihez.


```console
az role assignment create --assignee <appId value from output above> --role "Monitoring Metrics Publisher" --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123

#On Windows environment
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Contributor" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

Példa a kimenetre:

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>Log Analytics-munkaterület létrehozása

Ezután hajtsa végre ezeket a parancsokat egy Log Analytics munkaterület létrehozásához, és állítsa be a hozzáférési információkat környezeti változókba.

> [!NOTE]
> Hagyja ki ezt a lépést, ha már rendelkezik munkaterülettel.

```console
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

Példa a kimenetre:

```output
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/<Subscription ID>/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
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
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
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

Az Azure arc használatára képes SQL felügyelt példányok és az Azure arc engedélyezve PostgreSQL nagy kapacitású-kiszolgálócsoportok futtatásához használt metrikák feltöltéséhez a következő CLI-parancsok használhatók:

1. Az összes metrika exportálása a megadott fájlba:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the metrics
   azdata arc dc export --type metrics --path metrics.json
   ```

2. Metrikák feltöltése az Azure monitorba:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #upload the metrics
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >Várjon legalább 30 percet, miután az Azure ív használatára jogosult adatpéldányok létre lettek hozva az első feltöltéshez
   >
   >Győződjön `upload` meg arról, hogy a metrikák a Azure monitor után azonnal `export` , csak az elmúlt 30 percben fogadják el a metrikákat. [További információ](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)


Ha az exportálás során "nem sikerült beolvasni a mérőszámokat" hibaüzenet jelenik meg, ellenőrizze, hogy az adatgyűjtés a ```true``` következő parancs futtatásával van-e beállítva:

```console
azdata arc dc config show
```

és tekintse meg a "biztonsági szakasz" részt

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
      "allowRunAsRoot": false
    },
```

Ellenőrizze, `allowNodeMetricsCollection` hogy a és a `allowPodMetricsCollection` Tulajdonságok be vannak-e állítva `true` .

## <a name="view-the-metrics-in-the-portal"></a>A metrikák megtekintése a Portalon

A metrikák feltöltése után megtekintheti őket a Azure Portal.
> [!NOTE]
> Vegye figyelembe, hogy a feltöltött adatok feldolgozásához néhány percet is igénybe vehet, mielőtt megtekintheti a metrikákat a portálon.


A metrikák a portálon való megtekintéséhez használja ezt a hivatkozást a portál megnyitásához: ezt <https://portal.azure.com> követően keresse meg az adatbázis-példány nevét a keresősáv használatával:

Megtekintheti a CPU-kihasználtságot az Áttekintés oldalon, vagy ha részletesebb mérőszámokra van szüksége, kattintson a bal oldali navigációs panelen található mérőszámokra.

Válassza az SQL Server elemet metrikus névtérként:

Válassza ki a megjeleníteni kívánt mérőszámot (több lehetőség is kiválasztható):

A gyakoriság módosítása az elmúlt 30 percre:

> [!NOTE]
> Csak az elmúlt 30 percben tölthetők fel mérőszámok. Azure Monitor elutasítja a 30 percnél régebbi metrikákat.

## <a name="upload-logs-to-azure-monitor"></a>Naplók feltöltése az Azure Monitorba

 Az Azure arc-kompatibilis SQL felügyelt példányok és a AzureArc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoportok naplófájljainak feltöltéséhez futtassa a következő CLI-parancsokat:

1. Az összes napló exportálása a megadott fájlba:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the logs
   azdata arc dc export --type logs --path logs.json
   ```

2. Naplók feltöltése egy Azure monitor log Analytics-munkaterületre:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #Upload the logs
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>Naplók megtekintése Azure Portal

A feltöltött naplókat a következő módszerrel tudja lekérdezni a naplólekérdezés-kezelővel:

1. Nyissa meg a Azure Portal, majd keresse meg a munkaterületet név szerint a keresősáv tetején, majd jelölje ki
2. Kattintson a bal oldali panelen lévő Naplók elemre
3. Kattintson az első lépések elemre (vagy a Első lépések oldalon található hivatkozásokra kattintva további információkat tudhat meg a Log Analyticsról, ha új)
4. Kövesse az oktatóanyagot, ha többet szeretne megtudni Log Analyticsről, ha első alkalommal használja Log Analytics
5. Bontsa ki a táblázatok listájának alján lévő Egyéni naplók elemet, és látni fog egy „sql_instance_logs_CL” nevű táblázatot.
6. Kattintson a táblázat neve melletti „szem” ikonra
7. Kattintson a „Megtekintés a lekérdezésszerkesztőben” gombra
8. Ekkor megjelenik egy lekérdezés a lekérdezés-szerkesztőben, amely megjeleníti a napló legutóbbi 10 eseményét
9. Innen kísérletezhet a naplók lekérdezésszerkesztővel való lekérdezésével, riasztások beállításával stb.

## <a name="automating-uploads-optional"></a>Feltöltések automatizálása (nem kötelező)

Ha a mérőszámokat és a naplókat ütemezett alapon szeretné feltölteni, létrehozhat egy parancsfájlt, és néhány percenként elvégezheti az időzítő futtatását. Az alábbi példa a feltöltések automatizálását mutatja be egy linuxos rendszerhéj-parancsfájl használatával.

A kedvenc szöveg/kód szerkesztőjében adja hozzá a következő parancsfájlt a fájlhoz, és mentse parancsfájlként végrehajtható fájlként, például. sh (Linux/Mac) vagy. cmd,. bat,. ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

A parancsfájl végrehajtható fájljának elkészítése

```console
chmod +x myuploadscript.sh
```

Futtassa a szkriptet 20 percenként:

```console
watch -n 1200 ./myuploadscript.sh
```

Olyan Feladatütemezőt is használhat, mint például a cron vagy a Windows Feladatütemező, vagy egy Orchestrator, például a Ansible, a Puppet vagy a Chef.

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Általános útmutató a használat, a metrikák exportálásához és feltöltéséhez

Az Azure arc-kompatibilis adatszolgáltatások létrehozási, olvasási, frissítési és törlési műveleteinek naplózása számlázási és figyelési célból történik. Vannak olyan háttér-szolgáltatások, amelyek figyelik ezeket a szifilisz-műveleteket, és megfelelően kiszámítják a felhasználást. A használat vagy a felhasználás tényleges kiszámítása ütemezett alapon történik, és a háttérben történik. 

Az előzetes verzió ideje alatt ez a folyamat éjjel történik. Az általános útmutató a használat napi egyszeri feltöltése. Ha a használati adatokat több alkalommal exportálják és töltötték fel ugyanazon a 24 órás időszakon belül, akkor csak az erőforrás-leltár frissül Azure Portal de nem az erőforrás-használat.

A metrikák feltöltéséhez az Azure monitor csak az utolsó 30 perces adatokat fogadja el ([További információ](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)). A metrikák feltöltésére vonatkozó útmutató a metrikák az exportfájl létrehozása utáni azonnali feltöltésére szolgál, így megtekintheti a teljes adathalmazt Azure Portalban. Ha például a 2:00 ÓRAKOR exportálta a metrikákat, és a feltöltési parancsot 2:50 ÓRAKOR futtatta. Mivel a Azure Monitor csak az elmúlt 30 percben fogadja az adatgyűjtést, előfordulhat, hogy a portálon nem jelennek meg az összes információ. 

## <a name="next-steps"></a>Következő lépések

[Számlázási adatok feltöltése az Azure-ba, és megtekintés a Azure Portal](view-billing-data-in-azure.md)

[Az Azure arc adatkezelő erőforrásának megtekintése Azure Portal](view-data-controller-in-azure-portal.md)
