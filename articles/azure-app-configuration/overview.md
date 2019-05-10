---
title: Mi az Azure-alkalmazások konfigurálása? | Microsoft Docs
description: Az Azure App konfigurációs szolgáltatás áttekintése.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 985845197f8a1ece76fe0a620f05194109f51bd6
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408668"
---
# <a name="what-is-azure-app-configuration"></a>Mi az Azure-alkalmazások konfigurálása?

Az Azure App konfigurálása központi spravovat nastavení aplikace és jelzők funkció szolgáltatást biztosít. A modern programok, különösen egy felhőben futó programok általában számos összetevőből jellegű elosztott rendelkezik. Konfigurációs beállítások terjedhetnek ezen összetevők között vezethet rögzített hibaelhárítása hibák az alkalmazások központi telepítése során. Használhatja az Alkalmazáskonfigurációt tárolja az alkalmazás a beállításait, és tegye biztonságossá a hozzáfér egy helyen.

Alkalmazáskonfiguráció jelenleg nyilvános előzetes verzióban érhető el. Díjmentes használata az előzetes verzió ideje alatt. Iratkozzon fel a hozzá tartozó a [az Azure portal](https://portal.azure.com).

## <a name="why-use-app-configuration"></a>Miért használhatja az Alkalmazáskonfigurációt?

Felhőalapú alkalmazások gyakran több virtuális gépek és tárolók több régióban is futtathatja, és több külső szolgáltatások használata. Elosztott alkalmazás létrehozása, amely hatékony, és méretezhető kihívást jelent.

Különböző programozási módszerek segítségével a fejlesztők alkalmazásokat növekvő összetettsége foglalkozik. Például a 12 tényezős alkalmazásfejlesztési ismerteti számos tesztelt architektúramintákat és ajánlott eljárások a felhőbeli alkalmazásokhoz való használatra. Ez az útmutató egy kulcs ajánljuk, hogy külön konfigurációs code-ból. Ebben az esetben egy alkalmazás-konfigurációs beállítások tartani a végrehajtható fájl külső kell, és a futtatókörnyezet vagy külső forrásból olvas be.

Bármilyen alkalmazást is győződjön meg arról, az alkalmazás konfigurációját használja, az alábbi példák is használható a előnyeit kihasználó alkalmazástípusok:

* Mikroszolgáltatás-alapú Azure Kubernetes Service-ben, az Azure Service Fabric vagy egy vagy több földrajzi területeken telepített egyéb tárolóalapú alkalmazások
* Kiszolgáló nélküli alkalmazások, többek között az Azure Functions vagy más állapot nélküli számítási eseményvezérelt alkalmazásokat
* Folyamatos készregyártás

Alkalmazások konfigurálása az alábbi előnyöket biztosítja:

* Egy teljes körűen felügyelt szolgáltatás, amely percek alatt beállíthatja
* Rugalmas kulcs nyilatkozatok és leképezések
* Címkézés, a címkék
* Ismétlés időponthoz beállítások
* A szolgáltatás jelző felügyeleti dedikált felhasználói felülete
* Két készlet egyénileg definiált dimenziókra konfigurációk összehasonlítása
* Fokozott biztonsági keresztül az Azure által felügyelt identitásokból
* Teljes körű titkosítás, inaktív és átvitel közben
* Natív integrációt olyan népszerű keretrendszerekkel

Kiegészíti az Alkalmazáskonfigurációt [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), amely a titkos alkalmazáskulcsok tárolására szolgál. Alkalmazások konfigurálása megkönnyíti a megvalósítása a következő esetekben:

* Felügyeleti és terjesztési a különböző környezetekben és földrajzi területről hierarchikus konfigurációs adatok központosítása
* Dinamikus módosítását az alkalmazás beállításait, hogy újból üzembe helyeznie vagy egy alkalmazás újraindítása nélkül
* Valós idejű vezérlés szolgáltatások elérhetősége

## <a name="use-app-configuration"></a>Használhatja az Alkalmazáskonfigurációt

A legegyszerűbben úgy, hogy adjon hozzá egy ügyfélkódtáron egy alkalmazás a konfigurációs adattárolónál az alkalmazásnak, hogy a Microsoft biztosít. A programozási nyelv és keretrendszer alapul, az alábbi ajánlott módszerek lesznek elérhetők.

| Programozási nyelv és keretrendszer | Csatlakozás |
|---|---|
| .NET Core and ASP.NET Core | Alkalmazás konfigurációszolgáltatót a .NET Core |
| .NET and ASP.NET | Alkalmazás konfigurációja builder a .NET-hez |
| Java Spring | Alkalmazás-konfigurációs ügyfél Spring Cloud |
| Továbbiak | Alkalmazáskonfiguráció – REST API |

## <a name="next-steps"></a>További lépések

* [ASP.NET Core a rövid útmutató](./quickstart-aspnet-core-app.md)
* [.NET core rövid útmutató](./quickstart-dotnet-core-app.md)
* [.NET-keretrendszer rövid útmutató](./quickstart-dotnet-app.md)
* [Azure függvény rövid útmutató](./quickstart-azure-function-csharp.md)
* [Java Spring a rövid útmutató](./quickstart-java-spring-app.md)
* [ASP.NET Core szolgáltatás jelzőt a rövid útmutató](./quickstart-feature-flag-aspnet-core.md)
