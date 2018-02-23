---
title: "Hozzon létre, és a helyreállítási terv feladatátvételi és az Azure Site Recovery helyreállítási testreszabása |} Microsoft Docs"
description: "Ismerje meg, hogyan hozhat létre és testreszabása az Azure Site Recovery helyreállítási tervek. Ez a cikk ismerteti a feladatátvétel és a virtuális gépek és fizikai kiszolgálók helyreállításához."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 01/26/2018
ms.author: raynew
ms.openlocfilehash: 6ad82a8a2f8e16ab794ba90c109904bd45cb6b89
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-recovery-plan-by-using-site-recovery"></a>A Site Recovery segítségével a helyreállítási terv létrehozása

Ez a cikk ismerteti, hogyan hozhat létre, és testre szabhatja a helyreállítási terv [Azure Site Recovery](site-recovery-overview.md).

A következő helyreállítási terv létrehozása:

* Adja meg a csoportok a feladataikat együtt átadó, majd indítsa el a együtt gépek.
* Minta függőségek gépek csoportosításával őket egy helyreállítási terv csoport között. A feladatátvételt, és jelenítse meg az adott alkalmazást, például az alkalmazás virtuális gépek ugyanabban a helyreállítási terv csoportban.
* A feladatátvételt. A helyreállítási terv futtathatja a teszteket, tervezett, vagy nem tervezett feladatátvételt.

## <a name="why-use-a-recovery-plan"></a>Miért érdemes használni a helyreállítási terv

A helyreállítási terv segítségével felügyelhető kis független egység készíti elő rendszeres a helyreállítási folyamatot. Az egységek általában határoz meg az alkalmazás a környezetben. A helyreállítási terv segítségével definiálják, amelyben a virtuális gép elindításához. A helyreállítási terv segítségével is gyakori feladatok automatizálásához a helyreállítás során.

> [!TIP]
> Egy ellenőrizze, hogy felkészült a felhő áttelepítési vagy katasztrófa utáni helyreállítás módja annak érdekében, hogy az alkalmazások egy része a helyreállítási terv. Emellett győződjön meg arról, hogy minden helyreállítási terv tesztelni a helyreállításhoz az Azure-bA. A készenléti választhat magabiztosan áttelepítése vagy a teljes adatközpont Azure feladatátvételt.
 
A helyreállítási tervben szereplő három fő értéknövelő:

* A modell egy alkalmazás függőségeit rögzítéséhez.
* RTO csökkentése érdekében a legtöbb helyreállítási feladatok automatizálásához.
* Készen áll egy olyan vészhelyzet esetén a feladatátvételi teszt.

### <a name="model-an-application-to-capture-dependencies"></a>A modell egy alkalmazás függőségeit rögzítéséhez

A helyreállítási terv olyan virtuális gépet alkotó általában egy alkalmazást, és amely feladataikat együtt átadó. Helyreállítási terv használatával hoz létre, növelheti a helyreállítási terv csoport rögzítheti a alkalmazásspecifikus tulajdonságokat. 

Ez a cikk egy tipikus háromrétegű alkalmazás, amely előfordulhat, hogy egy záró, köztes és egy előtér-webkiszolgáló biztonsági SQL használjuk. Testre szabhatja a helyreállítási terv annak biztosítására, hogy a virtuális gépek indítsa el a megfelelő sorrendben egy feladatátvétel után. Az SQL-háttér első kell kezdődnie, a köztes tovább kell kezdődnie, és az előtér-webkiszolgáló utolsó kell kezdődnie. Ez a sorrend biztosítja, hogy működik-e az alkalmazás által az utolsó virtuális gép indításakor. 

Például a köztes kezdődik, amikor az megpróbál csatlakozni az SQL-réteg. A helyreállítási terv gondoskodik arról, hogy már fut az SQL-réteg. Rendelkezik egy előtér-kiszolgáló indítási utolsó is segít biztosítani, hogy a végfelhasználók nem csatlakoznak az alkalmazás URL-cím összetevőit mentése előtt és fut, és az alkalmazás készen áll kérések fogadására. Hozhat létre ezeket a függőségeket, testre szabhatja a helyreállítási terv csoportok hozzáadása, és válassza ki a virtuális gép. Csoportok közötti a virtuális gép áthelyezéséhez, módosítsa a virtuális gép.

