---
title: Saját kulcs konfigurálása az Azure Event Hubs inaktív adatainak titkosításához
description: Ez a cikk arról nyújt tájékoztatást, hogyan konfigurálhatja a saját kulcsaz Azure Event Hubs-adatok rest titkosításához.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: spelluru
ms.openlocfilehash: 43e626355feaf1e51fc840f82506c559a1859b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77621988"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Ügyfél által felügyelt kulcsok konfigurálása az Azure Event Hubs inaktív adatainak titkosításához az Azure Portal használatával
Az Azure Event Hubs az Azure Storage Service Encryption (Azure SSE) segítségével biztosítja az inaktív adatok titkosítását. Az Event Hubs az Azure Storage-ra támaszkodik az adatok tárolásához, és alapértelmezés szerint az Azure Storage-szal tárolt összes adat microsoftáltal kezelt kulccsal van titkosítva. 

## <a name="overview"></a>Áttekintés
Az Azure Event Hubs mostantól támogatja az inaktív adatok microsoftáltal felügyelt vagy ügyfél által felügyelt kulcsokkal való titkosítását (Saját kulcs hozása – BYOK). Ez a funkció lehetővé teszi az Azure Event Hubs-adatok titkosításához használt ügyfél által felügyelt kulcsokhoz való hozzáférés létrehozását, elforgatását, letiltását és visszavonását.

A BYOK szolgáltatás engedélyezése egyszeri beállítási folyamat a névtéren.

> [!NOTE]
> A BYOK-képességet az [Event Hubs dedikált egybérlős](event-hubs-dedicated-overview.md) fürtjei támogatják. Nem engedélyezhető a szabványos Event Hubs névterek.

Az Azure Key Vault segítségével kezelheti a kulcsokat, és naplózhatja a kulcshasználatot. Létrehozhatja saját kulcsait, és tárolhatja őket egy key vaultban, vagy használhatja az Azure Key Vault API-kat kulcsok létrehozásához. Az Azure Key Vaultról a [Mi az Azure Key Vault?](../key-vault/key-vault-overview.md)

Ez a cikk bemutatja, hogyan konfigurálhatja a key vault ügyfél által felügyelt kulcsok az Azure Portalhasználatával. Ha meg szeretné tudni, hogyan hozhat létre egy kulcstartót az Azure Portalon, olvassa el a [rövid útmutató: Az Azure Key Vault titkos kulcsának beállítása és beolvasása az Azure Portalon.](../key-vault/quick-create-portal.md)

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok azure-event hubs használatával a key vault két szükséges tulajdonságok konfigurálása szükséges. Ezek a következők: **Soft Delete** és **ne ürítse**. Ezek a tulajdonságok alapértelmezés szerint engedélyezve vannak, amikor új kulcstartót hoz létre az Azure Portalon. Ha azonban engedélyeznie kell ezeket a tulajdonságokat egy meglévő key vaulton, powershellt vagy Azure CLI-t kell használnia.

## <a name="enable-customer-managed-keys"></a>Ügyfél által kezelt kulcsok engedélyezése
Az ügyfél által felügyelt kulcsok engedélyezéséhez az Azure Portalon kövesse az alábbi lépéseket:

