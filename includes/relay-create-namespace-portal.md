---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 500db9f6d299ea81b1f1b1be864df5d91ba1eae7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76021318"
---
1. Jelentkezzen be az [Azure Portalra.][Azure portal]
1. Válassza az **Erőforrás létrehozása** lehetőséget. Ezután válassza **az Integrációs** > **továbbító lehetőséget.** Ha nem látja a **Relay** elemet a listában, válassza az **Összes megjelenítése** lehetőséget a jobb felső sarokban.
1. Válassza **a Létrehozás**lehetőséget, és írjon be egy névtérnevet a **Név** mezőbe. Az Azure Portal ellenőrzi, hogy a név elérhető-e.
1. Válasszon egy Azure-előfizetést, amelyben létre szeretné hozni a névteret.
1. Az [Erőforráscsoport csoport](../articles/azure-resource-manager/management/manage-resource-groups-portal.md)ban válasszon egy meglévő erőforráscsoportot, amelybe a névteret el szeretné helyezni, vagy hozzon létre egy újat.  
1. Válassza ki azt az országot vagy régiót, ahol a névteret üzemeltetni kell.

    ![Névtér létrehozása][create-namespace]

1. Kattintson a **Létrehozás** gombra. Az Azure Portal létrehozza a névteret, és engedélyezi azt. Néhány perc múltán a rendszer kiosztja az erőforrásokat a fiókja számára.

### <a name="get-management-credentials"></a>Felügyeleti hitelesítő adatok lekérése

1. Válassza **az Összes erőforrás**lehetőséget, majd válassza ki az újonnan létrehozott névtér nevét.
1. Válassza **a Megosztott hozzáférési házirendek lehetőséget.**  
1. A **Megosztott elérési szabályzatok** alatt válassza ki a **RootManageSharedAccessKey** elemet.
1. A **SAS-házirend: RootManageSharedAccessKey csoportban**válassza az Elsődleges kapcsolati karakterlánc melletti **Másolás** **gombot.** Ez a művelet a vágólapra másolja a kapcsolati karakterláncot későbbi használatra. Illessze be ezt az értéket a Jegyzettömbbe vagy egy másik ideiglenes helyre.
1. A későbbi használat érdekében ismételje meg az előző lépést, és másolja ki és illessze be az **Elsődleges kulcs** értékét egy ideiglenes helyre.  

    ![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com
