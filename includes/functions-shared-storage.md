---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "76963655"
---
A teljesítmény maximalizálása érdekében használjon külön Storage-fiókot minden egyes Function alkalmazáshoz. Ez különösen akkor fontos, ha Durable Functions vagy Event hub által aktivált függvények vannak, amelyek nagy mennyiségű tárolási tranzakciót eredményeznek. Ha az alkalmazás logikája az Azure Storage-t használja közvetlenül (a Storage SDK használatával) vagy a tárolási kötések valamelyikével, használjon dedikált Storage-fiókot. Ha például egy Event hub által aktivált függvény néhány adat a blob Storage-ba való írásával történik, akkor használjon két Storage-fiókot, &mdash; egyet a Function alkalmazáshoz, és egy másikat a függvény által tárolt blobokhoz.