---
title: 'Gyors útmutató: vállalati szintű gyorsítótár létrehozása'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure cache-példányt a Redis Enterprise szintjéhez
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/12/2020
ms.openlocfilehash: f3eee85c600d40b3997a0e6dff6a9b218242feb4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83204717"
---
# <a name="quickstart-create-an-enterprise-tier-cache-preview"></a>Gyors útmutató: vállalati szintű gyorsítótár létrehozása (előzetes verzió)

Az Azure cache for Redis vállalati szintjei teljes mértékben integrált és felügyelt [Redis Enterprise](https://redislabs.com/redis-enterprise/) -t biztosítanak az Azure-ban. Jelenleg előzetes verzióként érhetők el. Ebben az előzetes verzióban két új szint érhető el:
* Vállalat, amely felejtő memóriát használ a virtuális gépeken az adattároláshoz
* A vállalati SSD, amely illékony és nem felejtő memóriát (NVMe) használ az adattároláshoz.

Nincs az előzetes verzióhoz való csatlakozás díja. Ha érdekli, regisztráljon az [Azure piactéren](https://aka.ms/redispreviewsignup/) . Nagyon korlátozott számú résztvevővel rendelkezünk, és nem garantáljuk, hogy elfogadja az előzetes verziót.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, szüksége lesz egy Azure-előfizetésre. Ha még nem rendelkezik ilyennel, először hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="create-a-cache"></a>Gyorsítótár létrehozása
1. Gyorsítótár létrehozásához jelentkezzen be a Azure Portal az előnézeti meghívásban található hivatkozással, és válassza az **erőforrás létrehozása**lehetőséget.

   > [!IMPORTANT] 
   > Ne fizessen elő az Azure cache-re az *Redis,* a piactéren közvetlenül a vállalati szinten.
   > Ezt a lépést a Redis-portál felhasználói felületéhez tartozó Azure cache automatikusan hajtja végre.
   >
   
1. Az **új** lapon válassza az **adatbázisok** lehetőséget, majd válassza az Azure cache lehetőséget a **Redis számára**.
   
   ![Az Azure cache kiválasztása a Redis](media/cache-create/new-cache-menu.png)
   
1. Az **új Redis cache** lapon adja meg az új gyorsítótár beállításait.
   
   | Beállítás      | Ajánlott érték  | Leírás |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Előfizetés** | Legördülő menüből válassza ki az előfizetését. | Az előfizetés, amely alatt létre kell hoznia ezt az új Azure cache-t a Redis-példányhoz. | 
   | **Erőforráscsoport** | Legördülő listából válassza ki az erőforráscsoportot, vagy válassza az **új létrehozása** elemet, és adjon meg egy új erőforráscsoport-nevet. | Azon erőforráscsoport neve, amelyben létre szeretné hozni a gyorsítótárat és az egyéb erőforrásokat. Az összes alkalmazás-erőforrás egy erőforráscsoporthoz való elhelyezésével könnyedén kezelheti és törölheti azokat. | 
   | **DNS-név** | Adjon meg egy globálisan egyedi nevet. | A gyorsítótár nevének 1 és 63 karakter közötti sztringnek kell lennie, amely csak számokat, betűket vagy kötőjeleket tartalmaz. A névnek számmal vagy betűvel kell kezdődnie és végződnie, és nem tartalmazhat egymást követő kötőjeleket. A gyorsítótár-példány *állomásneve* a * \< DNS-név lesz>. <Azure region> . redisenterprise.cache.azure.net*. | 
   | **Hely** | Legördülő menüből válassza ki a helyet. | A vállalati szintek az USA nyugati régiójában, az USA 2. keleti régiójában és Nyugat-Európában is elérhetők. |
   | **Gyorsítótár szintje** | Legördülő menüből válassza ki a *nagyvállalati DRAM* vagy a *vállalati SSD* -szintet, valamint a méretet. |  A szinten a gyorsítótár számára elérhető méretet, teljesítményt és funkciókat határozza meg. |
   
   ![Nagyvállalati szint alapjai](media/cache-create/enterprise-tier-basics.png) 

1. Válassza a **Tovább: hálózatkezelés** és Kihagyás lehetőséget.

   > [!NOTE] 
   > A privát hivatkozás támogatása később fog megjelenni.
   >

1. Válassza a **Tovább: speciális**lehetőséget.
   
   Megtarthatja az alapértelmezett beállításokat, és szükség szerint módosíthatja azokat. Ha bekapcsolja a **hozzáférés engedélyezése csak a TLS protokollon keresztül lehetőséget**, a TLS használatával férhet hozzá az alkalmazás új gyorsítótárához.

   ![Nagyvállalati szintű fejlett](media/cache-create/enterprise-tier-advanced.png) 

   > [!NOTE] 
   > A Redis modulok még nem támogatottak a vállalati SSD-platformon. Ha Redis-modult szeretne használni, akkor mindenképpen válasszon egy vállalati szintű gyorsítótárat.
   >
   
1. Válassza a **Next (tovább): címkék** és Kihagyás lehetőséget.

1. Válassza a **Tovább: összefoglalás**lehetőséget.

   ![Nagyvállalati szintű összefoglalás](media/cache-create/enterprise-tier-summary.png) 

1. Kattintson a **feltételek**területen a jelölőnégyzetre, tekintse át a beállításokat, majd válassza a **felülvizsgálat + létrehozás**elemet.
   
   Eltarthat egy ideig a gyorsítótár létrehozásához. Nyomon követheti a folyamat előrehaladását az Azure cache Redis **– Áttekintés** oldalon. Ha az **állapot** **futásra**mutat, a gyorsítótár készen áll a használatra.

   > [!NOTE] 
   > A nagyvállalati szintű gyorsítótár létrejötte után egy kis idő elteltével az Azure Marketplace-ről a *Redis-hez, a nagyvállalati rétegekhöz való Azure cache*konfigurálásához egy **szükséges műveletet** fog kapni. Ehhez a művelethez nincs szükség. Ezt az e-mailt nyugodtan figyelmen kívül hagyhatja.
   >

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre az Azure cache nagyvállalati szintű példányát a Redis.

> [!div class="nextstepaction"]
> [Hozzon létre egy ASP.NET-webalkalmazást, amely egy Azure cache-t használ a Redis.](./cache-web-app-howto.md)

