---
title: A felhasználó üzembe helyezése az Azure AD Gallery alkalmazásban óra vagy több időt vesz igénybe
description: Annak megállapítása, hogy az alkalmazás kiépítés miért lehet hosszabb a vártnál.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: f0f572e5153b6e79bf7731d0d57b66e601a0054c
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/15/2020
ms.locfileid: "84781973"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Az Azure AD Gallery alkalmazásba történő felhasználói üzembe helyezése órákat vesz igénybe

Az alkalmazások automatikus kiosztásának első engedélyezésekor a kezdeti ciklus 20 perctől akár több óráig is eltarthat, az Azure AD-címtár méretétől és a kiépítés hatókörében lévő felhasználók számától függően. 

A későbbi szinkronizálások a kezdeti ciklus után gyorsabbak lesznek, mivel a kiépítési szolgáltatás a kezdeti ciklust követően mindkét rendszer állapotát jelképező vízjeleket tárolja, így javítja a későbbi szinkronizálások teljesítményét.

## <a name="how-to-improve-provisioning-performance"></a>A kiépítési teljesítmény javítása

Ha a kezdeti ciklus több mint néhány órát vesz igénybe, a teljesítmény javítása érdekében egy dologra van szükség:

-   **Felhasználói hatókörű szűrők.** A hatóköri szűrők lehetővé teszik a kiépítési szolgáltatás által az Azure AD által kinyert adatok finomhangolását a felhasználók adott attribútumérték alapján történő kiszűrésével. A szűrők hatókörével kapcsolatos további információkért lásd: [attribútum-alapú alkalmazás kiépítés hatókör-szűrőkkel](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>További lépések
[A felhasználók üzembe helyezésének automatizálása és az SaaS-alkalmazások kiépítése a Azure Active Directory](user-provisioning.md)

