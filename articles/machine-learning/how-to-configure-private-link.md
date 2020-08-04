---
title: Privát végpont konfigurálása (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Az Azure Private link használatával biztonságosan férhet hozzá a Azure Machine Learning-munkaterülethez egy virtuális hálózatról.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/28/2020
ms.openlocfilehash: dcf99b2a2a762114db45a9371a489b952b57d430
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542178"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Azure Private-hivatkozás konfigurálása Azure Machine Learning munkaterülethez (előzetes verzió)

Ebből a dokumentumból megtudhatja, hogyan használhatja az Azure Private-hivatkozást a Azure Machine Learning munkaterülettel. 

> [!IMPORTANT]
> Az Azure Private-hivatkozás használata Azure Machine Learning munkaterülettel jelenleg nyilvános előzetes verzióban érhető el. Ez a funkció csak az **USA keleti** és az **USA 2. nyugati** régiójában érhető el. Ez az előzetes verzió szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure Private link segítségével privát végponton keresztül csatlakozhat a munkaterülethez. A magánhálózati végpont a virtuális hálózaton belüli magánhálózati IP-címek készlete. Ezután korlátozhatja a hozzáférést a munkaterülethez, hogy csak a magánhálózati IP-címeken keresztül történjen. A privát hivatkozás segít csökkenteni az adatkiszűrése kockázatát. Ha többet szeretne megtudni a privát végpontokról, tekintse meg az [Azure privát hivatkozását](/azure/private-link/private-link-overview) ismertető cikket.

> [!IMPORTANT]
> Az Azure Private-hivatkozás nem befolyásolja az Azure Control Plant (felügyeleti műveletek), például a munkaterület törlését vagy a számítási erőforrások kezelését. Például létrehozhatja, frissítheti vagy törölheti a számítási célt. Ezeket a műveleteket a rendszer a szokásos módon, a nyilvános interneten hajtja végre.
>
> Azure Machine Learning számítási példányok előzetes verziója nem támogatott olyan munkaterületen, amelyben engedélyezve van a magánhálózati hivatkozás.
>
> A Mozilla Firefox használata esetén problémák merülhetnek fel a munkaterület privát végpontjának elérésére tett kísérlet során. Ez a probléma a HTTPS-en keresztül a Mozilla-on keresztül köthető a DNS-hez. Javasoljuk, hogy megkerülő megoldásként használja a Google Chrome Microsoft Edge használatát.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Privát végpontot használó munkaterület létrehozása

> [!IMPORTANT]
> Jelenleg csak az új Azure Machine Learning munkaterület létrehozásakor támogatjuk a privát végpontok engedélyezését.

A használható olyan [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) munkaterület létrehozásához, amely privát végponttal rendelkezik.

További információ a sablon használatáról, beleértve a privát végpontokat is: [Azure Resource Manager sablon használata munkaterületek létrehozásához Azure Machine Learninghoz](how-to-create-workspace-template.md).

## <a name="using-a-workspace-over-a-private-endpoint"></a>Munkaterület használata privát végponton keresztül

Mivel a munkaterület felé irányuló kommunikáció csak a virtuális hálózatról engedélyezett, a munkaterületet használó fejlesztési környezeteknek a virtuális hálózat tagjának kell lenniük. Például egy virtuális gép a virtuális hálózaton.

> [!IMPORTANT]
> A kapcsolat ideiglenes megszakadásának elkerülése érdekében a Microsoft javasolja a DNS-gyorsítótár kiürítését a munkaterülethez csatlakozó számítógépeken a privát hivatkozás engedélyezése után. 

Az Azure Virtual Machinesról a [Virtual Machines dokumentációjában](/azure/virtual-machines/)olvashat bővebben.


## <a name="using-azure-storage"></a>Az Azure Storage használata

A munkaterület által használt Azure Storage-fiók védelméhez helyezze azt a virtuális hálózaton belül.

A Storage-fiók virtuális hálózaton való elhelyezésével kapcsolatos információkért lásd: [Storage-fiók használata a munkaterülethez](how-to-enable-virtual-network.md#use-a-storage-account-for-your-workspace).

> [!WARNING]
> A Azure Machine Learning nem támogatja olyan Azure Storage-fiók használatát, amelynek engedélyezve van a magánhálózati kapcsolata.

## <a name="using-azure-key-vault"></a>Azure Key Vault használata

A munkaterület által használt Azure Key Vault biztonságossá tételéhez elhelyezheti a virtuális hálózaton belül, vagy engedélyezheti a saját hivatkozását.

A Key Vault virtuális hálózatban való elhelyezésével kapcsolatos információkért lásd: [Key Vault-példány használata a munkaterülettel](how-to-enable-virtual-network.md#key-vault-instance).

További információ a Key Vault privát hivatkozásának engedélyezéséről: [Key Vault integrálása az Azure Private linktel](/azure/key-vault/private-link-service).

## <a name="using-azure-kubernetes-services"></a>Az Azure Kubernetes Services használata

A munkaterület által használt Azure Kubernetes-szolgáltatások biztonságossá tételéhez helyezze azt egy virtuális hálózaton belül. További információ: az [Azure Kubernetes Services használata a munkaterülettel](how-to-enable-virtual-network.md#aksvnet).

A Azure Machine Learning mostantól támogatja az Azure Kubernetes szolgáltatás használatát, amelyhez engedélyezve van a privát kapcsolat.
Privát AK-fürt létrehozásához kövesse az [itt](https://docs.microsoft.com/azure/aks/private-clusters) található dokumentumokat

## <a name="azure-container-registry"></a>Azure Container Registry

A virtuális hálózaton belüli Azure Container Registry biztonságossá tételével kapcsolatos további információkért lásd: [Azure Container Registry használata](how-to-enable-virtual-network.md#azure-container-registry).

> [!IMPORTANT]
> Ha a Azure Machine Learning munkaterülethez privát hivatkozást használ, és a munkaterülethez tartozó Azure Container Registry a virtuális hálózaton helyezi el, akkor a következő Azure Resource Manager sablont is alkalmaznia kell. Ez a sablon lehetővé teszi, hogy a munkaterület kommunikáljon az ACR-szel a privát kapcsolaton keresztül.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "keyVaultArmId": {
      "type": "string"
      },
      "workspaceName": {
      "type": "string"
      },
      "containerRegistryArmId": {
      "type": "string"
      },
      "applicationInsightsArmId": {
      "type": "string"
      },
      "storageAccountArmId": {
      "type": "string"
      },
      "location": {
      "type": "string"
      }
  },
  "resources": [
      {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2019-11-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "identity": {
          "type": "SystemAssigned"
      },
      "sku": {
          "tier": "enterprise",
          "name": "enterprise"
      },
      "properties": {
          "sharedPrivateLinkResources":
  [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
          "keyVault": "[parameters('keyVaultArmId')]",
          "containerRegistry": "[parameters('containerRegistryArmId')]",
          "applicationInsights": "[parameters('applicationInsightsArmId')]",
          "storageAccount": "[parameters('storageAccountArmId')]"
      }
      }
  ]
}
```

## <a name="next-steps"></a>További lépések

A Azure Machine Learning munkaterület biztonságossá tételével kapcsolatos további információkért tekintse meg a [nagyvállalati biztonsági](concept-enterprise-security.md) cikket.
