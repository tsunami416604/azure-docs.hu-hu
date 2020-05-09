---
title: Azure AD-alkalmazás-regisztrációhoz tartozó végpontok beszerzése
titleSuffix: Microsoft identity platform
description: Az Azure AD-vel való fejlesztéshez vagy regisztráláshoz használt egyéni alkalmazás hitelesítési végpontjának megkeresése.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: ryanwi
ms.openlocfilehash: dc37ce474ad3226f5dbf7e6efd687ddf55f7da03
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926680"
---
# <a name="how-to-discover-endpoints"></a>Végpontok felderítése

Az alkalmazáshoz tartozó hitelesítési végpontokat a [Azure Portalban](https://portal.azure.com)találja.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza a **Azure Active Directory**lehetőséget.
1. A **kezelés**területen válassza a **Alkalmazásregisztrációk**lehetőséget, majd a felső menüben válassza a **végpontok** lehetőséget.

    Ekkor megjelenik a **végpontok** lap, amely megjeleníti a bérlőhöz tartozó hitelesítési végpontokat.
    
    Használja azt a végpontot, amely az alkalmazás **(ügyfél) azonosítójával** együtt használt hitelesítési protokollnak felel meg az alkalmazásra vonatkozó hitelesítési kérelem kiépítéséhez.

Az **országos felhők** (például az Azure ad China, a Germany és az Egyesült Államok kormánya) saját alkalmazás-regisztrációs portált és Azure ad-hitelesítési végpontokat tartalmazhatnak. További információk a [nemzeti felhők áttekintésében](authentication-national-cloud.md).

## <a name="next-steps"></a>További lépések

A különböző Azure-környezetekben található végpontokkal kapcsolatos további információkért tekintse meg az [országos felhők áttekintését](authentication-national-cloud.md).
