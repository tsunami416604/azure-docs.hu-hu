---
title: Feltételes hozzáférés – megfelelő eszközök megkövetelése – Azure Active Directory
description: Egyéni feltételes hozzáférési szabályzat létrehozása a megfelelő eszközök megköveteléséhez
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90d958d2adc8920e4e6ccbccef20acf20aedca4c
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561612"
---
# <a name="conditional-access-require-compliant-devices"></a>Feltételes hozzáférés: megfelelő eszközök megkövetelése

A Microsoft Intune központilag telepített szervezetek a megfelelőségi követelményeknek megfelelő eszközök azonosítására használhatják az eszközük által visszaadott adatokat, például:

* PIN-kód feloldásának megkövetelése
* Eszköz titkosításának megkövetelése
* Az operációs rendszer minimális vagy maximális verziójának megkövetelése
* Az eszköz megkövetelése nem feltört vagy feltört

A szabályzat megfelelőségi információi továbbítva lesznek az Azure AD-nek, ahol a feltételes hozzáférés az erőforrásokhoz való hozzáférés engedélyezésére vagy letiltására vonatkozó döntéseket hozhat. További információ az eszköz megfelelőségi házirendjeiről a cikkben található, [szabályok beállítása eszközökön a szervezet erőforrásaihoz való hozzáférés engedélyezéséhez az Intune használatával](/intune/protect/device-compliance-get-started)

## <a name="create-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

A következő lépések segítséget nyújtanak egy feltételes hozzáférési szabályzat létrehozásához, amely megköveteli, hogy az erőforrások eléréséhez szükséges eszközök megfeleljenek a szervezet Intune megfelelőségi szabályzatának.

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory** > **biztonsági** > **feltételes hozzáférés**lehetőséget.
1. Válassza az **új szabályzat**lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **hozzárendelések**alatt válassza a **felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás**területen válassza a **minden felhasználó**lehetőséget.
   1. A **kizárás**területen válassza a **felhasználók és csoportok** lehetőséget, majd válassza ki a szervezet vészhelyzeti hozzáférését vagy az adatbontási fiókokat. 
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **Cloud apps vagy a műveletek** > a **következők**közül válassza a **minden felhőalapú alkalmazás**lehetőséget.
   1. Ha ki kell zárnia bizonyos alkalmazásokat a szabályzatból, kiválaszthatja őket a **kizárás lapon** a **kizárt felhőalapú alkalmazások kiválasztása** területen, majd válassza a **kiválasztás**lehetőséget.
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **hozzáférés-vezérlés** > a **támogatás**területen jelölje be az **eszköz megfelelőként való megjelölésének megkövetelése**jelölőnégyzetet.
   1. Válassza a **kiválasztás**lehetőséget.
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva**értékre.
1. Válassza a **Létrehozás** lehetőséget a szabályzat engedélyezéséhez.

> [!NOTE]
> Az új eszközöket regisztrálhatja az Intune-ban, még akkor is, ha bejelöli az **eszköz megfelelőként való megjelölésének megkövetelése** az **összes felhasználó** és az **összes felhőalapú alkalmazás** számára a fenti lépésekkel. Az **eszköz megfelelőségi vezérlőként való megjelölésének megkövetelése** nem blokkolja az Intune-regisztrációt. 

### <a name="known-behavior"></a>Ismert viselkedés

Windows 7, iOS, Android, macOS és néhány külső webböngészőben az Azure AD az eszközt az Azure AD-vel való regisztráláskor kiépített ügyféltanúsítvány használatával azonosítja. Amikor a felhasználó először jelentkezik be a böngészőben, a rendszer a felhasználótól kéri a tanúsítvány kiválasztását. A felhasználónak ki kell választania ezt a tanúsítványt, mielőtt továbbra is használhassa a böngészőt.

## <a name="next-steps"></a>További lépések

[Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

[A hatás meghatározása a feltételes hozzáférésről szóló jelentés módban](howto-conditional-access-report-only.md)

[Bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával](troubleshoot-conditional-access-what-if.md)

[Az eszközök megfelelőségi szabályzatai működnek az Azure AD-vel](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
