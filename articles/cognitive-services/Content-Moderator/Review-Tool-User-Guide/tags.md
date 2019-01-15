---
title: Tartalom-jóváhagyás - Content Moderator egyéni címkék használatával
titlesuffix: Azure Cognitive Services
description: A Content Moderator alapértelmezett címkét tartalmaz, és az üzleti igényeinek megfelelő tartalom moderálása egyéni címkéket is létrehozhat.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: c1a547f99995d25d19dafb03276306c50a544c9a
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264716"
---
# <a name="create-and-use-moderation-tags"></a>Hozzon létre, és a moderálás címkék használata

A két alapértelmezett címkék mellett **isadult** (**egy**) és **isracy** (**r**), további célzott vizsgálata hozhat létre egyéni címkék. Ezeket az egyéni címkéket emberi teszik a felülvizsgálóknak a képek és szöveg hozzárendelése majd érhetők el.

## <a name="create-tags"></a>Címkék létrehozása

1.  Címkék kiválasztása a beállítások lapról.

  ![Tartalom-jóváhagyás címkék](images/tags-1.png)

2.  Adja meg a címke a kétbetűs rövid kód.
3.  Adja meg a címke nevét. Tartsa rövid és informatív nevét. Ha például **isbullying**.
4.  Adjon meg egy leírást.
5.  Kattintson az Add (Hozzáadás) parancsra.
6.  Amikor elkészült, a címkék létrehozása, kattintson a Mentés gombra.

![Tartalom-jóváhagyás címkék meghatározása](images/tags-2-define.png)

## <a name="using-custom-tags"></a>Egyéni címkék használatával

Egyéni címkék emberi vizsgálóeszközt során használatosak. Az előzetes verzióval fognak megjelenni, és a felülvizsgáló választja, kattintson rá.

![Tartalom-jóváhagyás címkék használatával](images/tags-3-use.png)

Kikapcsolhatja a különböző címkék különböző ellenőrzésekhez ellenőrzése vagy eszközhitelesítést látható van.
 
![Tartalom-jóváhagyás címkék letiltása](images/tags-4-disable.png)

Amíg nem törli a két alapértelmezett címkék **isadult** és **isracy**, törölheti a megadott egyéni címkéket. Kattintson a törölni kívánt címke melletti Kuka.

![Tartalom-jóváhagyás címkék törlése](images/tags-5-delete.png)

## <a name="next-steps"></a>További lépések

Címkék használata képmoderálás kapcsolatban lásd: [áttekintése lemezképek Metz](Review-Moderated-Images.md).
