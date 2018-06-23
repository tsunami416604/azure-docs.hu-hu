---
title: Az Eszközintelligencia szolgáltatás API Linking entitás |} Microsoft Docs
description: Megtudhatja, hogyan entitás csatolása az Eszközintelligencia szolgáltatás API-t használó kognitív szolgáltatások feladatainak entitás csatolása.
services: cognitive-services
author: DavidLiCIG
manager: wkwok
ms.service: cognitive-services
ms.component: entity-linking-intelligence
ms.topic: article
ms.date: 03/28/2016
ms.author: davl
ms.openlocfilehash: 662295c42a421fe76892e6e5aac7b7e7178d6155
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347006"
---
# <a name="entity-linking-intelligence-service-api"></a>Entity Linking Intelligence Service API

A Microsoft entitás Linking az Eszközintelligencia szolgáltatás, egy webszolgáltatás-bővítmény létrehozásával elősegítheti a fejlesztők a feladatokkal kapcsolatos entitás csatolása Üdvözöljük.

## <a name="entity-linking"></a>Entitáskapcsolás

Néha különböző környezetekben, a word előfordulhat, hogy használható névvel rendelkező entitás, olyan műveleteket, vagy más word belül egy adott mondat helyett szerepel. Például olyan esetben, amikor a „times” szó valamilyen elnevezett entitást jelent, előfordulhat, hogy két különböző entitásra is utal, például a „The New York Times” vagy a „Times Square” kifejezésekre. Adott dokumentum adott bekezdést, a entitás csatolása az Eszközintelligencia szolgáltatás ismeri fel, és minden különálló entitás környezete alapján azonosíthatja.  

Az alábbi ábra azt szemlélteti, entitás csatolása. Pontosabban Wikipedia használata esetén az entitás csatolása az Eszközintelligencia szolgáltatás észleli a bemeneti szöveg belül említett összes entitás és összekapcsolja azokat megfelelő hivatkozási pontok megfelelően a lap azonosítóját.

 ![A Mars entitás Linking minta](./Images/EntityLinkingSample1.png)
 
## <a name="get-started"></a>Első lépések
 
Gyorsan haladhat végig az alapvető funkciók entitás csatolása és előfizetési folyamat, tekintse meg az alapszintű bemutató.
[Ismerkedés a C# API Linking entitás](GettingStarted.md)


