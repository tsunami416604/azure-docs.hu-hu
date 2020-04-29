---
title: Az Azure biztonsági teljesítményteszt áttekintése
description: Biztonsági teljesítményteszt áttekintése
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: c16d7247b781fea04cfa2d53b8854cff14e039c7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77616450"
---
# <a name="overview-of-azure-security-baselines"></a>Az Azure biztonsági alaptervek áttekintése

Az Azure biztonsági alapkonfigurációi segítségével továbbfejlesztett eszközökkel, követéssel és biztonsági funkciókkal erősítheti termékeink biztonságát, és egységes környezetet biztosíthat a környezet biztonságossá tételéhez.

Az Azure szolgáltatás biztonsági alapkonfigurációi a felhő-központú vezérlési területekre összpontosítanak. Ezek a vezérlők összhangban vannak a jól ismert biztonsági összehasonlításokkal, például a Center for Internet Security (CIS) szolgáltatással. Az alaptervek útmutatást nyújtanak az [Azure biztonsági teljesítménytesztben](overview.md)felsorolt ellenőrzési területekhez.

Minden javaslat a következő információkat tartalmazza:
- **Azure ID**: a javaslatnak megfelelő Azure biztonsági TELJESÍTMÉNYTESZT-azonosító.
- **Javaslat**: közvetlenül az Azure-azonosító után a javaslat magas szintű leírást nyújt a vezérlőről.
- **Útmutató**: a javaslat indoklása és a megvalósításával kapcsolatos útmutatásra mutató hivatkozások. Ha Azure Security Center támogatja az ajánlást, a rendszer ezeket az információkat is felsorolja.
- **Felelősség**: felelős a vezérlő végrehajtásáért. A lehetséges forgatókönyvek az ügyfél felelőssége, a Microsoft felelőssége vagy a megosztott felelősség.
- **Azure Security Center figyelés**: azt határozza meg, hogy a vezérlőt Azure Security Center figyeli-e, hivatkozással.

Az alapkonfiguráció minden olyan javaslatot tartalmaz, amely nem alkalmazható erre a szolgáltatásra, így teljes képet kaphat arról, hogyan kapcsolódik az Azure biztonsági teljesítményteszt az egyes szolgáltatásokhoz. Előfordulhat, hogy esetenként olyan vezérlőkre van szükség, amelyek különböző okok miatt nem alkalmazhatók – például IaaS/számítási központú vezérlők (például az operációs rendszer konfigurálásához tartozó vezérlők esetében) – előfordulhat, hogy nem alkalmazhatók a Pásti-szolgáltatásokra.
