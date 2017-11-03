---
title: "Az Azure Security Center személyes adatok védelme |} Microsoft Docs"
description: "az Azure security Centerben személyes adatok védelme"
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
ms.date: 08/24/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 3a941389713a4d3dbffbbfe8a717409927d85c6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="protect-personal-data-from-breaches-and-attacks-azure-security-center"></a>Személyes adatok védelmét a problémák és támadások: az Azure Security Center

Ez a cikk segít megvédeni a személyes adatokat a problémák és támadások az Azure Security Center használatának megismerése.

## <a name="scenario"></a>Forgatókönyv 

Egy nagy körutazás cég, az Amerikai Egyesült Államokban telephelyének bővíti a mediterrán és Balti tengerek, valamint a Brit-szigetekre útvonalak biztosítani a műveleteket. Az adott erőfeszítéseket érdekében több kisebb körutazás sorok Olaszország, német, Dánia és az Egyesült szerzett

A vállalat a Microsoft Azure használ a felhőben tárolt vállalati adatokat. Ez magában foglalja a személyes azonosításra alkalmas adatok, például a nevét, a címét, a telefonszámokat és a hitelkártya adatait. Az adatokat is tartalmaz az emberi erőforrások, mint:

- Címek
- telefonszámok
- azonosító számokat
- orvosi adatok

A körutazás sor is fenntartja, nagy adatbázis ellenszolgáltatás és hűség program tagjainak. Vállalati alkalmazottak hozzáférjenek a hálózathoz, a távoli iroda a vállalat, és a világ utazás ügynökök rendelkezésre elérni egyes vállalati erőforrásokat.
Személyes adatokat a hálózaton keresztül halad, ezeket a helyeket és a Microsoft-adatközpont között.

## <a name="problem-statement"></a>Probléma leírása

A vállalat aggódik az Azure-erőforrások elleni támadások veszélye. Szeretnék ügyfelek és az alkalmazottak a személyes adatok veszélyeknek való kitettség megelőzése jogosulatlan személyek számára. Szeretnék útmutatást is megelőzése és a válasz/szervizelés, valamint a felhőalapú erőforrások a biztonság folyamatos figyelését hatékony módot.
Egy erős elleni védelmi vonalat mai valamint támadók van szükségük.

## <a name="company-goal"></a>Vállalati cél

A vállalati célok egyik fenyegetésekkel szembeni hatékony megvédi az ügyfelek és az alkalmazottak a személyes adatok biztonsága érdekében. A cél egyik azonnal reagálni a biztonsági szabályok megsértésére jeleit csökkenthető az káros hatása. Biztonsági aktuális állapotát, sebezhető felismerésében és elháríthatja a úgy van szüksége.

## <a name="solutions"></a>Megoldások

A Microsoft Azure Security Center (ASC) az integrált biztonsági monitorozást és felügyeleti megoldást nyújt. Könnyen használható és hatékony megelőzését, észlelését és ellenállási képességeket kínál biztosítja.

### <a name="prevention"></a>Megelőzés

ASC segít megelőzni, hogy a megsértése azáltal, hogy a biztonsági házirendek beállítása, közvetlenül az időponthoz kötött hozzáférést és biztonsági ajánlások megvalósításával.

Egy biztonsági házirend határozza meg az adott előfizetésen belüli erőforrásokon ajánlott meg. Csak a időben való hozzáférésre is használható az Azure virtuális gépeken, támadásoknak való kitettség csökkentése bejövő forgalmát zárolását. Biztonsági javaslatok az Azure-erőforrások biztonsági állapotát elemzése után ASC hozza létre.

#### <a name="how-do-i-set-security-policies-in-asc"></a>Biztonsági házirendek beállítása a ASC

Az egyes előfizetésekhez külön-külön biztonsági szabályzatot állíthat be. Biztonsági szabályzat módosításához az előfizetésben tulajdonos vagy közreműködő szerepkörrel kell rendelkeznie. Az Azure portálon tegye a következőket:

1. Válassza ki **házirend** ASC irányítópultján.

2. Válassza ki az előfizetést, amely engedélyezi a házirendet kívánja.

3. Válasszon **megakadályozási szabályzat** előfizetésenként házirendek konfigurálásához. **Adatgyűjtés a virtuális gépek** meg **meg.**

4. Az a **megakadályozási szabályzat** beállításnál válassza **a** a biztonsági javaslatok láthatók, amelyek kapcsolódnak az előfizetés engedélyezése.

![](media/protect-personal-data-azure-security-center/prevention-policy.png)

