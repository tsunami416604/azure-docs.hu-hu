---
title: "Az Azure MFA hozzáférési és használati jelentések |} Microsoft Docs"
description: "Ez ismerteti az Azure multi-factor Authentication szolgáltatás - jelentéseket."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 3f6b33c4-04c8-47d4-aecb-aa39a61c4189
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: joflore
ms.reviewer: alexwe
ms.openlocfilehash: 77d6742faadfaf3d7afccfbe888b910c80278737
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Azure multi-factor Authentication jelentései

Az Azure multi-factor Authentication segítségével, és a szervezet több jelentéseket biztosít. Ezek a jelentések a multi-factor Authentication kezelési portál is elérhetőek. A következő táblázat az elérhető jelentések megjelenítéséhez.

| Jelentés | Leírás |
|:--- |:--- |
| Használat |A használati jelentések megjelenítési adatok összesített használatát, a – felhasználói összefoglalás és a felhasználó adatait. |
| Kiszolgáló állapota |Ez a jelentés a fiókjához a multi-factor Authentication kiszolgálók állapotát jeleníti meg. |
| Letiltott felhasználók előzményei |Ezek a jelentések megjelenítése a felhasználók blokkolása és feloldása kérelmek előzményeit. |
| Kihagyott felhasználók előzményei |Átugorja a multi-factor Authentication hitelesítés a felhasználó telefonszámának kérelmek előzményeit jeleníti meg. |
| Csalási riasztás |Megjeleníti a megadott dátumtartományban küldött visszaélési riasztások előzményeit. |
| A várólistára |Listák sorba állított jelentések feldolgozási és azok állapotát. Töltse le, vagy a jelentés megtekintéséhez mutató hivatkozás a jelentés befejezésekor valósul meg. |

## <a name="view-reports"></a>Jelentések megtekintése

1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com).
2. A bal oldalon válassza az Active Directory elemet.
3. Kövesse az e két beállítás megadása, attól függően, hogy használ-e hitelesítésszolgáltatók egyikét:
   * **1. lehetőség**: a többtényezős hitelesítésszolgáltatók lapon. Válassza ki az MFA-szolgáltatóra, majd kattintson a **kezelése** panel alján.
   * **2. lehetőség**: válassza ki azt a címtárat, és navigáljon a **konfigurálása** fülre. A többtényezős hitelesítés szakaszban jelölje ki a **Szolgáltatásbeállítások kezelése** elemet. A multi-factor Authentication szolgáltatás beállításainak lap alján kattintson a nyissa meg a portál hivatkozását.
4. Az Azure multi-factor Authentication kezelési portál, válassza ki a kívánt jelentéstípust a **megtekint egy jelentést** a bal oldali navigációs szakasz.

<center>![Felhő](./media/multi-factor-authentication-manage-reports/report.png)</center>

## <a name="powershell-reporting"></a>PowerShell-jelentés

A következő Powershell-lel MFA regisztrált felhasználók azonosítása.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

A következő Powershell-lel MFA nem regisztrált felhasználók azonosítása.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

**További források**

* [A felhasználók számára](end-user/multi-factor-authentication-end-user.md)
* [Az Azure többtényezős hitelesítés az MSDN webhelyen](https://msdn.microsoft.com/library/azure/dn249471.aspx)
