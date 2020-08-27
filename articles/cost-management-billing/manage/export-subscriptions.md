---
title: Az Azure-előfizetés legfelső szintű adatainak exportálása
description: Ez a cikk azt mutatja be, hogyan tekintheti meg a fiókjához társított összes Azure-előfizetési azonosítót.
author: bandersmsft
ms.reviewer: matrive
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: b3b2e9b501f2ae103900a085e9b7a4b412efb78e
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686836"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Az előfizetés legfelső szintű adatainak exportálása és megtekintése
Ha meg szeretné tekinteni a felhasználói hitelesítő adataihoz tartozó előfizetési azonosítókat, [töltse le az előfizetési adatait tartalmazó .json fájlt az Azure Fiókközpontból](https://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

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
