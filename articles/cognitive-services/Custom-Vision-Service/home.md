---
title: Mi az az Azure Custom Vision?
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogy hozhat létre egyéni képosztályozókat az Azure-felhőben a Custom Vision Service használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 8a5dd16948724e3a79863450212702aa8aeb2347
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605142"
---
# <a name="what-is-azure-custom-vision"></a>Mi az az Azure Custom Vision?

Az Azure Custom Vision az cognitive szolgáltatás, amely lehetővé teszi elkészítheti, telepítheti, és javíthatja a saját rendszerkép osztályozó eszközökkel. Egy rendszerkép osztályozó egy AI-szolgáltatás, amely ellátja címkékkel (mely jelölik _osztályok_) a képek vizuális jellemzőinek megfelelően. Ellentétben a [számítógépes Látástechnológiai](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) , az egyéni vizuális lehetővé teszi, hogy a alkalmazni címkék meghatározásához.

## <a name="what-it-does"></a>Művelet

A Custom Vision service címkék alkalmazásához képek gépi tanulási algoritmust használ. A fejlesztői kell küldenie, amellyel funkciót, és nem áll rendelkezésre az a szóban forgó jellemzőkkel csoportok. Ön címkét saját kezűleg küldésének idején. Ezután az algoritmus betanítja ezekhez az adatokhoz, és kiszámítja a saját pontossága azonos rendszerképeket maga tesztelésével. Az algoritmus be van tanítva, miután tesztelése, újratanítása, és végül használja az alkalmazás igényeinek megfelelően új képek osztályozásához. A modellt exportálhatja offline használatra is.

### <a name="classification-and-object-detection"></a>Osztályozás és objektumészlelés

A Custom Vision funkciói két szolgáltatásra oszlanak. **Kép besorolási** kép egy vagy több címke vonatkozik. **Észlelési objektum** hasonló, de azt is koordinátáit adja vissza a képen az alkalmazott címkék találhatók.

### <a name="optimization"></a>Optimalizálás

A Custom Vision service gyorsan felismerni a rendszerképeket, így prototípus-készítés a modell egy kisebb mennyiségű adatot a fő különbségeit van optimalizálva. címke 50 képenként általában olyan remek kezdőpont. Azonban a szolgáltatás ne legyen optimális rendszerképek (például kisebb repedések vagy horpadásokra észlelése minőségi assurance forgatókönyvekben) finom eltérések észlelését.

Ezenkívül közül választhat az egyéni Látástechnológiai algoritmus optimalizált bizonyos tulajdonos anyagokat tartalmazó lemezképeket számos fajtáinak&mdash;például arcrész vagy kereskedelmi elemek. További információkért lásd: a [Tartalombesoroló létrehozása](getting-started-build-a-classifier.md) útmutató.

## <a name="what-it-includes"></a>Mit tartalmaz?

A Custom Vision Service natív SDK-k készleteként is elérhető egy webalapú felületen a [Custom Vision kezdőlapján](https://customvision.ai/). Létrehozása, tesztelése, és mindkét felületen keresztül a modell betanítását vagy is együtt használja.

![Custom Vision-kezdőlap egy Chrome-böngészőablakban](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Mint az összes, a Cognitive Services a Custom Vision service segítségével a fejlesztők célszerű tisztában lennie a házirendek a Microsoft-vásárlói adatokat. Tekintse meg a [Cognitive Services-lapra](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) a további Microsoft Trust Center.

## <a name="next-steps"></a>További lépések

Kövesse a [Tartalombesoroló létrehozása](getting-started-build-a-classifier.md) útmutató az első lépések a Custom Vision segítségével a weben, vagy fejezze be egy [kép besorolási oktatóanyag](csharp-tutorial.md) programkódban implementálni egy egyszerű forgatókönyvet.
