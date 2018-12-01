---
title: Az Azure Key Vault használata a felügyelt alkalmazások |} A Microsoft Docs
description: Bemutatja, hogyan használja az Azure Key Vaultban titkos hozzáférési kódjainak, ha a felügyelt alkalmazások üzembe helyezése
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 07/11/2018
ms.author: tomfitz
ms.openlocfilehash: dcfbb7f3b1d110d4c1fdf22863d795c85152ec35
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52725012"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Access Key Vault titkos kulcsából, Azure által felügyelt alkalmazások telepítésekor

Továbbítása (például a jelszó) biztonságos értéket paraméterként, üzembe helyezés során szüksége, amikor az értéket lekérheti egy [Azure Key Vault](../key-vault/key-vault-whatis.md). A Key Vault hozzáférhet a felügyelt alkalmazások üzembe helyezéséhez, hozzáférést kell biztosítania a **készülék erőforrás-szolgáltató** egyszerű szolgáltatást. Ez a cikk ismerteti, hogyan konfigurálhatja a felügyelt alkalmazások használata a Key Vault.

## <a name="enable-template-deployment"></a>Sablon üzembe helyezés engedélyezése

1. A portálon válassza ki a Key Vaulthoz.

1. Válassza a **Hozzáférési szabályzatok** lehetőséget.   

   ![Válassza ki a hozzáférési szabályzatok](./media/key-vault-access/select-access-policies.png)

1. Válassza a **Kattintson a speciális hozzáférési szabályzatok megtekintéséhez** lehetőséget.

   ![Speciális hozzáférési szabályzatok megjelenítéséhez](./media/key-vault-access/advanced.png)

1. Válassza ki **engedélyezze a hozzáférést az Azure Resource Manager-sablon telepítése**. Ezt követően válassza a **Mentés** lehetőséget.

   ![Sablon üzembe helyezés engedélyezése](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Adja hozzá a szolgáltatás mint közreműködő

1. Válassza ki **hozzáférés-vezérlés (IAM)**.

   ![Válassza ki a hozzáférés-vezérlés](./media/key-vault-access/access-control.png)

1. Válassza ki **szerepkör-hozzárendelés hozzáadása**.

   ![Válassza a hozzáadása](./media/key-vault-access/add-access-control.png)

1. Válassza ki **közreműködői** a szerepkörhöz. Keresse meg **készülék erőforrás-szolgáltató** , és jelölje ki az elérhető lehetőségek közül.

   ![Keresse meg a szolgáltató](./media/key-vault-access/search-provider.png)

1. Kattintson a **Mentés** gombra.

## <a name="reference-key-vault-secret"></a>Referencia a Key Vault titkos kulcs

Adhatók át egy titkos kulcsot a Key vaultból egy sablont az felügyelt alkalmazásban, használjon egy [hivatkozott sablonnak](../azure-resource-manager/resource-group-linked-templates.md) és a társított sablon paraméterei a Key Vault hivatkoznak. Adja meg a Key Vault erőforrás-Azonosítóját és a titkos kód nevét.

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

A Key Vault elérhetők legyenek egy felügyelt alkalmazás üzembe helyezése során konfigurált.

* További információ a értéket átadja a Key vaultból egy sablon paraméterként: [használata Azure Key Vaultban történő biztonságos paraméter értéke továbbítása üzembe helyezés során](../azure-resource-manager/resource-manager-keyvault-parameter.md).
* Felügyelt alkalmazás példákért lásd [mintaprojektekkel az Azure által felügyelt alkalmazások](sample-projects.md).
* Felhasználóifelület-definíciós fájl felügyelt alkalmazáshoz való létrehozásával kapcsolatban tekintse meg a [CreateUiDefinition első lépéseit bemutató](create-uidefinition-overview.md) témakört.