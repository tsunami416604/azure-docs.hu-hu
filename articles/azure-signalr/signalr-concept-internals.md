---
title: Az Azure SignalR szolgáltatás belső elemei
description: Ismerje meg az Azure SignalR Service belső adatait, az architektúrát, a kapcsolatokat és az adatok továbbításának módját.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 62afa5ee6993aa1bb3c7b5926e5320ab1fa510a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74157597"
---
# <a name="azure-signalr-service-internals"></a>Az Azure SignalR szolgáltatás belső elemei

Az Azure SignalR szolgáltatás ASP.NET Core SignalR keretrendszerre épül. Azt is támogatja ASP.NET SignalR, mint egy előnézeti funkció.

> A ASP.NET SignalR támogatása érdekében az Azure SignalR-szolgáltatás újramegvalósítja ASP.NET SignalR adatprotokollját a ASP.NET Core keretrendszeren felül

Könnyedén áttelepítheti a helyi ASP.NET Core SignalR alkalmazást a SignalR szolgáltatással való együttműködésre, néhány sornyi kódmódosítással.

Az alábbi ábra a SignalR szolgáltatás alkalmazáskiszolgálóval való használata során a tipikus architektúrát ismerteti.

A különbségek a saját üzemeltetésű ASP.NET Core SignalR alkalmazás tárgyalja is.

![Architektúra](./media/signalr-concept-internals/arch.png)

## <a name="server-connections"></a>Kiszolgálókapcsolatok

Saját üzemeltetésű ASP.NET Core SignalR alkalmazásszerver figyel, és közvetlenül összeköti az ügyfeleket.

A SignalR szolgáltatással az alkalmazáskiszolgáló már nem fogad állandó ügyfélkapcsolatokat:

1. A `negotiate` végpont ot az Azure SignalR Service SDK minden hubhoz elérhetővé teszi.
1. Ez a végpont válaszol az ügyfél egyeztetési kérelmeire, és átirányítja az ügyfeleket a SignalR szolgáltatásba.
1. Végül az ügyfelek a SignalR szolgáltatáshoz csatlakoznak.

További információt az [Ügyfélkapcsolatok](#client-connections)című témakörben talál.

Az alkalmazáskiszolgáló indítása után 
- A ASP.NET Core SignalR esetében az Azure SignalR Service SDK hubonként 5 WebSocket-kapcsolatot nyit meg a SignalR szolgáltatással. 
- A ASP.NET SignalR esetében az Azure SignalR Service SDK hubonként 5 WebSocket-kapcsolatot nyit meg a SignalR szolgáltatással, és egy alkalmazásonként WebSocket-kapcsolatot.

5 A WebSocket-kapcsolatok az alapértelmezett érték, amely a [konfigurációban](https://github.com/Azure/azure-signalr/blob/dev/docs/use-signalr-service.md#connectioncount)módosítható.

Az ügyfeleknek érkező és onnan érkező üzenetek et ezekkel a kapcsolatokkal multiplexeli a rendszer.

Ezek a kapcsolatok mindig a SignalR szolgáltatáshoz csatlakoznak. Ha hálózati probléma miatt megszakad a kiszolgálói kapcsolat,
- a kiszolgálói kapcsolat közötti kapcsolat bontása által kiszolgált összes ügyfél (további információ: Az ügyfél és a [kiszolgáló közötti adattovábbítás);](#data-transmit-between-client-and-server)
- a kiszolgálókapcsolat automatikusan újracsatlakozik.

## <a name="client-connections"></a>Ügyfélkapcsolatok

A SignalR szolgáltatás használatakor az ügyfelek az alkalmazáskiszolgáló helyett a SignalR szolgáltatáshoz csatlakoznak.
Az ügyfél és a SignalR szolgáltatás között két lépésből áll az állandó kapcsolatok létesítése.

1. Az ügyfél egyeztetési kérelmet küld az alkalmazáskiszolgálónak. Az Azure SignalR Service SDK használatával az alkalmazáskiszolgáló átirányítási választ ad vissza a SignalR-szolgáltatás URL-címével és hozzáférési jogkivonatával.

- A ASP.NET Core SignalR esetében egy tipikus átirányítási válasz a következőképpen néz ki:
    ```
    {
        "url":"https://test.service.signalr.net/client/?hub=chat&...",
        "accessToken":"<a typical JWT token>"
    }
    ```
- A ASP.NET SignalR esetében egy tipikus átirányítási válasz a következőképpen néz ki:
    ```
    {
        "ProtocolVersion":"2.0",
        "RedirectUrl":"https://test.service.signalr.net/aspnetclient",
        "AccessToken":"<a typical JWT token>"
    }
    ```

1. Az átirányítási válasz fogadása után az ügyfél az új URL-címet és hozzáférési jogkivonatot használja a normál folyamat elindításához a SignalR szolgáltatáshoz való csatlakozáshoz.

További információ ASP.NET Core SignalR [átviteli protokolljairól.](https://github.com/aspnet/SignalR/blob/release/2.2/specs/TransportProtocols.md)

## <a name="data-transmit-between-client-and-server"></a>Adatok továbbítása az ügyfél és a kiszolgáló között

Amikor egy ügyfél csatlakozik a SignalR szolgáltatáshoz, a szolgáltatás futásideje kiszolgálói kapcsolatot fog találni az ügyfél kiszolgálásához
- Ez a lépés csak egyszer történik meg, és az ügyfél- és kiszolgálókapcsolatok közötti egy-az-egyhez hozzárendelés.
- A leképezés a SignalR szolgáltatásban marad, amíg az ügyfél vagy a kiszolgáló nem bontja a kapcsolatot.

Ezen a ponton az alkalmazáskiszolgáló egy eseményt kap az új ügyféltől származó információkkal. Az alkalmazáskiszolgálón logikai kapcsolat jön létre az ügyféllel. Az adatcsatorna ügyfélről alkalmazáskiszolgálóra, a SignalR szolgáltatáson keresztül jön létre.

A SignalR szolgáltatás adatokat továbbít az ügyféltől a párosítási alkalmazáskiszolgálóra. És az alkalmazáskiszolgálóról származó adatokat a rendszer elküldi a leképezett ügyfeleknek.

Mint látható, az Azure SignalR szolgáltatás lényegében egy logikai átviteli réteg az alkalmazáskiszolgáló és az ügyfelek között. Minden állandó kapcsolat ki van töltve a SignalR szolgáltatásba.
Az alkalmazáskiszolgálónak csak az üzleti logikát kell kezelnie a központi osztályban, anélkül, hogy aggódnia kellene az ügyfélkapcsolatok miatt.