---
title: Cloud Partner Portal API-hivatkozás | Azure piactér
description: A, a használatának előfeltételei és a piactér API-műveleteinek listája.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: b6591e1780d03cbfaff70fbd19ec3dfd274fae79
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819629"
---
<a name="cloud-partner-portal-api-reference"></a>Cloud Partner Portal API-hivatkozás
==================================

A Cloud Partner Portal REST API-k lehetővé teszik a számítási feladatok, ajánlatok és kiadói profilok programozott lekérését és kezelését. Az API-k szerepköralapú hozzáférés-vezérlés (RBAC) használatával kényszerítik ki a megfelelő engedélyeket a feldolgozási idő során.

Ez a hivatkozás a Cloud Partner Portal REST API-k technikai részleteit ismerteti. A dokumentumban szereplő hasznos adatok csak referenciául szolgálnak, és új funkciók hozzáadásakor változhatnak.


<a name="prerequisites-and-considerations"></a>Előfeltételek és megfontolások
-------------------------------

Az API-k használata előtt tekintse át a következőket:

- Az [Előfeltételek](./cloud-partner-portal-api-prerequisites.md) című cikkből megtudhatja, hogyan adhat hozzá egy egyszerű szolgáltatásnevet a fiókjához, és hogyan szerezhet be Azure Active Directory (Azure ad) hozzáférési tokent a hitelesítéshez. 
- A két [Egyidejűség vezérlőelem](./cloud-partner-portal-api-concurrency-control.md).
az API-k meghívásához elérhető stratégiák.
- További API- [megfontolások](./cloud-partner-portal-api-considerations.md), például verziószámozás és hibakezelés.


<a name="common-tasks"></a>Gyakori feladatok
------------
Ez a hivatkozás a következő gyakori feladatok végrehajtására szolgáló API-kat ismerteti.


### <a name="offers"></a>Ajánlatok

-   [Az összes ajánlat beolvasása](./cloud-partner-portal-api-retrieve-offers.md)
-   [Adott ajánlat beolvasása](./cloud-partner-portal-api-retrieve-specific-offer.md)
-   [Ajánlat állapotának lekérése](./cloud-partner-portal-api-retrieve-offer-status.md)
-   [Ajánlat létrehozása](./cloud-partner-portal-api-creating-offer.md)
-   [Ajánlat közzététele](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Műveletek

-   [Műveletek lekérése](./cloud-partner-portal-api-retrieve-operations.md)
-   [Szalagműveletek megszakítása](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Alkalmazás közzététele

-   [Élesítés](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Egyéb feladatok

-   [A virtuális gépekre vonatkozó ajánlatok díjszabásának beállítása](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Hibaelhárítás

-   [Hitelesítési hibák hibaelhárítása](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
