---
title: Feladatátvételi és vész-helyreállítási Video Indexer
titleSuffix: Azure Media Services
description: Megtudhatja, hogyan hajthat végre feladatátvételt másodlagos Video Indexer-fiókra, ha regionális adatközpont-meghibásodás vagy katasztrófa következik be.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: 17c21900448fcb6d0a40fe5407f3b8bd62f9e3e4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79499617"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>Feladatátvételi és vész-helyreállítási Video Indexer

Azure Media Services Video Indexer nem biztosít azonnali feladatátvételt a szolgáltatáshoz, ha van regionális adatközpont-kimaradás vagy meghibásodás. Ez a cikk azt ismerteti, hogyan konfigurálhatja a környezetet a feladatátvételhez, így biztosítva az alkalmazások optimális rendelkezésre állását és a lehető legkevesebb helyreállítási időt, ha katasztrófa következik be.

Javasoljuk, hogy az üzletmenet-folytonossági vész-helyreállítást (BCDR) a regionális párokban konfigurálja az Azure elkülönítési és rendelkezésre állási házirendjeinek kihasználása érdekében. További információ: [Azure párosított régiók](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="prerequisites"></a>Előfeltételek

Azure-előfizetés. Ha még nem rendelkezik Azure-előfizetéssel, regisztráljon az [Azure ingyenes próbaverzióra](https://azure.microsoft.com/free/).

## <a name="failover-to-a-secondary-account"></a>Feladatátvétel másodlagos fiókba

A BCDR megvalósításához két Video Indexer fiókkal kell rendelkeznie a redundancia kezeléséhez.

1. Hozzon létre két Video Indexer fiókot az Azure-hoz csatlakoztatva (lásd: [video Indexer fiók létrehozása](connect-to-azure.md)). Hozzon létre egy fiókot az elsődleges régióhoz, a másikat pedig a párosított Azure-régióhoz.
1. Ha az elsődleges régióban hiba történt, váltson át az indexelésre a másodlagos fiók használatával.

> [!TIP]
> A BCDR automatizálásához állítsa be a tevékenységek naplójának riasztásait a szolgáltatás állapotával kapcsolatos értesítésekhez, mint a [szolgáltatási értesítéseken a létrehozási tevékenység naplójának riasztásai](../../service-health/alerts-activity-log-service-notifications.md).

További információ a több bérlő használatáról: [több bérlő kezelése](manage-multiple-tenants.md). A BCDR megvalósításához válasszon egyet a következő két lehetőség közül: [video Indexer fiók/bérlő](manage-multiple-tenants.md#video-indexer-account-per-tenant) vagy [Azure-előfizetés bérlőn](manage-multiple-tenants.md#azure-subscription-per-tenant).

## <a name="next-steps"></a>További lépések

Az [Azure-hoz csatlakoztatott video Indexer-fiók kezelése](manage-account-connected-to-azure.md).
