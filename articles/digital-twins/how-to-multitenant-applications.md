---
title: Több-bérlős alkalmazások engedélyezése – Azure digitális Twins | Microsoft Docs
description: Több-bérlős Azure Active Directory alkalmazások konfigurálása Azure digitális Twins-hoz.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 65e1fa3fe371766566eeeaaa2d33479ea0243d61
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383328"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Több-bérlős alkalmazások engedélyezése az Azure Digital Twins segítségével

Az Azure Digital Twins-ra épülő megoldások fejlesztői úgy találhatják, hogy több ügyfelet szeretnének támogatni egyetlen szolgáltatással vagy megoldással. Valójában a több- *bérlős* alkalmazások a leggyakoribb Azure-beli digitális Twins-konfigurációk.

Ez a dokumentum azt ismerteti, hogyan konfigurálható egy Azure digitális Twins-alkalmazás több Azure Active Directory bérlő és ügyfél támogatásához.

## <a name="multitenancy"></a>Bérlős

A több- *bérlős* erőforrás egyetlen kiosztott példány, amely több ügyfelet is támogat. Minden ügyfél saját független adattal és jogosultságokkal rendelkezik. Minden ügyfél felhasználói felülete el van különítve egymástól, így az alkalmazás "nézete" különbözik.

Ha többet szeretne megtudni a bérlős, olvassa el [a több-bérlős alkalmazások az Azure-ban](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications)című témakört.

## <a name="problem-scenario"></a>Probléma forgatókönyve

Ebben az esetben Vegyünk egy olyan fejlesztőt, aki egy Azure digitális Twins-megoldást (**fejlesztőt**) és egy olyan ügyfelet használ, aki ezt a megoldást használja (**ügyfél**):

- A **fejlesztő** Azure-előfizetéssel rendelkezik Azure Active Directory Bérlővel.
- A **fejlesztő** üzembe helyez egy Azure digitális Twins-példányt az Azure-előfizetésében. Azure Active Directory automatikusan létrehozott egy egyszerű szolgáltatást a **fejlesztői**Azure Active Directory-bérlőben.
- A **fejlesztői**Azure Active Directory bérlőn belüli felhasználók ezután [OAuth 2,0 jogkivonatokat tudnak beszerezni](./security-authenticating-apis.md) az Azure Digital Twins szolgáltatásból.
- A **fejlesztői** most létrehoz egy olyan Mobile-alkalmazást, amely közvetlenül integrálódik az Azure digitális Twins felügyeleti API-kkal.
- A **fejlesztő** lehetővé teszi, hogy a **felhasználó** használhassa a mobil alkalmazást.
- Az **ügyfélnek** engedélyeznie kell az Azure Digital Twins Management API használatát a **fejlesztői**alkalmazáson belül.

A probléma:

- Amikor az **ügyfél** bejelentkezik a **fejlesztői**alkalmazásba, az alkalmazás nem tud jogkivonatokat beszerezni az **ügyfél**felhasználói számára az Azure digitális Twins felügyeleti API-kkal való hitelesítéshez.
- Kivételt jelent a Azure Active Directory, amely azt jelzi, hogy az Azure digitális Twins nem ismerhető fel az **ügyfél**címtárában.

## <a name="problem-solution"></a>Probléma megoldása

Az előző probléma megoldásához a következő műveletek szükségesek ahhoz, hogy Azure Digital Twins-szolgáltatást hozzon létre az **ügyfél**Azure Active Directory bérlőn belül:

- Ha az **ügyfél** még nem rendelkezik Azure-előfizetéssel Azure Active Directory Bérlővel:

  - Az **ügyfél**Azure Active Directory bérlői rendszergazdájának utólagos elszámolású [Azure-előfizetést kell befizetnie](https://azure.microsoft.com/offers/ms-azr-0003p/).
  - Az **ügyfél**Azure Active Directory bérlői rendszergazdájának [az új előfizetéssel kell összekapcsolnia a bérlőt](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity).

- A [Azure Portal](https://portal.azure.com) **ügyfél**Azure Active Directory bérlői rendszergazdája a következő lépéseket hajtja végre:

  1. Az **előfizetések** keresése a felső Azure Search mezőben. Válassza az **Előfizetések** lehetőséget.
  1. Válassza ki azt az előfizetést, amely a **fejlesztői**alkalmazásban használandó Azure Active Directory Bérlővel rendelkezik.

     [![Azure Active Directory előfizetések](media/multitenant/ad-subscriptions.png)](media/multitenant/ad-subscriptions.png#lightbox)

  1. Válassza az **erőforrás-szolgáltatók**lehetőséget.
  1. Keressen rá a **Microsoft. IoTSpaces**kifejezésre.
  1. Kattintson a **Register** (Regisztrálás) elemre.

     [Azure Active Directory erőforrás-szolgáltatók ![](media/multitenant/ad-resource-providers.png)](media/multitenant/ad-resource-providers.png#lightbox)
  
## <a name="next-steps"></a>Következő lépések

- Ha szeretne többet megtudni arról, hogyan használhatja a felhasználó által definiált függvényeket az Azure Digital Twins szolgáltatással, olvassa el az [Azure Digital Twins felhasználói függvények létrehozása](./how-to-user-defined-functions.md)című témakört.

- Ha szeretné megtudni, hogyan használhatja a szerepköralapú hozzáférés-vezérlést az alkalmazás szerepkör-hozzárendelésekkel való további biztonságossá tételéhez, olvassa el az [Azure digitális Twins szerepköralapú hozzáférés-vezérlés létrehozása és kezelése](./security-create-manage-role-assignments.md)című témakört.
