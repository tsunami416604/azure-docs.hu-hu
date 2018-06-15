---
title: Engedélyezett állapotot váltani vagy a BizTalk szolgáltatások állapotok feladatok |} Microsoft Docs
description: 'A különböző MABS állapotú engedélyezett műveletek/műveletek: állítsa le, indítsa el, indítsa újra, felfüggesztése, folytatása, törlése, méretezhető, frissítheti a konfigurációs és biztonsági mentése'
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: aea738f3-ec76-4099-a41b-e17fea9e252f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2016
ms.author: mandia
ms.openlocfilehash: 05470e75fc7b46603c8fce3a98c66ac6a24758a8
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2017
ms.locfileid: "24102742"
---
# <a name="what-you-can-and-cant-do-using-the-biztalk-service-state"></a>Lehet, illetve mit nem használja a BizTalk szolgáltatás állapota

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Attól függően, hogy a BizTalk szolgáltatás aktuális állapotát, és a BizTalk szolgáltatás nem hajtható végre műveletek vannak.

Például egy új BizTalk szolgáltatás kiépítése. Amikor befejeződik, a BizTalk szolgáltatás van `active` állapotát. Az aktív állapotú leállítása, felfüggesztése és a BizTalk szolgáltatás törléséhez. Ha a BizTalk szolgáltatás leállítása és leállítása sikertelen lesz, majd a BizTalk szolgáltatás a egy `StopFailed` állapotát. Az a `StopFailed` állapotba kerül, a BizTalk szolgáltatásokat. A következő hiba akkor fordul elő, ha egy művelet nem engedélyezett, folytatása, például:

`Operation not allowed`

## <a name="view-the-possible-states"></a>A lehetséges állapotok megtekintése

A következő táblázat a műveletek vagy a műveletek is létrehozhatók, ha a BizTalk szolgáltatás egy adott állapotban. Egy ✔ azt jelenti, hogy a művelet engedélyezett az adott állapotban. Egy üres bejegyzést azt jelenti, hogy a művelet nem hajtható végre az adott állapotban.

| Szolgáltatás állapota | Indítás | Leállítás | Újraindítás | Felfüggesztése | Folytatás | Törlés | Méretezés | Frissítés <br/> Konfiguráció | Biztonsági mentés |
| --- | --- | --- | --- | --- | --- | --- |--- | --- | --- |
| Aktív |  | ✔ | ✔ | ✔ |  | ✔ |✔ |✔ |✔ |
| Letiltva |  |  |  |  |  | ✔ | |  |  | 
| Felfüggesztve |  |  |  |  | ✔ | ✔ | |  | ✔ |
| Leállítva | ✔ |  | ✔ |  |  | ✔ | |  | ✔ |
| Szolgáltatás frissítése sikertelen |  |  |  |  |  | ✔ | |  |  | 
| DisableFailed |  |  |  |  |  | ✔ | |  |  | 
| EnableFailed |  |  |  |  |  | ✔ | |  |  | 
| StartFailed <br/> StopFailed <br/> RestartFailed | ✔ | ✔ | ✔ |  |  | ✔ | | ✔ | |
| SuspendedFailed <br/> ResumeFailed|  |  |  | ✔ | ✔ | ✔ | |  |  | 
| CreatedFailed <br/> RestoreFailed |  |  |  |  |  | ✔ | |  |  | 
| ConfigUpdateFailed  |  |  | ✔ |  |  | ✔ | |✔ | |
| ScaleFailed |  |  |  |  |  | ✔ |✔ | |  |  | 



## <a name="see-also"></a>Lásd még:
* [Teendők, az irányítópult, a figyelő és a skála lapon a BizTalk szolgáltatások](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk szolgáltatások fejlesztői, Basic, Standard és prémium verzióval beolvasása](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [Készítsen biztonsági másolatot, és a BizTalk szolgáltatás visszaállítása](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [Sávszélesség-szabályozás, tekintse meg a BizTalk szolgáltatások](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
* [A Service Bus- és hozzáférés-vezérlés kibocsátó neve és a kiállító értékek beolvasása a BizTalk szolgáltatás](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
* [Hogyan kezdhetem el az Azure BizTalk Services SDK használatát](http://go.microsoft.com/fwlink/p/?LinkID=302335)

