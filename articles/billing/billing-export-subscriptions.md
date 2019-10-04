---
title: Az Azure-előfizetés legfelső szintű adatainak exportálása | Microsoft Docs
description: Ez a cikk azt mutatja be, hogyan tekintheti meg a fiókjához társított összes Azure-előfizetési azonosítót.
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
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "60918792"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Az előfizetés legfelső szintű adatainak exportálása és megtekintése
Ha meg szeretné tekinteni a felhasználói hitelesítő adataihoz tartozó előfizetési azonosítókat, [töltse le az előfizetési adatait tartalmazó .json fájlt az Azure Fiókközpontból](https://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

A letöltött .json fájl az alábbi adatokat tartalmazza:
- Email: A fiókjához társított e-mail-cím.
- Puid: A számlázási fiókjához társított egyedi azonosító.
- SubscriptionIds: A fiókhoz tartozó előfizetések listája az előfizetési azonosítók szerint felsorolva.

### <a name="subscriptionsjson-sample"></a>Példa subscriptions.json fájlra

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
