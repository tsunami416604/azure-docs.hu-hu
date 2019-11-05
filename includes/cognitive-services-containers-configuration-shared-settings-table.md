---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7ccbc6c06419d22add7c52829069bb858cb35cf7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73484077"
---
A tároló a következő konfigurációs beállításokkal rendelkezik:

|Kötelező|Beállítás|Cél|
|--|--|--|
|Igen|[ApiKey](#apikey-configuration-setting)|A számlázási információk nyomon követése.|
|Nem|[ApplicationInsights](#applicationinsights-setting)|Lehetővé teszi az [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetria támogatásának hozzáadását a tárolóhoz.|
|Igen|[Számlázás](#billing-configuration-setting)|Meghatározza a szolgáltatási erőforrás végpontjának URI-JÁT az Azure-ban.|
|Igen|[EULA](#eula-setting)| Azt jelzi, hogy elfogadta a tároló licencét.|
|Nem|[Fluentd](#fluentd-settings)|Naplózza a naplót, és opcionálisan metrikus adatokat is egy Fluent-kiszolgálóra.|
|Nem|HTTP-proxy|Konfigurál egy HTTP-proxyt a kimenő kérések elkészítése céljából.|
|Nem|[Naplózás](#logging-settings)|ASP.NET Core naplózási támogatást biztosít a tárolóhoz. |
|Nem|[Tartók](#mount-settings)|Adatokat olvas és ír a gazdagépről a tárolóba, a tárolóból pedig vissza a gazdagépre.|
