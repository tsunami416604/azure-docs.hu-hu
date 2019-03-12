---
title: Az Azure SignalR szolgáltatás belső elemei
description: Az Azure SignalR Service internals áttekintése.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: cbcdfccfdca1dbed3b766b3f50295b1d355b3478
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555083"
---
# <a name="azure-signalr-service-internals"></a>Az Azure SignalR szolgáltatás belső elemei

Az Azure SignalR Service ASP.NET Core SignalR keretrendszer épül. Ezen kívül támogatja az ASP.NET SignalR előzetes verzióként.

> Az ASP.NET SignalR támogatása érdekében az Azure SignalR Service reimplements adatprotokoll ASP.NET SignalR az ASP.NET Core lehet létrehozni

Egyszerűen áttelepítheti egy helyi ASP.NET Core SignalR szolgáltatásalkalmazás alkalmassá tétele a SignalR Service, néhány sornyi kód megváltoztatására.

Az alábbi ábrán a SignalR Service használata az alkalmazáskiszolgáló a tipikus architektúrát ismerteti.

Saját üzemeltetésű a SignalR az ASP.NET Core-alkalmazás közötti különbségeket, valamint ismertetik.

![Architektúra](./media/signalr-concept-internals/arch.png)

## <a name="server-connections"></a>Kiszolgálókapcsolatok

Helyi ASP.NET Core SignalR-alkalmazáskiszolgáló figyel, és az ügyfelek közvetlenül kapcsolódik.

A SignalR Service a kiszolgáló nem fogad állandó ügyfélkapcsolatokat, ehelyett:

1. A `negotiate` végpont Azure SignalR Service SDK-t tesz elérhetővé minden egyes hub.
1. Ez a végpont ügyfél egyeztetési kérésekre válaszol, és a SignalR Service irányítani az ügyfeleket.
1. Végül az ügyfelek csatlakoznak SignalR Service.

További információkért lásd: [ügyfélkapcsolatok](#client-connections).

Miután elindult a kiszolgáló, 
- ASP.NET Core SignalR, az Azure SignalR Service SDK SignalR Service hub 5 WebSocket-nként nyílik meg. 
- Az ASP.NET SignalR Azure SignalR Service SDK-t a SignalR Service hub 5 WebSocket-nként, és a egy alkalmazás WebSocket kapcsolaton egy nyílik meg.

5 WebSocket kapcsolatot alapértelmezett értéke, amely módosítható [konfigurációs](https://github.com/Azure/azure-signalr/blob/dev/docs/use-signalr-service.md#connectioncount).

Az ügyfelek üzeneteket fogja kell multiplexed be ezeket a kapcsolatokat.

Ezek a kapcsolatok marad kapcsolatban a SignalR Service folyamatosan. Ha egy kiszolgáló kapcsolat le van választva, a hálózati probléma,
- a kiszolgálókapcsolat kapcsolat által üzemeltetett összes ügyfélszámítógép (kapcsolatos további információkért lásd: [adatokat továbbít az ügyfél és kiszolgáló közötti](#data-transmit-between-client-and-server));
- a kiszolgálókapcsolat elindul az automatikus újracsatlakozás.

## <a name="client-connections"></a>Ügyfélkapcsolatok

A SignalR Service használata esetén az ügyfelek csatlakozni SignalR Service alkalmazáskiszolgáló helyett.
Az ügyfél és a SignalR Service között állandó kapcsolatot két lépésből áll.

1. Ügyfél-egyeztetés kérést küld a kiszolgáló. Az Azure SignalR Service SDK-kiszolgáló egy átirányítási válaszban SignalR Service URL-cím és a hozzáférési jogkivonat adja vissza.

- Az ASP.NET Core SignalR egy tipikus átirányítási válasz a következőhöz hasonló:
    ```
    {
        "url":"https://test.service.signalr.net/client/?hub=chat&...",
        "accessToken":"<a typical JWT token>"
    }
    ```
- Az ASP.NET SignalR egy tipikus átirányítási válasz a következőhöz hasonló:
    ```
    {
        "ProtocolVersion":"2.0",
        "RedirectUrl":"https://test.service.signalr.net/aspnetclient",
        "AccessToken":"<a typical JWT token>"
    }
    ```

1. Ügyfél a SignalR-szolgáltatáshoz való csatlakozáshoz a normál folyamat elindításához az átirányítási válaszban megjelenését követően használja az új URL-címet és a hozzáférési jogkivonatot.

További információ az ASP.NET Core SignalR [átviteli protokollok](https://github.com/aspnet/SignalR/blob/release/2.2/specs/TransportProtocols.md).

## <a name="data-transmit-between-client-and-server"></a>Ügyfél és kiszolgáló közötti átviteli adatok

Amikor egy ügyfél csatlakozik a SignalR Service, futtatókörnyezet megtalálja az ügyfél kiszolgálására kiszolgálói kapcsolat
- Ez a lépés csak egyszer történik, és a egy az egyhez típusú hozzárendelés az ügyfél és kiszolgáló kapcsolatok között.
- A leképezés megőrződik a SignalR Service, amíg az ügyfél vagy kiszolgáló bontja a kapcsolatot.

Ezen a ponton az alkalmazáskiszolgáló adatokkal egy eseményt kap az új ügyfél. A kiszolgáló az ügyfélnek logikai kapcsolat jön létre. Az adatcsatorna alkalmazáskiszolgáló keresztül a SignalR Service ügyfélről jön létre.

SignalR service küld a párosítási-kiszolgáló az ügyféltől érkező adatokat. És a csatlakoztatott ügyfelek küld a kiszolgáló adatait.

Ahogy láthatjuk, az Azure SignalR Service egy, amely lényegében logikai átviteli alkalmazáskiszolgáló és az ügyfelek között. Összes állandó kapcsolat kiszervezett SignalR Service.
Alkalmazáskiszolgáló csak kell kezelni az üzleti logika hub osztály, nem kell bajlódnunk ügyfélkapcsolatok.