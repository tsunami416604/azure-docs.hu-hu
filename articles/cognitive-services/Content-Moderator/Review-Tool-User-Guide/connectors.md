---
title: Más szolgáltatások csatlakoztatása a tartalom - Content Moderator moderálása
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan férhetnek hozzá a többi munkafolyamatait a Content Moderator API-k, összekötők használatával.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 3ee582e2541e4eb55e5ea1424782df132ecf3575
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58116554"
---
# <a name="connect-to-other-cognitive-services"></a>Csatlakozhat olyan cognitive services

Az Azure Content Moderator munkafolyamatokat használhat más API-k, Content Moderator API-k mellett. Más API-k a Content Moderator az összekötő segítségével érhető el. Az összekötő egy hivatkozást a más API-kat biztosít.

A Content Moderator alapértelmezett összekötőket tartalmaz:

* Emotion API
* Face API
* PhotoDNA Cloud Service

![Content Moderator elérhető összekötők](images/connectors-1.png)

## <a name="verify-your-credentials"></a>A hitelesítő adatok ellenőrzéséhez 

Megadhat egy munkafolyamatot, mielőtt győződjön meg arról, hogy érvényes hitelesítő adatokat használni kívánt API-összekötő:

1. Válassza ki a vizsgálóeszköz irányítópult **beállítások** > **összekötők**.

   ![Content Moderator válassza összekötők](images/connectors-2.png)

2. Válassza ki a **szerkesztése** mellett a hitelesítő adatait a kívánt összekötő található.

   ![A Content Moderator, válassza ki a szerkesztési szimbólum](images/connectors-3.png)

3. Az előfizetési kulcs jelenik meg. Ha módosítani szeretné, válassza ki a **mentése** befejezése után.

   ![Content Moderator szerkesztése összekötők lapon](images/connectors-4-1.png)
 
## <a name="add-a-connector"></a>Adjon hozzá egy összekötőt

1. Mielőtt hozzáad egy összekötőt, szüksége van egy előfizetési kulcsot. Válassza ki a vizsgálóeszköz irányítópult **beállítások** > **hitelesítő adatok**. Válassza ki, és másolja az értéket, amely szerepel a **Ocp-Admin-Subscription-Key** mezőbe.

2. Válassza ki **összekötők**. Válassza ki a vizsgálóeszközt irányítópulton megjelenített elérhető összekötő közül. Ezután válassza ki **Connect**. 

   ![Content Moderator hozzáadása összekötő lap](images/connectors-5.png)

3. Az a **Ocp-Admin-Subscription-Key** mezőbe illessze be a másolt kulcsot. Ezt követően válassza a **Mentés** lehetőséget.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan használható az összekötők [meghatározása egyéni munkafolyamatokat](workflows.md).
