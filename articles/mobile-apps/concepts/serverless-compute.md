---
title: Kiszolgáló nélküli Mobile-alkalmazás létrehozása Azure Functions és más szolgáltatásokkal
description: Ismerje meg a Solid, kiszolgáló nélküli mobil alkalmazások háttérbe állításához használt számítási szolgáltatásokat.
author: codemillmatt
ms.service: mobile-services
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 06eb757955f403b91ae38602f4dc8ccc3fa52f26
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450975"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>Mobil háttérrendszer-összetevők kiépítése számítási szolgáltatásokkal
Minden mobil alkalmazásnak szüksége van egy háttérre, amely az adattárolás, az üzleti logika és a biztonság tekintetében felelős. Az infrastruktúra üzemeltetéséhez és a háttérbeli kód végrehajtásához több kiszolgáló méretezését, kiépítését és méretezését kell biztosítania. Emellett az operációs rendszer frissítéseit és az érintett hardverek kezelését, valamint a biztonsági javítások alkalmazását is meg kell adnia. Ezután figyelnie kell a teljesítményre, a rendelkezésre állásra és a hibatűrésre vonatkozó összes infrastruktúra-összetevőt. 

A kiszolgáló nélküli architektúra hasznosnak bizonyulhat az ilyen típusú forgatókönyvek esetében, mert nem felügyelt kiszolgálókat, illetve nem felügyelt operációs rendszert vagy kapcsolódó szoftvereket vagy hardver-frissítéseket tartalmaz. A kiszolgáló nélküli architektúra elmenti a fejlesztői időt és a költségeket, ami gyorsabb időt jelent a piacra jutáshoz és az alkalmazások kiépítéséhez.

## <a name="benefits-of-compute"></a>A számítási előnyök
- A kiszolgálók absztrakciója azt jelenti, hogy nem kell aggódnia az üzemeltetéssel, a javítással és a biztonsággal kapcsolatban, ami lehetővé teszi, hogy kizárólag a kódra koncentráljon.
- Az azonnali és hatékony skálázás biztosítja, hogy az erőforrások automatikusan vagy igény szerint legyenek kiépítve bármilyen méretezéssel.
- Magas rendelkezésre állás és hibatűrés.
- A mikro-számlázási szolgáltatás garantálja, hogy csak akkor számítunk fel díjat, ha a kód ténylegesen fut.
- A kód az Ön által választott nyelven írt felhőben fut.

A következő szolgáltatások segítségével engedélyezheti a kiszolgáló nélküli számítási funkciókat a Mobile apps szolgáltatásban.

