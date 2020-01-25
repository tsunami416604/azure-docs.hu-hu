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
ms.openlocfilehash: 7d0c2586b129935043ae7b2eccd11cc3d65a385c
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712095"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Az Azure AD Gallery alkalmazásba történő felhasználói üzembe helyezése órákat vesz igénybe

Az alkalmazások automatikus kiosztásának első engedélyezésekor a kezdeti ciklus 20 perctől akár több óráig is eltarthat, az Azure AD-címtár méretétől és a kiépítés hatókörében lévő felhasználók számától függően. 

A későbbi szinkronizálások a kezdeti ciklus után gyorsabbak lesznek, mivel a kiépítési szolgáltatás a kezdeti ciklust követően mindkét rendszer állapotát jelképező vízjeleket tárolja, így javítja a későbbi szinkronizálások teljesítményét.

## <a name="how-to-improve-provisioning-performance"></a>A kiépítési teljesítmény javítása

Ha a kezdeti ciklus több mint néhány órát vesz igénybe, a teljesítmény javítása érdekében egy dologra van szükség:

-   **Felhasználói hatókörű szűrők.** A hatóköri szűrők lehetővé teszik a kiépítési szolgáltatás által az Azure AD által kinyert adatok finomhangolását a felhasználók adott attribútumérték alapján történő kiszűrésével. A szűrők hatókörével kapcsolatos további információkért lásd: [attribútum-alapú alkalmazás kiépítés hatókör-szűrőkkel](define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Következő lépések
[Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](user-provisioning.md)

