---
title: Kapcsolódás a Dynamics 365-hoz
description: Dynamics 365-rekordok létrehozása és kezelése Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 05/09/2020
tags: connectors
ms.openlocfilehash: 00bf8ea2b783e09711a95f203bdfcce0e6b90b2c
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994297"
---
# <a name="create-and-manage-records-in-dynamics-365-by-using-azure-logic-apps"></a>Rekordok létrehozása és kezelése a Dynamics 365-ben a Azure Logic Apps használatával

A Dynamics 365 a [Common Data Service](https://docs.microsoft.com/powerapps/maker/common-data-service/data-platform-intro)használja. A Dynamics 365-kapcsolatokhoz használja a [Common Data Service-összekötőt](https://docs.microsoft.com/connectors/commondataservice/).

> [!IMPORTANT]
> A [Dynamics 365-összekötő](https://docs.microsoft.com/connectors/dynamicscrmonline/) elavult, de továbbra is működni fog, amíg el nem távolítják. Ne használja a Dynamics 365-összekötőt új Logic apps-alkalmazásokhoz. A Dynamics 365-összekötő eltávolítására még nem jelentettek ütemtervet. Nem kell konvertálnia a meglévő Logic apps-alkalmazásokat az Common Data Service-összekötőre vagy más tervezett új összekötőre, de a logikai alkalmazásokat át kell alakítania az összekötő eltávolításakor. További információ: a [Dynamics 365 Connector elavult](https://docs.microsoft.com/power-platform/important-changes-coming).
>
> Az [Common Data Service-összekötő](https://docs.microsoft.com/connectors/commondataservice/) ugyanazokat a funkciókat biztosítja, mint az elavult Dynamics 365-összekötő, de a megbízhatóságot növelő tökéletesítéseket is tartalmaz. További információ a Common Data Service-összekötő logikai alkalmazásokban való használatáról: [Common Data Service-rekordok létrehozása és kezelése Azure Logic Apps használatával](../connectors/connect-common-data-service.md).

További információ a Common Data Serviceről:

* [További információ: a Common Data Service első lépései](https://docs.microsoft.com/learn/modules/get-started-with-powerapps-common-data-service/)
* [További információ: a Dynamics 365-adatai összekapcsolása és elemzése a Power platform és a Common Data Service használatával](https://docs.microsoft.com/learn/wwl/connect-analyze-dynamics-365-data/)