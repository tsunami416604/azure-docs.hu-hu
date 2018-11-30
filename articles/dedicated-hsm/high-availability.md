---
title: Az Azure dedikált HSM magas rendelkezésre állású |} A Microsoft Docs
description: Azure dedikált HSM FIPS szabványnak megfelelő Azure-ban kulcstároló-képességeket biztosít 140-2-3. szint tanúsítvánnyal
services: dedicated-hsm
author: barclayn
manager: mbaldwin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: barclayn
ms.openlocfilehash: eb3c6ea64b55d59f261927925d4d4ae87546bf95
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52319588"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Az Azure dedikált HSM magas rendelkezésre állás

Az Azure, a dedikált HSM által a Microsoft magas rendelkezésre állású adatközpontok támasztanak. Azonban minden olyan magas rendelkezésre állású adatközpontot kitéve, hibák és regionális meghibásodások rendkívüli körülmények között. A Microsoft annak érdekében, hogy egyetlen állványon megosztó eszközök kiépítése több eszközön nem vezethet egy adott régión belül különböző adatközpontokban a hardveres biztonsági modulokhoz helyez üzembe. Magas rendelkezésre állású nagyobb szintű társítja a HSM-EK egy régióban adatközpontokban érhető el. Akkor is pár eszközökre történő regionális feladatátvételt egy vész-helyreállítási helyzet a régiók között elosztva. Ez a többrétegű magas rendelkezésre állású konfigurációval minden eszköz hiba automatikusan kibocsátásokban megtörténik az alkalmazások használatához. Minden adatközpontban is tartalék eszközt és összetevőt helyszíni így időben minden olyan eszköz, sikertelen lehet cserélni.

## <a name="high-availability-example"></a>Példa a magas rendelkezésre állás

Információk a magas rendelkezésre állás érdekében hardveres biztonsági modulokhoz konfigurálása a szoftver szintjén van az "Gemalto Luna hálózati HSM felügyeleti útmutató". Ez a dokumentum megtalálható a [Gemalto támogatási Ügyfélportál](https://supportportal.gemalto.com/csm/).

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
