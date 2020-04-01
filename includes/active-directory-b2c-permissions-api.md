---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 71a6654acd436c27bd2370646dede81778113860
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186141"
---
#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Válassza **az Alkalmazások**lehetőséget, majd válassza ki azt a webalkalmazást, amelynek hozzáféréssel kell rendelkeznie az API-hoz. Például *webapp1*.
1. Válassza az **API-hozzáférés**lehetőséget, majd a **Hozzáadás**lehetőséget.
1. Az **API kiválasztása** legördülő menüben válassza ki azt az API-t, amelyhez a webalkalmazásnak hozzáférést kell biztosítani. Például *webapi1*.
1. A **Scopes kiválasztása** legördülő menüben jelölje ki a korábban definiált hatóköröket. *Például, demo.read* és *demo.write*.
1. Válassza **az OK gombot.**

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Válassza **az Alkalmazásregisztrációk (előzetes verzió)** lehetőséget, majd válassza ki azt a webalkalmazást, amelynek hozzáféréssel kell rendelkeznie az API-hoz. Például *webapp1*.
1. A **Kezelés csoportban**válassza az **API-engedélyek lehetőséget.**
1. A **Konfigurált engedélyek**csoportban válassza **az Engedély hozzáadása**lehetőséget.
1. Válassza a **Saját API-k** lapot.
1. Válassza ki azt az API-t, amelyhez a webalkalmazásnak hozzáférést kell biztosítani. Például *webapi1*.
1. Az **Engedély csoportban**bontsa ki a **bemutatót,** majd jelölje ki a korábban definiált hatóköröket. *Például, demo.read* és *demo.write*.
1. Válassza **az Engedélyek hozzáadása**lehetőséget. Az utasítás szerint várjon néhány percet, mielőtt továbblépne a következő lépésre.
1. Válassza **a Rendszergazdai hozzájárulás megadása (a bérlő neve) lehetőséget.**
1. Válassza ki a jelenleg bejelentkezett rendszergazdai fiókot, vagy jelentkezzen be egy fiókkal az Azure AD B2C-bérlőben, amely legalább a *felhőalkalmazás-rendszergazdai* szerepkörhöz van rendelve.
1. Válassza ki az **Elfogadás** lehetőséget.
1. Válassza a **Frissítés**lehetőséget, majd ellenőrizze, hogy a "Granted for ..." mindkét hatókör **Állapota** csoportban jelenik meg. Az engedélyek propagálása eltarthat néhány percig.