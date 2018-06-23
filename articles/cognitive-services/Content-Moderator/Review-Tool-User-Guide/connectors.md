---
title: Összekötők használata az Azure Content moderátor más API-k elérésére |} Microsoft Docs
description: Ismerje meg, hogyan férhet hozzá a tartalom moderátor munkafolyamatokat más API-k összekötők használatával.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/22/2017
ms.author: sajagtap
ms.openlocfilehash: d8114457e7079ca8772cab830bd011dcddf372f5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347019"
---
# <a name="connectors"></a>Összekötők

Az Azure Content moderátor munkafolyamatok használhatja más API mellett tartalom moderátor API-k. A tartalom moderátor összekötő használatával éri el a többi API-k. Az összekötő hivatkozást nyújt az egyéb API-kat.

Tartalom moderátor az alapértelmezett összekötők tartalmazza:

* Emotion API
* Face API
* PhotoDNA felhőalapú szolgáltatás

![Tartalom moderátor rendelkezésre álló összekötők](images/connectors-1.png)

## <a name="verify-your-credentials"></a>Ellenőrizze a hitelesítő adatokat 

Definiálása előtt célszerű egy munkafolyamatot, győződjön meg arról, hogy rendelkezik-e az összekötő API-k, amely a használni kívánt érvényes hitelesítő adatokat:

1.  Jelölje ki a felülvizsgálati eszköz irányítópult **beállítások** > **összekötők**.

  ![Tartalom moderátor válassza összekötők](images/connectors-2.png)

2.  Válassza ki a **szerkesztése** szimbólum mellett a megfelelő összekötőre, a hitelesítő adatok ellenőrzéséhez.

  ![Tartalom moderátor jelölje ki a Szerkesztés szimbólumot](images/connectors-3.png)

3.  Az Előfizetés kulcs jelenik meg. Ha végezze el a módosításokat, jelölje be **mentése** befejezése után.

  ![Tartalom szerkesztése összekötők moderátor lap](images/connectors-4-1.png)
 
## <a name="add-a-connector"></a>Összekötő hozzáadása

1.  Összekötő hozzáadása előtt be kell egy előfizetési kulcsot. Jelölje ki a felülvizsgálati eszköz irányítópult **beállítások** > **hitelesítő adatok**. Jelölje ki és másolja az érték, amely a **Ocp-rendszergazda-előfizetés-kulcs** mezőbe.

2.  Válassza ki **összekötők**. Válassza ki a rendelkezésre álló összekötők a felülvizsgálati eszköz irányítópulton megjelenített egyikét. Ezt követően válassza **Connect**. 

  ![Tartalom hozzáadása összekötő moderátor lap](images/connectors-5.png)

3.  Az a **Ocp-rendszergazda-előfizetés-kulcs** mezőbe illessze be a másolt kulcsot. Ezt követően válassza a **Mentés** lehetőséget.

## <a name="next-steps"></a>További lépések

* Összekötők használata [határozza meg az egyéni munkafolyamatokat](workflows.md).
