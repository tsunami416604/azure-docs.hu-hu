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
ms.openlocfilehash: 2114d9ecff5ee49c63d737cf13278fe45f1f9f73
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589960"
---
# <a name="overview-of-azure-security-baselines"></a>Az Azure biztonsági alaptervek áttekintése

Az Azure biztonsági alapkonfigurációi segítségével továbbfejlesztett eszközökkel, követéssel és biztonsági funkciókkal erősítheti termékeink biztonságát, és egységes környezetet biztosíthat a környezet biztonságossá tételéhez.

Az Azure szolgáltatás biztonsági alapkonfigurációi a felhő-központú vezérlési területekre összpontosítanak. Ezek a vezérlők összhangban vannak a jól ismert biztonsági összehasonlításokkal, például a Center for Internet Security (CIS) szolgáltatással. Az alaptervek útmutatást nyújtanak az [Azure biztonsági teljesítménytesztben](overview.md)felsorolt ellenőrzési területekhez.

Minden javaslat a következő információkat tartalmazza:
- **Azure ID**: a javaslatnak megfelelő Azure biztonsági TELJESÍTMÉNYTESZT-azonosító.
- **Javaslat**: közvetlenül az Azure-azonosító után a javaslat magas szintű leírást nyújt a vezérlőről.
- **Útmutató**: a javaslat indoklása és a megvalósításával kapcsolatos útmutatásra mutató hivatkozások. Ha Azure Security Center támogatja az ajánlást, a rendszer ezeket az információkat is felsorolja.
- **Felelősség**: felelős a vezérlő végrehajtásáért. A lehetséges forgatókönyvek az ügyfél felelőssége, a Microsoft felelőssége vagy a felelősség megosztása.
- **Azure Security Center figyelés**: azt határozza meg, hogy a vezérlőt Azure Security Center figyeli-e, hivatkozással.

Az alapkonfiguráció minden olyan javaslatot tartalmaz, amely nem alkalmazható erre a szolgáltatásra, így teljes képet kaphat arról, hogyan kapcsolódik az Azure biztonsági teljesítményteszt az egyes szolgáltatásokhoz. Esetenként olyan vezérlők is lehetnek, amelyek különböző okokból nem alkalmazhatók – például IaaS/számítási központú vezérlők (például az operációs rendszer konfigurációjának felügyeletére vonatkozó vezérlők) nem alkalmazhatók a Pásti-szolgáltatásokra.
