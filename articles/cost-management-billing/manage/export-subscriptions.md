---
title: Az Azure-előfizetés legfelső szintű adatainak exportálása | Microsoft Docs
description: Ez a cikk azt mutatja be, hogyan tekintheti meg a fiókjához társított összes Azure-előfizetési azonosítót.
keywords: ''
author: bandersmsft
ms.reviewer: matrive
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 83b6a9db0107cb35448aec491c81c2630e87ddd7
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199721"
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
