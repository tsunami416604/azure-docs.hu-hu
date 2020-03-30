---
title: Egyszerű szolgáltatás hozzáadása az Azure Analysis Services felügyeleti szerepkörhöz | Microsoft dokumentumok
description: Megtudhatja, hogy miként adhat hozzá egyszerű automatizálási szolgáltatást az Azure Analysis Services kiszolgálófelügyeleti szerepköréhez
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: 1370f65405963ebf825e986e6801607a0d96156e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78298088"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Egyszerű szolgáltatás hozzáadása a kiszolgálói rendszergazdai szerepkörhöz 

 A felügyelet nélküli PowerShell-feladatok automatizálásához az egyszerű szolgáltatásnak **kiszolgálói rendszergazdai** jogosultságokkal kell rendelkeznie a felügyelt Analysis Services-kiszolgálón. Ez a cikk azt ismerteti, hogyan adhat hozzá egy egyszerű szolgáltatás egy Azure AS-kiszolgálón a kiszolgáló rendszergazdái szerepkört. Ezt az SQL Server Management Studio vagy a Resource Manager sablon használatával teheti meg.

## <a name="before-you-begin"></a>Előkészületek
A feladat befejezése előtt rendelkeznie kell egy egyszerű szolgáltatás regisztrálva az Azure Active Directoryban.

[Egyszerű szolgáltatás létrehozása - Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Szolgáltatásnév létrehozása – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>Az SQL Server Management Studio használata

A kiszolgáló rendszergazdáit az SQL Server Management Studio (SSMS) segítségével állíthatja be. A feladat végrehajtásához [kiszolgálói rendszergazdai](analysis-services-server-admins.md) engedélyekkel kell rendelkeznie az Azure AS-kiszolgálón. 

1. Az SSMS-ben csatlakozzon az Azure AS-kiszolgálóhoz.
2. A **Kiszolgáló tulajdonságai biztonsága** > **párbeszédpanelen**kattintson a **Hozzáadás**gombra.
3. A **Felhasználó vagy csoport kiválasztása**csoportban keresse meg a regisztrált alkalmazást név szerint, válassza a **Hozzáadás**gombot.

    ![Egyszerű szolgáltatásfiók keresése](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Ellenőrizze az egyszerű szolgáltatásfiók-azonosítót, majd kattintson **az OK gombra.**
    
    ![Egyszerű szolgáltatásfiók keresése](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>Erőforrás-kezelő sablon használata

A kiszolgálórendszergazdákat az Analysis Services-kiszolgáló Azure Resource Manager-sablon használatával történő telepítésével is konfigurálhatja. A központi telepítést futtató identitásnak az Erőforrás **közreműködői** szerepköréhez kell tartoznia az [Azure szerepköralapú hozzáférés-vezérlésben (RBAC).](../role-based-access-control/overview.md)

> [!IMPORTANT]
> A szolgáltatásnév formátumot kell `app:{service-principal-client-id}@{azure-ad-tenant-id}`hozzáadni.

A következő Erőforrás-kezelő sablon egy Analysis Services-kiszolgálót telepít az Analysis Services felügyeleti szerepkörhöz hozzáadott szolgáltatással:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "analysisServicesServerName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "analysisServicesSkuName": {
            "type": "string"
        },
        "analysisServicesCapacity": {
            "type": "int"
        },
        "servicePrincipalClientId": {
            "type": "string"
        },
        "servicePrincipalTenantId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('analysisServicesServerName')]",
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('analysisServicesSkuName')]",
                "capacity": "[parameters('analysisServicesCapacity')]"
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "[concat('app:', parameters('servicePrincipalClientId'), '@', parameters('servicePrincipalTenantId'))]"
                    ]
                }
            }
        }
    ]
}
```

## <a name="using-managed-identities"></a>Felügyelt identitások használata

Felügyelt identitás is hozzáadható az Analysis Services Rendszergazdák listájához. Előfordulhat például, hogy rendelkezik [egy rendszeráltal hozzárendelt felügyelt identitással rendelkező logikai alkalmazással,](../logic-apps/create-managed-service-identity.md)és szeretné biztosítani számára az Analysis Services-kiszolgáló felügyeletének lehetőségét.

Az Azure Portal és API-k legtöbb részében a felügyelt identitások a szolgáltatásegyszerű objektumazonosítójuk használatával kerülnek azonosításra. Az Analysis Services azonban megköveteli, hogy az ügyfélazonosítójuk kal azonosítsa őket. Az ügyfélazonosító beszerzéséhez egy egyszerű szolgáltatás, használhatja az Azure CLI:

```bash
az ad sp show --id <ManagedIdentityServicePrincipalObjectId> --query appId -o tsv
```

Másik lehetőségként használhatja a PowerShellt:

```powershell
(Get-AzureADServicePrincipal -ObjectId <ManagedIdentityServicePrincipalObjectId>).AppId
```

Ezután használhatja ezt az ügyfél-azonosítót a bérlői azonosítóval együtt, hogy hozzáadja a felügyelt identitást az Analysis Services Rendszergazdák listájához, a fent leírtak szerint.

## <a name="related-information"></a>Kapcsolódó információk

* [SQL Server PowerShell modul letöltése](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [SSMS letöltése](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


