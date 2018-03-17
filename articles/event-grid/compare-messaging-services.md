---
title: "Azure messaging szolgáltatások összehasonlítása"
description: "Összehasonlítja az Azure Event rács, az Event Hubs és a Service Bus. Azt javasolja, hogy melyik szolgáltatás különböző helyzetek kezelésére."
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 03/16/2018
ms.author: tomfitz
ms.openlocfilehash: 30bbe7442cac96a1dcf6959cac2abedd61454a29
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="choose-between-azure-services-that-deliver-messages"></a>Válassza az Azure-szolgáltatásokat, hogy a üzenetek

Az Azure, amelyek segítik a teljes megoldás esemény üzenetek kézbesítése mindhárom szolgáltatást kínál. Ezek a szolgáltatások a következők:

* [Esemény rács](/azure/event-grid/)
* [Event Hubs](/azure/event-hubs/)
* [Service Bus](/azure/service-bus-messaging/)

Annak ellenére, hogy néhány Hasonlóságok, minden egyes szolgáltatás célja, adott helyzetekben. Ez a cikk ismerteti ezek a szolgáltatások, és a, valamit megismerheti, hogy melyiket válassza az alkalmazás közötti különbségekről. Sok esetben az üzenetkezelési szolgáltatások kiegészítik, és együtt is használható.

## <a name="event-vs-message-services"></a>Az üzenetsor-szolgáltatások és az esemény

Fontos megjegyezni, hogy a esemény szolgáltatások és üzenetet szolgáltatások közötti különbség van.

### <a name="event"></a>Esemény

Egy esemény egy egyszerűsített értesítést feltételt vagy állapotváltozás. Az esemény a közzétevő nem várt értéket, az eseményt a rendszer hogyan kezelje kapcsolatos rendelkezik. A fogyasztó az esemény mellett dönt, mi a teendő, ha az értesítés. Események diszkrét egység vagy egy sorozat része lehet.

Különálló események jelentés állapotváltozás és hajtható végre. A következő lépéssel, az ügyfél csak kell tudnia, hogy történt. Az eseményadatok Mi történt információkat tartalmaz, de nem rendelkezik az adatok az eseményt kiváltó. Például egy esemény értesíti a fogyasztók, hogy a fájl létrejött-e. A fájl általános információt tartalmazhat, de nem tartalmaz magát a fájlt. Különálló események alkalmasak a kiszolgáló nélküli méretezési igénylő megoldásokhoz.

Adatsorozat események jelentést egy feltételt és analyzable. Az események idő rendelt és egymáshoz. A fogyasztó az előkészített eseménysorozatokat, mi történt elemzéséhez szükséges.

### <a name="message"></a>Üzenet

Egy üzenet által felhasznált vagy máshol tárolt szolgáltatás nyers adatok. Az üzenet az üzenet folyamat elindul adatokat tartalmazza. A közzétevő, az üzenet rendelkezik egy általános gyakorlat kapcsolatos miként kezeli a fogyasztó a az üzenetet. A szerződés létezik-e a két fél közötti. Például a közzétevő a nyers adatokat tartalmazó üzenetet küld, és vár a felhasználó a fájl létrehozása az adatok, és választ küld, ha a munkát.

## <a name="comparison-of-services"></a>Szolgáltatások összehasonlítása

| Szolgáltatás | Cél | Típus | A következő esetekben használja |
| ------- | ------- | ---- | ----------- |
| Event Grid | Reaktív programozás | Események terjesztését (diszkrét) | Állapotmódosítások reagálnak |
| Event Hubs | Big Data típusú adatok feldolgozási folyamat | Esemény streaming (sorozat) | Telemetriai adatok és az elosztott adatokon adatfolyam |
| Service Bus | A jó minőségű vállalati üzenetkezeléstől | Üzenet | Rendelés feldolgozása és a pénzügyi tranzakciók |

### <a name="event-grid"></a>Event Grid

Esemény rács egy esemény csatlakozópanel, amely lehetővé teszi a eseményvezérelt, reaktív programozási. Használja a publish-modell előfizetés. Közzétevők események létrehozása, de nem a várt értéket arról, hogy mely események kezeli kell. Előfizetők kezelni kívánt eseményeket határozzák meg.

Esemény rács mélyen integrált Azure-szolgáltatások és harmadik féltől származó szolgáltatással integrálható. Leegyszerűsíti az események felhasználásához, és nem állandó lekérdezéshez kell csökkenti a költségeket. Esemény rács hatékony és megbízhatóan továbbítja az Azure és az-Azure erőforrások származó eseményeket. Az események regisztrált előfizető végpontok terjesztett. Az eseményüzenetben a szolgáltatások és alkalmazások változásairól reagálni szükséges információkat. Esemény rács nincs adatok folyamat, és nem kézbesíti a tényleges objektum, amely frissítve lett.

