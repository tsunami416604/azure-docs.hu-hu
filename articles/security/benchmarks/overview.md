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
ms.openlocfilehash: 12f463428e5e11fb11fc117aa9ffa2c606f17e26
ms.sourcegitcommit: 541e6139c535d38b9b4d4c5e3bfa7eef02446fdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75667449"
---
# <a name="overview"></a>Áttekintés

Az Azure biztonsági teljesítményteszt olyan javaslatokat tartalmaz, amelyek segítségével javíthatja alkalmazásai és adatai biztonságát az Azure-ban.   

Ez a teljesítményteszt a felhő-központú ellenőrzési területekre koncentrál. Ezek a vezérlők összhangban vannak a jól ismert biztonsági referenciaértékekkel, például a Center for Internet Security (CIS) vezérlők 7,1-es verziójában leírt módon. 

Az Azure biztonsági Teljesítménytesztben a következő vezérlők használhatók: 

- Hálózati biztonság 
- Naplózás és monitorozás 
- Identitás- és hozzáférés-vezérlés 
- Data Protection 
- Biztonságirés-kezelés 
- Leltár-és eszközfelügyelet 
- Biztonságos konfiguráció 
- Kártevők elleni védelem 
- Adat-helyreállítás 
- Incidensmegoldás 
- Behatolási tesztek és Red Team-gyakorlatok

## <a name="azure-security-benchmark-recommendations"></a>Az Azure biztonsági teljesítménytesztekkel kapcsolatos javaslatok 

Minden javaslat a következő információkat tartalmazza: 

- **Azure ID**: a javaslatnak megfelelő Azure biztonsági TELJESÍTMÉNYTESZT-azonosító. 
- **CIS-azonosító (k)** : a VIR teljesítményteszt-javaslata, amely megfelel a jelen javaslatnak.  
- **Felelősség**: azt, hogy az ügyfél vagy a szolgáltató (vagy mindkettő) felelős-e a javaslat végrehajtásáért. A biztonsági kötelezettségek a nyilvános felhőben vannak megosztva. Bizonyos biztonsági vezérlők csak a felhőalapú szolgáltató számára érhetők el, ezért a szolgáltató felelős az ilyen problémák kezeléséhez. Ezek általános megfigyelések – egyes szolgáltatások esetében a felelősség nem fog megjelenni, mint az Azure biztonsági Teljesítménytesztben. Ezeket a különbségeket az egyes szolgáltatásokra vonatkozó alapkövetelmények írják le. 
- **Részletek**: a javaslat indoklása és a javaslat megvalósításával kapcsolatos útmutatásra mutató hivatkozások. Ha Azure Security Center támogatja a javaslatot, az itt látható információk lesznek felsorolva.  

Örömmel vesszük a részletes visszajelzéseket és az aktív részvételt az Azure biztonsági teljesítményteszttel kapcsolatos erőfeszítésekben. Ha a teljesítményteszt-csapat közvetlen bemenetét szeretné megadni, töltse ki az űrlapot [https://aka.ms/AzSecBenchmark](https://aka.ms/AzSecBenchmark)címen.

## <a name="next-steps"></a>Következő lépések

Tekintse meg az első biztonsági ellenőrzést: [hálózati biztonság](security-control-network-security.md)
