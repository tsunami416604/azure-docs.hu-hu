---
title: A felügyeleti API-k és a szolgáltatási API-k közötti különbségek – Azure Batch | Microsoft Docs
description: Az API-k a Azure Batch szolgáltatás különböző rétegein működnek.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 181515c0f497af8ffadcb909c13e51a40bfbf3b0
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672763"
---
# <a name="service-level-and-management-level-apis"></a>Szolgáltatási szint és felügyeleti szint API-k

Azure Batch két API-készlettel rendelkezik, egyet a szolgáltatási szinthez, egyet pedig a felügyeleti szinthez. Az elnevezés gyakran hasonló, de eltérő eredményeket adnak vissza. Ha azt szeretné, hogy a tevékenységek naplói, akkor a felügyeleti API-kat kell használnia. A szolgáltatási szint API-jai megkerülik az Azure Resource Management réteget, és nincsenek naplózva.


A Batch-felügyelet és a Batch szolgáltatás egyaránt tartalmaz API-kat a készlethez, például:. 
- A készlet törlésére szolgáló API közvetlenül a Batch-fiókra van megcélozva: https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Ez a készlet törlésére szolgáló API- https://docs.microsoft.com/rest/api/batchmanagement/pool/delete a management.azure.com rétegre irányul.

