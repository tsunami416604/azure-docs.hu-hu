---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: bbcf38ab54632144920b729f433c222f426d96dc
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56825553"
---
1. Jelentkezzen be az [Azure Portalra][Azure portal].
2. A bal oldali menüben válassza az **+ Erőforrás létrehozása** elemet. Ezt követően válassza az **Integration** > **Relay** lehetőséget. Ha nem látja a **Relay** elemet a listában, válassza az **Összes megjelenítése** lehetőséget a jobb felső sarokban. 
3. A **Névtér létrehozása** alatt adja meg a névtér nevét. A rendszer azonnal ellenőrzi, hogy a név elérhető-e.
4. Az **Előfizetés** mezőben válassza ki azt az Azure-előfizetést, amelyben a névteret létre kívánja hozni.
5. Az [Erőforráscsoport](../articles/azure-resource-manager/manage-resource-groups-portal.md) mezőben válasszon ki egy meglévő erőforráscsoportot, amelybe a névteret helyezi, vagy hozzon létre egy újat.  
6. A **Hely** mezőben válassza ki azt az országot vagy régiót, amelyben a névtér üzemeltetve lesz.
   
    ![Névtér létrehozása][create-namespace]
7. Kattintson a **Létrehozás** gombra. A rendszer létrehozza, majd engedélyezi a névteret. Néhány perc múltán a rendszer kiosztja az erőforrásokat a fiókja számára.

### <a name="get-management-credentials"></a>Felügyeleti hitelesítő adatok lekérése

1. Válassza ki az **Összes erőforrás** elemet, majd az újonnan létrehozott névtér nevét.
2. A Relay-névtér alatt válassza ki a **Megosztott elérési szabályzatok** elemet.  
3. A **Megosztott elérési szabályzatok** alatt válassza ki a **RootManageSharedAccessKey** elemet.
   
    ![connection-info][connection-info]
4. A **házirend: RootManageSharedAccessKey**, jelölje be a **másolási** megjelenítő gombra **kapcsolati karakterlánc – elsődleges kulcs**. Ezzel kimásolja a kapcsolati sztringet a vágólapra a későbbi használathoz. Illessze be ezt az értéket a Jegyzettömbbe vagy egy másik ideiglenes helyre.
   
    ![connection-string][connection-string]

5. A későbbi használat érdekében ismételje meg az előző lépést, és másolja ki és illessze be az **Elsődleges kulcs** értékét egy ideiglenes helyre.  

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
