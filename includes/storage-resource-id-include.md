---
title: fájlbefoglalás
description: fájlbefoglalás
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/15/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 39b61c78cbd0333183c80234256d6041a109d846
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "68249916"
---
Az Azure AD-erőforrás azonosítója azt a célközönséget jelzi, amelyhez a kiállított jogkivonatok használhatók az Azure-erőforrásokhoz való hozzáférés biztosításához. Az Azure Storage esetében az erőforrás-azonosító egyedi lehet egyetlen Storage-fiókra, vagy bármely Storage-fiókra vonatkozhat. A következő táblázat ismerteti az erőforrás-AZONOSÍTÓhoz megadható értékeket:

|Erőforrás-azonosító  |Description  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | Egy adott Storage-fiók szolgáltatási végpontja. Ezzel az értékkel beszerezhetők egy jogkivonat, amely az adott Azure Storage-fiókra és szolgáltatásra irányuló kérések engedélyezésére szolgál. Cserélje le a zárójelben lévő értéket a Storage-fiók nevére.      |
|`https://storage.azure.com/`     | A használatával jogkivonatot kérhet az Azure Storage-fiókokra irányuló kérések engedélyezéséhez.        |
