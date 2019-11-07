---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: ce49e9e26160017d64d745c9c7ad5402bc3ae2ca
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73641554"
---
#### <a name="applicationstabapplications"></a>[Alkalmazások](#tab/applications/)

1. Válassza az **alkalmazások**lehetőséget, majd válassza ki azt a webalkalmazást, amelynek hozzáférést szeretne biztosítani az API-hoz. Például: *webapp1*.
1. Válassza az **API-hozzáférés**lehetőséget, majd kattintson a **Hozzáadás**gombra.
1. Az **API kiválasztása** legördülő menüben válassza ki azt az API-t, amelyhez hozzáférést szeretne adni a webalkalmazásnak. Például: *webapi1*.
1. A **hatókörök kiválasztása** legördülő menüben válassza ki a korábban definiált hatóköröket. Például: *bemutató. Read* és *demo. Write*.
1. Kattintson az **OK** gombra.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Válassza a **Alkalmazásregisztrációk (előzetes verzió)** lehetőséget, majd válassza ki azt a webalkalmazást, amelynek hozzáférést szeretne biztosítani az API-hoz. Például: *webapp1*.
1. A **kezelés**területen válassza az **API-engedélyek**lehetőséget.
1. A **konfigurált engedélyek**területen válassza **az engedély hozzáadása**elemet.
1. Válassza a **saját API** -k fület.
1. Válassza ki azt az API-t, amelyhez hozzáférést szeretne adni a webalkalmazásnak. Például: *webapi1*.
1. Az **engedély**területen bontsa ki a **bemutató**elemet, majd válassza ki a korábban definiált hatóköröket. Például: *bemutató. Read* és *demo. Write*.
1. Válassza az **engedélyek hozzáadása**lehetőséget. Az utasítás szerint várjon néhány percet, mielőtt továbblép a következő lépésre.
1. Válassza a **rendszergazdai jóváhagyás megadása (a bérlő neve)** lehetőséget.
1. Válassza ki a jelenleg bejelentkezett rendszergazdai fiókot, vagy jelentkezzen be egy olyan fiókkal a Azure AD B2C-bérlőben, amely legalább a *Cloud Application Administrator* szerepkörhöz van rendelve.
1. Válassza ki az **Elfogadás** lehetőséget.
1. Válassza a **frissítés**lehetőséget, majd ellenőrizze, hogy a "engedélyezve..." mindkét hatókör **állapota** alatt jelenik meg. Eltarthat néhány percig, amíg az engedélyek propagálása megtörténik.