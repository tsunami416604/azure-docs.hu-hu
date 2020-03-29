---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7ccbc6c06419d22add7c52829069bb858cb35cf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73484077"
---
A tároló a következő konfigurációs beállításokkal rendelkezik:

|Kötelező|Beállítás|Cél|
|--|--|--|
|Igen|[ApiKey (ApiKey)](#apikey-configuration-setting)|Nyomon követi a számlázási adatokat.|
|Nem|[ApplicationInsights](#applicationinsights-setting)|Lehetővé teszi [az Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetriai támogatás hozzáadását a tárolóhoz.|
|Igen|[Számlázás](#billing-configuration-setting)|Megadja a végpont URI-t az Azure-beli szolgáltatáserőforrás.|
|Igen|[Eula](#eula-setting)| Azt jelzi, hogy elfogadta a tároló licencét.|
|Nem|[Fluentd](#fluentd-settings)|Naplót és szükség esetén metrikaadatokat ír egy Fluentd kiszolgálóra.|
|Nem|HTTP-proxy|Http-proxykonfigurálása kimenő kérelmek hez.|
|Nem|[Naplózási](#logging-settings)|ASP.NET Core naplózási támogatást nyújt a tárolóhoz. |
|Nem|[Tartók](#mount-settings)|Adatokolvasása és írása a gazdaszámítógépről a tárolóba, majd a tárolóból vissza a gazdaszámítógéphez.|