A következő jellemzőkkel rendelkezik:

* dinamikusan méretezhető
* Alacsony költség
* serverless

### <a name="event-hubs"></a>Event Hubs

Az Azure Event Hubs egy big Data típusú adatok folyamat. Megkönnyíti a rögzítést, az adatmegőrzés és az ismétlés telemetriai adatok és az esemény adatfolyam adatok. Az adatok sok egyidejű forrásokból származhatnak. Az Event Hubs lehetővé teszi, hogy a telemetriai adatok és az esemény adatainak adatfolyam-feldolgozási infrastruktúra és az elemzések szolgáltatások számos különböző elérhetőek legyenek. Érhető el az adatfolyamok vagy csomagolt esemény kötegek. Ez a szolgáltatás, amely lehetővé teszi a valós idejű feldolgozással, valamint a tárolt nyers adatok ismételt ismétlés gyors adatok beolvasása egyetlen megoldást kínál. A streamelési adatok feldolgozására és elemzési egy fájlba rögzítheti azt.

A következő jellemzőkkel rendelkezik:

* Kis késés
* képes a fogadása és feldolgozása több millió esemény / másodperc

### <a name="service-bus"></a>Service Bus

A Service Bus hagyományos vállalati alkalmazások számára készült. A vállalati alkalmazásoknak tranzakciók, rendezés, kettős észlelés és azonnali konzisztencia. A Service Bus lehetővé teszi, hogy a felhő natív alkalmazások megbízható átmenet állapotkezelés az üzleti folyamatok biztosításához. Elvész, vagy nem lettek duplikálva nagy értékű üzenetek kezelésekor Azure Service Bus használatára. A Service Bus is elősegíti a rendkívül biztonságos kommunikációt hibrid felhőalapú megoldásokkal, és a meglévő helyszíni rendszerek csatlakozhat megoldások.

A Service Bus egy olyan közvetítőalapú üzenettovábbítás rendszer. Az "ügynök" üzenetek tárol (például egy üzenetsort) addig, amíg a fogyasztó fél készen áll az üzenetek fogadásához nem.

A következő jellemzőkkel rendelkezik:

* Megbízható aszinkron üzenetkézbesítést (a vállalati üzenetkezelési szolgáltatás), amely lekérdezési igényel
* Speciális üzenetkezelési szolgáltatások, például FIFO, kötegelés/munkamenetek, tranzakciók, kézbesítetlen levelek kezelése, historikus vezérlő, Útválasztás és szűrés vagy kettős észlelés

## <a name="use-the-services-together"></a>A szolgáltatások együtt használható

Bizonyos esetekben a szolgáltatások párhuzamos különböző szerepkört használja. Például egy kereskedelmi webhely használatával a Service Bus feldolgozni a rendelését, az Event Hubs hely telemetriai adatok rögzítéséhez és esemény rács események, például egy cikk teljesített válaszolni.

Más esetekben csatolunk összeállíthat egy esemény és az adatok csővezeték őket. Esemény rács segítségével más szolgáltatások események válaszolni. Például egy esemény rács segítségével az Event Hubs adatraktár adatok áttelepítéséhez, lásd: [big Data típusú adatok adatfolyamként történő adatraktár](event-grid-event-hubs-integration.md). A következő kép bemutatja az adatok folyamatos munkafolyamat.

![Az adatfolyam adatok áttekintése](./media/compare-messaging-services/overview.png)

## <a name="next-steps"></a>További lépések

* Az Azure üzenetküldési szolgáltatásokkal kapcsolatos további információkért lásd a következő blogbejegyzésben [események, adatpontok és üzenetek - az adatok közvetlenül az Azure üzenetküldési szolgáltatás kiválasztása](https://azure.microsoft.com/blog/events-data-points-and-messages-choosing-the-right-azure-messaging-service-for-your-data/).
* Esemény rácshoz ismertetőért lásd: [esemény rács](overview.md).
* Első lépésként esemény rácshoz, lásd: [Azure esemény rácshoz hozza létre és útvonal egyéni események](custom-event-quickstart.md).
* Bevezetés az Event Hubs használatába, lásd: [hozzon létre egy Event Hubs névtér és egy eseményközpontot, az Azure portál használatával](../event-hubs/event-hubs-create.md).
* Első lépésként a Service busszal, lásd: [az Azure portál használatával a Service Bus-névtér létrehozása](../service-bus-messaging/service-bus-create-namespace-portal.md).