## <a name="azure-functions"></a>Azure Functions
A [Azure functions](https://azure.microsoft.com/services/functions/) egy eseményvezérelt számítási felület, amellyel a kód a választott programozási nyelven íródott, és nem kell aggódnia a kiszolgálókon. A futtatásához nem kell kezelnie az alkalmazást vagy az infrastruktúrát. A függvények igény szerint méretezhetők, és csak a kód futásának idejére kell fizetni. Az Azure functions nagyszerű lehetőséget nyújt a mobil alkalmazások API-k megvalósítására. A szolgáltatás könnyen megvalósítható és karbantartható, és HTTP-n keresztül érhető el.

**A legfontosabb jellemzők**
- Eseményvezérelt és méretezhető, ahol eseményindítókat és kötéseket használhat a függvények meghívásakor és az általa összekapcsolt adatmennyiség meghatározásához.
- Saját függőségek használata, mivel a functions támogatja a NuGet és a NPM, így kedvenc kódtárait is használhatja.
- Integrált biztonság annak érdekében, hogy a HTTP-triggert használó függvények a OAuth-szolgáltatókkal, például a Azure Active Directory, a Facebook, a Google, a Twitter és a Microsoft-fiókkal is védve legyenek.
- Egyszerűsített integráció különböző [Azure-szolgáltatásokkal](/azure/azure-functions/functions-overview) és szolgáltatott szoftverekkel (SaaS).
- Rugalmas fejlesztés, amely lehetővé teszi, hogy a függvényeket közvetlenül a Azure Portal, vagy a folyamatos integráció beállítása és a kód üzembe helyezése a GitHub, az Azure DevOps Services és más támogatott fejlesztői eszközök segítségével.
- A functions futtatókörnyezet nyílt forráskódú, és elérhető a [githubon](https://github.com/azure/azure-webjobs-sdk-script).
- Továbbfejlesztett fejlesztési élmény, amely lehetővé teszi, hogy helyileg, a saját előnyben részesített szerkesztővel vagy könnyen használható webes felülettel végezzen nyomon követést, tesztelést és hibakeresést, integrált eszközökkel és beépített DevOps képességekkel.
- Számos programozási nyelv és üzemeltetési lehetőség a fejlesztéshez, mint például a C#, a Node. js, a Java, a JavaScript vagy a Python.
- A díjköteles díjszabás azt jelenti, hogy csak a kód futtatásával töltött idő után kell fizetnie.

**Referencia**
- [Azure Portal](https://portal.azure.com)
- [Az Azure Functions dokumentációja](/azure/azure-functions/)
- [Azure Functions – fejlesztői útmutató](/azure/azure-functions/functions-reference)
- [Rövid útmutatók](/azure/azure-functions/functions-create-first-function-vs-code)
- [Példák](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Azure App Service
A [Azure app Service](https://azure.microsoft.com/services/app-service/)segítségével az infrastruktúra kezelése nélkül hozhat létre és futtathat webalkalmazásokat és REST API-kat az Ön által választott programozási nyelven. Automatikus skálázást és magas rendelkezésre állást kínál, támogatja a Windows és a Linux rendszert, és lehetővé teszi a GitHub, az Azure DevOps vagy bármely git-tárház automatizált üzembe helyezését.

**A legfontosabb jellemzők**
- Több nyelv és keretrendszer támogatása a ASP.NET, a ASP.NET Core, a Java, a Ruby, a Node. js, a PHP és a Python számára. PowerShell- és egyéb szkripteket vagy futtatható fájlokat futtathat háttérszolgáltatásként.
- DevOps optimalizálás a folyamatos integráció és üzembe helyezés révén az Azure DevOps, a GitHub, a BitBucket, a Docker hub vagy a Azure Container Registry használatával. Alkalmazásait az App Service-ben az Azure PowerShell vagy a többplatformos parancssori felület (CLI) segítségével felügyelheti.
- Globális skálázás a magas rendelkezésre állással manuálisan vagy automatikusan vertikális felskálázáshoz.
- Kapcsolatok a SaaS-platformokhoz és a helyszíni adatokhoz több mint 50 összekötő közül választhatnak olyan nagyvállalati rendszerek esetében, mint az SAP, az SaaS-szolgáltatások, például a Salesforce, valamint az internetes szolgáltatások, például a Facebook. A helyszíni adatkapcsolatok hibrid és Azure-alapú virtuális hálózatok használatával érhetők el.
- A Azure App Service ISO, SOC és PCI szabványnak megfelelő. A felhasználók hitelesítése Azure Active Directory vagy a közösségi médiához, például a Google, a Facebook, a Twitter és a Microsoft szolgáltatáshoz való bejelentkezéssel. IP-címkorlátozásokat hozhat létre és kezelheti a szolgáltatásidentitásokat.
- Alkalmazás-sablonok az Azure Marketplace-en, például a WordPress, a Joomla és a Drupal alkalmazás-sablonok széles körének kiválasztásához.
- A Visual Studio dedikált eszközökkel való integrációja egyszerűsíti a létrehozás, telepítés és hibakeresés folyamatát.

**Referencia**
- [Azure Portal](https://portal.azure.com/)
- [Az Azure App Service dokumentációja](/azure/app-service/)
- [Rövid útmutatók](/azure/app-service/app-service-web-get-started-dotnet)
- [Példák](/azure/app-service/samples-cli)
- [Oktatóanyagok](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service
Az [Azure Kubernetes Service (ak)](https://azure.microsoft.com/services/kubernetes-service/) kezeli az üzemeltetett Kubernetes-környezetet. Az AK használatával gyorsan és egyszerűen helyezhet üzembe és kezelhet tároló alkalmazásokat a tároló-előkészítési szakértelem nélkül. Emellett kiküszöböli a folyamatban lévő műveletek és karbantartás terhét is. Az AK-ban igény szerint kiépítheti, frissítheti és méretezi az erőforrásokat, anélkül, hogy az alkalmazásokat offline állapotba helyezné.

**A legfontosabb jellemzők**
- A meglévő alkalmazások egyszerűen áttelepíthetők a tárolóba, és az AK-n belül futhatnak.
- Egyszerűsítse a Service-alapú alkalmazások üzembe helyezését és felügyeletét.
- Biztonságos DevOps az AK-hoz a sebesség és a biztonság egyensúlyának eléréséhez, és a kód gyorsabb méretezése.
- Az AK és a Azure Container Instances használatával könnyedén méretezheti a hüvelyeket a Container Instancesban, amelyek másodpercek alatt elkezdődnek.
- IoT-eszközök üzembe helyezése és kezelése igény szerint.
- Gépi tanulási modellek betanítása az eszközök, például a TensorFlow és a KubeFlow használatával.

**Referencia**
- [Azure Portal](https://portal.azure.com/)
- [Az Azure Kubernetes szolgáltatás dokumentációja](/azure/aks/)
- [Rövid útmutatók](/azure/aks/kubernetes-walkthrough-portal)
- [Oktatóanyagok](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
[Azure Container instances](https://azure.microsoft.com/services/container-instances/) nagyszerű megoldás minden olyan forgatókönyvhöz, amely elszigetelt tárolókban működhet, például egyszerű alkalmazásokkal, feladatok automatizálásával és felépítési feladatokkal. Gyorsan fejleszthet alkalmazásokat a virtuális gépek kezelése nélkül.

**A legfontosabb jellemzők**
- Gyors indítási idő Container Instances a virtuális gépek üzembe helyezése és kezelése nélkül is elindíthatja a tárolókat az Azure-ban másodpercek alatt.
- Nyilvános IP-kapcsolat és egyéni DNS-név.
- A hypervisor szintű biztonság, amely garantálja, hogy az alkalmazás egy tárolóban elszigetelt, mivel a virtuális gépen lenne.
- Egyéni méretek az optimális használathoz a CPU-magok és a memória pontos specifikációjának lehetővé tételével. A fizetés az igényelt erőforrások alapján történik másodpercalapú elszámolás szerint, így tényleges igényeinek megfelelően optimalizálhatja költségeit a legkisebb részletekig.
- Állandó tárterület az állapot lekéréséhez és megőrzéséhez. A Container Instances Azure Files-megosztások közvetlen csatlakoztatását kínálja.
- Azonos API-val ütemezett Linux-és Windows-tárolók.

**Referencia**
- [Azure Portal](https://portal.azure.com/)
- [Az Azure Container Instances dokumentációja](/azure/container-instances/)
- [Rövid útmutatók](/azure/container-instances/container-instances-quickstart-portal)
- [Példák](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Oktatóanyagok](/azure/container-instances/container-instances-tutorial-prepare-app)