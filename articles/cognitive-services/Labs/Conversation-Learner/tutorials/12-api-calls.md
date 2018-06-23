---
title: Használjon API használatával meghívja a beszélgetés tanuló alkalmazással - Microsoft kognitív szolgáltatások |} Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan használja az API-hívásokban beszélgetés tanuló alkalmazásokkal együtt használandó.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: ec752cbadfac7a47e08ed7b0ffe8bb475969fac5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348642"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-application"></a>Az API-hívásokban beszélgetés tanuló alkalmazás hozzáadása

Ez az oktatóanyag bemutatja, hogyan adhat az alkalmazáshoz az API-hívásokban. API-hívások olyan funkció, amely határozza meg, és írja be a botot, és amely beszélgetés tanuló elő tudják hívni.

## <a name="requirements"></a>Követelmények
Ez az oktatóanyag megköveteli, hogy fut-e a "tutorialAPICalls.ts" bot.

    npm run tutorial-api-calls

## <a name="details"></a>Részletek

- Az API-hívásokban olvashatja és kezelheti az entitásokat.
- Az API-hívásokban van az memória objektum eléréséhez.
- API-hívások is számos argumentumok – Ez lehetővé teszi a különböző célt szolgálhat azonos API hívása újbóli használata.

### <a name="open-the-demo"></a>Nyissa meg a bemutató

A webes felhasználói felület alkalmazások listájának megtekintéséhez kattintson a oktatóanyag – 12-APICalls. 

### <a name="entities"></a>Entitások

Egy entitás definiáltuk nevű számát az alkalmazásban.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>API-hívások
A kódot az API-hívásokban van definiálva az aktuális fájl: C:\<installedpath\>\src\demos\tutorialAPICalls.ts.

![](../media/tutorial12_apicalls.PNG)

- Az első API visszahívási RandomGreeting. Az üdvözlőlap változóban megadott véletlenszerű üdvözlő adja vissza.
- A szorzási API visszahívási: azt fogja szorozza meg a felhasználó által megadott két szám. Ezután a két szám a szorzás eredményét adja vissza. Ez azt jelenti, hogy a API visszahívások is igénybe vehet a bemeneti adatok. Vegye figyelembe, hogy memóriakezelő az első argumentumként. 
- A ClearEntities API visszahívási: ahhoz, hogy a felhasználó adja meg a következő számú entitás törlése. Ez azt szemlélteti, hogyan API-hívások állíthatók be entitások.

### <a name="actions"></a>Műveletek

Létrehoztunk négy művelet közül választhat. 

![](../media/tutorial12_actions.PNG)

- Mellett "milyen számon szeretné szorozza meg 12?" Ez az communicative művelet, három különböző API-hívások, amelyek a tipikus API-hívás minták szemléltetik.

- RandomGreeting: egy nem várja meg a művelet van. Állítsa be ezt, a művelet létrehozása párbeszédpanel, azt a API_LOCAL művelet típusa kiválasztva, majd RandomGreeting kiválasztva. 

![](../media/tutorial12_setupapicall.PNG)

A frissítés gomb melletti az API-t használja, ha a botot leállítására, és végezze el a módosításokat az API-kat a rendszer. Kattintson a frissítés a legújabb módosításainak érvényesítéséhez lenne.

Íme, hogyan hozza létre azt a szorozza meg a műveletet: API_Local és az API kiválasztása, után azt egy entitás ($number) a bemeneti érték első (num1string), és a megadott a második bemeneti érték (num2string) értéket (12). Ez a botot közötti kiosztótábla biztosít, és az API-t hívja, ezért a azonos visszahívási rendelhetők néhány műveleteket a rendszer, és hogyan hozzá vannak rendelve a térnek el egymástól.

![](../media/tutorial12_actionmultiply.PNG)

### <a name="train-dialog"></a>Vonat párbeszédpanel

Bemutatjuk, egy tanítási párbeszédpanelen.

1. Kattintson a vonat párbeszédpanelek, majd új vonat párbeszédpanel.
1. Adja meg "hi".
2. Kattintson a pontszám művelet.
3. Kattintással jelölje ki a RandomGreeting. Ez hajtja végre a véletlenszerű köszöntés API-hívás.
3. Jelölje be a "milyen számot szeretné szorozza meg 12?"
4. Adja meg a "8". Kattintson a pontszám műveletek.
4. Jelölje ki "$number szorzási 12'. Vegye figyelembe a szorzás eredményét.
5. Válassza ki a "Tiszta entitások".
    - Vegye figyelembe, hogy a szám entitás érték törölve lett.
3. Jelölje be a "milyen számot szeretné szorozza meg 12?"
4. Kattintson a végzett tesztelése.

![](../media/tutorial12_dialog.PNG)

Most láthatta hogyan kell regisztrálni az API-visszahívások, a közös környezeteken, és hogyan argumentumok megadása, és rendelje hozzá az értékek és a bennük foglalt entitások.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kártyák 1. rész](./13-cards-1.md)
