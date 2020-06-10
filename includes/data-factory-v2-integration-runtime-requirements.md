---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 0b03957178af0578d2c6cd91d7377c93f413cec3
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84629490"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Ha az adattár az alábbi módszerek egyikével van konfigurálva, egy saját üzemeltetésű [integrációs](../articles/data-factory/create-self-hosted-integration-runtime.md) modult kell beállítania az adattárhoz való csatlakozáshoz:

- Az adattár egy helyszíni hálózaton belül, egy Azure-beli virtuális hálózaton belül vagy az Amazon Virtual Private Cloud-on belül található.
- Az adattár egy felügyelt felhőalapú adatszolgáltatás, ahol a hozzáférés a tűzfalszabályok által engedélyezett IP-címekre korlátozódik.
