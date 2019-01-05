---
title: Használja az InSpec megfelelőségi automatizálása az Azure-infrastruktúra
description: Megtudhatja, hogyan észlelheti a problémákat az Azure-környezetek InSpec használatával
keywords: az Azure, a chef, a devops, a virtuális gépek, a áttekintése, automatizálás, inspce
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 9256a4daf6564761553b495e559805a46e4eae32
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050727"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Használja az InSpec megfelelőségi automatizálása az Azure-infrastruktúra
[InSpec](https://www.chef.io/inspec/) teszteléséhez és a naplózás az alkalmazások és infrastruktúra ingyenes és nyílt forráskódú keretrendszer. InSpec működik, a rendszer aktuális állapotával együtt a kívánt állapotban, amely könnyen olvasható és könnyen írási InSpec kódban, express összehasonlításával. InSpec szabálysértések észleli és jelentések formájában jeleníti meg csapatával az eredményeket, de helyezi, szervizelés irányítását. InSpec használhatja az Azure-ban futó virtuális gépek állapotának ellenőrzéséhez. Használhatja a InSpec vizsgálata, és erőforrásra és erőforráscsoportra belül egy előfizetés állapotának ellenőrzéséhez.

Ez a cikk ismerteti, hogy egyszerűbb biztonság és megfelelőség az Azure-ban InSpec használatának előnyei.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Győződjön meg arról, megfelelőségi így könnyen megismerhető és értékelése
Az InSpec alakítsa át a rendszerverzióval ellátott, végrehajtható, emberek számára olvasható kód követelményeket. Ez lehetővé teszi, hogy a tesztek szervezheti algyűjteményeinek összefüggő profilok, ahol meghatározása és testre szabhatja a kivételek, igény szerint.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Flotta kiterjedő a hibák észlelése és azok javítási rangsorolása
Ügynök nélküli kivételfigyelés InSpec észlelési üzemmód lehetővé teszi segítségével mérheti fel gyorsan – nagy mennyiségű - a kitettség szintjét. Hatás/súlyossági pontozó beépített metaadatok alapján határozható meg fókusz, milyen területeken a szervizelési.

## <a name="inspect-machines-data-and-new-saas-apis"></a>Gépek, az adatok és az új SaaS API-k vizsgálata
Győződjön meg arról, durva és a részletes információ a felhő megfelelőségi és a jelentés azt a helyességi feltételek folyamatosan az InSpec felhőalapú API megfelelőségi képességek segítségével.

## <a name="satisfy-audits"></a>Felel meg a naplók
Az InSpec reagálhat kérdések naplózása bármikor - nemcsak, például a negyedéves és éves előre meghatározott időközönként. InSpec lehetővé teszi egy auditált, a pontos megfelelőségi állapotáról, ahelyett hogy hatóságának felügyelete alatt áll egy auditor megállapításokat, hogy adja meg.

## <a name="reduce-ambiguity-and-miscommunication-regarding-rules"></a>Csökkentheti a félreérthetőség és miscommunication vonatkozó szabályok
Dokumentumok hagyja konfigurációkat, és értelmezése nyissa meg a folyamatokat. Végrehajtható kód eltávolítja a beszélgetések kapcsolatos mi értéke egyértelmű leképezés a képzés résztvevői hasznos képességekkel teszteket kell értékelni.

## <a name="keep-up-with-rapidly-changing-threat-and-compliance-landscapes"></a>Legyen naprakész a gyorsan változó fenyegetések és a megfelelőség környezetünk
InSpec lehetővé teszi az írási és észlelési kód közzététele ugyanarra a napra, és írja be az új szabályok új szabályzat rövid válaszidő. Ez azt jelenti, hogy a változások a fenyegetések vagy a szabályzat már nem egyenlő tudjon a vészhelyzetekre.

## <a name="next-steps"></a>További lépések
* [Windows virtuális gép létrehozása Chef használatával Azure-ban](/azure/virtual-machines/windows/chef-automation)