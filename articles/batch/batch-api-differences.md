---
title: A felügyeleti API-k és a szolgáltatás API-k közötti különbségek - Azure Batch | Microsoft dokumentumok
description: Api-k az Azure Batch-szolgáltatás különböző rétegein dolgoznak.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 181515c0f497af8ffadcb909c13e51a40bfbf3b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672763"
---
# <a name="service-level-and-management-level-apis"></a>Szolgáltatási szint és felügyeleti szintű API-k

Az Azure Batch két API-készlettel rendelkezik, amelyek közül az egyik a szolgáltatási szinthez, a másik pedig a felügyeleti szinthez tartozik. Az elnevezés gyakran hasonló, de különböző eredményeket adnak vissza. Ha azt szeretné, hogy a tevékenységnaplók, majd a felügyeleti API-kat kell használnia. A szolgáltatási szintű API-k megkerülik az Azure Erőforrás-kezelési réteget, és nem kerülnek naplózásra.


A kötegkezelés és a kötegszolgáltatás egyaránt rendelkezik API-k készlet, például. 
- Ez a törölni kívánt API közvetlenül a kötegfiókra irányul:https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Ez az API-t a készlet https://docs.microsoft.com/rest/api/batchmanagement/pool/delete törléséhez a management.azure.com réteget célozza meg.

