---
title: Több-bérlős alkalmazások az Azure digitális Twins |} A Microsoft Docs
description: Az ügyfelek az Azure Active Directory-bérlők regisztrálása az Azure digitális Ikerállapotának megismerése
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: mavoge
ms.openlocfilehash: a2d9ece119003c341f49ee03d735d5636b179a32
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51259887"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Több-bérlős alkalmazásokban az Azure digitális Twins engedélyezése

Az Azure digitális Twins általában használó fejlesztők számára létre szeretné hozni a több-bérlős alkalmazásokban. A *több-bérlős alkalmazás* egyetlen kiépített példányt, amely támogatja a több ügyfél van. Minden ügyfél saját független adatokat és jogosultságokkal rendelkezik.

Ez a dokumentum részletesen, amely támogatja a több Azure Active Directory (Azure AD) bérlőt és ügyfelei az Azure digitális Twins több-bérlős alkalmazás létrehozása.

## <a name="scenario-summary"></a>A forgatókönyv összegzése

Ebben az esetben fontolja meg a fejlesztői D és ügyfél-C:

- D fejlesztői az Azure AD-bérlő Azure-előfizetéssel rendelkezik.
- Fejlesztői D üzembe helyezi az Azure digitális Twins be Azure-előfizetésében.
- Fejlesztői D az Azure AD-bérlőn belüli felhasználók képesek a jogkivonatok lekérésére össze az Azure digitális Twins, mert az Azure ad-ben létrehozott egy szolgáltatás egyszerű fejlesztői D az Azure AD-bérlőben.
- Fejlesztői D létrehozza egy mobilalkalmazást közvetlenül integrálható az Azure digitális Twins felügyeleti API-val.
- Fejlesztői D engedélyezése C ügyfél a mobilalkalmazást.
- Ügyfél C engedéllyel kell rendelkeznie az Azure digitális Twins felügyeleti API fejlesztői D alkalmazáson belül.

  > [!IMPORTANT]
  > - Amikor ügyfél C fejlesztői D alkalmazás bejelentkezik, az alkalmazás nem tudta beolvasni a jogkivonatok kommunikáljon a felügyeleti API-t C ügyfél felhasználók számára.
  > - Az Azure AD jelez hibát, amely azt jelzi, hogy az Azure digitális Twins ügyfél C címtáron belül nem ismeri.

## <a name="solution"></a>Megoldás

Az előző forgatókönyvben megoldani, a következő műveleteket hozhat létre az Azure digitális Twins egyszerű szolgáltatás ügyfél C az Azure AD-bérlőn belül van szükség:

- Ha ügyfél C még nem rendelkezik Azure-előfizetés az Azure AD-bérlő:

  - A C ügyfél az Azure AD-bérlői rendszergazdai kell beszerezni egy [használatalapú Azure-előfizetés](https://azure.microsoft.com/offers/ms-azr-0003p/).
  - C az ügyfél az Azure AD-bérlő rendszergazdája ezután kell [hivatkozás az új előfizetés-bérlőjének](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

- Az a [az Azure portal](https://portal.azure.com), az Azure AD-bérlői rendszergazdai felhasználói C vesz igénybe az alábbi lépéseket:

  1. Nyissa meg **előfizetések**.
  1. Válassza ki az előfizetést, amelyhez az Azure AD-bérlő fejlesztői D alkalmazás használatban van.
  1. Válassza ki **erőforrás-szolgáltatók**.
  1. Keresse meg **Microsoft.IoTSpaces**.
  1. Kattintson a **Register** (Regisztrálás) elemre.
  
## <a name="next-steps"></a>További lépések

Felhasználó által definiált függvények használata az Azure digitális Twins kapcsolatos további információkért olvassa el [Azure digitális Twins UDF-EK](how-to-user-defined-functions.md).

Szerepköralapú hozzáférés-vezérlés használata az alkalmazást a szerepkör-hozzárendelések további védelméhez, olvassa el [Azure digitális Twins szerepköralapú hozzáférés-vezérlés](security-create-manage-role-assignments.md).
