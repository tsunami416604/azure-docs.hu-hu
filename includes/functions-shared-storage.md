---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76963655"
---
A teljesítmény maximalizálása érdekében minden függvényalkalmazáshoz külön tárfiókot használjon. Ez különösen akkor fontos, ha a Tartós függvények vagy az Event Hub aktivált függvények, amelyek egyaránt nagy mennyiségű tárolási tranzakciók generál. Amikor az alkalmazáslogika kommunikál az Azure Storage-szal, akár közvetlenül (a storage SDK használatával), vagy a storage-kötések egyikén keresztül, egy dedikált tárfiókot kell használnia. Például ha egy Event Hub-aktivált függvény írása néhány adatot blob&mdash;storage, két tárfiókok egyik a függvényalkalmazás és egy másik a függvény által tárolt blobok.