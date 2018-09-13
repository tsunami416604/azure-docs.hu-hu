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
ms.author: adpick
ms.openlocfilehash: 7bc9f4df6e98dd86283c4389466b13b8f6bb4d15
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35921701"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Exportálás és a legfelső szintű előfizetés adatainak megtekintése
Ha szeretne az előfizetés azonosítókat a felhasználói hitelesítő adatokhoz tartozó megtekintése [töltse le egy .JSON kiterjesztésű fájlt, amely az előfizetés adatait az Azure Account Center](http://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

A letöltött .JSON kiterjesztésű fájlt a következő információkat biztosítja:
- E-mail cím: Az a fiókjához társított e-mail-cím.
- PUID: Egyedi azonosítója a számlázási fiókjához.
- SubscriptionIds: Az előfizetések listája, amely számba veszi az előfizetés-azonosító. a fiók tartozik

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
