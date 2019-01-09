---
title: Több-bérlős alkalmazások az Azure digitális Twins |} A Microsoft Docs
description: Hogyan konfigurálható az Azure digitális Twins több-bérlős Azure Active Directory-alkalmazások.
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2019
ms.author: mavoge
ms.openlocfilehash: 2b4f9bf87122f047e496dca1dbd425db8ad7c16c
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119968"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Több-bérlős alkalmazásokban az Azure digitális Twins engedélyezése

Megoldások az Azure digitális Twins kialakító fejlesztők tapasztalhatja, hogy szeretné-e egy egyetlen szolgáltatást vagy a megoldás több ügyfél támogatására. Valójában a *több-bérlős* alkalmazások az Azure digitális Twins leggyakoribb konfigurációk közé tartoznak.

Ez a dokumentum ismerteti az Azure digitális Twins alkalmazás támogatja a több Azure Active Directory-bérlők és felhasználók konfigurálása.

## <a name="multitenancy"></a>Több-bérlős módhoz

A *több-bérlős* erőforrás egyetlen kiépített példányt, amely támogatja a több ügyfél. Minden ügyfél saját független adatokat és jogosultságokkal rendelkezik. Minden egyes felhasználói élmény el vannak különítve a többi összes úgy, hogy a "megtekintése" az alkalmazás különböző.

Több-bérlős módhoz kapcsolatos további információkért olvassa el [több-Bérlős alkalmazások az Azure-ban](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications).

## <a name="problem-scenario"></a>A probléma forgatókönyv

Ebben az esetben fontolja meg a fejlesztők az Azure digitális Twins megoldás összeállítása (**fejlesztői**) és a egy ügyfél, aki ezt a megoldást használja (**ügyfél**):

- **FEJLESZTŐI** egy Azure-előfizetést, az Azure Active Directory-bérlő tartozik.
- **FEJLESZTŐI** üzembe helyez egy Azure digitális Twins példányt az Azure-előfizetésében. Az Azure Active Directory automatikusan létrehozott egy szolgáltatást a fő **fejlesztői**az Azure Active Directory-bérlővel.
- Belüli felhasználók **fejlesztői**céljai az majd is az Azure Active Directory-bérlő [szerzi be a jogkivonatokat az OAuth 2.0](./security-authenticating-apis.md) az Azure digitális Twins szolgáltatástól.
- **FEJLESZTŐI** most hoz létre, egy mobilalkalmazás, amely közvetlenül integrálható az Azure digitális Twins felügyeleti API-kkal.
- **FEJLESZTŐI** lehetővé teszi, hogy **ügyfél** a mobilalkalmazás használata.
- **ÜGYFÉL** engedélyezni kell, hogy az Azure digitális Twins felügyeleti API-val belül **fejlesztői**az alkalmazást.

A problémát:

- Amikor **ügyfél** bejelentkezik **fejlesztői**az alkalmazást, az alkalmazás nem tudta beolvasni a tokenek számára **ügyfél**a felhasználóknak a hitelesítést az Azure digitális Twins felügyeleti API-kkal.
- Kivétel jelenik meg az Azure Active Directoryban jelzi, hogy az Azure digitális Twins belül nem felismert **ügyfél**a könyvtárban.

## <a name="problem-solution"></a>A probléma megoldáshoz

Az előző forgatókönyvben a probléma megoldásához, a következő műveletek szükségesek az Azure digitális Twins belül egyszerű szolgáltatás létrehozása a **ügyfél**az Azure Active Directory-bérlő:

- Ha **ügyfél** még nem rendelkezik Azure-előfizetés az Azure Active Directory-bérlő:

  - **ÜGYFÉL**az Azure Active Directory-bérlői rendszergazdai kell beszerezni egy [használatalapú Azure-előfizetés](https://azure.microsoft.com/offers/ms-azr-0003p/).
  - **ÜGYFÉL**céljai az Azure Active Directory-bérlő rendszergazdája, akkor kell [hivatkozás az új előfizetés-bérlőjének](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity).

- Az a [az Azure portal](https://portal.azure.com), **ügyfél**az Azure Active Directory-bérlői rendszergazdai vesz igénybe az alábbi lépéseket:

  1. Nyissa meg **előfizetések**.
  1. Válassza ki az előfizetést, amely rendelkezik az Azure Active Directory-bérlő használandó **fejlesztői**az alkalmazást.

     ![Az Azure Active Directory-előfizetések][1]

  1. Válassza ki **erőforrás-szolgáltatók**.
  1. Keresse meg **Microsoft.IoTSpaces**.
  1. Kattintson a **Register** (Regisztrálás) elemre.

     ![Az Azure Active Directory erőforrás-szolgáltatók][2]
  
## <a name="next-steps"></a>További lépések

- Felhasználó által definiált függvények használata az Azure digitális Twins kapcsolatos további információkért olvassa el [létrehozása az Azure digitális Twins felhasználó által definiált függvények](./how-to-user-defined-functions.md).

- Szerepköralapú hozzáférés-vezérlés használata az alkalmazást a szerepkör-hozzárendelések további védelméhez, olvassa el [létrehozása és kezelése az Azure digitális Twins szerepköralapú hozzáférés-vezérlés](./security-create-manage-role-assignments.md).

<!-- Images -->
[1]: media/multitenant/ad-subscriptions.png
[2]: media/multitenant/ad-resource-providers.png
