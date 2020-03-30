---
title: Az Azure-függvények tervezése azonos bevitelhez
description: Az Azure-függvények létrehozása idempotens
author: craigshoemaker
ms.author: cshoe
ms.date: 9/12/2019
ms.topic: article
ms.openlocfilehash: 15af60ac5a862e6fb20e65ba6fbb92482420b7c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226861"
---
# <a name="designing-azure-functions-for-identical-input"></a>Az Azure-függvények tervezése azonos bevitelhez

Az eseményvezérelt és üzenetalapú architektúra valósága azt diktálja, hogy azonos kéréseket kell elfogadni, miközben meg kell őrizni az adatok integritását és a rendszer stabilitását.

Ennek szemléltetésére fontolja meg a lifthívás gombját. Ahogy megnyomja a gombot, világít, és egy liftet küld a padlóra. Néhány pillanattal később valaki más is csatlakozik hozzád az előcsarnokban. Ez a személy mosolyog rád, és megnyomja a megvilágított gombot másodszor. Visszamosolyogsz, és kuncogsz magadban, ahogy emlékeztetnek arra, hogy a lift hívásának parancsa idempotens.

Ha a lift hívógombjának másodperces, harmadik vagy negyedik alkalommal megnyomja a gombot, az nem befolyásolja a végeredményt. Amikor megnyomja a gombot, függetlenül attól, hogy hányszor, a lift küldött a padlóra. Idempotent rendszerek, mint a lift, eredményeként ugyanazt az eredményt nem számít, hányszor azonos parancsokat adnak ki.

Amikor alkalmazások létrehozásáról van szó, vegye figyelembe a következő forgatókönyveket:

- Mi történik, ha a készletellenőrző alkalmazás többször is megpróbálja törölni ugyanazt a terméket?
- Hogyan viselkedik az emberi erőforrás-alkalmazás, ha egynél több kérés van egy alkalmazotti rekord létrehozására ugyanannak a személynek?
- Hová kerül a pénz, ha a banki alkalmazás 100 kérést kap ugyanannak a kifizetésnek a megfordítására?

Számos olyan környezet létezik, ahol egy függvényre vonatkozó kérelmek azonos parancsokat kaphatnak. Egyes helyzetek a következők:

- Többször is ugyanazt a kérést küldő házirendek újrapróbálkozása
- Az alkalmazásnak visszajátszott gyorsítótárazott parancsok
- Alkalmazáshibák több azonos kérelem küldésével

Az adatok integritásának és a rendszer állapotának védelme érdekében az idempotens alkalmazás olyan logikát tartalmaz, amely a következő viselkedéseket tartalmazhatja:

- Az adatok létezésének ellenőrzése a törlés végrehajtása előtt
- Annak ellenőrzése, hogy léteznek-e már adatok létrehozási művelet végrehajtása előtt
- Az adatok végleges konzisztenciáját eredményező logika összeegyeztetése
- Egyidejűség-vezérlők
- Párhuzamosságok észlelése
- Adatfrissesség érvényesítése
- A bemeneti adatok ellenőrzésére szolgáló őrlogika

Végső soron idempotencia érhető el azáltal, hogy egy adott intézkedés lehetséges, és csak egyszer hajtják végre.
