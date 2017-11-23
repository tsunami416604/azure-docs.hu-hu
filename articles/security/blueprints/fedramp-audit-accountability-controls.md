---
title: "FedRAMP Azure tervezetének Automation - naplózás és elszámolási kötelezettségéről szóló"
description: "Webalkalmazások FedRAMP – a naplózási és elszámolási kötelezettségéről szóló"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: c5858e07-ca74-4526-b31f-e6b4e55bb594
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 83ef9cbb7652bf128d7758237a8e6fbeed6c6565
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2017
---
# <a name="audit-and-accountability-au"></a>Naplózási és elszámolási kötelezettségéről szóló (AU)

> [!NOTE]
> Ezek az intézkedések határozzák meg NIST és az Egyesült Államok Kereskedelmi Minisztériuma a NIST különleges közlemény 800-53-as verziójának 4 részeként. Tekintse meg a NIST 800-53 fordított 4 tesztelési útmutató a vezérlők és eljárásokat olvashat.

## <a name="nist-800-53-control-au-1"></a>A NIST 800-53 vezérlő AU-1

#### <a name="audit-and-accountability-policy-and-procedures"></a>Naplózási és elszámolási kötelezettségéről szóló házirend- és eljárások

**AU-1** a szervezet házon belül fejlesztett alkalmazásokra, dokumentumokat, és hogy terjeszti [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök] egy naplózási és elszámolási kötelezettségéről szóló házirend célja, hatókör, szerepkörök, feladatok, felügyeleti kötelezettségvállalás orvosló a koordinációs szervezeti entitásokat, és a megfelelőségi; és eljárásokat azzal, hogy a naplózási és elszámolási kötelezettségéről szóló házirend és a kapcsolódó naplózási és elszámolási kötelezettségéről szóló vezérlők; végrehajtásának megkönnyítése ellenőrzi, és frissíti az aktuális naplózási és elszámolási kötelezettségéről szóló házirend [hozzárendelés: szervezet által meghatározott gyakoriság]; és elszámolási kötelezettségéről szóló eljárások [hozzárendelés: szervezet által meghatározott gyakoriság].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű naplózási és elszámolási kötelezettségéről szóló házirend és eljárások a vezérlő megoldására elegendő lehet. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-au-2a"></a>A NIST 800-53 vezérlő AU-2.a

#### <a name="audit-events"></a>Események naplózása

**AU-2.a** a szervezet megállapítja, hogy az adatokat a rendszer a következő események naplózása képes: [hozzárendelés: szervezet által meghatározott események].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének naplózási funkció Azure figyelő és a OMS Naplóelemzés szolgáltatást biztosítja. Az Azure biztosít, telepített erőforrások társított tevékenységre vonatkozó részletes naplókat. Ezek és az operációs rendszer szintű naplók Naplóelemzési által gyűjtött és az OMS-tárház tárolja. A Naplóelemzési ad eredményül a naplózási adatok a megoldás által telepített erőforrások között, és annak a felhasználói telepített webalkalmazás. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-au-2b"></a>A NIST 800-53 vezérlő AU-2.b

#### <a name="audit-events"></a>Események naplózása

**AU-2.b** a szervezet biztonsági naplózási függvény koordinálja az egyéb szervezeti entitások igénylő naplózási kapcsolatos információ javítása érdekében a kölcsönös támogatási és események kiválasztását segítségével.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél hivatkozhatnak egy meglévő vállalati szintű folyamat, amely meghatározza egy naplózható esemény. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-au-2c"></a>A NIST 800-53 vezérlő AU-2.c

#### <a name="audit-events"></a>Események naplózása

**AU-2.c** a szervezet egy profilkategóriák biztosít miért az események minősülnek megfelelő biztonsági incidensek után-az-tény vizsgálatok támogatásához.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének által naplózott eseményeket tartalmazza események bekövetkezésekor meghatározásához elegendő információt, az esemény forrását, az esemény, és más részletes információkat, amely támogatja a biztonsági események vizsgálat eredményeit. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-au-2d"></a>A NIST 800-53 vezérlő AU-2.d

