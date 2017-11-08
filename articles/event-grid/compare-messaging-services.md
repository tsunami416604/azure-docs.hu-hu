---
title: "Azure messaging szolgáltatások összehasonlítása"
description: "Összehasonlítja az Azure Event rács, az Event Hubs és a Service Bus. Azt javasolja, hogy melyik szolgáltatás különböző helyzetek kezelésére."
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/06/2017
ms.author: tomfitz
ms.openlocfilehash: 9a9baa457729bdc4d70a8f9f45701dbdb875d3ce
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="choose-between-azure-services-that-deliver-messages"></a>Válassza az Azure-szolgáltatásokat, hogy a üzenetek

Az Azure, amelyek segítik a teljes megoldás esemény üzenetek kézbesítése mindhárom szolgáltatást kínál. Ezek a szolgáltatások a következők:

* [Esemény rács](/azure/event-grid/)
* [Event Hubs](/azure/event-hubs/)
* [Szolgáltatásbusz](/azure/service-bus-messaging/)

Annak ellenére, hogy néhány Hasonlóságok, minden egyes szolgáltatás célja, adott helyzetekben. Ez a cikk ismerteti ezek a szolgáltatások, és a, valamit megismerheti, hogy melyiket válassza az alkalmazás közötti különbségekről. Sok esetben az üzenetkezelési szolgáltatások kiegészítik, és együtt is használható.

## <a name="event-vs-message-services"></a>Az üzenetsor-szolgáltatások és az esemény

Fontos megjegyezni, hogy a esemény szolgáltatások és üzenetet szolgáltatások közötti különbség van.

### <a name="event"></a>Esemény

Az esemény az egy művelet vagy állapotváltozás egyszerűsített értesítést. Az eseményadatok Mi történt információkat tartalmaz, de nem rendelkezik az adatok az eseményt kiváltó. Például egy esemény értesíti előfizetők, hogy a fájl létrejött-e. A fájl általános információt tartalmazhat, de nem tartalmaz magát a fájlt. Események általában eseménykezelők segítségével valós idejű indítható el.

Esemény rács egy szolgáltatás.

### <a name="message"></a>Üzenet

Egy üzenet által felhasznált vagy máshol tárolt szolgáltatás nyers adatok. Az üzenet az üzenet folyamat elindul adatokat tartalmazza. Ez az üzenet elektronikus kereskedelmi megrendelés felhasználói telemetriai bármi lehet. Egy eseményértesítés eltérően a közzétevő üzenet ettől a választ. Egy üzenet például a nyers adatokat tartalmaz, de vár a következő fájl létrehozása az adatokat a rendszer része. 

Az Event Hubs és a Service Bus üzenetküldési szolgáltatások.

## <a name="comparison-of-services"></a>Szolgáltatások összehasonlítása

| Szolgáltatás | Cél | Típus | A következő esetekben használja |
| ------- | ------- | ---- | ----------- |
| Event Grid | Reaktív programozás | Esemény | Állapotmódosítások reagálnak |
| Event Hubs | Big Data típusú adatok feldolgozási folyamat | Üzenet | Telemetriai adatok és az elosztott adatokon adatfolyam |
| Service Bus | A jó minőségű vállalati üzenetkezeléstől | Üzenet | Rendelés feldolgozása és a pénzügyi tranzakciók |

### <a name="event-grid"></a>Event Grid

Esemény rács egy esemény csatlakozópanel, amely lehetővé teszi a eseményvezérelt, reaktív programozási. Használja a publish-modell előfizetés. Közzétevők események létrehozása, de nem a várt értéket arról, hogy mely események kezeli kell. Előfizetők kezelni kívánt eseményeket határozzák meg.

Esemény rács mélyen integrált Azure-szolgáltatások és harmadik féltől származó szolgáltatással integrálható. Leegyszerűsíti az események felhasználásához, és nem állandó lekérdezéshez kell csökkenti a költségeket. Esemény rács hatékony és megbízhatóan továbbítja az Azure és az-Azure erőforrások származó eseményeket. Az események regisztrált előfizető végpontok terjesztett. Az eseményüzenetben a szolgáltatások és alkalmazások változásairól reagálni szükséges információkat. Esemény rács nincs adatok folyamat, és nem kézbesíti a tényleges objektum, amely frissítve lett.

