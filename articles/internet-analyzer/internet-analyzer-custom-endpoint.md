---
title: Egyéni végpont létrehozása | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan konfigurálhat egyéni végpontot az Internet Analyzer-erőforrással való méréshez.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 6a781e767393f35a1ec22f195ba787e3018d8d1e
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76713097"
---
# <a name="measure-custom-endpoints-to-evaluate-in-your-internet-analyzer-tests"></a>Az Internet Analyzer-tesztek alapján kiértékelt egyéni végpontok mérése 

Ez a cikk bemutatja, hogyan állíthat be egyéni végpontot az Internet Analyzer-tesztek részeként. Az egyéni végpontok segítségével kiértékelheti a helyszíni számítási feladatokat, a más felhőalapú szolgáltatókon futó munkaterheléseket és az egyéni Azure-konfigurációkat.  Két egyéni végpont összevetése egy tesztben lehetséges, ha az egyik végpont egy Azure-erőforrás. Az Internet Analyzer szolgáltatással kapcsolatos további információkért tekintse meg az [áttekintést](internet-analyzer-overview.md). 

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Előzetes teendők

Győződjön meg arról, hogy létrehoz egy Internet Analyzer-erőforrást, és válassza az "egyéni végpont" lehetőséget. Az Internet Analyzer feltételezi, hogy az egyéni végpont internetes elérhető. További információt az [Internet Analyzer-erőforrás létrehozása](internet-analyzer-create-test-portal.md)című témakörben talál.


## <a name="create-custom-endpoint"></a>Egyéni végpont létrehozása

1. [Itt](https://fpc.msedge.net/apc/trans.gif)töltheti le az átlátszó, egyképpontos tesztelési képet. Ez az egyképpontos rendszerkép az az objektum, amelyet a JavaScript a teljesítmény mérésére fog kérni.
2. Az egyéni webalkalmazásban telepítse a teszt lemezképet egy nyilvánosan elérhető elérési úton. Az elérési útnak HTTPS-kapcsolaton keresztül kell működnie. 
3. Másolja a teljes egyéni végpont URL-címét (például https://contoso.com/test/trans.gif) az egyéni végpont mezőbe a tesztelés létrehozásakor.

## <a name="next-steps"></a>Következő lépések

Az [Internet Analyzer gyakori kérdéseinek](internet-analyzer-faq.md) beolvasása

