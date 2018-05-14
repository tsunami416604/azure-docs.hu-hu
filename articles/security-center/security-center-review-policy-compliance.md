---
title: REST API-t Azure Security Center szabályzatoknak való megfelelés ellenőrzése |} Microsoft Docs
description: Ismerje meg, tekintse át az aktuális szabályzatoknak való megfelelőségét, a Security Center Azure REST API-k használatával.
services: security-center
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: alleonar
ms.openlocfilehash: 6c6764eec59633f0bdd0fa396c1581117a0c1e1d
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
---
# <a name="review-security-center-policy-compliance-using-rest-apis"></a>Tekintse át a Security Center szabályzatoknak való megfelelés REST API-k használatával

A Security Center rendszeresen ellenőrzi az Azure-erőforrások a meghatározott biztonsági házirendek szerinti. A Security Center is biztosít a REST API-t, amely lehetővé teszi, hogy tekintse át a megfelelőségüket a saját alkalmazásai; közvetlenül lekérdezni a szolgáltatást, vagy JSON-eredményeket importálása más alkalmazások. 

Itt megismerheti egy előfizetéshez társított összes Azure-erőforrások aktuális készletében lévő javaslatok lekérdezni.

Az aktuális készletében lévő javaslatok beolvasása:
``` http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/tasks?api-version={api-version}
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>A kérelem létrehozása  

A `{subscription-id}` paraméter szükséges, és tartalmaznia kell az előfizetési Azonosítóját az Azure-előfizetés a házirendek meghatározása. Ha több előfizetéssel rendelkezik, tekintse meg [működik-e több előfizetés](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions).  

A `api-version` paraméter megadása kötelező. Ezeket a végpontokat jelenleg csak a támogatott `api-version=2015-06-01-preview`. 

A következő fejléc szükség: 

|Fejléc|Leírás|  
|--------------------|-----------------|  
|*Content-Type:*|Kötelező. Beállítása `application/json`.|  
|*Engedélyezési:*|Kötelező. Egy érvényes értékre `Bearer` [hozzáférési jogkivonat](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |  

## <a name="response"></a>Válasz  

Állapotkód 200 (OK) visszaküldött a sikeres válasz, amely az Azure-erőforrások biztonságossá ajánlott feladatok listáját tartalmazza.

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

Az egyes elemek **érték** ajánlást jelöli:

|Válasz tulajdonság|Leírás|
|----------------|----------|
|**Állapot** | Azt jelzi, hogy a javaslat `active` vagy `resolved`. |
|**creationTimeUtc** | Dátum és idő, az UTC-t, az ajánlás létrehozásának megjelenítő. |
|**lastStateChangeUtc** | Dátum és idő, a UTV, az utolsó állapotváltozás, ha van ilyen. |
|**securityTaskParameters** | A javaslat; részletek tulajdonságok az alapul szolgáló ajánlás függvényében. |
||
  
A jelenleg támogatott vonatkozó javaslatokkal kapcsolatban lásd: [biztonsági ajánlások megvalósításával](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Más állapotkódok hibaállapotok jelzi. Ezekben az esetekben a válasz-objektum tartalmaz egy leírást, amely ismerteti, hogy miért volt sikertelen a kérelem.

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

Ez a válasz jeleníti meg két javaslatok; a lista minden eleme egy adott javaslat felel meg. Az első azt javasolja, hogy a Linux virtuális gépek a tárolás titkosítása és a második javasol egy SQL Server-naplózás engedélyezése.

A javaslatok engedélyezése a házirendek függvényében. További, beleértve az aktuálisan elérhető javaslatokat [biztonsági javaslatok kezelése az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations).


## <a name="see-also"></a>Lásd még  
- [Biztonsági szabályzatok beállítása](https://docs.microsoft.com/azure/security-center/security-center-policies-overview)
- [Az Azure biztonsági erőforrás-szolgáltató REST API-n](https://msdn.microsoft.com/library/azure/mt704034.aspx)   
- [Ismerkedés az Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
- [Az Azure Security Center PowerShell-modul](https://www.powershellgallery.com/packages/Azure-Security-Center/0.0.22)
