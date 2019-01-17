---
title: Használja az InSpec megfelelőségi automatizálása az Azure-infrastruktúra
description: Megtudhatja, hogyan észlelheti a problémákat az Azure-környezetek InSpec használatával
keywords: az Azure, a chef, a devops, a virtuális gépek, a áttekintése, automatizálás, inspec
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: a5de2ca04a193f97a2a65a043f744abb8e0ea758
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359221"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Használja az InSpec megfelelőségi automatizálása az Azure-infrastruktúra
[InSpec](https://www.chef.io/inspec/) Chef's nyílt forráskódú nyelv a biztonsági és megfelelőségi szabályok szoftverfejlesztő, a műveletek és a biztonsági mérnökökből között megosztható leíró. InSpec működik, az infrastruktúra aktuális állapotával együtt a kívánt állapotban, amely könnyen olvasható és könnyen írási InSpec kódban, express összehasonlításával. InSpec szabálysértések észleli és jelentések formájában jeleníti meg csapatával az eredményeket, de helyezi, szervizelés irányítását.

Használhatja az InSpec erőforrásra és erőforráscsoportra alapjait az előfizetést, beleértve a virtuális gépek, a hálózati konfigurációk, az Azure Active Directory-beállítások és további állapotának ellenőrzéséhez.

Ez a cikk ismerteti, hogy egyszerűbb biztonság és megfelelőség az Azure-ban InSpec használatának előnyei.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Győződjön meg arról, megfelelőségi így könnyen megismerhető és értékelése
Megfelelőségi dokumentáció számolótáblákban vagy a Word-dokumentumok írása hagyja értelmezése nyílt követelményeinek. Az InSpec alakítsa át a rendszerverzióval ellátott, végrehajtható, emberek számára olvasható kód követelményeket. Kód beszélgetések kapcsolatos mit kell értékelni egyértelmű leképezés a képzés résztvevői hasznos képességekkel tesztek értéke váltja fel.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Flotta kiterjedő a hibák észlelése és azok javítási rangsorolása
Ügynök nélküli kivételfigyelés által az InSpec észlelési üzemmód segítségével mérheti fel gyorsan – nagy mennyiségű - a kitettség szint. Hatás/súlyossági pontozó beépített metaadatok alapján határozható meg fókusz, milyen területeken a szervizelési. Olyan szabályok írhatók gyorsan új biztonsági réseket, és a szabályzat adott válaszként, és azokat azonnal megkezdik is.

## <a name="satisfy-audits"></a>Felel meg a naplók
Az InSpec reagálhat kérdések naplózása bármikor - nemcsak, például a negyedéves és éves előre meghatározott időközönként. A folyamatosan futó InSpec teszteket, egy a pontos megfelelőségi állapotáról és az előzmények auditált meg ahelyett, hogy hatóságának felügyelete alatt áll egy auditor megállapításokat.

## <a name="next-steps"></a>További lépések

* Próbálja meg az Azure Cloud shellben InSpec [ ![a Cloud Shell indítása](https://shell.azure.com/images/launchcloudshell.png "a Cloud Shell indítása")](https://shell.azure.com)
