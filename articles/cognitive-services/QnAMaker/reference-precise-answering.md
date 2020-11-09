---
title: Pontos hívásfogadás a válasz-span észlelés használatával – QnA Maker
description: A QnA Maker felügyelt szolgáltatásban elérhető pontos hívásfogadás funkció megismerése.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 4f64bab698cb87e26fa4fd1587c4269acf99fa59
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2020
ms.locfileid: "94384105"
---
# <a name="precise-answering"></a>Pontos hívásfogadás

A pontos hívásfogadás funkció lehetővé teszi, hogy pontos rövid választ kapjon a Tudásbázisban jelen lévő, a felhasználói lekérdezésekhez elérhető legjobb válasz-áthaladásból. Ez a funkció egy olyan mély tanulási modellt használ, amely a Futtatás során megérti a felhasználói lekérdezés célját, és a válasz áthaladásának pontos rövid válaszát észleli, ha egy rövid válasz van jelen a válasz-átjáróban. 

Ez a szolgáltatás alapértelmezés szerint be van kapcsolva a teszt ablaktáblán, így tesztelheti a forgatókönyvhöz kapcsolódó funkciókat. Ez a funkció rendkívül hasznos a fejlesztők és a végfelhasználók számára egyaránt. Mostantól a tartalmi fejlesztőknek nem kell manuálisan megadniuk a konkrét QnA párokat a tudásbázisban található minden egyes tényhez, és a végfelhasználónak nem kell megvizsgálnia a szolgáltatás által visszaadott teljes válasz-áthaladást, hogy megkeresse a felhasználó lekérdezésére adott tényleges tényt. 

## <a name="precise-answering-on-qna-maker-portal"></a>Pontos válaszadás QnA Maker portálon

A QnA Maker-portálon a test-panel megnyitásakor megjelenik egy lehetőség a **rövid válasz megjelenítéséhez** a tetején. Ez a beállítás alapértelmezés szerint ki lesz választva. Amikor lekérdezést ad meg a teszt ablaktáblán, egy rövid válasz jelenik meg a válasz áthaladásával együtt, ha a válaszban van egy rövid válasz. 
 
![Felügyelt engedélyezett teszt panel](../QnAMaker/media/conversational-context/test-pane-with-managed.png)

Ha csak a teszt ablaktáblában szeretné megjeleníteni a válasz áthaladását, törölje a **rövid válasz megjelenítése** lehetőséget. 

A szolgáltatás a pontos válasz megbízhatósági pontszámát is visszaadja egy **Válasz-span pontszámként** , amelyet a teszt ablaktáblán a lekérdezés alatt található **vizsgálat** lehetőségre kattintva adhat meg.

![Felügyelt válaszok span pontszáma](../QnAMaker/media/conversational-context/managed-answer-span-score.png)

## <a name="publishing-a-qna-maker-bot"></a>QnA Maker robot közzététele

Amikor közzétesz egy robotot, az alkalmazásban alapértelmezés szerint a pontos válasz engedélyezett élmény jelenik meg, ahol rövid választ fog látni a válasz áthaladásával együtt. A felhasználónak rugalmasan kell kiválasztania más élményeket a sablonnak a th eBot app Service használatával történő frissítésével. 

## <a name="language-support"></a>Nyelvi támogatás

Jelenleg a pontos válasz funkció csak angol nyelven támogatott.
