---
title: Azure Migrate előkészítése ISV-eszközzel vagy-mozgatóval való együttműködéshez
description: Ez a cikk azt ismerteti, hogyan lehet előkészíteni a Azure Migratet egy ISV-eszközzel vagy-mozgatóval való együttműködésre, majd az eszköz használatának megkezdésére.
ms.topic: how-to
ms.date: 05/07/2020
ms.openlocfilehash: 9513e783d4f9d7be83f1434d4dd9011844af8993
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682648"
---
# <a name="prepare-to-work-with-an-isv-tool-or-movere"></a>Felkészülés az ISV-eszköz vagy a mozgató tevékenységre

Ha [ISV-eszközt](migrate-services-overview.md#isv-integration) adott hozzá, vagy egy Azure Migrate projektre mutat, néhány lépést követnie kell, mielőtt összekapcsolja az eszközt, és nem küldi el az adatAzure Migratenak. 

## <a name="check-azure-ad-permissions"></a>Azure AD-engedélyek keresése

Az Azure-beli felhasználói fióknak a következő engedélyekkel kell rendelkeznie:

- Engedély Azure Active Directory (Azure AD) alkalmazás Azure-Bérlővel való regisztrálásához
- Jogosultság a szerepkör az Azure AD-alkalmazáshoz való kiosztására az előfizetés szintjén


### <a name="set-permissions-to-register-an-azure-ad-app"></a>Engedélyek beállítása Azure AD-alkalmazás regisztrálásához

1. Az Azure AD-ben keresse meg a fiókja szerepkört.
2. Ha rendelkezik a felhasználói szerepkörrel, válassza a bal oldali **felhasználói beállítások** elemet, és ellenőrizze, hogy a felhasználók regisztrálhatnak-e alkalmazásokat. Ha az **Igen**értékre van állítva, akkor az Azure ad-bérlő bármely felhasználója regisztrálhat egy alkalmazást. Ha a **nem**értékre van állítva, akkor csak a rendszergazda felhasználók regisztrálhatják az alkalmazásokat.   
3. Ha nincs engedélye, a rendszergazda felhasználó megadhatja a felhasználói fiókját az [alkalmazás-rendszergazdai](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-administrator) szerepkörrel, így regisztrálhatja az alkalmazást.
4. Az eszköz Azure Migratehoz való csatolása után a rendszergazda el tudja távolítani a szerepkört a fiókból.

### <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>Engedélyek beállítása egy szerepkör hozzárendeléséhez egy Azure AD-alkalmazáshoz
 
Az Azure-előfizetésében a fióknak **Microsoft. Authorization/*/Write** hozzáférésre van szüksége ahhoz, hogy szerepkört rendeljen egy Azure ad-alkalmazáshoz. 

1. Az Azure Portalon nyissa meg az **Előfizetések** oldalt.
2. Válassza ki az adott előfizetést. Ha nem látja, válassza a **globális előfizetések szűrőt**. 
3. Válassza **a saját engedélyek**lehetőséget. Ezután **kattintson ide az előfizetés teljes hozzáférés részleteinek megtekintéséhez**.
4. A **szerepkör-hozzárendelések**  >  **nézetben**tekintse át az engedélyeket. Ha a fiókja nem rendelkezik jogosultságokkal, kérje meg az előfizetés rendszergazdáját, hogy vegye fel a [felhasználót a felhasználói hozzáférés rendszergazdai](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) szerepkörbe vagy a [tulajdonos](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) szerepkörbe.
 

## <a name="start-using-the-tool"></a>Az eszköz használatának megkezdése

1. Ha még nem rendelkezik licenccel vagy ingyenes próbaverzióval az eszközhöz, a Azure Migrate eszköz bejegyzésében, a **regisztráció**területen válassza a **továbbiak**lehetőséget.
2. Az eszközben kövesse az utasításokat az eszközről a Azure Migrate projekthez való hivatkozáshoz, és az adatok Azure Migrateba való küldéséhez.

## <a name="next-steps"></a>További lépések

Kövesse az ISV vagy a mozgató utasításait az adatok Azure Migrate való elküldéséhez.

   
