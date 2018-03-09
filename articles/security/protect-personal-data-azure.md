---
title: "A Microsoft Azure-ban a személyes adatok védelme |} Microsoft Docs"
description: "Ez a cikk segítséget nyújtanak a személyes adatok védelme, és felel meg az általános adatok védelmi szabályozás (GDPR) Azure használatával"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 741fb17be315faacef6483cbaaa565136622cb45
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="protect-personal-data-in-microsoft-azure"></a>A Microsoft Azure-ban a személyes adatok védelme

Ez a cikk be, amelyek segítenek cikkek személyes adatok védelme az Azure biztonsági technológiák és szolgáltatások segítségével. Ez az a kulcsfontosságú követelmény az számos vállalati és iparági megfelelési és irányítási kezdeményezések. Az adatsorozat cikkek található információk segítségével például felel meg az általános adatok védelmi szabályozás (GDPR). A forgatókönyv probléma utasítás és a vállalati célok ide tartoznak.

## <a name="scenario-and-problem-statement"></a>Forgatókönyv és probléma fényében

Egy nagy körutazás cég, az Amerikai Egyesült Államokban telephelyének bővíti a műveleteket a mediterrán, Adriai, és Balti tengerek, valamint a Brit-szigetekre útvonalak biztosítani. Támogatás ezeket, több kisebb körutazás sorok Olaszország, Németországban, Dánia és az Egyesült szerzett

A vállalat a Microsoft Azure használ a felhőben tárolt vállalati adatokat. Ez lehet, hogy olyan felhasználói és/vagy alkalmazotti információkat, mint például:

- címek
- telefonszámok
- azonosító számokat
- Hitelkártya-adatokhoz

A vállalati adatok azokat, hogy szükség lenne rá részlegek számára elérhető tétele közben kell védeni az ügyfél és az alkalmazottak adatok védelme. (például a bérszámfejtési és foglalások részlegek)

## <a name="company-goals"></a>Vállalati célok 

- Adatforrások, amelyek tartalmazzák a személyes adatok titkosítását, ha a felhőben található.

- Az átvitel során titkosítva van a személyes adatok egyik helyről egy másikra. Ez érvényét veszti, ha az adatok áramlanak a virtuális hálózaton vagy az interneten keresztül a vállalati adatközpontban és az Azure felhőalapú között.

- Személyes adatok sértetlenségét és bizalmasságát védik a jogosulatlan hozzáférés erős Identitáskezelés és hozzáférés-vezérlési technológiák.

- Személyes adatok védve van az adatok biztonsági szabályok megsértésére felügyelet biztonsági réseket és a fenyegetések kitettség.

- Biztonsági állapotát az Azure-szolgáltatásokat, amely tárolja, és a személyes adatok megfelelőségét ellenőrizni kell, hogy azonosítsa a finomhangolási lehetőségeket, amelyekkel jobban megvédheti a személyes adatok.

## <a name="data-protection-guidance"></a>Data protection útmutató

A következő cikkek műszaki útmutató útmutatást, amelyek segítségével a fent felsorolt személyes adatok védelem céljainak eléréséhez tartalmaznak:

- [Az Azure titkosítási technológiák](protect-personal-data-at-rest.md)

- [Az Azure titkosítási technológiák](protect-personal-data-in-transit-encryption.md)

- [Az Azure identitások és hozzáférések technológiák](protect-personal-data-identity-access-controls.md)

- [Az Azure hálózati biztonsági technológiák](protect-personal-data-network-security.md)

- [Azure Security Center](protect-personal-data-azure-security-center.md)



## <a name="next-steps"></a>További lépések

- [Az Azure biztonsági információk hely](https://aka.ms/AzureSecInfo)

- [A Microsoft biztonsági és adatkezelési központ](https://www.microsoft.com/TrustCenter/default.aspx)

- [Azure Security Center](https://azure.microsoft.com/services/security-center/)

- [Az Azure Security csapat blogja](https://www.azuresecurityorg)

- [Azure.com webhelyre Blog - biztonsági](https://azure.microsoft.com/blog/topics/security/)
