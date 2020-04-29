---
title: Az Azure Functions áttekintése
description: Megtudhatja, hogyan optimalizálhat aszinkron számítási feladatokat percek alatt az Azure Functions használatával.
author: mattchenderson
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 01/16/2020
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f0948cb58ba9403a34fbfd61ec43c29bed3440bc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77621006"
---
# <a name="an-introduction-to-azure-functions"></a>Az Azure Functions bemutatása

Azure Functions lehetővé teszi, hogy kisebb kódrészleteket ("functions") futtasson anélkül, hogy az alkalmazás-infrastruktúrával kellene foglalkoznia. A Azure Functions segítségével a felhőalapú infrastruktúra biztosítja az alkalmazásnak a nagy léptékű futtatásához szükséges naprakész kiszolgálókat.

Egy függvényt egy adott típusú esemény "aktivál". A [támogatott eseményindítók](./functions-triggers-bindings.md) például az adatváltozások megválaszolására, az üzenetek megválaszolására, az ütemezett futtatásra vagy egy HTTP-kérelem eredményére reagálnak.

Habár a közvetlenül a számtalan szolgáltatással való integrációt végezheti el, a más szolgáltatásokkal való integrálás a kötések használatával egyszerűsíthető. A kötések lehetővé teszi, [hogy az Azure és a harmadik féltől származó szolgáltatások széles köréhez férhet hozzá](./functions-triggers-bindings.md).

## <a name="features"></a>Szolgáltatások

A Azure Functions főbb funkciói a következők:

- **Kiszolgáló nélküli alkalmazások**: a függvények lehetővé teszik [kiszolgáló](https://azure.microsoft.com/solutions/serverless/) nélküli alkalmazások fejlesztését Microsoft Azureon.

- **Nyelv kiválasztása**: a függvények a [C#, a Java, a JavaScript, a Python és a PowerShell](supported-languages.md)használatával választhatók.

- **Díjköteles díjszabási modell**: csak a kód futtatásával töltött idő után kell fizetnie. Tekintse meg a Használatalapú futtatási csomag lehetőséget a [díjszabás szakaszban](#pricing).  

- **Saját függőségek**használata: a függvények támogatják a NuGet és a NPM, és hozzáférést biztosítanak kedvenc könyvtáraihoz.

- **Integrált biztonság**: a http által aktivált függvények védelme olyan OAuth-szolgáltatókkal, mint például a Azure Active Directory, a Facebook, a Google, a Twitter és a Microsoft-fiók.

- **Egyszerűsített integráció**: egyszerűen integrálható az Azure-szolgáltatásokkal és a szolgáltatott szoftveres (SaaS) ajánlatokkal.

- **Rugalmas fejlesztés**: a folyamatos integráció beállítása és a kód üzembe helyezése a [GitHub](../app-service/scripts/cli-continuous-deployment-github.md), az [Azure DevOps Services](../app-service/scripts/cli-continuous-deployment-vsts.md)és más [támogatott fejlesztői eszközök](../app-service/deploy-local-git.md)segítségével.

- **Állapot-nyilvántartó kiszolgáló nélküli architektúra**: kiszolgáló nélküli alkalmazások összehangolása [Durable Functionsokkal](durable/durable-functions-overview.md).

- **Nyílt forráskódú**: a functions futtatókörnyezet nyílt forráskódú, és [elérhető a githubon](https://github.com/azure/azure-webjobs-sdk-script).

## <a name="what-can-i-do-with-functions"></a>Mire használhatom a Functions szolgáltatást?

A functions nagyszerű megoldás a tömeges adatok feldolgozására, a rendszerek integrálására, az internetes IoT, valamint az egyszerű API-k és a mikro-szolgáltatások létrehozására.

Többek között a következő főbb forgatókönyvekkel kezdheti el a lépéseket:

- **Http**: kód futtatása http- [kérelmek](functions-create-first-azure-function.md) alapján

- **Időzítő**: kód ütemezett [futtatása az előre meghatározott időpontokban](./functions-create-scheduled-function.md)

- **Azure Cosmos db**: [új és módosított Azure Cosmos db dokumentumok](./functions-create-cosmos-db-triggered-function.md) feldolgozása

- **Blob Storage**: [új és módosított Azure Storage-Blobok](./functions-create-storage-blob-triggered-function.md) feldolgozása

- **Üzenetsor-tárolás**: válaszadás az [Azure Storage üzenetsor üzeneteire](./functions-create-storage-queue-triggered-function.md)

- **Event Grid**: válaszadás [Azure Event Grid eseményekre előfizetések és szűrők használatával](../event-grid/resize-images-on-storage-blob-upload-event.md)

- **Event hub**: válaszadás [nagy mennyiségű Azure Event hub-eseményre](./functions-bindings-event-hubs.md)

- **Service Bus üzenetsor**: Kapcsolódás más Azure-vagy helyszíni szolgáltatásokhoz a [Service Bus üzenetsor üzeneteinek megválaszolásával](./functions-bindings-service-bus.md)

- **Service Bus témakör**: más Azure-szolgáltatások vagy helyszíni szolgáltatások összekapcsolása az [Service Bus témakör üzeneteinek megválaszolásával](./functions-bindings-service-bus.md)

## <a name="how-much-does-functions-cost"></a><a name="pricing"></a>Mennyibe kerül a Functions szolgáltatás használata?

Azure Functions háromféle díjszabási csomaggal rendelkezik. Válassza azt, amelyik leginkább megfelel az igényeinek:

- **Felhasználási terv**: az Azure biztosítja az összes szükséges számítási erőforrást. Nem kell aggódnia az erőforrás-kezeléssel kapcsolatban, és csak a kód futási idejéért kell fizetnie.

- **Prémium csomag**: megadhatja az előre bemelegítő példányok számát, amelyek mindig online állapotban vannak, és azonnal válaszolnak. A függvény futtatásakor az Azure minden további szükséges számítási erőforrást biztosít. A folyamatosan üzemelő, előre bemelegítő példányok és az Azure-ban használt további példányok kell fizetnie.

- **App Service terv**: a függvények futtatása ugyanúgy, mint a webalkalmazások. Ha a többi alkalmazáshoz App Service használ, a függvények külön díj nélkül is futtathatók ugyanazon a csomagon.

További információt a szolgáltatási csomagokról [az Azure Functions szolgáltatási csomagok áttekintésében](functions-scale.md) talál. Az árképzés további részleteiért lásd [a Functions díjszabási oldalát](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Következő lépések

- [Az első Azure-függvény létrehozása](functions-create-first-function-vs-code.md)  
  Ismerkedjen meg a [Visual Studio Code](functions-create-first-function-vs-code.md), a [parancssor](functions-create-first-azure-function-azure-cli.md)használatával, vagy használja a [Azure Portal](functions-create-first-azure-function.md).

- [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)  
  Részletesebb műszaki információkat tartalmaz az Azure Functions futtatókörnyezettel kapcsolatban, valamint segédanyagokat függvények kódolásához, illetve eseményindítók és kötések meghatározásához.

- [Az Azure Functions méretezése](functions-scale.md)  
  Az Azure Functions szolgáltatáshoz elérhető szolgáltatáscsomagokat ismerteti, köztük a Használatalapú futtatási csomagot, és segít a megfelelő csomag kiválasztásában.

- [További információ az Azure App Service szolgáltatásról](../app-service/overview.md)  
  Az Azure Functions az Azure App Service használatával biztosítja az olyan alapvető funkciókat, mint az üzembe helyezések, a környezeti változók és a diagnosztika.
