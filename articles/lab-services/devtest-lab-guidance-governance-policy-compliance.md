---
title: Irányítás az Azure DevTest Labs-infrastruktúra
description: Ez a cikk az Azure DevTest Labs-infrastruktúra cégirányítási útmutatást nyújt.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: spelluru
ms.openlocfilehash: 8165efaa10eb31410f00dfa9bfffb2c02d7396ae
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872329"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>Irányítás az Azure DevTest Labs-infrastruktúra - vállalati házirend és megfelelőség
Ez a cikk útmutatást nyújt szabályozó vállalati házirend és megfelelőség az Azure DevTest Labs-infrastruktúrához. 

## <a name="public-vs-private-artifact-repository"></a>Nyilvános és privát összetevőtárban

### <a name="question"></a>Kérdés
Mikor célszerű használni egy szervezet olyan nyilvános összetevőtárral és a DevTest Labs szolgáltatásban létrehozott privát összetevőtárban?

### <a name="answer"></a>Válasz
A [nyilvános összetevőtárral](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) biztosít egy kezdeti szoftvercsomagok leggyakrabban használt. Segít a gyors üzembe helyezési nélkül teljesítheti a kitűzött időpontja reprodukálnia általános fejlesztői eszközöket és beépülő modulok. Kiválaszthatja, hogy a saját privát tárház üzembe helyezéséhez. Nyilvános és a egy privát tárház párhuzamosan is használhatja. Dönthet úgy is, a nyilvános tárházban letiltása. A feltételek, és üzembe helyezése a privát tárházat kell meghatározni a következő kérdések és szempontok szerint:

- Rendelkezik a szervezete olyan követelménnyel rendelkezik vállalati licencelt szoftvereket a DevTest Labs-ajánlatába részeként? Ha a válasz Igen, a privát tárházat kell létrehozni.
- Nem a szervezet egyéni szoftver, amely tartalmaz egy adott művelethez, amely részeként szükség van az általános üzembe helyezési folyamat fejlesztése? Ha a válasz Igen, a privát tárházat kell telepíteni.
- Ha a szervezet cégirányítási házirend szükséges elkülönítés, és külső tárházak nem tartoznak a szervezet által közvetlen kezelés, privát összetevőtárban kell telepíteni. Ez a folyamat részeként egy kezdeti másolatot készít a nyilvános tárházban másolható és a privát tárházban integrálva. A nyilvános tárházban lehet letiltani, majd, hogy nem a szervezeten belül férhetnek azt többé. Ez a megközelítés arra kényszeríti a szervezeten belüli összes felhasználó csak egy adattárba, amely jogosult a szervezet rendelkezik, és minimalizálja a konfigurációs csúszásokat.

### <a name="single-repository-or-multiple-repositories"></a>Adattárba, vagy több adattárak 

### <a name="question"></a>Kérdés
Egy szervezet tervezése egy adattárba vagy több tárházak engedélyezése?

### <a name="answer"></a>Válasz
A szervezet teljes irányítás és a konfiguráció-kezelési stratégia részeként azt javasoljuk, hogy egy központi tárházban használja. Több adattárak használata esetén a silók a nem felügyelt szoftver az idő függvényében előfordulhat, hogy válnak. Egy központi tárházban, a több csapat felhasználhatja az adattárból projektjeikhez tartozó összetevőket. Szabványügyi szervezet, a biztonság, a könnyű kezelés kikényszeríti, és kiküszöböli a párhuzamos erőfeszítések. A forrásadattárakból részeként a következő műveletek használata ajánlott eljárások a hosszú távú felügyeleti és népszerűsítését:

- Az Azure-Adattárakkal társítása az azonos Azure Active Directory-bérlő az Azure-előfizetés által használt a hitelesítéshez és engedélyezéshez.
- Hozzon létre egy csoportot nevű **DevTest Labs minden fejlesztő** az Azure Active Directoryban, amely központilag felügyelt. Minden fejlesztő, aki összetevő fejlesztési hozzájárul az ebbe a csoportba kell elhelyezni.
- Az azonos Azure Active Directory-csoport hozzáférést biztosít az Azure-Adattárakkal adattárba, és a labor létrehozása a használható.
- Az Azure-Adattárakkal Elágaztatás és elágaztatási használandó külön egy a-development tárházhoz az elsődleges éles adattárból. Tartalom csak hozzáadódik a főágba irányuló lekéréses kérelmet a megfelelő kód áttekintése után. A kód a felülvizsgáló jóváhagyja a módosítást, miután érdeklődő fejlesztő, aki felelős karbantartása a master ággal, egyesíti a frissített kóddal. 