Részletesebb utasításokat és engedélyezhető házirend ajánlások előzetesben, lásd: [biztonsági házirendek beállítása az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-policies#set-security-policies).

#### <a name="how-do-i-configure-just-in-time-access-jit"></a>Hogyan konfigurálhatók a csak az idő Access (JIT)?

Igény szerinti engedélyezésekor a rendszer a Security Center zárolja az Azure virtuális gépek bejövő forgalmát az NSG-szabály által. Kiválaszthatja a a virtuális Gépre, amelyre a bejövő forgalom lesz zárolva. Igény szerinti hozzáférés használatára, tegye a következőket:

1. Válassza ki a **idő VM hozzáférés csempén csak** a ASC panelen.

2. Válassza ki a **ajánlott** fülre.

3. A **virtuális gépek**, válassza ki az engedélyezni kívánt virtuális gépeket. Ez helyezi a virtuális gépek melletti négyzetet. 
4. Válassza ki **JIT engedélyezése** virtuális gépeken.
5. Kattintson a **Mentés** gombra.

Láthatja majd ASC javasolja a rendszer engedélyezi az igény szerinti alapértelmezett portokat. Is hozzáadhat és engedélyezése a csak akkor szeretne új port konfigurálása idő megoldásban. A **közvetlenül a virtuális gép elérhető** csempe a Security Center az igény szerinti hozzáférés konfigurált virtuális gépek számát jeleníti meg. Azt is bemutatja a múlt héten engedélyezett hozzáférési kérelmek száma.

![](media/protect-personal-data-azure-security-center/jit-vm.png)

Ehhez útmutatást és az elérhető legyen, csak további információt lásd: [JIT-virtuális gép hozzáférés kezelése.](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

#### <a name="how-do-i-implement-asc-security-recommendations"></a>Hogyan valósítja meg a biztonsági javaslatok ASC?

A Security Center javaslatokat hoz létre, amikor lehetséges biztonsági réseket észlel. A javaslatok végigvezetik Önt a szükséges vezérlők konfigurálásának folyamatán. 
1. Válassza ki a **javaslatok** a ASC irányítópult csempét.

2. Tekintse meg a javaslatok, amelyben soronként jelöli egy javaslat táblázatos formában látható.

3. Szűrő ajánlásokat, jelölje be a **szűrő** és válassza ki a súlyosság és állapotának értékeket látni szeretné.

4. Azt ajánljuk, hogy nincs megfelelő elvetéséhez kattintson a jobb gombbal és válassza ki **leállítási.**

5. Értékelje ki, melyik javaslat előbb alkalmazni kell.

6. A javaslatok a prioritásuk szerinti sorrendben alkalmazza.

Lehetséges ajánlásokat, és hogyan kell alkalmazni az egyes útmutatók listájáért lásd: [biztonsági javaslatok kezelése az Azure Security Center.](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

### <a name="detection-and-response"></a>Felderítését és a válasz

Felderítését és a válasz Ugrás együtt, ahányat csak szeretne válaszolni minél gyorsabban után fenyegetést észlel.
ASC fenyegetésészlelés működik, automatikusan az Azure-erőforrások, a hálózati és az összekapcsolt partnermegoldások a biztonsági információk begyűjtése. ASC is gyorsan frissíti az észlelési algoritmusokat támadók kiadás új és egyre kifinomult biztonsági réseket. További információk a ASC tartozó fenyegetésészlelés működése, a következő témakörben: [az Azure Security Center az észlelési képességek](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities).

#### <a name="how-do-i-manage-and-respond-to-security-alerts"></a>Hogyan kezelése és válaszadás a biztonsági riasztásokra?

A Security Center szüksége gyorsan vizsgálja meg a problémát az információk mellett jelenik meg a rangsorolt biztonsági riasztások listája. A Security Center emellett a támadás elhárítására vonatkozóan javaslatokat tartalmaz. A biztonsági riasztások kezelése, tegye a következőket:

1. Válassza ki a **biztonsági riasztások** ASC irányítópultján csempére. Ez azt jelenti, az egyes riasztások részletei.

2. A riasztások dátum, állapot és súlyosság alapján szűrheti, jelölje be **szűrő** , és válassza ki a megjeleníteni kívánt értékeket.

3. Riasztást válaszolni, válassza ki azt, tekintse át az információkat, majd válassza ki a támadásnak kitett erőforrásra erőforrást.

4. Az a **leírás** mezőben adatait, többek között az ajánlott javítási láthatja.

![](media/protect-personal-data-azure-security-center/security-alerts.png)

Részletes utasítások a biztonsági riasztásokra való reagálásról, lásd: [kezelése és a biztonsági riasztásokra az Azure Security Center válaszol.](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

A biztonsági riasztások kivizsgálásának további segítséget a vállalati ASC riasztások integrálhatja a saját SIEM-megoldás használatával [Azure napló integrációs](https://aka.ms/AzLog).

#### <a name="how-do-i-manage-security-incidents"></a>Hogyan kezelheti a biztonsági események?

A ASC egy biztonsági incidens az összes riasztás erőforrás, amely megfelel-e a kill lánc minták összesítést. Az incidensek megnyitásakor megjelenik a kapcsolódó riasztások listája, amelyből további információkat kaphat az egyes riasztásokról. Incidensek a biztonsági riasztások csempén és panelen jelennek meg.

Tekintse át, és biztonsági incidensek kezeléséhez, tegye a következőket:

1. Válassza ki a **biztonsági riasztások** csempére. Amennyiben egy biztonsági incidens észlel, akkor jelenik meg a biztonsági riasztások graph alatt. Egy ikont, amely eltér a más riasztások lesz.

2. Válassza ki az incidenst, hogy a biztonsági incidens kapcsolatos további részletek megtekintéséhez. További részletek közé tartoznak a teljes leírását a súlyosság, a jelenlegi állapotában, a megtámadott erőforrások, az incidensek és a riasztások foglalt ezt az incidenst a javítási lépéseket.

Szűrheti, hogy **incidensek csak**, **riasztásokat csak**, vagy **mindkét**.

#### <a name="how-do-i-access-the-threat-intelligence-report"></a>Hogyan érhető el a fenyegetés az Eszközintelligencia-jelentés?

ASC elemzi a fenyegetések azonosítására különböző forrásokból származó információkat. Segít az incidensekre adott reakciók csapatok vizsgálja meg, és szervizelheti azokat a fenyegetéseket, a Security Center tartalmaz, amely leírja a fenyegetést észlelt fenyegetés eszközintelligencia jelentést.

A Security Center háromféle fenyegetés jelentéseit, amelyek / támadás eltérőek lehetnek.
Az elérhető jelentések a következők:

- Tevékenységcsoport a jelentés: támadó, a célok és taktikai mély dives tartalmazza.

- Kampány jelentés: adott támadás kampányok részleteit összpontosít.

- Fenyegetés összesítő jelentés: az előző két jelentés az összes elemet tartalmazza.

Az ilyen információkat legyen nagyon hasznos incidensekre adott reakciók, ahol nincs folyamatban lévő vizsgálat tudni, hogy a támadás, a támadó célokat, és mi a teendő soron a probléma elhárítása érdekében.

1. A fenyegetés az eszközintelligencia-jelentés megnyitásához tegye a következőket:

2. Válassza ki a **biztonsági riasztások** a ASC irányítópult csempét.

3. Jelölje ki a biztonsági riasztást, amelyekhez további információkhoz juthat.

4. Az a **jelentések** , majd kattintson a fenyegetés eszközintelligencia jelentésre mutató hivatkozást.

5. Ekkor megnyílik a PDF-fájl, amely letölthető.

![](media/protect-personal-data-azure-security-center/security-alerts-suspicious-process.png)

A ASC fenyegetés az eszközintelligencia-jelentés kapcsolatos további információkért lásd: [Azure Security Center fenyegetés Eszközintelligencia jelentés.](https://docs.microsoft.com/azure/security-center/security-center-threat-report)

### <a name="assessment"></a>Értékelés

Tesztelés, felmérési és a biztonságot értékelése érdekében ASC biztosít integrált biztonsági réseinek értékelése Qualys a felhő ügynökök, a virtuális gép javaslatok összetevő részeként.

A Qualys ügynök jelent a Qualys felügyeleti platform, amely ezután elküldi a biztonsági rés és állapotfigyelési adatokat biztonsági ASC biztonsági adatokat. A javaslat hozzáadni egy biztonsági rés értékelési megoldás megjelenik a **javaslatok** a ASC irányítópult paneljét.

Miután telepítette a biztonságirés-felmérési megoldást a célként megadott virtuális gépre, a Security Center átvizsgálja a virtuális gépet a rendszer és az alkalmazások biztonsági réseinek észlelése és azonosítása érdekében. Az észlelt problémák a **Virtuális gépekkel kapcsolatos javaslatok** beállítás alatt jelennek meg.

#### <a name="how-do-i-implement-a-vulnerability-assessment-solution"></a>Hogyan valósítja meg a biztonsági rés értékelési megoldás? 

Ha egy virtuális gép nem rendelkezik az integrált biztonsági rés értékelési megoldás már üzembe van helyezve, a Security Center azt javasolja, hogy telepíteni kell.

1. Az ASC irányítópult a a **javaslatok** panelen válassza **biztonsági rés értékelése megoldás hozzáadása.**

2. Válassza ki a virtuális gépeket, ahol a biztonsági rés értékelési megoldás telepíteni szeretné.

3. Kattintson a **telepíthető [száma] virtuális gépeket.**

4. Válasszon ki egy partneri megoldást, az Azure piactéren vagy alatt **meglévő megoldással** válasszon **Qualys.**

5. Ha bekapcsolja az automatikus frissítési beállítások ki- és kikapcsolása a **partneri megoldások** panelen.

A biztonsági rés értékelési megoldás megvalósításához további utasításokért lásd: [biztonsági réseinek értékelése az Azure Security Centerben.](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Center bemutatása](https://docs.microsoft.com/azure/security-center/security-center-get-started)

- [Azure Security Center bemutatása](https://docs.microsoft.com/azure/security-center/security-center-intro)

- [Azure Security Center riasztásait integrálása az Azure naplóelemzés integráció](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration)

- [Program az Azure Security Center az integrált biztonsági réseinek értékelése](https://blogs.msdn.microsoft.com/azuresecurity/2016/12/16/boost-azure-security-center-with-integrated-vulnerability-assessment/)