1. Keresse meg az Eseményközpontok dedikált fürtjeit.
1. Jelölje ki azt a névteret, amelyen engedélyezni szeretné a BYOK-ot.
1. Az Eseményközpontok névterének **Beállítások** lapján válassza a **Titkosítás**lehetőséget. 
1. Válassza ki az **ügyfél által felügyelt kulcstitkosítást az** alábbi képen látható módon. 

    ![Ügyfél által kezelt kulcs engedélyezése](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Kulcstartó beállítása billentyűkkel
Miután engedélyezte az ügyfél által kezelt kulcsokat, hozzá kell rendelnie az ügyfél által kezelt kulcsot az Azure Event Hubs névteréhez. Az Event Hubs csak az Azure Key Vaultot támogatja. Ha engedélyezi a **titkosítást az ügyfél által felügyelt kulcssal** beállítás az előző szakaszban, a kulcs importálása az Azure Key Vaultba. A kulcsoknak is rendelkezniük kell a kulcshoz **beállított helyreállítható törlés** és **a Ne ürítés** lehetőségével. Ezek a beállítások a [PowerShell](../key-vault/key-vault-soft-delete-powershell.md) vagy a [CLI](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection)használatával konfigurálhatók.

1. Új kulcstartó létrehozásához kövesse az Azure Key Vault [rövid útmutatóját.](../key-vault/key-vault-overview.md) A meglévő kulcsok importálásáról a [Kulcsok, titkos kulcsok és tanúsítványok – további](../key-vault/about-keys-secrets-and-certificates.md)információ.
1. Ha be szeretné kapcsolni a helyreállítható törlést és a védelem kiürítését a tároló létrehozásakor, használja az [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) parancsot.

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Ha tisztítási védelmet szeretne hozzáadni egy meglévő tárolóhoz (amelymár engedélyezve van a helyreállítható törlésfunkcióval), használja az [az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) parancsot.

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Hozzon létre kulcsokat az alábbi lépésekkel:
    1. Új kulcs létrehozásához válassza a **Beállítások**menü **Kulcsok** **létrehozása/importálás parancsát.**
        
        ![Válassza a Létrehozás/importálás gombot](./media/configure-customer-managed-key/select-generate-import.png)
    1. Állítsa be a **Beállítások at** **Generate** and give the key a name.

        ![Kulcs létrehozása](./media/configure-customer-managed-key/create-key.png) 
    1. Most már kiválaszthatja ezt a kulcsot az Event Hubs névtérhez társítva a legördülő listából történő titkosításhoz. 

        ![Kulcs kiválasztása a kulcstartóból](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Töltse ki a kulcs részleteit, és kattintson a **Kijelölés gombra.** Ez lehetővé teszi a névtérin lévő adatok titkosítását egy ügyfél által kezelt kulccsal. 


## <a name="rotate-your-encryption-keys"></a>A titkosítási kulcsok elforgatása
A kulcs a key vault ban az Azure Key Vaults rotációs mechanizmus használatával elforgathatja a kulcsot. További információt a [Kulcsrotáció és -naplózás beállítása című témakörben talál.](../key-vault/key-vault-key-rotation-log-monitoring.md) Az aktiválási és lejárati dátumok a kulcselforgatás automatizálására is beállíthatók. Az Event Hubs szolgáltatás észleli az új kulcsverziókat, és automatikusan elkezdi használni őket.

## <a name="revoke-access-to-keys"></a>Kulcsokhoz való hozzáférés visszavonása
A titkosítási kulcsokhoz való hozzáférés visszavonása nem törli az adatokat az Event Hubs-ból. Az adatok azonban nem érhetők el az Event Hubs névtérből. A titkosítási kulcsot hozzáférési házirenden keresztül vagy a kulcs törlésével vonhatja vissza. További információ a hozzáférési szabályzatokról és a kulcstartó biztonságos [hozzáférésből a kulcstartóhoz](../key-vault/key-vault-secure-your-key-vault.md)való védelméről.

A titkosítási kulcs visszavonása után a titkosított névtérben lévő Event Hubs szolgáltatás működésképtelenné válik. Ha a kulcshoz való hozzáférés engedélyezve van, vagy a törlési kulcs helyreáll, az Event Hubs szolgáltatás kiválasztja a kulcsot, hogy hozzáférhessen az adatokhoz a titkosított Event Hubs névtérből.

## <a name="set-up-diagnostic-logs"></a>Diagnosztikai naplók beállítása 
A BYOK-kompatibilis névterek diagnosztikai naplóinak beállítása megadja a szükséges információkat a műveletekről, ha egy névtér ügyfél által felügyelt kulccsal van titkosítva. Ezek a naplók engedélyezhetők, majd később streamelhetők egy eseményközpontba, vagy elemezhetők a naplóelemzésen keresztül, vagy adatfolyamként továbbíthatók a tárolóba, hogy személyre szabott elemzéseket hajtsanak végre. A diagnosztikai naplókról az [Azure diagnosztikai naplók áttekintése című témakörben olvashat bővebben.](../azure-monitor/platform/platform-logs-overview.md)

## <a name="enable-user-logs"></a>Felhasználói naplók engedélyezése
Az alábbi lépésekkel engedélyezheti az ügyfél által felügyelt kulcsok naplóit.

1. Az Azure Portalon keresse meg azt a névteret, amelyen a BYOK engedélyezve van.
1. Válassza **a Diagnosztikai beállítások lehetőséget** a **Figyelés**csoportban.

    ![Diagnosztikai beállítások kiválasztása](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Válassza **a +Diagnosztikai beállítás hozzáadása lehetőséget.** 

    ![Diagnosztikai beállítás hozzáadása kiválasztása](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Adjon meg egy **nevet,** és válassza ki, hogy hová szeretné streamelni a naplókat.
1. Válassza a **CustomerManagedKeyUserLogs** és **a Save lehetőséget.** Ez a művelet engedélyezi a BYOK naplóit a névtéren.

    ![Ügyfél által felügyelt kulcsfelhasználói naplók kiválasztása](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Naplóséma 
Minden napló JavaScript Object Notation (JSON) formátumban van tárolva. Minden bejegyzéshez olyan karakterláncmezők vannak, amelyek az alábbi táblázatban leírt formátumot használják. 

| Név | Leírás |
| ---- | ----------- | 
| Feladatneve | A sikertelen feladat leírása. |
| ActivityId azonosító | A nyomon követéshez használt belső azonosító. |
| category | A feladat besorolását határozza meg. Ha például a kulcs a key vault le van tiltva, akkor lenne egy információs kategória, vagy ha egy kulcs nem lehet kicsomagolni, akkor hiba alá kerülhet. |
| resourceId | Az Azure Resource Manager erőforrásazonosítója |
| keyVault | A kulcstartó teljes neve. |
| kulcs | Az Event Hubs névtér titkosításához használt kulcsnév. |
| version | A használt kulcs verziója. |
| Művelet | A kulcson a kulcson végrehajtott művelet a kulcstartóban. Például tiltsa le/engedélyezze a kulcsot, a tördelést vagy a kicsomagolást |
| code | A művelethez társított kód. Példa: Hibakód, a 404 azt jelenti, hogy a kulcs nem található. |
| message | A művelettel kapcsolatos hibaüzenetek |

Íme egy példa egy ügyfél által kezelt kulcs naplójára:

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="use-resource-manager-template-to-enable-encryption"></a>Titkosítás engedélyezése az Erőforrás-kezelő sablonnal
Ez a szakasz bemutatja, hogyan lehet elvégezni a következő feladatokat az **Azure Resource Manager-sablonok**használatával. 

1. Hozzon létre egy **Event Hubs névteret** felügyelt szolgáltatásidentitással.
2. Hozzon létre egy **kulcstartót,** és adja meg a szolgáltatás identitásának hozzáférését a key vaulthoz. 
3. Frissítse az Event Hubs névteret a key vault adataival (kulcs/érték). 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Event Hubs-fürt és névtér létrehozása felügyelt szolgáltatásidentitással
Ez a szakasz bemutatja, hogyan hozhat létre egy Azure Event Hubs névteret felügyelt szolgáltatásidentitással egy Azure Resource Manager-sablon és a PowerShell használatával. 

1. Hozzon létre egy Azure Resource Manager-sablont egy felügyelt szolgáltatásidentitással rendelkező Event Hubs névtér létrehozásához. Nevezze el a fájlt: **CreateEventHubClusterAndNamespace.json**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Hozzon létre egy sablon paraméterfájl nevű: **CreateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Cserélje le a következő értékeket: 
    > - `<EventHubsClusterName>`- Az Event Hubs-fürt neve    
    > - `<EventHubsNamespaceName>`- Az Event Hubs névterének neve
    > - `<Location>`- Az Event Hubs névterének helye

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. Futtassa a következő PowerShell-parancsot a sablon üzembe helyezéséhez egy Eseményközpont-névtér létrehozásához. Ezután olvassa be az Event Hubs névtér azonosítóját, hogy később használhassa. A `{MyRG}` parancs futtatása előtt cserélje le az erőforráscsoport nevére.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Az Event Hubs névtéridentitás-hozzáférés megadása a key vaulthoz

1. Futtassa a következő parancsot, hogy hozzon létre egy **kulcstartót a kiürítési védelem** és a **helyreállítható törlés** engedélyezve. 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    (1)    
    
    Meglévő **kulcstartó**frissítéséhez futtassa a következő parancsot. Adja meg az erőforráscsoport és a kulcstartó nevének értékeit a parancs futtatása előtt. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Állítsa be a key vault hozzáférési szabályzatot úgy, hogy az Event Hubs névtér felügyelt identitása hozzáférhessen a kulcstároló kulcsértékéhez. Használja az Előző szakasz Eseményközpontok névterének azonosítóját. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Adatok titkosítása az Event Hubs névterében a kulcstartóból származó ügyfél által felügyelt kulccsal
Eddig a következő lépéseket tette: 

1. Felügyelt identitással létrehozott egy prémium szintű névteret.
2. Hozzon létre egy kulcstartót, és adja meg a felügyelt identitás hozzáférést a key vault. 

Ebben a lépésben frissíteni fogja az Event Hubs névtér a key vault adatait. 

1. Hozzon létre egy **CreateEventHubClusterAndNamespace.json** nevű JSON-fájlt a következő tartalommal: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. Sablon paraméterfájl létrehozása: **UpdateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Cserélje le a következő értékeket: 
    > - `<EventHubsClusterName>`- Az Event Hubs-fürt neve.        
    > - `<EventHubsNamespaceName>`- Az Event Hubs névterének neve
    > - `<Location>`- Az Event Hubs névterének helye
    > - `<KeyVaultName>`- A kulcstartó neve
    > - `<KeyName>`- A kulcs neve a kulcstartóban

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. Futtassa a következő PowerShell-parancsot az Erőforrás-kezelő sablon telepítéséhez. A `{MyRG}` parancs futtatása előtt cserélje le az erőforráscsoport nevére. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>Hibaelhárítás
Ajánlott eljárásként mindig engedélyezze a naplókat, ahogy az az előző szakaszban látható. Segít nyomon követni a tevékenységeket, ha a BYOK titkosítás engedélyezve van. Ez is segít a hatóköre le a problémákat.

Az alábbiakban a közös hibakódokat kell keresni, ha byok titkosítás engedélyezve van.

| Műveletek | Hibakód | Az adatok eredményül kapott állapota |
| ------ | ---------- | ----------------------- | 
| Körbefolyatási/kicsomagolási engedély eltávolítása a key vaultból | 403 |    Hozzáférhetetlen |
| AAD szerepkör-tagság eltávolítása a wrap/unwrap engedélyt megadó AAD-tagból | 403 |  Hozzáférhetetlen |
| Titkosítási kulcs törlése a kulcstartóból | 404 | Hozzáférhetetlen |
| A kulcstartó törlése | 404 | Nem érhető el (feltételezi, hogy a helyreállítható törlés engedélyezve van, ami kötelező beállítás.) |
| A lejárati idő módosítása a titkosítási kulcson úgy, hogy az már lejárt | 403 |   Hozzáférhetetlen  |
| Az NBF módosítása (nem korábban), hogy a kulcstitkosítási kulcs ne aktív | 403 | Hozzáférhetetlen  |
| Az **MSFT-szolgáltatások engedélyezése** beállítás kiválasztása a kulcstartó tűzfalához, vagy más módon a titkosítási kulccsal rendelkező kulcstartó hálózati hozzáférésének letiltása | 403 | Hozzáférhetetlen |
| A kulcstartó áthelyezése egy másik bérlőre | 404 | Hozzáférhetetlen |  
| Időszakos hálózati probléma vagy DNS/AAD/MSI kimaradás |  | Elérhető a gyorsítótárazott adattitkosítási kulccsal |

> [!IMPORTANT]
> A Geo-DR engedélyezéséhez a BYOK titkosítást használó névtérben a másodlagos párosítási névtérnek egy dedikált fürtben kell lennie, és a rendszeren felügyelt identitást kell használnia. További információ: [Managed Identities for Azure Resources](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:
- [Eseményközpontok – áttekintés](event-hubs-about.md)
- [Key Vault – áttekintés – áttekintés](../key-vault/key-vault-overview.md)




