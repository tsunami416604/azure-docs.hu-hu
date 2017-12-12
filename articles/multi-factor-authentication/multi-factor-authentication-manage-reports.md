---
title: "Az Azure MFA hozzáférési és használati jelentések |} Microsoft Docs"
description: "Ez ismerteti az Azure multi-factor Authentication szolgáltatás - jelentéseket."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
editor: curtand
ms.assetid: 3f6b33c4-04c8-47d4-aecb-aa39a61c4189
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: 32697eea410cb9afaa0e4347acd1a280fcf94289
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Azure multi-factor Authentication jelentései

Az Azure multi-factor Authentication segítségével, és a szervezet több jelentéseket biztosít. Ezek a jelentések a multi-factor Authentication kezelési portál is elérhetőek. A következő táblázat az elérhető jelentések megjelenítéséhez.

| Jelentés | Leírás |
|:--- |:--- |
| Használat |A használati jelentések megjelenítési adatok összesített használatát, a – felhasználói összefoglalás és a felhasználó adatait. |
| Kiszolgáló állapota |Ez a jelentés a fiókjához a multi-factor Authentication kiszolgálók állapotát jeleníti meg. |
| Blokkolt felhasználó – előzmények |Ezek a jelentések megjelenítése a felhasználók blokkolása és feloldása kérelmek előzményeit. |
| Mellőzött felhasználó – előzmények |Átugorja a multi-factor Authentication hitelesítés a felhasználó telefonszámának kérelmek előzményeit jeleníti meg. |
| Visszaélési riasztás |Megjeleníti a megadott dátumtartományban küldött visszaélési riasztások előzményeit. |
| Várakozási sorba helyezve |Listák sorba állított jelentések feldolgozási és azok állapotát. Töltse le, vagy a jelentés megtekintéséhez mutató hivatkozás a jelentés befejezésekor valósul meg. |

## <a name="view-reports"></a>Jelentések megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a bal oldali **Azure Active Directory** > **felhasználók és csoportok** > **minden felhasználó** > **multi-factor Authentication Hitelesítési**.
3. A **a multi-factor authentication**, jelölje be **szolgáltatás beállításai**. Az alsó **speciális beállításai és jelentések megtekintése kezelése**, jelölje be **-portálon végezhető**.
4. Az Azure multi-factor Authentication kezelési portál, válassza ki a kívánt jelentéstípust a **megtekint egy jelentést** a bal oldali navigációs szakasz.

<center>![Felhő](./media/multi-factor-authentication-manage-reports/report.png)</center>

## <a name="powershell-reporting"></a>PowerShell-jelentés

A következő PowerShell-lel MFA regisztrált felhasználók azonosítása.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

A következő PowerShell-lel MFA nem regisztrált felhasználók azonosítása.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

**További források**

* [A felhasználók számára](end-user/multi-factor-authentication-end-user.md)
* [Az Azure többtényezős hitelesítés az MSDN webhelyen](https://msdn.microsoft.com/library/azure/dn249471.aspx)
