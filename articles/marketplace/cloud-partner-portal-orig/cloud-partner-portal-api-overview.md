---
title: Cloud Partner Portal API-referencia |} Az Azure Marketplace-en
description: Leírása, használatának előfeltételei és marketplace API műveletek listája.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 9626fdb5dbdf720a65217421565fc559a0cee684
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935473"
---
<a name="cloud-partner-portal-api-reference"></a>Cloud Partner Portal API-referencia
==================================

A Cloud Partner Portal REST API-k lehetővé teszik a programozott lekérés és a számítási feladatok, ajánlatok és közzétevő profil kezelése. Az API-k szerepköralapú hozzáférés-vezérlés (RBAC) használatával a megfelelő engedélyekkel a kényszerítése, feldolgozási ideje.

Ez az útmutató a Cloud Partner Portal REST API-k számára a technikai részleteket ismertet. Ebben a dokumentumban a payload minták csak referenciaként szolgálnak, és módosulhatnak, új szolgáltatásokat adunk hozzá.


<a name="prerequisites-and-considerations"></a>Előfeltételek és szempontok
-------------------------------

Mielőtt az API-kat használja, tekintse át:

- A [Előfeltételek](./cloud-partner-portal-api-prerequisites.md) hozzáadása egy egyszerű szolgáltatást a fiókjához további cikk és az Azure Active Directory (Azure AD) hozzáférési jogkivonatot kapjon a hitelesítéshez. 
- A két [egyidejűség-vezérlés](./cloud-partner-portal-api-concurrency-control.md).
elérhető API-k meghívására szolgáló stratégiák.
- További API [szempontok](./cloud-partner-portal-api-considerations.md), például a verziókezelés és a hibakezelés.


<a name="common-tasks"></a>Gyakori feladatok
------------
Ez a hivatkozás API-k az alábbi gyakori feladatait ismerteti.


### <a name="offers"></a>Ajánlatok

-   [Minden ajánlat beolvasása](./cloud-partner-portal-api-retrieve-offers.md)
-   [Egy adott ajánlat beolvasása](./cloud-partner-portal-api-retrieve-specific-offer.md)
-   [Ajánlat állapotának lekérése](./cloud-partner-portal-api-retrieve-offer-status.md)
-   [Ajánlat létrehozása](./cloud-partner-portal-api-creating-offer.md)
-   [Ajánlat közzététele](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Műveletek

-   [Műveletek lekérése](./cloud-partner-portal-api-retrieve-operations.md)
-   [Szalagműveletek megszakítása](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Alkalmazások közzététele

-   [Élesítés](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Egyéb feladatok

-   [Állítsa be a virtuális gép ajánlatok díjszabása](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Hibaelhárítás

-   [Hitelesítési hibák hibaelhárítása](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
