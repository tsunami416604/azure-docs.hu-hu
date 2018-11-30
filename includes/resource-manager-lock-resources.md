---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/25/2018
ms.author: tomfitz
ms.openlocfilehash: 03e4053b65cf39101e8cb5d35ce439a759ec11d6
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440212"
---
1. Az erőforrás, erőforráscsoport vagy előfizetés, amely a zárolni kívánt beállítási paneljén válassza **zárolja**.
   
      ![Válassza ki a zárolás](./media/resource-manager-lock-resources/select-lock.png)
2. Egy zárolás hozzáadásához válassza **Hozzáadás**. Ha szeretne létrehozni egy zárolása egy szülő szintjén, válassza ki a szülő. A jelenleg kijelölt erőforráshoz örökli a szülő a zárolást. Ha például lehetett zárolni az erőforráscsoport, a zárolás alkalmazandó az ahhoz tartozó összes erőforrást.
   
      ![zárolás felvétele](./media/resource-manager-lock-resources/add-lock.png) 
3. Adjon meg egy nevet és a zárolási szint a zárolást. Ha szeretné a zárolás leíró megjegyzések is hozzáadhat.
   
      ![set zárolása](./media/resource-manager-lock-resources/set-lock.png) 
4. A zárolás törléséhez válassza ki a három pontra, és **törlése** az elérhető lehetőségek közül.
   
      ![Törlési zárolást](./media/resource-manager-lock-resources/delete-lock.png) 

