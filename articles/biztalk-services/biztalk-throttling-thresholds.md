---
title: "BizTalk szolgáltatások szabályozását megismerése |} Microsoft Docs"
description: "Ismerje meg a sávszélesség-szabályozás küszöbértékeit, és az amiatt végbemenő futtatási viselkedés BizTalk szolgáltatások. Sávszélesség-szabályozás a memóriahasználat és üzenetek száma alapul. MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: f6663cf2-cda4-4bac-855e-27d2ad5c4fa4
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 145e7470bbc01c676a1fb5856c0f9a8726e667fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="biztalk-services-throttling"></a>BizTalk Services: Szabályozás

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Az Azure BizTalk szolgáltatások megvalósítja szolgáltatás sávszélesség-szabályozás két feltételek alapján: a memóriahasználat és feldolgozási egyidejű üzenetek száma. Ez a témakör a szabályozási küszöbértékek, és működését ismerteti, ha sávszélesség-szabályozási állapot akkor fordul elő.

## <a name="throttling-thresholds"></a>Sávszélesség-szabályozási küszöbértékek
Az alábbi táblázat a sávszélesség-szabályozási forrás- és a küszöbértékek:

|  | Leírás | Alsó küszöbérték | Magas küszöbértéket |
| --- | --- | --- | --- |
| Memory (Memória) |%-a teljes rendszer memória rendelkezésre álló/PageFileBytes. <p><p>Teljes rendelkezésre álló PageFileBytes körülbelül 2 alkalommal a RAM Memóriát a rendszer. |60% |70% |
| Üzenet feldolgozása |Feldolgozott üzenetek száma |40 * magok száma |100 * magok száma |

A magas küszöbérték elérésekor Azure BizTalk szolgáltatások szabályozása kezdődik. Sávszélesség-szabályozás leállítja az alsó küszöbérték elérésekor. A szolgáltatás használata esetén például 65 % rendszermemória. Ebben a helyzetben a szolgáltatás nem szabályozás. A szolgáltatás elindul, a rendszer memória 70 %. Ebben a helyzetben a szolgáltatást azelőtt gyorsítja fel, és továbbra is fennáll, addig, amíg a szolgáltatás pedig 60 % (alsó küszöbérték) rendszermemória szabályozása.

Azure BizTalk szolgáltatások nyomon követi a sávszélesség-szabályozási állapota (normál állapotban, és a szabályozottan halmozott állapot) és a sávszélesség-szabályozási időtartama.

## <a name="runtime-behavior"></a>Futtatási viselkedés
BizTalk szolgáltatások Azure szabályozási állapotba kerül, amikor az alábbiak történnek:

* Sávszélesség-szabályozás egy szerepkör-példány van. Példa:<br/>
  A szabályozás RoleInstanceA. RoleInstanceB nem a szabályozás. Ebben a helyzetben a RoleInstanceB üzenetek feldolgozása várt módon. RoleInstanceA üzeneteket a rendszer törli, és a következő hiba miatt sikertelen:<br/><br/>
  **Kiszolgáló túlterhelt. Próbálkozzon újra.**<br/><br/>
* Egyetlen lekéréses forrás ne kérdezze le, és töltse le az üzenetet. Példa:<br/>
  Egy folyamat üzenetek FTP külső forrásból kéri le. A szerepkörpéldányt, ennek során a lekéréses szabályozási állapotának beolvasása. Ebben a helyzetben a folyamat leállítása letöltése a további üzeneteket, amíg a szerepkörpéldányt leállítja a sávszélesség-szabályozás.
* Választ küldött az ügyfélnek, így az ügyfél is újra elküldeni az üzenetet.
* Meg kell várnia, amíg a sávszélesség-szabályozás nem oldódik. Pontosabban meg kell várnia az alsó küszöbérték elérésekor.

## <a name="important-notes"></a>Fontos megjegyzések
* Sávszélesség-szabályozás nem tiltható le.
* Sávszélesség-szabályozás küszöbértékek nem módosíthatók.
* Sávszélesség-szabályozás rendszerszintű valósul meg.
* Az Azure SQL adatbázis-kiszolgáló is rendelkezik beépített szabályozás.

## <a name="additional-azure-biztalk-services-topics"></a>További Azure BizTalk szolgáltatások kapcsolatos témakörök
* [Az Azure BizTalk szolgáltatások SDK telepítése](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Oktatóanyag: Azure BizTalk szolgáltatások](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Hogyan kezdhetem el az Azure BizTalk Services SDK használatát](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Az Azure BizTalk szolgáltatások](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Lásd még:
* [BizTalk szolgáltatások: Fejlesztői, Basic, Standard és prémium kiadás diagram](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk szolgáltatások: Kiépítés használata Azure klasszikus portál](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [BizTalk Services: Kiépítési állapot diagramja](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk Services: Irányítópult, Figyelés és Méret lapok](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk Services: Biztonsági mentés és visszaállítás](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk Services: Kiállító neve és kiállító kulcsa](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>

