---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/15/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 39b61c78cbd0333183c80234256d6041a109d846
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "68249916"
---
Az Azure AD-erőforrás azonosítója azt a célközönséget jelzi, amelyhez a kiállított jogkivonatok használhatók az Azure-erőforrásokhoz való hozzáférés biztosításához. Az Azure Storage esetében az erőforrás-azonosító egyedi lehet egyetlen Storage-fiókra, vagy bármely Storage-fiókra vonatkozhat. A következő táblázat ismerteti az erőforrás-AZONOSÍTÓhoz megadható értékeket:

|Erőforrás-azonosító  |Leírás  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | Egy adott Storage-fiók szolgáltatási végpontja. Ezzel az értékkel beszerezhetők egy jogkivonat, amely az adott Azure Storage-fiókra és szolgáltatásra irányuló kérések engedélyezésére szolgál. Cserélje le a zárójelben lévő értéket a Storage-fiók nevére.      |
|`https://storage.azure.com/`     | A használatával jogkivonatot kérhet az Azure Storage-fiókokra irányuló kérések engedélyezéséhez.        |
