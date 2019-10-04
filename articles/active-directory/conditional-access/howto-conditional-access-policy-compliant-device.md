---
title: Feltételes hozzáférés – megfelelő eszközök megkövetelése – Azure Active Directory
description: Egyéni feltételes hozzáférési szabályzat létrehozása a megfelelő eszközök megköveteléséhez
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: da1a5f54e5e989f661770d518a6753b831b59bd4
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69990513"
---
# <a name="conditional-access-require-compliant-devices"></a>Feltételes hozzáférés: Megfelelő eszközök megkövetelése

A Microsoft Intune központilag telepített szervezetek a megfelelőségi követelményeknek megfelelő eszközök azonosítására használhatják az eszközük által visszaadott adatokat, például:

* PIN-kód feloldásának megkövetelése
* Eszköz titkosításának megkövetelése
* Az operációs rendszer minimális vagy maximális verziójának megkövetelése
* Az eszköz megkövetelése nem feltört vagy feltört

A szabályzat megfelelőségi információi továbbítva lesznek az Azure AD-nek, ahol a feltételes hozzáférés az erőforrásokhoz való hozzáférés engedélyezésére vagy letiltására vonatkozó döntéseket hozhat.

## <a name="create-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

A következő lépések segítséget nyújtanak egy feltételes hozzáférési szabályzat létrehozásához, amely megköveteli, hogy az erőforrások eléréséhez szükséges eszközök megfeleljenek a szervezet Intune megfelelőségi szabályzatának.

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory** > a**feltételes hozzáférést**.
1. Válassza az **új szabályzat**lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **hozzárendelések**alatt válassza a **felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás**területen válassza a **minden felhasználó**lehetőséget.
   1. A **kizárás**területen válassza a **felhasználók és csoportok** lehetőséget, majd válassza ki a szervezet vészhelyzeti hozzáférését vagy az adatbontási fiókokat. 
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **Cloud apps vagy a műveletek** > területen válassza a **minden felhőalapú alkalmazás**lehetőséget.
   1. Ha ki kell zárnia bizonyos alkalmazásokat a szabályzatból, kiválaszthatja őket a kizárás lapon a **kizárt felhőalapú alkalmazások kiválasztása** területen, majd válassza a **kiválasztás**lehetőséget.
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **hozzáférés-vezérlés** > **megadása**területen jelölje be az **eszköz megfelelőként való**megjelölésének megkövetelése jelölőnégyzetet.
   1. Válassza a **kiválasztás**lehetőséget.
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési szabályzatot bekapcsolva értékre.
1. Válassza a **Létrehozás** lehetőséget a szabályzat engedélyezéséhez.

## <a name="next-steps"></a>További lépések

[Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

[Bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával](troubleshoot-conditional-access-what-if.md)

[Az eszközök megfelelőségi szabályzatai működnek az Azure AD-vel](https://docs.microsoft.com/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
