---
title: Az alkalmazás üzembe helyezése az Azure-ban az Habitat használatával
description: Ismerje meg, hogyan helyezheti üzembe az alkalmazást az Azure Virtual Machines és a containers szolgáltatásban
keywords: Azure, Chef, devops, Virtual Machines, áttekintés, automatizálás, Habitat
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: de444836c04d1d37a19a006ce4eafdcae867ca5a
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158056"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Az alkalmazás üzembe helyezése az Azure-ban az Habitat használatával
Az [Habitat](https://www.habitat.sh/) egy alkalmazás csomagolási és futtatókörnyezeti rendszere, amely az alkalmazást és annak automatizálását az üzembe helyezési egységként együtt csomagolja. Ez létrehoz egy végső hordozhatóságot az alkalmazás számára, amely lehetővé teszi, hogy a tárolók, a virtuális gépek, az operációs rendszer nélküli vagy a Pásti, újraírás vagy újracsomagolás nélkül legyen üzembe helyezhető.

Ez a cikk az Habitat használatának legfőbb előnyeit ismerteti.

## <a name="modernize-and-move-legacy-applications"></a>Örökölt alkalmazások modernizálása és áthelyezése
A régebbi operációs rendszerekről és a middleware-ből származó örökölt alkalmazásokat az élőhelyekkel való újracsomagolással távolíthatja el. Az eredményül kapott összetevő hordozható, és az új infrastruktúrára, például a felhőben futó virtuális gépekre vagy tárolóra is könnyen újraplatformra kerül.

## <a name="accelerate-container-adoption"></a>A Container bevezetésének gyorsítása
Az élőhely megoldja a komplex, a Service-orientált alkalmazások folyamatos üzembe helyezését azáltal, hogy pontosan a futásidejű függőségeket jelképezi. Az egyszerű kék/zöld üzembe helyezést az egyes összetevők és az építész kifinomult telepítési viselkedése nélkül helyezheti el anélkül, hogy bonyolult előkészítési folyamatokat kellene létrehozni.

## <a name="run-any-application-anywhere"></a>Tetszőleges alkalmazás futtatása bárhol
Az élőhelyek esetében az alkalmazások bármely futásidejű környezetben futtathatják a módosítást. Ez magában foglalja az operációs rendszer nélküli számítógépekről és a virtuális gépekről a tárolók (például a Docker), a fürt-felügyeleti rendszerek (például a Mesosphere vagy a Kubernetes) és a Péter rendszerek (például a Pivotal Cloud Foundry) összes elemét.

## <a name="integrate-into-the-chef-devops-workflow"></a>Integrálás a Chef DevOps-munkafolyamatba
Az Habitat projekt a Chef Software egyik nyílt forráskódú projektje, amely erős Közösség támogatásával rendelkezik. Az élőhely kihasználja a Chef mély tapasztalatát az infrastruktúra automatizálásával, hogy példátlan automatizálási képességeket hozzon az alkalmazások számára. A Chef kereskedelmi támogatást nyújt az élőhelyekhez, és zökkenőmentes integrációt biztosít az élőhelyek és a Chef automatizálása között, így teljes mértékben automatizálhatja az alkalmazás kiadási ciklusát a fejlesztéstől a üzembe helyezésig

## <a name="next-steps"></a>Következő lépések

* [Az élőhely kipróbálása](https://www.habitat.sh/learn/)
