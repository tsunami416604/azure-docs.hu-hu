---
title: Azure Key Vault használata felügyelt alkalmazásokkal | Microsoft Docs
description: Bemutatja, hogyan használhatók a hozzáférési Titkok a Azure Key Vault a felügyelt alkalmazások telepítésekor
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: a87066425845a7f1043576a858a361e601ba9cc8
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003413"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Key Vault titok elérése Azure Managed Applications telepítésekor

Ha biztonságos értéket (például jelszót) kell átadnia paraméterként az üzembe helyezés során, lekérheti az értéket egy [Azure Key Vault](../key-vault/key-vault-overview.md). Ha a felügyelt alkalmazások telepítésekor szeretné elérni a Key Vault, hozzáférést kell biztosítania a **készülék erőforrás-szolgáltatói** egyszerű szolgáltatásához. A felügyelt alkalmazások szolgáltatás ezt az identitást használja a műveletek futtatásához. Ha az üzembe helyezés során sikeresen beolvas egy Key Vault értéket, az egyszerű szolgáltatásnak el kell tudnia érni a Key Vault.

Ez a cikk azt ismerteti, hogyan konfigurálható a Key Vault a felügyelt alkalmazásokkal való együttműködéshez.

## <a name="enable-template-deployment"></a>Sablon központi telepítésének engedélyezése

1. A portálon válassza ki a Key Vault.

1. Válassza a **Hozzáférési szabályzatok** lehetőséget.   

   ![Hozzáférési szabályzatok kiválasztása](./media/key-vault-access/select-access-policies.png)

1. Válassza a **Kattintson a speciális hozzáférési szabályzatok megtekintéséhez** lehetőséget.

   ![Speciális hozzáférési szabályzatok megjelenítése](./media/key-vault-access/advanced.png)

1. Jelölje be **a Azure Resource Manager hozzáférésének engedélyezése a sablonok üzembe helyezéséhez**lehetőséget. Ezt követően válassza a **Mentés** lehetőséget.

   ![Sablon központi telepítésének engedélyezése](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Szolgáltatás hozzáadása közreműködőként

1. Válassza ki **hozzáférés-vezérlés (IAM)** .

   ![Hozzáférés-vezérlés kiválasztása](./media/key-vault-access/access-control.png)

1. Válassza ki **szerepkör-hozzárendelés hozzáadása**.

   ![Hozzáadás kiválasztása](./media/key-vault-access/add-access-control.png)

1. Válassza a szerepkör **közreműködői** lehetőséget. Keresse meg a **készülék erőforrás-szolgáltatóját** , és válassza ki az elérhető lehetőségek közül.

   ![Szolgáltató keresése](./media/key-vault-access/search-provider.png)

1. Kattintson a **Mentés** gombra.

## <a name="reference-key-vault-secret"></a>Key Vault titok hivatkozása

Ha át szeretne adni egy titkos kulcsot egy Key Vault a felügyelt alkalmazásban található sablonhoz, egy [csatolt sablont](../azure-resource-manager/resource-group-linked-templates.md) kell használnia, és hivatkoznia kell a Key Vaultra a társított sablon paraméterei között. Adja meg a Key Vault erőforrás-AZONOSÍTÓját és a titok nevét.

```json
"resources": [{
  "apiVersion": "2015-01-01",
  "name": "linkedTemplate",
  "type": "Microsoft.Resources/deployments",
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"
          },
          "secretName": "<secret-name>"
        }
      },
      "adminLogin": { "value": "[parameters('adminLogin')]" },
      "sqlServerName": {"value": "[parameters('sqlServerName')]"}
    }
  }
}],
```

## <a name="next-steps"></a>További lépések

Úgy konfigurálta a Key Vault, hogy elérhető legyen egy felügyelt alkalmazás telepítése során.

* A Key Vault sablon paraméterként való átadásával kapcsolatos információkért lásd: a [Azure Key Vault használata a biztonságos paraméterek értékének](../azure-resource-manager/resource-manager-keyvault-parameter.md)átadására a telepítés során.
* A felügyelt alkalmazások példáit lásd: [Sample projects for Azure Managed Applications](sample-projects.md).
* Felhasználóifelület-definíciós fájl felügyelt alkalmazáshoz való létrehozásával kapcsolatban tekintse meg a [CreateUiDefinition első lépéseit bemutató](create-uidefinition-overview.md) témakört.