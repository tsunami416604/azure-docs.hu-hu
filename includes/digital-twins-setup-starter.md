---
author: baanders
description: fájl belefoglalása a kezdő adatokhoz az Azure digitális Twins telepítőben
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: dcc616dd8d30bfadb751df9ba24d71d72675428a
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371454"
---
>[!NOTE]
>Ezeket a műveleteket egy, az Azure-előfizetéshez tartozó *tulajdonosi* szerepkörrel rendelkező felhasználó tölti ki. Bár egyes darabok ezen emelt szintű engedély nélkül is elvégezhető, a tulajdonos együttműködésére lesz szükség a felhasználható példány teljes beállításához. Tekintse meg a további információkat az [*Előfeltételek: szükséges engedélyek*](#prerequisites-permission-requirements) szakaszban alább.

Az új Azure Digital Twins-példány teljes beállítása három részből áll:
1. **A példány létrehozása**
2. A **felhasználó hozzáférési engedélyeinek beállítása**: az Azure-felhasználónak az *Azure Digital Twins tulajdonos (előzetes verzió)* szerepkörrel kell rendelkeznie a példányon a felügyeleti tevékenységek elvégzése érdekében. Ebben a lépésben hozzárendelheti saját magát ehhez a szerepkörhöz (ha Ön az Azure-előfizetés tulajdonosa), vagy az előfizetése tulajdonosának beszerzésével rendelheti hozzá.
3. **Ügyfélalkalmazások hozzáférési engedélyeinek beállítása**: gyakori, hogy a példányával folytatott kommunikációhoz használt ügyfélalkalmazás írása. Ahhoz, hogy az ügyfélalkalmazás hozzáférhessen az Azure Digital Twins-hoz, be kell állítania egy alkalmazás- *regisztrációt* [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) , hogy az ügyfélalkalmazás a példány hitelesítésére használja majd.

A folytatáshoz szüksége lesz egy Azure-előfizetésre. [Itt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)megadhat egyet ingyenesen.

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Előfeltételek: engedélyezési követelmények

A cikkben szereplő összes lépés végrehajtásához az Azure-előfizetésében tulajdonosként kell besorolni. 

A következő parancs futtatásával megtekintheti a jogosultsági szintet Cloud Shellban:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

Ha Ön a tulajdonosa, a `roleDefinitionName` kimenetben szereplő érték a *tulajdonos*:

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/owner-role.png" alt-text="Cloud Shell ablak, amely az az role-hozzárendelési lista parancs kimenetét mutatja":::

Ha úgy találja, hogy az érték *közreműködő* vagy valamilyen más, mint a *tulajdonos*, a következő módokon léphet tovább:
* Lépjen kapcsolatba az előfizetés tulajdonosával, és kérje meg a tulajdonost, hogy hajtsa végre a cikkben szereplő lépéseket az Ön nevében.
* Vegye fel a kapcsolatot az előfizetés tulajdonosával vagy valakivel a felhasználói hozzáférés rendszergazdai szerepkörrel az előfizetésben, és kérje meg, hogy az előfizetés tulajdonosaként emelje a tulajdonost az előfizetésben, hogy jogosult legyen a folytatásra. Attól függően, hogy ez megfelelő-e, a szervezettől és a benne lévő szerepkörtől függ.
