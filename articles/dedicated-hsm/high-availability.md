---
title: Magas rendelkezésre állás – Azure dedikált HSM-be |} A Microsoft Docs
description: Azure dedikált HSM magas rendelkezésre állású példa és alapszintű szempontok
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
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70882247"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Az Azure dedikált HSM magas rendelkezésre állás

Az Azure, a dedikált HSM által a Microsoft magas rendelkezésre állású adatközpontok támasztanak. Azonban minden olyan magas rendelkezésre állású adatközpontot kitéve, hibák és regionális meghibásodások rendkívüli körülmények között. A Microsoft annak érdekében, hogy egyetlen állványon megosztó eszközök kiépítése több eszközön nem vezethet egy adott régión belül különböző adatközpontokban a hardveres biztonsági modulokhoz helyez üzembe. A magas rendelkezésre állás további szintje úgy érhető el, hogy a HSM a Gemalto HA csoport használatával a régió adatközpontjai között párosítja. Akkor is pár eszközökre történő regionális feladatátvételt egy vész-helyreállítási helyzet a régiók között elosztva. Ez a többrétegű magas rendelkezésre állású konfigurációval minden eszköz hiba automatikusan kibocsátásokban megtörténik az alkalmazások használatához. Minden adatközpontban is tartalék eszközt és összetevőt helyszíni így időben minden olyan eszköz, sikertelen lehet cserélni.

## <a name="high-availability-example"></a>Példa a magas rendelkezésre állás

Információk a magas rendelkezésre állás érdekében hardveres biztonsági modulokhoz konfigurálása a szoftver szintjén van az "Gemalto Luna hálózati HSM felügyeleti útmutató". Ez a dokumentum a [GEMALTO HSM lapon](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/)érhető el.

Az alábbi ábrán egy magas rendelkezésre állású architektúra látható. Régióban több eszközre és a egy külön régióban párosított több eszközt használ. Ez az architektúra legalább négy hardveres biztonsági modulokhoz, a virtuális hálózati összetevőket használ.

![Magas rendelkezésre állású diagramja](media/high-availability/high-availability.png)

## <a name="next-steps"></a>További lépések

Javasoljuk, hogy a szolgáltatás magas rendelkezésre állással és biztonsággal, például az összes alapfogalmak megértsük eszközkiépítési és az alkalmazás-tervezés vagy a központi telepítés előtt.
További fogalom szintű témakörök:

* [Üzembe helyezési architektúrája](deployment-architecture.md)
* [Fizikai biztonság](physical-security.md)
* [Hálózat](networking.md)
* [Támogatási lehetőségek](supportability.md)
* [Monitorozás](monitoring.md)

Adott konfigurációval kapcsolatos részletekért a magas rendelkezésre állás érdekében hardveres biztonsági modulokhoz kérjük tekintse meg a rendszergazda útmutatók a Gemalto Ügyfélportál-támogatás és lásd a 6.
