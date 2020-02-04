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
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: 553cc7fd27571ebc925e33f824060c023664a369
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "75991775"
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
