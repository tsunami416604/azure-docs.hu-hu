---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 6c348b3dd0005eeab154aa2d74abc617cbd1d0cb
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87529388"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Ha az adattár egy helyszíni hálózaton, egy Azure-beli virtuális hálózaton vagy egy Amazon Virtual Private-felhőben található, létre kell hoznia egy saját üzemeltetésű [integrációs](../articles/data-factory/create-self-hosted-integration-runtime.md) modult a csatlakozáshoz.

Ha az adattár egy felügyelt felhőalapú adatszolgáltatás, akkor használhatja az Azure Integration Runtime szolgáltatást. Ha a hozzáférés csak azokra az IP-címekre korlátozódik, amelyeket a tűzfalszabályok engedélyeznek, akkor választhatja [Azure Integration Runtime IP](../articles/data-factory/azure-integration-runtime-ip-addresses.md) -címek hozzáadását az engedélyezési listához. 

A Data Factory által támogatott hálózati biztonsági mechanizmusokkal és lehetőségekkel kapcsolatos további információkért lásd az [adatelérési stratégiákat](../articles/data-factory/data-access-strategies.md).
