---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 31d4b65c6cd6a3dfe3cb779022329f338436c69b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184349"
---
Ha regisztrálegy alkalmazást az Azure AD B2C-bérlőben, használhatja az aktuális **alkalmazások** vagy az új egyesített **alkalmazásregisztrációk (előzetes verzió)** használatát. [További információ az új felületről](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + Előfizetés** ikont a portál eszköztárán, majd válassza ki az Azure AD B2C-bérlőt tartalmazó könyvtárat.
1. Az Azure Portalon keresse meg és válassza az **Azure Active Directoryt.**
1. A **Kezelés csoportban**válassza **az Alkalmazásregisztrációk (Örökölt)** lehetőséget.
1. Válassza az **Új alkalmazás regisztrálása** elemet.
1. Adjon nevet az alkalmazásnak. Például *a managementapp1.*
1. Az **Alkalmazástípus mezőben**válassza a **Web app / API**lehetőséget.
1. Adjon meg érvényes **URL-címet a bejelentkezési URL-címben.** Például: `https://localhost`. A végpontnak nem kell elérhetőnek lennie, de érvényes URL-címnek kell lennie.
1. Kattintson a **Létrehozás** gombra.
1. Rögzítse a **Regisztrált alkalmazás** áttekintése lapon megjelenő **alkalmazásazonosítót.** Ezt az értéket egy későbbi lépésben használhatja.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + Előfizetés** ikont a portál eszköztárán, majd válassza ki az Azure AD B2C-bérlőt tartalmazó könyvtárat.
1. Az Azure Portalon keresse meg és válassza ki **az Azure AD B2C parancsot.**
1. Válassza **az Alkalmazásregisztrációk (Előzetes verzió)** lehetőséget, majd az **Új regisztráció**lehetőséget.
1. Adja meg az alkalmazás **nevét.** Például *a managementapp1.*
1. Csak **ebben a szervezeti címtárban**válassza a Fiókok lehetőséget.
1. Az **Engedélyek csoportban**törölje a jelet a *Rendszergazda engedélyének megadása a nyílt és offline_access engedélyek jelölőnégyzetből.*
1. Kattintson a **Register** (Regisztrálás) elemre.
1. Rögzítse az alkalmazás áttekintő lapján megjelenő **alkalmazásazonosítót.** Ezt az értéket egy későbbi lépésben használhatja.