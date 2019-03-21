---
title: Használja az InSpec megfelelőségi automatizálása az Azure-infrastruktúra
description: Megtudhatja, hogyan észlelheti a problémákat az Azure-környezetek InSpec használatával
keywords: az Azure, a chef, a devops, a virtuális gépek, a áttekintése, automatizálás, inspec
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: bdfa30b48c79a8910d503bb9e54a42c30e5adba6
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58293923"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Használja az InSpec megfelelőségi automatizálása az Azure-infrastruktúra

[InSpec](https://www.chef.io/inspec/) Chef's nyílt forráskódú nyelv a biztonsági és megfelelőségi szabályok szoftverfejlesztő, a műveletek és a biztonsági mérnökökből között megosztható leíró. InSpec működik, az infrastruktúra aktuális állapotával együtt a kívánt állapotban, amely könnyen olvasható és könnyen írási InSpec kódban, express összehasonlításával. InSpec szabálysértések észleli és jelentések formájában jeleníti meg csapatával az eredményeket, de helyezi, szervizelés irányítását.

Használhatja az InSpec erőforrásra és erőforráscsoportra található egy előfizetésben, beleértve a virtuális gépek, a hálózati konfigurációk, az Azure Active Directory-beállítások és további állapotának ellenőrzéséhez.

Ez a cikk ismerteti, hogy egyszerűbb biztonság és megfelelőség az Azure-ban InSpec használatának előnyei.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Győződjön meg arról, megfelelőségi így könnyen megismerhető és értékelése

Megfelelőségi dokumentáció számolótáblákban vagy a Word-dokumentumok írása követelmények nyitva hagyja értelmezést. Az InSpec alakítsa át a rendszerverzióval ellátott, végrehajtható, emberek számára olvasható kód követelményeket. Kód beszélgetések kapcsolatos mit kell értékelni egyértelmű leképezés a képzés résztvevői hasznos képességekkel tesztek értéke váltja fel.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Flotta kiterjedő a hibák észlelése és azok javítási rangsorolása

Ügynök nélküli kivételfigyelés által az InSpec észlelési üzemmód segítségével mérheti fel gyorsan – nagy mennyiségű - a kitettség szint. Hatás/súlyossági pontozó beépített metaadatok alapján határozható meg fókusz, milyen területeken a szervizelési. Olyan szabályok írhatók gyorsan új biztonsági réseket, és a szabályzat adott válaszként, és azokat azonnal megkezdik is.

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>Vendég Szabályzatkonfiguráció-beli virtuális gépek naplózása

Az Azure közvetlenül támogatja a Chef InSpec definíciók naplózása keresztül az Azure virtual machines használatának [Azure Vendég Szabályzatkonfiguráció](/azure/governance/policy/concepts/guest-configuration). Vendég konfigurációs egy Linuxos virtuális gép egy megadott Chef InSpec definíció- és jelentések megfelelőség vissza az Azure Policy kiértékeli. Ezek a naplók eredményeit is jelentett keresztül az Azure Monitor naplóira; riasztások és a más automation-forgatókönyvek engedélyezése.

## <a name="satisfy-audits"></a>Felel meg a naplók

Az InSpec reagálhat kérdések naplózása bármikor - nemcsak, például a negyedéves és éves előre meghatározott időközönként. A folyamatosan futó InSpec teszteket, egy a pontos megfelelőségi állapotáról és az előzmények auditált meg ahelyett, hogy hatóságának felügyelete alatt áll egy auditor megállapításokat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Próbálja ki az Azure Cloud shellben InSpec](https://shell.azure.com)