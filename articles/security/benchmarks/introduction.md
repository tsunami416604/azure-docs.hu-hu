---
title: Az Azure biztonsági teljesítménytesztjének ismertetése
description: Biztonsági referenciaérték bevezetése
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: 8f65eb008d5df6ea3f2f85778d538023e6ce98eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945337"
---
# <a name="azure-security-benchmark-introduction"></a>Az Azure biztonsági teljesítménytesztjának bevezetése

Több éves vagy akár több évtizedes tapasztalattal rendelkezhet a helyszíni számítástechnikával kapcsolatban. Tudja, hogyan kell biztosítani azokat a telepítéseket; de a felhő más. Honnan tudja, hogy a felhőalapú telepítések biztonságosak-e? Mi a különbség a helyszíni rendszerek és a felhőalapú telepítések biztonsági gyakorlatai között?

A felhő biztonságának védelme érdekében számos tanulmány, ajánlott eljárás, referenciaarchitektúra, webes útmutató, nyílt forráskódú eszközök, kereskedelmi megoldások, intelligencia-hírcsatornák és egyebek széles gyűjteménye áll fenn. Melyik lehetőséget használja? Mit tehet azért, hogy elfogadható szintű biztonságot kapjon a felhőben? 

A felhőalapú telepítések biztonságossá tétele a felhőalapú biztonsági teljesítményértékelési javaslatokra való összpontosítás egyik legjobb módja. A szolgáltatások biztosítására vonatkozó teljesítménymutatók ajánlásai a kiberbiztonsági kockázatok alapvető megértésével és kezelésének módjával kezdődnek. Ezt a megértést a felhőszolgáltató tól származó teljesítményalapú biztonsági javaslatok elfogadásával segíthet a környezet adott biztonsági konfigurációs beállításainak kiválasztásában. 

Az Azure Security Benchmark nagy hatású biztonsági javaslatok gyűjteményét tartalmazza, amelyek segítségével biztosíthatja az Azure-ban használt szolgáltatások többségét. Ezeket a javaslatokat "általános" vagy "szervezeti"-ként is felhasználhatja, mivel a legtöbb Azure-szolgáltatásra alkalmazható. Az Azure Security Benchmark-javaslatok ezután testre szabott minden Egyes Azure-szolgáltatás, és ez a testreszabott útmutatást tartalmazza a szolgáltatás javaslatok at. 

Az Azure Security Benchmark dokumentáció biztonsági vezérlőket és szolgáltatási javaslatokat határoz meg.

- **Biztonsági vezérlők:** Az Azure Security Benchmark javaslatok biztonsági vezérlők szerint vannak kategorizálva. A biztonsági ellenőrzések magas szintű gyártói független biztonsági követelményeket jelentenek, például a hálózati biztonságot és az adatvédelmet. Minden biztonsági vezérlő rendelkezik egy sor biztonsági javaslatok és utasítások, amelyek segítségével hajtsa végre ezeket a javaslatokat. 
- **Szolgáltatási javaslatok:** Ha elérhető, az Azure-szolgáltatások teljesítményteszt-javaslatai tartalmazni fogják az Azure Security Benchmark-javaslatokat, amelyek kifejezetten az adott szolgáltatáshoz vannak szabva. 

A "Control", "Benchmark" és "Baseline" kifejezéseket gyakran használják az Azure Security Benchmark dokumentációjában, és fontos megérteni, hogy az Azure hogyan használja ezeket a kifejezéseket. 

| Időtartam | Leírás | Példa |
|--|--|--|
| Vezérlés | A **vezérlő** egy olyan funkció vagy tevékenység magas szintű leírása, amelyet meg kell oldani, és amely nem egy technológiára vagy megvalósításra jellemző. | Az adatvédelem az egyik biztonsági ellenőrzés. Ez a vezérlő olyan konkrét műveleteket tartalmaz, amelyekkel foglalkozni kell az adatok védelmének biztosítása érdekében. |
| Külső viszonyítási alap | A **teljesítményteszt** biztonsági javaslatokat tartalmaz egy adott technológiához, például az Azure-hoz. Az ajánlások szerint vannak kategorizálva, hogy melyik vezérlőhöz tartoznak. | Az Azure Security benchmark az Azure platformra vonatkozó biztonsági javaslatokat tartalmazza  |
| Alapkonfiguráció | Az **alapterv** a szervezet biztonsági követelményei. A biztonsági követelmények a referenciamutatókajánlásain alapulnak. Minden szervezet dönti el, hogy mely teljesítményteszt-ajánlásokat kell belefoglalni az alaptervbe. | A Contoso vállalat úgy hozza létre a biztonsági alapkonfigurációt, hogy az Azure Security Benchmark ban speciális javaslatokat kér. |

Örömmel fogadjuk az Azure Security Benchmarkra vonatkozó visszajelzéseit! Javasoljuk, hogy az alábbi visszajelzési területen adjon meg megjegyzéseket. Ha privátabban szeretné megosztani a bemenetet az Azure Security Benchmark csapatával, akkor töltse ki az űrlapot ahttps://aka.ms/AzSecBenchmark 