## <a name="corporate-security-policies"></a>Vállalati biztonsági szabályzatok

### <a name="question"></a>Kérdés
Hogyan biztosíthatja a szervezet vállalati biztonsági házirendek legyenek érvényben?

### <a name="answer"></a>Válasz
Egy szervezet előfordulhat, hogy érhet el, a következő műveletek végrehajtásával:

1. Fejlesztés és egy átfogó biztonsági házirend közzététele. A szabályzat szabályainak használati feltételek a használatával társított articulates szoftver, a felhőbeli eszközöket. Azt is meghatározza, milyen jól sérti a szabályzatot. 
2. Egyéni rendszerkép, az egyéni összetevők és a egy folyamatot, amely lehetővé teszi, hogy az active Directoryval meghatározott biztonsági tartományon belüli vezénylési fejleszthet. Ez a megközelítés kikényszeríti a céges határhálózaton, és beállítja a környezeti vezérlőknek az alábbiakat közös csoportját. Ezek a vezérlők a környezetre leselkedő fejlesztő során figyelembe veheti a fejlesztés és a egy biztonságos fejlesztési életciklus során kövesse az általános folyamat részeként. A cél is, amely nem túl szigorú környezetet biztosít a május akadályozzák a fejlesztési, de ésszerű csoportját. A csoportházirendek a szervezeti egység (OU), amely tartalmazza a virtuális gépeiről, éles környezetben található összes csoportházirendek egy része lehet. Másik lehetőségként el megfelelően az összes azonosított kockázatok csökkentése, egy további csoportot.

## <a name="data-integrity"></a>Adatok sértetlensége

### <a name="question"></a>Kérdés
Hogyan biztosíthatja a szervezet adatok integritásának megőrzése, győződjön meg arról, hogy a távoli eljáráshívás a fejlesztők nem tudja eltávolítani a kód vagy vezeti be a kártevők vagy nem jóváhagyott szoftverek?

### <a name="answer"></a>Válasz
Nincsenek külső tanácsadók, alvállalkozók és az alkalmazottak, amelyeket a távoli eljáráshívás DevTest Labs-környezetben együttműködéshez a fenyegetés ellenőrzési több réteget. 

Ahogy korábban is hangsúlyoztuk, az első lépés egy használati feltételei elkészíteni, és definiálva, amely egyértelműen ismerteti a következmények, ha valaki megsértik a házirendet kell rendelkeznie. 

Az első réteg azoknak a vezérlőelemeknek a távoli hozzáféréshez, amely nem kapcsolódik közvetlenül a labor VPN-kapcsolaton keresztül egy távoli hozzáférési házirend alkalmazása. 

A második réteg vezérlők, hogy a alkalmazni a csoportházirend-objektumok, amelyek megakadályozzák a másolás, és illessze be a távoli asztalon keresztül egy készletét. A hálózati házirend nem engedélyezi a kimenő a környezetben, például az FTP és RDP szolgáltatásokat a környezetén valósíthatja meg. Felhasználó által meghatározott útválasztás kényszerítheti az összes Azure hálózati forgalom vissza a helyszíni, de az Útválasztás nem sikerült fiókot előfordulhat, hogy engedélyezi az adatok feltöltését, kivéve, ha megvizsgálja a tartalom és a munkamenetek proxyn keresztüli ellenőrzött URL-címeket. Nyilvános IP-címek támogatása a DevTest Labs szolgáltatásban, hogy ne engedélyezze az adatközponthíd-képzés egy külső hálózati erőforrás a virtuális hálózaton belül korlátozott lehet.

Végső soron ugyanolyan típusú, a korlátozások a szervezeten belül, amely a cserélhető adathordozó vagy a külső URL-címek, amely fogadni tudta egy bejegyzést a tartalom minden lehetséges módszert is figyelembe kell alkalmazni kell. Tekintse meg a biztonsági szakembereknek, tekintse át és a egy biztonsági házirend bevezetése. További javaslatokért lásd: [Microsoft Kibertámadások biztonsági](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).


## <a name="next-steps"></a>További lépések
Lásd: [alkalmazás áttelepítését és integrálását](devtest-lab-guidance-governance-application-migration-integration.md).
