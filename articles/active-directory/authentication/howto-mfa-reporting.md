---
title: Azure MFA-kiszolgáló hozzáférési és használati jelentések |} A Microsoft Docs
description: Ez ismerteti az Azure multi-factor Authentication szolgáltatás - jelentések használata.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: ead1c5a899057bb26154b45c75251e7d9e481147
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160892"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Az Azure multi-factor Authentication jelentései

Az Azure multi-factor Authentication számos jelentéseket biztosít, amelyek segítségével az Ön és szervezete az Azure Portalon keresztül érhető el. Az alábbi táblázatban az elérhető jelentéseket sorolja fel:

| Jelentés | Hely | Leírás |
|:--- |:--- |:--- |
| Blokkolt felhasználó – előzmények | Az Azure AD > MFA-kiszolgáló > felhasználó blokkolása/feloldása | A felhasználók blokkolása és feloldása kérelmek előzményeit jeleníti meg. |
| Használat és a csalási riasztás | Az Azure AD > bejelentkezések | Információt nyújt az általános használat – felhasználói összefoglalás és felhasználói adatait; csakúgy, mint a megadott dátumtartományban küldött visszaélési riasztások előzményeit. |
| A helyszíni összetevők használata | Az Azure AD > MFA-kiszolgáló > tevékenységgel kapcsolatos jelentés | Információt nyújt az általános használati a multi-factor Authentication az NPS bővítményével, ADFS, és az MFA-kiszolgáló. |
| Mellőzött felhasználó – előzmények | Az Azure AD > MFA-kiszolgáló > egyszeri Mellőzés | Egy felhasználó a multi-factor Authentication kihagyásához a kérelmek előzményeit biztosítja. |
| Kiszolgáló állapota | Az Azure AD > MFA-kiszolgáló > kiszolgáló állapota | A fiókjához társított multi-factor Authentication kiszolgálók állapotát jeleníti meg. |

## <a name="view-reports"></a>Jelentések megtekintése 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldalon válassza ki a **Azure Active Directory** > **MFA-kiszolgáló**.
3. Válassza ki a megtekinteni kívánt jelentést.

   <center>![Felhő](./media/howto-mfa-reporting/report.png)</center>

## <a name="powershell-reporting"></a>PowerShell-jelentéskészítés

A multi-factor Authentication az alábbi PowerShell-lel regisztráló felhasználók azonosításához.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Azonosítsa azokat a felhasználókat, akik nem regisztrált multi-factor Authentication az alábbi PowerShell-lel.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>További lépések

* [A felhasználók számára](../user-help/multi-factor-authentication-end-user.md)
* [Az üzembe helyezés](concept-mfa-whichversion.md)
