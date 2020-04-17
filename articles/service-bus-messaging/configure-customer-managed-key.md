---
title: Saját kulcs konfigurálása az Azure Service Bus inaktív adatainak titkosításához
description: Ez a cikk arról nyújt tájékoztatást, hogyan konfigurálhatja a saját kulcsaz Azure Service Bus-adatok rest titkosításához.
services: service-bus-messaging
ms.service: service-bus
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: aschhab
ms.openlocfilehash: 82a5fbef8c307d60d82b147f04a2a687b8b0433e
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459066"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Ügyfél által felügyelt kulcsok konfigurálása az Azure Service Bus inaktív adatainak titkosításához az Azure Portal használatával
Az Azure Service Bus Premium az Azure Storage Service Encryption (Azure SSE) segítségével biztosítja az inaktív adatok titkosítását. A Service Bus Premium az Azure Storage-ra támaszkodik az adatok tárolásához, és alapértelmezés szerint az Azure Storage-szal tárolt összes adat microsoftáltal kezelt kulccsal van titkosítva. 

## <a name="overview"></a>Áttekintés
Az Azure Service Bus mostantól támogatja az inaktív adatok microsoftáltal kezelt vagy ügyfél által felügyelt kulcsokkal való titkosítását (Saját kulcs – BYOK). ez a funkció lehetővé teszi az Azure Service Bus inaktív titkosításához használt ügyfél által felügyelt kulcsokhoz való hozzáférés létrehozását, elforgatását, letiltását és visszavonását.

A BYOK szolgáltatás engedélyezése egyszeri beállítási folyamat a névtéren.

> [!NOTE]
> Van néhány kikötés, hogy az ügyfél által kezelt kulcs a szolgáltatási oldali titkosítást. 
>   * Ezt a funkciót az [Azure Service Bus Premium](service-bus-premium-messaging.md) tier támogatja. Nem engedélyezhető a Service Bus szabványos szintű névtereihez.
>   * A titkosítás csak új vagy üres névterekesetén engedélyezhető. Ha a névtér adatokat tartalmaz, akkor a titkosítási művelet sikertelen lesz.

Az Azure Key Vault segítségével kezelheti a kulcsokat, és naplózhatja a kulcshasználatot. Létrehozhatja saját kulcsait, és tárolhatja őket egy key vaultban, vagy használhatja az Azure Key Vault API-kat kulcsok létrehozásához. Az Azure Key Vaultról a [Mi az Azure Key Vault?](../key-vault/general/overview.md)

Ez a cikk bemutatja, hogyan konfigurálhatja a key vault ügyfél által felügyelt kulcsok az Azure Portalhasználatával. Ha meg szeretné tudni, hogyan hozhat létre egy kulcstartót az Azure Portalon, olvassa el a [rövid útmutató: Az Azure Key Vault titkos kulcsának beállítása és beolvasása az Azure Portalon.](../key-vault/secrets/quick-create-portal.md)

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok használata az Azure Service Bus használatához a key vaultkét szükséges tulajdonságkonfigurálása szükséges. Ezek a következők: **Soft Delete** és **ne ürítse**. Ezek a tulajdonságok alapértelmezés szerint engedélyezve vannak, amikor új kulcstartót hoz létre az Azure Portalon. Ha azonban engedélyeznie kell ezeket a tulajdonságokat egy meglévő key vaulton, powershellt vagy Azure CLI-t kell használnia.

## <a name="enable-customer-managed-keys"></a>Ügyfél által kezelt kulcsok engedélyezése
Az ügyfél által felügyelt kulcsok engedélyezéséhez az Azure Portalon kövesse az alábbi lépéseket:

