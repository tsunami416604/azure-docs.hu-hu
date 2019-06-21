---
title: Mi az Azure SignalR Service?
description: Az Azure SignalR Service áttekintése.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 06/20/2019
ms.author: zhshang
ms.openlocfilehash: dc7ba3585ec49921c0a0e66185fc5550d3d4a006
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303615"
---
# <a name="what-is-azure-signalr-service"></a>Mi az Azure SignalR Service?

Az Azure SignalR szolgáltatás leegyszerűsíti a valós idejű webes funkciók hozzáadását az alkalmazásokhoz HTTP-n keresztül. A valós idejű funkció lehetővé teszi, hogy a szolgáltatás tartalomfrissítéseket küldjön le a csatlakoztatott ügyfeleknek, például az egyoldalas web- vagy mobilalkalmazásoknak. Így az ügyfelek a kiszolgáló lekérdezése nélkül frissülnek vagy küldenek frissítésekre vonatkozó új HTTP-kéréseket.


A cikk áttekintést nyújt az Azure SignalR szolgáltatásról.

## <a name="what-is-azure-signalr-service-used-for"></a>Mire használják az Azure SignalR szolgáltatást?

Adatok leküldése valós időben, ügyfél-kiszolgálóról igénylő forgatókönyvek az Azure SignalR Service használhatja.

Hagyományos valós idejű funkciókkal, amelyek gyakran igényelnek lekérdezési kiszolgálóról is használhatja az Azure SignalR Service.

Az Azure SignalR Service számos iparágban hasznosak, tetszőleges alkalmazástípus, amely valós idejű tartalomfrissítéseket szükséges használták. Hogy listázza, amelyek az Azure SignalR Service jó néhány példa:

* **Nagy gyakoriságú adatfrissítések:** játékok, szavazás, lekérdezés, árverések.
* **Irányítópultok és a figyelés:** céges irányítópult, pénzügyi piaci adatokat, az azonnali értékesítési frissítés, több résztvevős játék érdeklődők és IoT-figyelés.
* **Csevegés:** élő csevegőszoba, csevegőrobot, online ügyfél-támogatási, valós idejű vásárlási Segéd, messenger, a játékbeli Csevegés és így tovább.
* **Valós idejű helyét a térképen:** logisztikai követési, a kézbesítési állapotának nyomon követése, a szállítás ügyfélállapot-frissítés, a GPS-alkalmazások.
* **Valós idejű célzott hirdetések:** személyre szabott leküldéses ads olvasási idő és ajánlatok, interaktív hirdetéseket.
* **Együttműködésen alapuló alkalmazások:** alkalmazásbeli társszerzőség, rajztáblaként alkalmazásokat és szoftver teljesíti a csapat.
* **Leküldéses értesítések:** közösségi hálózati, e-mailt, játékot, utazási riasztás.
* **Valós idejű szórásos:** élő közvetítés hang/kép, a feliratozás, lefordításáról, események/hírek szórásos élő.
* **IoT és csatlakoztatott eszközök:** valós idejű IoT-metrikák, a távvezérlés, a valós idejű állapotát és a hely nyomon követése.
* **Automatizálás:** felsőbb rétegbeli események valós idejű eseményindító.

## <a name="what-are-the-benefits-using-azure-signalr-service"></a>Milyen előnyökkel Azure SignalR Service segítségével?

**A szabvány alapú:**

