---
title: Azure IoT Hub árképzési megértése |} Microsoft Docs
description: Fejlesztői útmutató - hogyan mérési és árképzési működik, beleértve az IoT-központ dolgozott példák kapcsolatos információkat.
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 1ac90923-1edf-4134-bbd4-77fee9b68d24
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 9b0d2df078c59c7d261fd3231450ddfb2fdcd88e
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="azure-iot-hub-pricing-information"></a>Díjszabási információkért Azure IoT Hub

[Az Azure IoT Hub árképzési] [ lnk-pricing] különböző Termékváltozatai és az IoT-központ díjszabása az általános információkat nyújt. A cikkben megtudhatja, hogyan a különböző IoT-központ funkciók mérése üzenetekként IoT-központ által a további részletek.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>Művelet díjak

| Művelet | Számlázási adatokat | 
| --------- | ------------------- |
| Identitásjegyzék műveletei <br/> (létrehozása, beolvasása, listázása, frissítése és törlése) | Nem számítjuk fel. |
| Az eszközről a felhőbe irányuló üzenetek | Sikeresen küldött üzenetek a 4 KB-os adattömböket a érkező van szó, az IoT-központot. Például egy 6 KB-os üzenet díjfizetéssel 2 üzenet. |
| Felhő-eszközre küldött üzenetek | Sikeresen elküldött üzenetek van szó, a 4 KB-os adattömböket, például egy 6 KB-os üzenet díjfizetéssel 2 üzenet. |
| Fájlfeltöltések | Azure Storage fájlátvitel nem forgalmi díjas IoT hubból. Átviteli kezdeményezés és befejezési üzeneteit van szó, mint 4 KB-os lépésekben mért messaged. Például egy 10 MB-os fájlban fel van töltve az Azure Storage költség mellett két üzenet. |
| Közvetlen metódusok | Sikeres metóduskérelmek van szó, a 4 KB-os adattömböket, nem üres azokkal válaszait van szó, a 4 KB-os adattömböket, a további üzeneteket. Kérelmek leválasztott eszközökre van szó, a 4 KB-os adattömbök üzeneteihez. Például egy metódust, amely az eszköz válaszára nem szervezethez eredményez 6 KB-os szervezethez két üzenetekként számítjuk fel. Egy metódust, amely az eszközről egy 1 KB-os válasz eredményez 6 KB-os szervezethez fel van töltve, a kérelem két üzenetről és a válasz egy másik üzenetet. |
| A két eszköz és a modul olvassa be | A két beolvassa az eszköz vagy modul és a megoldás háttérrendszere end van szó, mint 512 bájtos adattömbök üzenetek. Például egy 6 KB-os iker olvasása díjfizetéssel 12 üzeneteihez. |
| És modul iker frissítések (címkék és tulajdonságait) | Az eszköz vagy modul és a megoldás háttérrendszeréhez iker frissítések van szó, az 512 bájtos adattömbök üzeneteihez. Például egy 6 KB-os iker olvasása díjfizetéssel 12 üzeneteihez. |
| Eszköz- és modul iker lekérdezések | Lekérdezések van szó, attól függően, hogy az eredmény méretének az 512 bájtos adattömbök üzeneteihez. |
| Feladatműveletek <br/> (létrehozás, frissítés, listázás, törlés) | Nem számítjuk fel. |
| Feladatok eszközönkénti műveletek | Feladatok műveletek (például a kettős frissítések, és módszerek) normál van szó. Például egy 1 KB-os kérelmek és üres-törzsének válaszokban 1000 metódushívások eredményezve feladat díjfizetéssel 1000 üzeneteket. |

> [!NOTE]
> Különböző méretű arra az esetre vonatkoznak annak eldöntéséhez, hogy a terhelés méretének bájtban (protokoll keretezési van figyelmen kívül hagyva). Az üzenetek, amelyeknek tulajdonságait és a szervezet, méretének számított protokoll-független módon. További információkért lásd: [IoT Hub fejlesztői útmutató üzenetküldési][lnk-message-size].

## <a name="example-1"></a>#1. példa

Egy eszköz küld az IoT-központ, amely majd beolvassa az Azure Stream Analytics egy 1 KB-os eszközről a felhőbe üzenet / perc. A megoldás háttérrendszeréhez hív meg (a 512 bájtos tartalom) metódus az eszközön 10 percenként egy bizonyos művelet indításához. Az eszköz válaszol-e a metódus eredménye 200 bájt.

Az eszköz használ:

* Egy üzenet * 60 perc * 24 óra = 1440 üzenet az eszközről a felhőbe üzenetek naponta.
* Két kérelem és válasz * 6 alkalommal óránként * 24 óra = 288 üzenetek a módszer.

A számítás 1728 üzenet naponta összesen biztosít.

## <a name="example-2"></a>#2. példa

Egy eszköz óránként egy 100 KB-os eszközről a felhőbe üzenetet küld. Azt is frissíti az eszköz kettős 1 KB-os hasznos adat található négy óránként. A megoldás háttérrendszere végén, egyszer naponta, a 14-KB eszköz iker olvas, és frissíti azt konfigurációk módosítása 512 bájtos Payload van jelen.

Az eszköz használ:

* 25 (100 KB/4 KB-os) üzenetek * 24 órát eszköz a felhőbe küldött üzeneteket.
* Két üzenet (1 KB/0,5 KB) * hat napi időpontot az eszközfrissítésekhez iker.

A számítás 612 üzenet naponta összesen biztosít.

A megoldás háttérrendszeréhez 29 üzenetek összesen 28 üzenetek (14 KB/0,5 KB) az eszköz iker olvasására, valamint frissíti, egy üzenet használ fel.

Az összes, az eszköz és a megoldás háttérrendszeréhez 641 üzenet naponta felhasználását.


[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-message-size]: iot-hub-devguide-messages-construct.md
