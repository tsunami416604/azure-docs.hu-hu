---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/27/2019
ms.author: marsma
ms.openlocfilehash: b83a6b9185eb4ef127da1dd3b55aba4e8b2945f9
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326315"
---
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C**lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C**lehetőséget.
1. Válassza az **alkalmazások**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.
1. Adja meg az alkalmazás nevét. Például: *nativeapp1*.
1. **Natív ügyfél**esetén válassza az **Igen**lehetőséget.
1. **Egyéni átirányítási URI** -t adjon meg egyedi sémával. Például: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Az átirányítási URI kiválasztásakor két fontos szempontot kell figyelembe venni:
    1. **Egyedi**: Az átirányítási URI sémájának minden alkalmazás esetében egyedinek kell lennie. A példában `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` `com.onmicrosoft.contosob2c.exampleapp` a séma. Ezt a mintát kell követni. Ha két alkalmazás ugyanazt a sémát használja, a felhasználó választhat egy alkalmazást. Ha a felhasználó helytelenül választ, a bejelentkezés sikertelen lesz.
    1. **Befejezés**: Az átirányítási URI-nak egy sémával és egy útvonallal kell rendelkeznie. Az elérési útnak legalább egy perjelet tartalmaznia kell a tartomány után. A `//oauth/` például akkor működik, ha `//oauth` sikertelen. Az URI-ban ne szerepeljenek speciális karakterek, például aláhúzások.
1. Kattintson a **Létrehozás** gombra.
