---
title: Az Azure biztonsági teljesítményteszt áttekintése
description: Biztonsági teljesítményteszt áttekintése
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 7c1eb9dceda70cd74237f8a824ef03bb9c2dc7ae
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82606840"
---
# <a name="security-baselines-for-azure"></a>Biztonsági alapkonfigurációk az Azure-hoz

Az Azure biztonsági alapkonfigurációi a jobb szerszámozás, nyomon követés és biztonsági funkciók révén fokozzák a biztonságot. Emellett egységes felhasználói élményt biztosítanak a környezet biztonságossá tételéhez.

Az Azure biztonsági alapkonfigurációi a felhő-központú vezérlési területekre összpontosítanak. Ezek a vezérlők összhangban vannak a jól ismert biztonsági összehasonlításokkal, például a Center for Internet Security (CIS) szolgáltatással. Az alaptervek útmutatást nyújtanak az [Azure biztonsági teljesítménytesztben](overview.md)felsorolt ellenőrzési területekhez.

Minden javaslat a következő információkat tartalmazza:

- **Azure ID**: a javaslatnak megfelelő Azure biztonsági TELJESÍTMÉNYTESZT-azonosító.
- **Javaslat**: közvetlenül az Azure-azonosító után a javaslat magas szintű leírást nyújt a vezérlőről.
- **Útmutató**: a javaslat indoklása és a megvalósításával kapcsolatos útmutatásra mutató hivatkozások. Ha Azure Security Center támogatja az ajánlást, a rendszer ezeket az információkat is felsorolja.
- **Felelősség**: felelős a vezérlő végrehajtásáért. A lehetséges forgatókönyvek az ügyfél felelőssége, a Microsoft felelőssége vagy a megosztott felelősség.
- **Azure Security Center figyelés**: azt határozza meg, hogy a vezérlőt Azure Security Center figyeli-e, hivatkozással.

Az alapkonfiguráció minden olyan javaslatot tartalmaz, amely nem alkalmazható erre a szolgáltatásra, így teljes képet kaphat arról, hogyan kapcsolódik az Azure biztonsági teljesítményteszt az egyes szolgáltatásokhoz. Előfordulhat, hogy esetenként olyan vezérlőkre van szükség, amelyek különböző okok miatt nem alkalmazhatók – például IaaS/számítási központú vezérlők (például az operációs rendszer konfigurálásához tartozó vezérlők esetében) – előfordulhat, hogy nem alkalmazhatók a Pásti-szolgáltatásokra.
