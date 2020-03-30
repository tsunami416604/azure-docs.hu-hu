---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 2e90d218aa6dc90746ba0e928fb3393f0bdb5e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68966352"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Ha az adattár az alábbi módokon van konfigurálva, be kell állítania egy [saját üzemeltetésű integrációs futásidőt](../articles/data-factory/create-self-hosted-integration-runtime.md) az adattárhoz való csatlakozáshoz:

- Az adattár egy helyszíni hálózaton, az Azure Virtuális hálózaton belül vagy az Amazon Virtual Private Cloud-on belül található.
- Az adattár egy felügyelt felhőalapú adatszolgáltatás, ahol a hozzáférés a tűzfalszabályokban szereplő IP-kre korlátozódik.
