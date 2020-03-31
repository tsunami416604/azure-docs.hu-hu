---
title: Az Azure App Configurationről
description: Az Azure App konfigurációs szolgáltatás áttekintése.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: overview
ms.date: 02/19/2020
ms.openlocfilehash: 1f1cec68813d33e7fa19a414a30adfc9a41df91f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77523475"
---
# <a name="what-is-azure-app-configuration"></a>Az Azure App Configurationről

Az Azure App Configuration szolgáltatás központilag kezelheti az alkalmazásbeállításokat és a szolgáltatásjelzőket. A modern programok, különösen a felhőben futó programok, általában számos összetevővel rendelkeznek, amelyek a természetben oszlanak meg. A konfigurációs beállítások ezen összetevők közötti terjesztése az alkalmazások központi telepítése során nehezen elhárítható hibákhoz vezethet. Az Alkalmazáskonfiguráció segítségével egy helyen tárolhatja az alkalmazás összes beállítását, és egy helyen biztosíthatja a hozzáférésüket.

## <a name="why-use-app-configuration"></a>Miért érdemes használni az Alkalmazáskonfigurációt?

A felhőalapú alkalmazások gyakran több virtuális gépen vagy tárolón futnak több régióban, és több külső szolgáltatást használnak. Egy robusztus és skálázható alkalmazás létrehozása elosztott környezetben jelentős kihívást jelent.

A különböző programozási módszerek segítenek a fejlesztőknek az alkalmazások készítésének növekvő összetettségének kezelésében. A [Twelve-Factor alkalmazás](https://12factor.net/) például számos jól tesztelt architekturális mintát és ajánlott eljárásokat ír le a felhőalapú alkalmazásokhoz. Az útmutató egyik legfontosabb javaslata a konfiguráció és a kód elkülönítése. Az alkalmazás konfigurációs beállításait a végrehajtható fájlon kívül kell tartani, és a futásidejű környezetből vagy egy külső forrásból kell beolvasni.

Bár bármely alkalmazás használhatja az alkalmazáskonfigurációt, a következő példák azok az alkalmazástípusok, amelyek számára előnyös a használata:

* Az Azure Kubernetes-szolgáltatáson, az Azure Service Fabricen vagy más, egy vagy több földrajzi területen üzembe helyezett tárolóba helyezett alkalmazásokon alapuló mikroszolgáltatások
* Kiszolgáló nélküli alkalmazások, amelyek magukban foglalják az Azure Functions-t vagy más, eseményvezérelt állapot nélküli számítási alkalmazásokat
* Folyamatos üzembe helyezési folyamat

Az Alkalmazáskonfiguráció a következő előnyöket kínálja:

* Egy teljes körűen felügyelt szolgáltatás, amely percek alatt beállítható
* Rugalmas kulcsábrázolások és leképezések
* Címkézés címkékkel
* A beállítások időponthoz való bejátszása
* Dedikált felhasználói felület a szolgáltatásjelzők kezeléséhez
* Két konfigurációkészlet összehasonlítása egyénileg definiált dimenziókon
* Fokozott biztonság az Azure által felügyelt identitások révén
* Bizalmas információk titkosítása nyugalmi és átvitel közben
* Natív integráció a népszerű keretekkel

Az alkalmazáskonfiguráció kiegészíti az [Azure Key Vaultot,](https://azure.microsoft.com/services/key-vault/)amely alkalmazástitok tárolására szolgál. Az Alkalmazáskonfiguráció megkönnyíti a következő forgatókönyvek megvalósítását:

* A hierarchikus konfigurációs adatok kezelésének és terjesztésének központosítása különböző környezetekben és földrajzi területeken
* Az alkalmazásbeállítások dinamikus módosítása alkalmazásújratelepítése vagy újraindítása nélkül
* A funkciók rendelkezésre állásának szabályozása valós időben

## <a name="use-app-configuration"></a>Az alkalmazáskonfiguráció használata

Az alkalmazáskonfigurációs tároló alkalmazáshoz való hozzáadásának legegyszerűbb módja a Microsoft által biztosított ügyféltár. A következő módszerek állnak rendelkezésre az alkalmazáshoz való csatlakozáshoz, a választott nyelvtől és keretrendszertől függően

| Programozási nyelv és keret | Csatlakozás |
|---|---|
| .NET Core és ASP.NET Core | Alkalmazáskonfigurációs szolgáltató a .NET Core alkalmazáskonfigurálásához |
| .NET Framework és ASP.NET | Alkalmazáskonfiguráció-szerkesztő a .NET alkalmazáshoz |
| Java Spring | Alkalmazáskonfigurációs ügyfél a Spring Cloud szolgáltatáshoz |
| Egyéb | Alkalmazáskonfigurációs REST API |

## <a name="next-steps"></a>További lépések

* [ASP.NET Core rövid útmutató](./quickstart-aspnet-core-app.md)
* [.NET Core rövid útmutató](./quickstart-dotnet-core-app.md)
* [A .](./quickstart-dotnet-app.md)
* [Az Azure Functions rövid útmutatója](./quickstart-azure-functions-csharp.md)
* [Java tavaszi rövid útmutató](./quickstart-java-spring-app.md)
* [ASP.NET Core szolgáltatás jelzőjának rövid útmutatója](./quickstart-feature-flag-aspnet-core.md)
* [Gyorsútmutató a Tavaszi rendszerindítás funkciójelzőről](./quickstart-feature-flag-spring-boot.md)
