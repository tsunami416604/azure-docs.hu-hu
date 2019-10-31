---
title: Helyszíni adatátjáró a Azure Analysis Serviceshoz | Microsoft Docs
description: A helyszíni átjáróra akkor van szükség, ha az Azure-beli Analysis Services-kiszolgáló a helyszíni adatforrásokhoz csatlakozik.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 35ffc7f3c97ca7ab14f94c3607560ffb6ea0b399
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73146850"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Csatlakozás helyszíni adatforrásokhoz helyszíni adatátjáróval

A helyszíni adatátjáró biztonságos adatátvitelt biztosít a helyszíni adatforrások és a felhőben lévő Azure Analysis Services-kiszolgálók között. Az azonos régióban található több Azure Analysis Services kiszolgáló használata mellett az átjáró legújabb verziója Azure Logic Apps, Power BI, Power apps és Microsoft Flow is működik. Több szolgáltatást is hozzárendelhet ugyanahhoz az előfizetéshez, és ugyanazon a régióban egyetlen átjáróval. Míg a telepített átjáró ugyanaz, mint az összes szolgáltatás, Azure Analysis Services és Logic Apps néhány további lépéssel.

Azure Analysis Services esetén a telepítő az átjáróval való első alkalommal egy négy részből álló folyamat:

- A **telepítő letöltése és futtatása** – ezzel a lépéssel telepítheti az átjárószolgáltatás szolgáltatást a szervezeten belüli számítógépen. A [bérlő](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) Azure ad-fiókjával is bejelentkezhet az Azure-ba. Az Azure B2B-(vendég-) fiókok nem támogatottak.

- **Az átjáró regisztrálása** – ebben a lépésben meg kell adnia egy nevet és egy helyreállítási kulcsot az átjáróhoz, és ki kell választania egy régiót, és regisztrálnia kell az átjárót az átjáró Cloud Service-ben. Az átjáró-erőforrás bármely régióban regisztrálható, de javasoljuk, hogy a Analysis Services-kiszolgálókkal megegyező régióban legyen. 

- **Átjáró-erőforrás létrehozása az Azure** -ban – ebben a lépésben létrehoz egy átjáró-erőforrást az Azure-előfizetésében.

- **Csatlakoztassa a kiszolgálókat az átjáró-erőforráshoz** – ha rendelkezik az előfizetésben található átjáró-erőforrással, megkezdheti a kiszolgálók csatlakoztatását. Több kiszolgálót és más erőforrást is összekapcsolhat, ha azok ugyanahhoz az előfizetéshez és régióhoz tartoznak.

## <a name="how-it-works"> </a>Működés
A szervezet egyik számítógépén telepített átjáró Windows-szolgáltatásként, helyszíni **adatátjáróként**fut. Ez a helyi szolgáltatás a Azure Service Buson keresztül regisztrálva van az átjáró Cloud Service-ben. Ezután létrehoz egy helyszíni adatátjáró-erőforrást az Azure-előfizetéséhez. Az Azure Analysis Services-kiszolgálók ezután csatlakoznak az Azure Gateway-erőforráshoz. Ha a kiszolgálón lévő modelleknek lekérdezésekhez vagy feldolgozáshoz kell csatlakozniuk a helyszíni adatforrásokhoz, a lekérdezés és az adatfolyam áthalad az átjáró erőforrásán, Azure Service Bus, a helyi helyszíni adatátjáró szolgáltatáson és az adatforrásokon. 

