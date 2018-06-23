---
title: Versioning és a címkézést a beszélgetés tanuló alkalmazással - kognitív Microsoft-szolgáltatások használata |} Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan versioning és a címkézés beszélgetés tanuló alkalmazásokkal együtt használandó.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: ea013db078ff33f8597b0e15a8fc951e8ae320e8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348646"
---
# <a name="how-to-use-versioning-and-tagging"></a>Versioning és címkézés

Ez az oktatóanyag bemutatja, hogyan címkét a beszélgetés tanuló alkalmazás verzióját, és állítsa be melyik verziója "élő".  

## <a name="requirements"></a>Követelmények
Ez az oktatóanyag szükséges botot emulátorral napló párbeszédpanelek, nem a napló párbeszédpanel webes felhasználói felületének létrehozásához.  

Ez az oktatóanyag megköveteli, hogy fut-e az általános útmutató botot:

    npm run tutorial-general

## <a name="details"></a>Részletek

Szerkesztésekor, mindig szerkeszti a "fő" nevű tag – címkézett verziók főkiszolgáló (amely lényegében pillanatképet készít, master) hozhat létre, de címkézett verziói nem szerkeszthetők.

## <a name="steps"></a>Lépések

### <a name="install-the-bot-framework-emulator"></a>Telepítse az Botot keretrendszer emulátort

- Nyissa meg a következőt: [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
- Töltse le és telepítse az emulátort.

### <a name="create-an-app"></a>Alkalmazás létrehozása

1. Kattintson az új alkalmazás
2. A név mezőben adja meg az oktatóanyag-16-Versioning
3. Kattintson a Létrehozás gombra 
4. Kattintson a beállítások
5. Másolja az alkalmazás azonosítója

### <a name="configure-the-emulator"></a>Az emulátor konfigurálása

- A beszélgetés tanuló gyökérmappájába nyissa meg a .env fájlt.
- Illessze be az alkalmazás Azonosítóját CONVERSATION_LEARNER_APP_ID értékeként
- Kilépés a parancssorba, majd futtassa újból a beszélgetés tanuló szolgáltatás újraindításához:
 
    Futtassa az oktatóanyag általános npm 

### <a name="actions"></a>Műveletek

Hozzon létre egy műveletet:

1. A webes felhasználói felület váltani.
1. Kattintson a műveletek, majd új művelet.
2. A választ, adja meg a "hi ott (verzió: 1)".
3. Kattintson a Mentés gombra.


![](../media/tutorial16_action1.PNG)

Új címke létrehozásához:

3. Kattintson a "beállítások", és hozzon létre egy új "címke".
    - Neki "verzió 1"
4. Állítsa be a "verzió 1" kell "élő".  
    - Az élő címke verzióról"1" beállítás hatásának, hogy a csatornák használata a botot használja a "version 1" címke.
    - Az alkalmazások címkézett verziói nem érinti a módosításokat (módosítása a műveletek, entitásokat, vonat párbeszédpanelek hozzáadása).  
    - A "fő" címke (módosítása műveletek, entitásokat, vonat párbeszédpanelek hozzáadása) alkalmazás módosításainak mindig történik.  Más szóval "fő" értéke csak címke által módosítható; más címkék pillanatképek rögzítettek.
    - Bejelentkezés párbeszédpanelek a beszélgetés tanuló felhasználói felület mindig használja főkiszolgáló (nem a élő címke).

![](../media/tutorial16_v1_create.PNG)

Megjegyzés: a verziót a beállítások jött létre:

![](../media/tutorial16_settings.PNG)

Adjuk hozzá a második műveletet:

1. Kattintson a műveletek, majd új művelet.
2. A válasz írja be a "(2-es verziójú) bye bye".

Az első művelet szerkesztése:

1. Kattintson a műveletek.
2. A műveletek, kattintson a "hi ott (verzió: 1)".
3. A válasz módosítása "hi ott (2-es verziójú)".

![](../media/tutorial16_hi_there_v2.PNG)

### <a name="switch-to-the-bot-emulator"></a>Váltás a botot emuláló

1. A felhasználói felület botot adja meg a "goodbye".
2. Megjegyzés: a botot válaszol, "hi ott (verzió: 1)".
    - Ez azt jelenti, hogy "élő" értéke 1-es verziójával. 

![](../media/tutorial16_bf_response.PNG)

### <a name="switch-to-the-web-ui"></a>Váltás a webes felhasználói felületen

1. Kattintson a napló párbeszédpanelek, (ha bármely párbeszédpanelek, frissítse az alkalmazás nem jelenik meg).
2. Kattintson a "hi ott (2-es verziójú)"

Vegye figyelembe, hogy javításokat választani a minden jelenleg elérhető művelet kitöltésében. Ezeket a szerkesztéseket a fő fog történni.

Most láthatta versioning működése, és hogyan kezelheti a Botot keretrendszer emulator használatával botot.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bemutató - jelszó alaphelyzetbe állítása](./demo-password-reset.md)