A SignalR számos, valós idejű webalkalmazások létrehozására használt technológia absztrakciója. A [WebSocket](https://wikipedia.org/wiki/WebSocket) az ideális átviteli technológia, de más technológiákat (például [kiszolgáló által küldött eseményeket (SSE)](https://wikipedia.org/wiki/Server-sent_events) és hosszú lekérdezést) is használ a rendszer, ha egyéb lehetőségek nem érhetők el. A SignalR automatikusan észleli és inicializálja a megfelelő átvitelt, a kiszolgáló és az ügyfél által támogatott szolgáltatások alapján.

**Natív ASP.NET Core-támogatás:**

SignalR Service natív programozási környezetet biztosít az ASP.NET Core és az ASP.NET. A SignalR Service új SignalR-alkalmazás fejlesztése, vagy meglévő SignalR-ről a SignalR Service alkalmazás minimális erőfeszítések van szükség.
SignalR Service támogatja az ASP.NET Core új funkció, kiszolgálóoldali Blazor is.

**Széles körű ügyfelek támogatása:**

SignalR Service széles ügyfelek, például webes és mobilböngészők, asztali alkalmazások, a mobile apps, kiszolgálói folyamat, IoT-eszközök és játékkonzol együttműködik. SignalR Service SDK-kat kínál a különböző nyelveken. Natív ASP.NET Core- vagy ASP.NET mellett C# SDK-k a SignalR Service emellett biztosítja a JavaScript ügyféloldali SDK-val, engedélyezheti a webes ügyfelektől és számos JavaScript-keretrendszer. Java-alkalmazások, beleértve a natív Android-alkalmazások a Java ügyfél-SDK használata is támogatott. SignalR Service támogatja a REST API-t és a kiszolgáló nélküli Azure Functions és az Event Grid integráció révén.

**Nagy méretű ügyfélkapcsolatok kezelésére:**

SignalR Service nagyméretű valós idejű alkalmazások számára készült. SignalR Service lehetővé teszi, hogy működjön együtt a méretezési csoport több példány ügyfélkapcsolatok millió. A szolgáltatás több globális régiót is támogatja a horizontális skálázás, magas rendelkezésre állású vagy vészhelyreállításra.

**A helyi futtatására a SignalR terheket eltávolítása:**

Saját üzemeltetésű SignalR alkalmazások képest, SignalR Service átváltása eltávolítja a vissza, amely képes kezelni a skálázását követve rugalmasan méretezhető az ügyfélkapcsolatok síkok felügyelnie. A teljes körűen felügyelt szolgáltatás is egyszerűbbé teszi a webes alkalmazásokhoz, és menti az üzemeltetési költség. SignalR Service globális jelenléttel és világszínvonalú adatközpont és hálózat kínál, méretezhető több millió, kapcsolatok, garantál SLA-t, adja meg a megfelelőség és az Azure standard szintű biztonságot.

![Managed SignalR Service](./media/signalr-overview/managed-signalr-service.png)

**Az ajánlat részletes API-k különböző üzenetkezelési mintát:**

SignalR Service lehetővé teszi, hogy a kiszolgáló üzeneteket küldeni egy adott kapcsolat, az összes kapcsolat vagy egy adott felhasználóhoz tartozik, vagy egy tetszőleges csoportba lettek helyezve kapcsolatok egy részhalmazára.

## <a name="how-to-use-azure-signalr-service"></a>Az Azure SignalR szolgáltatás használata

Programmal az Azure SignalR Service szolgáltatással, mint az itt felsorolt egyes számos különböző módja van:

- **[ASP.NET Core SignalR-alkalmazás skálázása](signalr-concept-scale-aspnet-core.md)** – Az Azure SignalR szolgáltatás integrálása egy ASP.NET Core SignalR-alkalmazással több százezer kapcsolatra való horizontális felskálázáshoz.
- **[Kiszolgáló nélküli, valós idejű alkalmazások létrehozása](signalr-concept-azure-functions.md)** – Az Azure SignalR szolgáltatással integrált Azure Functions használata kiszolgáló nélküli, valós idejű alkalmazások létrehozására, többek között JavaScript, C# és Java nyelven.
- **[Üzenetek küldése a kiszolgálóról az ügyfeleknek REST API-n keresztül](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** – Az Azure SignalR szolgáltatás biztosít egy REST API-t, amely lehetővé teszi, hogy az alkalmazások bármilyen REST-kompatibilis programnyelven üzeneteket küldjenek a SignalR szolgáltatáshoz csatlakoztatott ügyfeleknek.