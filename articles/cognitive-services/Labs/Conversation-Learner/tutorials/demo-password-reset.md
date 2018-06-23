---
title: Beszélgetés tanuló alkalmazás, jelszó-visszaállítás - Microsoft kognitív szolgáltatásokat bemutató |} Microsoft Docs
titleSuffix: Azure
description: Útmutató a bemutató beszélgetés tanuló alkalmazás létrehozásához.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 24d61787a79ee1a1a9737c417aa966cc8fd75930
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348614"
---
# <a name="demo-password-reset"></a>Bemutató: Jelszó alaphelyzetbe állítása
Ez a bemutató, amelyek segíthetnek a jelszó-átállításra egyszerű műszaki támogatás bot mutatja be. 

Azt illusztrálja, hogyan beszélgetés tanuló nem triviális párbeszédpanel adatfolyamok, több kapcsolja sorozatok, például egy tartományhoz csatlakozó out osztályt is találhat. Ebben a bemutatóban a kódok vagy entitások nem használja.

## <a name="requirements"></a>Követelmények
Ez az oktatóanyag megköveteli, hogy fut-e a jelszó alaphelyzetbe állítása botot

    npm run demo-password

### <a name="open-the-demo"></a>Nyissa meg a bemutató

A webes felhasználói felület alkalmazások listájának megtekintéséhez kattintson a oktatóanyag bemutató jelszó alaphelyzetbe állítása. 

### <a name="actions"></a>Műveletek

Létrehoztunk műveleteket, ha a felhasználó segítségre megoldások többek között a jelszó szüksége van.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Képzési párbeszédpanelek

Számos képzési párbeszédpanelek. Van például felhasználói kérelmek például tartomány; kívül esnek a "útvonalak" kívüli tartomány osztály – bemutatók a botot megkapta-e tartományi kérelmek kívül néhány példát, és képes válaszolni a "I nem segít, hogy."

![](../media/tutorial_pw_reset_entities.PNG)

Tegyük fel próbáljuk meg egy tanítási munkamenet.

1. Kattintson a vonat párbeszédpanelek, majd új vonat párbeszédpanel.
1. Adja meg a "Megszakadt a jelszavam".
2. Kattintson a pontszám művelet.
3. Jelölje be az "Is, amely a helyi fióknak vagy a Microsoft-fiókkal?"
4. Adja meg a "Helyi fiók".
5. Kattintson a pontszám műveletek.
3. Jelölje be a "Windows verziójának rendelkezik?"
4. Adja meg "a Windows 8'.
5. Kattintson a pontszám műveletek.
6. Jelölje ki "megoldás: a Windows 8-as jelszó alaphelyzetbe állításával."
4. Kattintson a tanítási végezhető el.

Próbáljuk meg, hogyan a botot egy tartományhoz csatlakozó out osztályt is találhat.

1. Kattintson a vonat párbeszédpanelek, majd új vonat párbeszédpanel.
1. Adja meg a "keresést a".
    - Íme egy tartományhoz csatlakozó out osztály. 
2. Kattintson a pontszám művelet.
3. Jelölje be a "Sajnos I nem segít, hogy."
    - Figyelje meg, ez a beállítás a pontszám jelenleg alacsony. De után egy kis oktatási a pontszám magasabb kap.
4. Kattintson a tanítási végezhető el.

Most láthatta egy alapvető technikai támogatás bemutató létrehozása, és hogyan megtanulhassa megoldást nyújtanak, és mintalekérdezések kívül is kezelni.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bemutató - pizzaszósz sorrendje](./demo-pizza-order.md)
