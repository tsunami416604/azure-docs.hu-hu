---
title: Azure IoT Hub díjszabásával |} A Microsoft Docs
description: Fejlesztői útmutató – hogyan mérési és díjszabásának főbb jellemzői az IoT Hub többek között működött példák kapcsolatos információkat.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.openlocfilehash: 9b6db1b7171652ea5ace4db370b72dc22b6bdc90
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271225"
---
# <a name="azure-iot-hub-pricing-information"></a>Az Azure IoT Hub díjszabása

Az [Azure IoT hub díjszabása](https://azure.microsoft.com/pricing/details/iot-hub) a IoT hub különböző SKU-ról és díjszabásáról nyújt általános információt. Ez a cikk tartalmaz további részleteket a hogyan az IoT Hub különféle funkciók biztosításához mérjük üzenetekként IoT Hub által.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>A művelet díjai

| Művelet | Számlázási adatok | 
| --------- | ------------------- |
| Identitásjegyzék műveletei <br/> (létrehozása, beolvasása, listázása, frissítése és törlése) | Nem számítunk fel díjat. |
| Az eszközről a felhőbe irányuló üzenetek | Sikeresen elküldött üzenetet az IoT Hub a bejövő forgalom 4 KB-os blokkonként számítunk fel. Ha például egy 6 KB-os üzenetet 2 darab kell fizetnie. |
| Felhőből az eszközre irányuló üzenetek | Sikeresen elküldött üzeneteket 4 KB-os blokkonként számítunk, például 6 KB-os üzenetet 2 darab díját. |
| Fájlfeltöltések | Az Azure Storage-fájlátvitel IoT Hub által nem forgalmi díjas. Fájl adatátviteli kezdeményezése és -befejezési üzenetet számítunk fel, a 4 KB-os egységekben mért messaged. Például egy 10 MB-os fájl átvitele két üzenetként történik az Azure Storage-költség mellett. |
| Közvetlen metódusok | A metódusok sikeres kérelmeit 4 KB-os adattömbökben számítjuk fel, a válaszokat pedig 4 KB-os adattömbökben, további üzenetként számítjuk fel. A leválasztott eszközöket kérelmeket 4 KB-os blokkonként darab üzenetként számítjuk fel. Például egy 4 KB-os törzstel rendelkező metódus, amely az eszköz törzsével kapcsolatos választ eredményez, két üzenetként lesz felszámítva. A kérelem számára két üzenet és a egy másik üzenet a válasz egy metódust, amely az eszközről egy 1 KB-os válaszul 6 KB-os szervezethez kell fizetnie. |
| Eszköz- és a modul a páros olvasási | A páros olvasási az eszköz vagy a modul és a megoldás háttérrendszere a végfelhasználók 512 bájtos tömbökben darab üzenetként számítjuk fel. Ha például egy 6 KB-os pár olvasása díjat számítunk fel 12 darab üzenetként. |
| Eszköz- és modul ikereszköz-frissítések (címkék és tulajdonságok) | Az eszköz vagy a modul és a megoldás háttérrendszere az ikereszköz-frissítések 512 bájtos tömbökben darab üzenetként számítjuk fel. Ha például egy 6 KB-os pár olvasása díjat számítunk fel 12 darab üzenetként. |
| Eszköz- és modul ikereszköz-lekérdezések | Lekérdezések függően az eredmény mérete 512 bájtos adattömbök darab üzenetként számítjuk fel. |
| Feladatműveletek <br/> (létrehozás, frissítés, listázás, törlés) | Nem számítunk fel díjat. |
| Feladatok eszközönkénti műveleti | Feladatok műveleteket (például az ikereszköz-frissítések, és módszerek) szokásos módon számítjuk fel. Például egy feladat 1000 metódust hívja az 1 KB-os kérelmek és válaszok üres – törzs eredményez 1000 üzeneteket kell fizetnie. |
| Életben tartási üzenetek | Az AMQP és MQTT protokoll használata esetén a kapcsolat által küldött üzenetek és a vyjednávání által küldött üzenetek nem terheli. |

> [!NOTE]
> Méretek arra az esetre vonatkoznak a hasznos adatainak mérete (bájt) (protokoll keretező figyelmen kívül hagyja) a mérlegeli. Üzenetek, tulajdonságok és a szervezet rendelkezik, amelyek mérete számított protokoll-független módon. További információ: [IoT hub üzenet formátuma](iot-hub-devguide-messages-construct.md).

## <a name="example-1"></a>#1. példa

Egy eszközt az IoT hubhoz, amelyeket majd az Azure Stream Analytics egy 1 KB-os eszköz – felhő üzenet percenkénti küld. A megoldás háttérbe állítása 10 percenként meghívja a (512 bájtos adattartalommal rendelkező) metódust az eszközön egy adott művelet elindításához. Az eszköz válaszol az módszer az eredménye, 200 bájt.

Az eszköz felhasználja:

* Egy üzenet * 60 perc * 24 óra = az eszköz – felhő üzenetek napi 1440 üzenet.
* Két kérés és válasz * 6 alkalommal / óra * 24 óra = 288 üzenetekre, a metódusok.

Ehhez a számításhoz 1728 üzenet naponta biztosít.

## <a name="example-2"></a>#2. példa

Egy eszköz 100 KB-os eszközről-a-felhőbe egy üzenetet küld az óránként. Azt is az ikereszköz 1 KB-os hasznos adatokkal négy óránként frissíti. A megoldás háttérrendszere vége, egyszer naponta 14 KB-os ikereszköz beolvasása és konfigurációk módosítása 512 bájtos hasznos adatokkal frissíti.

Az eszköz felhasználja:

* 25 (100 KB és 4 KB-os) üzenetek * 24 órát, hogy az eszköz – felhő üzeneteket.
* Két üzenet (1 KB-os/0,5 KB-os) * hatszor napi ikereszköz-frissítések.

Ehhez a számításhoz 612 üzenet naponta biztosít.

A megoldás háttérrendszere üzeneteket 29 összesen olvasni az ikereszköz 28 üzenetek (14 KB/0,5 KB-os), valamint egy üzenetet a frissítés után használ fel.

Az összes az eszköz és a megoldás háttérrendszere felhasználását napi 641 üzenet.
