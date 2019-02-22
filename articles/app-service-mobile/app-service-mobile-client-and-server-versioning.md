---
title: Ügyfél- és a Mobile Apps és Mobile Services SDK verziószámozása |} A Microsoft Docs
description: Ügyféloldali SDK-k listáját, és a Mobile Services és az Azure Mobile Apps server SDK verziókkal való kompatibilitás érdekében
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
ms.openlocfilehash: 56c5e0582afe55dcd63aa056817898d3d4942419
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594066"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>A Mobile Apps és Mobile Services ügyfél- és szerveroldali verziószámozás
Az Azure Mobile Services legújabb verziója a **Mobile Apps** Azure App Service szolgáltatásával.

A Mobile Apps ügyfél- és SDK-k eredetileg alapuló azokat, a Mobile Servicesben, de azok *nem* kompatibilis egymással.
Kell használnia, amely azt jelenti, hogy egy *Mobile Apps* ügyféloldali SDK-val rendelkező egy *Mobile Apps* kiszolgálói SDK, és ehhez hasonlóan az *Mobile Services*. A jelen szerződés ki egy speciális fejléc értéke az ügyfél és a kiszolgáló SDK-k által használt `ZUMO-API-VERSION`.

Megjegyzés: Ha ez a dokumentum hivatkozik egy *mobilszolgáltatások* háttér, nem feltétlenül szükséges a Mobile Services üzemeltethető. Már lehetséges a futtatása az App Service-ben kódváltoztatás nélkül mobilszolgáltatás migrálása, de a szolgáltatás továbbra is szeretné az *mobilszolgáltatások* SDK-verziókra.

Áttelepítés az App Service-ben kódváltoztatás nélkül kapcsolatos további információkért lásd: a cikk [Mobilszolgáltatás áttelepítése az Azure App Service-ben].

## <a name="header-specification"></a>Fejléc-specifikáció
A kulcs `ZUMO-API-VERSION` vagy a HTTP-fejléc, vagy a lekérdezési karakterlánc is megadható. Az űrlap egy verzió-karakterlánca értéke **x.y.z**.

Példa:

GET https://service.azurewebsites.net/tables/TodoItem

HEADERS: ZUMO-API-VERZIÓ: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Megtagadja a verzió ellenőrzése
Érték beállításával ellenőrzése verziója is kikapcsolja **igaz** az Alkalmazásbeállítás **MS_SkipVersionCheck**. Adja meg, a web.config vagy az Azure portal alkalmazás beállítások szakaszában.

> [!NOTE]
> Nincsenek a viselkedésbeli változások között a Mobile Services és a Mobile Apps, különösen a kapcsolat nélküli szinkronizálás, a hitelesítés és leküldéses értesítések száma. Csak verzió ellenőrzésének biztosításához, hogy ezek a viselkedésbeli változások tilos megszüntetnie a alkalmazás funkcióit teszt befejezése után meg kell tilthatók le.
>
>

## <a name="summary-of-compatibility-for-all-versions"></a>Az összes verzió esetén kompatibilitási összefoglalása
Az alábbi diagram bemutatja az összes ügyfél és kiszolgáló típusú közötti kompatibilitást. A háttérrendszernek vagy Mobile sorolja be a rendszer **szolgáltatások** vagy Mobile **alkalmazások** alapján a kiszolgáló SDK-t, amelyet használ.

|  | **Mobile Services** Node.js vagy a .NET | **A Mobile Apps** Node.js vagy a .NET |
| --- | --- | --- |
| [A Mobile Services-ügyfelek] |OK |Hiba történt\* |
| [A Mobile Apps-ügyfelek] |Hiba történt\* |OK |

\*Ez lehet irányítani megadásával **MS_SkipVersionCheck**.

<!-- IMPORTANT!  The anchors for Mobile Services and Mobile Apps MUST be 1.0.0 and 2.0.0 respectively, since there is an exception error message that uses those anchors. -->

