---
title: Felügyelt identitástámogatás konfigurálása új Service Fabric-fürthöz
description: Így engedélyezheti a felügyelt identitások támogatását egy új Azure Service Fabric-fürtben
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 0e35d2192fdcdb294b349105f3f0158564cec86b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76930462"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster-preview"></a>Felügyelt identitástámogatás konfigurálása új Service Fabric-fürthöz (előzetes verzió)

Felügyelt [identitások használata az Azure-erőforrások a](../active-directory/managed-identities-azure-resources/overview.md) Service Fabric-alkalmazások, először engedélyezze a felügyelt *identitásjogkivonat-szolgáltatás* a fürtön. Ez a szolgáltatás felelős a Service Fabric-alkalmazások felügyelt identitások használatával történő hitelesítésért, valamint a hozzáférési jogkivonatok nevükben történő beszerzéséért. Ha a szolgáltatás engedélyezve van, láthatja, hogy a Service Fabric Explorer a **rendszer** szakaszban a bal oldali ablaktáblában, fut a neve **fabric:/System/ManagedIdentityTokenService** mellett más rendszerszolgáltatások.

> [!NOTE]
> A Service Fabric 6.5.658.9590-es vagy újabb verziója szükséges a **felügyelt identitásjogkivonat-szolgáltatás**engedélyezéséhez.  

## <a name="enable-the-managed-identity-token-service"></a>A felügyelt identitásjogkivonat-szolgáltatás engedélyezése

A felügyelt identitásjogkivonat-szolgáltatás fürtlétrehozáskori engedélyezéséhez adja hozzá a következő kódrészletet a fürt Azure Resource Manager-sablonhoz:

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

## <a name="errors"></a>Hibák

Ha a központi telepítés sikertelen ezzel az üzenettel, az azt jelenti, hogy a fürt nem a szükséges Service Fabric-verzión található (a minimálisan támogatott futásidő 6,5 CU2):


```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="related-articles"></a>Kapcsolódó cikkek

* [Felügyelt identitástámogatás áttekintése](./concepts-managed-identity.md) az Azure Service Fabricben

* [Felügyelt identitás támogatásengedélyezése meglévő Azure Service Fabric-fürtben](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>További lépések

* [Azure Service Fabric-alkalmazás üzembe helyezése rendszerhez rendelt felügyelt identitással](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Azure Service Fabric-alkalmazás üzembe helyezése felhasználó által hozzárendelt felügyelt identitással](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [A Service Fabric-alkalmazások felügyelt identitásának kihasználása a szolgáltatáskódból](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure Service Fabric-alkalmazás elérésének biztosítása más Azure-erőforrásokhoz](./how-to-grant-access-other-resources.md)