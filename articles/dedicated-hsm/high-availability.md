---
title: Magas rendelkezésre állás – Azure dedikált HSM-be |} A Microsoft Docs
description: Azure dedikált HSM magas rendelkezésre állású példa és alapszintű szempontok
services: dedicated-hsm
author: barclayn
manager: barbkess
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: barclayn
ms.openlocfilehash: bf55761bb58e309f919e08530fe047a137756323
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656544"
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
