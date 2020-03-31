---
title: Blob-kimenet hitelesítése felügyelt identitással az Azure Stream Analytics segítségével
description: Ez a cikk ismerteti, hogyan használhatja a felügyelt identitások az Azure Stream Analytics-feladat hitelesítéséhez az Azure Blob storage kimenetén.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 13f48a9e0bc3ed8f8c4d5f1b7da4b6c03f54cdf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129977"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage-output"></a>Az Azure Stream Analytics-feladat azure blob storage-kimenetre való hitelesítéséhez használja a felügyelt identitást

[Felügyelt identitás hitelesítése](../active-directory/managed-identities-azure-resources/overview.md) kimeneti Azure Blob storage biztosít Stream Analytics-feladatok közvetlen hozzáférést biztosít a tárfiók használata helyett egy kapcsolati karakterláncot. A fokozott biztonság mellett ez a funkció azt is lehetővé teszi, hogy adatokat írjon egy tárfiókba egy virtuális hálózat (VNET) az Azure-on belül.

Ez a cikk bemutatja, hogyan engedélyezheti a felügyelt identitást a Stream Analytics-feladat Blob kimenet(ek)hez az Azure Portalon és egy Azure Resource Manager-telepítésen keresztül.

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>A Stream Analytics-feladat létrehozása az Azure Portalhasználatával

1. Hozzon létre egy új Stream Analytics-feladatot, vagy nyisson meg egy meglévő feladatot az Azure Portalon. A képernyő bal oldalán található menüsorban válassza a **Konfigurálás**csoportban a **Felügyelt identitás** lehetőséget. Győződjön meg arról, hogy a "Rendszeráltal hozzárendelt felügyelt identitás használata" lehetőség van kiválasztva, majd kattintson a **mentés** gombra a képernyő alján.

   ![A Stream Analytics felügyelt identitásának konfigurálása](./media/common/stream-analytics-enable-managed-identity.png)

