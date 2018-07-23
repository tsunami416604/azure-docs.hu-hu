---
title: Entitások használata Beszélgetéstanuló modell – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használhatja az entitásokat egy Beszélgetéstanuló modellel.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f851d43d69999a848dea01c9457a379adb63353b
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39172381"
---
# <a name="introduction-to-entities"></a>Az entitások bemutatása

Ez az oktatóanyag bemutatja a entitásokat, és bemutatja, hogyan használhatja a "Disqualifying entitások" és "Entitások kötelező" mezőket a műveletek.

## <a name="video"></a>Videó

[![3. oktatóanyag előzetes verzió](http://aka.ms/cl-tutorial-03-preview)](http://aka.ms/blis-tutorial-03)

## <a name="requirements"></a>Követelmények

Ehhez az oktatóanyaghoz, hogy fut-e az általános oktatóanyag robotot

    npm run tutorial-general

## <a name="details"></a>Részletek

Ebben az oktatóanyagban két gyakori használati entitások mutatja be.  Először entitásokat is nyerje ki oszt fel egy felhasználó üzenetben, például a "Mi az az időjárás, a Seattle?" város azonosítása.  Entitások kijelölhetjük másodszor, amikor műveletek érhetők el.  Pontosabban a művelet folyamatban "kötelező" vagy "kizárásának" entitás listázhatja:
- Egy műveletet kötelező entitások szerepelnie kell a robot a memóriában ahhoz, hogy a művelet csak akkor érhető el
- Entitások kizárásának kell *nem* jelen a robot a memóriában ahhoz, hogy a művelet csak akkor érhető el

Egyéb oktatóanyagok más aspektusait entitások, például az előre összeállított entitások, többértékű negálható entitások, programozott és az entitások kódban adatműveletekkel entitások terjed ki.

## <a name="steps"></a>Lépések

### <a name="create-the-model"></a>A modell létrehozása

1. A webes felhasználói felületén kattintson az új modell
2. A nevet írja be a IntroToEntities. Ezután kattintson a Létrehozás gombra.

### <a name="create-entity"></a>Entitás létrehozása

1. Kattintson az entitásokat, majd az új entitás.
2. Az entitás nevét adja meg a város.
3. Kattintson a Létrehozás gombra

> [!NOTE]
> Az entitástípus "egyéni" – Ez azt jelenti, hogy az entitás is kell betanítani.  Előre összeállított entitások, ami azt jelenti, hogy azok viselkedése nem módosítható – ezek terjed ki az oktatóanyagban egy másik is vannak.

### <a name="create-two-actions"></a>Hozzon létre két műveletet

1. Kattintson a műveletek, majd az új művelet
2. Válaszként írja be a "Nem tudom, melyik városban szeretne".
3. Adja meg $city kizárásának entitásokat. Kattintson a Mentés gombra.
    - Ez azt jelenti, hogy ha ehhez az entitáshoz van definiálva a robot a memóriában, majd ezt a műveletet a rendszer *nem* érhető el.
2. Kattintson a műveletek, majd új műveletet egy másik művelet létrehozása.
3. A válasz írja be a "a $city az időjárás is valószínűleg sunny".
4. Szükséges entitások, az városa entitás lett hozzáadva automatikusan óta elé.
5. Kattintson a Save (Mentés) gombra.

Most már két műveletet.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-bot"></a>A robot betanítása

1. Kattintson a vonat párbeszédpanelek, majd az új Train párbeszédpanel.
2. Írja be a "hello".
3. Kattintson a pontszám műveletek, és válassza a "Nem tudom, melyik városban szeretne?"
    - Ha az városa entitás szükség a válasz nem választható, mert az városa entitás nincs definiálva a robot a memóriában.
2. Válassza ki a "Nem tudom, melyik városban szeretne".
4. Adja meg a "seattle". Jelölje ki a seattle, majd kattintson az városa.
5. Kattintson a pontszám műveletek
    - Város értéke most a robot a memóriában.
    - "A $city időjárási is valószínűleg sunny" már elérhető adott válaszként. 
6. Jelölje ki a "$city az időjárás valószínűleg sunny".

Vegyük például a felhasználó beírja a "repeat". 
1. Írja be, és adja meg. Város entitás és az értékét a memóriában van és elérhető.
2. Jelölje ki a "$city az időjárás valószínűleg sunny".

![](../media/tutorial3_entities.PNG)

Ezzel létrehozott egy entitás és példányát, a felhasználó üzenetek címkével.  Is használt az entitás jelenléte vagy hiánya a robot a memóriában ellenőrzési műveletek keresztül érhető el, a művelet kizárásának, és a szükséges entitások mezőt.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Várt entitás](./4-expected-entity.md)
