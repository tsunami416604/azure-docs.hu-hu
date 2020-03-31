---
title: Videoindexelő feladatátvétel és vész-helyreállítás
titleSuffix: Azure Media Services
description: Megtudhatja, hogyan feladatátvétel egy másodlagos videoindexer-fiók, ha egy regionális adatközpont hiba vagy katasztrófa történik.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499617"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>Videoindexelő feladatátvétel és vész-helyreállítás

Az Azure Media Services videoindexelő nem biztosít azonnali feladatátvételt a szolgáltatás, ha van egy regionális adatközpont kimaradás vagy hiba. Ez a cikk bemutatja, hogyan konfigurálhatja a környezetet feladatátvételre az alkalmazások optimális rendelkezésre állásának biztosítása érdekében, és a minimális helyreállítási idő, ha katasztrófa történik.

Azt javasoljuk, hogy konfigurálja az üzletmenet-folytonossági vész-helyreállítási (BCDR) a regionális párok között, hogy kihasználhassa az Azure elkülönítési és rendelkezésre állási szabályzatait. További információ: [Azure párosított régiók](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="prerequisites"></a>Előfeltételek

Azure-előfizetés. Ha még nem rendelkezik Azure-előfizetéssel, regisztráljon az [Ingyenes Azure-próbaverzióra.](https://azure.microsoft.com/free/)

## <a name="failover-to-a-secondary-account"></a>Feladatátvétel másodlagos fiókba

A BCDR megvalósításához két videoindexelő-fiókkal kell rendelkeznie a redundancia kezeléséhez.

1. Hozzon létre két, az Azure-hoz kapcsolódó Video [Indexer-fiókot (lásd: Videoindexer-fiók létrehozása).](connect-to-azure.md) Hozzon létre egy fiókot az elsődleges régióhoz, a másikat pedig a párosított azure-régióhoz.
1. Ha az elsődleges régióban hiba van, váltson indexelésre a másodlagos fiók használatával.

> [!TIP]
> A BCDR automatizálásához a szolgáltatásállapot-értesítések tevékenységnapló-értesítéseinek beállításával a [tevékenységnapló-értesítések létrehozása szerint.](../../service-health/alerts-activity-log-service-notifications.md)

Több bérlő használatáról a [Több bérlő kezelése című témakörben talál.](manage-multiple-tenants.md) A BCDR megvalósításához válasszon a következő két lehetőség közül: [Video Indexer-fiók bérlőnként](manage-multiple-tenants.md#video-indexer-account-per-tenant) vagy [Azure-előfizetés bérlőnként.](manage-multiple-tenants.md#azure-subscription-per-tenant)

## <a name="next-steps"></a>További lépések

[Az Azure-hoz csatlakoztatott Video Indexer-fiók kezelése.](manage-account-connected-to-azure.md)
