---
title: API használatával meghívja a Beszélgetéstanuló modellel – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használható az API-hívások Beszélgetéstanuló modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 1d4013d736d8cfcb75874bc0c86d20b86ab4dd62
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215839"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>API-hívások Beszélgetéstanuló modell hozzáadása

Ez az oktatóanyag bemutatja, hogyan adhat hozzá a modell API-hívások. API-hívások olyan funkció, amely határozza meg, és írja be a robot, és amely Beszélgetéstanuló hívhatja.

## <a name="video"></a>Videó

[![Az oktatóanyag 12 előzetes verzió](http://aka.ms/cl-tutorial-12-preview)](http://aka.ms/blis-tutorial-12)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e a "tutorialAPICalls.ts" robot.

    npm run tutorial-api-calls

## <a name="details"></a>Részletek

- API-hívások megtekintheti és módosíthatja az entitásokat.
- API-hívások hozzáférése a memória-kezelő objektumot.
- API-hívások is számos argumentumok – Ez lehetővé teszi, hogy újra az azonos API-hívást különböző célokat szolgálnak.

### <a name="open-the-demo"></a>Nyissa meg a bemutatót

A webes felhasználói felületen modell-lista kattintson a oktatóanyag – 12-APICalls. 

### <a name="entities"></a>Entitások

Egy entitás rendelkezik meghatározott nevű száma a modellben.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>API-hívások
A kódot az API-hívásokhoz van definiálva a következő fájlt: C:\<installedpath\>\src\demos\tutorialAPICalls.ts.

![](../media/tutorial12_apicalls.PNG)

- Az első API-visszahívás RandomGreeting. A üdvözlés változóban meghatározott véletlenszerű üdvözlőszöveget adja vissza.
- A szorzás API visszahívás:, fogja meg a felhasználó által megadott két szám. Ezután a két szám a szorzás eredményét adja vissza. Ez jelzi, hogy API-visszahívások eltarthat bemenetei között. Vegye figyelembe, hogy memóriakezelő az első argumentum. 
- A ClearEntities API visszahívás: a felhasználók a következő számát adja meg a számot entitás törlése. Ez azt szemlélteti, hogyan API-hívások entitásokat is módosíthatja.

### <a name="actions"></a>Műveletek

Négy művelet hoztunk létre. 

![](../media/tutorial12_actions.PNG)

- Mellett "milyen számon szeretné szorzása a következővel 12?" Ez az egy communicative művelet, három különböző API-hívások, amelyek a tipikus API-hívás minták mutatják be.

- RandomGreeting: egy nem várakozási művelet van. Ezt állítja be, a művelet létrehozása párbeszédpanelen, hogy API_LOCAL művelet típusa kiválasztva, majd RandomGreeting kiválasztott. 

![](../media/tutorial12_setupapicall.PNG)

A frissítés gomb mellett az API-t használja, ha a robot leállítása, és ne módosítsa az API-kat is. Kattintson a frissítés a legutóbbi módosítások átvételéhez lenne.

Itt látható, hogyan hoztunk létre a szorozza meg a műveletet: Miután API_Local és API-t, hogy egy entitás ($number) a bemeneti érték első (num1string), és a megadott a második bemeneti érték (num2string) értéket (12). Ez a robot közötti kiosztótábla szinten biztosítja, és az API-hívások, így ugyanazt a visszahívás is le lehet képezni néhány műveleteket a rendszer, és hogyan hozzá vannak rendelve a térnek el egymástól.

![](../media/tutorial12_actionmultiply.PNG)

### <a name="train-dialog"></a>Train párbeszédpanel

Nézzük végig, egy tanítási párbeszédpanel.

1. Kattintson a vonat párbeszédpanelek, majd az új Train párbeszédpanel.
1. Adja meg "hi".
2. Kattintson a pontszám művelet.
3. Kattintással jelölje ki a RandomGreeting. Ez hajtja végre a véletlenszerű üdvözlőszöveget API-hívás.
3. Jelölje be a "milyen számot szeretné szorzása a következővel 12?"
4. Adja meg a "8". Kattintson a pontszám műveletek.
4. Válassza ki a(z) $number szorzása 12'. Vegye figyelembe a szorzás az eredményt.
5. Válassza ki a "Tiszta entitások".
    - A `number` törölve lett az entitás értékét.
3. Jelölje be a "milyen számot szeretné szorzása a következővel 12?"
4. Kattintson a kész tesztelése.

![](../media/tutorial12_dialog.PNG)

Most láthatta, API-visszahívások regisztrálása gyakori mintáit, és hogyan argumentumok megadása, és rendelje hozzá az értékeket, és azokat az entitásokat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kártyák: 1. rész](./13-cards-1.md)
