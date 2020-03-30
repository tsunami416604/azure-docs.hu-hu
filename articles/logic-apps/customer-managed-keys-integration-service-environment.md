---
title: Ügyfél által kezelt kulcsok beállítása az ise-kben lévő adatok titkosításához
description: Saját titkosítási kulcsok létrehozása és kezelése az inaktív adatok védelméhez az Azure Logic Apps integrációs szolgáltatási környezetekben (ISEs)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: fa39c8f65b00283044ef31dc7577a4668b3e634b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127647"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>Ügyfél által felügyelt kulcsok beállítása az inaktív adatok titkosításához az Integrációs szolgáltatás környezeteihez (ISEs) az Azure Logic Apps-ben

Az Azure Logic Apps az Azure Storage-ra támaszkodik [az inaktív adatok tárolásához](../storage/common/storage-service-encryption.md)és automatikus titkosításához. Ez a titkosítás védi az adatokat, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében. Alapértelmezés szerint az Azure Storage microsoft által felügyelt kulcsokat használ az adatok titkosításához. Az Azure Storage titkosításának működéséről az [Azure Storage titkosítása az inaktív adatokés az](../storage/common/storage-service-encryption.md) Azure Data [Encryption-at-In](../security/fundamentals/encryption-atrest.md)című témakörben talál további információt.

Amikor létrehoz egy [integrációs szolgáltatási környezetet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) a logikai alkalmazások üzemeltetéséhez, és szeretné, hogy jobban szabályozhassa az Azure Storage által használt titkosítási kulcsokat, beállíthatja, használhatja és kezelheti saját kulcsát az [Azure Key Vault](../key-vault/key-vault-overview.md)használatával. Ezt a lehetőséget "Hozd saját kulcsodhoz" (BYOK) is ismerünk, és a kulcsot "ügyfél által kezelt kulcsnak" nevezzük.

Ez a témakör bemutatja, hogyan állíthatja be és adhatja meg a saját titkosítási kulcsot, amelyet az ISE létrehozásakor a Logic Apps REST API használatával használhat. Az ISE Logic Apps REST API-n keresztüli létrehozásának általános lépéseit az [Integrációs szolgáltatás környezetének (ISE) létrehozása a Logic Apps REST API használatával című témakörben](../logic-apps/create-integration-service-environment-rest-api.md)tartalmazza.

## <a name="considerations"></a>Megfontolandó szempontok

* Az ISE ügyfél által felügyelt kulcstámogatása jelenleg csak ezekben az Azure-régiókban érhető el: USA nyugati régiója 2, USA keleti régiója és USA déli középső régiója.

* Az ügyfél által felügyelt kulcsot csak az *ISE létrehozásakor*adhatja meg, ezt követően nem. Az ISE létrehozása után ez a kulcs nem tiltható le. Jelenleg nem áll rendelkezésre támogatás az ISE ügyfél által felügyelt kulcsának elforgatásához.

* Az ügyfél által felügyelt kulcsok támogatásához az ISE megköveteli, hogy a [rendszer által hozzárendelt felügyelt identitás](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) engedélyezve legyen. Ez az identitás lehetővé teszi, hogy az ISE hitelesítse az erőforrásokhoz való hozzáférést más Azure Active Directory (Azure AD) bérlők, így nem kell bejelentkezni a hitelesítő adatokkal.

* Jelenleg egy OLYAN ISE létrehozásához, amely támogatja az ügyfél által felügyelt kulcsokat, és a rendszer által hozzárendelt identitás engedélyezve van, meg kell hívnia a Logic Apps REST API-t https put kérelem használatával.

