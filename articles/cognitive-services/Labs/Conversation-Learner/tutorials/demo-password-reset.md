---
title: Bemutató Beszélgetéstanuló modell, jelszó-visszaállítási – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan hozhat létre egy bemutató Beszélgetéstanuló modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: bd0bcd79bb21dc3973b34086f6dad21b47a95c2f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240868"
---
# <a name="demo-password-reset"></a>Bemutató: Jelszó-visszaállítás
Ez a bemutató mutatja be egy egyszerű technikai támogatás robot, amelyek segíthetnek a jelszó-visszaállítási folyamatait. 

Megmutatja, hogyan Beszélgetéstanuló tudhat meg nem triviális párbeszédpanel folyamatok, több kapcsolja sorozatok, beleértve egy out-a-domain-osztályt. Ez a bemutató nem használja a kódok vagy entitásokat.

## <a name="video"></a>Videó

[![Bemutató jelszó előzetes verzió](https://aka.ms/cl-demo-password-preview)](https://aka.ms/blis-demo-password)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e a jelszó alaphelyzetbe állítása robotot

    npm run demo-password

### <a name="open-the-demo"></a>Nyissa meg a bemutatót

A webes felhasználói felületen modell-lista kattintson a oktatóanyag bemutató jelszó alaphelyzetbe állítása. 

### <a name="actions"></a>Műveletek

Ha a felhasználó keres súgó a jelszavát, beleértve a megoldások a műveletek hoztunk létre.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Képzési párbeszédpanelek

Számos képzési párbeszédpanelek. Még nincsenek technológiáink miként kívüli tartomány osztály – például a felhasználói kérések, tartományi; "útvonalak" esnek a robot adott tartomány kérések ki néhány példát, és képes válaszolni az "I nem tud segíteni az adott."

![](../media/tutorial_pw_reset_entities.PNG)

Tegyük fel próbáljon meg egy tanítási munkamenet.

1. Kattintson a vonat párbeszédpanelek, majd az új Train párbeszédpanel.
1. Adja meg a "Megszakadt a jelszó".
2. Kattintson a pontszám művelet.
3. Jelölje be a "Is, amely a helyi fiók vagy a Microsoft-fiókkal?"
4. Adja meg a "Helyi fiók".
5. Kattintson a pontszám műveletek.
3. Jelölje be a "Windows melyik verziója van?"
4. Adja meg "a Windows 8'.
5. Kattintson a pontszám műveletek.
6. Válassza ki a(z) megoldás: a Windows 8-as jelszó alaphelyzetbe állítása. "
4. Kattintson a tanítási kész gombra.

Próbáljuk meg, hogyan a robot további egy out-a-domain-osztályt.

1. Kattintson a vonat párbeszédpanelek, majd az új Train párbeszédpanel.
1. Adja meg a "webes keresés".
    - Ez a tartományon kívüli out osztály egy példát. 
2. Kattintson a pontszám művelet.
3. Jelölje be a "Sajnos tudok, amely nem segítség."
    - Figyelje meg, hogy ez a beállítás a pontszám értéke jelenleg alacsony. De után egy kicsit több oktatás, a pontszám magasabb fog kapni.
4. Kattintson a tanítási kész gombra.

Most láthatta, hogyan hozhat létre egy alapvető technikai támogatás bemutatót, és hogyan tudhat megoldást nyújtanak, és a mintalekérdezések kívül is kezelésére.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bemutató – kétpizzás sorrend](./demo-pizza-order.md)
