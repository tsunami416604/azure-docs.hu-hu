---
title: A csapatadat-elemzési folyamat életciklusának telepítési szakasza
description: Az adatelemzési projektek üzembe helyezési szakaszának céljai, feladatai és céljai
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1138c95274c769186a9a29aa4d35517e378baeae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720486"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>A csapatadat-elemzési folyamat életciklusának telepítési szakasza

Ez a cikk ismerteti a csapat adatelemzési folyamat (TDSP) telepítéséhez kapcsolódó célokat, feladatokat és eredményeket. Ez a folyamat egy ajánlott életciklust biztosít, amely az adatelemzési projektek strukturálásához használható. Az életciklus felvázolja azokat a főbb szakaszokat, amelyeket a projektek általában végrehajtanak, gyakran ismétlődően:

   1. **Üzleti ismertetés**
   2. **Adatgyűjtés és adatértelmezés**
   3. **Modellezés**
   4. **Környezet**
   5. **Felhasználói elfogadás**

Itt van egy vizuális ábrázolása a TDSP életciklus: 

![TDSP életciklus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cél
Az adatfolyamattal rendelkező modelleket éles vagy éles környezetben helyezheti üzembe a végső felhasználói elfogadás érdekében. 

## <a name="how-to-do-it"></a>Hogyan kell csinálni
Az ebben a szakaszban tárgyalt fő feladat:

**A modell üzembe helyezése:** Telepítse a modellt és a folyamatot egy éles vagy éles környezetben az alkalmazás felhasználása.

### <a name="operationalize-a-model"></a>Modell üzembe helyezése
Miután olyan modellkészletet rendelkezik, amely jól teljesít, üzembe hatja őket más alkalmazások számára. Az üzleti követelményektől függően az előrejelzések valós időben vagy kötegelve történnek. A modellek üzembe helyezéséhez egy nyílt API-felülettel teheti elérhetővé őket. A felület lehetővé teszi, hogy a modell könnyen fogyasztható a különböző alkalmazások, mint például:

   * Online weboldalak
   * Táblázatok 
   * Irányítópultok
   * Üzletági alkalmazások 
   * Háttéralkalmazások 

Példák a modell operationalization egy Azure Machine Learning webszolgáltatás, [az Azure Machine Learning webszolgáltatás telepítése.](../studio/deploy-a-machine-learning-web-service.md) Ajánlott eljárás telemetriai adatok at és figyelést az éles modell és az üzembe helyezett adatfolyamat. Ez a gyakorlat segít a rendszer állapotjelentésében és hibaelhárításában.  

## <a name="artifacts"></a>Összetevők

* Állapotirányítópult, amely a rendszer állapotát és a legfontosabb mutatókat jeleníti meg
* Végső modellezési jelentés az üzembe helyezés részleteivel
* Végső megoldásarchitektúra dokumentum


## <a name="next-steps"></a>További lépések

A TDSP életciklusának egyes lépéseire mutató hivatkozások:

   1. [Üzleti ismertetés](lifecycle-business-understanding.md)
   2. [Adatgyűjtés és megértés](lifecycle-data.md)
   3. [Modellezés](lifecycle-modeling.md)
   4. [Környezet](lifecycle-deployment.md)
   5. [Felhasználói elfogadás](lifecycle-acceptance.md)

Teljes forgatókönyveket biztosítunk, amelyek bemutatják a folyamat összes lépését az adott forgatókönyvekhez. A [Példa forgatókönyvek](walkthroughs.md) cikk hivatkozásokat és miniatűr leírásokat tartalmazó forgatókönyvek listáját tartalmazza. A forgatókönyvek bemutatják, hogyan kombinálhatja a felhőt, a helyszíni eszközöket és szolgáltatásokat egy munkafolyamatban vagy folyamatban egy intelligens alkalmazás létrehozásához. 

Példák az Azure Machine Learning Studio tdsp-kben végrehajtott lépések végrehajtásáról: [Use the TDSP with Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