![Működési elv](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Lekérdezések és adatfolyamok:

1. A felhőalapú szolgáltatás létrehoz egy lekérdezést a helyszíni adatforráshoz tartozó titkosított hitelesítő adatokkal. Ezt követően a rendszer elküldje a várólistára az átjáró feldolgozásához.
2. Az átjáró Cloud Service elemzi a lekérdezést, és leküldi a kérést a [Azure Service Busnak](https://azure.microsoft.com/documentation/services/service-bus/).
3. A helyszíni adatátjáró lekérdezi a függőben lévő kérelmek Azure Service Busét.
4. Az átjáró lekéri a lekérdezést, visszafejti a hitelesítő adatokat, és az adatforrásokhoz csatlakozik a hitelesítő adatokkal.
5. Az átjáró elküldi a lekérdezést az adatforrásnak a végrehajtáshoz.
6. Az eredményeket az adatforrásból, vissza az átjáróba, majd a Cloud Service-be és a-kiszolgálóra küldi a rendszer.

## <a name="installing"></a>Telepítése

Azure Analysis Services környezet telepítésekor fontos, hogy kövesse a helyszíni [adatátjáró telepítése és konfigurálása a Azure Analysis Services számára](analysis-services-gateway-install.md)című témakörben ismertetett lépéseket. Ez a cikk Azure Analysis Servicesra vonatkozik. További lépéseket is tartalmaz, amelyek szükségesek egy helyszíni adatátjáró-erőforrás beállításához az Azure-ban, és a Azure Analysis Services-kiszolgáló összekapcsolása az erőforrással.

## <a name="ports-and-communication-settings"></a>Portok és kommunikációs beállítások

Az átjáró létrehoz egy kimenő kapcsolódást Azure Service Bushoz. A következő kimenő portokon kommunikál: TCP 443 (alapértelmezett), 5671, 5672, 9350 – 9354.  Az átjáró nem igényel bejövő portokat.

Előfordulhat, hogy az adatterületének IP-címeit is meg kell adnia a tűzfalon. Letöltheti az [Microsoft Azure Datacenter IP-listáját](https://www.microsoft.com/download/details.aspx?id=41653). A lista hetente frissül. Az Azure-adatközpont IP-listájában felsorolt IP-címek CIDR-jelöléssel rendelkeznek. További információ: osztály nélküli [tartományok közötti útválasztás](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

A következő az átjáró által használt teljes tartománynevek.

| Tartománynevek | Kimenő portok | Leírás |
| --- | --- | --- |
| *. powerbi.com |80 |A telepítő letöltéséhez használt HTTP. |
| *. powerbi.com |443 |HTTPS |
| *. analysis.windows.net |443 |HTTPS |
| *. login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queueing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Service Bus Relay figyelő a TCP protokollon keresztül (az Access Control token beszerzéséhez 443 szükséges) |
| *. frontend.clouddatahub.net |443 |HTTPS |
| *. core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *. msftncsi.com |443 |Az internetkapcsolat tesztelésére szolgál, ha az átjáró nem érhető el a Power BI szolgáltatás. |
| *. microsoftonline-p.com |443 |Hitelesítéshez használatos a konfigurációtól függően. |
| dc.services.visualstudio.com  |443 |A AppInsights használja a telemetria gyűjtésére. |

### <a name="force-https"></a>HTTPS-kommunikáció kényszerítése Azure Service Bus

Az átjárót úgy kényszerítheti, hogy a közvetlen TCP helyett HTTPS használatával kommunikáljon Azure Service Busekkel. Ez azonban nagy mértékben csökkentheti a teljesítményt. A *Microsoft. PowerBI. DataMovement. pipeline. GatewayCore. dll. config* fájlt úgy módosíthatja, hogy az értéket a `AutoDetect` értékről `Https`ra módosítja. Ez a fájl általában a *C:\Program Files\On-premises adatátjáróban*található.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="next-steps"></a>Következő lépések 

A következő cikkek tartalmazzák a helyszíni adatátjáró általános tartalmát, amely az átjáró által támogatott összes szolgáltatásra vonatkozik:

* [Helyszíni adatátjáró – GYIK](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)   
* [A helyszíni adatátjáró alkalmazás használata](https://docs.microsoft.com/data-integration/gateway/service-gateway-app)   
* [Bérlői szintű felügyelet](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)
* [Proxybeállítások konfigurálása](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)   
* [Kommunikációs beállítások módosítása](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)   
* [Naplófájlok konfigurálása](https://docs.microsoft.com/data-integration/gateway/service-gateway-log-files)   
* [Hibaelhárítás](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Az átjáró teljesítményének figyelése és optimalizálása](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)
