---
title: Az Azure IoT Hub IP-kapcsolat szűrők |} A Microsoft Docs
description: Hogyan használható az IP-szűrés blokk-kapcsolatokat, az adott IP-címek az Azure IoT hubra. Letilthatja egyes érkező kapcsolatokat vagy IP-címek tartományát.
author: rezasherafat
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: rezas
ms.openlocfilehash: 864af9cae35912d95f2c0bf0b574a5ca2404a608
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190641"
---
# <a name="use-ip-filters"></a>IP-szűrők használata

Biztonsági fontos szempont alapján az Azure IoT Hub bármely IoT-megoldás a rendszer. Egyes esetekben kell kifejezetten megad az IP-címek eszközök csatlakozhatnak, amelyről a biztonsági konfiguráció részeként. A _IP-szűrő_ funkciója lehetővé teszi visszautasítja, vagy meghatározott IPv4-címek érkező forgalmat fogad szabályok konfigurálása.

## <a name="when-to-use"></a>A következő esetekben használja

Amikor hasznos lehet az IoT Hub-végpontok bizonyos IP-címek blokkolása, van két adott használati eseteket:

- Az IoT hub kell forgalom fogadására csak a megadott IP-címről, és elvetheti a minden mást. Például használja az IoT hub- [Az Azure Expressroute] egy IoT hubot és a helyszíni infrastruktúra közötti privát kapcsolatok létesíthetők.
- Az IoT hub rendszergazdája gyanús számítógépként azonosított IP-címekről érkező forgalom elutasítása kell.

## <a name="how-filter-rules-are-applied"></a>Szűrési szabályok alkalmazása

Az IP-szűrési szabályok vonatkoznak az IoT Hub szolgáltatási szint. Ezért az IP-szűrési szabályok vonatkoznak az összes kapcsolat olyan eszközökkel és a háttér-alkalmazásokat bármely támogatott protokoll használatával.

Bármely IP-címet, amely az IoT hub rejecting IP szabály megegyezik a kapcsolódási kísérlet kap egy jogosulatlan 401-es állapotkód és a leírást. A válaszüzenet említse meg az IP-szabályt.

## <a name="default-setting"></a>Alapértelmezett beállítás

Alapértelmezés szerint a **IP-szűrő** rács a portálon az IoT hub, az üres. Ez az alapértelmezett beállítás, az azt jelenti, hogy a hub fogad kapcsolatokat IP-címeket. Ez az alapértelmezett beállítás megegyezik egy szabályt, amely elfogadja a 0.0.0.0/0 IP-címtartományt.

![Az IoT Hub alapértelmezett IP-szűrési beállítások][img-ip-filter-default]

## <a name="add-or-edit-an-ip-filter-rule"></a>Adja hozzá, vagy egy IP-szűrési szabály szerkesztése

Amikor hozzáad egy IP-szűrési szabály, a rendszer felszólítja a következő értékeket:

- Egy **IP-szűrési szabály nevének** , amely legfeljebb 128 karakter hosszúságú egyedi, kis-és nagybetűket, alfanumerikus karakterláncnak kell lennie. Csak az ASCII 7 bites alfanumerikus karaktereket plusz `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` fogadja.
- Válassza ki a **elutasítása** vagy **fogadja el** , a **művelet** az IP-szűrési szabály.
- Adjon meg egyetlen IPv4-cím vagy IP-címeket a CIDR-jelölésrendszerben egy kódblokkot. Például a CIDR jelölésrendszerben 192.168.100.0/22 jelöli az 1024 IPv4-címek 192.168.100.0 a 192.168.103.255 közötti.

![Egy IoT hubra egy IP-szűrési szabály hozzáadása][img-ip-filter-add-rule]

A szabály menti, után megjelenik egy riasztást arról tájékoztatja, a frissítés folyamatban van.

![Értesítés az IP-szűrési szabály mentése][img-ip-filter-save-new-rule]

A **Hozzáadás** lehetőség le van tiltva, ha egyenlege eléri a maximális 10 IP-szűrési szabályok.

Meglévő szabály szerkesztéséhez kattintson duplán a szabályt tartalmazó sorhoz.

> [!NOTE]
> Visszautasítja IP címek tudja megakadályozni más Azure-szolgáltatásoktól (például az Azure Stream Analytics, Azure Virtual Machines vagy a portálon a Device Explorer) az IoT hub használata.

> [!WARNING]
> Ha üzenetek olvasásához az IoT hub a IP-szűrés engedélyezve van az Azure Stream Analytics (ASA) használja, használja az Event Hub-kompatibilis névvel és a végpont az IoT hub az ASA kapcsolati karakterláncban.

## <a name="delete-an-ip-filter-rule"></a>Az IP-szűrési szabály törlése

Az IP-szűrési szabály törléséhez válassza ki egy vagy több szabályt a rácson, majd kattintson a **törlése**.

![Egy IoT-központ IP-szűrési szabály törlése][img-ip-filter-delete-rule]

## <a name="ip-filter-rule-evaluation"></a>IP-szűrési szabály értékelése

IP-szűrési szabályok a rendszer sorrendben alkalmazza, és az első szabály, amely az IP-cím megegyezik a elfogadása vagy elutasítása műveletet határozza meg.

Például ha azt szeretné, a tartomány 192.168.100.0/22 címeit fogadja el, és minden más elutasítása, az első szabály a rácson a cím-tartományt 192.168.100.0/22 kell fogadnia. A következő szabályt kell utasítania összes címet a tartomány 0.0.0.0/0 használatával.

A rács az IP-szűrési szabályok sorrendjének módosítása egy sor elején függőleges három pontra kattint, és húzza használatával, és dobja el.

Az új IP-szűrő szabály rendelés mentéséhez kattintson az **mentése**.

![Az IoT Hub IP-szűrési szabályok sorrendjének módosítása][img-ip-filter-rule-order]

## <a name="next-steps"></a>További lépések

Részletesebb megismerése az IoT Hub képességeit, tekintse meg:

- [Műveletek figyelése][lnk-monitor]
- [Az IoT Hub-metrikák][lnk-metrics]

<!-- Images -->
[img-ip-filter-default]: ./media/iot-hub-ip-filtering/ip-filter-default.png
[img-ip-filter-add-rule]: ./media/iot-hub-ip-filtering/ip-filter-add-rule.png
[img-ip-filter-save-new-rule]: ./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png
[img-ip-filter-delete-rule]: ./media/iot-hub-ip-filtering/ip-filter-delete-rule.png
[img-ip-filter-rule-order]: ./media/iot-hub-ip-filtering/ip-filter-rule-order.png


<!-- Links -->

[IoT Hub developer guide]: iot-hub-devguide.md
[Az Azure Expressroute]:  https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services

[lnk-monitor]: iot-hub-operations-monitoring.md
[lnk-metrics]: iot-hub-metrics.md
