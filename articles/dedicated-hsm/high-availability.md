---
title: Magas rendelkezésre állás - Azure dedikált HSM | Microsoft dokumentumok
description: Az Azure dedikált HSM magas rendelkezésre állású példája és alapvető szempontok
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70882247"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Az Azure dedikált HSM magas rendelkezésre állása

Az Azure dedikált HSM-et a Microsoft magas rendelkezésre állású adatközpontjai támasztják alá. Azonban minden magas rendelkezésre állású adatközpont ki van téve a honosított hibáknak, és szélsőséges körülmények között, regionális szintű hibák. A Microsoft hsm-eszközöket telepít egy régiókülönböző adatközpontjaiban annak biztosítása érdekében, hogy több eszköz kiépítése ne eredményezze, hogy ezek az eszközök egyetlen állványt osztanak meg. A Gemalto HA-csoport szolgáltatás használatával további magas rendelkezésre állás érhető el a HSM-ek egy régió adatközpontjai közötti párosításával. Az eszközök régiók közötti párosítása is lehetséges a regionális feladatátvétel kezelése érdekében vész-helyreállítási helyzetben. Ezzel a többrétegű, magas rendelkezésre állású konfigurációval minden eszközhiba automatikusan megszűnik az alkalmazások működésének fenntartásához. Minden adatközpont rendelkezik a helyszínen található tartalék eszközökkel és összetevőkkel is, így minden meghibásodott eszköz időben cserélhető.

## <a name="high-availability-example"></a>Magas rendelkezésre állás példa

A HSM-eszközök szoftverszintű magas rendelkezésre állásra való beállításával kapcsolatos információk a "Gemalto Luna Network HSM Administration Guide" (Gemalto Luna Network HSM felügyeleti útmutató) című részben találhatóak. Ez a dokumentum a [Gemalto HSM oldalon](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/)érhető el.

Az alábbi ábrán egy magas rendelkezésre állású architektúra látható. Több eszközt használ a régióban, és több eszközt párosít egy külön régióban. Ez az architektúra legalább négy HSM-eszközt és virtuális hálózati összetevőt használ.

![Magas rendelkezésre állásdiagram](media/high-availability/high-availability.png)

## <a name="next-steps"></a>További lépések

Javasoljuk, hogy a szolgáltatás minden kulcsfontosságú fogalma, például a magas rendelkezésre állás és a biztonság, jól érthető az eszköz kiépítése és az alkalmazás tervezése vagy üzembe helyezése előtt.
További koncepciószintű témák:

* [Telepítési architektúra](deployment-architecture.md)
* [Fizikai biztonság](physical-security.md)
* [Hálózat](networking.md)
* [Támogatási lehetőségek](supportability.md)
* [Megfigyelő](monitoring.md)

A HSM-eszközök magas rendelkezésre állásra való konfigurálásával kapcsolatos további részletekért tekintse meg a Gemalto ügyfélszolgálati portálját a rendszergazdai útmutatókért, és lásd a 6.
