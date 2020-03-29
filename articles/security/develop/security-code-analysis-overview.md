---
title: A Microsoft biztonsági kódelemzés dokumentációjának áttekintése
description: Ez a cikk a Microsoft biztonsági kódelemzés bővítményének áttekintése
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
ms.openlocfilehash: 963bc909b69962cded0a50d717e3a653d3d69769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74851468"
---
# <a name="about-microsoft-security-code-analysis"></a>A Microsoft biztonsági kód elemzéséről

A Microsoft Security Code Analysis bővítménysegítségével a csapatok hozzáadhatják a biztonsági kódelemzést az Azure DevOps folyamatos integrációs és kézbesítési (CI/CD) folyamataikhoz. Ezt az elemzést a [Microsoft Biztonságos fejlesztési életciklus (SDL)](https://www.microsoft.com/securityengineering/sdl/practices) szakértői ajánlják.

Az egységes felhasználói felület leegyszerűsíti a biztonságot azáltal, hogy elrejti a futó eszközök összetettségét. Az eszközök NuGet-alapú kézbesítésével a csapatoknak már nem kell kezelniük a szerszámozás telepítését vagy frissítését. A parancssori és a buildfeladatok alapvető felületeivel minden felhasználó ugyanolyan mértékben szabályozhatja az eszközöket, amennyire csak szeretné.

A csapatok hatékony utófeldolgozási képességeket is használhatnak, például:

- Naplók közzététele megőrzésre.
- Használható, fejlesztőközpontú jelentések létrehozása.
- A regressziós tesztek buildtöréseikonfigurálása.

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>Miért érdemes a Microsoft Security Code Analysis-et használni?

### <a name="security-simplified"></a>Egyszerűsített biztonság

A Microsoft Security Code Analysis eszközök hozzáadása az Azure DevOps-folyamathoz ugyanolyan egyszerű, mint új feladatok hozzáadása. Testreszabhatja a feladatokat, vagy használhatja az alapértelmezett viselkedésüket. A feladatok az Azure DevOps-folyamat részeként futnak, és olyan naplókat hoznak létre, amelyek sokféle eredményt részleteznek.

### <a name="clean-builds"></a>Tiszta építmények

Miután az eszközök által jelentett kezdeti problémákat kezelte, beállíthatja, hogy a bővítmény új problémákra épüljön.A folyamatos integráció beállítása minden lekéréses kérelemre épül.

### <a name="set-it-and-forget-it"></a>Állítsa be, és felejtsd el

Alapértelmezés szerint a build feladatok és eszközök naprakészek maradnak. Ha van egy eszköz frissített verziója, nem kell letöltenie és telepítenie. A bővítmény gondoskodik a frissítésről.

### <a name="under-the-hood"></a>technikai részletek

A bővítmény buildfeladatai a következők bonyolultságát rejtik el:
  - Biztonsági statikus elemző eszközök futtatása.
  - A naplófájlokból származó eredmények feldolgozása összesítő jelentés létrehozásához vagy a build megszakításához.

## <a name="microsoft-security-code-analysis-tool-set"></a>Microsoft biztonsági kódelemző eszközkészlet

A Microsoft Biztonsági kódelemzés bővítménye a fontos elemzési eszközök legújabb verzióit teszi elérhetővé. A bővítmény microsoftos és nyílt forráskódú eszközöket is tartalmaz.

Ezek az eszközök automatikusan letöltődnek a felhőben üzemeltetett ügynök, miután a megfelelő build feladat konfigurálása és futtatása a folyamat ot.

Ez a szakasz a bővítményben jelenleg elérhető eszközök készletét sorolja fel. Figyelje meg a további eszközök hozzáadását. Is, küldje el nekünk a javaslatokat az eszközök, amelyeket szeretne, hogy adjunk.

### <a name="anti-malware-scanner"></a>Anti-Malware Scanner

A Kártevőirtó-vizsgálat készítési feladata most már szerepel a Microsoft Security Code Analysis bővítményben. Ezt a feladatot olyan buildügynökön kell futtatni, amelyen a Windows Defender már telepítve van. További információt a [Windows Defender webhelyén talál.](https://aka.ms/defender)

### <a name="binskim"></a>BinSkim között

BinSkim egy hordozható futtatható (PE) könnyű szkenner, amely érvényesíti fordító beállításait, linker beállításokat, és egyéb biztonsági szempontjából releváns jellemzői bináris fájlokat. Ez a buildfeladat parancssori burkolót biztosít a binskim.exe konzolalkalmazás körül. A BinSkim egy nyílt forráskódú eszköz. További információ: [BinSkim on GitHub](https://github.com/Microsoft/binskim).

### <a name="credential-scanner"></a>Hitelesítő adatok atszogatása

A forráskódban tárolt jelszavak és egyéb titkos kulcsok jelentős problémát jelentenek. A Credential Scanner egy szabadalmaztatott statikus elemző eszköz, amely segít megoldani ezt a problémát. Az eszköz észleli a hitelesítő adatokat, titkos kulcsokat, tanúsítványokat és más bizalmas tartalmakat a forráskódban és a build kimenetében.

### <a name="microsoft-security-risk-detection"></a>A Microsoft biztonsági kockázatészlelése

A Microsoft Security Risk Detection (MSRD) egy felhőalapú szolgáltatás a fuzz teszteléséhez. Azonosítja a szoftverben kihasználható biztonsági hibákat. Ehhez a szolgáltatáshoz külön előfizetés és aktiválás szükséges. További információt az [MSRD Fejlesztői központban talál.](https://docs.microsoft.com/security-risk-detection/)

### <a name="roslyn-analyzers"></a>Roslyn analizátorok

A Roslyn Analyzeers a Microsoft fordítóintegrált eszköze a felügyelt C# és Visual Basic kódok statikus elemzéséhez. További információ: [Roslyn-alapú analizátorok](https://docs.microsoft.com/dotnet/standard/analyzers/).

### <a name="tslint"></a>TSLint között

A TSLint egy bővíthető statikus elemző eszköz, amely ellenőrzi a TypeScript-kódot az olvashatóság, a karbantarthatóság és a működési hibák szempontjából. Ez széles körben támogatja a modern szerkesztők és épít rendszerek. Testreszabhatja a saját szöszszabályok, konfigurációk, és formatters. A TSLint egy nyílt forráskódú eszköz. További információ: [TSLint on GitHub](https://github.com/palantir/tslint).

## <a name="analysis-and-post-processing-of-results"></a>Az eredmények elemzése és utófeldolgozása

A Microsoft biztonsági kódelemzés bővítményének három utófeldolgozási feladata is van. Ezek a feladatok segítenek a biztonsági eszköz feladatainak elemzésében. Amikor hozzáadjuk egy folyamathoz, ezek a feladatok általában az összes többi eszközfeladatot követik.

### <a name="publish-security-analysis-logs"></a>Biztonsági elemzési naplók közzététele

A Biztonsági elemzési naplók közzététele buildfeladat megőrzi a build során futtatott biztonsági eszközök naplófájljait. Elolvashatja ezeket a naplókat a vizsgálat és nyomon követés.

A naplófájlokat közzéteheti az Azure Artifacts.You can publish the log files to Azure Artifacts as a .zip file. A privát buildelőügynökből is átmásolhatja őket egy elérhető fájlmegosztásra.

### <a name="security-report"></a>Biztonsági jelentés

A biztonsági jelentés build feladata elemzi a naplófájlokat. Ezeket a fájlokat a build során futó biztonsági eszközök hozták létre. A buildfeladat ezután egyetlen összegző jelentésfájlt hoz létre. Ez a fájl az elemző eszközök által talált összes problémát mutatja.

Ezt a feladatot beállíthatja úgy, hogy az eredményeket jelentse az adott eszközökhöz vagy az összes eszközhöz. Azt is megadhatja, hogy milyen problémaszintet jelentsen, például csak a hibákat, illetve a hibákat és a figyelmeztetéseket is.

### <a name="post-analysis-build-break"></a>Elemzés utáni (buildszünet)

Az elemzés utáni build feladattal olyan buildtörést adhat be, amely szándékosan hibás buildet eredményez. Buildtörést kell beadnia, ha egy vagy több elemzési eszköz problémákat jelent a kódban.

Beállíthatja ezt a feladatot úgy, hogy megszakítsa az adott eszközök vagy az összes eszköz által talált problémák összeállítását. A talált problémák, például hibák vagy figyelmeztetések súlyossága alapján is konfigurálható.

>[!NOTE]
>A tervezés szerint minden létrehozási feladat sikeres, ha a feladat sikeresen befejeződik. Ez akkor is igaz, függetlenül attól, hogy egy eszköz problémákat talál-e, így a build az összes eszköz futtatásának engedélyezésével a befejezésig futtatható.

## <a name="next-steps"></a>További lépések

A Microsoft biztonsági kódelemzésének üzembe helyezésével és telepítésével kapcsolatos tudnivalókért tekintse meg [a Bevezetés és telepítés útmutatóját.](security-code-analysis-onboard.md)

A buildfeladatok konfigurálásáról további információt a [konfigurációs útmutatóban](security-code-analysis-customize.md) vagy a [YAML konfigurációs útmutatóban talál.](yaml-configuration.md)

Ha további kérdései vannak a kiterjesztéssel és a kínált eszközökkel kapcsolatban, tekintse meg [a GYIK oldalt.](security-code-analysis-faq.md)
