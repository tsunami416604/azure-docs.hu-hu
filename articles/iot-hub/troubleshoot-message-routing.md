---
title: Az Azure IoT üzenet-útválasztásának hibáinak megoldása
description: Hibaelhárítás az Azure IoT üzenet-útválasztáshoz
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: asrastog
ms.openlocfilehash: 871a4c7d99fc44cf9868f19e41560e6e7a2e22f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84793323"
---
# <a name="troubleshooting-message-routing"></a>Üzenetek útválasztásának hibaelhárítása

Ez a cikk a IoT Hub [üzenet-útválasztással](iot-hub-devguide-messages-d2c.md)kapcsolatos gyakori problémákkal és megoldásokkal kapcsolatos figyelési és hibaelhárítási útmutatást nyújt. 

## <a name="monitoring-message-routing"></a>Figyelési üzenet útválasztása

[IoT hub mérőszámok](iot-hub-metrics.md) felsorolja az összes olyan metrikát, amely alapértelmezés szerint engedélyezve van a IoT hub számára. Javasoljuk, hogy figyelje az üzenet-útválasztáshoz és a végpontokhoz kapcsolódó metrikákat, hogy áttekintést nyújtson az elküldött üzenetekről. Kapcsolja be a [diagnosztikai naplókat](iot-hub-monitor-resource-health.md) Azure monitor diagnosztikai beállításokban az **útvonalak**műveleteinek nyomon követéséhez. Ezeket a diagnosztikai naplókat Azure Monitor naplókba, Event Hubsba vagy az Azure Storage-ba is elküldhetik egyéni feldolgozásra. Megtudhatja, hogyan [állíthatja be és használhatja a metrikákat és a diagnosztikai naplókat egy IoT hub](tutorial-use-metrics-and-diags.md).