#### <a name="audit-events"></a>Események naplózása

**AU-2.d** a szervezet határozza meg, hogy vannak-e a következő események naplózandó információk rendszerből: [hozzárendelés: szervezet által meghatározott naplózott események (a AU-2/a. meghatározott események részhalmazát) együtt gyakorisága (vagy minden azonosított esemény naplózása Situation requiring)].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének által naplózott eseményeket foglalja bele azokat rendszervizsgálati módban levő telepített erőforrások, az operációs rendszer szintű naplókat, az Active Directory-naplók Azure tevékenységi naplóit, és az SQL Server naplózza. Az ügyfelek kritikus igényeihez naplózandó további események is kiválaszthat. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-au-2-3"></a>A NIST 800-53 vezérlő AU-2 (3)

#### <a name="audit-events--reviews-and-updates"></a>Események naplózása |} Ellenőrzések és frissítések

**AU-2 (3)** a szervezet ellenőrzi, és frissíti a naplózott események [hozzárendelés: szervezet által meghatározott gyakoriság].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél lehet, hogy egy meglévő vállalati szintű időszakosan ellenőrizhetjük támaszkodjon és naplózott eseményeket meghatározott számú folyamatot nem lehet frissíteni. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-au-3"></a>A NIST 800-53 vezérlő AU-3

#### <a name="content-of-audit-records"></a>Az auditálási rekordok tartalom

**AU-3** az információs rendszer állít elő, auditálási rekordok tartalmazó megállapítja, milyen típusú esemény történt, amikor az esemény lépett fel, ahol az esemény történt, az esemény forrását, az esemény eredményét, illetve bármely identitás egyéni felhasználók számára, vagy az eseményhez tartozó témákat tárgyalja.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének Azure, a Windows Server és az SQL Server beépített naplózási képességeket támaszkodik. Ezek naplózási megoldások rögzítési naplózási rekordokat részletességgel megfelelnek ennek a vezérlőnek. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-au-3-1"></a>A NIST 800-53 vezérlő AU-3 (1)

#### <a name="content-of-audit-records--additional-audit-information"></a>Az auditálási rekordok tartalom |} Kiegészítő naplózási információk

**AU-3 (1)** az információs rendszer állít elő, a következő további információkat tartalmazó auditálási rekordok: [hozzárendelés: szervezet által meghatározott további, részletes információkat].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Azure tevékenységnapló események naplózási információk több mint 20 típusú mezőket tartalmazó részletes séma használja. Tevékenységnapló, mellett az Azure tervezetének telepíti az OMS Szolgáltatáshoz, amely támogatja az adatforrások, például Windows-naplók, Linux naplókat, Azure diagnosztikai naplók és felhasználói naplók érdekében számos Naplóelemzési megoldás.  |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-au-3-2"></a>A NIST 800-53 vezérlő AU-3 (2)

#### <a name="content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>Az auditálási rekordok tartalom |} A tervezett naplózási rekord tartalom központi kezelésére

**AU-3 (2)** a információk rendszer biztosít a központosított kezelését és konfigurálását a tartalmat, hogy az auditálási rekordok által generált rögzíthetők [hozzárendelés: szervezet által megadott információk rendszerösszetevők].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének által telepített összes virtuális gépet a telepített Active Directory-tartományhoz csatlakozik. Minden tartományhoz csatlakozó virtuális gépek megvalósításához olyan csoportházirenddel, amely beállítható úgy, hogy az operációs rendszer szintű naplózási rendszer konfigurálásának központi kezelését. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-au-4"></a>A NIST 800-53 vezérlő AU-4

#### <a name="audit-storage-capacity"></a>Naplózási tárolási kapacitás

**AU-4** a szervezet foglal le a naplózási rekord tárolási kapacitás összhangban [hozzárendelés: a naplózási szervezet által meghatározott rekord tárolási követelményeinek].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének szeretné megőrizni auditálási rekordok egy évig elegendő tárolókapacitással foglal le. Összes naplózási bejegyzések gyűjti össze a Naplóelemzési, amely egy éves megőrzési van konfigurálva. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-au-5a"></a>A NIST 800-53 vezérlő AU-5.a

