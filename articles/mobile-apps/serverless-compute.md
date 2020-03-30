---
title: Kiszolgáló nélküli mobilalkalmazás-háttérrendszer létrehozása az Azure Functions és más szolgáltatások segítségével
description: Ismerje meg a megbízható, kiszolgáló nélküli mobilalkalmazás-háttérrendszer létrehozásához használt számítási szolgáltatásokat.
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: d6a2bbc984a8808d2cd8a9b292e55d80f43f2e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240149"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>Mobil háttér-összetevők létrehozása számítási szolgáltatásokkal
Minden mobilalkalmazásnak szüksége van egy háttérrendszerre, amely felelős az adattárolásért, az üzleti logikáért és a biztonságért. Az infrastruktúra kezelése a háttérkód üzemeltetéséhez és végrehajtásához több kiszolgáló méretezését, üzembe telepítését és méretezését igényli. Emellett kezelnie kell az operációs rendszer frissítéseit és az érintett hardvereket, és alkalmaznia kell a biztonsági javításokat. Ezután figyelnie kell az összes ilyen infrastruktúra-összetevőt a teljesítmény, a rendelkezésre állás és a hibatűrés szempontjából. 

A kiszolgáló nélküli architektúra hasznos lehet az ilyen típusú esetekben, mert nincs kezelhető kiszolgáló, és nincs operációs rendszer vagy kapcsolódó szoftver vagy hardverfrissítés. A kiszolgáló nélküli architektúra időt és költséget takarít meg a fejlesztők számára, ami gyorsabb piacra kerülést és az alkalmazások készítésére összpontosító energiát jelent.

## <a name="benefits-of-compute"></a>A számítás előnyei
- A szerverek absztrakciója azt jelenti, hogy nem kell aggódnia a tárhely, a javítás és a biztonság miatt, ami lehetővé teszi, hogy kizárólag a kódra összpontosítson.
- Az azonnali és hatékony skálázás biztosítja, hogy az erőforrások kiépítése automatikusan vagy igény szerint, bármilyen méretekben van szüksége.
- Magas rendelkezésre állás és hibatűvel.
- A mikroszámlázás biztosítja, hogy csak akkor kell fizetnie, ha a kód ténylegesen fut.
- A kód az Ön által választott nyelven írt felhőben fut.

Az alábbi szolgáltatások segítségével kiszolgáló nélküli számítási lehetőségeket engedélyezhet a mobilalkalmazásokban.

