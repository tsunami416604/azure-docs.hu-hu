---
title: Felügyelt identitástámogatás konfigurálása meglévő Service Fabric-fürtben
description: Így engedélyezheti a felügyelt identitások támogatását egy meglévő Azure Service Fabric-fürtben
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: cb6e4ab00afd80cba41881e46296f7046a905919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934950"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster-preview"></a>Felügyelt identitástámogatás konfigurálása meglévő Service Fabric-fürtben (előzetes verzió)

Felügyelt [identitások használata az Azure-erőforrások a](../active-directory/managed-identities-azure-resources/overview.md) Service Fabric-alkalmazások, először engedélyezze a felügyelt *identitásjogkivonat-szolgáltatás* a fürtön. Ez a szolgáltatás felelős a Service Fabric-alkalmazások felügyelt identitások használatával történő hitelesítésért, valamint a hozzáférési jogkivonatok nevükben történő beszerzéséért. Ha a szolgáltatás engedélyezve van, láthatja, hogy a Service Fabric Explorer a **rendszer szakasza** a bal oldali ablaktáblában, néven futó **fabric:/System/ManagedIdentityTokenService**.

> [!NOTE]
> A Service Fabric 6.5.658.9590-es vagy újabb verziója szükséges a **felügyelt identitásjogkivonat-szolgáltatás**engedélyezéséhez.  
>
> A fürt Service Fabric-verziója az Azure Portalon található a fürterőforrás megnyitásával és a **Service Fabric verziótulajdonságának** ellenőrzésével az **Essentials** szakaszban.
>
> Ha a fürt **kézi** frissítési módban van, először frissítenie kell azt 6.5.658.9590 vagy újabb verzióra.

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>*Felügyelt identitásjogkivonat-szolgáltatás* engedélyezése meglévő fürtben

A felügyelt identitásjogkivonat-szolgáltatás meglévő fürtben való engedélyezéséhez két módosítást tartalmazó fürtfrissítést kell kezdeményeznie: (1) A felügyelt identitásjogkivonat-szolgáltatás engedélyezése és (2) az egyes csomópontok újraindítását. Először adja hozzá a következő részletet a fürt Azure Resource Manager sablon:

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

Ahhoz, hogy a módosítások érvénybe lépjenek, a frissítési szabályzatot is módosítania kell, hogy a fürtön keresztül a frissítés előrehaladtával a Service Fabric futásidejű állapotának erőteljes újraindítását adja meg. Ez az újraindítás biztosítja, hogy az újonnan engedélyezett rendszerszolgáltatás elindul, és fut az egyes csomópontokon. Az alábbi kódrészletben `forceRestart` található az alapvető beállítás; a beállítások hátralévő részében használja a meglévő értékeket.  

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
> A frissítés sikeres befejezése után ne felejtse `forceRestart` el visszaállítani a beállítást, hogy minimalizálja a későbbi frissítések hatását. 

## <a name="errors-and-troubleshooting"></a>Hibák és hibaelhárítás

Ha a központi telepítés sikertelen a következő üzenettel, az azt jelenti, hogy a fürt nem fut elég magas Service Fabric-verzión:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>További lépések
* [Azure Service Fabric-alkalmazás üzembe helyezése rendszerhez rendelt felügyelt identitással](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Azure Service Fabric-alkalmazás üzembe helyezése felhasználó által hozzárendelt felügyelt identitással](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [A Service Fabric-alkalmazások felügyelt identitásának kihasználása a szolgáltatáskódból](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure Service Fabric-alkalmazás elérésének biztosítása más Azure-erőforrásokhoz](./how-to-grant-access-other-resources.md)