#### <a name="response-to-audit-processing-failures"></a>Válasz feldolgozási hibák naplózása

**AU-5.a** információk teljesítményriasztások [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök] naplózási feldolgozási hibája esetén.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Azure-figyelő és Naplóelemzési a szolgáltatási állapotát az Azure állapot webhely és a szolgáltatás állapotának panel az Azure portálon érhető el. Riasztások szolgáltatáshoz értesítést feldolgozási hibák naplózási más típusú konfigurálható. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-au-5b"></a>A NIST 800-53 vezérlő AU-5.b

#### <a name="response-to-audit-processing-failures"></a>Válasz feldolgozási hibák naplózása

**AU-5.b** az adatokat a rendszer a következő további műveleteket hajtja végre: [hozzárendelés: szervezet által meghatározott műveleteket, amelyeket olyankor kell végrehajtani (például információ rendszer leállítása, felülírni a legrégebbi rekordjait, auditálási rekordok létrehozásának leállítása)].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének által üzembe helyezett erőforrás által létrehozott összes naplózási rekord Naplóelemzési által gyűjtött, és az egy évig őrzi meg. A Foglalás a naplózási rekord tárolására dinamikusan lefoglalt kapacitása elegendő biztosítása érhető el. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-au-5-1"></a>A NIST 800-53 vezérlő AU-5 (1)

#### <a name="response-to-audit-processing-failures--audit-storage-capacity"></a>Válasz feldolgozási hibák naplózása |} Naplózási tárolási kapacitás

**AU-5 (1)** az információs rendszer biztosít olyan figyelmeztetés, ami [hozzárendelés: szervezet által meghatározott csoporthoz, a szerepkörök és/vagy a helyek] belül [hozzárendelés: szervezet által meghatározott időszakra vonatkozó] Ha lefoglalt naplózási rekord tárolókötetet elér [ Hozzárendelés: szervezet által meghatározott százalékos] maximális tárház naplózási rekord tárolási kapacitás.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének által üzembe helyezett erőforrás által létrehozott összes naplózási rekord Naplóelemzési által gyűjtött, és az egy évig őrzi meg. A Foglalás a naplózási rekord tárolására dinamikusan lefoglalt kapacitása elegendő biztosítása érhető el. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-au-5-2"></a>A NIST 800-53 vezérlő AU-5 (2)

#### <a name="response-to-audit-processing-failures--real-time-alerts"></a>Válasz feldolgozási hibák naplózása |} Valós idejű riasztások

**AU-5 (2)** az információs rendszer biztosít a riasztás [hozzárendelés: valós idejű időszak szervezet által meghatározott] való [hozzárendelés: szervezet által meghatározott csoporthoz, a szerepkörök és/vagy a helyek] Ha fordulhat elő, a következő sikertelen események naplózása: [hozzárendelés: szervezet által meghatározott sikertelen események naplózása igénylő, valós idejű riasztások].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | A szolgáltatás állapotának panel az Azure-portálon az Azure-szolgáltatás állapotának érhető el. Riasztások szolgáltatáshoz értesítést feldolgozási hibák naplózási más típusú konfigurálható. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-au-6a"></a>A NIST 800-53 vezérlő AU-6.a

#### <a name="audit-review-analysis-and-reporting"></a>Tekintse át, elemzési és jelentéskészítési naplózása

**AU-6.a** a szervezet ellenőrzi és elemzi az adatokat rendszer auditálási rekordok [hozzárendelés: szervezet által meghatározott gyakoriság] jelek a [hozzárendelés: szervezet által meghatározott nem megfelelő vagy szokatlan tevékenység].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős áttekintését és elemzése az auditálási rekordok ügyfél telepített erőforrások (alkalmazásokhoz, operációs rendszerek, adatbázisok és szoftverek között). |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-au-6b"></a>A NIST 800-53 vezérlő AU-6.b