![Képernyőfelvétel egy példa a helyreállítási terv a Site Recovery szolgáltatásban](./media/site-recovery-create-recovery-plans/rp.png)

Miután elvégezte a testreszabás, a pontos lépések az a helyreállítási jelenítheti meg. A helyreállítási terv feladatátvételi során végrehajtott lépések sorrendjének itt található:

1. A leállítási lépése megkísérli a virtuális gépek helyszíni kikapcsolása. (Csak feladatátvételi tesztet, amely során az elsődleges hely tovább futni kell.)
2. A leállítási kísérlet elindítja a virtuális gépek feladatátvétele párhuzamosan a helyreállítási tervben. A feladatátvételi lépés elkészíti a virtuális gépek lemezeit a replikált adatok használatával.
3. Az indítási csoportok ahhoz, azok hajtható végre, és indítsa el a virtuális gépek minden egyes csoport. Először csoport 1 hajt végre, akkor csoport 2 hajt végre, és végül végrehajtja a 3 csoport. Ha bármelyik csoportot (például egy elosztott terhelésű előtér) több virtuális géphez, a virtuális gépek párhuzamosan elindításához.

> [!TIP]
> Csoportok közötti sorrendi biztosítja, hogy különböző alkalmazásrétegek közötti függőségeket figyelembe vannak-e véve. Párhuzamossági, ahol szeretné használni, javítja az alkalmazás helyreállítási RTO.

   > [!NOTE]
   > Párhuzamos feladatátvételt egyetlen csoport részét képező gépek. A csoportok sorrendjében feladatátvételt eltérő csoportokba tartozó gépek. A gépek csoport 2 Indítsa el a feladatátvétel csak a csoport 1 gépeire rendelkezik átadja a feladatokat, és elindítása után.

### <a name="automate-most-recovery-tasks-to-reduce-rto"></a>RTO csökkentése érdekében a legtöbb helyreállítási feladatok automatizálásához

Nagy alkalmazások visszaállítása egy összetett feladat lehet. Jegyezze meg a chaos időpontjairól sok manuális lépések nehéz, és hibalehetőségeket rejt magában hiba teszi a folyamatot. Emellett előfordulhat, valakinek, aki nem érzékeli az alkalmazás menő bemutatása, akik ténylegesen elindítja a feladatátvételt. 

A helyreállítási terv segítségével automatizálni kell tennie minden lépésnél szükséges műveleteket. Azure Automation-forgatókönyv használatával a szükséges műveleteket állíthat be. A runbookok közös helyreállítási feladatokat, például a következő példákban szereplő feladatok automatizálható. Nem lehet automatizálni feladatokhoz beilleszthet manuális műveletek a helyreállítási terv.

* **Az Azure virtuális gép feladatátvételt követően a feladatok**: ezek a feladatok általában kell csatlakozni a virtuális géphez. Példák:
    * Hozzon létre egy nyilvános IP-címet a virtuális gép feladatátvételt követően.
    * Rendelje hozzá a átvevő virtuális gép hálózati adapter hálózati biztonsági csoport.
    * A terheléselosztó hozzáadása egy rendelkezésre állási csoportot.
* **A virtuális gép feladatátvételt követően belül feladatok**: ezek a feladatok konfigurálja újra az alkalmazást, hogy továbbra is fennáll, az új környezetben megfelelő működéséhez. Példák:
    * Módosítsa a virtuális gépen belüli adatbázis-kapcsolati karakterláncot.
    * A web server konfigurációs vagy a szabályok módosítása.

> [!TIP]
> Egy kattintással feladatátvételi elérése és RTO optimalizálhatja a helyreállítás utáni feladatok automatizálására Automation-forgatókönyv használatával teljes helyreállítási terv létrehozása.

### <a name="test-failover-to-be-ready-for-a-disaster"></a>Készen áll egy olyan vészhelyzet esetén a feladatátvétel tesztelése

A helyreállítási terv segítségével indítás, feladatátvétel vagy a feladatátvételi tesztet. Mindig befejeződik a feladatátvétel előtt az alkalmazást a feladatátvételi tesztet. Teszt feladatátadás súgó ellenőrizze, hogy az alkalmazás megjelenik a helyreállítási helyen. A beállításokban történt kihagyott, ha egyszerűen aktiválhatja a tisztítás, és majd végezze el újra a feladatátvételi tesztet. Ezt a feladatátvételi tesztet több alkalommal amíg biztos benne, hogy az alkalmazás zökkenőmentesen helyreállíthatja.

![Képernyőfelvétel egy példa a helyreállítási terv teszt a Site Recovery szolgáltatásban](./media/site-recovery-create-recovery-plans/rptest.png)

> [!TIP]
> Mivel minden alkalmazáshoz egyedi, kell felépítenie helyreállítási tervek az egyes alkalmazásokhoz. 
>
> Emellett környezetben mai dinamikus, datacenter arra irányul, alkalmazásokat és függőségi viszonyaikat gyakran módosítják. Győződjön meg arról, hogy a helyreállítási terv aktuális, hogy tesztelni az alkalmazások negyedévente.

## <a name="create-a-recovery-plan"></a>Helyreállítási terv létrehozása

1. Válassza ki **helyreállítási tervek** > **helyreállítási terv létrehozása**.
   Adja meg a helyreállítási tervben és a forrás és cél nevét. A forráshely kell rendelkeznie, amelyek engedélyezve vannak a feladatátvétel és a helyreállítási virtuális gépek. Válassza ki a forrás és cél alapján a helyreállítási terv részeként kívánt virtuális gépeket. 

   |Forgatókönyv                   |Forrás               |Cél           |
   |---------------------------|---------------------|-----------------|
   |Azure – Azure             |Azure region         |Azure region     |
   |VMware – Azure            |Configuration server |Azure            |
   |A Virtual Machine Manager (VMM) az Azure-bA               |A VMM megjelenített neve    |Azure            |
   |Replikálás Hyper-V-helyről az Azure-ba      |Hyper-V hely neve    |Azure            |
   |Fizikai gépek az Azure-bA |Configuration server |Azure            |
   |A VMM VMM                 |A VMM rövid neve    |A VMM megjelenített neve|

   > [!NOTE]
   > A helyreállítási terv forrás és cél azonos virtuális gépek is tartalmazhat. VMware és a System Center Virtual Machine Manager (VMM) virtuális gépeket nem lehet ugyanabban a helyreállítási tervben. Azonban adhat hozzá VMware virtuális gépek és fizikai gépek ugyanabban a helyreállítási tervben. Ebben az esetben az egyik gépen forrása a konfigurációs kiszolgáló.

2. A **válassza ki a virtuális gépek**, válassza ki a hozzáadni kívánt az alapértelmezett (csoportot % 1) a helyreállítási tervben szereplő kívánt virtuális gépek (vagy replikációs csoport). Virtuális gépek, amelyek a forrás (a kijelölt helyreállítási tervben) védett, és amely védett a cél (a kijelölt helyreállítási tervben) jelölhet ki.

## <a name="customize-and-extend-recovery-plans"></a>Testreszabására és kibővítésére helyreállítási tervek

Testreszabásához és kiterjesztéséhez a helyreállítási terv, nyissa meg a helyreállítás helyreállítási terv erőforrás ablaktáblán. Válassza ki a **Testreszabás** fülre. Testre szabhatja és kiterjesztése a helyreállítási tervek a következő beállítások segítségével:

