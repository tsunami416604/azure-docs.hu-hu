---
title: Az alkalmazás telepítése az Azure a Habitat használatával
description: Ismerje meg, hogyan következetesen az alkalmazás telepítése az Azure-beli virtuális gépek és tárolók
keywords: az Azure, a chef, a devops, a virtuális gépek, a áttekintése, automatizálás, a habitat használatával
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 2bdcd4c504822a2e60156b0ac565465e0cf23a85
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358524"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Az alkalmazás telepítése az Azure a Habitat használatával
[Habitat](https://www.habitat.sh/) az alkalmazás csomagolás és a futtatókörnyezet rendszer, amely az alkalmazás és az automation együtt üzembe helyezési egységként bundles. Ezzel létrehozza az alkalmazás, így lehetővé teszi egy újraírási nélkül telepíthetők tárolók, virtuális gépek, operációs rendszer nélküli vagy PaaS, vagy újracsomagolás ultimate hordozhatóságát.

Ez a cikk ismerteti a Habitat használatával fő előnyei.

## <a name="modernize-and-move-legacy-applications"></a>Végezzen modernizálást és örökölt alkalmazások áthelyezése
Ingyenes örökölt alkalmazások a régebbi operációs rendszereket és közbenső szoftver által újracsomagolás őket a habitat használatával. Az eredményül kapott összetevőben hordozható, és könnyedén replatforms újabb infrastruktúra-kiszolgálóra, például virtuális gépeket vagy a felhőben futó tárolók.

## <a name="accelerate-container-adoption"></a>Gyorsabb tároló elfogadása
Habitat megoldja a folyamatos üzembe helyezés, az összetett, mikroszolgáltatás-alapú alkalmazások pontosan jelölő futtatókörnyezeti függőségek szerint. Túl az egyes összetevők kék vagy zöld üzembe helyezni és bonyolult összehangolásra folyamatok létrehozása nélkül kifinomult központi telepítés működése tervezhet.

## <a name="run-any-application-anywhere"></a>Alkalmazások futtatása bárhol
A habitat használatával nem módosított semmilyen futtatókörnyezetben futtatható alkalmazások. Ez mindent tartalmaz, az operációs rendszer nélküli és virtuális gépek tárolók (például a Dockert), fürt-kezelő rendszerek (például a Mesosphere vagy Kubernetes) és PaaS rendszereket (ilyen például a Pivotal Cloud Foundry-hoz).

## <a name="integrate-into-the-chef-devops-workflow"></a>A Chef DevOps-munkafolyamatban integrálása
A Habitat-projektet az egyik egy nyílt forráskódú projektje, a Chef szoftverhez, egy erős közösségi támogatás. Habitat kihasználja a Chef's tapasztalattal az infrastruktúra-automatizálást ahhoz, hogy alkalmazásokat páratlan automatizálási képességeivel. Chef kereskedelmi támogatja a Habitat és összeállít egy zökkenőmentes integráció a Habitat és a Chef Automate automatizálásához a fejlesztéstől az üzembe helyezés az alkalmazás kiadási ciklus között.

## <a name="next-steps"></a>További lépések

* [Próbálja ki a habitat használatával](https://www.habitat.sh/learn/)
