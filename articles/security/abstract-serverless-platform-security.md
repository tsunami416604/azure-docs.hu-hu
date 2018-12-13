---
title: Absztrakt – az Azure Functions és a kiszolgáló nélküli platformon biztonság
description: Az Azure Functions kiszolgáló nélküli Platform biztonsági tanulmány az absztrakt.
author: TomShinder
ms.author: TomSh
ms.date: 06/21/2018
ms.topic: article
ms.service: security
ms.openlocfilehash: 87010b3256fb8eef6871d76f80db2999760386b4
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53320744"
---
# <a name="azure-functions-and-serverless-platform-security"></a>Az Azure Functions és a kiszolgáló nélküli platformon biztonság
## <a name="abstract"></a>Absztrakt
A legtöbb vállalkozás kell jelentős mennyiségű erőforrással és idővel a kiszolgálók, kezelése, amely hozzáadja a költségek. Ha a vállalatok kevesebb erőforrást segítségével felügyelheti a kiszolgálók, figyelmüket nagyszerű alkalmazások készítésére.  

Kiszolgáló nélküli számítástechnika segítségével éppen ezt, mert az alkalmazások futtatásához és méretezéséhez szükséges infrastruktúra van kezelve. A kiszolgáló nélküli számítástechnika a kiszolgálók, az infrastruktúra és az operációs rendszerek absztrakciója. Kiszolgáló nélküli számítástechnika, az események és eseményindítók hajtóereje van, melyek minden zajló közel valós időben – a felhőben. 

Teljes körűen felügyelt szolgáltatás a felügyeleti kiszolgáló és a kapacitás tervezése rejtve marad a fejlesztőnek. A kiszolgáló nélküli keretrendszer segítséget nyújt a fejlesztés és az Azure Functions szolgáltatással kiszolgáló nélküli alkalmazások központi telepítése. Olyan parancssori felületet (CLI) által kínált struktúra és az automation segítségével mikroszolgáltatásokból álló, functions és az események összetett, eseményvezérelt, kiszolgáló nélküli architektúrákat hozhat létre. Egy Azure-függvényt egy központi telepítési, például a mikroszolgáltatások független egység. Csupán kód, a felhőben, egy feladat végrehajtásához általában írt telepített.

Annak ellenére, hogy az előnnyel jár kiszolgáló nélküli biztonsági rendelkezik a saját kockázati tényezők alapján kezelésére. A kiszolgáló nélküli módszer nem vezeti be az új biztonsági problémákat, de a meglévő biztonsági aggályokat megközelítés hogy igényel. Ez a tanulmány ezeket a biztonsági kérdéseket összpontosít: 
* Kiszolgáló nélküli platformon előnyei
* A kiszolgáló nélküli számítástechnika biztonsági problémái
* Kritikus biztonsági problémák és megoldások a környezetében az Azure
* A Microsoft kiszolgáló nélküli platform biztonságossá tétele

[A tanulmány letöltése](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-functions-serverless-platform-security/Microsoft%20Serverless%20Platform.pdf)

