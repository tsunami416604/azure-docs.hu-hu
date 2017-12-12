---
title: "A felhasználók átadása egy Azure AD-katalógusában alkalmazás véve óra vagy több |} Microsoft Docs"
description: "Annak ellenőrzése, ezért az alkalmazás történő előfordulhat, hogy kell a vártnál tovább tart, mint amennyi várt"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: c9ed12569c0adc5ed8625a8d9fc81c9bee874cd4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>A felhasználók átadása egy Azure AD-katalógusában alkalmazás véve óra vagy több

Ha először engedélyezve van az alkalmazás automatikus kiépítés, a kezdeti szinkronizálás is igénybe vehet 20 percet az Azure AD-címtár és a felhasználók kialakítási hatókörében számát méretétől függően több órát. 

A kezdeti szinkronizálás után az ezt követő szinkronizálások lehet gyorsabb, mint a létesítési szolgáltatás, amelyben a kezdeti szinkronizálást, és ezt követő szinkronizálások teljesítményének javítása után mindkét állapota egy vízjelek tárolja.

## <a name="how-to-improve-provisioning-performance"></a>Üzembe helyezési teljesítményének javításával

A kezdeti szinkronizálás csak néhány óra tart, ha van egy művelet, amelyet teljesítmény javítása érdekében:

-   **Felhasználói tartalmazó szűrők.** Hatókörként szűrők lehetővé teszik finom hangolási az adatokat, amelyek az Azure AD által meghatározott attribútumértékek alapján felhasználók kiszűrése bontja ki a létesítési szolgáltatás. A helyezése hatókörszűrőkkel további információkért lásd: [alkalmazások Attribútumalapú üzembe helyezése hatókörszűrőkkel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>Következő lépések
[Felhasználói kiépítésének és megszüntetésének biztosítása SaaS-alkalmazásokhoz az Azure Active Directoryval történő automatizálásához](active-directory-saas-app-provisioning.md)

