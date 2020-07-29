---
title: Kiszolgálók és virtuális gépek áttelepítése az Azure-ba Azure Site Recovery
description: Ismerteti, hogyan telepítheti át a helyszíni és az Azure IaaS virtuális gépeket az Azure-ba a Azure Site Recovery szolgáltatás használatával.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: raynew
ms.openlocfilehash: aaf01dcd63c21e4741456f4f7fccaf22b8fbfffc
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281310"
---
# <a name="about-migration"></a>A migrálás ismertetése

A Azure Migrate szolgáltatás használatával telepítse át a virtuális gépeket és a kiszolgálókat az Azure-ba a Azure Site Recovery szolgáltatás helyett. [További](../migrate/migrate-services-overview.md) információ a Azure Migrateról.


## <a name="why-use-azure-migrate"></a>Miért javasolt az Azure Migrate használata?

Az áttelepítéshez Azure Migrate használata számos előnyt biztosít:
 
 
- Azure Migrate központosított hubot biztosít a felderítéshez, az értékeléshez és az Azure-ba való áttelepítéshez.
- A Azure Migrate használata lehetővé teszi a Azure Migrate eszközökkel, más Azure-szolgáltatásokkal és harmadik féltől származó eszközökkel való együttműködés és jövőbeni bővíthetőség biztosítását.
- A Azure Migrate: a kiszolgáló áttelepítési eszköze az Azure-ba történő kiszolgáló-áttelepítéshez készült. Áttelepítésre van optimalizálva. Nem kell megismernie az áttelepítéshez közvetlenül nem kapcsolódó fogalmakat és forgatókönyveket. 
- A 180 napos áttelepítéshez nem tartoznak eszköz-használati díjak a virtuális gépek replikálásának időpontjában. Ez időt biztosít az áttelepítés befejezésére. Csak a replikációhoz használt tárolási és hálózati erőforrásokért, valamint a tesztelési áttelepítések során felhasznált számítási díjakért kell fizetnie.
- A VMware virtuális gépek esetében a Azure Migrate ügynök nélküli áttelepítést biztosít az ügynök-alapú áttelepítés mellett.
- Új áttelepítési funkciókat rangsorolunk a Azure Migrate: csak a kiszolgáló áttelepítését szolgáló eszközhöz.

## <a name="when-to-use-site-recovery"></a>Mikor kell használni a Site Recovery?

Site Recovery kell használni:

- A helyszíni gépek Azure-ba való vész-helyreállításához.
- Azure-beli virtuális gépek vész-helyreállításához az Azure-régiók között.

Habár azt javasoljuk, hogy a Azure Migrate használatával telepítse át a helyszíni kiszolgálókat az Azure-ba, ha már elindította az áttelepítést a Site Recoveryval, továbbra is használhatja az áttelepítés befejezéséhez.  

## <a name="next-steps"></a>További lépések

> [Tekintse át](../migrate/resources-faq.md) a Azure Migrateával kapcsolatos gyakori kérdéseket.
