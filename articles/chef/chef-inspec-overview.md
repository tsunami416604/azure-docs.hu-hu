---
title: A megfelelőségi automatizálásához az Azure infrastruktúra InSpec használata
description: Az Azure-környezetekhez azonosíthatók a problémák InSpec használata
keywords: Azure, a chef, a devops, a virtuális gépek, a áttekintése, automatizálhatja, inspce
ms.service: virtual-machines-linux
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 4193b7fdb3932cbffa2b56b5d7eee6f3b573bd99
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260070"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>A megfelelőségi automatizálásához az Azure infrastruktúra InSpec használata
[InSpec](https://www.chef.io/inspec/) szabad és nyílt forráskódú keretrendszere, amely a vizsgálat és naplózás az alkalmazásaikat és infrastruktúrájukat. A rendszer a kívánt állapot, amely kifejezi könnyen olvasási és írási könnyen InSpec kódban az aktuális állapotával összehasonlításával inSpec működik. InSpec megsértésének felismeri és megállapítások jelentés formájában megjeleníti, de helyezi, szervizelés irányítását. InSpec használhatja az Azure-ban futó virtuális gépek állapotának ellenőrzése. InSpec segítségével is vizsgálati és erőforrások és erőforráscsoportok belül előfizetés állapotát ellenőrzi.

Ez a cikk ismerteti a megkönnyítése biztonsági és megfelelőségi Azure InSpec használatának előnyeit.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Megfelelőségi könnyedén ismertetése és értékelése
A InSpec alakítsa át a követelmények a rendszerverzióval ellátott, végrehajtható, emberek számára olvasható kódra. Ez lehetővé teszi, hogy a vizsgálatok rendezze összeállítható profilok, ahol határozza meg, és igény szerint testre szabhatja a kivételeket.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Járműflotta szintű problémákat észleli, és a szervizelés rangsorolása
Ügynök nélküli InSpec észlelési üzemmód lehetővé teszi a szintje - léptékű - gyors felméréséhez. Beépített metaadatok hatás/súlyossági pontozó megállapíthatja, milyen területeken összpontosíthat a szervizelés a.

## <a name="inspect-machines-data-and-new-saas-apis"></a>Vizsgálja meg a gépeket, adatokat és új SaaS API-k
A InSpec felhőalapú API megfelelőségi képességek lehetővé teszik, hogy folyamatosan ellenőrizze durva és a minden részletre kiterjedő attribútumaival kapcsolatos igények, a felhő megfelelőségi és a jelentést.

## <a name="satisfy-audits"></a>Naplózás kielégítéséhez.
A InSpec reagálhat naplózandó kérdések bármikor - nemcsak, például a negyedéves és éves előre meghatározott időközönként. InSpec lehetővé teszi egy naplózási ciklus ismerete, a pontos megfelelőségi előírások alatt hatóságának felügyelete alatt áll egy auditor megállapítások helyett.

## <a name="reduce-ambiguity-and-miscommunication-regarding-rules"></a>Csökkentse a félreérthetőség és miscommunication vonatkozó szabályok
Dokumentumok konfigurációk hagyja, és értelmezési nyissa meg a folyamatok. Végrehajtható kód mi anyagi tesztek tiszta biztonsági mentés helyett kell értékelni kapcsolatos témakörök eltávolítja.

## <a name="keep-up-with-rapidly-changing-threat-and-compliance-landscapes"></a>A sűrűn módosuló fenyegetés és megfelelőségi tájak tartani
InSpec lehetővé teszi írási és észlelési kód közzététele ugyanarra a napra esnek, és új szabályok írása az új szabályzat gyors válasz. Ez azt jelenti, hogy a fenyegetések vagy más szabályozást változásai nem egyenlő-e a vészhelyzetek.

## <a name="next-steps"></a>További lépések
* [Windows virtuális gép létrehozása Azure-ban Chef](/azure/virtual-machines/windows/chef-automation)