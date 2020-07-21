---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 994b8285999bfa52e2aea9a57ad832aefddcfb76
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544873"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Ha az adattár egy helyszíni hálózaton, egy Azure-beli virtuális hálózaton vagy egy Amazon Virtual Private-felhőben található, létre kell hoznia egy saját üzemeltetésű [integrációs](../articles/data-factory/create-self-hosted-integration-runtime.md) modult a csatlakozáshoz.

Ha az adattár egy felügyelt felhőalapú adatszolgáltatás, ahol a hozzáférés a tűzfalszabályok által engedélyezett IP-címekre korlátozódik, az Azure Integration Runtime használatával és az [IP-címeket](../articles/data-factory/azure-integration-runtime-ip-addresses.md) az engedélyezési listához adhatja hozzá. 

Az adattárak általános eléréséhez az Data Factory által támogatott hálózati biztonsági mechanizmusokkal kapcsolatos információkért tekintse meg az [adathozzáférési stratégiákat](../articles/data-factory/data-access-strategies.md) .
