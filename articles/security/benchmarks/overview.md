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
ms.openlocfilehash: 2f4b034948605b0a53a0320863608d284719a96d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587532"
---
# <a name="overview-of-the-azure-security-benchmark"></a>Az Azure biztonsági referenciaértékének áttekintése

Az Azure Security Benchmark olyan javaslatokat tartalmaz, amelyek segítenek az alkalmazások és az azure-beli adatok biztonságának javításában.

Ez a teljesítményteszt a felhőközpontú vezérlőterületekre összpontosít. Ezek a vezérlők összhangban vannak a jól ismert biztonsági referenciaértékekkel, például az Internet biztonsági központ (CIS) 7.1-es verziójával ismertetett vezérlőkkel.

Az Azure Security Benchmark a következő vezérlőket használja: 

- [Hálózati biztonság](security-control-network-security.md)
- [Naplózás és monitorozás](security-control-logging-monitoring.md)
- [Identitás- és hozzáférés-vezérlés](security-control-identity-access-control.md)
- [Adatvédelem](security-control-data-protection.md)
- [Biztonságirés-kezelés](security-control-vulnerability-management.md)
- [Leltár-és eszközfelügyelet](security-control-inventory-asset-management.md)
- [Biztonságos konfiguráció](security-control-secure-configuration.md)
- [Kártevők elleni védelem](security-control-malware-defense.md)
- [Adat-helyreállítás](security-control-data-recovery.md)
- [Incidensre adott válasz](security-control-incident-response.md)
- [Behatolási tesztek és Red Team-gyakorlatok](security-control-penetration-tests-red-team-exercises.md)

Letöltheti az [Azure Security Benchmark v1 excel táblázatkezelőt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets)is.

## <a name="azure-security-benchmark-recommendations"></a>Az Azure biztonsági teljesítménytesztre vonatkozó javaslatok 

Minden ajánlás a következő információkat tartalmazza: 

- **Azure-azonosító:** Az Azure Security Benchmark-azonosító, amely megfelel a javaslatnak. 
- **CIS-azonosító(k):** Az ezen ajánlásnak megfelelő CIS-referenciaajánlás(ok).  
- **Felelősség**: Függetlenül attól, hogy az ügyfél vagy a szolgáltató (vagy mindkettő) felelős-e ezen ajánlás végrehajtásáért. A biztonsági felelősségek a nyilvános felhőben vannak megosztva. Egyes biztonsági vezérlők csak a felhőszolgáltató számára érhetők el, ezért a szolgáltató felelős ezek kezeléséért. Ezek általános megfigyelések – egyes egyes szolgáltatások esetében a felelősség más lesz, mint az Azure Biztonsági teljesítménytesztben felsoroltak. Ezeket a különbségeket az egyes szolgáltatásokra vonatkozó alapjavaslatok ismertetik. 
- **Részletek**: Az ajánlás indoklása és a végrehajtásra vonatkozó útmutatásra mutató hivatkozások. Ha a javaslatot az Azure Security Center támogatja, ezek az információk is megjelennek.

Örömmel fogadjuk részletes visszajelzéseit és aktív részvételét az Azure Security Benchmark erőfeszítéseiben. Ha szeretné, hogy a Benchmark csapat közvetlen bemenet, [https://aka.ms/AzSecBenchmark](https://aka.ms/AzSecBenchmark)kérjük, töltse ki az űrlapot a .

## <a name="next-steps"></a>Következő lépések

- Lásd az első biztonsági vezérlőt: [Hálózati biztonság](security-control-network-security.md)
- Olvassa el az [Azure Security Benchmark bevezetést](introduction.md)
- Az [Azure Security Benchmark v1 excel-táblázatának letöltése](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets)
