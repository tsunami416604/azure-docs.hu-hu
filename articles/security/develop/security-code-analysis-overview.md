---
title: Microsoft Azure biztonsági kód elemzése – dokumentáció áttekintése
description: Ez a cikk a biztonsági kód elemzése bővítmény áttekintését mutatja be
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 283d63bafc583f2ac9da3294644aaebd17d7c950
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718287"
---
# <a name="about-microsoft-security-code-analysis"></a>Tudnivalók a Microsoft biztonsági kódok elemzéséről

A **Microsoft biztonsági kód elemzése bővítmény** lehetővé teszi a csapatok számára, hogy zökkenőmentesen integrálják a biztonsági kód elemzését az Azure DevOps CI/CD-folyamataiba a Microsoft [biztonságos fejlesztési életciklus (SDL)](https://www.microsoft.com/securityengineering/sdl/practices) szakértői által ajánlott módon. A biztonság egy egységes UX-en keresztül egyszerűsíthető, amely a különböző eszközök futtatásának bonyolultságát is elvonta. Az eszközök NuGet-alapú kézbesítésével a csapatoknak már nincs szükségük az eszközkészlet telepítésének és frissítésének kezeléséhez. A parancssorral és az alapszintű Build-feladattal, az összes felhasználóval, a hozzáértési eszközökről a mindennapi fejlesztőkig, akár alig, akár sokkal többen vezérelheti az eszközöket. A csapatok olyan hatékony post-feldolgozási képességeket is kihasználhatnak, mint például az adatmegőrzési naplók közzététele, a hasznosítható fejlesztői fókuszált jelentések generálása & a kiépítési megszakítások konfigurálása a regressziók

## <a name="why-microsoft-security-code-analysis"></a>A Microsoft biztonsági kódjának elemzése

### <a name="security-simplified"></a>A biztonság egyszerűsített

A biztonsági kód elemzési eszközeinek az Azure DevOps-folyamathoz való hozzáadása olyan egyszerű, mint az új feladatok hozzáadása. Szabja testre őket, vagy lépjen az alapértelmezett beállításokkal. A feladatok a DevOps folyamat részeként futnak, és a naplókat készítenek a különböző megállapítások részleteiről.

### <a name="clean-builds"></a>Tiszta buildek

Az eszközök által jelentett kezdeti problémák kezelése után beállíthatja, hogy a bővítmény új problémák esetén is kibontsa a buildeket. A folyamatos integráció létrehozása minden lekéréses kérelem esetében még soha nem volt ilyen egyszerű!

### <a name="set-it-and-forget-it"></a>Beállítás és felejtsd el

A Build-feladatok és-eszközök beállítható úgy, hogy naprakészek maradjanak (és alapértelmezés szerint). Ha az eszköz frissített verziója van, nincs szükség a letöltésére és telepítésére; Ez a bővítmény gondoskodik az Ön számára. 

>>>
### <a name="under-the-hood"></a>A motorháztető alatt

A Microsoft biztonsági kód Analysis Extension felépítési feladatait a (z) bonyolultságának tulajdonítja:
  - A biztonsági statikus elemzési eszközök futtatása, valamint
  - A naplófájlokból származó eredmények feldolgozásával hozzon létre egy összegző jelentést, vagy szakítsa meg a buildet.
>>>

## <a name="security-code-analysis-toolset"></a>Biztonsági kód elemzése eszközkészlet

A Microsoft biztonsági kód elemzése bővítmény azonnal elérhetővé teszi a fontos Analysis Tools legújabb verzióit. A bővítmény a Microsoft belső és a nyílt forráskódú eszközöket is tartalmazza. Az eszközök automatikusan letöltődnek a felhőben üzemeltetett ügynökön, miután konfigurálta & a folyamat futtatását a megfelelő felépítési feladattal. Alább láthatók a bővítményben jelenleg elérhető eszközök. Tájékozódjon a további információkért, és küldje el nekünk javaslatait a hozzáadott eszközökhöz.

### <a name="anti-malware-scanner"></a>Kártevő szoftver víruskereső

A kártevő-elhárító képolvasó felépítési feladata mostantól a Microsoft biztonsági kód elemzése bővítmény részét képezi. Olyan Build-ügynökön kell futnia, amelyen már telepítve van a Windows Defender. További információért látogasson el a [Defender](https://aka.ms/defender) webhelyére 

### <a name="binskim"></a>BinSkim

A BinSkim egy hordozható végrehajtható fájl (PE), amely ellenőrzi a fordító/a csatoló beállításait és az egyéb, a biztonság szempontjából releváns bináris jellemzőit. A Build feladat parancssori burkolót biztosít a BinSkim. exe alkalmazás köré. A BinSkim egy nyílt forráskódú eszköz, és további információért látogasson el [a BinSkim-ra a githubon](https://github.com/Microsoft/binskim)

### <a name="credential-scanner"></a>Hitelesítőadat-olvasó

A forráskódban tárolt jelszavak és egyéb titkos kódok jelenleg jelentős problémát jelentenek. A hitelesítőadat-olvasó egy szabadalmaztatott statikus elemzési eszköz, amely a forráskódban és a Build kimenetében észleli a hitelesítő adatokat, titkokat, tanúsítványokat és más bizalmas tartalmakat.

### <a name="microsoft-security-risk-detection"></a>Microsoft biztonsági kockázatok észlelése

A biztonsági kockázatok észlelése a Microsoft egyedi felhőalapú fuzz Testing Service a szoftverben található kihasználható biztonsági hibák azonosításához. A szolgáltatáshoz külön előkészítési folyamat szükséges. További információért látogasson el [a MSRD on docs.microsoft.com](https://docs.microsoft.com/security-risk-detection/)

### <a name="roslyn-analyzers"></a>-Elemzők

A Microsoft fordító-integrált statikus elemzési eszköze a felügyelt kód (C# és a VB) elemzéséhez. További információért látogasson el [a docs.microsoft.com](https://docs.microsoft.com/dotnet/standard/analyzers/)

### <a name="tslint"></a>TSLint

A TSLint egy bővíthető statikus elemzési eszköz, amely az olvashatóságot, a karbantartást és a funkcionalitással kapcsolatos hibákat a géppel vizsgálja. A modern szerkesztők széles körben támogatottak, és a saját vonali szabályokkal, konfigurációkkal és Formázók használatával testreszabhatók. A TSLint egy nyílt forráskódú eszköz, és további információért látogasson el [a TSLint-ra a githubon](https://github.com/palantir/tslint)

## <a name="analysis-and-post-processing-of-results"></a>Az eredmények elemzése és utólagos feldolgozása

A Microsoft biztonsági kód elemzése bővítmény három hasznos utólagos feladattal is rendelkezik, amelyek segítségével feldolgozhatja és elemezheti a biztonsági eszközök feladatai által talált eredményeket. Ezek általában a folyamatba kerülnek, az összes többi eszköz feladatát követően.

### <a name="publish-security-analysis-logs"></a>Biztonsági elemzési naplók közzététele
A biztonsági elemzési naplók közzététele felépítési feladat megőrzi a biztonsági eszközök naplófájljait a létrehozás során a vizsgálathoz és a követéshez.

Ezek közzétehetők az Azure-kiszolgálói összetevőkön (zip-fájlként), vagy a privát Build-ügynökből egy elérhető fájlmegosztás másolatait.

### <a name="security-report"></a>Biztonsági jelentés
A biztonsági jelentés létrehozása feladat elemzi a biztonsági eszközök által létrehozott naplófájlokat a létrehozás során, és létrehoz egy összegző jelentési fájlt az Analysis Tools által talált összes problémával.

A feladat beállítható úgy, hogy az adott eszközök vagy az összes eszköz esetében jelentést készítsenek, és azt is kiválaszthatja, hogy a rendszer milyen szintű problémákat (hibákat és hibákat és figyelmeztetéseket) jelentsen.

### <a name="post-analysis-build-break"></a>Elemzés utáni (Build Break)
Az elemzés utáni felépítési feladat lehetővé teszi, hogy az ügyfél beinjektáljon egy összeállítási megszakítást, és a kiépítés meghiúsuljon abban az esetben, ha egy vagy több elemzési eszköz jelentést tesz a kód megállapításairól vagy problémáiról.

A feladat beállítható úgy, hogy az adott eszközök vagy az összes eszköz által észlelt problémákra felépítse a buildet, valamint a talált problémák súlyossága (hibák vagy figyelmeztetések) alapján.

>[!NOTE]
>Az egyes felépítési feladatok sikeresek lesznek, tervezéssel, ha az eszköz sikeresen befejeződik, függetlenül attól, hogy vannak-e megállapítások, vagy sem, hogy a Build az összes eszköz futtatásának befejezését követően is futtatható legyen.

## <a name="next-steps"></a>További lépések

A biztonsági kód elemzésének bevezetésére és telepítésére vonatkozó utasításokért tekintse meg a bevezetési [és telepítési útmutatót](security-code-analysis-onboard.md)

A Build-feladatok konfigurálásával kapcsolatos további információkért tekintse meg a [konfigurációs útmutatót](security-code-analysis-customize.md)

Ha további kérdései vannak a bővítményről és a rendelkezésre álló eszközökről, tekintse meg a [Gyakori kérdések oldalát.](security-code-analysis-faq.md)