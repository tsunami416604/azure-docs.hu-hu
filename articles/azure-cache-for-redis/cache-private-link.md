---
title: Azure cache a Redis az Azure Private linkkel (előzetes verzió)
description: Az Azure Private Endpoint egy olyan hálózati adapter, amely az Azure-beli privát kapcsolaton keresztül az Azure cache-hez biztosít privát és biztonságos Redis. Ebből a cikkből megtudhatja, hogyan hozhat létre Azure cache-t, egy Azure-beli virtuális hálózatot és egy privát végpontot a Azure Portal használatával.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: d85fe36bb948ae9a0c81fa25f87450c7f5fe93b7
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337262"
---
# <a name="azure-cache-for-redis-with-azure-private-link-preview"></a>Azure cache a Redis az Azure Private linkkel (előzetes verzió)
Az Azure Private Endpoint egy olyan hálózati adapter, amely az Azure-beli privát kapcsolaton keresztül az Azure cache-hez biztosít privát és biztonságos Redis. 

Ebből a cikkből megtudhatja, hogyan hozhat létre Azure cache-t, egy Azure-beli virtuális hálózatot és egy privát végpontot a Azure Portal használatával.  

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)

> [!NOTE]
  > Ez a funkció jelenleg előzetes verzióban érhető el – [lépjen kapcsolatba velünk](mailto:azurecache@microsoft.com) , ha érdeklik.
  >


## <a name="create-a-cache"></a>Gyorsítótár létrehozása
1. Gyorsítótár létrehozásához jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és válassza az **erőforrás létrehozása**lehetőséget. 

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Válassza az erőforrás létrehozása lehetőséget.":::
   
1. Az **új** lapon válassza az **adatbázisok** lehetőséget, majd válassza az Azure cache lehetőséget a **Redis számára**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Válassza ki az Azure cache-t a Redis.":::
   
