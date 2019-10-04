---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 3e3617f68d58e51f24affaae7d69812195b72fd0
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71210238"
---
1. Jelentkezzen be az [Azure Portalra][Azure portal].
1. Válassza ki **erőforrás létrehozása**. Ezt követően válassza az **Integration** > **Relay** lehetőséget. Ha nem látja a **Relay** elemet a listában, válassza az **Összes megjelenítése** lehetőséget a jobb felső sarokban.
1. Válassza a **Létrehozás**lehetőséget, majd adja meg a névtér nevét a **név** mezőben. Azure Portal ellenőrzi, hogy a név elérhető-e.
1. Válassza ki azt az Azure-előfizetést, amelyben létre kívánja hozni a névteret.
1. Az [erőforráscsoport](../articles/azure-resource-manager/manage-resource-groups-portal.md)mezőben válasszon ki egy meglévő erőforráscsoportot, amelyben a névteret helyezi, vagy hozzon létre egy újat.  
1. Válassza ki azt az országot vagy régiót, amelyben a névteret üzemeltetni szeretné.

    ![Névtér létrehozása][create-namespace]

1. Kattintson a **Létrehozás** gombra. A Azure Portal létrehozza a névteret, és engedélyezi azt. Néhány perc múltán a rendszer kiosztja az erőforrásokat a fiókja számára.

### <a name="get-management-credentials"></a>Felügyeleti hitelesítő adatok lekérése

1. Válassza a **minden erőforrás**lehetőséget, majd válassza ki az újonnan létrehozott névtér nevét.
1. Válassza a **megosztott hozzáférési szabályzatok**lehetőséget.  
1. A **Megosztott elérési szabályzatok** alatt válassza ki a **RootManageSharedAccessKey** elemet.
1. Az **sas-szabályzat alatt: RootManageSharedAccessKey**válassza a **Másolás** gombot az **elsődleges kapcsolódási karakterlánc**mellett. Ezzel a művelettel a vágólapra másolja a kapcsolati karakterláncot későbbi használatra. Illessze be ezt az értéket a Jegyzettömbbe vagy egy másik ideiglenes helyre.
1. A későbbi használat érdekében ismételje meg az előző lépést, és másolja ki és illessze be az **Elsődleges kulcs** értékét egy ideiglenes helyre.  

    ![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com
