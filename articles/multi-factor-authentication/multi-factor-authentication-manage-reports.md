---
title: "Az Azure MFA hozzáférési és használati jelentések |} Microsoft Docs"
description: "Ez ismerteti az Azure multi-factor Authentication szolgáltatás - jelentéseket."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 3f6b33c4-04c8-47d4-aecb-aa39a61c4189
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: fb83e957a206bff29132973d2dd3e9a7b5f9f060
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Azure multi-factor Authentication jelentései

Az Azure multi-factor Authentication segítségével, és a szervezet az Azure-portálon keresztül érhető el több jelentéseket biztosít. A következő táblázat az elérhető jelentések megjelenítéséhez.

| Jelentés | Hely | Leírás |
|:--- |:--- |:--- |
| Blokkolt felhasználó – előzmények | Az Azure AD > multi-factor Authentication kiszolgáló > felhasználó blokkolása/feloldása | A felhasználók blokkolása és feloldása kérelmek előzményeit jeleníti meg. |
| Használati és csalás riasztások | Az Azure AD > bejelentkezések | Információt nyújt összesített használatát, a – felhasználói összefoglalás és a felhasználó adatait; csakúgy, mint a megadott dátumtartományban küldött visszaélési riasztások előzményeit. |
| Mellőzött felhasználó – előzmények | Az Azure AD > multi-factor Authentication kiszolgáló > egyszeri Mellőzés | A felhasználó átugorja a multi-factor Authentication kérelmek előzményeit biztosítja. |
| Kiszolgáló állapota | Az Azure AD > multi-factor Authentication kiszolgáló > kiszolgáló állapota | A fiókjához társított multi-factor Authentication kiszolgálók állapotát jeleníti meg. |

## <a name="view-reports"></a>Jelentések megtekintése 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a bal oldali **Azure Active Directory** > **MFA kiszolgáló**.
3. Válassza ki a megtekinteni kívánt jelentést.

   <center>![Felhő](./media/multi-factor-authentication-manage-reports/report.png)</center>

## <a name="powershell-reporting"></a>PowerShell-jelentés

A következő PowerShell-lel MFA regisztrált felhasználók azonosítása.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

A következő PowerShell-lel MFA nem regisztrált felhasználók azonosítása.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>További lépések

* [A felhasználók számára](end-user/multi-factor-authentication-end-user.md)
* [Ha központi telepítése](multi-factor-authentication-get-started.md)
