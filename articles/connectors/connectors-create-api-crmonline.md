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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82994297"
---
# <a name="create-and-manage-records-in-dynamics-365-by-using-azure-logic-apps"></a>Rekordok létrehozása és kezelése a Dynamics 365-ben a Azure Logic Apps használatával

A Dynamics 365 a [Common Data Service](https://docs.microsoft.com/powerapps/maker/common-data-service/data-platform-intro)használja. A Dynamics 365-kapcsolatokhoz használja a [Common Data Service-összekötőt](https://docs.microsoft.com/connectors/commondataservice/).

> [!IMPORTANT]
> A [Dynamics 365-összekötő](https://docs.microsoft.com/connectors/dynamicscrmonline/) elavult, de továbbra is működni fog, amíg el nem távolítják. Ne használja a Dynamics 365-összekötőt új Logic apps-alkalmazásokhoz. A Dynamics 365-összekötő eltávolítására még nem jelentettek ütemtervet. A meglévő logikai alkalmazásokat nem kell átalakítani a Common Data Service-összekötő, vagy egy másik tervezett, új összekötő használatához, az összekötő eltávolításakor azonban át kell alakítani a logikai alkalmazásokat. További információ: a [Dynamics 365 Connector elavult](https://docs.microsoft.com/power-platform/important-changes-coming).
>
> A [Common Data Service-összekötő](https://docs.microsoft.com/connectors/commondataservice/) ugyanazokat a képességeket biztosítja, mint az elavult Dynamics 365-összekötő, csak megbízhatóságot növelő fejlesztéseket tartalmaz. A Common Data Service-összekötő logikai alkalmazásokban való használatával kapcsolatos további információkért lásd a [Common Data Service-rekordok az Azure Logic Apps használatával való létrehozását és kezelését](../connectors/connect-common-data-service.md) ismertető részt.

További információ a Common Data Serviceről:

* [További információ: a Common Data Service első lépései](https://docs.microsoft.com/learn/modules/get-started-with-powerapps-common-data-service/)
* [További információ: a Dynamics 365-adatai összekapcsolása és elemzése a Power platform és a Common Data Service használatával](https://docs.microsoft.com/learn/wwl/connect-analyze-dynamics-365-data/)