1. Nyissa meg a Service Bus Premium névterét.
2. A Service Bus névterének **Beállítások** lapján válassza a **Titkosítás**lehetőséget.
3. Válassza ki az **ügyfél által felügyelt kulcstitkosítást az** alábbi képen látható módon.

    ![Ügyfél által kezelt kulcs engedélyezése](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Kulcstartó beállítása billentyűkkel

Miután engedélyezte az ügyfél által felügyelt kulcsokat, hozzá kell rendelnie az ügyfél által felügyelt kulcsot az Azure Service Bus-névtérhez. A Service Bus csak az Azure Key Vaultot támogatja. Ha engedélyezi a **titkosítást az ügyfél által felügyelt kulcssal** beállítás az előző szakaszban, a kulcs importálása az Azure Key Vaultba. A kulcsoknak is rendelkezniük kell a kulcshoz **beállított helyreállítható törlés** és **a Ne ürítés** lehetőségével. Ezek a beállítások a [PowerShell](../key-vault/general/soft-delete-powershell.md) vagy a [CLI](../key-vault/general/soft-delete-cli.md#enabling-purge-protection)használatával konfigurálhatók.

1. Új kulcstartó létrehozásához kövesse az Azure Key Vault [rövid útmutatóját.](../key-vault/general/overview.md) A meglévő kulcsok importálásáról a [Kulcsok, titkos kulcsok és tanúsítványok – további](../key-vault/about-keys-secrets-and-certificates.md)információ.
1. Ha be szeretné kapcsolni a helyreállítható törlést és a védelem kiürítését a tároló létrehozásakor, használja az [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) parancsot.

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Ha tisztítási védelmet szeretne hozzáadni egy meglévő tárolóhoz (amelymár engedélyezve van a helyreállítható törlésfunkcióval), használja az [az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) parancsot.

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Hozzon létre kulcsokat az alábbi lépésekkel:
    1. Új kulcs létrehozásához válassza a **Beállítások**menü **Kulcsok** **létrehozása/importálás parancsát.**
        
        ![Válassza a Létrehozás/importálás gombot](./media/configure-customer-managed-key/select-generate-import.png)

    1. Állítsa be a **Beállítások at** **Generate** and give the key a name.

        ![Kulcs létrehozása](./media/configure-customer-managed-key/create-key.png) 

    1. Most már kiválaszthatja ezt a kulcsot a Service Bus névtérhez társítva a legördülő listából történő titkosításhoz. 

        ![Kulcs kiválasztása a kulcstartóból](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > A redundancia érdekében legfeljebb 3 kulcsot adhat hozzá. Abban az esetben, ha az egyik kulcs lejárt, vagy nem érhető el, a többi kulcs titkosításra lesz használva.
        
    1. Töltse ki a kulcs részleteit, és kattintson a **Kijelölés gombra.** Ez lehetővé teszi a névtérin lévő adatok titkosítását egy ügyfél által kezelt kulccsal. 


    > [!IMPORTANT]
    > Ha az Ügyfél által kezelt kulcsot és a geokatasztrófa-helyreállítást szeretné használni, kérjük, tekintse át az alábbi 
    >
    > Az ügyfél által felügyelt kulccsal való inaktív titkosítás engedélyezéséhez a Service Bus felügyelt identitásához hozzáférési [szabályzat](../key-vault/general/secure-your-key-vault.md) van beállítva a megadott Azure KeyVault-on. Ez biztosítja az Azure KeyVault-hoz való ellenőrzött hozzáférést az Azure Service Bus névtérből.
    >
    > Ennek köszönhetően:
    > 
    >   * Ha [a földrajzi vészhelyreállítás](service-bus-geo-dr.md) már engedélyezve van a Service Bus névtérben, és engedélyezni szeretné az ügyfél által felügyelt kulcsot, akkor 
    >     * Szakítsa meg a párosítást
    >     * [Állítsa be a hozzáférési szabályzatot](../key-vault/general/managed-identity.md) a felügyelt identitás mind az elsődleges és másodlagos névterek a key vault.
    >     * Titkosítás beállítása az elsődleges névtéren.
    >     * Párosítsa újra az elsődleges és másodlagos névtereket.
    > 
    >   * Ha olyan Service Bus-névtérben szeretné engedélyezni a Geo-DR-t, ahol az ügyfél által kezelt kulcs már be van állítva, akkor -
    >     * [Állítsa be a hozzáférési szabályzatot](../key-vault/general/managed-identity.md) a felügyelt identitás a másodlagos névtér a key vault.
    >     * Párosítsa az elsődleges és másodlagos névtereket.


## <a name="rotate-your-encryption-keys"></a>A titkosítási kulcsok elforgatása

A kulcs a key vault ban az Azure Key Vaults rotációs mechanizmus használatával elforgathatja a kulcsot. További információt a [Kulcsrotáció és -naplózás beállítása című témakörben talál.](../key-vault/secrets/key-rotation-log-monitoring.md) Az aktiválási és lejárati dátumok a kulcselforgatás automatizálására is beállíthatók. A Service Bus szolgáltatás észleli az új kulcsverziókat, és automatikusan elkezdi használni őket.

## <a name="revoke-access-to-keys"></a>Kulcsokhoz való hozzáférés visszavonása

A titkosítási kulcsokhoz való hozzáférés visszavonása nem törli az adatokat a Service Busból. Az adatok azonban nem érhetők el a Service Bus névtérből. A titkosítási kulcsot hozzáférési házirenden keresztül vagy a kulcs törlésével vonhatja vissza. További információ a hozzáférési szabályzatokról és a kulcstartó biztonságos [hozzáférésből a kulcstartóhoz](../key-vault/general/secure-your-key-vault.md)való védelméről.

A titkosítási kulcs visszavonása után a service bus szolgáltatás a titkosított névtérben működésképtelenné válik. Ha a kulcshoz való hozzáférés engedélyezve van, vagy a törölt kulcs helyreáll, a Service Bus szolgáltatás kiválasztja a kulcsot, hogy hozzáférhessen a titkosított Service Bus névtérből származó adatokhoz.

## <a name="use-resource-manager-template-to-enable-encryption"></a>Titkosítás engedélyezése az Erőforrás-kezelő sablonnal
Ez a szakasz bemutatja, hogyan lehet elvégezni a következő feladatokat az **Azure Resource Manager-sablonok**használatával. 

1. **Hozzon** létre egy prémium szintű Service Bus névteret **felügyelt szolgáltatásidentitással.**
2. Hozzon létre egy **kulcstartót,** és adja meg a szolgáltatás identitásának hozzáférését a key vaulthoz. 
3. Frissítse a Service Bus névterét a key vault adataival (kulcs/érték). 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>Prémium szintű Service Bus-névtér létrehozása felügyelt szolgáltatásidentitással
Ez a szakasz bemutatja, hogyan hozhat létre egy Azure Service Bus-névteret felügyelt szolgáltatásidentitással egy Azure Resource Manager-sablon és a PowerShell használatával. 

1. Hozzon létre egy Azure Resource Manager-sablont egy Service Bus prémium szintű névtér létrehozásához felügyelt szolgáltatásidentitással. Nevezze el a fájlt: **CreateServiceBusPremiumNamespace.json**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace."
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
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
    
             }
          }
       ],
       "outputs":{
          "ServiceBusNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.ServiceBus/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Hozzon létre egy sablon paraméterfájl nevű: **CreateServiceBusPremiumNamespaceParams.json**. 

    > [!NOTE]
    > Cserélje le a következő értékeket: 
    > - `<ServiceBusNamespaceName>`- A Service Bus névterének neve
    > - `<Location>`- A Service Bus névterének helye

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    ```
3. Futtassa a következő PowerShell-parancsot a sablon központi telepítéséhez egy prémium szintű Service Bus-névtér létrehozásához. Ezután olvassa be a Service Bus névtér azonosítóját, hogy később használhassa. A `{MyRG}` parancs futtatása előtt cserélje le az erőforráscsoport nevére.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>A Service Bus-névtér identitásának hozzáférése a kulcstartóhoz

1. Futtassa a következő parancsot, hogy hozzon létre egy **kulcstartót a kiürítési védelem** és a **helyreállítható törlés** engedélyezve. 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    (1)
    
    Meglévő **kulcstartó**frissítéséhez futtassa a következő parancsot. Adja meg az erőforráscsoport és a kulcstartó nevének értékeit a parancs futtatása előtt. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Állítsa be a key vault hozzáférési szabályzatot úgy, hogy a Service Bus névtér felügyelt identitása hozzáférhessen a kulcstároló kulcsértékéhez. Használja a Service Bus névtér azonosítóját az előző szakaszból. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Adatok titkosítása a Service Bus névterén a kulcstartóból származó ügyfél által felügyelt kulccsal
Eddig a következő lépéseket tette: 

1. Felügyelt identitással létrehozott egy prémium szintű névteret.
2. Hozzon létre egy kulcstartót, és adja meg a felügyelt identitás hozzáférést a key vault. 

Ebben a lépésben frissíti a Service Bus névteret a key vault adataival. 

1. Hozzon létre egy **UpdateServiceBusNamespaceWithEncryption.json** nevű JSON-fájlt a következő tartalommal: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
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
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
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

2. Sablonparaméter-fájl létrehozása: **UpdateServiceBusNamespaceWithEncryptionParams.json**.

    > [!NOTE]
    > Cserélje le a következő értékeket: 
    > - `<ServiceBusNamespaceName>`- A Service Bus névterének neve
    > - `<Location>`- A Service Bus névterének helye
    > - `<KeyVaultName>`- A kulcstartó neve
    > - `<KeyName>`- A kulcs neve a kulcstartóban  

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
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
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:
- [Service Bus – áttekintés](service-bus-messaging-overview.md)
- [Key Vault – áttekintés – áttekintés](../key-vault/general/overview.md)


