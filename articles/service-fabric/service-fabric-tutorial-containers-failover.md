---
title: "Feladatátvétel és az Azure Service Fabric-tárolók alkalmazások skálázása |} Microsoft Docs"
description: "Ismerje meg a feladatátvételi a rendszer hogyan kezelje az Azure Service Fabric-tárolók alkalmazásban.  Ismerje meg, a tárolók és a fürtben futó szolgáltatások méretezése is."
services: service-fabric
documentationcenter: 
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: "Docker, tárolók, mikroszolgáltatások létrehozására, a Service Fabric, Azure"
ms.assetid: 
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 21dd9dfbc90c26236c43e2c334305ca97f63d361
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="demonstrate-fail-over-and-scaling-of-container-services-with-service-fabric"></a>Sikertelen alatt és a Service Fabric tároló szolgáltatások méretezési bemutatása

Ez az oktatóanyag három egy sorozat része. Ebben az oktatóanyagban elsajátíthatja feladatátvételi rendszer hogyan kezelje a Service Fabric-tároló alkalmazások. Emellett megtanulhatja a tárolók skálázása. Ebben az oktatóanyagban az alábbiakat végezte el:

> [!div class="checklist"]
> * További tudnivalók a Service Fabric-fürt feladatátvétele tároló  
> * A webes előtér-tárolók egy kérelem beérkezésétől méretezése

## <a name="prerequisites"></a>Előfeltételek
Az alkalmazás [2. rész](service-fabric-tutorial-package-containers.md) futtatja a Service Fabric-fürt aktív.

## <a name="fail-over-a-container-in-a-cluster"></a>Tároló feladatátvétele a fürtben
A Service Fabric biztosítja, hogy a tároló példányok automatikusan áthelyezi a fürt többi csomópontjának hiba történjen. Manuálisan is üríthetik a csomópontokat, tároló, és azokat szabályosan áthelyezése a fürt többi tagján. A szolgáltatásokat többféleképpen is méretezheti – ebben a példában a Service Fabric Explorert használjuk.

Az előtértároló feladatátvételéhez hajtsa végre a következő lépéseket:

1. Nyissa meg a Service Fabric Explorert a fürtben – például: `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`.
2. Kattintson a a **fabric: / TestContainer/azurevotefront** csomópont a fanézetben a partíció csomópontot (GUID képviseli). Figyelje meg, a csomópont neve, a TreeView vezérlő, amely jelzi, hogy a csomópontok a tárolóban fut rajta – példa`_nodetype_1`
3. Bontsa ki a **csomópontok** a TreeView vezérlő csomópont. A három pont (három ponttal jelölt) kattintson a Tovább gombra a csomópont, amelyen a tárolót.
1. Válassza az **Újraindítás** lehetőséget a csomópont újraindításához, majd erősítse meg az újraindítási műveletet. Az újraindítás kikényszeríti a tároló feladatátvételét a fürt egy másik csomópontjára.

![noderestart][noderestart]

Figyelje meg, hogyan a csomópont neve, amely jelzi, ha fut az előtér-tárolókat, most módosítja a fürt egy másik csomópontjára. Néhány másodpercen belül megtekintheti az alkalmazásnak újra, és tekintse meg az alkalmazás most már fut egy másik csomópont kell lennie.

## <a name="scale-containers-and-services-in-a-cluster"></a>Skála tárolók és a szolgáltatások egy fürtben
A Service Fabric-tárolók a terhelést a szolgáltatások olyan fürtön belül is méretezhető. Egy tároló bővítse a fürtben futó példányok számának módosítása.

Méretezési előtér-webkiszolgáló, tegye a következőket:

1. Nyissa meg a Service Fabric Explorert a fürtben – például: `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`.
2. Mellett kattintson a három pont (három ponttal jelölt) a **fabric: / TestContainer/azurevotefront** a fa csomópontjához megtekintése, és válassza a **méretezési szolgáltatás**.

![sfxscale][sfxscale]

Választhat méretezési előtér-webkiszolgáló példányainak száma.

3. Módosítsa a számot **2**-re, és kattintson a **Szolgáltatás méretezése** gombra.
4. Kattintson a a **fabric: / TestContainer/azurevotefront** a fa csomópontjához megtekintése, és bontsa ki a partíció csomópontot (GUID képviseli).

![sfxscaledone][sfxscaledone]

Most láthatja, hogy a szolgáltatás két példánnyal rendelkezik. A fanézetben tekintse meg a példányok futtassa mely csomópontok.

Ezzel az egyszerű felügyeleti eljárással megdupláztuk az előtér-szolgáltatás számára a felhasználói terhelések feldolgozásához rendelkezésre álló erőforrások mennyiségét. Fontos megérteni, hogy nincs szükség több példányra ahhoz, hogy a szolgáltatás megbízhatóan fusson. Ha egy szolgáltatás meghibásodik, a Service Fabric gondoskodik róla, hogy egy új szolgáltatáspéldány kezdjen futni a fürtben.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban tároló feladatátvételi továbbá az alkalmazás skálázás lett mutatja. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * További tudnivalók a Service Fabric-fürt feladatátvétele tároló  
> * A webes előtér-tárolók egy kérelem beérkezésétől méretezése

Az oktatóanyag adatsorozat megtanulta, hogyan: 
> [!div class="checklist"]
> * Tároló képek létrehozása
> * Leküldéses tároló képek Azure tároló beállításjegyzék
> * A Service Fabric használatával Yeoman csomag tárolói
> * Hozza létre, és a tárolók a Service Fabric-alkalmazás futtatása
> * Hogyan történik meg a Service Fabric feladatátvételi és a méretezésről

[noderestart]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialnoderestart.png
[sfxscale]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscale.png
[sfxscaledone]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscaledone.png
