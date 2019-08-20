---
title: Azure Service Fabric – új Azure Service Fabric-fürt üzembe helyezése felügyelt identitás támogatásával | Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre olyan új Service Fabric-fürtöt, amelyen engedélyezve van a felügyelt identitás
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: 80fb06c96f727d2b78faf4f100d59341aeaeff42
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624956"
---
# <a name="create-a-new-azure-service-fabric-cluster-with-managed-identity-support-preview"></a>Új Azure Service Fabric-fürt létrehozása felügyelt identitás-támogatással (előzetes verzió)

Az Azure Service Fabric-alkalmazások felügyelt identitás funkciójának eléréséhez először engedélyeznie kell a felügyelt Identity token szolgáltatást a fürtön. Ez a szolgáltatás felelős a felügyelt identitások használatával Service Fabric alkalmazások hitelesítéséhez, valamint a hozzáférési jogkivonatok nevében való beszerzéséhez. Ha a szolgáltatás engedélyezve van, a bal oldali ablaktábla **System (rendszer** ) szakaszának Service Fabric Explorer alatt láthatja, hogy az egyéb rendszerszolgáltatások mellett a **háló:/System/ManagedIdentityTokenService** néven fut.

> [!NOTE]
> A **felügyelt Identity token szolgáltatás**engedélyezéséhez Service Fabric futtatókörnyezet 6.5.658.9590 vagy újabb verziója szükséges.  

## <a name="enable-the-managed-identity-token-service"></a>Felügyelt Identity token szolgáltatás engedélyezése 
Ha engedélyezni szeretné a felügyelt identitás-jogkivonat szolgáltatást a fürt létrehozási időpontjában, akkor a következő kódrészletet használhatja egy Azure Resource Manager sablonban:

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

Ha az üzembe helyezés ezzel az üzenettel meghiúsul, az azt jelenti, hogy a fürt nincs a szükséges Service Fabric-verzióban (a minimális támogatott futtatókörnyezet 6,5 CU2):



```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>További lépések
* [Azure Service Fabric-alkalmazás üzembe helyezése rendszerhez rendelt felügyelt identitással](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Azure Service Fabric-alkalmazás üzembe helyezése felhasználó által hozzárendelt felügyelt identitással](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Service Fabric alkalmazás felügyelt identitásának kihasználása a szolgáltatási kódból](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure Service Fabric-alkalmazások hozzáférésének biztosítása más Azure-erőforrásokhoz](./how-to-grant-access-other-resources.md)

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Felügyelt identitások támogatásának](./concepts-managed-identity.md) áttekintése az Azure Service Fabric

* [Felügyelt identitás támogatásának engedélyezése meglévő Azure Service Fabric-fürtben](./configure-existing-cluster-enable-managed-identity-token-service.md)
