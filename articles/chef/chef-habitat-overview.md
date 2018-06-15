---
title: Élőhely segítségével az Azure alkalmazás telepítése
description: Az alkalmazás számára az Azure virtuális gépek és a tárolók következetesen telepítése
keywords: Azure, a chef, a devops, a virtuális gépek, a áttekintése, automatizálhatja, élőhely
ms.service: virtual-machines-linux
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 73c6834eb53c0496fa673dd25ab90abc18a6a139
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267445"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Élőhely segítségével az Azure alkalmazás telepítése
[Élőhely](https://www.habitat.sh/) egy első a kind nyílt forráskódú projekt, amely egy teljesen új Alkalmazáskezelés módjáról nyújt. Élőhely lehetővé teszi az alkalmazás és az automatizálás a telepítési egység, amely. Alkalmazások vannak egy egyszerűsített "élőhely" csomagolni, amikor a futásidejű környezetre, hogy a tároló, operációs rendszer nélküli vagy PaaS,-e már nincs a fókusz, és nem korlátozhatják az alkalmazás. 

Ez a cikk ismerteti a élőhely használatának előnyeit.

## <a name="support-for-the-modern-application"></a>A modern alkalmazások támogatása
Élőhely csomagokat tartalmaz mindent, az alkalmazás teljes életciklus futtatásához szükséges. Élőhely alapvető összetevői a következők:
- Csomagolás-formátum – alkalmazások élőhely csomagban atomi, nem módosítható és naplózható.
- A élőhely felügyelő alkalmazáscsomagok fut, a csomagok társ meg kapcsolatokat, a frissítési stratégia és biztonsági házirendjeivel. A élőhely felügyelő konfigurálja és kezeli az alkalmazás abban a környezetben nem található.
- A élőhely ökoszisztéma is biztosít a szolgáltatás egy élőhely terv veszi, a élőhely csomagot hoz létre, és közzéteszi azokat egy raktár számára.

## <a name="run-any-application-anywhere"></a>Bármely alkalmazás bárhol futtatása
Élőhely, az alkalmazások bármely futtatókörnyezetben változatlan futtathatja. Ez tartalmaz mindent, ami az operációs rendszer nélküli és virtuális gépek tárolók (például a Docker), fürt-felügyeleti rendszerekhez (például a Mesosphere vagy Kubernetes) és PaaS rendszerek (például felhő Foundry döntő).

## <a name="easily-port-legacy-applications"></a>Könnyen port régebbi alkalmazásokhoz
Örökölt alkalmazások van burkolva élőhely csomagban, amikor az alkalmazások függetlenek a környezet, amely eredetileg tervezték őket. A csomagok gyorsan áthelyezhető korszerűbb környezetekben, például a felhő vagy a tárolókat. Emellett élőhely csomagok rendelkezik egy szabványos, kifelé irányuló felületet, mert örökölt alkalmazások vált sokkal könnyebben kezelhető.

## <a name="improve-the-container-experience"></a>A tároló-élmény javítása
Élőhely egyszerűbben éles környezetben tárolók kezelése. A tárolóban lévő Alkalmazáskonfiguráció automatizálásával élőhely a kihívásokkal fejlesztők arcfelismerési megoldást, amikor tároló-alapú alkalmazások fejlesztői környezetben éles környezetben.

## <a name="integrate-into-the-chef-devops-workflow"></a>Integrálja a Chef DevOps munkafolyamat
A élőhely projekt által Chef támogat. Élőhely kihasználja a mély élmény Chef meg az infrastruktúra Automation szolgáltatásban, hogy alkalmazások egyedülálló automatizálási lehetőségek körét. Chef fog élőhely kereskedelmi támogatása kínálnak, és élőhely és Chef kézbesítési teljesen automatizálhatja az alkalmazás kiadási telepítéshez fejlesztési ciklus gördülékeny integrációjával biztosítja.

## <a name="next-steps"></a>További lépések
* [Windows virtuális gép létrehozása Azure-ban Chef](/azure/virtual-machines/windows/chef-automation)