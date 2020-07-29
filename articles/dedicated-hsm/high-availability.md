---
title: Magas rendelkezésre állás – Azure dedikált HSM | Microsoft Docs
description: Azure dedikált HSM – magas rendelkezésre állás – példa és alapvető szempontok
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: mbaldwin
ms.openlocfilehash: 536ef62acad900090924598edfa45450b2a8c951
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "70882247"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Azure dedikált HSM – magas rendelkezésre állás

Az Azure dedikált HSM-et a Microsoft által biztosított, magasan elérhető adatközpontok támasztják alá. Azonban a magas rendelkezésre állású adatközpontok sebezhetők a honosított hibák és szélsőséges körülmények között, regionális szintű hibák esetén. A Microsoft különböző adatközpontokban helyez üzembe HSM-eszközöket egy adott régióban, így biztosítva, hogy több eszköz kiépítése ne vezessen egyetlen Racket megosztó eszközökhöz sem. A magas rendelkezésre állás további szintje úgy érhető el, hogy a HSM a Gemalto HA csoport használatával a régió adatközpontjai között párosítja. Az eszközök a régiók között is összeállíthatók a vész-helyreállítási helyzetek regionális feladatátvételének megoldásához. Ezzel a többrétegű magas rendelkezésre állási konfigurációval minden eszköz meghibásodása automatikusan megtartja az alkalmazások működését. Az összes adatközponthoz is vannak tartalék eszközök és összetevők a helyszínen, így minden hibás eszköz lecserélhető egy időben.

## <a name="high-availability-example"></a>Magas rendelkezésre állási példa

A HSM-eszközök magas rendelkezésre álláshoz való konfigurálásáról a szoftver szintjén a "Gemalto Luna Network HSM felügyeleti útmutató" című cikk nyújt tájékoztatást. Ez a dokumentum a [GEMALTO HSM lapon](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/)érhető el.

A következő ábra egy magasan elérhető architektúrát mutat be. Több eszközt használ a régióban, és több eszköz is párosítva van egy külön régióban. Ez az architektúra legalább négy HSM-eszközt és virtuális hálózatkezelési összetevőt használ.

![Magas rendelkezésre állási diagram](media/high-availability/high-availability.png)

## <a name="next-steps"></a>További lépések

Javasoljuk, hogy a szolgáltatás alapvető fogalmait, például a magas rendelkezésre állást és a biztonságot is jól megértse az eszköz kiépítése és az alkalmazások tervezése vagy üzembe helyezése előtt.
További fogalmi szintű témakörök:

* [Üzembe helyezési architektúra](deployment-architecture.md)
* [Fizikai biztonság](physical-security.md)
* [Hálózat](networking.md)
* [Támogatási lehetőségek](supportability.md)
* [Figyelés](monitoring.md)

A HSM-eszközök magas rendelkezésre állásra való konfigurálásával kapcsolatos részletes információkért tekintse meg a Gemalto ügyfélszolgálati portálját a rendszergazdai útmutatókhoz, és tekintse meg a 6. szakaszt.
