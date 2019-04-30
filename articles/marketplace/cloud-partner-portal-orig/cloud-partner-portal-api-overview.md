---
title: Cloud Partner Portal API-referencia |} A Microsoft Docs
description: Leírása, használatának előfeltételei és marketplace API műveletek listája.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 116eb48330381c7560c55ea9535b3c1b7c6a6a70
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61094395"
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
