---
title: Tollas tesztelés | Microsoft Docs
description: Ez a cikk áttekintést nyújt a penetrációs tesztelési folyamatról, valamint arról, hogy az Azure-infrastruktúrában futó alkalmazások hogyan teljesítik a legrészletesebb műveleteket.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2018
ms.author: terrylan
ms.openlocfilehash: db6e25b6304ee9ac41ca95d5a3a6eac0e91eb41b
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87287773"
---
# <a name="penetration-testing"></a>Behatolás tesztelése
Az Azure az alkalmazások teszteléséhez és üzembe helyezéséhez való használatának egyik előnye, hogy gyorsan lekérheti a környezetek létrehozását. Nem kell aggódnia az igényléssel, a beszerzéssel és a saját helyszíni hardverek üzemeltetésével és halmozásával kapcsolatban.

Ez nagyszerű – de továbbra is meg kell győződnie arról, hogy a szokásos biztonsági átvilágítás végrehajtása folyamatban van. Az egyik legvalószínűbb, hogy a penetráció teszteli az Azure-ban üzembe helyezett alkalmazásokat.

Lehet, hogy már tudja, hogy a Microsoft [Az Azure-környezetének penetrációs tesztelését](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)végzi. Ez segít az Azure-beli fejlesztése terén.

Nem vesszük figyelembe az alkalmazás tesztelését, de tisztában vagyunk vele, hogy a saját alkalmazásaiban kell majd tesztelést végeznie. Ez jó dolog, mert az alkalmazások biztonságának növelése révén biztonságosabbá teheti a teljes Azure-ökoszisztémát.

2017. június 15-től a Microsoft már nem igényel előzetes jóváhagyást az Azure-erőforrásokra vonatkozó behatolási teszt elvégzéséhez. Azok az ügyfelek, akik formálisan szeretnék dokumentálni a jövőbeli penetrációs tesztelési folyamatokat, Microsoft Azure az [Azure-szolgáltatások elterjedtségének teszteléséről szóló értesítési űrlapot](https://portal.msrc.microsoft.com/en-us/engage/pentest)kell kitölteniük. Ez a folyamat csak Microsoft Azurehoz kapcsolódik, és nem alkalmazható más Microsoft Cloud szolgáltatásra.

>[!IMPORTANT]
>A Microsoft a toll-tesztelési tevékenységek bejelentése után már nem szükséges, hogy az ügyfeleknek továbbra is meg kell felelniük az összevonáshoz [Microsoft Cloud egységes penetráció tesztelési szabályainak](https://technet.microsoft.com/mt784683).

A végrehajtható szabványos tesztek a következők:

* Tesztek a végpontokon az [Open Web Application Security Project (OWASP) Top 10 biztonsági rések](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project) felfedéséhez
* A végpontok [fuzz Testing tesztelése](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)
* A végpontok [portjának vizsgálata](https://en.wikipedia.org/wiki/Port_scanner)

## <a name="next-steps"></a>További lépések

- Ha az Microsoft Azure-ban üzemeltetett alkalmazásaira vonatkozóan szeretne formálisan dokumentálni egy közelgő behatolási tesztet, akkor a [bevezetési tesztelési szabályokkal](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2) kapcsolatos további információkért tekintse át a részvételi tesztre vonatkozó szabályokat, és töltse ki a tesztelési értesítés
