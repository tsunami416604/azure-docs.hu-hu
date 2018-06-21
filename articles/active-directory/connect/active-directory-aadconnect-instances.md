---
title: 'Az Azure AD Connect: Szolgáltatáspéldány szinkronizálása |} Microsoft Docs'
description: Ezen a lapon dokumentumokat az Azure AD-példányban szempontjai.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 351f3e34f5050d922655a633ec89f51d191b946e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295616"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Az Azure AD Connect: Különleges szempontok példányok
Az Azure AD Connect leggyakrabban használt világszerte példányát az Azure AD és az Office 365. Emellett vannak más esetekben és ezeket az URL-címek és más szempontot különböző követelményekkel rendelkezik.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Germany
A [Microsoft Cloud Németország](http://www.microsoft.de/cloud-deutschland) egy német adatok adatkezelő által működtetett szuverén felhőalapú.

| Nyissa meg a proxykiszolgáló URL-címek |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| + Tanúsítvány-visszavonási listákat |

Amikor bejelentkezik az Azure AD-bérlő, a onmicrosoft.de tartományban olyan fiókot kell használnia.

Jelenleg nem szerepel a Microsoft Cloud németországi funkciói:

* **A jelszóvisszaírás** érhető el az előzetes 1.1.570.0 az Azure AD Connect verziójával és után.
* Más Azure AD Premium-szolgáltatások nem érhetők el.

## <a name="microsoft-azure-government-cloud"></a>A Microsoft Azure Government felhő
A [a Microsoft Azure Government felhő](https://azure.microsoft.com/features/gov/) az Amerikai Egyesült Államok kormánya felhőalapú.

A felhő által a DirSync korábbi verziókban támogatott. A build 1.1.180 az Azure AD Connect a felhő következő generációja esetén támogatott. Ebben a generációban csak USA alapján végpontok használja, és nyissa meg a proxykiszolgáló URL-címek különböző listája.

| Nyissa meg a proxykiszolgáló URL-címek |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*. windows.net (automatikus szükséges az Azure AD kormányzati bérlői észlelési) |
| \*.gov.us.microsoftonline.com |
| + Tanúsítvány-visszavonási listákat |

> [!NOTE]
> AAD-csatlakozás verziója 1.1.647.0, frissítésétől AzureInstance értékre állítja a beállításjegyzékben már nincs szükség, feltéve, hogy *. windows.net meg nyitva a a proxy kiszolgálókra.

Jelenleg nem szerepel a Microsoft Azure Government felhőalapú szolgáltatások:

* **A jelszóvisszaírás** érhető el az előzetes 1.1.570.0 az Azure AD Connect verziójával és után.
* Más Azure AD Premium-szolgáltatások nem érhetők el.

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
