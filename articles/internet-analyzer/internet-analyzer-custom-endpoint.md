---
title: Egyéni végpont létrehozása | Microsoft dokumentumok
description: Ebből a cikkből megtudhatja, hogyan konfigurálhat egyéni végpontot az Internetes elemző erőforrással való méréshez.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 6a781e767393f35a1ec22f195ba787e3018d8d1e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76713097"
---
# <a name="measure-custom-endpoints-to-evaluate-in-your-internet-analyzer-tests"></a>Egyéni végpontok mérése az Internetes elemző tesztekben 

Ez a cikk bemutatja, hogyan állíthat be egy egyéni végpontot az Internetes elemző tesztek részeként mérhető. Az egyéni végpontok segítenek kiértékelni a helyszíni számítási feladatokat, a más felhőszolgáltatókon futó számítási feladatokat és az egyéni Azure-konfigurációkat.  Két egyéni végpont oka egy tesztben lehetséges, ha az egyik végpont egy Azure-erőforrás. Az Internet Analyzer programról további információt az [áttekintésben talál.](internet-analyzer-overview.md) 

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy beállított egy Internetes elemző erőforrást, és válassza az "Egyéni végpont" lehetőséget. Az Internet Analyzer feltételezi, hogy az egyéni végpont internetről elérhető. További információt az [Internet Analyzer Resource létrehozása című témakörben talál.](internet-analyzer-create-test-portal.md)


## <a name="create-custom-endpoint"></a>Egyéni végpont létrehozása

1. Töltsön le egy átlátszó egypixeles tesztképet [itt](https://fpc.msedge.net/apc/trans.gif). Ez az egy képpontméretű kép az az eszköz, amelyet az ügyfél JavaScript-je lehív a teljesítmény mérésére.
2. Az egyéni webalkalmazásban telepítse a tesztlemezképet egy nyilvánosan elérhető elérési úton. Az elérési útnak HTTPS-en keresztül kell működnie. 
3. Másolja a teljes egyéni végpont URL-címét (például https://contoso.com/test/trans.gif) az egyéni végpontmezőbe a teszt létrehozása során.

## <a name="next-steps"></a>További lépések

Olvassa el az [Internet Analyzer GYIK-et](internet-analyzer-faq.md)

