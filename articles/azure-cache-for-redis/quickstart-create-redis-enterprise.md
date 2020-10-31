---
title: 'Gyors útmutató: vállalati szintű gyorsítótár létrehozása'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure cache-példányt a Redis Enterprise szintjéhez
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: bd5e05f38d34199d9012c52ca3fdad33af231aad
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127985"
---
# <a name="quickstart-create-an-enterprise-tier-cache-preview"></a>Gyors útmutató: vállalati szintű gyorsítótár létrehozása (előzetes verzió)

Az Azure cache for Redis vállalati szintjei teljes mértékben integrált és felügyelt [Redis Enterprise](https://redislabs.com/redis-enterprise/) -t biztosítanak az Azure-ban. Jelenleg előzetes verzióként érhetők el. Ebben az előzetes verzióban két új szint érhető el:
* Vállalat, amely felejtő memóriát használ a virtuális gépeken az adattároláshoz
* A nagyvállalati Flash, amely illékony és nem felejtő memóriát (NVMe vagy SSD) használ az adattároláshoz.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, szüksége lesz egy Azure-előfizetésre. Ha még nem rendelkezik ilyennel, először hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="create-a-cache"></a>Gyorsítótár létrehozása
1. Gyorsítótár létrehozásához jelentkezzen be a Azure Portal az előnézeti meghívásban található hivatkozással, és válassza az **erőforrás létrehozása** lehetőséget.

1. Az **új** lapon válassza az **adatbázisok** lehetőséget, majd válassza az Azure cache lehetőséget a **Redis számára** .
   
   :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Az Azure cache kiválasztása a Redis":::
   
1. Az **új Redis cache** lapon adja meg az új gyorsítótár beállításait.
   
   | Beállítás      | Ajánlott érték  | Leírás |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Előfizetés** | Legördülő menüből válassza ki az előfizetését. | Az előfizetés, amely alatt létre kell hoznia ezt az új Azure cache-t a Redis-példányhoz. | 
   | **Erőforráscsoport** | Legördülő listából válassza ki az erőforráscsoportot, vagy válassza az **új létrehozása** elemet, és adjon meg egy új erőforráscsoport-nevet. | Azon erőforráscsoport neve, amelyben létre szeretné hozni a gyorsítótárat és az egyéb erőforrásokat. Az összes alkalmazás-erőforrás egy erőforráscsoporthoz való elhelyezésével könnyedén kezelheti és törölheti azokat. | 
   | **DNS-név** | Adjon meg egy globálisan egyedi nevet. | A gyorsítótár nevének 1 és 63 karakter közötti sztringnek kell lennie, amely csak számokat, betűket vagy kötőjeleket tartalmaz. A névnek számmal vagy betűvel kell kezdődnie és végződnie, és nem tartalmazhat egymást követő kötőjeleket. A gyorsítótár-példány *állomásneve* a lesz *\<DNS name> . <Azure region> redisenterprise.cache.azure.net* . | 
   | **Hely** | Legördülő menüből válassza ki a helyet. | A nagyvállalati rétegek az előzetes verzióban korlátozott Azure-régiókban érhetők el. |
   | **Gyorsítótár típusa** | Legördülő menüből válassza ki a *vállalati* vagy *vállalati Flash* -szintet és a méretet. |  A szinten a gyorsítótár számára elérhető méretet, teljesítményt és funkciókat határozza meg. |
   
   :::image type="content" source="media/cache-create/enterprise-tier-basics.png" alt-text="Az Azure cache kiválasztása a Redis" alatt bejelöli a mezőt.
   >

1. Válassza a **Tovább: hálózatkezelés** és Kihagyás lehetőséget.

   > [!NOTE] 
   > A magánhálózati hivatkozás lehetőség bekerül, és előfordulhat, hogy nem érhető el azonnal a régiójában.
   >

1. Válassza a **Next (tovább): speciális** lehetőséget, és állítsa be a **fürtözési házirendet** a **vállalat** számára.
   
   Megtarthatja az alapértelmezett beállításokat, és szükség szerint módosíthatja azokat. Ha bekapcsolja a **hozzáférés engedélyezése csak a TLS protokollon keresztül lehetőséget** , a TLS használatával férhet hozzá az alkalmazás új gyorsítótárához.

   :::image type="content" source="media/cache-create/enterprise-tier-advanced.png" alt-text="Az Azure cache kiválasztása a Redis":::

   > [!NOTE] 
   > A Redis modulok még nem támogatottak a vállalati Flash-szinten. Ha Redis-modult szeretne használni, akkor mindenképpen válasszon egy vállalati szintű gyorsítótárat.
   >
   
1. Válassza a **Next (tovább): címkék** és Kihagyás lehetőséget.

1. Válassza a **Tovább: Ellenőrzés és létrehozás** lehetőségre.

   :::image type="content" source="media/cache-create/enterprise-tier-summary.png" alt-text="Az Azure cache kiválasztása a Redis":::

1. Tekintse át a beállításokat, és kattintson a **Létrehozás** gombra.
   
   Eltarthat egy ideig a gyorsítótár létrehozásához. Nyomon követheti a folyamat előrehaladását az Azure cache Redis **– Áttekintés** oldalon. Ha az **állapot** **futásra** mutat, a gyorsítótár készen áll a használatra.

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre az Azure cache nagyvállalati szintű példányát a Redis.

> [!div class="nextstepaction"]
> [Hozzon létre egy ASP.NET-webalkalmazást, amely egy Azure cache-t használ a Redis.](./cache-web-app-howto.md)

