---
title: A felügyeleti API-k és a szolgáltatási API-k közötti különbségek
description: Az API-k a Azure Batch szolgáltatás különböző rétegein működnek.
ms.topic: conceptual
ms.date: 02/26/2020
ms.custom: seodec18
ms.openlocfilehash: 33b8c5980aba1090155d6b136c6707e928666abf
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115397"
---
# <a name="service-level-and-management-level-apis"></a>Szolgáltatási szint és felügyeleti szint API-k

Azure Batch két API-készlettel rendelkezik, egyet a szolgáltatási szinthez, egyet pedig a felügyeleti szinthez. Az elnevezés gyakran hasonló, de eltérő eredményeket adnak vissza. Ha azt szeretné, hogy a tevékenységek naplói, akkor a felügyeleti API-kat kell használnia. A szolgáltatási szint API-jai megkerülik az Azure Resource Management réteget, és nincsenek naplózva.


A Batch-felügyelet és a Batch szolgáltatás egyaránt tartalmaz API-kat a készlethez, például:. 
- A készlet törlésére szolgáló API közvetlenül a Batch-fiókra van megcélozva:https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- A készlet https://docs.microsoft.com/rest/api/batchmanagement/pool/delete törlésére szolgáló API a Management.Azure.com rétegre irányul.

