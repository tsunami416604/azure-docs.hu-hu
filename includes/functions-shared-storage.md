---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963655"
---
A teljesítmény maximalizálása érdekében használjon külön Storage-fiókot minden egyes Function alkalmazáshoz. Ez különösen akkor fontos, ha Durable Functions vagy Event hub által aktivált függvények vannak, amelyek nagy mennyiségű tárolási tranzakciót eredményeznek. Ha az alkalmazás logikája az Azure Storage-t használja közvetlenül (a Storage SDK használatával) vagy a tárolási kötések valamelyikével, használjon dedikált Storage-fiókot. Ha például egy Event hub által aktivált függvény a blob Storage-ba ír néhány adatát, akkor két Storage-fiókot kell használnia,&mdash;egyet a Function alkalmazáshoz, és egy másikat a függvény által tárolt Blobok számára.