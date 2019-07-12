---
title: Diagnosztikai naplók a Media Services az Azure monitoron keresztül figyelése |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan irányíthatja, és megtekintheti a diagnosztikai naplók az Azure monitoron keresztül.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 233b043ffdc295fe94ed2e3ba837d4229848df22
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795843"
---
# <a name="monitor-media-services-diagnostic-logs"></a>A Media Services diagnosztikai naplók figyelése

[Az Azure Monitor](../../azure-monitor/overview.md) lehetővé teszi, hogy a figyelő metrikák és diagnosztikai naplók, amelyek segítenek megérteni, hogyan az alkalmazások hajt végre. Ez a részletes ismertetését a funkciót, és miért érdemes az Azure Media Services-metrikák és diagnosztikai naplók megtekintéséhez lásd: [figyelő Media Services-metrikák és diagnosztikai naplók](media-services-metrics-diagnostic-logs.md).

Ez a cikk bemutatja, hogyan adatainak átirányítása a tárfiókba, és nézze meg az adatokat. 

## <a name="prerequisites"></a>Előfeltételek

- [A Media Services-fiók létrehozása](create-account-cli-how-to.md).
- Felülvizsgálat [figyelő Media Services-metrikák és diagnosztikai naplók](media-services-metrics-diagnostic-logs.md).

## <a name="route-data-to-the-storage-account-using-the-portal"></a>Útvonal-adatok a storage-fiókba, a portál használatával

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.
1. Keresse meg a Media Services-fiókba, és kattintson a **diagnosztikai beállítások** alatt **figyelő**. Itt láthatja az előfizetésben lévő összes olyan erőforrást, amely monitorozási adatokat készít az Azure Monitoron keresztül. 

    ![Diagnosztikai beállítások szakasz](media/media-services-diagnostic-logs/logs01.png)

1. Kattintson a **diagnosztikai beállítás hozzáadása**.

   Az erőforrások diagnosztikai beállítása annak definíciója, hogy *milyen* monitorozási adatokat kell átirányítani egy adott erőforrásból, és *hová* kell kerülniük ezeknek a monitorozási adatoknak.

1. A megjelenő szakaszban adjon egy **nevet** a beállításnak, és jelölje be az **Archiválás tárfiókba** jelölőnégyzetet.

    Válassza ki a tárfiókot, amelyhez hozzá szeretne küldeni a naplókat, és nyomja le **OK**.
1. Jelölje be a **Napló** és a **Metrika** alatti jelölőnégyzeteket. Az erőforrás típusától függően előfordulhat, hogy csak az egyik lehetőség jelenik meg. Ezek a jelölőnégyzetek szabályozzák, hogy a napló- és metrikaadatok milyen, az erőforrástípushoz elérhető kategóriái kerülnek a kiválasztott célra, amely ebben az esetben a tárfiók.

   ![Diagnosztikai beállítások szakasz](media/media-services-diagnostic-logs/logs02.png)
1. Állítsa a **Megőrzés (nap)** csúszkát a 30 értékre. Ez a csúszka beállítja a monitorozási adatok tárfiókban való megőrzésének időtartamát napokban. Az Azure Monitor automatikusan törli a megadott számú napnál régebbi adatokat. A nulla értékű megőrzési időszak határozatlan ideig tárolja az adatokat.
1. Kattintson a **Save** (Mentés) gombra.

Az erőforrás monitorozási adatai mostantól a tárfiókba kerülnek.

## <a name="route-data-to-the-storage-account-using-the-cli"></a>Útvonal-adatok a tárfiókba, a parancssori felületről

Ahhoz, hogy a diagnosztikai naplókat egy tárfiókban, a következőt futtatná `az monitor diagnostic-settings` parancssori felületi parancsot: 

```cli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Példa:

```cli
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforams  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount" \
    --resource-group "amsResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="view-data-in-the-storage-account-using-the-portal"></a>A portál használatával a tárfiókban lévő adatok megtekintése

Ha követte az előző lépéseket, az adatok elkezdtek a tárfiókba érkezni.

Előfordulhat, hogy akár öt percet is várnia kell, mielőtt az esemény megjelenik a tárfiókban.

1. A portálban navigáljon a bal oldali navigációs sávon található **Tárfiókok** szakaszhoz.
1. Azonosítsa az előző szakaszban létrehozott tárfiókot, és kattintson rá.
1. Kattintson a **Blobok**, majd a címkéjű tárolóra **insights-logs-keydeliveryrequests**. Ez az a tároló, amely tartalmaz, a naplók. Monitorozási adatok van vannak tárolókba osztva erőforrás-azonosítója, majd dátum és idő alapján.
1. Az erőforrás-azonosító, dátum és idő tárolóiba kattintva keresse meg a PT1H.json fájlt. Kattintson a PT1H.json fájlra, majd a **Letöltés** gombra.

 Most megtekintheti a tárfiókban tárolt JSON-eseményt.

### <a name="examples-of-pt1hjson"></a>Példák a PT1H.json

#### <a name="clear-key-delivery-log"></a>Kulcsok kézbesítési napló

```json
{
  "time": "2019-05-21T00:07:33.2820450Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 253,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "e4276e1d-c012-40b1-80d0-ac15808b9277",
      "nbf": "1558396914",
      "exp": "1558400814",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "fb5c2b3a-bffa-4434-9c6f-73d689649add",
    "keyType": "Clear",
    "keyId": "e4276e1d-c012-40b1-80d0-ac15808b9277",
    "policyName": "SharedContentKeyPolicyUsedByAllAssets",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

#### <a name="widevine-encrypted-key-delivery-log"></a>Widevine titkosított kulcskézbesítési napló

```json
{
  "time": "2019-05-20T23:15:22.7088747Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 69,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
      "nbf": "1558392430",
      "exp": "1558396330",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "49613dd2-16aa-4595-a6e0-4e68beae6d37",
    "keyType": "Widevine",
    "keyId": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
    "policyName": "DRMContentKeyPolicy",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

## <a name="see-also"></a>Lásd még

* [Az Azure Monitor-metrikák](../../azure-monitor/platform/data-platform.md)
* [Az Azure Monitor diagnosztikai naplók](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [Gyűjtése és felhasználása a naplófájlok adatait az Azure-erőforrások](../../azure-monitor/platform/diagnostic-logs-overview.md)

## <a name="next-steps"></a>További lépések

[Metrikák figyelése](media-services-metrics-howto.md)
