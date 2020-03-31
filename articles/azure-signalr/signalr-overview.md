---
title: Mi az Azure SignalR szolgáltatás?
description: Jobban megismerheti, hogy milyen tipikus használati forgatókönyvek használata az Azure SignalR használatához, és ismerje meg az Azure SignalR legfontosabb előnyeit.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 31cb4570dd0952f60fbff2fa1131b3af36c278cb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74157581"
---
# <a name="what-is-azure-signalr-service"></a>Mi az Azure SignalR szolgáltatás?

Az Azure SignalR szolgáltatás leegyszerűsíti a valós idejű webes funkciók hozzáadását az alkalmazásokhoz HTTP-n keresztül. A valós idejű funkció lehetővé teszi, hogy a szolgáltatás tartalomfrissítéseket küldjön le a csatlakoztatott ügyfeleknek, például az egyoldalas web- vagy mobilalkalmazásoknak. Így az ügyfelek a kiszolgáló lekérdezése nélkül frissülnek vagy küldenek frissítésekre vonatkozó új HTTP-kéréseket.


A cikk áttekintést nyújt az Azure SignalR szolgáltatásról.

## <a name="what-is-azure-signalr-service-used-for"></a>Mire használják az Azure SignalR szolgáltatást?

Minden olyan forgatókönyv, amely az adatok kiszolgálóról ügyfélre valós idejű lenyomását igényli, használhatja az Azure SignalR Szolgáltatást.

A hagyományos valós idejű funkciók, amelyek gyakran igényelnek lekérdezést a kiszolgálóról, az Azure SignalR-szolgáltatást is használhatják.

Az Azure SignalR szolgáltatást számos iparágban használták, minden olyan alkalmazástípushoz, amely valós idejű tartalomfrissítéseket igényel. Felsorolunk néhány példát, amelyek jó az Azure SignalR Szolgáltatás használatához:

* **Nagyfrekvenciás adatfrissítések:** játék, szavazás, szavazás, aukció.
* **Irányítópultok és figyelés:** vállalati irányítópult, pénzügyi piaci adatok, azonnali értékesítési frissítés, többjátékos játékranglistáés IoT-figyelés.
* **Csevegés:** élő csevegőszoba, csevegőrobot, on-line ügyfélszolgálat, valós idejű vásárlási asszisztens, messenger, játékon belüli csevegés és így tovább.
* **Valós idejű hely a térképen:** logisztikai követés, kézbesítési állapotkövetés, szállítási állapotfrissítések, GPS-alkalmazások.
* **Valós idejű célzott hirdetések:** személyre szabott, valós idejű leküldéses hirdetések és ajánlatok, interaktív hirdetések.
* **Együttműködésen alapuló alkalmazások:** társszerzőség, rajztábla-alkalmazások és csapatértekezleti szoftverek.
* **Push értesítések:** közösségi hálózat, e-mail, játék, utazási figyelmeztetés.
* **Valós idejű műsorszolgáltatás:** élő hang-/videoközvetítés, élő feliratozás, fordítás, események/hírközvetítés.
* **IoT és csatlakoztatott eszközök:** valós idejű IoT-metrikák, távvezérlés, valós idejű állapot és helymeghatározás.
* **Automatizálás:** valós idejű eseményindító a felsőbb szintű eseményekből.

## <a name="what-are-the-benefits-using-azure-signalr-service"></a>Milyen előnyökkel jár az Azure SignalR szolgáltatás használata?

**Standard alapú:**

