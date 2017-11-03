---
title: "Azure IoT Hub árképzési megértése |} Microsoft Docs"
description: "Fejlesztői útmutató - hogyan mérési és árképzési működik, beleértve az IoT-központ dolgozott példák kapcsolatos információkat."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 1ac90923-1edf-4134-bbd4-77fee9b68d24
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2017
ms.author: elioda
ms.openlocfilehash: 05006a78cc7d82bc048ec5706465f7140eb40e94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-iot-hub-pricing-information"></a>Díjszabási információkért Azure IoT Hub

[Az Azure IoT Hub árképzési] [ lnk-pricing] különböző Termékváltozatai és az IoT-központ díjszabása az általános információkat nyújt. A cikkben megtudhatja, hogyan a különböző IoT-központ funkciók mérése üzenetekként IoT-központ által a további részletek.

## <a name="charges-per-operation"></a>Művelet díjak

| Művelet | Számlázási adatokat | 
| --------- | ------------------- |
| Identitásjegyzék műveletei <br/> (létrehozása, beolvasása, listázása, frissítése és törlése) | Nem számítjuk fel. |
| Az eszközről a felhőbe irányuló üzenetek | Sikeresen elküldött üzenetek van szó, a 4 KB-os adattömbök érkező az IoT hubhoz például 6 KB-os üzenet díjfizetéssel 2 üzenet. |
| Felhő-eszközre küldött üzenetek | Sikeresen elküldött üzenetek van szó, a 4 KB-os adattömböket, például egy 6 KB-os üzenet díjfizetéssel 2 üzenet. |
| Fájlfeltöltések | Azure Storage fájlátvitel nem forgalmi díjas IoT hubból. Átviteli kezdeményezés és befejezési üzeneteit van szó, mint 4 KB-os lépésekben mért messaged. Például egy 10 MB-os fájlban fel van töltve az Azure Storage költség mellett két üzenet. |
| Közvetlen metódusok | Sikeres metóduskérelmek van szó, a 4 KB-os adattömböket, nem üres azokkal válaszait van szó, a 4 KB-os további üzeneteihez. Kérelmek leválasztott eszközökre van szó, a 4 KB-os adattömbök üzeneteihez. Például egy metódust, amely az eszköz válaszára nem szervezethez eredményez 6 KB-os szervezethez díjfizetéssel két üzenetekként; egy metódust, amely az eszközről egy 1 KB-os válasz eredményez 6 KB-os szervezethez fel van töltve, a kérelem két üzenetről és a válasz egy másik üzenetet. |
| Ikereszköz-olvasások | Eszköz iker beolvassa az eszköz és a megoldás háttérrendszere end van szó, mint 512 bájtos adattömbök üzenetek. Például egy 6 KB-os eszköz iker olvasása díjfizetéssel 12 üzeneteihez. |
| A két eszközfrissítésekhez (címkék és tulajdonságait) | Kettős eszközfrissítésekhez az eszközről, és a megoldás háttérből van szó, az 512 bájtos adattömbök üzeneteihez. Például egy 6 KB-os eszköz iker olvasása díjfizetéssel 12 üzeneteihez. |
| Eszköz iker lekérdezések | Lekérdezések van szó, attól függően, hogy az eredmény méretének az 512 bájtos adattömbök üzeneteihez. |
| Feladatműveletek <br/> (létrehozás, frissítés, listázás, törlés) | Nem számítjuk fel. |
| Feladatok eszközönkénti műveletek | Feladatok műveletek (például a kettős eszközfrissítésekhez, és módszerek) normál van szó. Például egy 1 KB-os kérelmek és üres-törzsének válaszokban 1000 metódushívások eredményezve feladat díjfizetéssel 1000 üzeneteket. |

> [!NOTE]
> Különböző méretű arra az esetre vonatkoznak annak eldöntéséhez, hogy a terhelés méretének bájtban (protokoll keretezési van figyelmen kívül hagyva). Üzenetek (melyek tulajdonságait és a szervezet) esetén a méret számított protokoll-független módon leírtak szerint a [IoT Hub fejlesztői útmutató üzenetküldési][lnk-message-size].

## <a name="example-1"></a>#1. példa

Egy eszköz küld az IoT-központ, amely majd beolvassa az Azure Stream Analytics egy 1 KB-os eszközről a felhőbe üzenet / perc. A megoldás háttérrendszeréhez hív meg (a 512 bájtos tartalom) metódus az eszközön 10 percenként egy bizonyos művelet elindítása. Az eszköz válaszol-e a metódus eredménye 200 bájt.

Az eszköz akkor 1 üzenetet * 60 perc * 24 óra = 1440 üzenet eszközről a felhőbe üzenetek naponta és 2 kérés és válasz * 6 alkalommal óránként * 24 óra = 288 üzenetek a módszer naponta 1728 üzenetek összesen.

## <a name="example-2"></a>#2. példa

Egy eszköz óránként egy 100 KB-os eszközről a felhőbe üzenetet küld. Azt is frissíti az eszköz kettős 1 KB-os hasznos adat található 4 óránként. A megoldás háttérrendszere végén, egyszer naponta, a 14-KB eszköz iker olvas, és frissíti azt konfigurációk módosítása 512 bájtos Payload van jelen.

Az eszköz akkor 25 (100KB/4 KB-os) üzenetek * 24 órát eszközről a felhőbe üzenetek, valamint 2 üzenet (1KB/0,5 KB) * 6 napi időpontot az eszközfrissítésekhez két, összesen 612 üzenet naponta.
A megoldás háttérrendszeréhez 28 üzenetek (14KB/0,5 KB) az eszköz iker olvasására, valamint 1 az üzenethez, és a frissítést, akkor 29 üzenetek összesen.

Az összes, az eszköz és a megoldás háttérrendszeréhez 641 üzenet naponta felhasználását.


[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-message-size]: iot-hub-devguide-messages-construct.md