Javasoljuk továbbá a [tartalék útvonal](iot-hub-devguide-messages-d2c.md#fallback-route) engedélyezését is, ha olyan üzeneteket szeretne fenntartani, amelyek nem egyeznek a lekérdezéssel az útvonalak egyikén sem. Ezeket a [beépített végponton](iot-hub-devguide-messages-read-builtin.md) lehet megőrizni a beállított megőrzési napok mennyiségére vonatkozóan. 

## <a name="top-issues"></a>Leggyakoribb problémák

Az üzenetek útválasztásával kapcsolatos leggyakoribb problémák a következők. A hibaelhárítás megkezdéséhez kattintson a probléma részletes lépéseire.

* [Az eszközökről érkező üzeneteket a rendszer nem a várt módon irányítja át](#messages-from-my-devices-are-not-being-routed-as-expected)
* [Hirtelen leállt az üzenetek beolvasása a beépített Event Hubs végponton](#i-suddenly-stopped-getting-messages-at-the-built-in-endpoint)

### <a name="messages-from-my-devices-are-not-being-routed-as-expected"></a>Az eszközökről érkező üzeneteket a rendszer nem a várt módon irányítja át

A probléma megoldásához elemezze a következőket.

#### <a name="the-routing-metrics-for-this-endpoint"></a>A végpont útválasztási metrikái
Az útválasztással kapcsolatos összes [IoT hub-metrika](iot-hub-devguide-endpoints.md) előre van meghatározva az *útválasztással*. Több mérőszámból is egyesítheti az információkat, így azonosíthatja a problémák alapvető okát. A metrikus **útválasztási kézbesítési kísérletekkel** például azonosíthatja, hogy hány üzenet lett kézbesítve egy végpontnak, vagy el lett dobva, ha nem felelnek meg a lekérdezéseknek valamelyik útvonalon és a tartalék útvonalon. Tekintse át az **útválasztási késés** metrikáját, és figyelje meg, hogy az üzenetek kézbesítésének késése állandó vagy növekvő. A növekvő késés egy adott végponttal kapcsolatos problémát jelezhet, és azt javasoljuk, hogy ellenőrizze [a végpont állapotát](#the-health-of-the-endpoint). Ezek az útválasztási mérőszámok olyan [dimenziókkal](iot-hub-metrics.md#dimensions) is rendelkeznek, amelyek részletezik a metrikát, például a végpont típusát, az adott végpont nevét és az üzenet kézbesítésének okát.

#### <a name="the-diagnostic-logs-for-any-operational-issues"></a>Az esetleges működési problémák diagnosztikai naplói 
Figyelje meg az **útvonalak** [diagnosztikai naplóit](iot-hub-monitor-resource-health.md#routes) , hogy további információkat kapjon az útválasztási és végponti [műveletekről](#operation-names) , vagy azonosítsa a hibákat és a vonatkozó [hibakódot](#common-error-codes) a probléma további megismeréséhez. A naplóban szereplő művelet neve például azt jelzi, hogy az útvonal nem értékelhető ki az **RouteEvaluationError** miatt. A probléma megoldásához használja az adott [művelet neveként](#operation-names) megadott tippeket. Ha egy esemény hibát jelez, a napló további információkat is tartalmaz arról, hogy miért nem sikerült a kiértékelés. Ha például a művelet neve **EndpointUnhealthy**, a 403004 [hibakód](#common-error-codes) azt jelzi, hogy a végpont elfogyott.

#### <a name="the-health-of-the-endpoint"></a>A végpont állapota
A végpontok [állapotának beolvasásához](iot-hub-devguide-endpoints.md#custom-endpoints) használja a REST API a [végpont állapota](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) lehetőséget. A *végponti állapot beolvasása* API emellett információt nyújt az üzenet sikeres elküldéséről a végpontra, a [legutóbbi ismert hibára](#last-known-errors-for-iot-hub-routing-endpoints), a legutóbbi ismert hibára és az utolsó alkalommal, amikor egy küldési kísérletet hajtottak végre ehhez a végponthoz. Használja a megadott [utolsó ismert hiba](#last-known-errors-for-iot-hub-routing-endpoints)lehetséges enyhítését.

### <a name="i-suddenly-stopped-getting-messages-at-the-built-in-endpoint"></a>Hirtelen leállt az üzenetek beolvasása a beépített végponton

A probléma megoldásához elemezze a következőket.

#### <a name="was-a-new-route-created"></a>Új útvonal lett létrehozva?
Az útvonal létrehozása után az adatforgalom a beépített végpontra áramlik, hacsak nem jön létre útvonal a végponthoz. Annak biztosítása érdekében, hogy az üzenetek továbbra is a beépített végpontra haladjanak, ha új útvonalat adnak hozzá, konfigurálja az útvonalat az *események* végpontjának. 

#### <a name="was-the-fallback-route-disabled"></a>Le lett tiltva a tartalék útvonal?
A tartalék útvonal minden olyan üzenetet elküld, amely nem felel meg a lekérdezési feltételeknek a meglévő útvonalakon a [beépített Event Hubs](iot-hub-devguide-messages-read-builtin.md) (üzenetek/események) számára, amely kompatibilis a [Event Hubsokkal](https://docs.microsoft.com/azure/event-hubs/). Ha az üzenet-útválasztás be van kapcsolva, engedélyezheti a tartalék útvonal funkciót. Ha nincs elérhető útvonal a beépített végponthoz, és a tartalék útvonal engedélyezve van, csak az útvonalakon nem egyező üzeneteket küld a rendszer a beépített végpontnak. Továbbá, ha az összes meglévő útvonal törölve van, a tartalék útvonalnak engedélyezve kell lennie az összes, a beépített végponton tárolt érték fogadásához.

Engedélyezheti vagy letilthatja a tartalék útvonalat a Azure Portal->üzenet-útválasztás panelen. A [FallbackRouteProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) Azure Resource Manager is használhat egyéni végpontot a tartalék útvonalhoz.

## <a name="last-known-errors-for-iot-hub-routing-endpoints"></a>IoT Hub útválasztási végpontok utolsó ismert hibái

<a id="last-known-errors"></a>
[!INCLUDE [iot-hub-include-last-known-errors](../../includes/iot-hub-include-last-known-errors.md)]

## <a name="routes-diagnostic-logs"></a>A diagnosztikai naplók útvonala

A következő műveletek nevei és hibakódok vannak naplózva a [diagnosztikai naplókban](iot-hub-monitor-resource-health.md#routes).

<a id="diagnostics-operation-names"></a>
### <a name="operation-names"></a>Műveletek nevei

[!INCLUDE [iot-hub-diagnostics-operation-names](../../includes/iot-hub-diagnostics-operation-names.md)]

<a id="diagnostics-error-codes"></a>
### <a name="common-error-codes"></a>Gyakori hibakódok

[!INCLUDE [iot-hub-diagnostics-error-codes](../../includes/iot-hub-diagnostics-error-codes.md)]

## <a name="next-steps"></a>További lépések

Ha további segítségre van szüksége, vegye fel a kapcsolatot az Azure [-szakértőkkel az MSDN Azure-ban, és stack overflow fórumokon](https://azure.microsoft.com/support/forums/)is. Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.
