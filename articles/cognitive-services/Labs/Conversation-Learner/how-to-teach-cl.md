---
title: A Conversation Learner-Microsoft Cognitive Services megismerése | Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan taníthatja meg Conversation Learnereit.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: a18d4c31da4ffeefebd4bda9aa441fdfec062be9
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705271"
---
# <a name="how-to-teach-with-conversation-learner"></a>Tanítás a Beszélgetéstanulóval 

Ez a dokumentum ismerteti, hogy milyen jeleket Conversation Learner a rendszer, és leírja, hogyan tanul.  

Az oktatás két különálló lépésből bontható: az entitások kinyerése és a művelet kiválasztása.

## <a name="entity-extraction"></a>Entitás kibontása

A borítók alatt Conversation Learner [Luis](https://www.luis.ai) -t használ az entitások kinyeréséhez.  Ha ismeri a LUIS-t, ez a tapasztalat az entitások kinyerésére vonatkozik Conversation Learnerban.

Az entitások kinyerési modelljei a felhasználók teljes tartalmára és *kontextusára* vonatkozó ismerettel rendelkeznek.  Ha például a "Seattle" szó olyan városként van megjelölve, mint a "mi az időjárás Seattle-ben", az entitások kinyerése képes arra, hogy ugyanazt a tartalmat ("Seattle") olyan városként Ismerje meg, mint például a "Seattle populációja", még akkor is, ha a hosszúságú kimondott szöveg nagyon különbözőek.  Ezzel szemben, ha a "Francis" nevet a "Francis" találkozó bejelentése a "Francis" néven ismeri fel, akkor egy új, korábban láthatatlan név is felismerhető egy hasonló kontextusban, például: "találkozó létrehozása a Robin használatával".  A gépi tanulás a betanítási példák alapján kikövetkezteti, hogy mikor kell részt venni a tartalomban, a kontextusban vagy mindkettőben.

Az entitások kivonása jelenleg csak a jelenlegi Kimondás tartalmát ismeri.  A művelet kiválasztásával ellentétben (alább) nem ismeri a párbeszédpanelek előzményeit, például a korábbi rendszerállapotot, a korábbi felhasználó bekapcsolt vagy korábban felismert entitásokat.  Ennek eredményeképpen az entitások kinyerésének viselkedése az összes hosszúságú kimondott szöveg "Shared".  Ha például a felhasználó az "azt szeretném, hogy az Apple" kifejezés "Fruit" típusúként van megjelölve az egyik felhasználó teljes nevében, akkor az entitás kinyerési modellje várhatóan azt is jelzi, hogy ez a lemondás ("szeretném, hogy az Apple") mindig "Fruit" (Apple) címkével legyen ellátva.

Ha az entitások kinyerése nem a várt módon viselkedik, a következő lehetőségek lehetségesek:

- Első lépésként további betanítási példákat is hozzáadhat – különösen olyan példákat, amelyek felfedik a jellemző entitások környezetét (a környező szavakat) vagy kivételeket.
- Szükség esetén érdemes lehet "várt entitás" tulajdonságot hozzáadni egy művelethez.  További részletekért tekintse meg az oktatóanyag a várt entitásokról című témakört.
- Habár manuálisan `EntityExtractionCallback` is felvehetők az entitások a kóddal való kinyeréséhez, ez a legkevésbé ajánlott módszer, mivel a rendszer a gépi tanulás fejlesztései miatt nem fog kihasználni.

## <a name="action-selection"></a>Művelet kiválasztása

A művelet kiválasztása egy visszatérő neurális hálózatot használ, amely az összes beszélgetési előzmény bemenetét veszi figyelembe.  Így a művelet kiválasztása olyan állapot-nyilvántartó folyamat, amely ismeri a korábbi felhasználói hosszúságú kimondott szöveg, az entitások értékeit és a rendszer hosszúságú kimondott szöveg.  

Bizonyos jeleket a tanulási folyamat természetesen előnyben részesített.  Más szóval, ha Conversation Learner meg tudja magyarázni egy művelet kiválasztási döntését az "előnyben részesített" jelek használatával, a következő lesz: Ha nem, akkor a "kevésbé preferált" jeleket fogja használni.

Itt látható egy táblázat, amely a Conversation Learner összes jeleit mutatja, és melyeket a művelet kiválasztása használ.  Vegye figyelembe, hogy a felhasználói hosszúságú kimondott szöveg lévő Word Order utasítás figyelmen kívül lesz hagyva.

Jel | Preferencia (1 = leginkább előnyben részesített) | Megjegyzések
--- | --- | --- 
Rendszerművelet az előző kanyarban | 1 | 
Az aktuális fordulatban lévő entitások | 1 | 
Hogy ez az első turn | 1 |
A jelenlegi felhasználó teljes szövegének pontos egyezése | 2 | 
Hasonló – a jelenlegi felhasználó teljes kifejezése | 3 | 
Rendszerműveletek korábbi lépések előtt | 4 |
Az aktuális fordulat előtt megjelenő entitások | 4 | 
Felhasználói hosszúságú kimondott szöveg az aktuális fordulat előtt | 5 | 

> [!NOTE]
> A művelet kiválasztása nem veszi figyelembe a rendszerműveletek tartalmát – a szöveg, a kártya tartalma vagy az API neve vagy viselkedése – csak a rendszerművelet identitása.  Ennek eredményeképpen a művelet tartalmának módosítása nem változtatja meg a műveleti kiválasztási modell viselkedését.
>
> Továbbá, az entitások tartalma/értékei nem használhatók – csak a jelenlétük/távollétük.

Ha a művelet kiválasztása nem a várt módon viselkedik, a következő lehetőségek lehetségesek:

- További betanítási párbeszédpanelek hozzáadása, különösen azok a párbeszédablakok, amelyek bemutatják, hogy a jelzések milyen műveleteket választanak ki.  Ha például a művelet kiválasztása egy másik jelet részesíti előnyben, adjon meg olyan példákat, amelyek az előnyben részesített jelet ugyanabban az állapotban mutatják, és a többi jelzés eltérő.  Néhány adatsor több tanítási párbeszédpanelt is igénybe vehet.
- Adja hozzá a "kötelező" és a "nem megfelelő" entitásokat a műveleti definícióhoz.  Ez akkor fordul elő, ha a műveletek elérhetők, és hasznosak lehetnek az üzleti szabályok és a józan ész mintázatok kifejezésére. 

## <a name="updates-to-models"></a>Modellek frissítései

Minden alkalommal, amikor hozzáad vagy szerkeszt egy entitást, műveletet vagy betanítási párbeszédpanelt a felhasználói felületen, ez egy kérést hoz létre, amely az entitás-kinyerési modellt és a művelet kiválasztási modelljét is újra betanítja.  A kérelem várólistára kerül, és az újraképzés aszinkron módon történik.  Ha új modell érhető el, az az adott ponttól kezdve az entitások kinyerésére és a művelet kiválasztására szolgál.  Ez az újraképzési folyamat gyakran körülbelül 5 másodpercet vesz igénybe, de hosszabb lehet, ha a modell összetett, vagy ha magas a betöltés a betanítási szolgáltatásban.

Mivel a képzés aszinkron módon történik, lehetséges, hogy az elvégzett módosítások nem tükröződnek azonnal.  Ha az entitás kinyerése vagy a művelet kiválasztása nem a várt módon működik az elmúlt 5-10 másodpercben elvégzett módosítások alapján, ez az oka lehet.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alapértelmezett értékek és határok](./cl-values-and-boundaries.md)
