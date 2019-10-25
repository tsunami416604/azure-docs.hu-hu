---
title: Kiszolgáló nélküli mobil alkalmazás-háttér létrehozása Azure Functions és más szolgáltatásokkal
description: Ismerje meg a számítási szolgáltatásokat a Solid, Server nélküli Mobile Application háttérrendszer létrehozásához.
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: b8378be675b86ada10b8442c40a54b635ee115a9
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795860"
---
# <a name="build-mobile-backend-components-with-compute-services"></a>Mobil háttér-összetevők kiépítése számítási szolgáltatásokkal
Minden mobil alkalmazásnak szüksége van egy olyan háttérre, amely az adattárolás, az üzleti logika és a biztonság tekintetében felelős. Az infrastruktúra üzemeltetéséhez és végrehajtásához a háttérrendszer kódja megköveteli a több kiszolgáló méretezését, kiépítését és méretezését, az operációs rendszer frissítéseinek és hardverének kezelését, a biztonsági javítások alkalmazását, majd a teljesítményre vonatkozó összes infrastruktúra-összetevő figyelését. rendelkezésre állás és hibatűrés. A kiszolgáló nélküli architektúra hasznosnak bizonyulhat, mivel a fejlesztők nem kezelhetik a felügyelni kívánt kiszolgálókat, sem az operációs rendszert, sem a kapcsolódó szoftver/hardveres frissítéseket. Számos fejlesztői időt és pénzt takaríthat meg, ami gyorsabb és célirányos energiát jelent az alkalmazások létrehozásához.

## <a name="benefits-of-compute"></a>A számítási előnyök
- Kiszolgálók absztrakciója: nem kell aggódnia az üzemeltetéssel, a javítással és a biztonsággal kapcsolatban, így a fejlesztők csak a kódra koncentrálnak.
- Az azonnali és hatékony skálázás biztosítja, hogy az erőforrások automatikusan vagy igény szerint legyenek kiépítve bármilyen méretezéssel, amire szüksége van.
- Magas rendelkezésre állás és hibatűrés.
- A Micro számlázási szolgáltatás garantálja, hogy csak akkor számítunk fel díjat, ha a kód ténylegesen fut.
- A felhőben futó kód írása a választott nyelven.

A következő szolgáltatások segítségével engedélyezheti a kiszolgáló nélküli számítási funkciókat a Mobile apps szolgáltatásban.