1. Az **új Redis cache** lapon adja meg az új gyorsítótár beállításait.
   
   | Beállítás      | Ajánlott érték  | Leírás |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-név** | Adjon meg egy globálisan egyedi nevet. | A gyorsítótár nevének 1 és 63 karakter közötti sztringnek kell lennie, amely csak számokat, betűket vagy kötőjeleket tartalmaz. A névnek számmal vagy betűvel kell kezdődnie és végződnie, és nem tartalmazhat egymást követő kötőjeleket. A gyorsítótár-példány *állomásneve* a * \<DNS name> . Redis.cache.Windows.net*lesz. | 
   | **Előfizetés** | Legördülő menüből válassza ki az előfizetését. | Az előfizetés, amely alatt létre kell hoznia ezt az új Azure cache-t a Redis-példányhoz. | 
   | **Erőforráscsoport** | Legördülő listából válassza ki az erőforráscsoportot, vagy válassza az **új létrehozása** elemet, és adjon meg egy új erőforráscsoport-nevet. | Azon erőforráscsoport neve, amelyben létre szeretné hozni a gyorsítótárat és az egyéb erőforrásokat. Az összes alkalmazás-erőforrás egy erőforráscsoporthoz való elhelyezésével könnyedén kezelheti és törölheti azokat. | 
   | **Hely** | Legördülő menüből válassza ki a helyet. | Válasszon ki egy [régiót](https://azure.microsoft.com/regions/) a többi olyan szolgáltatás közelében, amely a gyorsítótárat fogja használni. |
   | **Tarifacsomag** | Legördülő menüből válassza ki a [díjszabási szintet](https://azure.microsoft.com/pricing/details/cache/). |  A tarifacsomag határozza meg a gyorsítótár méretét, teljesítményét és elérhető funkcióit. További információ: [Azure cache for Redis – áttekintés](cache-overview.md). |
   
1. Válassza a **Létrehozás** lehetőséget. 
   
    :::image type="content" source="media/cache-private-link/3-new-cache.png" alt-text="Azure cache létrehozása a Redis számára.":::
   
   Eltarthat egy ideig a gyorsítótár létrehozásához. Nyomon követheti a folyamat előrehaladását az Azure cache Redis **– Áttekintés** oldalon. Ha az **állapot** **futásra**mutat, a gyorsítótár készen áll a használatra.
    
    :::image type="content" source="media/cache-private-link/4-status.png" alt-text="Redis létrehozott Azure cache.":::

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Ebben a szakaszban létre fog hozni egy virtuális hálózatot és alhálózatot.

1. Válassza az **Erőforrás létrehozása** lehetőséget.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Válassza az erőforrás létrehozása lehetőséget.":::

2. Az **új** lapon válassza a **hálózatkezelés** , majd a **virtuális hálózat**lehetőséget.

    :::image type="content" source="media/cache-private-link/5-select-vnet.png" alt-text="Hozzon létre egy virtuális hálót.":::

3. A **virtuális hálózat létrehozása**területen adja meg vagy válassza ki ezt az információt az **alapok** lapon:

    | **Beállítás**          | **Érték**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt részletei**  |                                                                 |
    | Előfizetés     | Legördülő menüből válassza ki az előfizetését.                                  |
    | Erőforráscsoport   | Válassza ki a legördülő listát, és válasszon ki egy erőforráscsoportot. |
    | **Példány részletei** |                                                                 |
    | Név             | Be**\<virtual-network-name>**                                    |
    | Régió           | Válassza**\<region-name>** |

4. Válassza az **IP-címek** lapot, vagy válassza a **következő: IP-címek** gombot az oldal alján.

5. Az **IP-címek** lapon adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | IPv4-címterület | Be**\<IPv4-address-space>** |

6. Az **alhálózat neve**alatt válassza ki az **alapértelmezett**szót.

7. Az **alhálózat szerkesztése**területen adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | Alhálózat neve | Be**\<subnet-name>** |
    | Alhálózati címtartomány | Be**\<subnet-address-range>**

8. Válassza a **Mentés** lehetőséget.

9. Válassza a **felülvizsgálat + létrehozás** lapot, vagy kattintson a **felülvizsgálat + létrehozás** gombra.

10. Válassza a **Létrehozás** lehetőséget.


## <a name="create-a-private-endpoint"></a>Privát végpont létrehozása 

Ebben a szakaszban létrehoz egy privát végpontot, és a korábban létrehozott gyorsítótárhoz kapcsolja.

1. Keresse meg a **privát hivatkozást** , majd nyomja le az ENTER billentyűt, vagy válassza ki a keresési javaslatok közül.

    :::image type="content" source="media/cache-private-link/7-create-private-link.png" alt-text="Privát hivatkozás keresése.":::

2. A képernyő bal oldalán válassza a **privát végpontok**lehetőséget.

    :::image type="content" source="media/cache-private-link/8-select-private-endpoint.png" alt-text="Válassza a privát hivatkozás lehetőséget.":::

3. A privát végpont létrehozásához kattintson a **+ Hozzáadás** gombra. 

    :::image type="content" source="media/cache-private-link/9-add-private-endpoint.png" alt-text="Adjon hozzá egy privát hivatkozást.":::

4. A **magánhálózati végpont létrehozása lapon**konfigurálja a saját végpontjának beállításait.

    | Beállítás | Érték |
    | ------- | ----- |
    | **PROJEKT RÉSZLETEI** | |
    | Előfizetés | Legördülő menüből válassza ki az előfizetését. |
    | Erőforráscsoport | Válassza ki a legördülő listát, és válasszon ki egy erőforráscsoportot. |
    | **PÉLDÁNY RÉSZLETEI** |  |
    | Név |Adja meg a privát végpont nevét.  |
    | Régió |Legördülő menüből válassza ki a helyet. |
    |||

5. Válassza a **következő: erőforrás** gombot a lap alján.

6. Az **erőforrás** lapon válassza ki az előfizetését, válassza ki a Microsoft. cache/Redis típusú erőforrástípust, majd válassza ki az előző szakaszban létrehozott gyorsítótárat.

    :::image type="content" source="media/cache-private-link/10-resource-private-endpoint.png" alt-text="A privát kapcsolat erőforrásai.":::

7. A lap alján kattintson a **következő: konfigurálás** gombra.

8. A **konfiguráció** lapon válassza ki az előző szakaszban létrehozott virtuális hálózatot és alhálózatot.

    :::image type="content" source="media/cache-private-link/11-configuration-private-endpoint.png" alt-text="A privát hivatkozás konfigurációja.":::

9. A lap alján kattintson a **Next: Tags (tovább** ) gombra.

10. A **címkék** lapon adja meg a nevet és az értéket, ha az erőforrást kategorizálni szeretné. Ez a lépés nem kötelező.

    :::image type="content" source="media/cache-private-link/12-tags-private-endpoint.png" alt-text="A privát hivatkozáshoz tartozó címkék.":::

11. Válassza a **felülvizsgálat + létrehozás**lehetőséget. A **felülvizsgálat + létrehozás**   lapon az Azure ellenőrzi a konfigurációt.

12. Amint megjelenik az **átadott zöld ellenőrzés** üzenet, válassza a **Létrehozás**lehetőséget.


## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a privát hivatkozásokról, tekintse meg az [Azure Private link dokumentációját](https://docs.microsoft.com/azure/private-link/private-link-overview). 

