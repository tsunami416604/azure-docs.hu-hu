---
title: "A figyelő API-k közzé az Azure API Management |} Microsoft Docs"
description: "Ez az oktatóanyag az Azure API Management az API figyeléséről további lépések."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: bdca9d4968e9e68314f350787907f15e417821f7
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
---
# <a name="monitor-published-apis"></a>Közzétett API-k figyelése

Az Azure figyelő az Azure-szolgáltatások, amely az összes Azure-erőforrások figyelése egyetlen helyről biztosít. Azure megfigyelővel ábrázolhatja, lekérdezése, továbbítani, archiválására, és a metrikák és a naplók az Azure erőforrások, például az API Management érkező műveletek. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tevékenységnaplók megtekintése
> * Diagnosztikai naplók megtekintése
> * Az API-nézet metrikája 
> * Riasztási szabályt beállítani, ha az API lekérdezi a jogosulatlan hívások

A következő videó bemutatja, hogyan figyelheti a figyelővel az Azure API Management. 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]
>
>

## <a name="prerequisites"></a>Előfeltételek

+ Fejezze be a következő gyorsindítási: [hozzon létre egy Azure API Management példányt](get-started-create-service-instance.md).
+ Is, végezze el a következő oktatóanyagot: [importálása és az első API-t közzétenni](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="diagnostic-logs"></a>Tevékenység naplók megtekintése

Tevékenységi naplóit adja meg az API Management-szolgáltatások a végrehajtott műveletek betekintést. Tevékenység-naplók segítségével meghatározhatja a "mi, ki, és mikor" az összes írni az API Management szolgáltatásokban végzett műveleteket (PUT, POST, Törlés). 

> [!NOTE]
> Tevékenység naplói nem tartalmazzák (GET) olvasási műveletek vagy a műveletek végre Publisher a klasszikus portálon vagy az eredeti felügyeleti API-k használatával.

Tevékenység naplók elérhetők az API Management szolgáltatásban, és elérni az összes Azure-erőforrások Azure figyelőben naplókat. 

Tevékenység naplók megtekintése:

1. Az a **API Management** példányt, kattintson a **tevékenységnapló**.

## <a name="view-diagnostic-logs"></a>Diagnosztikai naplók megtekintése

Diagnosztikai naplók gazdag információkkal kapcsolatos műveletek és a naplózás és a hibaelhárítási célból fontos hibák. Diagnosztikai naplók eltérnek a tevékenységi naplóit. Tevékenység naplók az Azure-erőforrások a végrehajtott műveletek betekintést. Diagnosztikai naplók Észreveheti az olyan műveletek, hogy az erőforrás végre magát.

Diagnosztikai naplók elérése:

1. Az a **API Management** példányt, kattintson a **diagnosztikai naplófájl**.

## <a name="view-metrics-of-your-apis"></a>Az API-k nézet metrikák

Az API Management bocsát ki metrikák percenként, felkínálva a valós idejű információkat az állapot és az API-kat állapotának közelében. Az alábbiakban néhány elérhető metrikai összefoglalása látható:

* Kapacitás (előzetes verzió): döntések frissítése/alacsonyabb verziójúra változtatása a APIM szolgáltatások segítségével. A metrika egy perc alatt kibocsátott, és az átjáró kapacitás tükrözi a jelentés időpontjában. A metrika terjedő 0 – 100, és az átjáró recourses például a Processzor- és memóriafelhasználását alapján számítja ki.
* Átjáró kérelmek teljes száma: az API-lekérdezések száma az időtartamon belül. 
* Átjáró sikeres kérelmek:, beleértve a 304, 307 és annak minden kisebb, mint 301 (például 200) sikeres HTTP válaszkódot kapott API-kérelmek száma. 
* Sikertelen átjáró kérelmek:, beleértve a 400-as és annak minden nagyobb, mint 500 hibás HTTP válaszkódot kapott API-kérelmek száma.
* Jogosulatlan átjáró kérelmek: érkezett a HTTP válaszkódot, beleértve a 401-es, a 403-as és a 429 API-kérelmek száma. 
* Más átjáró kérelmek: érkezett a HTTP válaszkódot, amelyek nem tartoznak sem a megelőző kategóriák (például 418) API-kérelmek száma.

Metrikák eléréséhez:

1. Válassza ki **metrikák** a lap alján a menüből.
2. A legördülő listában válassza ki szeretné metrikák (több metrikák adhat hozzá). 
    
    Válassza például **átjáró kérelmek teljes száma** és **sikertelen átjáró kérelmek** az elérhető mérőszámok listájából.
3. A diagram bemutatja az API-hívások száma összesen. Azt is bemutatja, melyeknél nem sikerült API-hívások száma. 

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>Jogosulatlan kérelmet riasztási szabály beállítása

A riasztások metrikák és tevékenység naplók alapján konfigurálhatja. Azure a figyelő riasztást tegye a következőket, amikor elindítja a konfigurálását teszi lehetővé:

* E-mail értesítés küldése
* A webhook hívása
* Egy Azure logikai alkalmazás meghívása

Riasztások konfigurálása:

1. Válassza ki **riasztási szabályok** a menüsorában az oldal alján.
2. Válassza ki **metrika riasztás hozzáadása**.
3. Adjon meg egy **neve** erre a riasztásra vonatkozóan.
4. Válassza ki **jogosulatlan átjáró kérelmek** , a figyelendő metrikát.
5. Válassza ki **E-mail-tulajdonosok, közreműködőknek és olvasóknak**.
6. Kattintson az **OK** gombra.
7. Próbálja meg az API-kulcs nélkül konferencia-API hívása. Ez az API Management szolgáltatás tulajdonosa egy figyelmeztető e-mailt. 

    > [!TIP]
    > A riasztási szabály is meghívhatja a webes Hook vagy Azure Logic Apps kiváltásakor van.

    ![set Close-Up riasztás](./media/api-management-azure-monitor/set-up-alert.png)

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Tevékenységnaplók megtekintése
> * Diagnosztikai naplók megtekintése
> * Az API-nézet metrikája 
> * Riasztási szabályt beállítani, ha az API lekérdezi a jogosulatlan hívások

Előzetes következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Nyomkövetés-hívások](api-management-howto-api-inspector.md)