A SignalR számos, valós idejű webalkalmazások létrehozására használt technológia absztrakciója. A [WebSocket](https://wikipedia.org/wiki/WebSocket) az ideális átviteli technológia, de más technológiákat (például [kiszolgáló által küldött eseményeket (SSE)](https://wikipedia.org/wiki/Server-sent_events) és hosszú lekérdezést) is használ a rendszer, ha egyéb lehetőségek nem érhetők el. A SignalR automatikusan észleli és inicializálja a megfelelő átvitelt, a kiszolgáló és az ügyfél által támogatott szolgáltatások alapján.

**Natív ASP.NET Core támogatás:**

A SignalR Service natív programozási élményt nyújt ASP.NET Core és ASP.NET. Az új SignalR alkalmazás fejlesztése a SignalR szolgáltatással, vagy a meglévő SignalR alapú alkalmazásból a SignalR szolgáltatásba való áttelepítés minimális erőfeszítéseket igényel.
A SignalR Service támogatja ASP.NET Core új funkcióját, a Szerver oldali Blazort is.

**Széles körű ügyféltámogatás:**

A SignalR Szolgáltatás az ügyfelek széles körével működik együtt, például webes és mobil böngészőkkel, asztali alkalmazásokkal, mobilalkalmazásokkal, kiszolgálói folyamatokkal, IoT-eszközökkel és játékkonzolokkal. A SignalR Szolgáltatás különböző nyelveken kínál SDK-kat. A natív ASP.NET Core vagy ASP.NET C# SDK-k mellett a SignalR Service javascript ügyfél SDK-t is biztosít a webes ügyfelek és számos JavaScript-keretrendszer engedélyezéséhez. Java kliens SDK is támogatott Java alkalmazások, beleértve az Android natív alkalmazások. A SignalR-szolgáltatás támogatja a REST API-t, és az Azure Functions és az Event Grid integrációrévén kiszolgáló nélküli.

**Nagyméretű ügyfélkapcsolatok kezelése:**

A SignalR szolgáltatás nagyméretű, valós idejű alkalmazásokhoz készült. A SignalR szolgáltatás lehetővé teszi, hogy több példány együtt dolgozzon az ügyfélkapcsolatok millióira való méretezéshez. A szolgáltatás is támogatja a több globális régióban a szilánkok, magas rendelkezésre állású, vagy vész-helyreállítási célokra.

**Távolítsa el az önkiszolgáló SignalR terheit:**

A saját üzemeltetésű SignalR-alkalmazásokhoz képest a SignalR szolgáltatásra való váltás szükségtelenné teszi a mérlegeket és az ügyfélkapcsolatokat kezelő háttérsíkok kezelését. A teljes körűen felügyelt szolgáltatás leegyszerűsíti a webes alkalmazásokat, és megtakarítja a tárhelyköltséget. A SignalR szolgáltatás globális elérést és világszínvonalú adatközpontot és hálózatot kínál, több millió kapcsolatra skáláz, garantálja az SLA-t, miközben az Azure-szabvány nak megfelelő en-biztonsággal és biztonsággal rendelkezik.

![Felügyelt signalr szolgáltatás](./media/signalr-overview/managed-signalr-service.png)

**Gazdag API-kat kínálhat a különböző üzenetkezelési mintákhoz:**

A SignalR szolgáltatás lehetővé teszi, hogy a kiszolgáló üzeneteket küldjön egy adott kapcsolatra, az összes kapcsolatra vagy egy adott felhasználóhoz tartozó vagy tetszőleges csoportba helyezett kapcsolatok egy részhalmazára.

## <a name="how-to-use-azure-signalr-service"></a>Az Azure SignalR szolgáltatás használata

Az Azure SignalR Szolgáltatással számos különböző módon programozhat, mivel az itt felsorolt minták közül néhány:

- **[ASP.NET Core SignalR-alkalmazás skálázása](signalr-concept-scale-aspnet-core.md)** – Az Azure SignalR szolgáltatás integrálása egy ASP.NET Core SignalR-alkalmazással több százezer kapcsolatra való horizontális felskálázáshoz.
- **[Kiszolgáló nélküli, valós idejű alkalmazások létrehozása](signalr-concept-azure-functions.md)** – Az Azure SignalR szolgáltatással integrált Azure Functions használata kiszolgáló nélküli, valós idejű alkalmazások létrehozására, többek között JavaScript, C# és Java nyelven.
- **[Üzenetek küldése a kiszolgálóról az ügyfeleknek REST API-n keresztül](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** – Az Azure SignalR szolgáltatás biztosít egy REST API-t, amely lehetővé teszi, hogy az alkalmazások bármilyen REST-kompatibilis programnyelven üzeneteket küldjenek a SignalR szolgáltatáshoz csatlakoztatott ügyfeleknek.
