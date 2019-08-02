---
title: Video Indexer Üzletmenet-folytonosság és vész-helyreállítási (BCDR) – Azure | Microsoft Docs
description: Megtudhatja, hogyan hajthat végre feladatátvételt másodlagos Video Indexer-fiókra, ha regionális adatközpont-leállás vagy-meghibásodás történik.
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
ms.openlocfilehash: ce788b4640f0a6c6f25b3280ce4f52fd018d1699
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668306"
---
# <a name="handle-video-indexer-business-continuity-and-disaster-recovery"></a>Kezelheti Video Indexer üzletmenet folytonosságát és a vész-helyreállítást

A Azure Media Services Video Indexer nem biztosít azonnali feladatátvételt a szolgáltatáshoz, ha van regionális adatközpont-kimaradás vagy meghibásodás. Ez a cikk azt ismerteti, hogyan konfigurálhatja a környezetet a feladatátvételhez, így biztosítva az alkalmazások optimális rendelkezésre állását és a lehető legkevesebb helyreállítási időt, ha katasztrófa következik be.

Javasoljuk, hogy az üzletmenet-folytonossági vész-helyreállítást (BCDR) a regionális párokban konfigurálja az Azure elkülönítési és rendelkezésre állási házirendjeinek kihasználása érdekében. További információ: [Azure párosított régiók](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="prerequisites"></a>Előfeltételek 

Azure-előfizetés. Ha még nem rendelkezik Azure-előfizetéssel, regisztráljon az [Azure ingyenes próbaverzióra](https://azure.microsoft.com/free/).

## <a name="failover-to-a-secondary-account"></a>Feladatátvétel másodlagos fiókba

A BCDR megvalósítása érdekében két Video Indexer fiókkal kell rendelkeznie a redundancia kezeléséhez.

1. Hozzon létre két Video Indexer fiókot az Azure-hoz csatlakoztatva (lásd: [fiókok létrehozása](connect-to-azure.md)). Egyet az elsődleges régióhoz, a másikat pedig a párosított Azure-régióhoz. 
1. Ha az elsődleges régióban hiba történt, váltson át az indexelésre a másodlagos fiók használatával.

> [!TIP]
> A BCDR automatizálásához állítsa be a tevékenységek naplójának riasztásait a szolgáltatás állapotával kapcsolatos értesítésekhez, mint a [szolgáltatási értesítéseken a létrehozási tevékenység naplójának riasztásai](../../service-health/alerts-activity-log-service-notifications.md).

További információ a több bérlő használatáról: [több bérlő kezelése](manage-multiple-tenants.md). A BCDR megvalósításához válasszon a következő két lehetőség közül: [Video Indexer fiók/](manage-multiple-tenants.md#video-indexer-account-per-tenant) bérlő vagy [Azure-előfizetés](manage-multiple-tenants.md#azure-subscription-per-tenant)

## <a name="next-steps"></a>További lépések

Az [Azure-hoz csatlakoztatott video Indexer-fiók kezelése](manage-account-connected-to-azure.md).
