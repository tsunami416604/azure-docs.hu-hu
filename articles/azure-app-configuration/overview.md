---
title: Mi az Azure app Configuration?
description: Az Azure app Configuration szolgáltatás áttekintése.
author: yegu-ms
ms.author: yegu
ms.service: azure-app-configuration
ms.topic: overview
ms.date: 02/24/2019
ms.openlocfilehash: 40630bbbbcea344fb74d8ad971eb4c808bf0c142
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495250"
---
# <a name="what-is-azure-app-configuration"></a>Mi az Azure app Configuration?

Az Azure app Configuration szolgáltatással központilag kezelhető az Alkalmazásbeállítások és a funkciók jelzői. A modern programok, különösen a felhőben futó programok általában számos, természetben terjesztett összetevővel rendelkeznek. A konfigurációs beállítások ezen összetevők között történő terjesztése a hibák elhárításához vezethet az alkalmazások központi telepítése során. Az alkalmazás konfigurációja segítségével tárolhatja az alkalmazás összes beállítását, és egy helyen biztonságossá teheti a hozzáférését.

Az alkalmazás konfigurációja jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verzió ideje alatt ingyenesen használható. A [Azure Portal](https://portal.azure.com)regisztrálhat.

## <a name="why-use-app-configuration"></a>Miért érdemes az alkalmazás konfigurációját használni?

A felhőalapú alkalmazások gyakran több virtuális gépen vagy tárolón futnak több régióban, és több külső szolgáltatást használnak. A robusztus és skálázható elosztott alkalmazások létrehozása kihívást jelent.

A különböző programozási módszerek segítenek a fejlesztőknek az alkalmazások készítésének egyre összetettebb bonyolultságával kapcsolatban. A [tizenkét faktoros alkalmazás](https://12factor.net/) például számos jól tesztelt építészeti mintát és ajánlott eljárásokat ismertet a felhőalapú alkalmazásokkal való használathoz. Az útmutató egyik kulcsfontosságú javaslata, hogy elkülönítse a konfigurációt a kódból. Ebben az esetben az alkalmazás konfigurációs beállításait a végrehajtható fájlon kívül kell tartani, és az alkalmazást a futtató környezetből vagy egy külső forrásból kell beolvasni.

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
* Adattitkosítások teljes egészében vagy az átvitel során
* Natív integráció népszerű keretrendszerekkel

Az alkalmazás konfigurációja kiegészíti [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), amely az alkalmazások titkos kulcsainak tárolására szolgál. Az alkalmazás konfigurálása megkönnyíti a következő forgatókönyvek megvalósítását:

* A különböző környezetek és földrajzi területek hierarchikus konfigurációs beállításainak központosított kezelése és elosztása
* Az alkalmazás beállításainak dinamikus módosítása anélkül, hogy újra kellene telepíteni vagy újraindítani egy alkalmazást
* A vezérlési funkció valós idejű rendelkezésre állása

## <a name="use-app-configuration"></a>Alkalmazás konfigurációjának használata

Az alkalmazások konfigurációs tárolójának az alkalmazáshoz való hozzáadásának legegyszerűbb módja a Microsoft által biztosított ügyféloldali kódtár. A programozási nyelv és keretrendszer alapján a következő legjobb módszerek érhetők el Önnek.

| Programozási nyelv és keretrendszer | A csatlakozás lépései |
|---|---|
| .NET Core és ASP.NET Core | Alkalmazás-konfigurációs szolgáltató a .NET Core-hoz |
| .NET-keretrendszer és ASP.NET | A .NET-hez készült app Configuration Builder |
| Java Spring | Alkalmazás-konfigurációs ügyfél a Spring Cloud-hoz |
| Egyebek | Alkalmazás-konfiguráció REST API |

## <a name="next-steps"></a>Következő lépések

* [ASP.NET Core rövid útmutató](./quickstart-aspnet-core-app.md)
* [.NET Core gyors útmutató](./quickstart-dotnet-core-app.md)
* [.NET-keretrendszer – gyors útmutató](./quickstart-dotnet-app.md)
* [Azure Functions rövid útmutató](./quickstart-azure-functions-csharp.md)
* [Java Spring rövid útmutató](./quickstart-java-spring-app.md)
* [ASP.NET Core funkció jelzője](./quickstart-feature-flag-aspnet-core.md)
* [A Spring boot szolgáltatás jelölője](./quickstart-feature-flag-spring-boot.md)