- **Új csoportok hozzáadása**: az alapértelmezett csoport hét további helyreállítási terv csoportokat is hozzáadhat. Ezt követően is hozzáadhat további gépeket vagy replikációs csoport helyreállítási terv csoportokhoz. Csoportok számozása a sorrendben, ahol hozzá őket. Megadhat egy virtuális géphez, vagy a replikációs csoport csak egy helyreállítási terv csoportban.
- **Adja hozzá a manuális műveletet**: manuális műveletek előtt vagy a helyreállítási terv csoport után is hozzáadhat. A helyreállítási terv fut, leállítja a pontnál, amelyen a manuális műveletet beszúrni. Egy párbeszédpanelen kéri, hogy adja meg, hogy befejeződött-e a manuális műveletet.
- **A parancsprogramok hozzáadásához**: előtt vagy után egy helyreállítási terv csoport futtatott parancsfájlok, adhat hozzá. Amikor egy új, hozzáadja a csoport műveletek új készletét. Például egy csoport 1 előtti lépések hozta neve *csoport 1: előtti lépések*. Ebben a készletben található összes előtti lépések jelennek meg. Hozzáadhat egy parancsfájlt az elsődleges helyen, csak akkor, ha a VMM-kiszolgáló telepítve van. További információkért lásd: [VMM parancsfájl hozzáadása egy helyreállítási terv](site-recovery-how-to-add-vmmscript.md).
- **Adja hozzá az Azure runbookok**: helyreállítási tervek kiterjesztheti az Azure runbookok használatával. Használhatja például a runbook feladatok automatizálásához, vagy hozzon létre egylépéses helyreállítási. További információkért lásd: [hozzáadása Azure Automation runbookjai helyreállítási tervekbe](site-recovery-runbook-automation.md).


## <a name="add-a-script-runbook-or-manual-action-to-a-plan"></a>Egy parancsfájl, a runbookot vagy a manuális műveletet hozzáadása egy csomaghoz

Virtuális gépek vagy a replikációs csoport helyreállítási terv alapértelmezett csoporthoz hozzáadása, után is hozzáadhat egy parancsfájl vagy a manuális műveletet a helyreállítási terv csoporthoz.

1. Nyissa meg a helyreállítási terv.
2. Az a **lépés** listára, válassza ki egy elemet. Ezt követően válassza **parancsfájl** vagy **manuális művelet**.
3. Adja meg, hogy a parancsfájl vagy a művelet előtt vagy után a kijelölt elem hozzáadásához. A parancsfájl helyét felfelé vagy lefelé áthelyezéséhez jelölje ki a **fel** vagy **le** gombokat.
4. Ha egy új VMM, válassza ki a **VMM parancsfájl feladatátvétel**. A **parancsfájl elérési útján**, írja be a relatív elérési utat a megosztáshoz. Például **\RPScripts\RPScript.PS1**.
5. Ha hozzáad egy Automation-runbook, adja meg az Automation-fiók, ahol a runbook is található. Ezután válassza ki az Azure-forgatókönyvek parancsfájlt.
6. Győződjön meg arról, hogy a parancsfájl megfelelően működik-e, tegye a helyreállítási terv feladatátvétel.

Parancsfájl vagy a runbook lehetőségek érhetők el, csak a következő esetekben, és a feladatátvétel, vagy a feladat-visszavétel során. A manuális műveletet is a feladatátvételi és a feladat-visszavétel érhető el.


|Forgatókönyv               |Feladatátvétel |Feladat-visszavétel |
|-----------------------|---------|---------|
|Azure – Azure         |Forgatókönyv |Forgatókönyv  |
|VMware – Azure        |Forgatókönyv |NA       | 
|VMM-replikáció Azure-ba           |Forgatókönyv |Szkript   |
|Replikálás Hyper-V-helyről az Azure-ba  |Forgatókönyv |NA       |
|A VMM VMM             |Szkript   |Szkript   |


## <a name="next-steps"></a>További lépések

* További információ [feladatátvételek futtató](site-recovery-failover.md).  
* A helyreállítási terv működés közben, olvassa el ezt a videót:
    
    > [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]
