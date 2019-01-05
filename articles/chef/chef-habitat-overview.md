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
ms.openlocfilehash: 4847d9ce551c9acf1e4fb6325c770187b2cfd89f
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052291"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Az alkalmazás telepítése az Azure a Habitat használatával
[Habitat](https://www.habitat.sh/) van egy először az altípus nyílt forráskódú projekt, amely egy teljesen új megközelítést kínál az alkalmazások kezelése. Habitat teszi az alkalmazás és az automation az egység a központi telepítés. Alkalmazások egy egyszerű "habitat" burkolja, ha a futtatási környezetet egy tároló, operációs rendszer nélküli vagy PaaS, akár már nem a fókuszt, és nem korlátozza az alkalmazás. 

Ez a cikk ismerteti a Habitat használatával járó előnyöket.

## <a name="support-for-the-modern-application"></a>A probléma a modern alkalmazások támogatása
Habitat-csomagok közé tartozik minden olyan alkalmazás teljes életciklusa során futtatásához szükséges. A habitat alapvető összetevői a következők:
- Csomagolási formátumot - alkalmazások, a Habitat csomag: atomi, nem módosítható és naplózható.
- A Habitat felügyelő alkalmazáscsomagok fut, a csomagok társ kontrollálhassa kapcsolatok, a frissítési stratégia és biztonsági házirendjeivel. A Habitat felügyelő konfigurálja és felügyeli az alkalmazást a megtalálható bármilyen környezetben.
- A Habitat-ökoszisztéma is biztosít egy build service, a Habitat buildcsomag vesz igénybe, a Habitat csomagot hoz létre, és közzéteszi azt egy raktár számára.

## <a name="run-any-application-anywhere"></a>Alkalmazások futtatása bárhol
A habitat használatával nem módosított semmilyen futtatókörnyezetben futtatható alkalmazások. Az alkalmazás képes bármi lehet, az operációs rendszer nélküli és virtuális gépek tárolók (például a Dockert), fürt-kezelő rendszerek (például a Mesosphere vagy Kubernetes) és PaaS rendszereket (ilyen például a Pivotal Cloud Foundry-hoz).

## <a name="easily-port-legacy-applications"></a>Könnyedén port örökölt alkalmazások
Örökölt alkalmazások a Habitat csomag burkolja, amikor az alkalmazások függetlenek a környezet, amely eredetileg tervezték őket. A csomagok gyorsan áthelyezhetők korszerűbb környezetekben, például a felhőben és a tárolók. Is mivel a Habitat-csomagok vannak egy standard szintű és kifelé irányuló felületen, örökölt alkalmazások válnak sokkal könnyebben kezelhető.

## <a name="improve-the-container-experience"></a>A tároló élmény javítása érdekében
Habitat kezelése a tárolók éles környezetben bonyolultságát is csökkenti. Alkalmazáskonfigurációs a tárolókon belül automatizálásával a Habitat a kihívások fejlesztők face címeket a tárolóalapú alkalmazások fejlesztési környezetből éles környezetbe történő áthelyezés során.

## <a name="integrate-into-the-chef-devops-workflow"></a>A Chef DevOps-munkafolyamatban integrálása
A Habitat projekt által Chef támogat. Habitat kihasználja a Chef's tapasztalattal az infrastruktúra-automatizálást ahhoz, hogy alkalmazásokat páratlan automatizálási képességeivel. Chef lesz a Habitat kereskedelmi támogatást, és győződjön meg, hogy a Habitat és a Chef Delivery alkalmazás kiadási ciklus a fejlesztéstől az üzembe helyezés automatizálása zökkenőmentes integrációjával.

## <a name="next-steps"></a>További lépések
* [Windows virtuális gép létrehozása Chef használatával Azure-ban](/azure/virtual-machines/windows/chef-automation)