## <a name="azure-functions"></a>Azure Functions
[Az Azure Functions](https://azure.microsoft.com/services/functions/) egy eseményvezérelt számítási élmény, amely segítségével futtathatja a kódot, az Ön által választott programozási nyelven írva, anélkül, hogy aggódnia kellene a kiszolgálók miatt. Nem kell kezelnie az alkalmazást vagy az infrastruktúrát a futtatásához. A függvények igény szerint méretezhetők, és csak a kód futásának idejéért kell fizetnie. Az Azure-függvények nagyszerű lehetőséget jelentenek egy mobilalkalmazás API-jának megvalósítására. Könnyen megvalósíthatók és karbantarthatók, és HTTP-n keresztül érhetők el.

**A legfontosabb jellemzők**
- Eseményvezérelt és méretezhető, ahol az eseményindítók és kötések segítségével meghatározhatja, hogy egy függvény mikor hívható meg, és milyen adatokat kapcsol össze.
- Hozza létre saját függőségeit, mert a Functions támogatja a NuGet és az NPM funkciót, így használhatja kedvenc könyvtárait.
- Integrált biztonság, így megvédheti a HTTP-aktivált függvényeket az OAuth-szolgáltatókkal, például az Azure Active Directoryval, a Facebookkal, a Google-lal, a Twitterrel és a Microsoft-fiókkal.
- Egyszerűsített integráció a különböző [Azure-szolgáltatásokkal](/azure/azure-functions/functions-overview) és szoftverekkel szolgáltatásként (SaaS).
- Rugalmas fejlesztés, így közvetlenül az Azure Portalon kódolhatja a funkcióit, vagy folyamatos integrációt állíthat be, és telepítheti a kódot a GitHubon, az Azure DevOps-szolgáltatásokon és más támogatott fejlesztői eszközökön keresztül.
- A függvények futásideje nyílt forráskódú és elérhető a [GitHubon.](https://github.com/azure/azure-webjobs-sdk-script)
- Továbbfejlesztett fejlesztési élmény, ahol helyileg kódolhat, tesztelhet és debugolhat az előnyben részesített szerkesztő vagy könnyen használható webes felület használatával, integrált eszközökkel és beépített DevOps-képességekkel.
- Különböző programozási nyelvek és üzemeltetési lehetőségek a fejlesztéshez, például C#, Node.js, Java, JavaScript vagy Python.
- A használatalapú díjszabási modell azt jelenti, hogy csak a kód futtatásával töltött időért kell fizetnie.

**Referencia**
- [Azure-portál](https://portal.azure.com)
- [Az Azure Functions dokumentációja](/azure/azure-functions/)
- [Azure Functions – fejlesztői útmutató](/azure/azure-functions/functions-reference)
- [Rövid útmutatók](/azure/azure-functions/functions-create-first-function-vs-code)
- [Minták](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Azure App Service
Az [Azure App Service segítségével](https://azure.microsoft.com/services/app-service/)az infrastruktúra kezelése nélkül hozhat létre és üzemeltethet webalkalmazásokat és RESTful API-kat az Ön által választott programozási nyelven. Automatikus skálázást és magas rendelkezésre állást kínál, támogatja a Windows és a Linux rendszert, és lehetővé teszi a GitHub, az Azure DevOps vagy bármely Git-tárkocsi automatikus telepítését.

**A legfontosabb jellemzők**
- Több nyelvi és keretrendszer támogatása ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP vagy Python. PowerShell- és egyéb szkripteket vagy futtatható fájlokat futtathat háttérszolgáltatásként.
- DevOps-optimalizálás az Azure DevOps, a GitHub, a BitBucket, a Docker Hub vagy az Azure Container Registry folyamatos integrációja és üzembe helyezése révén. Alkalmazásait az App Service-ben az Azure PowerShell vagy a többplatformos parancssori felület (CLI) segítségével felügyelheti.
- Globális méretezés magas rendelkezésre állással a manuális vagy automatikus méretezéshez.
- SaaS-platformokkal és helyszíni adatokkal való kapcsolatok, amelyek több mint 50 összekötő közül választhatnak a vállalati rendszerekhez, például az SAP-hoz, a SaaS-szolgáltatásokhoz, például a Salesforce-hoz és az internetes szolgáltatásokhoz, például a Facebookhoz. A helyszíni adatok elérése hibrid kapcsolatok és az Azure virtuális hálózatok használatával.
- Az Azure App Service ISO, SOC és PCI-kompatibilis. Hitelesítse a felhasználókat az Azure Active Directoryval, vagy jelentkezzen be a közösségi médiában, például a Google, a Facebook, a Twitter és a Microsoft. IP-címkorlátozásokat hozhat létre és kezelheti a szolgáltatásidentitásokat.
- Alkalmazássablonok az Azure Marketplace-en az alkalmazássablonok széles listájából választhatók, például a WordPress, a Joomla és a Drupal.
- A Visual Studio és a Visual Studio dedikált eszközeivel való integráció leegyszerűsíti a létrehozás, üzembe helyezés és hibakeresés munkáját.

**Referencia**
- [Azure-portál](https://portal.azure.com/)
- [Az Azure App Service dokumentációja](/azure/app-service/)
- [Rövid útmutatók](/azure/app-service/app-service-web-get-started-dotnet)
- [Minták](/azure/app-service/samples-cli)
- [Oktatóanyagok](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service
[Az Azure Kubernetes-szolgáltatás (AKS)](https://azure.microsoft.com/services/kubernetes-service/) kezeli a üzemeltetett Kubernetes-környezetet. Az AKS gyors és egyszerű üzembe helyezését és kezelését teszi a tárolóvezénylési szakértelem nélkül. Emellett kiküszöböli a folyamatban lévő műveletek és karbantartás terhét. Az AKS-rendelkezések, frissítések és méretezések az erőforrásokigény szerint, az alkalmazások offline állapotba helyezése nélkül.

**A legfontosabb jellemzők**
- Egyszerűen áttelepítheti a meglévő alkalmazásokat a tárolókba, és futtatható az AKS-en belül.
- Egyszerűsítse a mikroszolgáltatásokon alapuló alkalmazások üzembe helyezését és felügyeletét.
- Biztonságos DevOps az AKS-hez a sebesség és a biztonság közötti egyensúly eléréséhez és a kód gyorsabb, nagy méretekben való biztosításához.
- Könnyedén skálázható az AKS és az Azure Container Instances használatával a másodpercek alatt induló tárolópéldányokon belüli podok kiépítéséhez.
- Igény szerint telepítheti és kezelheti az IoT-eszközöket.
- Gépi tanulási modellek betanítása olyan eszközök használatával, mint a TensorFlow és a KubeFlow.

**Referencia**
- [Azure-portál](https://portal.azure.com/)
- [Az Azure Kubernetes-szolgáltatás dokumentációja](/azure/aks/)
- [Rövid útmutatók](/azure/aks/kubernetes-walkthrough-portal)
- [Oktatóanyagok](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
[Az Azure Container Instances](https://azure.microsoft.com/services/container-instances/) egy nagyszerű megoldás minden olyan forgatókönyv, amely elszigetelt tárolókban, például egyszerű alkalmazásokban, feladatautomatizálásban és build feladatokban működhet. Gyorsan fejleszthet alkalmazásokat a virtuális gépek kezelése nélkül.

**A legfontosabb jellemzők**
- Gyors indítási idő, mint a container példányok másodpercek alatt elindíthatja a tárolókat az Azure-ban, anélkül, hogy üzembe helyezni és kezelni a virtuális gépeket.
- Nyilvános IP-kapcsolat és egyéni DNS-név.
- Hypervisor-szintű biztonság, amely garantálja az alkalmazás olyan elszigetelt egy tárolóban, mint egy virtuális gép.
- Egyedi méretek az optimális kihasználtság érdekében, lehetővé téve a CPU magok és a memória pontos specifikációit. A fizetés az igényelt erőforrások alapján történik másodpercalapú elszámolás szerint, így tényleges igényeinek megfelelően optimalizálhatja költségeit a legkisebb részletekig.
- Állandó tároló az állapot beolvasásához és megőrzéséhez. A Container Instances az Azure Files-megosztások közvetlen csatlakoztatását kínálja.
- Linux és Windows-tárolók ütemezve ugyanazzal az API-val.

**Referencia**
- [Azure-portál](https://portal.azure.com/)
- [Az Azure Container Instances dokumentációja](/azure/container-instances/)
- [Rövid útmutatók](/azure/container-instances/container-instances-quickstart-portal)
- [Minták](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Oktatóanyagok](/azure/container-instances/container-instances-tutorial-prepare-app)