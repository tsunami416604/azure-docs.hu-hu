---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 13ac2ec0569dc79791eca9efb2bd51e7b76ddd05
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572218"
---
1. Jelentkezzen be az [Azure Portalra][Azure portal].
2. A bal oldali menüben válassza az **+ Erőforrás létrehozása** elemet. Ezt követően válassza az **Integration** > **Relay** lehetőséget. Ha nem látja a **Relay** elemet a listában, válassza az **Összes megjelenítése** lehetőséget a jobb felső sarokban. 
3. A **Névtér létrehozása** alatt adja meg a névtér nevét. A rendszer azonnal ellenőrzi, hogy a név elérhető-e.
4. Az **Előfizetés** mezőben válassza ki azt az Azure-előfizetést, amelyben a névteret létre kívánja hozni.
5. Az [Erőforráscsoport](../articles/azure-resource-manager/resource-group-portal.md) mezőben válasszon ki egy meglévő erőforráscsoportot, amelybe a névteret helyezi, vagy hozzon létre egy újat.  
6. A **Hely** mezőben válassza ki azt az országot vagy régiót, amelyben a névtér üzemeltetve lesz.
   
    ![Névtér létrehozása][create-namespace]
7. Kattintson a **Létrehozás** gombra. A rendszer létrehozza, majd engedélyezi a névteret. Néhány perc múltán a rendszer kiosztja az erőforrásokat a fiókja számára.

### <a name="get-management-credentials"></a>Felügyeleti hitelesítő adatok lekérése

1. Válassza ki az **Összes erőforrás** elemet, majd az újonnan létrehozott névtér nevét.
2. A Relay-névtér alatt válassza ki a **Megosztott elérési szabályzatok** elemet.  
3. A **Megosztott elérési szabályzatok** alatt válassza ki a **RootManageSharedAccessKey** elemet.
   
    ![connection-info][connection-info]
4. A **Szabályzat: RootManageSharedAccessKey** alatt válassza a **Kapcsolati sztring – elsődleges kulcs** melletti **Másolás** gombot. Ezzel kimásolja a kapcsolati sztringet a vágólapra a későbbi használathoz. Illessze be ezt az értéket a Jegyzettömbbe vagy egy másik ideiglenes helyre.
   
    ![connection-string][connection-string]

5. A későbbi használat érdekében ismételje meg az előző lépést, és másolja ki és illessze be az **Elsődleges kulcs** értékét egy ideiglenes helyre.  

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
