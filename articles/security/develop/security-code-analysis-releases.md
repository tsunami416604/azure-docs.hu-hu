---
title: A Microsoft biztonsági kódelemzési kiadásai
description: Ez a cikk a Microsoft Security Code Analysis bővítmény közelgő kiadásait ismerteti
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/14/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: a07ce7437d664baca0cfdc310dbc2631f41fdbcc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81462034"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>A Microsoft biztonsági kódelemzési kiadásai és ütemterve

A Microsoft Security Code Analysis csapata a fejlesztői támogatással együttműködve büszkén jelenti be az MSCA-bővítmény legújabb és közelgő fejlesztéseit. Kérjük, olvassa el az alábbi ütemtervet.

![Kiadások](./media/security-code-analysis-releases/releases.png)

## <a name="credential-scanner-v20-released-on-april-1-2020"></a>Credential Scanner v2.0: Megjelent április 1, 2020

### <a name="innovations--improvements"></a>Újítások & fejlesztések

- **Core motor**

   - Átlagos teljesítményfrissítés 25%-kal, közel lineáris futási idővel
   - Kontextuson/bizonyítékokon alapuló keresés és rangsorolás a nagyobb pontosság érdekében
   - Az általános jelszóészlelésés és a nyilvánvaló helyőrzők egyeztetési logikájának fejlesztései (például fakePassword)

- **Lefedettség** - Támogatás 25+ titkos típusok, beleértve a következő top kért:

   - Hálófiók tanúsítványának jelmondata
   - Ügyféltitkos/API-kulcs
   - HTTP engedélyezési fejléc
   - Amazon S3 ügyfél titkos hozzáférési kulcs
   - Azure Active Directory ügyfél-hozzáférési jogkivonat
   - Azure függvény főkiszolgáló/API-kulcs
   - Power BI access kulcs
   - Az Azure Resource Manager sablon jelszómintája

- **Kimenetek**

   - A SARIF 2.1 és CSV fájlkimeneti fájlformátumok támogatása

## <a name="binskim-v160-to-be-released-on-april-2020"></a>BinSkim v1.6.0: Fel kell szabadítani április 2020

### <a name="improvements"></a>Fejlesztések

- FUNKCIÓ: Frissítés a végső SARIF v2 -re (2.1.16-os verzió). Ez lehetővé teszi az eredmények gyorsítótárazását a parancssori --kikeresések esetén, ami jelentős teljesítményjavulást jelent, ha rekurzívmódon elemzi a könyvtárakat a bevizsgálati célok több példányával.
- HIBAJAVÍTÁS: Hibaelírás javítása a BA2021-ben. DoNotMarkWritableSectionsAsExecutable kimenet.
- TELJESÍTMÉNY: Szüntesse meg az EREDETI-dokumentum betöltését a felügyelt szerelvények összes nem vegyes üzemmódjában, beleértve az IL-könyvtárat (előre összeállított) bináris fájlokat.
- HAMIS NEGATÍV JAVÍTÁS: Ellenőrizze, hogy egy bináris mellett elhelyezett PDB valóban megegyezik-e az elemzés alatt álló binárissal.
- FUNKCIÓ: Adjon meg --local-symbol-könyvtárak argumentumot további (helyi, nem szimbólum-kiszolgáló) PDB-kitekintési helyek megadásához
- HAMIS POZITÍV JAVÍTÁS: A létrehozott .NET magnatív bootstrap exe PDB-alapú elemzésének kihagyása (amely nem felhasználó által szabályozható kód).

## <a name="whats-next-in-fy20"></a>Mi a következő lépés a FY20-ban?

- Java biztonsági elemző eszköz
- Python biztonsági elemző eszköz
- ES Lint helyett TS Lint typescript és JavaScript

## <a name="next-steps"></a>További lépések

A Microsoft biztonsági kódelemzésének üzembe helyezésével és telepítésével kapcsolatos tudnivalókért tekintse meg [a Bevezetés és telepítés útmutatóját.](security-code-analysis-onboard.md)

Ha további kérdése van a kiterjesztéssel és a kínált eszközökkel kapcsolatban, tekintse meg [a GYIK oldalt.](security-code-analysis-faq.md)
