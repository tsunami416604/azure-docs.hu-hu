---
title: Az Azure AD Gallery alkalmazásba való kiépítés órákig vagy még több időt vesz igénybe | Microsoft Docs
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
ms.openlocfilehash: ca2257a46815dd5e685b9cc746a64cede510d10a
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034187"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Az Azure AD Gallery alkalmazásba történő felhasználói üzembe helyezése órákat vesz igénybe

Az alkalmazások automatikus kiosztásának első engedélyezésekor a kezdeti ciklus 20 perctől akár több óráig is eltarthat, az Azure AD-címtár méretétől és a kiépítés hatókörében lévő felhasználók számától függően. 

A későbbi szinkronizálások a kezdeti ciklus után gyorsabbak lesznek, mivel a kiépítési szolgáltatás a kezdeti ciklust követően mindkét rendszer állapotát jelképező vízjeleket tárolja, így javítja a későbbi szinkronizálások teljesítményét.

## <a name="how-to-improve-provisioning-performance"></a>A kiépítési teljesítmény javítása

Ha a kezdeti ciklus több mint néhány órát vesz igénybe, a teljesítmény javítása érdekében egy dologra van szükség:

-   **Felhasználói hatókörű szűrők.** A hatóköri szűrők lehetővé teszik a kiépítési szolgáltatás által az Azure AD által kinyert adatok finomhangolását a felhasználók adott attribútumérték alapján történő kiszűrésével. A szűrők hatókörével kapcsolatos további információkért lásd: [attribútum-alapú alkalmazás kiépítés hatókör-szűrőkkel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>További lépések
[Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](user-provisioning.md)

