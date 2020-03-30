---
title: Helyszíni adatátjáró az Azure Analysis Services számára | Microsoft dokumentumok
description: Helyszíni átjáróra van szükség, ha az Azure-beli Analysis Services-kiszolgáló helyszíni adatforrásokhoz csatlakozik.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 648646b6f973762245c344cd2629a874a219b170
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310152"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Csatlakozás helyszíni adatforrásokhoz a helyszíni adatátjáróval

A helyszíni adatátjáró biztonságos adatátvitelt biztosít a helyszíni adatforrások és az Azure Analysis Services-kiszolgálók között a felhőben. Amellett, hogy több Azure Analysis Services-kiszolgálóval dolgozik ugyanabban a régióban, az átjáró legújabb verziója is együttműködik az Azure Logic Apps, a Power BI, a Power Apps és a Power Automate használatával. Bár a telepített átjáró az összes ilyen szolgáltatás esetében azonos, az Azure Analysis Services és a Logic Apps további lépéseket tartalmaz.

Az itt megadott információk az Azure Analysis Services helyszíni adatátjáróval való működésére vonatkoznak. Ha többet szeretne megtudni az átjáróról és arról, hogyan működik más szolgáltatásokkal, olvassa el [a Mi a helyszíni adatátjáró?](/data-integration/gateway/service-gateway-onprem)

Az Azure Analysis Services esetében az átjáró val való első beállítás négy részből álló folyamat:

- **Telepítés letöltése és futtatása** – Ez a lépés átjárószolgáltatást telepít a szervezet egyik számítógépére. Az Azure-ba is bejelentkezik a [bérlő Azure-beli](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) AD-ben lévő fiók használatával. Az Azure B2B (vendég) fiókok nem támogatottak.

- **Az átjáró regisztrálása** – Ebben a lépésben megadhat egy nevet és egy helyreállítási kulcsot az átjáróhoz, és kijelölhet egy régiót, amely regisztrálja az átjárót a Gateway Cloud Service szolgáltatással. Az átjáró-erőforrás bármely régióban regisztrálható, de ajánlott, hogy ugyanabban a régióban legyen, mint az Analysis Services-kiszolgálók. 

- **Hozzon létre egy átjáró-erőforrást az Azure-ban** – Ebben a lépésben hozzon létre egy átjáró-erőforrást az Azure-ban.

- **A kiszolgálók csatlakoztatása az átjáró-erőforráshoz** – Miután rendelkezik átjáróerőforrással, megkezdheti a kiszolgálók csatlakoztatását. Több kiszolgálót és más erőforrásokat is csatlakoztathat, feltéve, hogy ugyanabban a régióban vannak.



## <a name="how-it-works"></a><a name="how-it-works"> </a>Működés
A szervezet számítógépén telepített átjáró **Windows-szolgáltatásként, helyszíni adatátjáróként**fut. Ez a helyi szolgáltatás az Azure Service Buson keresztül van regisztrálva a Gateway felhőszolgáltatásban. Ezután hozzon létre egy helyszíni adatátjáró-erőforrást az Azure-előfizetéséhez. Az Azure Analysis Services-kiszolgálók ezután csatlakozik az Azure átjáró erőforrás. Amikor a kiszolgálón lévő modelleknek csatlakozniuk kell a helyszíni adatforrásokhoz lekérdezésekhez vagy feldolgozáshoz, egy lekérdezés és az adatfolyam áthalad az átjáróerőforráson, az Azure Service Buson, a helyi helyszíni adatátjáró-szolgáltatáson és az adatforrásokon. 

