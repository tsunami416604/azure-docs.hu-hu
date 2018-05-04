---
title: Az Azure IoT Hub IP-kapcsolat szűrők |} Microsoft Docs
description: Útmutató az Azure IoT hub az adott IP-címek a blokk-kapcsolatokat a szűrési IP-címet használ. Letilthatja egyes érkező kapcsolatokat vagy IP-címek tartományát.
services: iot-hub
documentationcenter: ''
author: BeatriceOltean
manager: timlt
editor: ''
ms.assetid: f833eac3-5b5f-46a7-a47b-f4f6fc927f3f
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: boltean
ms.openlocfilehash: 0229b8b16de1fa04d953c90895b29e4fbad60ec3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="use-ip-filters"></a>IP-szűrők használata

A biztonság az Azure IoT Hub alapján IoT megoldások fontos eleme. Néha kell explicit módon adja meg az IP-címek, ahol az eszközök csatlakozni tud a biztonsági beállítások részeként. A _IP-szűrő_ funkciója lehetővé teszi elutasítása, vagy meghatározott IPv4-címeket érkező forgalmat fogad szabályok konfigurálása.

## <a name="when-to-use"></a>A következő esetekben használja

Nincsenek két adott használati esetek letilthatja egyes IP-címek az IoT-központok végpontjai hasznos:

- Az IoT hub kell forgalom fogadására csak a megadott IP-címről, és minden más elutasítása. Az IoT hub való használata esetén például [Azure Express Route] az IoT-központ és a helyszíni infrastruktúra közötti magánhálózati kapcsolatok létrehozásához.
- Az IoT hub-rendszergazda által azonosított, gyanús IP-címekről érkező forgalom elutasítása kell.

## <a name="how-filter-rules-are-applied"></a>Állapotszűrő szabályok alkalmazása

Az IP-szűrő szabályok érvényesek az IoT-központ szolgáltatás szintjén. Ezért az IP-szűrési szabályokat alkalmazni az összes kapcsolat az eszközökről és a háttér-alkalmazások bármely támogatott protokoll használatával.

A kapcsolódási kísérlet egy IP-címről, amely megfelel egy rejecting IP szabályt az IoT hub kap egy jogosulatlan 401-es állapotkód és a leírást. A válaszüzenet nem említi az IP-szabály.

## <a name="default-setting"></a>Alapértelmezett beállítás

Alapértelmezés szerint a **IP-szűrő** a portálon az IoT-központ a rács értéke üres. Ez az alapértelmezett beállítás azt jelenti, hogy a központ kapcsolatokat fogad IP-címeket. Ez az alapértelmezett beállítás megegyezik egy szabályt, amely elfogadja a 0.0.0.0/0 IP-címtartományt.

![Az IoT-központ alapértelmezett IP-szűrési beállítások][img-ip-filter-default]

## <a name="add-or-edit-an-ip-filter-rule"></a>Az IP-szűrési szabály felvétele vagy szerkesztése

Amikor egy IP-szűrő szabályt, kéri a következő értékeket:

- Egy **IP-szűrés szabálynév** , amely legfeljebb 128 karakter hosszú egyedi, nem betűérzékeny, alfanumerikus karakterláncnak kell lennie. Csak az ASCII 7 bites alfanumerikus karakterek plusz `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` elfogadottak.
- Válassza ki a **elutasítása** vagy **fogadja el** , a **művelet** esetében az IP-szűrő szabályt.
- Adjon meg egy IPv4-cím vagy IP-címek a CIDR jelölésrendszer blokkban. Például a CIDR jelölésrendszer 192.168.100.0/22 képviseli az 1024 IPv4-címeket a 192.168.100.0 192.168.103.255.

![Az IP-szűrési szabály hozzáadása az IoT-központ][img-ip-filter-add-rule]

A szabály mentése után látni egy riasztást, amely értesíti, hogy a frissítés van folyamatban.

![Értesítés egy IP-szűrési szabály mentése][img-ip-filter-save-new-rule]

A **Hozzáadás** lehetőség le van tiltva, ha eléri a maximális száma 10 IP-szűrő szabályokban.

A szabályt tartalmazó sor duplán kattintva szerkesztheti a meglévő szabály.

> [!NOTE]
> Elutasítása IP címeket így előfordulhat, hogy más Azure-szolgáltatások (például az Azure Stream Analytics, Azure virtuális gépek vagy az eszköz Explorer, a portálon) az IoT hub való interakció.

> [!WARNING]
> Ha üzenetek olvasásakor az IoT-központ IP-szűrés engedélyezve van az Azure Stream Analytics (ASA) használ, használja a Event Hub-kompatibilis nevét és az IoT-központ végpontjának a ASA kapcsolati karakterláncban.

## <a name="delete-an-ip-filter-rule"></a>Az IP-szűrési szabály törlése

Az IP-szűrési szabály törléséhez válassza ki egy vagy több szabály a rács, majd kattintson a **törlése**.

![Az IoT-központ IP-szűrési szabály törlése][img-ip-filter-delete-rule]

## <a name="ip-filter-rule-evaluation"></a>IP-szűrési szabály értékelése

IP-szűrési szabályokat a rendszer sorrendben alkalmazza, és az első szabály, amely megfelel az IP-cím határozza meg, a elfogadása vagy elutasítása művelet.

Például ha a tartomány 192.168.100.0/22 címek elfogadása vagy elutasítása minden más, a rács lévő első szabály kell fogadnia a cím tartomány 192.168.100.0/22. A következő szabály kell utasítania összes cím a tartomány 0.0.0.0/0 használatával.

A rács IP-Állapotszűrő szabályok sorrendjének módosításához kattintson egy sor elején függőleges három pontra, majd húzza használatával, és dobja el.

Az új IP-szűrő szabály rendelés mentéséhez kattintson **mentése**.

![Az IoT-központ IP-szűrő szabályok sorrendjének módosítása][img-ip-filter-rule-order]

## <a name="next-steps"></a>További lépések

Az IoT-központ képességeit további megismeréséhez lásd:

- [Figyelési műveletek][lnk-monitor]
- [Az IoT-központ metrikák][lnk-metrics]

<!-- Images -->
[img-ip-filter-default]: ./media/iot-hub-ip-filtering/ip-filter-default.png
[img-ip-filter-add-rule]: ./media/iot-hub-ip-filtering/ip-filter-add-rule.png
[img-ip-filter-save-new-rule]: ./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png
[img-ip-filter-delete-rule]: ./media/iot-hub-ip-filtering/ip-filter-delete-rule.png
[img-ip-filter-rule-order]: ./media/iot-hub-ip-filtering/ip-filter-rule-order.png


<!-- Links -->

[IoT Hub developer guide]: iot-hub-devguide.md
[Azure Express Route]:  https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services

[lnk-monitor]: iot-hub-operations-monitoring.md
[lnk-metrics]: iot-hub-metrics.md
