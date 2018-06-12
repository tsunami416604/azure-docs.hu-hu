---
title: SendGrid |} Microsoft Docs
description: Az Azure App service Logic Apps alkalmazások létrehozása A SendGrid-kapcsolatszolgáltató e-mail küldésre és címzettlisták kezelésére nyújt lehetőséget.
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: bc4f1fc2-824c-4ed7-8de8-e82baff3b746
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 0b34a76ecaf4997cbf66c3d026cd770aa8aa080d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295839"
---
# <a name="get-started-with-the-sendgrid-connector"></a>A SendGrid szolgáltatás összekötőjével az első lépései
A SendGrid-kapcsolatszolgáltató e-mail küldésre és címzettlisták kezelésére nyújt lehetőséget.

Most hozzon létre egy Logic app kezdheti, lásd: [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-sendgrid"></a>Kapcsolatot létesíthet a SendGrid
A Logic apps sendgrid létrehozásához, létre kell hoznia egy **kapcsolat** adja meg a részleteket a következő tulajdonságokkal: 

| Tulajdonság | Szükséges | Leírás |
| --- | --- | --- |
| ApiKey |Igen |SendGrid API-kulcs megadása |

> [!INCLUDE [Steps to create a connection to SendGrid](../../includes/connectors-create-api-sendgrid.md)]
> 


Miután létrehozta a kapcsolatot, a műveletek végrehajtásához, és figyeljen az eseményindítók a használhatja.

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/sendgrid/).

## <a name="more-connectors"></a>További összekötők
Lépjen vissza a [API-k lista](apis-list.md).