## <a name="azure-functions"></a>Azure Functions
A [Azure functions](https://azure.microsoft.com/services/functions/) egy eseményvezérelt számítási felület, amely lehetővé teszi, hogy az Ön által választott programozási nyelven írt kódot végrehajtsa anélkül, hogy a kiszolgálókkal kellene aggódnia. A fejlesztőknek nem kell felügyelni az alkalmazást vagy az infrastruktúrát a futtatásához. A függvények igény szerint méretezhetők, és csak a kód futásának idejére kell fizetni. A Azure Functions nagyszerű lehetőséget biztosít a mobil alkalmazások API-k megvalósítására, mivel ezek nagyon egyszerűek a HTTP-n keresztül történő megvalósításához és karbantartásához.

**Főbb funkciók**
- **Eseményvezérelt és skálázható** , ahol a fejlesztők **eseményindítókat és kötéseket** használhatnak a függvények meghívásakor és az általa összekapcsolt adatmennyiség meghatározásához.
- **Saját függőségek használata** – A Functions a NuGetet és az NPM-et is támogatja, így Ön a kedvenc könyvtárait használhatja.
- A **beépített biztonság** lehetővé teszi a http által aktivált függvények, például a Azure Active Directory, a Facebook, a Google, a Twitter és a Microsoft-fiók OAuth-szolgáltatókkal való védelmére.
- **Egyszerűsített integráció** különböző [Azure-szolgáltatásokkal](/azure/azure-functions/functions-overview#integrations) és szolgáltatott szoftveres (SaaS) ajánlatokkal.
- A **rugalmas fejlesztés** lehetővé teszi a függvények közvetlenül a portálon való kódolását, vagy a folyamatos integráció beállítását, valamint a kód üzembe helyezését a GitHub, az Azure DevOps Services és más támogatott fejlesztői eszközök segítségével.
- A functions futtatókörnyezet **nyílt forráskódú** , és elérhető a [githubon](https://github.com/azure/azure-webjobs-sdk-script).
-  **Továbbfejlesztett fejlesztési élmény** , ahol a fejlesztők a saját előnyben részesített szerkesztővel vagy könnyen használható webes felülettel kezelhetik, ellenőrizhetik és lekereshetik az integrált eszközökkel és beépített DevOps-képességekkel.
- **Számos programozási nyelv és üzemeltetési lehetőség** – fejlesztés a, C#a Node. js, a Java, a JavaScript vagy a Python használatával.
- **Használatalapú fizetési modell** – Csak annyi időért kell fizetnie, amennyit a kódja fut.

**Hivatkozik**
- [Azure Portalra](https://portal.azure.com)
- [Dokumentáció](/azure/azure-functions/)
- [Azure Functions fejlesztői útmutató](/azure/azure-functions/functions-reference)
- [Rövid útmutatók](/azure/azure-functions/functions-create-first-function-vs-code)
- [Minták](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="app-service"></a>App Service
[Azure app Service](https://azure.microsoft.com/services/app-service/) A Azure App Service lehetővé teszi a webalkalmazások és a REST-alapú API-k kiépítését és üzemeltetését az infrastruktúra kezelése nélkül. Automatikus méretezést és magas rendelkezésre állást kínál, támogatja a Windows és a Linux rendszert is, valamint lehetővé teszi az automatikus üzembe helyezéseket a GitHub, az Azure DevOps vagy bármely egyéb Git-adattár használatával.

**Főbb funkciók**
- **Több nyelv és keretrendszer** támogatja a ASP.net, a ASP.net Core, a Java, a Ruby, a Node. js, a PHP és a Python használatát. A PowerShell és más parancsfájlok vagy végrehajtható fájlok háttér-szolgáltatásként is futtathatók.
- **DevOps-optimalizálás** – folyamatos integrációt és üzembe helyezést állíthat be az Azure DevOps, a GitHub, a BitBucket, a Docker hub vagy a Azure Container Registry számára. Az alkalmazásait a App Service Azure PowerShell vagy a többplatformos parancssori felület (CLI) használatával kezelheti.
- **Globális skálázás a magas rendelkezésre állással** manuálisan vagy automatikusan vertikális felskálázáshoz.
- **A SaaS-platformokhoz és helyszíni adatokhoz való kapcsolódás** több mint 50 összekötő közül választhat a nagyvállalati rendszerekhez (például SAP), az SaaS-szolgáltatásokhoz (például a Salesforce) és az internetes szolgáltatásokhoz (például a Facebookhoz). Hibrid kapcsolatok és Azure-beli virtuális hálózatok használatával férhet hozzá a helyszíni adataihoz.
- **Biztonság és megfelelőség** – a Azure app Service ISO, SoC és PCI szabványoknak megfelelő. Felhasználók hitelesítése Azure Active Directory vagy közösségi bejelentkezéssel (Google, Facebook, Twitter és Microsoft). Hozzon létre IP-cím korlátozásokat, és kezelje a szolgáltatás identitásait.
- **Alkalmazás-sablonok** az Azure Marketplace-en, például a WordPress, a Joomla és a Drupal alkalmazás sablonjainak széles körének kiválasztásához.
- A Visual Studio- **integráció** a Visual Studióban dedikált eszközökkel egyszerűsíti a létrehozás, telepítés és hibakeresés folyamatát.

**Hivatkozik**
- [Azure Portalra](https://portal.azure.com/)
- [Dokumentáció](/azure/app-service/)
- [Rövid útmutatók](/azure/app-service/app-service-web-get-started-dotnet)
- [Minták](/azure/app-service/samples-cli)
- [oktatóanyagokat](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)
Az [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) felügyeli az üzemeltetett Kubernetes környezetet, lehetővé téve a tárolóalapú alkalmazások gyors és egyszerű üzembe helyezését és felügyeletét tárolóvezénylési szakértelem nélkül is. Ezenkívül a folyamatban lévő műveletek és karbantartás terhét is megszünteti az erőforrások igény szerinti kiépítésével, frissítésével és méretezésével anélkül, hogy offline állapotba kellene helyezni az alkalmazásait. 

**Főbb funkciók**
- A **meglévő alkalmazások egyszerűen áttelepíthetők** a tárolóba, és az AK-n belül futhatnak.
- **Egyszerűsítse a Service-alapú alkalmazások üzembe helyezését és felügyeletét** .
- **Biztonságos DevOps az AK** -hoz a sebesség és a biztonság egyensúlyának eléréséhez, és a kód gyorsabb méretezése.
- Az **AK és ACI használatával könnyedén méretezheti** a hüvelyeket, amelyek másodpercek alatt elkezdődnek.
- **IoT az eszközök üzembe helyezését és felügyeletét** igény szerint.
- **Gépi tanulási modell tanítása** eszközök, például a TensorFlow és a KubeFlow használatával.

**Hivatkozik**
- [Azure Portalra](https://portal.azure.com/)
- [Dokumentáció](/azure/aks/)
- [Rövid útmutatók](/azure/aks/kubernetes-walkthrough-portal)
- [oktatóanyagokat](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
[Azure Container instances (ACI)](https://azure.microsoft.com/services/container-instances/) nagyszerű megoldás minden olyan forgatókönyvhöz, amely elszigetelt tárolókban működhet, beleértve az egyszerű alkalmazásokat, a feladatok automatizálását és a felépítési feladatokat. Gyorsan fejleszthet alkalmazásokat a virtuális gépek kezelése nélkül.

**Főbb funkciók**
- **Gyors indítási idő** az ACI-ban másodpercek alatt elindíthatja a tárolókat az Azure-ban anélkül, hogy virtuális gépeket kellene kiépíteni és kezelni.
- **Nyilvános IP-kapcsolat és egyéni DNS-név**.
- A **hypervisor szintű biztonság** , amely garantálja, hogy az alkalmazás egy tárolóban elszigetelt, mivel a virtuális gépen lenne.
- **Egyéni méretek** az optimális használathoz a CPU-magok és a memória pontos specifikációjának lehetővé tételével. A fizetés az igényelt erőforrások alapján történik másodpercalapú elszámolás szerint, így tényleges igényeinek megfelelően optimalizálhatja költségeit a legkisebb részletekig.
- Az **állandó tárterület** az állapot lekérése és megtartása érdekében az ACI közvetlen Azure Files-megosztást biztosít.
- Azonos API-val ütemezett **Linux-és Windows-tárolók** .

**Hivatkozik**
- [Azure Portalra](https://portal.azure.com/)
- [Dokumentáció](/azure/container-instances/)
- [Rövid útmutatók](/azure/container-instances/container-instances-quickstart-portal)
- [Minták](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [oktatóanyagokat](/azure/container-instances/container-instances-tutorial-prepare-app)