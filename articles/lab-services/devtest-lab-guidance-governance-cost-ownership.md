---
title: Költség- és tulajdonjog kezelése az Azure DevTest Labs ben
description: Ez a cikk olyan információkat tartalmaz, amelyek segítenek a költségekre való optimalizálásban és a tulajdonjog okba igazítása a környezetben.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: b82d338f85f1b43712296ac7f27bdad55f8f1919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74561666"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Az Azure DevTest Labs infrastruktúrájának irányítása – A költségek és a tulajdonjog kezelése
A költség és a tulajdonjog elsődleges szempont, ha figyelembe veszi a fejlesztési és tesztelési környezetek építését. Ebben a szakaszban olyan információkat talál, amelyek segítenek a költségekre való optimalizálásban és a tulajdonjog nak a környezetben való igazításában.

## <a name="optimize-for-cost"></a>Optimalizálás a költségekre

### <a name="question"></a>Kérdés
Hogyan optimalizálhatom a költségeket a DevTest Labs környezetben?

### <a name="answer"></a>Válasz
A DevTest Labs számos beépített szolgáltatása segít a költségek optimalizálásában. Tekintse meg [a költséggazdálkodást, a küszöbértékeket](devtest-lab-configure-cost-management.md) [és a házirendekkel](devtest-lab-set-lab-policy.md) kapcsolatos cikkeket a felhasználók tevékenységeinek korlátozásához. 

A fejlesztői tesztlabs fejlesztési és tesztelési számítási feladatok, érdemes lehet a [vállalati fejlesztési/tesztelési előfizetési előny](https://azure.microsoft.com/offers/ms-azr-0148p/), a nagyvállalati szerződés részeként. Alternatív megoldásként, ha a Pay as You Go ügyfél, érdemes megfontolni a [Pay-as-you-go DevTest ajánlat](https://azure.microsoft.com/offers/ms-azr-0023p/).

Ez a megközelítés számos előnnyel jár:

- Speciális alacsonyabb fejlesztési/tesztelési díjak Windows virtuális gépeken, felhőszolgáltatásokon, HDInsight-alkalmazásokon, App Service-alkalmazásokon és logikai alkalmazásokon
- Nagyvállalati szerződés (EA) díjai más Azure-szolgáltatásokra
- Exkluzív fejlesztői/tesztelési képek elérése a Galériában, beleértve a Windows 8.1-et és a Windows 10-et
 
Csak az aktív Visual Studio-előfizetők (standard előfizetések, éves felhőalapú előfizetések és havi felhőalapú előfizetések) használhatják a nagyvállalati fejlesztési és tesztelési előfizetésen belül futó Azure-erőforrásokat. A végfelhasználók azonban visszajelzést adhatnak az alkalmazáshoz, vagy elvételi teszteket végezhetnek. Az előfizetésen belüli erőforrások használata az alkalmazások fejlesztésére és tesztelésére korlátozódik, és nem kínálnak rendelkezésre állási garanciát.

Ha úgy dönt, hogy a DevTest ajánlat használatát, vegye figyelembe, hogy ez az előny kizárólag az alkalmazások fejlesztéséhez és teszteléséhez. Az előfizetésen belüli használat nem tartalmaz pénzügyileg támogatott SLA-t, kivéve az Azure DevOps és a HockeyApp használatát.

## <a name="define-a-role-based-access-across-your-organization"></a>Szerepköralapú hozzáférés definiálása a szervezeten belül
### <a name="question"></a>Kérdés
Hogyan definiálhatom a szerepköralapú hozzáférés-vezérlést a DevTest Labs környezetekhez annak érdekében, hogy az informatikai szolgáltatások szabályozhassák, miközben a fejlesztők/tesztek el tudják végezni a munkájukat? 

### <a name="answer"></a>Válasz
Széles minta létezik, azonban a részletek a szervezettől függnek.

A központi informatikai szolgáltatásoknak csak azt kell birtokolniuk, ami szükséges, és lehetővé kell tenniük, hogy a projekt- és alkalmazáscsapatok a szükséges szintű ellenőrzéssel rendelkezhessenek. Ez általában azt jelenti, hogy a központi informatikai a központi informatikai tulajdonosa az előfizetés, és kezeli az alapvető informatikai funkciók, például a hálózati konfigurációk. Az előfizetés **tulajdonosainak** kicsinek kell lennie. Ezek a tulajdonosok szükség esetén további tulajdonosokat jelölhetnek ki, vagy előfizetési szintű házirendeket alkalmazhatnak, például "Nincs nyilvános IP".

Előfordulhat, hogy a felhasználók egy részhalmaza, amely hozzáférést igényel egy előfizetés, például a Tier1 vagy tier 2 támogatás. Ebben az esetben azt javasoljuk, hogy adjon ezeknek a felhasználóknak a **közreműködői** hozzáférést, hogy azok kezelni tudják az erőforrásokat, de ne biztosítsanak felhasználói hozzáférést vagy módosítsák a házirendeket.

A DevTest Labs erőforrás tulajdonosa a tulajdonosok, akik közel vannak a projekt/alkalmazás csapat. Ez azért van, mert megértik a követelményeket a gépek és a szükséges szoftverek tekintetében. A legtöbb szervezetben a DevTest Labs erőforrás tulajdonosa általában a projekt/fejlesztési vezető. Ez a tulajdonos kezelheti a felhasználókat és a szabályzatokat a tesztkörnyezetben, és kezelheti a DevTest Labs környezetben lévő összes virtuális gépet.

A projekt/alkalmazás csapat tagjai hozzá kell adni a DevTest Labs-felhasználók szerepkör. Ezek a felhasználók virtuális gépeket hozhatnak létre (a labor- és előfizetési szintű szabályzatokkal összhangban). Saját virtuális gépeiket is kezelhetik. Nem kezelhetik a más felhasználókhoz tartozó virtuális gépeket.

További információ: [Azure enterprise scaffold – előíró előfizetés-cégirányítási](/azure/architecture/cloud-adoption/appendix/azure-scaffold) dokumentáció.


## <a name="next-steps"></a>További lépések
Lásd: [Vállalati szabályzat és megfelelőség](devtest-lab-guidance-governance-policy-compliance.md).
