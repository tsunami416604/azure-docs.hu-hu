---
title: "A Mobile Apps és a Mobile Services SDK versioning ügyfél és kiszolgáló |} Microsoft Docs"
description: "A Mobile Services és az Azure Mobile Apps server SDK verzióival való kompatibilitás és az ügyfél SDK-k listája"
services: app-service\mobile
documentationcenter: 
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: f79e819b1547f81498ea213858faf3c75e374782
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>A Mobile Apps és a Mobile Services ügyfél és kiszolgáló versioning
Azure Mobile Services legújabb verziója a **Mobile Apps** az Azure App Service szolgáltatást.

A Mobile Apps-ügyfél és kiszolgáló SDK a Mobile Services az eredetileg alapulnak, de azok *nem* kompatibilis egymással.
Ez azt jelenti, hogy kell használnia egy *Mobile Apps* ügyfél SDK rendelkező egy *Mobile Apps* server SDK és ehhez hasonlóan az *Mobile Services*. Ehhez a szerződéshez használják az ügyfél és kiszolgáló SDK-k, különleges fejléc értéke ki `ZUMO-API-VERSION`.

Megjegyzés: Ha ez a dokumentum hivatkozik egy *Mobile Services* háttér, nem feltétlenül kell a Mobile Services is működtetnek. Már lehetséges a futtatásához az App Service kód módosítás nélkül mobilszolgáltatás áttelepítése, de a szolgáltatás továbbra is dolgozna *Mobile Services* SDK-verzió.

Az App Service kód módosítás nélkül áttelepítéssel kapcsolatos további tudnivalókért tekintse meg a cikket [Mobile szolgáltatás áttelepítése az Azure App Service].

## <a name="header-specification"></a>Fejléc meghatározása
A kulcs `ZUMO-API-VERSION` vagy a HTTP-fejléc, vagy a lekérdezési karakterlánc is megadható. Az űrlap egy verzió-karakterlánca értéke **x.y.z**.

Példa:

Https://service.azurewebsites.net/tables/TodoItem beolvasása

FEJLÉCEK: ZUMO-API-VERZIÓ: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Meggátolható a verzió ellenőrzése
Verzióellenőrzés értékének beállításával lehet kikapcsolja **igaz** az alkalmazás-beállítás **MS_SkipVersionCheck**. Adja meg, vagy a Web.config fájlban, vagy az Azure-portálon Alkalmazásbeállítások szakaszában.

> [!NOTE]
> Számos viselkedésváltozások Mobile Services és a Mobile Apps, különösen a kapcsolat nélküli szinkronizálás, hitelesítés és leküldéses értesítések között. Csak kikapcsolja kell verzió ellenőrzése, hogy a viselkedés a módosítások nem törhetik az alkalmazás funkcióinak biztosításához teszt befejezése után.
>
>

## <a name="summary-of-compatibility-for-all-versions"></a>Az összes verzió kompatibilitási összegzése
Az alábbi táblázatban látható az összes ügyfél és kiszolgáló közötti kompatibilitást. A háttér vagy a Mobile lesz minősítve **szolgáltatások** vagy Mobile **alkalmazások** a kiszolgálón használt SDK-alapú.

|  | **Mobilszolgáltatások** Node.js vagy .NET | **Mobilalkalmazások** Node.js vagy .NET |
| --- | --- | --- |
| [A Mobile Services ügyfelek] |oké |Hiba történt\* |
| [Mobile Apps-ügyfelek] |Hiba történt\* |oké |

\*Ez is vezérelhető megadásával **MS_SkipVersionCheck**.

<!-- IMPORTANT!  The anchors for Mobile Services and Mobile Apps MUST be 1.0.0 and 2.0.0 respectively, since there is an exception error message that uses those anchors. -->

<!-- NOTE: the fwlink to this document is http://go.microsoft.com/fwlink/?LinkID=690568 -->

## <a name="1.0.0"></a>A Mobile Services-ügyfél és kiszolgáló
Az alábbi táblázat az ügyfél SDK-k kompatibilisek-e **Mobile Services**.

Megjegyzés: a Mobile Services-ügyfél SDK-k *nem* fejléc értéke küldési `ZUMO-API-VERSION`. Ha a szolgáltatás a fejléc vagy a lekérdezési karakterláncokra vonatkozó értéket kap olyan hibaüzenetet küld, kivéve, ha rendelkezik explicit módon választotta ki a fent leírt módon.

### <a name="MobileServicesClients"></a>Mobile *szolgáltatások* ügyfél SDK-k
| Ügyfélplatform | Verzió | A verziófejléc-érték |
| --- | --- | --- |
| Felügyelt ügyfél (Windows, Xamarin) |[1.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2) |n/a |
| iOS |[2.2.2](http://aka.ms/gc6fex) |n/a |
| Android |[2.0.3](https://go.microsoft.com/fwLink/?LinkID=280126) |n/a |
| HTML |[1.2.7](http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js) |n/a |

### <a name="mobile-services-server-sdks"></a>Mobile *szolgáltatások* server SDK-k
| Kiszolgáló platform | Verzió | Elfogadott verzió fejléc |
| --- | --- | --- |
| .NET |[WindowsAzure.MobileServices.Backend.* verzió 1.0.x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/) |** Nem verziófejléc ** |
| Node.js |(hamarosan elérhető) |**Nincs verzió fejléc** |

<!-- TODO: add Node npm version -->

### <a name="behavior-of-mobile-services-backends"></a>A Mobile Services háttérkiszolgálókon viselkedése
| ZUMO-API-VERZIÓ | MS_SkipVersionCheck értéke | Válasz |
| --- | --- | --- |
| Nincs megadva |Bármelyik |200 - OK |
| Bármely érték |True (Igaz) |200 - OK |
| Bármely érték |A megadott FALSE/nem |400 - Hibás kérés |

## <a name="2.0.0"></a>Az Azure Mobile Apps-ügyfél és kiszolgáló
### <a name="MobileAppsClients"></a>Mobile *alkalmazások* ügyfél SDK-k
Az SDK-ügyfél a következő verziójú indítása verzióellenőrzés jelent a **Azure Mobile Apps**:

| Ügyfélplatform | Verzió | A verziófejléc-érték |
| --- | --- | --- |
| Felügyelt ügyfél (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](http://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](http://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

<!-- TODO: add HTML version when released -->

### <a name="mobile-apps-server-sdks"></a>Mobile *alkalmazások* server SDK-k
Verzióellenőrzés szerepel a kiszolgáló SDK verzió a következő:

| Kiszolgáló platform | SDK | Elfogadott verzió fejléc |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[Azure-mobileszköz-alkalmazások)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Mobile Apps háttérkiszolgálókon viselkedése
| ZUMO-API-VERZIÓ | MS_SkipVersionCheck értéke | Válasz |
| --- | --- | --- |
| NULL értékű vagy x.y.z |True (Igaz) |200 - OK |
| NULL értékű |A megadott FALSE/nem |400 - Hibás kérés |
| 1.x.y |A megadott FALSE/nem |400 - Hibás kérés |
| 2.0.0-2.x.y |A megadott FALSE/nem |200 - OK |
| 3.0.0-3.x.y |A megadott FALSE/nem |400 - Hibás kérés |

## <a name="next-steps"></a>Következő lépések
* [Mobile szolgáltatás áttelepítése az Azure App Service]

[A Mobile Services ügyfelek]: #MobileServicesClients
[Mobile Apps-ügyfelek]: #MobileAppsClients


[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Mobile szolgáltatás áttelepítése az Azure App Service]: app-service-mobile-migrating-from-mobile-services.md
