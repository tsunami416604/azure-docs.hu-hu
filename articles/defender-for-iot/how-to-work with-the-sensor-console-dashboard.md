---
title: Az érzékelő konzol irányítópultjának használata
description: Az irányítópult segítségével gyorsan megtekintheti a hálózat biztonsági állapotát. Magas szintű áttekintést nyújt a fenyegetésekről az egész rendszerre vonatkozóan egy idővonalon, valamint a kapcsolódó eszközökre vonatkozó információkkal.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/03/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 735b1ce4391598d05a1bf0b4486503092f4de37d
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2020
ms.locfileid: "97840628"
---
# <a name="the-dashboard"></a>Az irányítópult

Az irányítópult segítségével gyorsan megtekintheti a hálózat biztonsági állapotát. Magas szintű áttekintést nyújt a fenyegetésekről az egész rendszerre vonatkozóan egy idővonalon, valamint a kapcsolódó eszközökről, többek között a következőkről:

- Különböző súlyossági szintű riasztások:

- Kritikus

- Jelentős

- Kismérvű

- Figyelmeztetések

- A lap közepén található két mérőműszer a másodpercenkénti (PPS) és a nem Visszaigazolatlan riasztásokat (UA) jelzi. A **PPS** a rendszer által másodpercenként visszaigazolt csomagok száma. Az **ua** a még nem elismert riasztások száma.

- A nem visszaigazolt riasztások listája a leírással.

- A riasztás leírását tartalmazó idősor.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/dashboard-alert-screen-v2.png" alt-text="Irányítópult":::

## <a name="viewing-the-latest-alerts"></a>A legújabb riasztások megtekintése

A nem visszaigazolt riasztások (UA) mérőműszer az oldal közepén jelzi az ilyen riasztások számát. A riasztások listájának megtekintéséhez válassza a **további riasztások** elemet az irányítópult lap alján, vagy válassza a **riasztások** lehetőséget az oldalsó menüben.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unhandled-alerts-list.png" alt-text="Nem nyugtázott riasztások":::

## <a name="status-boxes"></a>Állapot mezők

Ebben a szakaszban az egyes állapotok mezőit ismertetjük.

| Állapot mező és mérőműszerek | Leírás |
| -------------- | -------------- |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/critical-alert-status-box-v2.png" alt-text="Kritikus riasztások"::: | **Kritikus riasztások** – az oldal felső részén található doboz jelzi a kritikus riasztások számát. Jelölje be ezt a jelölőnégyzetet, ha a riasztások leírását szeretné megjeleníteni az idővonalon és a mérőműszerek listáján, ha van ilyen.                              |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/major-alert-status-box-v2.png" alt-text="Főbb riasztások"::: | **Nagyobb riasztások** – az oldal jobb felső sarkában található jelölőnégyzet a fő riasztások számát jelzi. Jelölje be ezt a jelölőnégyzetet, ha a riasztások leírását szeretné megjeleníteni az idővonalon és a mérőműszerek listáján, ha van ilyen.                                     |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/minor-alert-status-box-v2.png" alt-text="Másodlagos riasztások"::: | **Másodlagos riasztások** – az oldal bal alsó sarkában található jelölőnégyzet a kisebb riasztások számát jelzi. Jelölje be ezt a jelölőnégyzetet, ha a riasztások leírását szeretné megjeleníteni az idővonalon és a mérőműszerek listáján, ha van ilyen.                                   |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/warnings-alert-status-box-v2.png" alt-text="Figyelmeztető riasztások"::: | **Figyelmeztető riasztások** – az oldal alján található doboz a figyelmeztető riasztások számát jelzi. Jelölje be ezt a jelölőnégyzetet, ha a riasztások leírását szeretné megjeleníteni az idővonalon és a mérőműszerek listáján, ha van ilyen.                             |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/all-alert-status-box-v2.png" alt-text="All Alerts"::: | **Minden riasztás** – a lap jobb alsó sarkában lévő mező a kritikus, a fő, a másodlagos és a figyelmeztető riasztások teljes számát jelzi. Jelölje be ezt a jelölőnégyzetet, ha a riasztások leírását szeretné megjeleníteni az idővonalon és a mérőműszerek listáján, ha van ilyen. |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/packets-per-second-gauge-v2.png" alt-text="Másodpercenkénti csomagok száma"::: | **Csomagok másodpercenként (PPS)** – a PPS metrika a hálózat teljesítményének indikátora. |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unacknowledged-events-gauge-v2.png" alt-text="Nem nyugtázott események (UA)"::: | Nem **nyugtázott események** – ez a metrika a nem nyugtázott események számát jelzi.

## <a name="using-the-timeline"></a>Az idősor használata

A riasztások a dátum-és időadatokat tartalmazó függőleges idővonalon jelennek meg.

Az idősor grafikus megjelenítése:

- Kritikus riasztások

- Főbb riasztások

- Másodlagos riasztások

- Figyelmeztető riasztások

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/timeline-of-events.png" alt-text="Idősor gráf":::

## <a name="viewing-alerts"></a>Riasztások megtekintése

Kattintson a riasztási mező alján lévő **lefelé mutató** nyílra a riasztási bejegyzés és az eszközök adatainak megjelenítéséhez.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/extended-alert-screen.png" alt-text="Riasztási bejegyzés és eszközök adatai":::

- Válassza ki az eszközt, vagy jelenítse meg az **eszközöket** a fizikai mód térképének megjelenítéséhez. A tárgyú eszközök ki vannak emelve.

- Válassza az :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/excel-icon.png" alt-text="Excel"::: elemet a riasztást tartalmazó CSV-fájl exportálásához.

- Rendszergazdák és biztonsági elemzők – az összes kapcsolódó riasztás **visszaigazolásához** válassza :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/approve-all-icon.png" alt-text="az összes"::: elfogadása lehetőséget.

- Válassza ki a riasztási bejegyzést a riasztás típusának és leírásának megtekintéséhez:

- Válassza a :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pdf-icon.png" alt-text="PDF":::lehetőséget a riasztási jelentés PDF-fájlként való letöltéséhez.

- Válassza a :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pin-icon.png" alt-text="rögzítés":::lehetőséget a riasztás rögzítéséhez vagy feloldásához.

- Válassza a :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/download-icon.png" alt-text="Letöltés"::: lehetőséget a riasztás kivizsgálásához a hálózati protokollok elemzését tartalmazó Pcap fájl letöltésével.

- Válassza a :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/cloud-download-icon.png" alt-text="felhő"::: lehetőséget, ha le szeretne tölteni egy SZŰRt Pcap-fájlt, amely csak a riasztással kapcsolatos csomagokat tartalmazza, így csökkentve a kimeneti fájlok méretét, és lehetővé teszi a célzottabb elemzést. Megtekintheti a [Wireshark](https://www.wireshark.org/)használatával.

- Válassza a :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/navigate-icon.png" alt-text="navigáció"::: lehetőséget, hogy a kért riasztás időpontjában navigáljon az esemény idővonalához.

- Rendszergazdák és biztonsági elemzők – csak a riasztás állapotának módosítása a Visszaigazolatlan értékről a visszaigazolásra. Válassza a tanulás lehetőséget az észlelt tevékenység jóváhagyásához.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unauthorized-internet-connectivity-detection-v3.png" alt-text="Jogosulatlan internetkapcsolat észlelhető":::

## <a name="see-also"></a>További információ

[Riasztások használata az érzékelőn](how-to-work-with-alerts-on-your-sensor.md)