* Az ISE-t létrehozó HTTPS PUT-kérelem elküldése után *30 percen* belül hozzáférést kell [biztosítania a kulcstartószámára az ISE rendszerhez rendelt identitásához.](#identity-access-to-key-vault) Ellenkező esetben az ISE létrehozása sikertelen, és engedélyhibát okoz.

## <a name="prerequisites"></a>Előfeltételek

* Ugyanazok [az előfeltételek](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) és [követelmények az ISE-hez való hozzáférés engedélyezéséhez,](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) mint amikor ise-t hoz létre az Azure Portalon

* Olyan Azure-kulcstartó, amelyen engedélyezve van a **helyreállítható törlés** és a **Ne ürítés** tulajdonságok

  A tulajdonságok engedélyezéséről az [Azure Key Vault helyreállító törlési áttekintése](../key-vault/key-vault-ovw-soft-delete.md) és Az ügyfelek által felügyelt kulcsok [konfigurálása az Azure Key Vault szolgáltatással című](../storage/common/storage-encryption-keys-portal.md)témakörben olvashat bővebben. Ha most ismerkedik az Azure Key Vault szolgáltatással, ismerje meg, [hogyan hozhat létre kulcstrezort](../key-vault/quick-create-portal.md#create-a-vault) az Azure Portalon vagy az Azure PowerShell [new-azkeyvault-i](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault)parancshasználatával.

* A kulcstartóban az alábbi tulajdonságértékekkel létrehozott kulcs:

  | Tulajdonság | Érték |
  |----------|-------|
  | **Kulcs típusa** | RSA |
  | **RSA-kulcs mérete** | 2048 |
  | **Engedélyezve** | Igen |
  |||

  ![Az ügyfél által felügyelt titkosítási kulcs létrehozása](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  További információ: [Ügyfél által felügyelt kulcsok konfigurálása](../storage/common/storage-encryption-keys-portal.md) az Azure Key Vault szolgáltatással vagy az Azure PowerShell [add-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/Add-AzKeyVaultKey)paranccsal.

* Az ise létrehozásához használható eszköz a Logic Apps REST API https PUT-kérelemmel történő hívásával. Használhatja például [a Postman](https://www.getpostman.com/downloads/)alkalmazást, vagy létrehozhat egy logikai alkalmazást, amely végrehajtja ezt a feladatot.

<a name="enable-support-key-system-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>Ise létrehozása kulcstartóval és felügyelt identitástámogatással

Az ISE létrehozásához hívja meg a Logic Apps REST API-t, tegye meg ezt a HTTPS PUT-kérelmet:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> A Logic Apps REST API 2019-05-01 verzió megköveteli, hogy saját HTTP PUT-kérelmet ISE-összekötők.

A telepítés általában két órán belül befejeződik. Esetenként a telepítés akár négy órát is igénybe vehet. A központi telepítés állapotának ellenőrzéséhez az [Azure Portalon](https://portal.azure.com)az Azure eszköztáron válassza ki az értesítések ikonját, amely megnyitja az értesítések ablaktábláját.

> [!NOTE]
> Ha az üzembe helyezés sikertelen, vagy törli az ISE-t, az Azure-nak akár egy órába is telhet, mielőtt felszabadítja az alhálózatokat. Ez a késleltetés azt jelenti, hogy előfordulhat, hogy várnia kell, mielőtt újra felhasználná ezeket az alhálózatokat egy másik ISE-ben.
>
> Ha törli a virtuális hálózatot, az Azure általában akár két órát is igénybe vehet az alhálózatok felszabadítása előtt, de ez a művelet hosszabb időt vehet igénybe. 
> Virtuális hálózatok törlésekor győződjön meg arról, hogy nincs enek erőforrások csatlakoztatva. 
> Lásd: [Virtuális hálózat törlése](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

### <a name="request-header"></a>Kérelem fejléce

A kérelem fejlécében adja meg a következő tulajdonságokat:

* `Content-type`: Állítsa ezt `application/json`a tulajdonságot a értékre.

* `Authorization`: Állítsa be ezt a tulajdonságértékét a tulajdonosi jogkivonatra a használni kívánt Azure-előfizetéshez vagy erőforráscsoporthoz hozzáféréssel rendelkező ügyfél számára.

### <a name="request-body"></a>A kérés törzse

A kérelem törzsében engedélyezze a további elemek támogatását az ISE-definícióban megadott adatok megadásával:

* A rendszer által hozzárendelt felügyelt identitás, amelyet az ISE a kulcstartó eléréséhez használ
* A kulcstartó és a használni kívánt ügyfél által felügyelt kulcs

#### <a name="request-body-syntax"></a>Kérelem törzsének szintaxisa

Az ISE létrehozásakor használandó tulajdonságokat az alábbi hivatkozási alapként ismerteti:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.KeyVault/vaults/{key-vault-name}",
            },
            "keyName": "{customer-managed-key-name}",
            "keyVersion": "{key-version-number}"
         }
      }
   }
}
```

#### <a name="request-body-example"></a>Törzs igénylése példa

Ez a példa kérelem törzse a mintaértékeket mutatja:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "SystemAssigned"
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.KeyVault/vaults/FabrikamKeyVault",
            },
            "keyName": "Fabrikam-Encryption-Key",
            "keyVersion": "********************"
         }
      }
   }
}
```

<a name="identity-access-to-key-vault"></a>

## <a name="grant-access-to-your-key-vault"></a>Hozzáférés a kulcstartóhoz

*30 percen* belül, miután elküldte a HTTP PUT-kérelmet az ISE létrehozásához, hozzá kell adnia egy hozzáférési szabályzatot a key vaulthoz az ISE rendszerhez rendelt identitásához. Ellenkező esetben az ISE létrehozása sikertelen, és engedélyhiba jelenik meg. 

Ehhez a feladathoz használhatja az Azure PowerShell [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) parancsot, vagy az alábbi lépéseket az Azure Portalon:

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg az Azure-kulcstartót.

1. A key vault menüjében válassza az **Access-házirendek** > **hozzáférési házirend hozzáadása lehetőséget,** például:

   ![Hozzáférési házirend hozzáadása a rendszer által hozzárendelt felügyelt identitáshoz](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. A **Hozzáférési házirend hozzáadása** ablaktábla megnyitása után hajtsa végre az alábbi lépéseket:

   1. Adja meg a következő beállításokat:

      | Beállítás | Értékek |
      |---------|--------|
      | **Konfigurálás sablonból (nem kötelező)** | Kulcskezelés |
      | **Kulcsengedélyek** | - **Kulcskezelési műveletek:** Get, List <p><p>- **Kriptográfiai műveletek:** Kulcs kicsomagolása, wrap kulcs |
      |||

      ![Válassza ki a "Kulcskezelés" > a "Kulcsengedélyek" lehetőséget.](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. A **Tag kijelölése**csoportban válassza **a Nincs lehetőséget.** A **Fő** ablaktábla megnyitása után a keresőmezőben keresse meg és jelölje ki az ISE-t. Ha elkészült, válassza **a Hozzáadás kiválasztása lehetőséget.** > **Add**

      ![Válassza ki az ISE-t, amelyet főfelhasználóként szeretne használni](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. Ha végzett az **Access-házirendek** ablaktáblával, válassza a **Mentés lehetőséget.**

További információ: [Key Vault-hitelesítés biztosítása felügyelt identitással.](../key-vault/managed-identity.md#grant-your-app-access-to-key-vault)

## <a name="next-steps"></a>További lépések

* További információ az [Azure Key Vaultról](../key-vault/key-vault-overview.md)