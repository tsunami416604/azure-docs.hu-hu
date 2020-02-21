---
title: Az Azure App Configurationről
description: Az Azure app Configuration szolgáltatás áttekintése.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: overview
ms.date: 02/19/2020
ms.openlocfilehash: 1f1cec68813d33e7fa19a414a30adfc9a41df91f
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523475"
---
# <a name="what-is-azure-app-configuration"></a>Az Azure App Configurationről

Az Azure app Configuration szolgáltatással központilag kezelhető az Alkalmazásbeállítások és a funkciók jelzői. A modern programok, különösen a felhőben futó programok általában számos, természetben terjesztett összetevővel rendelkeznek. A konfigurációs beállítások ezen összetevők között történő terjesztése a hibák elhárításához vezethet az alkalmazások központi telepítése során. Az alkalmazás konfigurációja segítségével tárolhatja az alkalmazás összes beállítását, és egy helyen biztonságossá teheti a hozzáférését.

## <a name="why-use-app-configuration"></a>Miért érdemes az alkalmazás konfigurációját használni?

A felhőalapú alkalmazások gyakran több virtuális gépen vagy tárolón futnak több régióban, és több külső szolgáltatást használnak. A robusztus és skálázható alkalmazások elosztott környezetben való létrehozása jelentős kihívást jelent.

A különböző programozási módszerek segítenek a fejlesztőknek az alkalmazások készítésének egyre összetettebb bonyolultságával kapcsolatban. A [tizenkét faktoros alkalmazás](https://12factor.net/) például számos jól tesztelt építészeti mintát és ajánlott eljárásokat ismertet a felhőalapú alkalmazásokkal való használathoz. Az útmutató egyik kulcsfontosságú javaslata, hogy elkülönítse a konfigurációt a kódból. Az alkalmazás konfigurációs beállításait a végrehajtható fájlon kívül kell tartani, és a rendszerből kell beolvasni a futásidejű környezetből vagy egy külső forrásból.

Habár bármely alkalmazás használhatja az alkalmazások konfigurációját, az alábbi példák az alkalmazás használatának előnyeit hasznosító alkalmazások típusai:

* Az Azure Kubernetes szolgáltatáson, az Azure Service Fabricon vagy egy vagy több földrajzi régióban üzembe helyezett más, tárolóban lévő alkalmazásokon alapuló szolgáltatások
* Kiszolgáló nélküli alkalmazások, amelyek Azure Functions vagy más eseményvezérelt állapot nélküli számítási alkalmazásokat tartalmaznak
* Folyamatos üzembe helyezési folyamat

Az alkalmazás konfigurálása a következő előnyöket kínálja:

* Egy teljes körűen felügyelt szolgáltatás, amely percek alatt beállítható
* Rugalmas kulcsos ábrázolások és leképezések
* Címkézés címkékkel
* Beállítások időponthoz való ismétlése
* Dedikált felhasználói felület a szolgáltatás-jelölők felügyeletéhez
* Két konfiguráció összehasonlítása egyéni által definiált dimenziókra
* Fokozott biztonság az Azure által felügyelt identitások révén
* Bizalmas adatok titkosítása a nyugalmi állapotban és az átvitel során
* Natív integráció népszerű keretrendszerekkel

Az alkalmazás konfigurációja kiegészíti [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), amely az alkalmazások titkos kulcsainak tárolására szolgál. Az alkalmazás konfigurálása megkönnyíti a következő forgatókönyvek megvalósítását:

* A különböző környezetek és földrajzi területek hierarchikus konfigurációs beállításainak központosított kezelése és elosztása
* Az alkalmazás beállításainak dinamikus módosítása anélkül, hogy újra kellene telepíteni vagy újraindítani egy alkalmazást
* A vezérlési funkció valós idejű rendelkezésre állása

## <a name="use-app-configuration"></a>Alkalmazás konfigurációjának használata

Az alkalmazások konfigurációs tárolójának az alkalmazáshoz való hozzáadásának legegyszerűbb módja a Microsoft által biztosított ügyféloldali kódtár. A választott nyelvtől és keretrendszertől függően az alábbi módszerek érhetők el az alkalmazáshoz való csatlakozáshoz

| Programozási nyelv és keretrendszer | Csatlakozás |
|---|---|
| .NET Core and ASP.NET Core | Alkalmazás-konfigurációs szolgáltató a .NET Core-hoz |
| .NET-keretrendszer és ASP.NET | A .NET-hez készült app Configuration Builder |
| Java Spring | Alkalmazás-konfigurációs ügyfél a Spring Cloud-hoz |
| Egyéb | Alkalmazás-konfiguráció REST API |

## <a name="next-steps"></a>Következő lépések

* [ASP.NET Core rövid útmutató](./quickstart-aspnet-core-app.md)
* [.NET Core gyors útmutató](./quickstart-dotnet-core-app.md)
* [.NET-keretrendszer – gyors útmutató](./quickstart-dotnet-app.md)
* [Azure Functions rövid útmutató](./quickstart-azure-functions-csharp.md)
* [Java Spring rövid útmutató](./quickstart-java-spring-app.md)
* [ASP.NET Core funkció jelzője](./quickstart-feature-flag-aspnet-core.md)
* [A Spring boot szolgáltatás jelölője](./quickstart-feature-flag-spring-boot.md)
