---
title: Hibaelhárítási útmutató az Azure Service Bus szolgáltatáshoz | Microsoft dokumentumok
description: Ez a cikk az Azure Service Bus üzenetkezelési kivételeinek listáját és a kivétel bekövetkezésekor végrehajtandó javasolt műveleteket tartalmazza.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2020
ms.author: aschhab
ms.openlocfilehash: 63bf035d4e19cc1d64998a6ad533812e71ee71b8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887774"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Hibaelhárítási útmutató az Azure Service Bus szolgáltatáshoz
Ez a cikk hibaelhárítási tippeket és javaslatokat tartalmaz néhány olyan problémával kapcsolatban, amelyek az Azure Service Bus használatakor jelenhetnek meg. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Kapcsolódási, tanúsítvány- vagy idő-meghosszabbítási problémák
A következő lépések segíthetnek a kapcsolódási/tanúsítvány-/idő-kikapcsolási problémák elhárításában a *.servicebus.windows.net alatt található összes szolgáltatás esetében. 

- Tallózás vagy [wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/`. Segít annak ellenőrzésében, hogy van-e IP-szűrés vagy virtuális hálózati vagy tanúsítványlánc problémák (a leggyakoribb java SDK használataesetén).

    Példa a sikeres üzenetre:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Példa hibaüzenet a hibaüzenetre:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Futtassa a következő parancsot annak ellenőrzéséhez, hogy a tűzfal on-e blokkolva van-e a port. A használt portok: 443 (HTTPS), 5671 (AMQP) és 9354 (Net Messaging/SBMP). A használt könyvtártól függően más portok is használatosak. Itt van a minta parancs, amely ellenőrzi, hogy az 5671 port blokkolva van-e. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Linuxalatt:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Ha időszakos kapcsolódási problémák merülnek fel, futtassa a következő parancsot, és ellenőrizze, hogy vannak-e eldobott csomagok. Ez a parancs 1 másodpercenként 25 különböző TCP-kapcsolatot próbál létesíteni a szolgáltatással. Ezután ellenőrizheti, hogy közülük hánysikerült/sikertelen, és megtekintheti a TCP-kapcsolat késését is. Letöltheti az `psping` eszközt [innen](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    Egyenértékű parancsokat használhat, ha más eszközöket `tnc`használ, például a , `ping`és így tovább. 
- Szerezzen be egy hálózati nyomkövetést, ha az előző lépések nem segítenek, és elemezze azt olyan eszközökkel, mint a [Wireshark](https://www.wireshark.org/). Szükség esetén forduljon [a Microsoft támogatási szolgálatához.](https://support.microsoft.com/) 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>A szolgáltatás frissítésekkel/újraindításokkal kapcsolatos problémák
A háttérszolgáltatás-frissítések és -újraindítások a következő hatást gyakorolhatják az alkalmazásokra:

- A kérelmek egy pillanatra meglesznek fojtva.
- Előfordulhat, hogy csökken a bejövő üzenetek/kérések.
- A naplófájl hibaüzeneteket tartalmazhat.
- Az alkalmazások néhány másodpercre leválaszthatók a szolgáltatásról.

Ha az alkalmazáskód SDK-t használ, az újrapróbálkozási szabályzat már be van építve és aktív. Az alkalmazás újra csatlakozik anélkül, hogy jelentős hatással lenne az alkalmazásra/munkafolyamatra.

## <a name="unauthorized-access-send-claims-are-required"></a>Jogosulatlan hozzáférés: Jogcímek küldése szükséges
Ez a hibaüzenet akkor jelenhet meg, amikor a Visual Studio szolgáltatásból próbál hozzáférni egy service bus-témakörhöz egy helyszíni számítógépen, egy felhasználó által hozzárendelt felügyelt identitással, küldési engedélyekkel.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

A hiba megoldásához telepítse a [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) könyvtárat.  További információ: [Local development authentication](..\key-vault\service-to-service-authentication.md#local-development-authentication). 

Ha tudni szeretné, hogyan rendelhet engedélyeket a szerepkörökhöz, olvassa el a Felügyelt identitás hitelesítése az [Azure Active Directoryval az Azure Service Bus-erőforrások eléréséhez című témakört.](service-bus-managed-service-identity.md)

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket: 

- [Az Azure Resource Manager kivételei](service-bus-resource-manager-exceptions.md). Felsorolja az Azure Service Bus azure-erőforrás-kezelővel (sablonok vagy közvetlen hívások) használatával történő interakció során létrehozott kivételeket.
- [Üzenetküldési kivételek](service-bus-messaging-exceptions.md). A . 

