---
author: baanders
description: fájl belefoglalása a szerepkörre az Azure digitális Twins telepítőjének telepítéséhez
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 2f10a6915a3edf673316ae9151b6052442678ef9
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87832345"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Előfeltételek: engedélyezési követelmények

A cikkben szereplő összes lépés végrehajtásához az Azure-előfizetésében tulajdonosként kell besorolni. 

A következő parancs futtatásával megtekintheti a jogosultsági szintet Cloud Shellban:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

> [!NOTE]
> Ha a parancs hibát ad vissza, ami azt jelzi, hogy a parancssori **felület nem találja a felhasználó vagy az egyszerű szolgáltatásnév kifejezést a Graph adatbázisban**:
>
> A cikk további részében használja a felhasználói *objektumazonosító* helyett a saját e-mail-címét. Ez akkor fordulhat elő, ha a felhasználók személyes [Microsoft-fiókokkal (MSAs)](https://account.microsoft.com/account)rendelkeznek. 
>
> A felhasználói fiók kiválasztásához és a részletek megnyitásához használja [Azure Active Directory felhasználók Azure Portal lapját](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) . A felhasználó *ObjectId*másolása:
>
> :::image type="content" source="../articles/digital-twins/media/includes/user-id.png" alt-text="A felhasználó oldalának nézete Azure Portal kiemelve a GUID azonosítót az &quot;objektumazonosító&quot; mezőben" lightbox="../articles/digital-twins/media/includes/user-id.png":::
>
> Ezután ismételje meg a szerepkör-hozzárendelési lista parancsát a felhasználó *objektum-azonosítójával* az e-mail cím helyett.

A szerepkör-hozzárendelési lista parancs futtatása után, ha Ön a tulajdonosa, a `roleDefinitionName` kimenetben szereplő érték a *tulajdonos*:

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="A felhasználó oldalának nézete Azure Portal kiemelve a GUID azonosítót az &quot;objektumazonosító&quot; mezőben":::

Ha úgy találja, hogy az érték *közreműködő* vagy valamilyen más, mint a *tulajdonos*, a következő módokon léphet tovább:
* Lépjen kapcsolatba az előfizetés tulajdonosával, és kérje meg a tulajdonost, hogy hajtsa végre a cikkben szereplő lépéseket az Ön nevében.
* Vegye fel a kapcsolatot az előfizetés tulajdonosával vagy valakivel a felhasználói hozzáférés rendszergazdai szerepkörrel az előfizetésben, és kérje meg, hogy az előfizetés tulajdonosaként emelje a tulajdonost az előfizetésben, hogy jogosult legyen a folytatásra. Attól függően, hogy ez megfelelő-e, a szervezettől és a benne lévő szerepkörtől függ.