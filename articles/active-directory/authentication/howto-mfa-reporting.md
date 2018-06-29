---
title: Az Azure MFA hozzáférési és használati jelentések |} Microsoft Docs
description: Ez ismerteti az Azure multi-factor Authentication szolgáltatás - jelentéseket.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 12/15/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 4eb91e37331a5af064d2af0e937eb071d805688f
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097879"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Azure multi-factor Authentication jelentései

Az Azure multi-factor Authentication segítségével, és a szervezet az Azure-portálon keresztül érhető el több jelentéseket biztosít. A következő táblázat az elérhető jelentések megjelenítéséhez.

| Jelentés | Hely | Leírás |
|:--- |:--- |:--- |
| Blokkolt felhasználó – előzmények | Az Azure AD > multi-factor Authentication kiszolgáló > felhasználó blokkolása/feloldása | A felhasználók blokkolása és feloldása kérelmek előzményeit jeleníti meg. |
| Használati és csalás riasztások | Az Azure AD > bejelentkezések | Információt nyújt összesített használatát, a – felhasználói összefoglalás és a felhasználó adatait; csakúgy, mint a megadott dátumtartományban küldött visszaélési riasztások előzményeit. |
| A helyszíni összetevők használata | Az Azure AD > multi-factor Authentication kiszolgáló > tevékenységgel kapcsolatos jelentés | Ez a témakör információkat összesített használatát a multi-factor Authentication bővítményével NPS, AD FS, és a multi-factor Authentication kiszolgáló. |
| Mellőzött felhasználó – előzmények | Az Azure AD > multi-factor Authentication kiszolgáló > egyszeri Mellőzés | A felhasználó átugorja a multi-factor Authentication kérelmek előzményeit biztosítja. |
| Kiszolgáló állapota | Az Azure AD > multi-factor Authentication kiszolgáló > kiszolgáló állapota | A fiókjához társított multi-factor Authentication kiszolgálók állapotát jeleníti meg. |

## <a name="view-reports"></a>Jelentések megtekintése 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a bal oldali **Azure Active Directory** > **MFA kiszolgáló**.
3. Válassza ki a megtekinteni kívánt jelentést.

   <center>![Felhő](./media/howto-mfa-reporting/report.png)</center>

## <a name="powershell-reporting"></a>PowerShell-jelentés

A következő PowerShell-lel MFA regisztrált felhasználók azonosítása.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

A következő PowerShell-lel MFA nem regisztrált felhasználók azonosítása.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>További lépések

* [A felhasználók számára](end-user/current/multi-factor-authentication-end-user.md)
* [Ha központi telepítése](concept-mfa-whichversion.md)