2. Az Azure Blob storage kimeneti kimeneti fogadójának kimeneti tulajdonságok ablakában válassza a Hitelesítési mód legördülő menüt, és válassza a **Felügyelt identitás lehetőséget.** A többi kimeneti tulajdonsággal kapcsolatos további információkért [lásd: Az Azure Stream Analytics kimeneteinek megértése.](./stream-analytics-define-outputs.md) Amikor végzett, kattintson a **Mentés** gombra.

   ![Az Azure Blob storage-kimenetének konfigurálása](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. Most, hogy a feladat létrejött, tekintse meg [a Stream Analytics-feladat hozzáférésének a tárfiókhoz](#give-the-stream-analytics-job-access-to-your-storage-account) című szakaszát ebben a cikkben.

## <a name="azure-resource-manager-deployment"></a>Az Azure Resource Manager üzembe helyezése

Az Azure Resource Manager használatával teljes mértékben automatizálhatja a Stream Analytics-feladat üzembe helyezését. A Resource Manager-sablonokat az Azure PowerShell vagy az [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)használatával telepítheti. Az alábbi példák az Azure CLI használatával.


1. **Microsoft.StreamAnalytics/streamingjobs** erőforrást felügyelt identitással úgy hozhat létre, hogy a következő tulajdonságot építi fel az Erőforrás-kezelő sablon erőforrásszakaszában:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Ez a tulajdonság arra utasítja az Azure Resource Managert, hogy hozza létre és kezelje a Stream Analytics-feladat identitását. Az alábbiakban egy példa: Resource Manager-sablon, amely telepíti a Stream Analytics-feladatot felügyelt identitás engedélyezve van, és egy Blob kimeneti fogadó, amely felügyelt identitást használ:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "MyStreamingJob",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "serialization": {
                                    "type": "JSON",
                                    "properties": {
                                        "encoding": "UTF8"
                                    }
                                },
                                "datasource":{
                                    "type":"Microsoft.Storage/Blob",
                                    "properties":{
                                        "storageAccounts": [
                                            { "accountName": "MyStorageAccount" }
                                        ],
                                        "container": "test",
                                        "pathPattern": "segment1/{date}/segment2/{time}",
                                        "dateFormat": "yyyy/MM/dd",
                                        "timeFormat": "HH",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    A fenti feladat az alábbi Azure CLI-parancs használatával telepíthető az **ExampleGroup** erőforráscsoportba:

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. A feladat létrehozása után az Azure Resource Manager segítségével lekérheti a feladat teljes definícióját.

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    A fenti parancs az alábbihoz hasonló választ ad vissza:

    ```json
    {
        "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/streamingjobs/{RESOURCE_NAME}",
        "identity": {
            "principalId": "{PRINCIPAL_ID}",
            "tenantId": "{TENANT_ID}",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "{RESOURCE_NAME}",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "{JOB_ID}",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "{RESOURCE_GROUP}",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

   Vegye figyelembe a **rendszerbiztonsági azonosítót** a feladat definíciója, amely azonosítja a feladat felügyelt identitás az Azure Active Directoryban, és a következő lépésben a rendszer a Stream Analytics-feladat hozzáférést a tárfiókhoz.

3. Most, hogy a feladat létrejött, tekintse meg [a Stream Analytics-feladat hozzáférésének a tárfiókhoz](#give-the-stream-analytics-job-access-to-your-storage-account) című szakaszát ebben a cikkben.


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>A Stream Analytics-feladat hozzáférésének megadása a tárfiókhoz

A Stream Analytics-feladat nak két hozzáférési szintet adhat:

1. **Tárolószintű hozzáférés:** ez a beállítás hozzáférést biztosít a feladathoz egy adott meglévő tárolóhoz.
2. **Fiókszintű hozzáférés:** ez a beállítás általános hozzáférést biztosít a feladatnak a tárfiókhoz, beleértve az új tárolók létrehozását is.

Hacsak nincs szüksége a feladatra, hogy tárolókat hozzon létre az Ön nevében, válassza **a Tároló szintű hozzáférést,** mivel ez a beállítás megadja a feladatnak a szükséges minimális hozzáférési szintet. Mindkét lehetőség az alábbiakban ismertetjük az Azure Portalon és a parancssorban.

### <a name="grant-access-via-the-azure-portal"></a>Hozzáférés megadása az Azure Portalon keresztül

#### <a name="container-level-access"></a>Tárolószintű hozzáférés

1. Keresse meg a tároló konfigurációs ablaktábláját a tárfiókon belül.

2. A bal oldalon válassza a **hozzáférés-vezérlés (IAM)** lehetőséget.

3. A "Szerepkör-hozzárendelés hozzáadása" szakaszban kattintson a **Hozzáadás gombra.**

4. A szerepkör-hozzárendelés ablaktáblán:

    1. A **szerepkör** beállítása "Storage Blob Data Contributor" (Tárolóblob-adatközreműködő)
    2. Győződjön meg arról, hogy a **hozzáférés hozzárendelése a** legördülő menü "Azure AD-felhasználó, csoport vagy egyszerű szolgáltatás" lesz.
    3. Írja be a Stream Analytics-feladat nevét a keresőmezőbe.
    4. Válassza ki a Stream Analytics-feladatot, és kattintson a **Mentés gombra.**

   ![Tárolóhozzáférés megadása](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>Fiókszintű hozzáférés

1. Nyissa meg a tárfiókot.

2. A bal oldalon válassza a **hozzáférés-vezérlés (IAM)** lehetőséget.

3. A "Szerepkör-hozzárendelés hozzáadása" szakaszban kattintson a **Hozzáadás gombra.**

4. A szerepkör-hozzárendelés ablaktáblán:

    1. A **szerepkör** beállítása "Storage Blob Data Contributor" (Tárolóblob-adatközreműködő)
    2. Győződjön meg arról, hogy a **hozzáférés hozzárendelése a** legördülő menü "Azure AD-felhasználó, csoport vagy egyszerű szolgáltatás" lesz.
    3. Írja be a Stream Analytics-feladat nevét a keresőmezőbe.
    4. Válassza ki a Stream Analytics-feladatot, és kattintson a **Mentés gombra.**

   ![Fiókhozzáférés megadása](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>Hozzáférés megadása a parancssoron keresztül

#### <a name="container-level-access"></a>Tárolószintű hozzáférés

Egy adott tárolóhoz való hozzáférés hez futtassa a következő parancsot az Azure CLI használatával:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>Fiókszintű hozzáférés

A teljes fiókhoz való hozzáféréshez futtassa a következő parancsot az Azure CLI használatával:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>VNET-hozzáférés engedélyezése

A tárfiók **tűzfalainak és virtuális hálózatainak**konfigurálásakor tetszés szerint engedélyezheti a hálózati forgalmat más megbízható Microsoft-szolgáltatásokból. Amikor a Stream Analytics a Felügyelt identitás használatával hitelesíti magát, bizonyítja, hogy a kérelem egy megbízható szolgáltatásból származik. Az alábbiakban a virtuális hálózat hozzáférési kivétel engedélyezésére vonatkozó utasításokat olvashat.

1.  Keresse meg a "Tűzfalak és virtuális hálózatok" ablaktáblát a tárfiók konfigurációs ablaktábláján.
2.  Győződjön meg arról, hogy a "Megbízható Microsoft-szolgáltatások hozzáférhetnek ehhez a tárfiókhoz" beállítás engedélyezve van.
3.  Ha engedélyezte, kattintson a **Mentés gombra.**

   ![VNET-hozzáférés engedélyezése](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="limitations"></a>Korlátozások
Az alábbiakban a funkció jelenlegi korlátait olvashatja:

1. Klasszikus Azure Storage-fiókok.

2. Azure-fiókok Az Azure Active Directory nélkül.

3. A több-bérlős hozzáférés nem támogatott. Egy adott Stream Analytics-feladathoz létrehozott egyszerű szolgáltatásnak ugyanabban az Azure Active Directory-bérlőben kell lennie, amelyben a feladatot létrehozták, és nem használható egy másik Azure Active Directory-bérlőben található erőforrással.

4. [A felhasználó hozrendelt identitása](../active-directory/managed-identities-azure-resources/overview.md) nem támogatott. Ez azt jelenti, hogy a felhasználó nem tudja megadni a saját egyszerű szolgáltatását, amelyet a Stream Analytics-feladat használ. Az egyszerű szolgáltatást az Azure Stream Analytics-nek kell létrehoznia.

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics kimeneteinek megismerése](./stream-analytics-define-outputs.md)
* [Az Azure Stream Analytics egyéni blobkimeneti particionálása](./stream-analytics-custom-path-patterns-blob-storage-output.md)
