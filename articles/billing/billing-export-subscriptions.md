---
title: Az Azure-előfizetés felső szintű adatainak exportálása |} A Microsoft Docs
description: Bemutatja, hogyan tekintheti meg az összes Azure-előfizetés azonosítókat a fiókjához társított.
keywords: ''
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: 8a3d1a3a6b1dce1d729942715bbe5962837ff093
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57532133"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Exportálás és a legfelső szintű előfizetés adatainak megtekintése
Ha szeretne az előfizetés azonosítókat a felhasználói hitelesítő adatokhoz tartozó megtekintése [töltse le egy .JSON kiterjesztésű fájlt, amely az előfizetés adatait az Azure Account Center](https://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

A letöltött .JSON kiterjesztésű fájlt a következő információkat biztosítja:
- E-mail: A fiókjához társított e-mail-cím.
- PUID azonosítója: Az Ön számlázási fiókjához tartozó egyedi azonosítója.
- SubscriptionIds: A fiókjához, előfizetés-azonosító. a felsorolt tartozó előfizetések listája

### <a name="subscriptionsjson-sample"></a>Subscriptions.JSON minta

```json
{
  "Email":"admin@contoso.com",
  "Puid":"00052xxxxxxxxxxx",
  "SubscriptionIds":[
    "38124d4d-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "7c8308f1-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "39a25f2b-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "52ec2489-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "e42384b2-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "90757cdc-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  ]
}
```
