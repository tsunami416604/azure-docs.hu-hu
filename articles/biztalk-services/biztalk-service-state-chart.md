---
title: Engedélyezett állapotot, vagy a BizTalk szolgáltatások állapota a feladatok |} A Microsoft Docs
description: 'A műveletek/műveletek különböző MABS állapot engedélyezett: állítsa le, indítsa el, indítsa újra, felfüggesztése, folytatása, törlése, méretezhető, frissítse a konfigurációs és biztonsági mentése'
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
ms.openlocfilehash: bbe1288a42db307001ac778394ac410206f1df21
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228198"
---
# <a name="what-you-can-and-cant-do-using-the-biztalk-service-state"></a>Hogy és mit nem hajtható végre, használja a BizTalk-szolgáltatás állapota

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

A BizTalk-szolgáltatás aktuális állapotát, attól függően számos műveleteket, lehet, és a BizTalk-szolgáltatás nem hajtható végre.

Ha például üzembe helyezi egy új BizTalk szolgáltatást. Ha sikeresen befejeződik, a BizTalk-szolgáltatás van `active` állapota. Aktív állapotban állítsa le, felfüggesztése és a BizTalk-szolgáltatás törlése. A BizTalk szolgáltatás leállítása és a Leállítás meghiúsul, akkor kerül a BizTalk-szolgáltatás egy `StopFailed` állapota. Az a `StopFailed` állapotba, újraindíthatja a BizTalk szolgáltatást. Ha egy művelet nem engedélyezett, folytatása, például a következő hiba jelenik meg:

`Operation not allowed`

## <a name="view-the-possible-states"></a>A lehetséges állapotok megtekintése

Az alábbi táblázatok tartalmazzák a műveletek vagy műveleteket, amelyeket végezhető, ha a BizTalk-szolgáltatás egy meghatározott állapotban van. Egy ✔ azt jelenti, hogy a művelet adott állapotban engedélyezett. Egy üres bejegyzés azt jelenti, hogy a művelet nem hajtható végre, az adott állapotot.

| Szolgáltatás állapota | Indítás | Leállítás | Újraindítás | Felfüggesztés | Folytatás | Törlés | Méretezés | Frissítés <br/> Konfiguráció | Backup |
| --- | --- | --- | --- | --- | --- | --- |--- | --- | --- |
| Aktív |  | ✔ | ✔ | ✔ |  | ✔ |✔ |✔ |✔ |
| Letiltva |  |  |  |  |  | ✔ | |  |  | 
| Felfüggesztve |  |  |  |  | ✔ | ✔ | |  | ✔ |
| Leállítva | ✔ |  | ✔ |  |  | ✔ | |  | ✔ |
| Szolgáltatás frissítése nem sikerült |  |  |  |  |  | ✔ | |  |  | 
| DisableFailed |  |  |  |  |  | ✔ | |  |  | 
| EnableFailed |  |  |  |  |  | ✔ | |  |  | 
| StartFailed <br/> StopFailed <br/> RestartFailed | ✔ | ✔ | ✔ |  |  | ✔ | | ✔ | |
| SuspendedFailed <br/> ResumeFailed|  |  |  | ✔ | ✔ | ✔ | |  |  | 
| CreatedFailed <br/> RestoreFailed |  |  |  |  |  | ✔ | |  |  | 
| ConfigUpdateFailed  |  |  | ✔ |  |  | ✔ | |✔ | |
| ScaleFailed |  |  |  |  |  | ✔ |✔ | |  |  | 



## <a name="see-also"></a>Lásd még:
* [Mit tehet a BizTalk Services az irányítópult, figyelés és méret lapok](https://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [A BizTalk Services fejlesztői, alapszintű, Standard és prémium szintű kiadás beolvasása](https://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [Készítsen biztonsági másolatot, és a BizTalk-szolgáltatás visszaállítása](https://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [Szabályozás a BizTalk Services ismertetése](https://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
* [A Service Bus- és hozzáférés-vezérlés kiállító neve és kiállító kulcsérték lekéréséhez a BizTalk szolgáltatás](https://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
* [Hogyan kezdhetem el az Azure BizTalk Services SDK használatát](https://go.microsoft.com/fwlink/p/?LinkID=302335)

