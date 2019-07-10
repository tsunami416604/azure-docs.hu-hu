---
title: Aktív tanulás – Personalizer
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: edjez
ms.openlocfilehash: c44afc81a7ec9d05cdc04cc8bc46c77cd51ceaf8
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722532"
---
# <a name="active-learning-and-learning-policies"></a>Aktív tanulás és a szabályzatok tanulási 

Ha az alkalmazás meghívja a rang API-t, a tartalom egy rangsorát kap. Üzleti logika a sorrend meghatározására segítségével határozza meg, ha a tartalom lehet a felhasználó adatai jelennek meg. Amikor megjeleníti a rangsorolt tartalmat, amely egy _aktív_ rangsorolják esemény. Ha az alkalmazás nem jelenik meg, amely a tartalom, vagyis rangsorolva, amelyekről egy _inaktív_ rangsorolják esemény. 

Aktív rangsorolják eseményadatok Personalizer küld vissza. Ezt az információt segítségével továbbra is az aktuális learning szabályzat a modell betanításához.

## <a name="active-events"></a>Aktív események

Aktív események mindig megjelennek a felhasználó számára, és a ellenszolgáltatás hívás gombra kattintva zárja be a learning hurok visszaküldését. 

### <a name="inactive-events"></a>Inaktív események 

Inaktív eseményeket nem szabad módosítani az alapul szolgáló modell, mert a felhasználó nem lett megadva egy alkalommal a rangsorolt tartalom közül választhat.

## <a name="dont-train-with-inactive-rank-events"></a>Az inaktív rangsorolják események nem betanítása 

Egyes alkalmazások esetében szükség lehet a rang API meghívása anélkül, hogy még ha az alkalmazás megjeleníti az eredményeket a felhasználónak. 

Ez akkor fordul elő, ha:

* Előfordulhat, hogy lehet előre megjelenítése néhány felhasználói felület, amely a felhasználót, vagy esetleg nem kapják meg megtekintéséhez. 
* Előfordulhat, hogy az alkalmazás foglalják prediktív személyre szabása, amelyben rangsorolják hívások kisebb valós idejű környezettel végzett, és előfordulhat, hogy a kimenetet, vagy nem lehetséges, hogy az alkalmazás által használt. 

### <a name="disable-active-learning-for-inactive-rank-events-during-rank-call"></a>Aktív tanulás inaktív rangsorolják események rangsorolják hívás során letiltása

Letiltja az automatikus tanulás, hívja meg a rang `learningEnabled = False`.

Az inaktív esemény Learning implicit módon aktiválódik, ha elküldi egy díjazásban belüli Rangjának.

## <a name="learning-policies"></a>Tanulási házirendek

Tanulási házirend határozza meg, az adott *hiperparaméterek* , a modell betanítása. Két modellje közül ugyanazokat az adatokat, tanított különböző tanulási szabályzatokat, a rendszer eltérően viselkednek.

### <a name="importing-and-exporting-learning-policies"></a>Tanulási házirendek importálása és exportálása

Importálhatja és exportálhatja a tanulási házirend fájlokat az Azure Portalról. Ez lehetővé teszi, hogy mentse a meglévő szabályzatokat, tesztelje le azokat, cserélje le a és a későbbi felhasználás és naplózási összetevők, a kódot verziókövetési rendszerben archiválja őket.

### <a name="learning-policy-settings"></a>Tanulási házirend-beállítások

A beállítások a **Learning házirend** nem tartozhat módosítható. Csak akkor módosítsa a beállításokat, ha megismeri, hogyan Personalizer kerültek. A felhasználó tudta nélkül beállításainak módosítása esetén hatásai, beleértve a Personalizer modellek érvénytelenítése.

### <a name="comparing-effectiveness-of-learning-policies"></a>Tanulási házirendek hatékonyságának összehasonlítása

Összehasonlíthatja a különböző tanulási házirendek lenne végrehajtása után Personalizer naplók az elmúlt adatokon foglalkozások [offline értékelések](concepts-offline-evaluation.md).

[Töltse fel a saját tanulási házirendek](how-to-offline-evaluation.md) való összehasonlításra az aktuális learning házirend.

### <a name="discovery-of-optimized-learning-policies"></a>Felderítési optimalizált learning házirendek

Personalizer több optimalizált learning házirendet hozhat létre, amikor egy [offline értékelési](how-to-offline-evaluation.md). Egy több optimalizált learning szabályzatot, amely jobb jutalmakat van egy kapcsolat nélküli értékelési jelenik meg, jobb eredményeket, ha a használt online Personalizer előállításához.

Az optimalizált learning házirend létrehozását követően alkalmazhat, közvetlenül a Personalizer így azonnal lecseréli a jelenlegi házirend, vagy további értékelés céljából mentse, és döntse el, a jövőben e elveti, menteni vagy alkalmazás később.