---
title: Az Azure-előfizetés felső szintű adatainak exportálása |} Microsoft Docs
description: Bemutatja, hogyan tekintheti meg az összes Azure-előfizetés a fiókjához társított azonosítók.
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
ms.date: 10/9/2017
ms.author: adpick
ms.openlocfilehash: b995b0c7154faab66a44bef1a7d74eeb8404e5c1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655116"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Exportálás és a legfelső szintű előfizetés adatainak megtekintése
Ha szeretné tekintse meg az előfizetés a felhasználó hitelesítő adataival, társított azonosítókra [az előfizetési adatok .JSON kiterjesztésű fájl letöltését az Azure Account Center](http://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

A letöltött .JSON kiterjesztésű fájlt a következő adatokat tartalmazza:
- E-mailek: A fiókjához társított e-mail címe.
- PUID: A rendelt egyedi azonosítóként folyószámlájára.
- SubscriptionIds: A fiókjához, előfizetés-azonosító. által számba tartozó előfizetések listája

### <a name="subscriptionsjson-sample"></a>Subscriptions.JSON minta
~~~~
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
~~~~
