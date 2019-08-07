---
title: Javaslatok szervizelése Azure Security Centerban | Microsoft Docs
description: Ez a dokumentum ismerteti, hogyan javíthatja a javaslatait a Azure Security Centerban, hogy segítsen az Azure-erőforrások védelmében és a biztonsági szabályzatoknak való megfelelésben.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2019
ms.author: v-mohabe
ms.openlocfilehash: b947796d4af9def1a274f6e04f356c3173d29fff
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779000"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Javaslatok szervizelése Azure Security Center

A javaslatok segítséget nyújtanak az erőforrások jobb biztonságossá tételéhez.  A javaslatot a javaslatban megadott szervizelési lépések követésével hajthatja végre. 

## Szervizelési lépések<a name="remediation-steps"></a>

Az összes javaslat áttekintése után döntse el, hogy melyiket kell elsőként kijavítani. Javasoljuk, hogy a [biztonságos pontszám hatása](security-center-recommendations.md#monitor-recommendations) alapján rangsorolja, hogy mi az első.

1. A listában kattintson a javaslatra.
1. Kövesse a **szervizelési lépések** szakaszban található utasításokat. Minden javaslathoz saját utasítások vannak megadva. Az alábbi szervizelési lépések azt mutatják be, hogyan konfigurálhatja az alkalmazásokat, hogy csak a HTTPS protokollon keresztül engedélyezze a forgalmat.

    ![Javaslat részletei](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Ha elkészült, egy értesítés jelenik meg, amely tájékoztatja, ha a szervizelés sikeres volt.

## <a name="next-steps"></a>További lépések

Ebből a dokumentumból megtudhatta, hogyan javíthatja az ajánlásokat a Security Centerban. Ha többet szeretne megtudni a Security Centerről, tekintse meg a következő témaköröket:

* [Biztonsági szabályzatok beállítása Azure Security Centerban](tutorial-security-policy.md) – Ismerje meg, hogyan konfigurálhatja az Azure-előfizetések és-erőforráscsoportok biztonsági szabályzatait.
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának monitorozásához.