#### <a name="audit-review-analysis-and-reporting"></a>Tekintse át, elemzési és jelentéskészítési naplózása

**AU-6.b** a szervezet jelentést az eredményekről [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél (AU-06.a meghatározott) a nem megfelelő vagy szokatlan tevékenység eredményeit reporting ügyfél telepített erőforrások felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-au-6-1"></a>A NIST 800-53 vezérlő AU-6 (1)

#### <a name="audit-review-analysis-and-reporting--process-integration"></a>Tekintse át, elemzési és jelentéskészítési naplózása |} Folyamat-integráció

**AU-6 (1)** a szervezet naplózási tekintse át, elemzési és jelentéskészítési szervezeti folyamatok támogatása vizsgálat és a gyanús tevékenységek válasz folyamatok integrálásához automatizált mechanizmusok funkcióit használja.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél hivatkozhatnak egy vállalati szintű központi naplózási felülvizsgálati, elemzési és jelentéskészítési képességet. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-au-6-3"></a>A NIST 800-53 vezérlő AU-6 (3)

#### <a name="audit-review-analysis-and-reporting--correlate-audit-repositories"></a>Tekintse át, elemzési és jelentéskészítési naplózása |} Naplózási adattárak összefüggéseket

**AU-6 (3)** a szervezet elemzi, és korrelálja naplózási rekordok között ahhoz, hogy a teljes szervezetre kiterjedő situational tájékoztatási különböző adattárak.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének valósítja meg a Naplóelemzési megoldás az OMS központosíthatja a naplózási adatokat telepített erőforrások között szervezeti situational tájékoztatási támogatása. Az ügyfelek előfordulhat, hogy kiválasztott további integrálásához Naplóelemzési más rendszerekkel. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-au-6-4"></a>A NIST 800-53 vezérlő AU-6 (4)

#### <a name="audit-review-analysis-and-reporting--central-review-and-analysis"></a>Tekintse át, elemzési és jelentéskészítési naplózása |} Központi áttekintése és elemzése

**AU-6 (4)** az információs rendszer lehetővé teszi a központilag tekintse át, és elemezze az auditálási rekordok több összetevői a rendszerben.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének a Naplóelemzési megoldás az OMS központosíthatja a naplózási adatokat telepített erőforrások, támogató központi felülvizsgálati, elemzési és jelentéskészítési valósítja meg. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-au-6-5"></a>A NIST 800-53 vezérlő AU-6 (5)

#### <a name="audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>Tekintse át, elemzési és jelentéskészítési naplózása |} Integráció / vizsgálatát, és figyelési képességek

**AU-6 (5)** a szervezet az auditálási rekordok elemzés integrálható elemzése [kijelölés (egy vagy több): a biztonsági rés információt; teljesítményadatokat; monitoring információk; rendszer vizsgálata [Hozzárendelés: más forrásokból gyűjtött szervezet által meghatározott adatok vagy információk]] használatával tovább javíthatja a nem megfelelő vagy a szokatlan tevékenységek azonosítására képes.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének telepíti az OMS biztonsági és hitelesítési megoldás. Ez a megoldás révén átfogó képet kaphat a biztonságot. A biztonsági és naplózási irányítópult érhetők el adatok használata telepített OMS megoldásokkal, a naplóadatok és a biztonsági rés adatait alapterv és a javítás assessment integrálása telepített erőforrások biztonsági állapotának magas szintű betekintést nyújt. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-au-6-6"></a>A NIST 800-53 vezérlő AU-6 (6)

#### <a name="audit-review-analysis-and-reporting--correlation-with-physical-monitoring"></a>Tekintse át, elemzési és jelentéskészítési naplózása |} Korrelációs fizikai figyelési

**AU-6 (6)** a szervezet adatainak használatával tovább javíthatja a gyanús, nem megfelelő, szokatlan vagy rosszindulatú tevékenységek azonosítására képes fizikai hozzáférés figyelése auditálási rekordok adatait ad eredményül.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Az ügyfelek nem rendelkeznek minden rendszer erőforrásokhoz való fizikai hozzáférés az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | Microsoft Azure SIM adapterek fizikai kapcsolódó figyelési adatokat használ, és megfelel az auditálási rekordok határozza meg, ha volt bármilyen társított logikai megsértése vagy gyanús viselkedését fizikai incidensek észlelésekor. |


 ### <a name="nist-800-53-control-au-6-7"></a>A NIST 800-53 vezérlő AU-6 (7)

#### <a name="audit-review-analysis-and-reporting--permitted-actions"></a>Tekintse át, elemzési és jelentéskészítési naplózása |} Engedélyezett műveletek

**AU-6 (7)** a szervezet megadja az engedélyezett műveleteket az egyes [kijelölés (egy vagy több): információ rendszerfolyamat; szerepkör; felhasználói] társított a felülvizsgálati, elemzés és jelentése naplózott adatok.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének által telepített Windows virtuális gépek valósítja meg az operációs rendszer szintű engedélyek, amelyek korlátozzák a felhasználó a tekintetben naplózási információkkal elvégezhető műveletekhez. Az Azure felhasználók vagy felhasználói csoportok rendelhet szerepköröket (például tulajdonos, közreműködő, olvasó vagy egy egyéni biztonsági szerepkört) minden olyan erőforrásnál rendelkezésre műveletek korlátozása, vagy megoldások, beleértve a Naplóelemzési telepítve.  |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-au-6-10"></a>A NIST 800-53 vezérlő AU-6 (10)

#### <a name="audit-review-analysis-and-reporting--audit-level-adjustment"></a>Tekintse át, elemzési és jelentéskészítési naplózása |} Naplózási szint módosítása

**AU-6 (10)** a szervezet Itt állíthatja be a naplózási felülvizsgálati, elemzési és jelentéskészítési információkat rendszerből bűnüldözési információ, az eszközintelligencia-információkat, vagy egyéb hiteles alapján változása esetén információ.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős naplózási felülvizsgálati, elemzési és jelentéskészítési ügyfél telepített erőforrásokat (alkalmazásokhoz, operációs rendszerek, adatbázisok és szoftverek között) szintjét módosítására vonatkozó törvények által nyújtott információk alapján változása esetén érvényesítési, az eszközintelligencia vagy más hiteles forrásból. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-au-7a"></a>A NIST 800-53 vezérlő AU-7.a

#### <a name="audit-reduction-and-report-generation"></a>Naplózási csökkentése és a jelentés létrehozása

**AU-7.a** az információs rendszer biztosít egy naplózási csökkentése és a jelentés generációs funkció, amely támogatja az igény szerinti naplózási felülvizsgálati, elemzési és jelentéskészítési követelmények és biztonsági incidensek után-az-tény vizsgálatokat.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének a Naplóelemzési megoldás az OMS valósítja meg. A Naplóelemzési által kezelt erőforrások egy központi tárházban történő adatainak begyűjtése OMS figyelési szolgáltatásokat biztosít. Az összegyűjtésüket követően az adatok használhatók riasztáshoz, elemzéshez vagy exportáláshoz. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-au-7b"></a>A NIST 800-53 vezérlő AU-7.b

#### <a name="audit-reduction-and-report-generation"></a>Naplózási csökkentése és a jelentés létrehozása

**AU-7.b** az adatokat a rendszer egy naplózási csökkentése és a jelentés generációs képességet biztosít, amely nem változtatja meg az eredeti tartalom vagy a naplónyilvántartás rendelési idő.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének a Naplóelemzési megoldás az OMS valósítja meg. A Naplóelemzési által kezelt erőforrások egy központi tárházban történő adatainak begyűjtése OMS figyelési szolgáltatásokat biztosít. A tartalom és az idő rendelés az auditálási rekordok nem változnak, amikor Naplóelemzési által gyűjtött. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-au-7-1"></a>A NIST 800-53 vezérlő AU-7 (1)

#### <a name="audit-reduction-and-report-generation--automatic-processing"></a>Naplózási csökkentése és jelentéskészítésre |} Automatikus feldolgozása

**AU-7 (1)** az információs rendszer lehetővé teszi az események alapján számára auditálási rekordok feldolgozásához [hozzárendelés: auditálási rekordok belül a szervezet által megadott naplózási mezők].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének a Naplóelemzési megoldás az OMS valósítja meg. A Naplóelemzési által kezelt erőforrások egy központi tárházban történő adatainak begyűjtése OMS figyelési szolgáltatásokat biztosít. Az összegyűjtésüket követően az adatok használhatók riasztáshoz, elemzéshez vagy exportáláshoz. A Log Analytics egy hatékony lekérdezési nyelvet biztosít a tárházban tárolt adatok kinyeréséhez. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-au-8a"></a>A NIST 800-53 vezérlő AU-8.a

#### <a name="time-stamps"></a>Időbélyegek

**AU-8.a** a információk rendszere belső rendszerideje időbélyegek az auditálási rekordok létrehozásához.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének által üzembe helyezett erőforrás használja belső rendszerideje időbélyegek az auditálási rekordok létrehozásához. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-au-8b"></a>A NIST 800-53 vezérlő AU-8.b

#### <a name="time-stamps"></a>Időbélyegek

**AU-8.b** az információs rendszer naplózási azt jelzi, hogy az egyezményes világidő (UTC) vagy (GMT) greenwichi középidő kell hozzárendelni az időbélyegek rögzíti, és megfelel-e [hozzárendelés: idő mérése a szervezet által meghatározott granularitási].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének által üzembe helyezett erőforrás használja belső rendszerideje időbélyegek az auditálási rekordok létrehozásához. UTC időbélyeggel rögzíti. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-au-8-1a"></a>A NIST 800-53 vezérlő AU-8 (1) sémaszintjén

#### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>Időbélyegek |} Mérvadó szinkronizálást

**AU-8 (1) sémaszintjén** az adatokat a rendszer összehasonlítja a belső információ rendszerideje [hozzárendelés: szervezet által meghatározott gyakorisága], [hozzárendelés: szervezet által meghatározott mérvadó időforrást].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének által üzembe helyezett erőforrás használja belső rendszerideje időbélyegek az auditálási rekordok létrehozásához. Belső rendszerideje konfigurált egy mérvadó időforrást szinkronizálni. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-au-8-1b"></a>A NIST 800-53 vezérlő AU-8 (1) .b

#### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>Időbélyegek |} Mérvadó szinkronizálást

**AU-8 (1) .b** az adatokat a rendszer szinkronizálja a mérvadó forrás belső rendszerideje, amikor időeltérés érték nagyobb, mint [hozzárendelés: szervezet által meghatározott időszak].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének által üzembe helyezett erőforrás használja belső rendszerideje időbélyegek az auditálási rekordok létrehozásához. Belső rendszerideje konfigurált egy mérvadó időforrást szinkronizálni. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-au-9"></a>A NIST 800-53 vezérlő AU-9

#### <a name="protection-of-audit-information"></a>Naplózási információk védelme

**AU-9** az adatokat a rendszer megakadályozza naplózási információk és a naplózási eszközök jogosulatlan hozzáférés, módosítását és törlését.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Logikai hozzáférés-vezérlést naplózási információkat és az Azure tervezetének belül eszközöket az illetéktelen hozzáféréstől, módosítását és törlését védelmére használhatók. Az Azure Active Directory vezérlésről jóváhagyott logikai szerepköralapú csoporttagságok használatával. Naplózási adatainak megtekintéséhez, és a naplózási eszközök használata a felhasználók számára ezen engedélyeket igénylő korlátozható. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-au-9-2"></a>A NIST 800-53 vezérlő AU-9 (2)

#### <a name="protection-of-audit-information--audit-backup-on-separate-physical-systems--components"></a>Naplózási adatok védelmének |} Biztonsági mentés külön fizikai rendszerek naplózási / összetevőinek

**AU-9 (2)** auditálási rekordok készít biztonsági másolatot az adatokat a rendszer [hozzárendelés: szervezet által meghatározott gyakoriság] egy fizikailag másik rendszerre vagy rendszer-vagy naplózott összetevő rendszerösszetevő.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének az OMS a Naplóelemzés szolgáltatás megvalósítja. Telepített virtuális gépek és az Azure diagnosztikai tárfiók csatlakoztatott adatforrások szolgáltatáshoz, és külön-külön megőrzi a forrásból. Gyűjti össze az adatokat az OMS közel valós időben. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-au-9-3"></a>A NIST 800-53 vezérlő AU-9 (3)

#### <a name="protection-of-audit-information--cryptographic-protection"></a>Naplózási adatok védelmének |} Titkosítási védelem

**AU-9 (3)** az információs rendszer naplózási információk és a naplózási eszközök sértetlenségének védelme érdekében titkosítási mechanizmus valósítja meg.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének az OMS a Naplóelemzés szolgáltatás megvalósítja. Naplóelemzési biztosítja, hogy a bejövő adatok megbízható forrásból származó érvényesítésével azonosítsa a tanúsítványok és az Azure hitelesítési adatok integritását. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-au-9-4"></a>A NIST 800-53 vezérlő AU-9 (4)

#### <a name="protection-of-audit-information--access-by-subset-of-privileged-users"></a>Naplózási adatok védelmének |} Megfelelő jogosultsággal rendelkező felhasználók csoportja által hozzáférés

**AU-9 (4)** a szervezet engedélyezi a hozzáférést csak naplózási funkció kezelése [hozzárendelés: megfelelő jogosultsággal rendelkező felhasználók szervezet által meghatározott részhalmaza].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Logikai hozzáférés-vezérlést naplózási információkat és az Azure tervezetének belül eszközöket az illetéktelen hozzáféréstől, módosítását és törlését védelmére használhatók. Az Azure Active Directory vezérlésről jóváhagyott logikai szerepköralapú csoporttagságok használatával. Naplózási adatainak megtekintéséhez, és a naplózási eszközök használata a felhasználók számára ezen engedélyeket igénylő korlátozható.
 |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-au-10"></a>A NIST 800-53 vezérlő AU-10

#### <a name="non-repudiation"></a>Nem megtagadás

**AU-10** az információs rendszer véd egy természetes személyhez (vagy egy adott folyamat nevében) pontosan megtagadása, hogy végre [hozzárendelés: szervezet által meghatározott műveleteket hatálya alá nem megtagadás].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének naplózási funkció Azure figyelő és a OMS Naplóelemzés szolgáltatást biztosítja. Az Azure biztosít, telepített erőforrások társított tevékenységre vonatkozó részletes naplókat. Ezek és az operációs rendszer szintű naplók Naplóelemzési által gyűjtött és az OMS-tárház tárolja. Ezek a naplók információkat rendszer események részletes rekordok tartalmazott, és nem megtagadás szemben biztosítanak védelmet. További a hozzáférési adatok naplózása használatával korlátozva szerepköralapú hozzáférés-vezérlés unauthored módosításának és törlésének a naplóadatok megelőzése érdekében. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-au-11"></a>A NIST 800-53 vezérlő AU-11

#### <a name="audit-record-retention"></a>Naplózási rekordok megőrzési

**AU-11** a szervezet megőrzi az auditálási rekordok [hozzárendelés: szervezet által meghatározott időszak rekordok adatmegőrzési konzisztens] előírásoknak megfelelő és támogatást nyújt a biztonsági események követően-a-tény-vizsgálatok és szervezeti adatok megőrzési követelményeinek.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének az OMS a Naplóelemzés szolgáltatás megvalósítja. A Naplóelemzési által kezelt erőforrások egy központi tárházban történő adatainak begyűjtése OMS figyelési szolgáltatásokat biztosít. Összegyűjtését követően az adatok / Log Analyticshez konfigurációs egy évig őrzi meg. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-au-12a"></a>A NIST 800-53 vezérlő AU-12.a

#### <a name="audit-generation"></a>Naplózási létrehozása

**AU-12.a** az információk rendszer biztosít a AU-2 meghatározott események naplózási rekord generációs funkció egy. a(z) [hozzárendelés: szervezet által megadott információk rendszerösszetevők].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének által naplózott eseményeket foglalja bele azokat rendszervizsgálati módban levő telepített erőforrások, az operációs rendszer szintű naplókat, az Active Directory-naplók Azure tevékenységi naplóit, és az SQL Server naplózza. Az ügyfelek kritikus igényeihez naplózandó további események is kiválaszthat. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-au-12b"></a>A NIST 800-53 vezérlő AU-12.b

#### <a name="audit-generation"></a>Naplózási létrehozása

**AU-12.b** lehetővé teszi, hogy az adatokat a rendszer [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök] kiválasztásához, milyen események által meghatározott összetevők a információs rendszer naplózandó vannak.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Azure-ban és a virtuális gép operációs rendszer szintű szerepköralapú hozzáférés-vezérlés használatával elérése funkciók naplózása történik. Felhasználók a megfelelő szerepkör-alapú hitelesítési eseményt kell naplózni, az Azure tervezetének által üzembe helyezett erőforrás által kiválasztott konfigurációjának konfigurálhatja. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-au-12c"></a>A NIST 800-53 vezérlő AU-12.c

#### <a name="audit-generation"></a>Naplózási létrehozása

**AU-12.c** az információs rendszer auditálási rekordok AU-2.d definiált eseményeket a állít elő. a tartalomhoz, a AU-3.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének által naplózott eseményeket foglalja bele azokat rendszervizsgálati módban levő telepített erőforrások, az operációs rendszer szintű naplókat, az Active Directory-naplók Azure tevékenységi naplóit, és az SQL Server naplózza. Az ügyfelek kritikus igényeihez naplózandó további események is kiválaszthat. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-au-12-1"></a>A NIST 800-53 vezérlő AU-12 (1)

#### <a name="audit-generation--system-wide--time-correlated-audit-trail"></a>Létrehozás naplózása |} Rendszerszintű / idő Korrelált napló

**AU-12 (1)** az információs rendszer lefordítja az auditálási rekordok [hozzárendelés: szervezet által megadott információk rendszerösszetevők] az egy rendszerszintű (logikai és fizikai) napló, amely idő-összefüggő belül [hozzárendelése: szervezet által meghatározott szint a napló az egyes rekordok időbélyegeket közötti kapcsolat tűréshatáron].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure tervezetének az OMS a Naplóelemzés szolgáltatás megvalósítja. A Naplóelemzési által kezelt erőforrások egy központi tárházban történő adatainak begyűjtése OMS figyelési szolgáltatásokat biztosít. Naplózási rekord időbélyegeket nem változnak, ezért a napló idő korrelált. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-au-12-3"></a>A NIST 800-53 vezérlő AU-12 (3)

#### <a name="audit-generation--changes-by-authorized-individuals"></a>Létrehozás naplózása |} Módosítások által felhatalmazott személyek

**AU-12 (3)** az információs rendszer lehetőséget biztosít [hozzárendelés: szervezet által definiált egyéni felhasználók vagy szerepkörök] kell elvégezni a naplózás módosítása [hozzárendelés: szervezet által megadott információk rendszerösszetevők] [alapján Hozzárendelés: választható esemény szervezet által meghatározott feltételek] belül [hozzárendelés: szervezet által megadott küszöbértékek].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Azure-ban és a virtuális gép operációs rendszer szintű szerepköralapú hozzáférés-vezérlés használatával elérése funkciók naplózása történik. Felhasználók a megfelelő szerepkör-alapú hitelesítési eseményt kell naplózni, az Azure tervezetének által üzembe helyezett erőforrás által kiválasztott konfigurációjának konfigurálhatja. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |
