---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: mimart
ms.openlocfilehash: e37419b5d568b0f60e4c6a0c8fed26eb418f501e
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/02/2020
ms.locfileid: "84297486"
---
#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Válassza a **Alkalmazásregisztrációk (előzetes verzió)** lehetőséget, majd válassza ki azt a webalkalmazást, amelynek hozzáférést szeretne biztosítani az API-hoz. Például: *webapp1*.
1. A **kezelés**területen válassza az **API-engedélyek**lehetőséget.
1. A **konfigurált engedélyek**területen válassza **az engedély hozzáadása**elemet.
1. Válassza a **saját API** -k fület.
1. Válassza ki azt az API-t, amelyhez hozzáférést szeretne adni a webalkalmazásnak. Például: *webapi1*.
1. Az **engedély**területen bontsa ki a **bemutató**elemet, majd válassza ki a korábban definiált hatóköröket. Például: *bemutató. Read* és *demo. Write*.
1. Válassza az **engedélyek hozzáadása**lehetőséget.
1. Válassza a **rendszergazdai jóváhagyás megadása (a bérlő neve)** lehetőséget.
1. Ha a rendszer kéri, hogy válasszon ki egy fiókot, válassza ki a jelenleg bejelentkezett rendszergazdai fiókot, vagy jelentkezzen be egy olyan fiókkal a Azure AD B2C-bérlőben, amely legalább a *Cloud Application Administrator* szerepkörhöz van rendelve.
1. Válassza az **Igen** lehetőséget.
1. Válassza a **frissítés**lehetőséget, majd ellenőrizze, hogy a "engedélyezve..." mindkét hatókör **állapota** alatt jelenik meg.

#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Válassza az **alkalmazások**lehetőséget, majd válassza ki azt a webalkalmazást, amelynek hozzáférést szeretne biztosítani az API-hoz. Például: *webapp1*.
1. Válassza az **API-hozzáférés**lehetőséget, majd kattintson a **Hozzáadás**gombra.
1. Az **API kiválasztása** legördülő menüben válassza ki azt az API-t, amelyhez hozzáférést szeretne adni a webalkalmazásnak. Például: *webapi1*.
1. A **hatókörök kiválasztása** legördülő menüben válassza ki a korábban definiált hatóköröket. Például: *bemutató. Read* és *demo. Write*.
1. Kattintson az **OK** gombra.
