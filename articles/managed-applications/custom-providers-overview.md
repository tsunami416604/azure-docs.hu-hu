---
title: Egyéni szolgáltatók az Azure előzetes verziójának áttekintése
description: Egyéni erőforrás-szolgáltató létrehozásához az Azure Resource Managerrel kapcsolatos fogalmakat ismerteti
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: bbfb10f612690af0f4fd3683e0f58986a21048d8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159855"
---
# <a name="azure-custom-providers-preview-overview"></a>Az Azure egyéni szolgáltatók minta áttekintése

Az Azure egyéni szolgáltatók bővítheti Azure-bA és a szolgáltatás. Létrehozhat saját erőforrás-szolgáltató, beleértve a testre szabott erőforrástípusainak és műveleteinek. Az egyéni szolgáltató integrálva van az Azure Resource Managerrel. Használhatja a Resource Manager funkciók, például a sablon-üzembehelyezések és a szerepköralapú hozzáférés-vezérlés, üzembe helyezését, és saját szolgáltatás védelme.

Ez a cikk az egyéni szolgáltatók és annak képességeit áttekintést nyújt. Az alábbi képen látható a munkahelyi flow-erőforrások és a egy egyéni szolgáltató definiált műveletek.

![Egyéni szolgáltatók áttekintése](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> Egyéni szolgáltatók jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="define-your-custom-provider"></a>Az egyéni szolgáltató megadása

Indítsa el, hogy az Azure Resource Manager az egyéni szolgáltató ismertek. Helyez üzembe az Azure egyéni szolgáltató erőforrás, amely használja az erőforrás típusát **Microsoft.CustomProviders/resourceProviders**. Az adott erőforrás erőforrásokhoz és műveletekhez tartozó meghatározása a szolgáltatás.

Például, ha a szolgáltatáshoz szükség van egy nevű erőforrástípust **felhasználók**, az egyéni szolgáltató definíciója közé tartozik-e az erőforrás típusát. Egyes erőforrástípusok adjon meg egy végpontot, amely az adott erőforrástípus REST műveleteket (GET, PUT, DELETE) biztosít. A végpont üzemeltethető bármilyen környezetben, és hogyan a szolgáltatás végzi el az erőforrástípus műveleteket az logikáját tartalmazza.

Az erőforrás-szolgáltató az egyéni műveletek is meghatározhat. Műveletek a POST műveletek képviseli. Műveletek használata a műveleteket, például Indítás, Leállítás vagy újraindítás. Megad egy végpontot, amely kezeli a kérelmet.

Az alábbi példa bemutatja, hogyan definiálhat egy egyéni szolgáltató a művelet és a egy erőforrás típusa.

```json
{
  "apiVersion": "2018-09-01-preview",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[parameters('funcName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[concat('https://', parameters('funcName'), '.azurewebsites.net/api/{requestPath}')]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[concat('https://', parameters('funcName'), '.azurewebsites.net/api/{requestPath}')]"
      }
    ]
  }
},
```

A **routingType**, az elfogadott értékek a következők `Proxy` és `Cache`. Proxy: kérelmek az erőforrástípushoz a vagy művelet a végpont által kezelt. Az ügyfélgyorsítótár beállítása csak támogatott erőforrástípus esetében nem műveleteket. Adja meg a gyorsítótárban, is meg kell a proxy. Gyorsítótár azt jelenti, hogy a végpont érkező válaszokat tárolódnak az olvasási műveletek optimalizálása érdekében. Az ügyfélgyorsítótár beállítása használatával megkönnyíti, amely egységes és megfelelő más Resource Manager szolgáltatással API-k megvalósításához.

## <a name="deploy-your-resource-types"></a>A következő típusú erőforrások üzembe helyezése

Az egyéni szolgáltató meghatározása, után telepítheti a testre szabott erőforrástípusok. Az alábbi példa bemutatja a JSON és a sablon üzembe helyezéséhez az erőforrástípust az egyéni szolgáltató szerepeltetni kívánt. Az erőforrástípus is üzembe helyezhetők az egyéb Azure-erőforrások ugyanabban a sablonban.

```json
{
    "apiVersion": "2018-09-01-preview",
    "type": "Microsoft.CustomProviders/resourceProviders/users",
    "name": "[concat(parameters('rpname'), '/santa')]",
    "location": "[parameters('location')]",
    "properties": {
        "FullName": "Santa Claus",
        "Location": "NorthPole"
    }
}
```

## <a name="manage-access"></a>Hozzáférés kezelése

Az Azure [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) az erőforrás-szolgáltató való hozzáférés kezelése. Hozzárendelhet [beépített szerepkörök](../role-based-access-control/built-in-roles.md) például tulajdonos, közreműködő vagy olvasó a felhasználók számára. Vagy megadhat [egyéni szerepkörök](../role-based-access-control/custom-roles.md) kifejezetten az erőforrás-szolgáltató az műveletek.

## <a name="next-steps"></a>További lépések

Ebből a cikkből megismerhette egyéni szolgáltatókat. Nyissa meg a következő cikk az egyéni szolgáltató létrehozásához.

> [!div class="nextstepaction"]
> [Oktatóanyag: Hozzon létre egyéni szolgáltatót és az egyéni erőforrások üzembe helyezése](create-custom-provider.md)
