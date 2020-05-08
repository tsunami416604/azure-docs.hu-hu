---
title: Az Azure Migrate előkészítése az ISV-eszközök és a mozgatói működéséhez
description: Az Azure Migrate előkészítése az ISV-eszközök és a mozgatói működéséhez
ms.topic: how-to
ms.date: 05/07/2020
ms.openlocfilehash: b2ac95743e97bce41f38a4078e5e38f6b1bf7cd9
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2020
ms.locfileid: "82906981"
---
# <a name="prepare-to-work-with-isv-toolsmovere"></a>Felkészülés az ISV-eszközök és a mozgató működésére

Ha egy [ISV-eszközt](migrate-services-overview.md#isv-integration)vagy egy Azure Migrate-projektet adott hozzá, néhány lépést kell elkészítenie az eszköz csatolása előtt, és el kell küldenie az adatAzure Migratenak. 

## <a name="check-azure-ad-permissions"></a>Azure AD-engedélyek keresése

Az Azure-beli felhasználói fióknak néhány engedélyre van szüksége:

- Engedély az Azure AD-alkalmazások Azure-Bérlővel való regisztrálásához.
- Engedély a szerepkör az Azure AD-alkalmazáshoz való lefoglalásához az előfizetés szintjén.


## <a name="set-permissions-to-register-an-azure-ad-app"></a>Engedélyek beállítása Azure AD-alkalmazás regisztrálásához

1. A Azure Active Directoryban keresse meg a fiókja szerepkört. Ha rendelkezik a felhasználói szerepkörrel, kattintson a bal oldali **felhasználói beállítások** elemre, és ellenőrizze, hogy a felhasználók regisztrálhatnak-e alkalmazásokat.
2. Ha az **Igen**értékre van állítva, akkor az Azure ad-bérlő bármely felhasználója regisztrálhat egy alkalmazást. Ha nem, akkor csak a rendszergazda felhasználók regisztrálhatják az alkalmazásokat.
3. Ha nincs engedélye, a rendszergazda felhasználó megadhatja a felhasználói fiókját az [alkalmazás-rendszergazdai](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-administrator) szerepkörrel, így regisztrálhatja az alkalmazást.
4. Az eszköz Azure Migratehoz való csatolása után a rendszergazda el tudja távolítani a szerepkört a fiókból.

## <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>Engedélyek beállítása egy szerepkör hozzárendeléséhez egy Azure AD-alkalmazáshoz
 
Az Azure-előfizetésében a fióknak **Microsoft. Authorization/*/Write hozzáférésre**van szüksége ahhoz, hogy szerepkört rendeljen egy Azure ad-alkalmazáshoz. 

1. Az előfizetési engedélyek ellenőrzését a Azure Portalban nyissa meg az **előfizetések**lehetőséget.
2. Válassza ki az adott előfizetést. Ha nem látja, válassza a **globális előfizetések szűrőt**. 
3. Válassza **a saját engedélyek**lehetőséget. Ezután **kattintson ide az előfizetés teljes hozzáférés részleteinek megtekintéséhez**.
4. A **szerepkör-hozzárendelések** > **nézetben**tekintse meg az engedélyeket.
5. Ha a fiókja nem rendelkezik jogosultságokkal, kérje meg az előfizetés rendszergazdáját, hogy vegye fel a [felhasználót a felhasználói hozzáférés rendszergazdai](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) szerepkörbe vagy a [tulajdonos](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) szerepkörbe.
 

## <a name="start-using-the-tool"></a>Az eszköz használatának megkezdése

1. Ha még nem rendelkezik licenccel vagy ingyenes próbaverzióval az eszközhöz, a Azure Migrate eszköz bejegyzésében, a **regisztráció**területen kattintson a **További információ**lehetőségre.
2. Az eszközön kövesse az utasításokat, hogy az eszközről a Azure Migrate projektre hivatkozzon, és az adatok elküldése a Azure Migrate.

## <a name="next-steps"></a>További lépések

Az ISV vagy a mozgató utasításokat követve küldje el az adatküldés Azure Migrate.

   