<!-- NOTE: the fwlink to this document is https://go.microsoft.com/fwlink/?LinkID=690568 -->

## <a name="1.0.0"></a>A Mobile Services ügyfél és kiszolgáló
Az ügyfél SDK-k az alábbi táblázat a kompatibilisek **mobilszolgáltatások**.

Megjegyzés: a Mobile Services ügyféloldali SDK-k *nem* elküldeni egy fejléc értéke `ZUMO-API-VERSION`. Ha a szolgáltatás kap a fejléc vagy lekérdezési karakterlánc értéke visszaad egy hiba, kivéve, ha explicit módon ki a fent leírt beleegyezett.

### <a name="MobileServicesClients"></a> A Mobile *szolgáltatások* ügyféloldali SDK-k
| Ügyfélplatform | Verzió | A verziófejléc-érték |
| --- | --- | --- |
| Felügyelt ügyfelek (Windows, a Xamarin) |[1.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2) |n/a |
| iOS |[2.2.2](https://aka.ms/gc6fex) |n/a |
| Android |[2.0.3](https://go.microsoft.com/fwLink/?LinkID=280126) |n/a |
| HTML |[1.2.7](https://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js) |n/a |

### <a name="mobile-services-server-sdks"></a>A Mobile *szolgáltatások* server SDK-k
| Kiszolgálóplatform | Verzió | Elfogadott verzió fejléce |
| --- | --- | --- |
| .NET |[WindowsAzure.MobileServices.Backend.* Version 1.0.x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/) |**Nincs verzió fejléce** |
| Node.js |(hamarosan elérhető) |**Nincs verzió fejléce** |

<!-- TODO: add Node npm version -->

### <a name="behavior-of-mobile-services-backends"></a>A mobilszolgáltatások háttérkomponenseinek viselkedését
| ZUMO-API-VERZIÓ | MS_SkipVersionCheck értékét | Válasz |
| --- | --- | --- |
| Nincs megadva |Bármelyik |200 - OK |
| Bármilyen érték |True (Igaz) |200 - OK |
| Bármilyen érték |A megadott FALSE/nem |400 – Hibás kérelem |

## <a name="2.0.0"></a>Az Azure Mobile Apps-ügyfél és kiszolgáló
### <a name="MobileAppsClients"></a> A Mobile *alkalmazások* ügyféloldali SDK-k
Az ügyfél-SDK következő verziói kezdve verzió ellenőrzése jelent a **Azure Mobile Apps**:

| Ügyfélplatform | Verzió | A verziófejléc-érték |
| --- | --- | --- |
| Felügyelt ügyfelek (Windows, a Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

<!-- TODO: add HTML version when released -->

### <a name="mobile-apps-server-sdks"></a>A Mobile *alkalmazások* server SDK-k
A következő kiszolgálói SDK-verziókra verzió ellenőrzése tartalmazza:

| Kiszolgálóplatform | SDK | Elfogadott verzió fejléce |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Mobilalkalmazások háttérkomponenseinek viselkedését
| ZUMO-API-VERZIÓ | MS_SkipVersionCheck értékét | Válasz |
| --- | --- | --- |
| x.y.z vagy Null |True (Igaz) |200 - OK |
| Null |A megadott FALSE/nem |400 – Hibás kérelem |
| 1.x.y |A megadott FALSE/nem |400 – Hibás kérelem |
| 2.0.0-2.x.y |A megadott FALSE/nem |200 - OK |
| 3.0.0-3.x.y |A megadott FALSE/nem |400 – Hibás kérelem |

## <a name="next-steps"></a>További lépések
* [Mobilszolgáltatás áttelepítése az Azure App Service-ben]

[A Mobile Services-ügyfelek]: #MobileServicesClients
[A Mobile Apps-ügyfelek]: #MobileAppsClients


[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
[Mobilszolgáltatás áttelepítése az Azure App Service-ben]: app-service-mobile-migrating-from-mobile-services.md
