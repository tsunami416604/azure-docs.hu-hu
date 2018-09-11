---
title: Tekintse át a Security Center szabályzatoknak való megfelelés, Azure REST API-val |} A Microsoft Docs
description: Ismerje meg, tekintse át a jelenlegi szabályzatoknak való megfelelőségét, a Security Center az Azure REST API-k használatával.
services: security-center
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: alleonar
ms.openlocfilehash: 1443486590859aac5591aff2ab0551bed9228d7b
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44301749"
---
# <a name="review-security-center-policy-compliance-using-rest-apis"></a>Tekintse át a Security Center szabályzatoknak való megfelelés REST API-k használatával

A Security Center rendszeresen ellenőrzi a meghatározott biztonsági házirendek ellen az Azure-erőforrások. A Security Center is biztosít, amellyel áttekintheti a megfelelőséget a saját alkalmazások; a REST API-val közvetlenül le a szolgáltatást, vagy JSON-eredményeket importálása más alkalmazásokat is. 

Itt megtudhatja a jelenlegi javaslatcsoportot lekérése egy előfizetéshez társított összes Azure-erőforrást.

A jelenlegi javaslatcsoportot lekéréséhez:
``` http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/tasks?api-version={api-version}
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>A kérelem létrehozása  

A `{subscription-id}` paraméter megadása kötelező, és tartalmaznia kell az Azure-előfizetést a házirendek meghatározása az előfizetés-azonosítója. Ha több előfizetéssel rendelkezik, tekintse meg [több előfizetés használata](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions).  

A `api-version` paraméter megadása kötelező. Ezeket a végpontokat jelenleg csak a támogatott `api-version=2015-06-01-preview`. 

A következő fejléceket szükség: 

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*A Content-Type:*|Kötelező. Állítsa be `application/json`.|  
|*Hitelesítés:*|Kötelező. Egy érvényes értékre `Bearer` [hozzáférési jogkivonat](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |  

## <a name="response"></a>Válasz  

Állapotkód: 200 (OK) adja vissza a sikeres válasz, amely biztonságossá tétele az Azure-erőforrások ajánlott feladatok listáját tartalmazza.

``` json
{  
  "value": [  
    {  
       "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{region}/tasks/{task-id}",
       "name": "{task_id}",
       "type": "Microsoft.Security/locations/{region}/tasks",
       "properties": {
       "state": "Active",
       "subState": "NA",
       "creationTimeUtc": "{create-time}",
       "lastStateChangeTimeUtc": "{last-state-change}",
       "securityTaskParameters": "{security-task-properties}"
    } 
  ]  
}  
```  

Egyes elemeiről **érték** ajánlás jelöli:

|Válasz tulajdonság|Leírás|
|----------------|----------|
|**állapot** | Azt jelzi, hogy javaslatot `active` vagy `resolved`. |
|**CreationTimeUtc** | Dátum és időpont (UTC), a javaslat létrehozásakor megjelenítése. |
|**lastStateChangeUtc** | Dátum és idő, UTV, a legutóbbi állapot változik, ha van ilyen. |
|**securityTaskParameters** | Az ajánlás; részletei az alapul szolgáló ajánlás tulajdonságok függően változnak. |
||
  
Jelenleg támogatott javaslatokért lásd: [biztonsági ajánlások megvalósítása](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Más állapotkódok hibaállapotok jelzik. Ezekben az esetekben a Válaszobjektum elmagyarázza, hogy miért nem sikerült a kérés leírása tartalmazza.

``` json
{  
  "value": [  
    {  
      "description": "Error response describing why the operation failed."  
    }  
  ]  
}  
```  

## <a name="example-response"></a>Példaválasz  

``` json
{  
  "value": [  
        {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{region}/tasks/{task-id}",
            "name": "{task_id}",
            "type": "Microsoft.Security/locations/{region}/tasks",
            "properties": {
                "state": "Active",
                "subState": "NA",
                "creationTimeUtc": "{create-time}",
                "lastStateChangeTimeUtc": "{last-state-change}",
                "securityTaskParameters": {
                    "vmId": "/subscriptions/{subscription-id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}",
                    "vmName": "{vm_name}",
                    "severity": "{severity}",
                    "isOsDiskEncrypted": {is_os_disk_encrypted},
                    "isDataDiskEncrypted": {is_data_disk_encrypted},
                    "name": "EncryptionOnVm",
                    "uniqueKey": "EncryptionOnVmTaskParameters_/subscriptions/{subscription-id}/resourceGroups/{resoource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}",
                    "resourceId": "/subscriptions/{subscription_id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}"
                }
            }
        },
        {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{location}/tasks/{task-id}",
            "name": "{task-id}",
            "type": "Microsoft.Security/locations/{region}/tasks",
            "properties": {
                "state": "Active",
                "subState": "NA",
                "creationTimeUtc": "{create-time}",
                "lastStateChangeTimeUtc": "{last-state-change}",
                "securityTaskParameters": {
                    "serverName": "{sql-server-name}",
                    "serverId": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}",
                    "name": "Enable auditing for the SQL server",
                    "uniqueKey": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}/auditingPolicies/Default",
                    "resourceId": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}"
                }
            }
        }  ]  
}  
```  

A válaszból kiderül, két javaslatok; a lista minden eleme egy adott javaslatot felel meg. Az első javasolja a tároló a Linux rendszerű virtuális gép titkosított, és a második javasol egy SQL Server naplózásának engedélyezése.

A javaslatok a szabályzatok, hogy engedélyezve van a függően változnak. További, beleértve a jelenleg elérhető javaslatok: [biztonsági javaslatok kezelése az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations).


## <a name="see-also"></a>Lásd még  
- [Biztonsági szabályzatok beállítása](https://docs.microsoft.com/azure/security-center/security-center-policies-overview)
- [Az Azure Security erőforrás-szolgáltató REST API-val](https://msdn.microsoft.com/library/azure/mt704034.aspx)   
- [Azure REST API használatának első lépései](https://docs.microsoft.com/rest/api/azure/)   
- [Az Azure Security Center PowerShell-modul](https://www.powershellgallery.com/packages/Azure-Security-Center/0.0.22)
