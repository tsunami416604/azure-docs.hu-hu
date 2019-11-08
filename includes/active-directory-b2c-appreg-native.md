---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 0b5c5fdddeea961858a2c3b8cd69c365bb28f26e
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799879"
---
Az alkalmazások Azure AD B2C-bérlőben való regisztrálásához használhatja a jelenlegi **alkalmazásokat** , vagy az új, egyesített **Alkalmazásregisztrációk (előzetes verzió)** élményt. [További információ az új felületről](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Alkalmazások](#tab/applications/)

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C**lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C**lehetőséget.
1. Válassza az **alkalmazások**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.
1. Adja meg az alkalmazás nevét. Például: *nativeapp1*.
1. **Natív ügyfél**esetén válassza az **Igen**lehetőséget.
1. **Egyéni átirányítási URI** -t adjon meg egyedi sémával. Például: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Az átirányítási URI kiválasztásakor két fontos szempontot kell figyelembe venni:
    * **Egyedi**: az átirányítási URI sémájának minden alkalmazás esetében egyedinek kell lennie. A példában a `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect``com.onmicrosoft.contosob2c.exampleapp` a séma. Ezt a mintát kell követni. Ha két alkalmazás ugyanazt a sémát használja, a felhasználó választhat egy alkalmazást. Ha a felhasználó helytelenül választ, a bejelentkezés sikertelen lesz.
    * **Befejezés**: az átirányítási URI-nak egy sémával és egy útvonallal kell rendelkeznie. Az elérési útnak legalább egy perjelet tartalmaznia kell a tartomány után. `//oauth/` működik például, amikor `//oauth` meghibásodik. Az URI-ban ne szerepeljenek speciális karakterek, például aláhúzások.
1. Kattintson a **Létrehozás** gombra.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C**lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C**lehetőséget.
1. Válassza a **Alkalmazásregisztrációk (előzetes verzió)** lehetőséget, majd válassza az **új regisztráció**lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: *nativeapp1*.
1. A **támogatott fiókok típusai**területen válassza a **fiókok lehetőséget bármely szervezeti címtárban vagy bármely identitás-szolgáltatóban**.
1. Az **átirányítási URI**alatt válassza a legördülő menüből a **nyilvános ügyfél/natív (mobil & Desktop)** lehetőséget.
1. Adjon meg egy egyedi sémával rendelkező átirányítási URI-t. Például: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Az átirányítási URI kiválasztásakor két fontos szempontot kell figyelembe venni:
    * **Egyedi**: az átirányítási URI sémájának minden alkalmazás esetében egyedinek kell lennie. A példában a `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect``com.onmicrosoft.contosob2c.exampleapp` a séma. Ezt a mintát kell követni. Ha két alkalmazás ugyanazt a sémát használja, a felhasználó választhat egy alkalmazást. Ha a felhasználó helytelenül választ, a bejelentkezés sikertelen lesz.
    * **Befejezés**: az átirányítási URI-nak egy sémával és egy útvonallal kell rendelkeznie. Az elérési útnak legalább egy perjelet tartalmaznia kell a tartomány után. `//oauth/` működik például, amikor `//oauth` meghibásodik. Az URI-ban ne szerepeljenek speciális karakterek, például aláhúzások.
1. Az **engedélyek**területen jelölje be a *rendszergazdai jóváhagyás megadása az OpenID és a offline_access engedélyek számára* jelölőnégyzetet.
1. Kattintson a **Register** (Regisztrálás) elemre.