A következő jellemzőkkel rendelkezik:

* dinamikusan méretezhető
* Alacsony költség
* Kiszolgáló nélküli

### <a name="event-hubs"></a>Event Hubs

Az Azure Event Hubs egy big Data típusú adatok folyamat. Megkönnyíti a rögzítést, az adatmegőrzés és az ismétlés telemetriai adatok és az esemény adatfolyam adatok. Az adatok sok egyidejű forrásokból származhatnak. Az Event Hubs lehetővé teszi, hogy a telemetriai adatok és az esemény adatainak adatfolyam-feldolgozási infrastruktúra és az elemzések szolgáltatások számos különböző elérhetőek legyenek. Érhető el az adatfolyamok vagy csomagolt esemény kötegek. Ez a szolgáltatás, amely lehetővé teszi a valós idejű feldolgozással, valamint a tárolt nyers adatok ismételt ismétlés gyors adatok beolvasása egyetlen megoldást kínál.

A következő jellemzőkkel rendelkezik:

* Kis késés
* képes a fogadása és feldolgozása több millió esemény / másodperc

### <a name="service-bus"></a>Service Bus

A Service Bus hagyományos vállalati alkalmazások számára készült. A vállalati alkalmazásoknak tranzakciók, rendezés, kettős észlelés és azonnali konzisztencia. A Service Bus lehetővé teszi, hogy a felhő natív alkalmazások megbízható átmenet állapotkezelés az üzleti folyamatok biztosításához. Elvész, vagy nem lettek duplikálva nagy értékű üzenetek kezelésekor Azure Service Bus használatára. A Service Bus is elősegíti a rendkívül biztonságos kommunikációt hibrid felhőalapú megoldásokkal, és a meglévő helyszíni rendszerek csatlakozhat megoldások.

A következő jellemzőkkel rendelkezik:

* Megbízható aszinkron üzenetkézbesítést (a vállalati üzenetkezelési szolgáltatás), amely lekérdezési igényel
* Speciális üzenetkezelési szolgáltatások, például FIFO, kötegelés/munkamenetek, tranzakciók, kézbesítetlen levelek kezelése, historikus vezérlő, Útválasztás és szűrés vagy kettős észlelés

## <a name="use-the-services-together"></a>A szolgáltatások együtt használható

Bizonyos esetekben a szolgáltatások párhuzamos különböző szerepkört használja. Például egy kereskedelmi webhely használatával a Service Bus feldolgozni a rendelését, az Event Hubs hely telemetriai adatok rögzítéséhez és esemény rács események, például egy cikk teljesített válaszolni.

Más esetekben csatolunk összeállíthat egy esemény és az adatok csővezeték őket. Esemény rács segítségével más szolgáltatások események válaszolni. Például egy esemény rács segítségével az Event Hubs adatraktár adatok áttelepítéséhez, lásd: [big Data típusú adatok adatfolyamként történő adatraktár](event-grid-event-hubs-integration.md). A következő kép bemutatja az adatok folyamatos munkafolyamat.

![Az adatfolyam adatok áttekintése](./media/compare-messaging-services/overview.png)

## <a name="next-steps"></a>Következő lépések

* Az Azure üzenetküldési szolgáltatásokkal kapcsolatos további információkért lásd a következő blogbejegyzésben [események, adatpontok és üzenetek - az adatok közvetlenül az Azure üzenetküldési szolgáltatás kiválasztása](https://azure.microsoft.com/blog/events-data-points-and-messages-choosing-the-right-azure-messaging-service-for-your-data/).
* Esemény rácshoz ismertetőért lásd: [esemény rács](overview.md).
* Első lépésként esemény rácshoz, lásd: [Azure esemény rácshoz hozza létre és útvonal egyéni események](custom-event-quickstart.md).
* Bevezetés az Event Hubs használatába, lásd: [hozzon létre egy Event Hubs névtér és egy eseményközpontot, az Azure portál használatával](../event-hubs/event-hubs-create.md).
* Első lépésként a Service busszal, lásd: [az Azure portál használatával a Service Bus-névtér létrehozása](../service-bus-messaging/service-bus-create-namespace-portal.md).