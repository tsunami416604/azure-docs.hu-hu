---
title: Azure Service Fabric – meglévő Azure Service Fabric-fürt konfigurálása a felügyelt identitások támogatásának engedélyezéséhez
description: Ez a cikk bemutatja, hogyan konfigurálhat egy meglévő Azure Service Fabric-fürtöt a felügyelt identitások támogatásának engedélyezéséhez
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 13b8b38a206b0dae0877263a5cda56a134d4788d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75351610"
---
# <a name="configure-an-existing-azure-service-fabric-cluster-to-enable-managed-identity-support-preview"></a>Meglévő Azure Service Fabric-fürt konfigurálása a felügyelt identitás támogatásának engedélyezéséhez (előzetes verzió)
Az Azure Service Fabric-alkalmazások felügyelt identitás funkciójának eléréséhez először engedélyeznie kell a **felügyelt Identity token szolgáltatást** a fürtön. Ez a szolgáltatás felelős a felügyelt identitások használatával Service Fabric alkalmazások hitelesítéséhez, valamint a hozzáférési jogkivonatok nevében való beszerzéséhez. Ha a szolgáltatás engedélyezve van, a bal oldali ablaktábla **System (rendszer** ) szakaszának Service Fabric Explorer alatt láthatja, hogy a **háló:/System/ManagedIdentityTokenService**néven fut.

> [!NOTE]
> A **felügyelt Identity token szolgáltatás**engedélyezéséhez Service Fabric futtatókörnyezet 6.5.658.9590 vagy újabb verziója szükséges.  
> 
> A fürt Service Fabric verzióját a Azure Portal megkeresheti a fürterőforrás megnyitásával és a **Service Fabric Version** tulajdonságának ellenőrzésével az **Essentials (alapvető** erőforrások) szakaszban.
> 
> Ha a fürt **manuális** frissítési módban van, először frissítenie kell a 6.5.658.9590 vagy újabb verzióra.


## <a name="enable-the-managed-identity-token-service-in-an-existing-cluster"></a>Felügyelt identitás-jogkivonat szolgáltatás engedélyezése meglévő fürtben
Ha engedélyezni szeretné a felügyelt identitás-jogkivonat szolgáltatást egy meglévő fürtben, a fürt frissítését két módosítást kell megadnia: a felügyelt identitási jogkivonat szolgáltatás engedélyezése és az egyes csomópontok újraindításának kérése. Ehhez adja hozzá a következő két kódrészletet a Azure Resource Manager sablonhoz:

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

Ahhoz, hogy a módosítások életbe lépjenek, módosítania kell a frissítési házirendet, hogy az egyes csomópontokon a frissítés folyamata során az Service Fabric-futtatókörnyezet kényszerített újraindítását is megadja. Ez az újraindítás biztosítja, hogy az újonnan engedélyezett rendszerszolgáltatás elindult, és minden csomóponton fusson. Az alábbi kódrészletben `forceRestart` az alapvető beállítás; a többi beállításhoz használja a meglévő értékeket.  

```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```

> [!NOTE]
> A frissítés sikeres befejezése után ne felejtse el visszaállítani a `forceRestart` beállítást, hogy csökkentse a további frissítések hatását. 

## <a name="errors-and-troubleshooting"></a>Hibák és hibaelhárítás

Ha az üzembe helyezés a következő üzenettel meghiúsul, az azt jelenti, hogy a fürt nem elég magas Service Fabric-verzión fut:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Következő lépések
* [Azure Service Fabric-alkalmazás üzembe helyezése rendszerhez rendelt felügyelt identitással](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Azure Service Fabric-alkalmazás üzembe helyezése felhasználó által hozzárendelt felügyelt identitással](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Service Fabric alkalmazás felügyelt identitásának kihasználása a szolgáltatási kódból](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure Service Fabric-alkalmazások hozzáférésének biztosítása más Azure-erőforrásokhoz](./how-to-grant-access-other-resources.md)