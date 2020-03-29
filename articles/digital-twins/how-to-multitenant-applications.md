---
title: Több-bérlős alkalmazások engedélyezése - Azure Digital Twins | Microsoft dokumentumok
description: Több-bérlős Azure Active Directory-alkalmazások konfigurálása az Azure Digital Twins számára.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 6e1321e01d8d12974a2704f4478b02a26c14142f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264933"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Több-bérlős alkalmazások engedélyezése az Azure Digital Twins segítségével

Az Azure Digital Twins-re épülő megoldások fejlesztői úgy találhatják, hogy egyetlen szolgáltatással vagy megoldással több ügyfelet szeretnének támogatni. Valójában *a több-bérlős* alkalmazások a leggyakoribb Azure Digital Twins konfigurációk közé tartoznak.

Ez a dokumentum bemutatja, hogyan konfigurálhat egy Azure Digital Twins alkalmazást több Azure Active Directory-bérlő és -ügyfél támogatására.

## <a name="multitenancy"></a>Több-bérlős

A *több-bérlős* erőforrás egyetlen kiépített példány, amely több ügyfelet támogat. Minden ügyfélnek saját független adatai és jogosultságai vannak. Minden ügyfél tapasztalata el van különítve egymástól, így az alkalmazás "nézete" eltérő.

Ha többet szeretne megtudni a többbérlős szolgáltatásról, olvassa el [a Több-bérlős alkalmazások az Azure-ban.](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications)

## <a name="problem-scenario"></a>Probléma forgatókönyv

Ebben a forgatókönyvben fontolja meg egy fejlesztő épület egy Azure Digital Twins megoldás (**DEVELOPER**) és egy ügyfél, aki használja ezt a megoldást **(CUSTOMER):**

- **A DEVELOPER** rendelkezik egy Azure-előfizetéssel egy Azure Active Directory-bérlővel.
- **A DEVELOPER** egy Azure Digital Twins-példányt telepít az Azure-előfizetésbe. Az Azure Active Directory automatikusan létrehozott egy egyszerű szolgáltatása **a DEVELOPER**Azure Active Directory-bérlőben.
- A **DEVELOPER's**Azure Active Directory-bérlőn belüli felhasználók ezután [oauth 2.0-s jogkivonatokat szerezhetnek](./security-authenticating-apis.md) be az Azure Digital Twins szolgáltatásból.
- **A DEVELOPER** most létrehoz egy mobilalkalmazást, amely közvetlenül integrálható az Azure Digital Twins Management API-kkal.
- **Developer** lehetővé teszi **az ügyfél** számára a mobil alkalmazás használatát.
- **Az ÜGYFÉL-nek** jogosultnak kell lennie az Azure Digital Twins Management API használatára a **DEVELOPER**alkalmazásában.

A probléma:

- Amikor **az ÜGYFÉL** bejelentkezik a **DEVELOPER**alkalmazásába, az alkalmazás nem szerezhet tokeneket az **ÜGYFÉL**felhasználói számára az Azure Digital Twins Management API-kkal való hitelesítéshez.
- Egy kivétel t ad ki az Azure Active Directoryban, amely jelzi, hogy az Azure Digital Twins nem ismeri fel **a CUSTOMER**címtárban.

## <a name="problem-solution"></a>Problémamegoldás

Az előző problémaforgatókönyv megoldásához a következő műveletekre van szükség az Azure Digital Twins szolgáltatásnév létrehozásához az **ÜGYFÉL**Azure Active Directory-bérlőn belül:

- Ha **az ÜGYFÉL** még nem rendelkezik Azure-előfizetéssel egy Azure Active Directory-bérlővel:

  - **A CUSTOMER**Azure Active Directory-bérlői rendszergazdájának be kell szereznie egy [felosztó-kiosztó Azure-előfizetést.](https://azure.microsoft.com/offers/ms-azr-0003p/)
  - **Az ÜGYFÉL**Azure Active Directory-bérlői rendszergazdájának ezután össze kell [kapcsolnia a bérlőt az új előfizetéssel.](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)

- Az [Azure Portalon](https://portal.azure.com)a **CUSTOMER's**Azure Active Directory bérlői rendszergazdája a következő lépéseket teszi:

  1. Az **Előfizetések** keresése a legfelső Azure-keresőmezőben. Válassza az **Előfizetések** lehetőséget.
  1. Válassza ki azt az előfizetést, amely rendelkezik a **DEVELOPER**alkalmazásában használandó Azure Active Directory-bérlővel.

     [![Azure Active Directory-előfizetések](media/multitenant/ad-subscriptions.png)](media/multitenant/ad-subscriptions.png#lightbox)

  1. Válassza ki **az erőforrás-szolgáltatókat**.
  1. Keressen a **Microsoft.IoTSpaces**.
  1. Kattintson a **Register** (Regisztrálás) elemre.

     [![Azure Active Directory-erőforrás-szolgáltatók](media/multitenant/ad-resource-providers.png)](media/multitenant/ad-resource-providers.png#lightbox)
  
## <a name="next-steps"></a>További lépések

- Ha többet szeretne tudni arról, hogyan használhatja a felhasználó által definiált függvényeket az Azure Digital Twins szolgáltatással, olvassa el [az Azure Digital Twins felhasználó által definiált függvények létrehozása](./how-to-user-defined-functions.md)című olvasni.

- A szerepköralapú hozzáférés-vezérlés használatával tovább biztosíthatja az alkalmazást szerepkör-hozzárendelésekkel, olvassa el [az Azure Digital Twins szerepköralapú hozzáférés-vezérlés létrehozása és kezelése](./security-create-manage-role-assignments.md)című útmutatót.
