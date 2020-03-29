---
title: A Key Vault használata felügyelt alkalmazás telepítésekor
description: Bemutatja, hogyan használhatja a hozzáférési titkokat az Azure Key Vaultban a felügyelt alkalmazások telepítésekor
author: tfitzmac
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: f434ad6e19c89f248fec948c0a049fabb0f7c476
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248436"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Access Key Vault titkos elérése az Azure felügyelt alkalmazások telepítésekor

Ha a központi telepítés során paraméterként egy biztonságos értéket (például egy jelszót) kell átadnia, lekérheti az értéket egy [Azure Key Vaultból.](../../key-vault/key-vault-overview.md) A Key Vault eléréséhez felügyelt alkalmazások telepítésekor hozzáférést kell biztosítania a **készülék erőforrás-szolgáltató** egyszerű hozzáférést kell biztosítania. A Felügyelt alkalmazások szolgáltatás ezt az identitást használja a műveletek futtatásához. Ahhoz, hogy az üzembe helyezés során sikeresen lekérje az értéket egy Key Vaultból, a szolgáltatásnévnek képesnek kell lennie a Key Vault elérésére.

Ez a cikk azt ismerteti, hogyan konfigurálhatja a Key Vault ot a felügyelt alkalmazásokkal való együttműködésre.

## <a name="enable-template-deployment"></a>Sablon telepítésének engedélyezése

1. A portálon válassza ki a Key Vault.

1. Válassza a **Hozzáférési szabályzatok** lehetőséget.   

   ![Hozzáférési házirendek kiválasztása](./media/key-vault-access/select-access-policies.png)

1. Válassza a **Kattintson a speciális hozzáférési szabályzatok megtekintéséhez** lehetőséget.

   ![Speciális hozzáférési házirendek megjelenítése](./media/key-vault-access/advanced.png)

1. Válassza **a Hozzáférés engedélyezése az Azure Resource Manager hez a sablon telepítéséhez**lehetőséget. Ezután válassza a **Mentés gombot.**

   ![Sablon telepítésének engedélyezése](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Szolgáltatás hozzáadása közreműködőként

1. Válassza **a Hozzáférés-vezérlés (IAM) lehetőséget.**

   ![Hozzáférés-vezérlés kiválasztása](./media/key-vault-access/access-control.png)

1. Válassza a **Szerepkör-hozzárendelés hozzáadása** lehetőséget.

   ![Hozzáadás kiválasztása](./media/key-vault-access/add-access-control.png)

1. Válassza **a Közreműködő** lehetőséget a szerepkörhöz. Keresse meg **a készülékerőforrás-szolgáltatót,** és válassza ki a rendelkezésre álló lehetőségek közül.

   ![Szolgáltató keresése](./media/key-vault-access/search-provider.png)

1. Kattintson a **Mentés** gombra.

## <a name="reference-key-vault-secret"></a>Referencia kulcstároló titkos kulcsa

Ha egy key vaultból egy titkos kulcsot szeretne átadni egy sablonnak a felügyelt alkalmazásban, egy csatolt vagy beágyazott sablont kell [használnia,](../templates/linked-templates.md) és hivatkoznia kell a Key Vaultra a csatolt vagy beágyazott sablon paramétereiben. Adja meg a Key Vault erőforrás-azonosítóját és a titkos kulcs nevét.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where the resources will be deployed."
      }
    },
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the keyvault that contains the secret."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "The name of the secret."
      }
    },
    "vaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group that contains the keyvault."
      }
    },
    "vaultSubscription": {
      "type": "string",
      "defaultValue": "[subscription().subscriptionId]",
      "metadata": {
        "description": "The name of the subscription that contains the keyvault."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

## <a name="next-steps"></a>További lépések

Úgy állította be a Key Vaultot, hogy elérhető legyen a felügyelt alkalmazások üzembe helyezése során.

* A Key Vault ból sablonparaméterként megadott értékről az [Azure Key Vault használata a biztonságos paraméter értékének a telepítés során történő átadása című](../templates/key-vault-parameter.md)témakörben olvashat.
* Felügyelt alkalmazáspéldákért olvassa el [a Mintaprojektek az Azure által felügyelt alkalmazásokhoz.](sample-projects.md)
* Felhasználóifelület-definíciós fájl felügyelt alkalmazáshoz való létrehozásával kapcsolatban tekintse meg a [CreateUiDefinition első lépéseit bemutató](create-uidefinition-overview.md) témakört.
