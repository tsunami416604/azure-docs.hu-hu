---
title: Az Azure Security Benchmark áttekintése
description: Biztonsági teljesítményteszt – áttekintés
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: c16d7247b781fea04cfa2d53b8854cff14e039c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616450"
---
# <a name="overview-of-azure-security-baselines"></a>Az Azure biztonsági alapkonfigurációinak áttekintése

Az Azure Biztonsági alapkonfigurációk továbbfejlesztett eszközekkel, nyomon követéssel és biztonsági funkciókkal segítik termékeink biztonságát, és egységes élményt nyújtanak a környezet védelme során.

Az Azure Service Security Baselines a felhőközpontú vezérlőterületekre összpontosít. Ezek a vezérlők összhangban vannak a jól ismert biztonsági referenciaértékekkel, például az Internetbiztonsági Központ (CIS) által leírt vezérlőkkel. Alapterveink útmutatást nyújtanak az [Azure Security Benchmark](overview.md)ban felsorolt vezérlőterületekhez.

Minden ajánlás a következő információkat tartalmazza:
- **Azure-azonosító:** Az Azure Security Benchmark-azonosító, amely megfelel a javaslatnak.
- **Javaslat:** Közvetlenül az Azure-azonosító után a javaslat a vezérlő magas szintű leírását.
- **Útmutató**: Az ajánlás indoklása és a végrehajtásra vonatkozó útmutatásra mutató hivatkozások. Ha a javaslatot az Azure Security Center támogatja, ezek az információk is megjelennek.
- **Felelősség**: Ki felelős az ellenőrzés végrehajtásáért. A lehetséges forgatókönyvek az ügyfelek felelőssége, a Microsoft felelőssége vagy a megosztott felelősség.
- **Az Azure Security Center figyelése:** Függetlenül attól, hogy a vezérlő figyeli az Azure Security Center, hivatkozással.

Minden javaslat, beleértve az adott szolgáltatásra nem alkalmazható javaslatokat is, szerepel nek az alaptervben, hogy teljes képet nyújtson az Azure Biztonsági teljesítményteszt az egyes szolgáltatásokhoz való viszonyáról. Előfordulhat, hogy a vezérlők, amelyek nem alkalmazhatók különböző okok miatt, például az IaaS/compute-központú vezérlők (például az operációs rendszer konfigurációjának kezelése) nem alkalmazható a PaaS-szolgáltatások.
