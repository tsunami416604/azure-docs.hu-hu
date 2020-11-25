---
title: Rugalmas identitás-és hozzáférés-kezelés kiépítése Azure Active Directory
description: Útmutató az építészek, az informatikai rendszergazdák és a fejlesztők számára a rugalmasság kiépítéséhez a személyazonossági rendszerek megszakadásához.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7dfd51b0ed43badbc6a4882f619cb718952b0e85
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919563"
---
# <a name="building-resilience-into-identity-and-access-management-with-azure-active-directory"></a>Rugalmasság kialakítása az identitás-és hozzáférés-kezeléshez Azure Active Directory

Az identitás-és hozzáférés-kezelés (IAM) a folyamatok, szabályzatok és technológiák olyan keretrendszere, amely megkönnyíti az identitások kezelését és azok elérését. Magában foglalja a felhasználók és más fiókok hitelesítését és engedélyezését támogató számos összetevőt a rendszeren.

IAM rugalmasság a rendszerösszetevők megszakadásának lehetősége, és a helyreállítás minimális hatással van a vállalatra, a felhasználókra, az ügyfelekre és a műveletekre. A függőségek, az összetettség és az egypontos hibák csökkentése érdekében az átfogó hibakezelés lehetővé teszi a rugalmasságot.

A megszakadást a IAM-rendszerek bármely összetevőjéből elérheti. Rugalmas IAM-rendszer létrehozásához a rendszer megszakítja a megszakadást, és megtervezi azt. 

A IAM-megoldás rugalmasságának megtervezése során vegye figyelembe a következő elemeket: 

* A IAM-rendszerre támaszkodó alkalmazásai.

* A hitelesítési hívásokat a nyilvános infrastruktúrák használják, beleértve a távközlési vállalatokat, az internetszolgáltatókat és a nyilvános kulcsú szolgáltatókat.

* A felhő és a helyszíni identitás-szolgáltatók.

* Más, a IAM-ra támaszkodó szolgáltatások, valamint az azokat összekötő API-k.

* A rendszerben található egyéb helyszíni összetevők.

Fontos a források forrása, felismerése és tervezése is. Azonban további azonosító rendszerek hozzáadásával, valamint azok eredő függőségeivel és összetettségével csökkentheti a rugalmasságot a növelésük helyett.

Ha nagyobb rugalmasságot kíván létrehozni a rendszerekben, tekintse át a következő cikkeket:

* [Hozzon létre rugalmasságot a IAM-infrastruktúrában](resilience-in-infrastructure.md)

* [IAM-rugalmasság létrehozása az alkalmazásokban](resilience-app-development-overview.md)

* [Hozzon létre rugalmasságot a CIAM-rendszerekben](resilience-b2c.md)
