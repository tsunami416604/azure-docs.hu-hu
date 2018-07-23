---
title: Verziókezelés és a címkézés a Beszélgetéstanuló modell – a Microsoft Cognitive Services használata |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használható a verziókezelés és a egy Beszélgetéstanuló modellel címkézés.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: c7f23d989cbfa0ece9e404a0fe0feb68cf5fddb2
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170545"
---
# <a name="how-to-use-versioning-and-tagging"></a>Verziókezelés és a címkézés használata

Ez az oktatóanyag bemutatja, hogyan a Beszélgetéstanuló modell verziói címkét, és állítsa be melyik verzió "élő".  

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz log párbeszédpanelek, nem a napló párbeszédpanel webes felhasználói felület létrehozása a robot emulator használatával.  

Ehhez az oktatóanyaghoz, hogy fut-e az általános oktatóanyag bot:

    npm run tutorial-general

## <a name="details"></a>Részletek

Szerkesztésekor, mindig a "master" nevű címke szerkesztése – címkézett verziók master (Ez lényegében pillanatfelvételének elkészítése master) hozhat létre, de nem szerkesztheti a címkézett verziók.

## <a name="steps"></a>Lépések

### <a name="install-the-bot-framework-emulator"></a>Telepítse a Bot framework-emulátor

- Nyissa meg a következőt: [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
- Töltse le és telepítse az emulátort.

### <a name="create-an-model"></a>A modell létrehozása

1. Kattintson az új modell
2. A név mezőben adja meg az oktatóanyag-16-verziókezelés
3. Kattintson a Létrehozás gombra 
4. Kattintson a beállítások
5. A modell azonosítóját

### <a name="configure-the-emulator"></a>Az emulator konfigurálása

- Beszélgetéstanuló gyökérmappájában nyissa meg a .env fájlt.
- Illessze be a Modellazonosító CONVERSATION_LEARNER_MODEL_ID értéket
- A Beszélgetéstanuló szolgáltatás újraindításához a parancssor használatával kilép, és ismételt:
 
    oktatóanyag – általános futtatásához npm 

### <a name="actions"></a>Műveletek

Hozzunk létre egy műveletet:

1. Váltson a webes felhasználói felületen.
1. Kattintson a műveletek, majd az új művelet.
2. Válaszként, adja meg a "hi ott (1-es)".
3. Kattintson a Mentés gombra.


![](../media/tutorial16_action1.PNG)

Új címke létrehozásához:

3. Kattintson a "beállítások", és hozzon létre egy új "címke".
    - Nevezze el "1. verziójának"
4. Állítsa be az "1. verziójának" kell "élő".  
    - Az élő címkét "1. verziójának" beállítás hatásának, hogy ez a robot használatával csatornák használja a "version 1" címke.
    - Modellek címkézett verziói nem érinti a módosításokat (módosítása műveletek, entitásokat, train párbeszédpanelek hozzáadása).  
    - A "master" címke mindig készülnek el módosításainak egy modell (műveleteket hajthat végre, az entitások módosítása, train párbeszédpanelek hozzáadása).  Más szóval "master", amelyek megváltoztatják; csak címke más címkék pillanatképek vannak rögzítve.
    - Bejelentkezés párbeszédpanelek a beszélgetés Learner felhasználói felület mindig használja master (nem a élő címke).

![](../media/tutorial16_v1_create.PNG)

A verzió létrehozása a beállítások:

![](../media/tutorial16_settings.PNG)

Adjunk hozzá egy második műveletet:

1. Kattintson a műveletek, majd az új művelet.
2. A választ adja meg a "bye bye (2-es)".

Az első teendő szerkesztése:

1. Kattintson a műveletek.
2. A műveletek, kattintson a "hi ott (1-es)".
3. Módosítsa a válasz a "hi ott (2. verzió)".

![](../media/tutorial16_hi_there_v2.PNG)

### <a name="switch-to-the-bot-emulator"></a>Váltson át a robot-emulátor

1. A robot felhasználói Felületet adja meg a "goodbye értékre".
2. A robot válaszol "üdv mindenkinek ott (1-es)".
    - Ez bemutatja az 1. verziójának "élő". 

![](../media/tutorial16_bf_response.PNG)

### <a name="switch-to-the-web-ui"></a>A webes felhasználói felület

1. Kattintson a napló párbeszédpanelek, és (Ha nem lát minden párbeszédpanelek, a frissítés gombra).
2. Kattintson a "hi ott (2-es)"

> [!NOTE]
> Javításokat minden jelenleg elérhető művelet kiválasztásával teheti azt. Ezeket a szerkesztéseket a fő fog érkezni.

Most láthatta versioning működését, és hogyan használhatja a robot a Bot framework emulator használatával.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bemutató – jelszó-visszaállítás](./demo-password-reset.md)
