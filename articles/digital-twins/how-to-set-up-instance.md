---
title: Azure Digital Twins-példány létrehozása
titleSuffix: Azure Digital Twins
description: 'Lásd: az Azure Digital Twins szolgáltatás példányának beállítása.'
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c9489b9e1afe5e42121f61a3b0b50b28b2401bd3
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392284"
---
# <a name="set-up-an-azure-digital-twins-instance"></a>Azure digitális Twins-példány beállítása

Ez a cikk végigvezeti az új Azure Digital Twins-példány beállításának alapvető lépésein. Ez magában foglalja a példány létrehozását, valamint [Azure Active Directory (HRE)](../active-directory/fundamentals/active-directory-whatis.md) engedélyeknek a példányhoz való hozzárendelését.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="set-up-an-azure-digital-twins-instance"></a>Azure digitális Twins-példány beállítása

Ezután futtassa a következő parancsokat egy új Azure-erőforráscsoport létrehozásához, amely ebben az útmutatóban használható, majd hozzon létre egy új Azure Digital Twins-példányt ebben az erőforráscsoportban.

```azurecli
az group create --location <region> --name <name-for-your-resource-group>
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

> [!TIP]
> Futtassa a következő parancsot az Azure-régiók azon neveinek listájához, amelyek átadhatók az Azure CLI parancsaihoz:
> ```azurecli
> az account list-locations -o table
> ```
> Ha szeretné megtekinteni, hogy mely régiók támogatják az Azure Digital Twins-t, látogasson el az [Azure-termékek területre](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).

A parancsok eredménye így néz ki, így a létrehozott erőforrásokra vonatkozó információk kihelyezése:

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="Parancsablak az erőforráscsoport és az Azure Digital Twins-példány sikeres létrehozásával":::

Jegyezze fel az Azure Digital Twins-példány *állomásneve*, *neve*és *resourceGroup* a kimenetből. Ezek az összes olyan kulcsfontosságú érték, amire szüksége lehet, ha folytatja az Azure Digital Twins-példánnyal való munkát, a hitelesítés és a kapcsolódó Azure-erőforrások beállításához.

> [!TIP]
> Ezeket a tulajdonságokat, valamint a példány összes tulajdonságát a futtatásával bármikor megtekintheti `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

### <a name="assign-azure-active-directory-permissions"></a>Azure Active Directory engedélyek kiosztása

Az Azure Digital Twins [Azure Active Directory (HRE)](../active-directory/fundamentals/active-directory-whatis.md) szerepkör-alapú hozzáférés-vezérlést (RBAC) használ. Ez azt jelenti, hogy az adatsík hívásokat az Azure Digital Twins-példányhoz kell rendelnie, és előbb hozzá kell rendelnie egy szerepkört ezekkel az engedélyekkel.

Ahhoz, hogy az Azure Digital Twins-t egy ügyfélalkalmazás használatával használhassa, meg kell győződnie arról, hogy az ügyfélalkalmazás képes hitelesíteni az Azure Digital Twins szolgáltatásban. Ezt egy Azure Active Directory (HRE) alkalmazás regisztrációjának beállításával teheti meg, amely a következő témakörben olvasható [: az ügyfélalkalmazás hitelesítése](how-to-authenticate-client.md).

#### <a name="assign-yourself-a-role"></a>Saját szerepkör kiosztása

Hozzon létre egy szerepkör-hozzárendelést saját magához az Azure-előfizetéséhez tartozó HRE-bérlőhöz társított e-mail-cím használatával. 

Először is győződjön meg róla, hogy az Azure-előfizetésében tulajdonosként van besorolva. Ezt a parancs használatával ellenőrizheti `az role assignment list --assignee <your-Azure-email>` , és ellenőrizheti, hogy a *RoleDefinitionName* értéke *tulajdonos*-e. Az előfizetés tulajdonosaként a következő paranccsal rendelheti hozzá a felhasználót az Azure Digital Twins-példány tulajdonosi szerepköréhez:

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-AAD-email>" --role "Azure Digital Twins Owner (Preview)"
```

Ennek a parancsnak az eredménye a létrehozott szerepkör-hozzárendeléssel kapcsolatos információ.

> [!TIP]
> Ha a *400: BadRequest* hiba jelenik meg, futtassa a következő parancsot a felhasználó *ObjectId* beszerzéséhez:
> ```azurecli
> az ad user show --id <your-AAD-email> --query objectId
> ```
> Ezután ismételje meg a szerepkör-hozzárendelési parancsot a felhasználó *objektum-azonosítójával* az e-mail cím helyett.

Most már rendelkezik egy Azure Digital Twins-példánnyal, amely készen áll a használatra.

## <a name="next-steps"></a>Következő lépések

Tekintse meg, hogyan állíthatja be és hitelesítheti az ügyfélalkalmazás működését a példánnyal való együttműködéshez:
* [Útmutató: ügyfélalkalmazás hitelesítése](how-to-authenticate-client.md)
