---
title: Microsoft biztonsági kód elemzésének kiadásai
description: Ez a cikk a Microsoft biztonsági kód elemzése bővítmény közelgő kiadásait ismerteti
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/24/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: d4281d3b6132e551283a71cd1801ef462fbfc68c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146131"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Microsoft biztonsági kód elemzésének kiadásai és ütemterve

A Microsoft biztonsági kód Analysis Team a Fejlesztői támogatás partnerrel együttműködve büszkén jelenti be, hogy a legújabb és a közelgő fejlesztéseket a MSCA-bővítményben mutatjuk be. Tekintse meg az alábbi ütemtervet.

![Kiadások](./media/security-code-analysis-releases/releases.png)

## <a name="credential-scanner-v20-released-in-april-2020"></a>Hitelesítőadat-olvasó v 2.0: megjelent április 2020

### <a name="innovations--improvements"></a>Innovációk & fejlesztések

- **Alapszintű motor**

   - 25%-os teljesítmény-frissítés a közel lineáris futási idővel
   - Kontextus/bizonyítékon alapuló keresés és rangsorolás a nagyobb pontosság érdekében
   - Az általános jelszó-észlelések és a megfelelő logika fejlesztése a nyilvánvaló helyőrzők esetében (például fakePassword)

- **Lefedettség** – a 25 + titkos típusok támogatása, beleértve a következő kért legfelső szintű támogatást:

   - Háló fiók tanúsítványának jelszava
   - Ügyfél titkos/API-kulcsa
   - HTTP-engedélyezési fejléc
   - Amazon S3-ügyfél titkos kulcs elérési kulcsa
   - Ügyfél-hozzáférési jogkivonat Azure Active Directory
   - Azure Function Master/API-kulcs
   - Power BI hozzáférési kulcs
   - Azure Resource Manager sablon jelszavának mintája

- **Kimenetek**

   - A SARIF 2,1 és a CSV-fájl kimeneti fájlformátumának támogatása

## <a name="binskim-v160-released-in-april-2020"></a>BinSkim v 1.6.0: megjelent április 2020

### <a name="improvements"></a>Fejlesztései

- SZOLGÁLTATÁS: frissítés a Final SARIF v2-re (2.1.16 verzió). Ez lehetővé teszi, hogy az eredmények gyorsítótárazása a parancssorból történő továbbításkor történjen, jelentős teljesítmény-javulást jelent, amikor rekurzív módon elemzi a címtárakat a vizsgálati célok több példányával.
- HIBAJAVÍTÁS: a BA2021 helyesírásának javítása. DoNotMarkWritableSectionsAsExecutable kimenete.
- TELJESÍTMÉNY: a PDB betöltésének megszüntetése a felügyelt szerelvények összes nem vegyes üzemmódja számára, beleértve az IL-függvénytárat (az idő előtt lefordított) bináris fájlokat.
- HAMIS negatív javítás: annak ellenőrzése, hogy egy, a bináris fájl mellé helyezett PDB az elemzés alatt álló bináris ÉRTÉKkel egyezik-e.
- SZOLGÁLTATÁS: adja meg a--local-Symbol-könyvtárak argumentumot a további (helyi, nem szimbólum-kiszolgáló) PDB kikeresési helyeinek megadásához.
- HAMIS pozitív javítás: hagyja ki a PDB-alapú elemzést a generált .NET Core Native bootstrap exe-re (amely nem felhasználó által irányítható kód).

## <a name="whats-next-in-fy20"></a>Mi a következő lépés a FY20-ben?

- Java Security Analysis Tool
- Python biztonsági elemzési eszköz
- Az írógéppel és a JavaScripthez tartozó

## <a name="next-steps"></a>További lépések

A Microsoft biztonsági kódok elemzésének bevezetésével és telepítésével kapcsolatos útmutatásért tekintse meg a bevezetési [és telepítési útmutatót](security-code-analysis-onboard.md).

Ha további kérdései vannak a bővítményről és a rendelkezésre álló eszközökről, tekintse meg a [Gyakori kérdések oldalát](security-code-analysis-faq.md).
