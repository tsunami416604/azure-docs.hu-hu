---
title: Az ügyfél és a kiszolgáló SDK verziószámozása Mobile Apps és Mobile Servicesban | Microsoft Docs
description: Az ügyfél SDK-k listája és a Mobile Services és az Azure kiszolgálói SDK-verzióinak kompatibilitása Mobile Apps
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 7133e8bc7d04b3653b6b788347b7bc5176087f4c
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883476"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Az ügyfél és a kiszolgáló verziószámozása Mobile Apps és Mobile Services
Az Azure Mobile Services legújabb verziója a Azure App Service **Mobile apps** szolgáltatása.

A Mobile Apps ügyfél-és kiszolgálói SDK-k eredetileg a Mobile Serviceson alapulnak, de *nem* kompatibilisek egymással.
Ezt a *Mobile apps* ügyfél-SDK-t kell használnia egy *Mobile apps* Server SDK-val, és hasonlóan a *Mobile Serviceshoz*. Ez a szerződés az ügyfél és a kiszolgáló SDK-k által használt speciális fejléc- `ZUMO-API-VERSION`értékkel van kikényszerítve.

Megjegyzés: Ha ez a dokumentum egy *Mobile Services* háttérre hivatkozik, nem feltétlenül szükséges Mobile Services. Mostantól lehetőség van arra, hogy a Mobile Service-t a kód módosítása nélkül App Service futtasson, de a szolgáltatás továbbra is *Mobile Services* SDK-verziót használ.

## <a name="header-specification"></a>Fejléc-specifikáció
A kulcs `ZUMO-API-VERSION` a HTTP-fejlécben vagy a lekérdezési karakterláncban adható meg. Az érték az **x. y. z**formátumú Version karakterlánc.

Példa:

GET https://service.azurewebsites.net/tables/TodoItem

FEJLÉCEK ZUMO-API-VERSION: 2.0.0

UTÁNI https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>A verziók ellenőrzésének megtagadása
A verzió-ellenőrzés letiltásához állítsa az **igaz** értéket a **MS_SkipVersionCheck**beállításnál. Adja meg ezt a web. config fájlban, vagy a Azure Portal alkalmazás beállításai szakaszában.

> [!NOTE]
> Mobile Services és Mobile Apps között számos viselkedési változás van, különösen az offline szinkronizálás, a hitelesítés és a leküldéses értesítések területein. A teljes tesztelést követően csak a verziók ellenőrzését kell letiltani annak biztosításához, hogy ezek a viselkedési változások ne megtörjék az alkalmazás funkcióit.

## <a name="2.0.0"></a>Azure Mobile Apps-ügyfél és-kiszolgáló
### <a name="MobileAppsClients"></a>Mobile *apps* ügyféloldali SDK-k
A verzió-ellenőrzés az Azure-hoz készült ügyfél-SDK következő verzióival kezdődően lett bevezetve **Mobile apps**:

| Ügyféloldali platform | Version | Verzió fejlécének értéke |
| --- | --- | --- |
| Felügyelt ügyfél (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

### <a name="mobile-apps-server-sdks"></a>Mobile *apps* Server SDK-k
A verzió ellenőrzése a következő kiszolgálói SDK-verziókba tartozik:

| Kiszolgálói platform | SDK | Elfogadott verzió fejléce |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Mobile Apps háttérrendszer működése
| ZUMO-API-VERSION | MS_SkipVersionCheck értéke | Válasz |
| --- | --- | --- |
| x. y. z vagy Null |True |200 - OK |
| Null |Hamis/nincs megadva |400 – Hibás kérés |
| 1. x. y |Hamis/nincs megadva |400 – Hibás kérés |
| 2.0.0 – 2. x. y |Hamis/nincs megadva |200 - OK |
| 3.0.0-3. x. y |Hamis/nincs megadva |400 – Hibás kérés |

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
