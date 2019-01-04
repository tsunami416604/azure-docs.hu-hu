---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 57e54c4c3b8ae44d42051c87356f1feeaa5a3f10
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53977208"
---
Ez a tároló a következő konfigurációs beállításokkal rendelkezik:

|Szükséges|Beállítás|Cél|
|--|--|--|
|Igen|[ApiKey](#apikey-setting)|Számlázási adatok nyomon követésére szolgál.|
|Nem|[ApplicationInsights](#applicationinsights-setting)|Lehetővé teszi, hogy [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetriai támogatása a tárolóba.|
|Igen|[Számlázás](#billing-setting)|Adja meg a végpont a szolgáltatás-erőforrás URI-ját az Azure-ban.|
|Igen|[Végfelhasználói licencszerződés](#eula-setting)| Azt jelzi, hogy Ön már elfogadta a licencet, a tároló.|
|Nem|[Fluentd](#fluentd-settings)|Napló írási és opcionálisan metrikaadatok Fluentd kiszolgálóhoz.|
|Nem|[Logging](#logging-settings)|Biztosítja a naplózás a tároló támogatása ASP.NET Core. |
|Nem|[Csatlakoztatja](#mount-settings)|Olvasási és írási adatok tárolót a gazdaszámítógép és a tároló található vissza gazdaszámítógéphez.|
