---
title: A felhasználó üzembe helyezése az Azure AD Gallery alkalmazásban óra vagy több időt vesz igénybe
description: Annak megállapítása, hogy az alkalmazás kiépítés miért lehet hosszabb a vártnál.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7425731a8d4adde11cd3f15df2cd27cd8541f615
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275698"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Az Azure AD Gallery alkalmazásba történő felhasználói üzembe helyezése órákat vesz igénybe

Az alkalmazások automatikus kiosztásának első engedélyezésekor a kezdeti ciklus 20 perctől akár több óráig is eltarthat, az Azure AD-címtár méretétől és a kiépítés hatókörében lévő felhasználók számától függően. 

A későbbi szinkronizálások a kezdeti ciklus után gyorsabbak lesznek, mivel a kiépítési szolgáltatás a kezdeti ciklust követően mindkét rendszer állapotát jelképező vízjeleket tárolja, így javítja a későbbi szinkronizálások teljesítményét.

## <a name="how-to-improve-provisioning-performance"></a>A kiépítési teljesítmény javítása

Ha a kezdeti ciklus több mint néhány órát vesz igénybe, a teljesítmény javítása érdekében egy dologra van szükség:

-   **Felhasználói hatókörű szűrők.** A hatóköri szűrők lehetővé teszik a kiépítési szolgáltatás által az Azure AD által kinyert adatok finomhangolását a felhasználók adott attribútumérték alapján történő kiszűrésével. A szűrők hatókörével kapcsolatos további információkért lásd: [attribútum-alapú alkalmazás kiépítés hatókör-szűrőkkel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>Következő lépések
[Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](user-provisioning.md)