![Működés](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Lekérdezések és adatfolyam:

1. A felhőszolgáltatás létrehoz egy lekérdezést a helyszíni adatforráshoz tartozó titkosított hitelesítő adatokkal. Ezt a rendszer elküldi az átjáró várólistájára feldolgozásra.
2. Az átjárófelhő-szolgáltatás elemzi a lekérdezést, és leküldi a kérelmet az [Azure Service Bus.](https://azure.microsoft.com/documentation/services/service-bus/)
3. A helyszíni adatátjáró lekérdezi a függőben lévő kéréseket az Azure Service Busról.
4. Az átjáróhoz beérkezik a lekérdezés, az elvégzi a hitelesítő adatok visszafejtését, majd kapcsolódik az adatforrásokhoz ezekkel a hitelesítő adatokkal.
5. A futtatáshoz az átjáró a lekérdezést elküldi az adatforrásnak.
6. A futtatás eredményeit az adatforrás visszaküldi az átjárónak, majd a felhőszolgáltatásnak és az Ön kiszolgálójának.

## <a name="installing"></a>Telepítés

Az Azure Analysis Services-környezettelepítésekor fontos, hogy kövesse a [helyszíni adatátjáró telepítése és konfigurálása](analysis-services-gateway-install.md)az Azure Analysis Services számára című témakörben leírt lépéseket. Ez a cikk az Azure Analysis Services-re vonatkozik. További lépéseket tartalmaz egy helyszíni adatátjáró-erőforrás beállításához az Azure-ban, és az Azure Analysis Services-kiszolgáló csatlakoztatásához az erőforráshoz.

## <a name="ports-and-communication-settings"></a>Portok és kommunikációs beállítások

Az átjáró kimenő kapcsolatot hoz létre az Azure Service Bus felé. A következő kimenő portokon kommunikál: TCP 443 (alapméretezett), 5671, 5672, 9350–9354.  Az átjáró nem igényel bejövő portokat.

Előfordulhat, hogy az adatrégió IP-címeit meg kell adnia a tűzfalon. [A Microsoft Azure Datacenter IP-címlistáját innen töltheti le](https://www.microsoft.com/download/details.aspx?id=56519). A listát hetente frissítjük. Az Azure Datacenter listájában szereplő IP-címek a CIDR-jelölésrendszer használatával vannak megadva. További információ: [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Az alábbiakban az átjáró által használt, teljesen minősített tartománynevek et használjuk.

| Tartománynevek | Kimenő portok | Leírás |
| --- | --- | --- |
| *.powerbi.com |80 |A telepítő letöltéséhez használt HTTP-cím. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queueing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |A Service Bus Relay figyelői a TCP-n (443-as portot igényel a hozzáférés-vezérlési token beszerzéséhez) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Az internetkapcsolat tesztelésére használható, ha az átjáró nem érhető el a Power BI szolgáltatás számára. |
| *.microsoftonline-p.com |443 |Hitelesítésre használható a konfigurációtól függően. |
| dc.services.visualstudio.com  |443 |Az AppInsights telemetriai adatok gyűjtésére használja. |

### <a name="forcing-https-communication-with-azure-service-bus"></a><a name="force-https"></a>HTTPS-kommunikáció kényszerítése az Azure Service Bus felé

Kényszerítheti az átjárót, hogy kommunikáljon az Azure Service Bus használatával HTTPS helyett a közvetlen TCP; ez azonban nagymértékben csökkentheti a teljesítményt. A *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* fájl módosításával módosíthatja `AutoDetect` `Https`az értéket a értékről a értékre. Ez a fájl általában a *C:\Program Files\On-premises adatátjárón*található.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="next-steps"></a>További lépések 

A következő cikkek a helyszíni adatátjáró általános tartalma, amely az átjáró által támogatott összes szolgáltatásra vonatkozik:

* [Helyszíni adatátjáró – gyakori kérdések](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)   
* [A helyszíni adatátjáró alkalmazás használata](https://docs.microsoft.com/data-integration/gateway/service-gateway-app)   
* [Bérlőszintű felügyelet](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)
* [Proxybeállítások konfigurálása](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)   
* [Kommunikációs beállítások módosítása](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)   
* [Naplófájlok konfigurálása](https://docs.microsoft.com/data-integration/gateway/service-gateway-log-files)   
* [Elhárítása](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Átjáró teljesítményének monitorozása és optimalizálása](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)
