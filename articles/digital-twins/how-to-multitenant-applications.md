---
title: Több-bérlős alkalmazásokban az Azure digitális Twins engedélyezése |} A Microsoft Docs
description: Az ügyfelek az Azure Active Directory-bérlők regisztrálása az Azure digitális Ikerállapotának megismerése
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: mavoge
ms.openlocfilehash: e465ab95b670268f8ef31472259783fce8f24b36
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324273"
---
# <a name="how-to-enable-multitenant-applications-with-azure-digital-twins"></a>Több-bérlős alkalmazásokban az Azure digitális Twins engedélyezése

Az Azure digitális Twins platform segítségével a fejlesztők számára a több-bérlős alkalmazások célszerű. A *több-bérlős alkalmazás* egy üzembe helyezett példányban több minden rendelkező ügyfelek a saját független adatokat és a jogosultságok támogatásához használt.

Ez a dokumentum részletesen bemutatja több Azure Active Directory (AD) bérlőt és ügyfelek támogatása az Azure digitális Twins több-bérlős alkalmazásokat hozhat létre.

## <a name="scenario-summary"></a>A forgatókönyv összegzése

Ebben az esetben fontolja meg a fejlesztői D és ügyfél-C:

- D fejlesztői az Azure AD-bérlő Azure-előfizetéssel rendelkezik.
- Fejlesztői D alkalmazva van egy digitális Twins-példányt az Azure-előfizetésében.
- Fejlesztői D Azure AD-bérlő felhasználóit képesek a jogkivonatok lekérésére digitális Twins össze az Azure ad-ben van létrehozása óta egy szolgáltatás egyszerű fejlesztői D az Azure AD-bérlőben.
- Fejlesztői D létrehozza egy mobilalkalmazást közvetlenül integrálható a digitális Twins felügyeleti API-val.
- Fejlesztői D majd engedélyezése C ügyfél a mobilalkalmazást.
- Ügyfél C most kell azt, hogy a digitális Twins felügyeleti API-val fejlesztői D alkalmazáson belül.

  > [!IMPORTANT]
  > - Amikor ügyfél C fejlesztői D alkalmazás bejelentkezik, az alkalmazás nem tudják lekérni a jogkivonatokat kommunikáljon a felügyeleti API-t C ügyfél felhasználók számára.
  > - Az Azure AD kivételt fogja kijelezni egy hibaüzenet, hogy digitális Twins ügyfél C címtáron belül nem ismeri.

## <a name="solution"></a>Megoldás

A fenti forgatókönyv megoldani, a következő műveletek egy digitális Twins ügyfél C az Azure AD-bérlőn belül szolgáltatásnév létrehozásához van szükség:

- Ha ügyfél C még nem rendelkezik Azure-előfizetés az Azure AD-bérlő:

  - A C ügyfél Azure AD-bérlő rendszergazdája kell beszerezni egy [használatalapú Azure-előfizetés](https://azure.microsoft.com/offers/ms-azr-0003p/).
  - A C ügyfél Azure AD-bérlő rendszergazdája kell majd [hivatkozás az új előfizetés-bérlőjének](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

- Az a [az Azure Portal](https://portal.azure.com), az Azure AD-bérlő felügyeleti ügyfél C kell majd:
  1. Nyissa meg **előfizetések**.
  1. Válassza ki az előfizetést, amelyhez az Azure AD-bérlő fejlesztői D alkalmazás használatban van.
  1. Válassza ki **erőforrás-szolgáltatók**.
  1. Keresse meg **Microsoft.IoTSpaces**.
  1. Kattintson a **regisztrálása**.
  
## <a name="next-steps"></a>További lépések

Az Azure digitális Twins felhasználó által definiált függvények használatával kapcsolatos további tudnivalókért olvassa el [Azure digitális Twins UDF-EK](how-to-user-defined-functions.md).

Szerepköralapú hozzáférés-vezérlés használata az alkalmazást a szerepkör-hozzárendelések további védelméhez, olvassa el [digitális Twins szerepköralapú hozzáférés-vezérlés](security-create-manage-role-assignments.md).
