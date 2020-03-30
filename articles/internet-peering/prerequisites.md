---
title: A Microsoft segítségével történő társviszony-létesítés előfeltételei
titleSuffix: Azure
description: A Microsoft segítségével történő társviszony-létesítés előfeltételei
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3c820a7be561aeef9b7e50fd0ac0cf4dee721af8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775406"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>A Microsoft segítségével történő társviszony-létesítés előfeltételei

Győződjön meg arról, hogy az alábbi előfeltételek teljesülnek, mielőtt új társviszony-létesítést kérne, vagy egy örökölt társviszony-létesítést azure-erőforrássá alakítana át.

## <a name="azure-related-prerequisites"></a>Az Azure-ral kapcsolatos előfeltételek
* **Microsoft Azure-fiók:** Ha nem rendelkezik Microsoft Azure-fiókkal, hozzon létre egy [Microsoft Azure-fiókot.](https://azure.microsoft.com/free) A társviszony-létesítés beállításához érvényes és aktív Microsoft Azure-előfizetésre van szükség, mivel a társviszony-létesítések az Azure-előfizetéseken belüli erőforrásokként vannak modellezve. Fontos megjegyezni, hogy:
    * A társviszony-létesítés beállításához használt Azure-erőforrástípusok mindig ingyenes Azure-termékek, azaz nem kell fizetnie egy Azure-fiók létrehozásáért, vagy egy előfizetés létrehozásáért, vagy az Azure-erőforrások **eléréséhez PeerAsn** és **társviszony-létesítés** beállításához. Ez nem tévesztendő össze az Ön és a Microsoft közötti közvetlen társviszony-létesítésre vonatkozó társviszony-létesítési megállapodással, amelynek feltételeit kifejezetten megvitatja társviszony-létesítő csapatunk. Ha bármilyen kérdése van ebben a kérdésben, [forduljon a Microsoft társviszony-létesítési](mailto:peering@microsoft.com) kérdésének.
    * Ugyanazt az Azure-előfizetést használhatja más Azure-termékek vagy felhőszolgáltatások eléréséhez, amelyek ingyenesek vagy fizetősek lehetnek. Amikor egy fizetős termékhez fér hozzá, díjat kell fizetnie.
    * Ha új Azure-fiókot és/vagy előfizetést hoz létre, akkor egy próbaidőszak alatt jogosult lehet ingyenes Azure-kreditre, amelyet az Azure Cloud-szolgáltatások kipróbálásához használhatja. Ha érdekel, további információért látogasson el a [Microsoft Azure-fiókba.](https://azure.microsoft.com/free)

* **Társasa ASN társítása:** A társviszony-létesítés kérése előtt először társítsa asn-ját és kapcsolattartási adatait az előfizetéséhez. Kövesse a [TársASN társítása az Azure-előfizetéshez](howto-subscription-association-powershell.md)című útmutató utasításait.

## <a name="other-prerequisites"></a>Egyéb előfeltételek
* **Társviszony-létesítési db-profil:** A peeringdb-n a társaknak teljes [PeeringDB](https://www.peeringdb.com)és naprakész profillal kell rendelkezniük. Ezeket az információkat regisztrációs rendszerünkben arra használjuk, hogy érvényesítsük a partner adatait, például a NOC-adatokat, a műszaki elérhetőségi adatokat, valamint a társviszony-létesítési lehetőségekben való jelenlétüket stb.

## <a name="next-steps"></a>További lépések

* [Közvetlen társviszony-létesítés létrehozása vagy módosítása a portál használatával.](howto-direct-portal.md)
* [Örökölt közvetlen társviszony konvertálása Azure-erőforrássá a portál használatával](howto-legacy-direct-portal.md)
* [Exchange-társviszony-létesítés létrehozása vagy módosítása a portál használatával](howto-exchange-portal.md)
* [Örökölt Exchange-társviszony konvertálása Azure-erőforrássá a portál használatával](howto-legacy-exchange-portal.md)