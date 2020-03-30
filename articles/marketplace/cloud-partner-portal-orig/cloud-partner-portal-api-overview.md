---
title: Cloud Partner Portal API –hivatkozás | Azure Piactér
description: A használandó előfeltételek és a piactéri API-műveletek listája.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 296c667876807bdd05a6281de461df76207b5490
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288546"
---
<a name="cloud-partner-portal-api-reference"></a>Felhőpartneri portál API-útmutatója
==================================

A Cloud Partner Portal REST API-k lehetővé teszik a számítási feladatok, ajánlatok és közzétevői profilok programozott lekérését és kezelését. Az API-k szerepköralapú hozzáférés-vezérlés (RBAC) használatával kényszerítik a megfelelő engedélyeket a feldolgozási idő alatt.

Ez a hivatkozás a Cloud Partner Portal REST API-k technikai részleteit tartalmazza. A dokumentumban szereplő hasznos teherminták csak tájékoztató jellegűek, és az új funkciók hozzáadásával változhatnak.


<a name="prerequisites-and-considerations"></a>Előfeltételek és szempontok
-------------------------------

Az API-k használata előtt tekintse át a következőket:

- [Az előfeltételek](./cloud-partner-portal-api-prerequisites.md) cikk megtudhatja, hogyan adhat hozzá egy egyszerű szolgáltatás a fiókjához, és egy Azure Active Directory (Azure AD) hozzáférési jogkivonatot a hitelesítéshez. 
- A két [egyidejűség-ellenőrzés](./cloud-partner-portal-api-concurrency-control.md).
api-k hívására rendelkezésre álló stratégiákat.
- További [API-szempontok](./cloud-partner-portal-api-considerations.md), például verziószámozás és hibakezelés.


<a name="common-tasks"></a>Gyakori feladatok
------------
Ez a hivatkozás a következő gyakori feladatok végrehajtásához szükséges API-kat részletezi.


### <a name="offers"></a>Ajánlatok

-   [Az összes ajánlat lekérése](./cloud-partner-portal-api-retrieve-offers.md)
-   [Adott ajánlat lekérése](./cloud-partner-portal-api-retrieve-specific-offer.md)
-   [Ajánlat állapotának lekérése](./cloud-partner-portal-api-retrieve-offer-status.md)
-   [Ajánlat létrehozása](./cloud-partner-portal-api-creating-offer.md)
-   [Ajánlat közzététele](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Műveletek

-   [Műveletek lekérése](./cloud-partner-portal-api-retrieve-operations.md)
-   [Szalagműveletek megszakítása](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Alkalmazás közzététele

-   [Élesítés](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Egyéb feladatok

-   [A virtuális gépi ajánlatok díjszabásának beállítása](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Hibaelhárítás

-   [Hitelesítési hibák hibaelhárítása](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
