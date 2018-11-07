---
title: További információ a BizTalk Services szabályozás |} A Microsoft Docs
description: Ismerje meg a szabályozási küszöbeivel, és az amiatt végbemenő futásidejű viselkedéseket a BizTalk Services. Szabályozás a memóriahasználat és az üzenetek száma alapul. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: f6663cf2-cda4-4bac-855e-27d2ad5c4fa4
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: daab61a0ea9321b0fb918c60688215c80088e0bc
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51243351"
---
# <a name="biztalk-services-throttling"></a>BizTalk Services: Szabályozás

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Az Azure BizTalk Services megvalósítja a társzolgáltatás szabályozása két feltételek alapján: memóriahasználat és az egyidejű-üzenetek feldolgozási száma. Ez a témakör a szabályozási határértékek és ismerteti a működését, ha sávszélesség-szabályozási állapot akkor fordul elő.

## <a name="throttling-thresholds"></a>Szabályozási Küszöbeivel
Az alábbi táblázat a sávszélesség-szabályozási forrás és a küszöbértékek:

|  | Leírás | Alsó küszöbérték | Magas küszöbértéket |
| --- | --- | --- | --- |
| Memory (Memória) |%-a teljes rendszer memória rendelkezésre álló/PageFileBytes. <p><p>Teljes rendelkezésre álló PageFileBytes körülbelül 2 alkalommal a memória, a rendszer. |60% |70% |
| Üzenetfeldolgozás |Feldolgozott üzenetek száma |40 * magok száma |100 * magok száma |

Magas küszöbérték elérése esetén az Azure BizTalk Services elindítja szabályozását. Szabályozás leáll, az alsó küszöbérték elérésekor. A szolgáltatás használata esetén például 65 %-os rendszer memóriáját. Ebben a helyzetben a szolgáltatás nincs szabályozás. A szolgáltatás elindul a 70 %-os rendszer memória használata. Ebben a helyzetben a szolgáltatás szabályozza, és továbbra is fennáll, addig, amíg a szolgáltatás 60 %-os (alsó küszöbérték) rendszermemória használ szabályozását.

Az Azure BizTalk Services nyomon követi a sávszélesség-szabályozási állapot (normál állapotban vagy szabályozott állapotban) és a sávszélesség-szabályozási időtartama.

## <a name="runtime-behavior"></a>Működését
Amikor az Azure BizTalk Services szabályozási állapotba kerül, az alábbiak történnek:

* Egy szerepkör-példány ilyen szabályozás van. Példa:<br/>
  RoleInstanceA szabályozza. RoleInstanceB nem szabályozza. Ebben a helyzetben a RoleInstanceB üzenetek feldolgozása elvárt módon. Üzenetek RoleInstanceA elvesznek, és a következő hiba miatt sikertelen:<br/><br/>
  **A kiszolgáló elfoglalt. Próbálkozzon újra.**<br/><br/>
* Egyetlen lekéréses forrás ne lekérdezni, és töltse le az üzenetet. Példa:<br/>
  Egy folyamatot egy külső forrásból FTP lekéri az üzeneteket. A szerepkörpéldány ennek során a lekéréses szabályozási állapotának beolvasása. Ebben a helyzetben a folyamat leáll, letölti a további üzeneteket, mindaddig, amíg a szerepkörpéldány leállítja a szabályozás.
* A válasz elküldhetők az ügyfélprogramnak, így az ügyfél az üzenet újból elküldheti.
* Meg kell várnia, amíg az fel lett oldva. Pontosabban meg kell várnia az alsó küszöbérték elérésekor.

## <a name="important-notes"></a>Fontos megjegyzések
* Nem lehet letiltani a szabályozás.
* Nem lehet módosítani a szabályozási küszöbeivel.
* Szabályozás a rendszerre van megvalósítva.
* Az Azure SQL Database-kiszolgáló is rendelkezik beépített szabályozás.

## <a name="additional-azure-biztalk-services-topics"></a>További Azure BizTalk Services kapcsolatos témakörök
* [Az Azure BizTalk Services SDK telepítése](https://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Az oktatóanyagok: Az Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Hogyan kezdhetem el az Azure BizTalk Services SDK használatát](https://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Az Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Lásd még:
* [A BizTalk Services: Fejlesztői, alapszintű, Standard és prémium kiadások diagramja](https://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk Services: Kiépítési állapot diagramja](https://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk Services: Irányítópult, Figyelés és Méret lapok](https://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk Services: Biztonsági mentés és visszaállítás](https://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk Services: Kiállító neve és kiállító kulcsa](https://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>

