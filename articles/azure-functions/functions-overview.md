---
title: Az Azure Functions áttekintése
description: Megtudhatja, hogyan optimalizálhat aszinkron számítási feladatokat percek alatt az Azure Functions használatával.
author: mattchenderson
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 01/16/2020
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f0948cb58ba9403a34fbfd61ec43c29bed3440bc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77621006"
---
# <a name="an-introduction-to-azure-functions"></a>Az Azure Functions bemutatása

Az Azure Functions lehetővé teszi, hogy kis kóddarabokat (úgynevezett "függvényeket") futtasson anélkül, hogy aggódnia kellene az alkalmazás-infrastruktúra miatt. Az Azure Functions segítségével a felhőalapú infrastruktúra biztosítja az összes olyan naprakész kiszolgálót, amely az alkalmazás nagy méretekben való futtatásához szükséges.

A függvényt egy adott eseménytípus "váltja ki". [A támogatott eseményindítók](./functions-triggers-bindings.md) közé tartozik az adatok változásaira való válaszadás, az üzenetekre adott válaszadás, ütemezés szerint vagy HTTP-kérelem eredményeként történő futtatás.

Bár a szolgáltatások számtalanával szemben mindig kódolhat, a más szolgáltatásokkal való integráció kötések használatával egyszerűsíthető. A kötések [deklaratív hozzáférést biztosítanak az Azure és a külső szolgáltatások széles köréhez.](./functions-triggers-bindings.md)

## <a name="features"></a>Szolgáltatások

Az Azure Functions néhány fontos funkciója a következő:

- **Kiszolgáló nélküli alkalmazások:** A függvények lehetővé teszik [kiszolgáló nélküli](https://azure.microsoft.com/solutions/serverless/) alkalmazások fejlesztését a Microsoft Azure-ban.

- **Nyelvválasztás:** A Függvények írása a [C#, Java, JavaScript, Python és PowerShell](supported-languages.md)használatával.

- **Használatalapú díjszabási modell:** Csak a kód futtatásával töltött időért fizessen. Tekintse meg a Használatalapú futtatási csomag lehetőséget a [díjszabás szakaszban](#pricing).  

- **Hozza létre saját függőségeit**: A functions támogatja a NuGet és az NPM funkciót, így hozzáférhet kedvenc könyvtáraihoz.

- **Integrált biztonság:** A HTTP-triggeráltal aktivált függvények védelme az OAuth-szolgáltatókkal, például az Azure Active Directoryval, a Facebookkal, a Google-lal, a Twitterrel és a Microsoft-fiókkal.

- **Egyszerűsített integráció:** Egyszerűen integrálható az Azure-szolgáltatásokkal és a szoftverszolgáltatásként (SaaS) nyújtott ajánlatokkal.

- **Rugalmas fejlesztés:** Folyamatos integráció beállítása és a kód üzembe helyezése a [GitHubon,](../app-service/scripts/cli-continuous-deployment-github.md) [az Azure DevOps-szolgáltatásokon](../app-service/scripts/cli-continuous-deployment-vsts.md)és más [támogatott fejlesztői eszközökön](../app-service/deploy-local-git.md)keresztül.

- **Állapotalapú kiszolgáló nélküli architektúra**: A kiszolgáló nélküli alkalmazások at tartós függvényekkel kell elbékíteni. [Durable Functions](durable/durable-functions-overview.md)

- **Nyílt forráskódú**: A Functions runtime nyílt forráskódú és [elérhető a GitHubon.](https://github.com/azure/azure-webjobs-sdk-script)

## <a name="what-can-i-do-with-functions"></a>Mire használhatom a Functions szolgáltatást?

A Functions nagyszerű megoldás a tömeges adatok feldolgozására, a rendszerek integrálására, a dolgok internetével (IoT) való együttműködésre, valamint egyszerű API-k és mikroszolgáltatások készítésére.

Sablonok sorozata érhető el a legfontosabb forgatókönyvek elindításához, beleértve a következőket:

- **HTTP**: Kód [futtatása HTTP-kérelmek](functions-create-first-azure-function.md) alapján

- **Időzítő:** Előre [meghatározott időpontokban futtatandó](./functions-create-scheduled-function.md) ütemezési kód

- **Azure Cosmos DB:** [Új és módosított Azure Cosmos DB-dokumentumok feldolgozása](./functions-create-cosmos-db-triggered-function.md)

- **Blob storage**: [Új és módosított Azure Storage-blobok feldolgozása](./functions-create-storage-blob-triggered-function.md)

- **Várólista-tárolás:** Válasz az [Azure Storage várólista-üzeneteire](./functions-create-storage-queue-triggered-function.md)

- **Event Grid**: Előfizetések és szűrők segítségével válaszolhat az [Azure Event Grid eseményeire](../event-grid/resize-images-on-storage-blob-upload-event.md)

- **Event Hub**: Nagy [mennyiségű Azure Event Hub-eseményre](./functions-bindings-event-hubs.md) adott válasz

- **Service Bus-várólista:** Csatlakozás más Azure- vagy helyszíni szolgáltatásokhoz [a Service Bus várólista-üzeneteinek megválaszolásával](./functions-bindings-service-bus.md)

- **Szolgáltatásbusz-témakör:** Más Azure-szolgáltatások vagy helyszíni szolgáltatások csatlakoztatása [a Service Bus témakörüzeneteinek megválaszolásával](./functions-bindings-service-bus.md)

## <a name="how-much-does-functions-cost"></a><a name="pricing"></a>Mennyibe kerül a Functions szolgáltatás használata?

Az Azure Functions háromféle díjszabási csomaggal rendelkezik. Válassza azt, amelyik leginkább megfelel az igényeinek:

- **Felhasználási terv:** Az Azure biztosítja az összes szükséges számítási erőforrásokat. Nem kell aggódnia az erőforrás-kezelés miatt, és csak a kód futási idejéért kell fizetnie.

- **Prémium csomag:** Számos előre felmelegített példányt ad meg, amelyek mindig online állapotban vannak, és készen állnak az azonnali válaszadásra. A függvény futtatásakor az Azure minden további számítási erőforrást biztosít, amely szükséges. A folyamatosan futó, előmelegített példányokért kell fizetnie, és az Azure-ban használt további példányok az alkalmazás be- és kiméretezése érdekében.

- **App Service-csomag:** Futtassa a funkciókat, mint a webes alkalmazások. Ha az App Service-t használja a többi alkalmazáshoz, a függvények további költségek nélkül futtathatók ugyanazon a csomagon.

További információt a szolgáltatási csomagokról [az Azure Functions szolgáltatási csomagok áttekintésében](functions-scale.md) talál. Az árképzés további részleteiért lásd [a Functions díjszabási oldalát](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Következő lépések

- [Az első Azure-függvény létrehozása](functions-create-first-function-vs-code.md)  
  Ismerkedés a [Visual Studio Code](functions-create-first-function-vs-code.md)szolgáltatással , a [parancssorával,](functions-create-first-azure-function-azure-cli.md)vagy használja az [Azure portalt.](functions-create-first-azure-function.md)

- [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)  
  Részletesebb műszaki információkat tartalmaz az Azure Functions futtatókörnyezettel kapcsolatban, valamint segédanyagokat függvények kódolásához, illetve eseményindítók és kötések meghatározásához.

- [Az Azure Functions méretezése](functions-scale.md)  
  Az Azure Functions szolgáltatáshoz elérhető szolgáltatáscsomagokat ismerteti, köztük a Használatalapú futtatási csomagot, és segít a megfelelő csomag kiválasztásában.

- [További információ az Azure App Service szolgáltatásról](../app-service/overview.md)  
  Az Azure Functions az Azure App Service használatával biztosítja az olyan alapvető funkciókat, mint az üzembe helyezések, a környezeti változók és a diagnosztika.
