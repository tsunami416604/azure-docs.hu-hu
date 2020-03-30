---
title: Ügyfél- és kiszolgálós SDK-verziószámozás
description: Az ügyfél SDK-k listája és kompatibilitás a kiszolgáló SDK-verzióival a mobilszolgáltatásokhoz és az Azure Mobile Apps-hez.
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: a9ba442c00ec2498139ee34a1ff7497c98f17ede
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293489"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Ügyfél- és kiszolgálóverzió-verziók a mobilalkalmazásokban és a mobilszolgáltatásokban

Az Azure Mobile Services legújabb verziója az Azure App Service **mobilalkalmazások** szolgáltatása.

A mobilalkalmazások ügyfél- és kiszolgálói SDK-k eredetileg a Mobilszolgáltatások szolgáltatásban találhatókon alapulnak, de *nem* kompatibilisek egymással.
Ez azt illeti, *mobilalkalmazások* ügyfélSDK-t kell használnia *a Mobile Apps* server SDK-val és hasonlóképpen a *mobilszolgáltatásokhoz.* Ezt a szerződést az ügyfél és a kiszolgáló SDK-k által használt speciális fejlécérték érvényesíti. `ZUMO-API-VERSION`

Megjegyzés: ha ez a dokumentum *egy mobilszolgáltatás-háttérprogramra* hivatkozik, nem feltétlenül kell a mobilszolgáltatásokban üzemeltetni. Mostantól lehetőség van egy mobilszolgáltatás átáttelepítésére az App Service-en kódmódosítás nélkül, de a szolgáltatás továbbra is a *Mobile Services* SDK-verzióit használná.

## <a name="header-specification"></a>Fejléc specifikációja
A `ZUMO-API-VERSION` kulcs megadható a HTTP fejlécben vagy a lekérdezési karakterláncban. Az érték **x.y.z**formátumú verziókarakterlánc.

Példa:

`GET https://service.azurewebsites.net/tables/TodoItem`

FEJLÉCEK: ZUMO-API-VERSION: 2.0.0

`POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0`

## <a name="opting-out-of-version-checking"></a>Leiratkozás a verzióellenőrzésről
A verzióellenőrzés letiltását úgy tilthatja le, hogy **true** értéket állít be az alkalmazásbeállításhoz **MS_SkipVersionCheck.** Adja meg ezt a web.config vagy az Azure Portal Alkalmazásbeállítások szakaszában.

> [!NOTE]
> A mobilszolgáltatások és a mobilalkalmazások között számos viselkedésbeli változás van, különösen az offline szinkronizálás, a hitelesítés és a leküldéses értesítések területén. Csak a teljes tesztelés után tiltsa le a verzióellenőrzést, hogy ezek a viselkedésbeli változások ne szakadjanak meg az alkalmazás funkcióinak.

## <a name="azure-mobile-apps-client-and-server"></a><a name="2.0.0"></a>Azure Mobile Apps ügyfél- és kiszolgáló
### <a name="mobile-apps-client-sdks"></a><a name="MobileAppsClients"></a>*Mobilalkalmazások* ügyfél SDK-i
A verzióellenőrzés az ügyfél SDK Azure Mobile **Apps**alkalmazáshoz való verziójával kezdődött:

| Ügyfélplatform | Verzió | Verziófejléc értéke |
| --- | --- | --- |
| Felügyelt ügyfél (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

### <a name="mobile-apps-server-sdks"></a>*Mobilalkalmazások* kiszolgálójának SDK-i
A verzióellenőrzés a kiszolgáló SDK következő verzióiban található:

| Kiszolgálóplatform | SDK | Elfogadott verziófejléc |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azúr-mobilalkalmazások)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>A mobilalkalmazások háttérrendszerek viselkedése
| ZUMO-API-VERZIÓ | A MS_SkipVersionCheck értéke | Válasz |
| --- | --- | --- |
| x.y.z vagy Null |True (Igaz) |200 - OK |
| Null |Hamis/nincs megadva |400 – Hibás kérés |
| 1.x.y |Hamis/nincs megadva |400 – Hibás kérés |
| 2.0.0-2.x.y |Hamis/nincs megadva |200 - OK |
| 3.0.0-3.x.y |Hamis/nincs megadva |400 – Hibás kérés |

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
