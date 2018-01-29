---
title: "Hozzon létre, és szabja testre a helyreállítási terv feladatátvételi és az Azure Site Recovery helyreállítási |} Microsoft Docs"
description: "Ismerteti, hogyan hozhat létre és testreszabása az Azure Site Recovery szolgáltatásban a feladatátvétel és a virtuális gépek és fizikai kiszolgálók helyreállításához a helyreállítási terv"
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
ms.openlocfilehash: 9839a989246b28c1a194b8d1f0e99c1bd80ac2e5
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="create-recovery-plans"></a>A helyreállítási terv létrehozása


Ez a cikk tájékoztatást ad azokról a létrehozása és testreszabása a helyreállítási terv [Azure Site Recovery](site-recovery-overview.md).

Megjegyzéseit vagy kérdéseit a cikk alján, vagy az [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti fel.

 Hozzon létre helyreállítási tervek a következő:

* Adja meg a csoportok a feladataikat együtt átadó, amely majd együtt elindításához gépek.
* Gépek, együtt csoportosításával őket egy helyreállítási modellre függőségeinek csoport megtervezése. Például a feladatátvétel, és jelenítse meg az adott alkalmazást, akkor csoportjához összes, a virtuális gépek az adott alkalmazáshoz, a helyreállítási terv azonos csoportjához.
* A feladatátvételt. A helyreállítási terv futtathatja a teszteket, tervezett, vagy nem tervezett feladatátvételt.

## <a name="why-use-recovery-plans"></a>A helyreállítási terv miért érdemes használni?

A helyreállítási tervek segítségével tervezheti meg rendszeres helyreállítási folyamat kis független egység felügyelhető létrehozásával. Ezeket az egységeket általában jelöli egy alkalmazást a környezetben. Helyreállítási terv nem csak megadhatja a feladatütemezési, amelyben a virtuális gépek indítása, de is segítséget nyújt gyakori feladatok automatizálásához a helyreállítás során.


**Alapvetően egyik módja annak ellenőrzésére, hogy felkészült a áttelepülés a felhőbe, vagy vész-helyreállítási van biztosítva, hogy minden alkalmazás saját, a helyreállítási terv része, és a helyreállítási tervek mindegyikének tesztelni a Microsoft Azure helyreállítás. A készenléti rendelkező magabiztosan áttelepíthető vagy feladatátvételi Microsoft Azure a teljes adatközpont.**
 
Az alábbiakban a három legfontosabb értéknövelő a helyreállítási terv:

### <a name="model-an-application-to-capture-dependencies"></a>A modell egy alkalmazás függőségeit rögzítéséhez

A helyreállítási terv olyan virtuális gépek általában, az alkalmazás álló, hogy a feladatátvétel együtt. A helyreállítási terv használatával hoz létre, ennek a csoportnak a alkalmazásspecifikus tulajdonságokat rögzítéséhez fejlesztheti.
 
Ossza meg velünk igénybe vehet a példa egy tipikus három réteg alkalmazás

* egy SQL-háttérrendszer
* egy köztes
* egy webes előtér

Győződjön meg arról, hogy a virtuális gépek kapja meg a megfelelő sorrendben utáni feladatátvevő a helyreállítási terv testre szabható. Az SQL-háttérrendszer először érkezik, a köztes tovább kell elérni, és a webes előtér utolsó kell elérni. Ez a sorrend lehetővé teszi bizonyos, hogy működik-e az alkalmazás megjelenik az utolsó virtuális gép indításakor. Például a köztes megjelenik, amikor az megpróbál kapcsolódni az SQL-réteghez, és a helyreállítási terv gondoskodik, hogy már fut az SQL-réteg. Előtérbeli kiszolgálók utolsó közeledik is biztosítja, hogy a végfelhasználók ne csatlakoztassa az alkalmazás URL-cím tévedésből mindaddig, amíg minden mentése összetevői futnak, és az alkalmazás készen áll kérések fogadására. Ezeket a függőségeket létrehozásához testre szabhatja a helyreállítási tervet, ha csoportokat szeretne adni. Ezután válasszon ki egy virtuális gépet, és áthelyezi a csoportok közötti csoportjának módosítása.

![A minta helyreállítási terv](./media/site-recovery-create-recovery-plans/rp.png)

Ha befejezte a testreszabás, a pontos lépések az a helyreállítási jelenítheti meg. A lépések végrehajtása a helyreállítási terv feladatátvételi sorrendje a következő:

* Először van a leállítása lépést, amely megkísérli kapcsolja ki a virtuális gépek helyszíni (ha az elsődleges hely továbbra is futnia kell a feladatátvételi teszt csak)
* Ezután elindítja a helyreállítási terv párhuzamosan virtuális gépeinek feladatátvételi. A feladatátvételi lépés elkészíti a virtuális gépek lemezeit a replikált adatokat.
* Végül az indítási csoportok hajtható végre az sorrendben, először, indítsa el a virtuális gépek minden csoport – csoport 1 majd csoport 2, és végül 3 csoport. Ha egynél több virtuális gépek vannak a csoportban (például egy elosztott terhelésű webes előtér) az összes elindítja be párhuzamosan.

**Csoportok közötti sorrendi biztosítja, hogy a különböző alkalmazásrétegek közötti függőségeket is figyelembe véve, és párhuzamossági megfelelő javítja az alkalmazás helyreállítási RTO.**

   > [!NOTE]
   > Egyetlen csoport részét képező gépek fog megfelelni a feladatátvétel párhuzamosan. Gépek, amelyek különböző csoportokba tartozó fog megfelelni a feladatátvétel, a csoportok oder. Csak azután csoport 1 összes gép a feladatátvételt és a legközelebbi, a gépek csoport 2 indul el a feladatátvételt.

### <a name="automate-most-recovery-tasks-to-reduce-rto"></a>RTO csökkentése érdekében a legtöbb helyreállítási feladatok automatizálásához

Nagy alkalmazások visszaállítása egy összetett feladat lehet. Egyúttal nehéz megjegyezni, hogy a pontos testreszabási lépéseket feladatátvételi vagy az áttelepítés utáni. Egyes esetekben nincs meg, de valaki más, aki nem ismerik az alkalmazás menő bemutatása, akiknek el elindíthatja a feladatátvételt. Jelszóelőzmények chaos időpontja a túl sok manuális is nehézkes és nagyon eséllyel fordulnak elő hiba. A helyreállítási terv akadályozható meg a szükséges műveleteket kell venni a Microsoft Azure Automation-forgatókönyv használatával minden lépésnél automatizálásához. A runbookok közös helyreállítási feladatokat, például az alábbi példák automatizálható. Ezeket a feladatokat, amelyet nem lehet automatizálni, a helyreállítási terv valós manuális műveletek beszúrása lehetőséget.

* Feladatok az Azure virtuális gépen a feladatátvevő utáni – a következő: szükséges, hogy a Kapcsolódás a virtuális gépet, például:
    * Egy nyilvános IP-címet létrehozni a feladás egy vagy több virtuális gép feladatátvételi
    * Rendelje hozzá egy NSG-t a sikertelen virtuális gép hálózati keresztül
    * A terheléselosztó hozzáadása egy rendelkezésre állási csoportot
* A virtuális gépen belüli feladatok feladatátvétel utáni – ezek konfigurálja újra az alkalmazást, így továbbra is fennáll, megfelelő működéséhez az új környezetben, például:
    * Módosítsa a virtuális gépen belüli, az adatbázis-kapcsolati karakterlánc
    * Web server konfigurációs/szabályok módosítása

**A teljes helyreállítási tervet, amely automatizálja a feladás egy vagy több helyreállítási feladatok automation-forgatókönyveket egy kattintással feladatátvételi elérése, és optimalizálja a RTO.**

### <a name="test-failover-to-be-ready-for-a-disaster"></a>Készen áll egy olyan vészhelyzet esetén a feladatátvétel tesztelése

A helyreállítási terv segítségével indítás, feladatátvétel vagy a feladatátvételi tesztet. A feladatátvétel előtt az alkalmazást a feladatátvételi teszt mindig el kell végeznie. A feladatátvételi teszt segítségével ellenőrizze, hogy az alkalmazás fogja elérni a helyreállítási helyen.  Ha valami kihagyott, könnyen tisztítás aktiválhatja, és végezze el újra a feladatátvételi tesztet. Ezt a feladatátvételi tesztet több alkalommal amíg tudja biztosan, hogy zökkenőmentesen helyreállíthatja az alkalmazás.

![Helyreállítási terv teszt](./media/site-recovery-create-recovery-plans/rptest.png)

**Mindegyik alkalmazáshoz különböző, és minden testre szabott helyreállítási tervek készítéséhez szükséges. Is a dinamikus adatközpontba világában az alkalmazásokat és függőségi viszonyaikat tartsa módosítani. Feladatátvételi teszt futtatásával ellenőrizze, hogy a helyreállítási terv aktuális alkalmazásait negyedévenként.**

## <a name="how-to-create-a-recovery-plan"></a>A helyreállítási terv létrehozása

1. Kattintson a **helyreállítási tervek** > **helyreállítási terv létrehozása**.
   Adja meg a helyreállítási tervben és a forrás és cél nevét. A forráshely kell rendelkeznie, amelyek engedélyezve vannak a feladatátvétel és a helyreállítási virtuális gépek. Válassza ki a forrás és cél alapján a helyreállítási terv részeként kívánt virtuális gépeket. 

   |Forgatókönyv                   |Forrás               |Cél           |
   |---------------------------|---------------------|-----------------|
   |Azure – Azure             |Azure region         |Azure-régió     |
   |VMware az Azure-bA            |Configuration server |Azure            |
   |VMM-replikáció Azure-ba               |A VMM rövid neve    |Azure            |
   |A Hyper-v hely az Azure-bA      |Hyper-v hely neve    |Azure            |
   |Fizikai gépek az Azure-bA |Configuration server |Azure            |
   |A VMM VMM                 |A VMM rövid neve    |A VMM rövid neve|

   > [!NOTE]
   > A helyreállítási terv forrás és cél azonos virtuális gépek is tartalmazhat. VMware és a VMM virtuális gépek nem lehet azonos a helyreállítási terv része. VMware virtuális gépek és fizikai gépek azonban lehet helyezni őket forrása a konfigurációs kiszolgáló ugyanahhoz a csomaghoz fel.

2. A **válassza ki a virtuális gépek**, válassza ki a hozzáadni kívánt az alapértelmezett (csoportot % 1) a helyreállítási tervben szereplő kívánt virtuális gépek (vagy replikációs csoport). A kijelölés csak ezen virtuális gépek (a kijelölt helyreállítási tervben) védett a forráson és a cél (a kijelölt helyreállítási tervben) védett engedélyezett.

## <a name="how-to-customize-and-extend-recovery-plans"></a>Hogyan testreszabására és kibővítésére helyreállítási tervek

Testre szabhatja és a helyreállítási terv kiterjesztése a Site Recovery helyreállítási terv erőforráspanelre fogja, majd kattintson a Testreszabás fülre.

Testre szabhatja és a helyreállítási terv kiterjesztése:

- **Új csoportok hozzáadása**– vegye fel a további helyreállítási terv csoportot (legfeljebb 7) az alapértelmezett csoporthoz, majd további gépeket vagy replikációs csoport helyreállítási terv csoportokhoz. Csoportok számozása a sorrendben, ahol hozzá őket. Egy virtuális géphez, vagy a replikációs csoport esetében is csak egy helyreállítási terv csoportba foglalandó.
- **Adja hozzá a manuális műveletet**– manuális műveletek előtt vagy a helyreállítási terv csoport után is hozzáadhat. A helyreállítási terv fut, leállítja a pontnál, amelyen a manuális műveletet beszúrni. Egy párbeszédpanelen kéri, hogy adja meg, hogy befejeződött-e a manuális műveletet.
- **A parancsprogramok hozzáadásához**– előtt vagy után egy helyreállítási terv csoport futtatott parancsfájlok, adhat hozzá. Amikor egy új, hozzáadja a csoport műveletek új készletét. Például a csoport 1 előtti ismertetett lépések jön létre a name: csoport 1: előtti lépéseket. Ebben a készletben található összes előtti lépések jelennek meg. Csak adhat hozzá parancsfájl az elsődleges helyen, ha a VMM-kiszolgáló telepítve van. [További információk](site-recovery-how-to-add-vmmscript.md).
- **Adja hozzá az Azure runbookok**– kiterjesztheti a helyreállítási terv Azure runbookok. Például feladatok automatizálásához, vagy hozzon létre egylépéses helyreállítási. [További információk](site-recovery-runbook-automation.md).


## <a name="how-to-add-a-script-runbook-or-manual-action-to-a-plan"></a>Egy parancsfájl, a runbookot vagy a manuális műveletet hozzáadása egy csomaghoz

Hozzáadhat egy parancsfájl vagy a manuális műveletet az alapértelmezett helyreállítási terv csoporthoz hozzáadott virtuális gépek vagy a replikációs csoport, és a csomag létrehozása után.

1. Nyissa meg a helyreállítási terv.
2. Bármely elemére a **lépés** listára, majd **parancsfájl** vagy **manuális művelet**.
3. Adja meg, hogy a parancsfájl vagy a művelet előtt vagy után a kijelölt elem hozzáadásához. A parancsfájl helyét felfelé vagy lefelé áthelyezéséhez használja a **fel** és **le** gombokat.
4. Ha egy új VMM, válassza ki a **VMM parancsfájl feladatátvétel**. A **parancsfájl elérési útján**, írja be a relatív elérési utat a megosztáshoz. A VMM az alábbi példában, adja meg az elérési út: **\RPScripts\RPScript.PS1**.
5. Ha hozzáad egy Azure Automation szolgáltatásbeli könyv futtassa, adja meg, ahol a runbook is található Azure Automation-fiók, és válassza ki a megfelelő Azure-forgatókönyvek parancsfájlt.
6. Győződjön meg arról, hogy a parancsfájl megfelelően működik-e, tegye a helyreállítási terv feladatátvétel.

Az parancsfájl vagy a runbook beállítások érhetők el, csak a következő esetekben, a feladatátvétel, vagy a feladat-visszavétel során. A manuális műveletet feladatátvételi és a feladat-visszavétel érhető el.


|Forgatókönyv               |Feladatátvétel |Feladat-visszavétel |
|-----------------------|---------|---------|
|Azure – Azure         |Runbookok |Forgatókönyv  |
|VMware az Azure-bA        |Runbookok |NA       | 
|VMM-replikáció Azure-ba           |Runbookok |Szkript   |
|Az Azure-bA Hyper-v hely  |Runbookok |NA       |
|A VMM VMM             |Szkript   |Szkript   |


## <a name="next-steps"></a>További lépések

[További](site-recovery-failover.md) feladatátvételek futtatásával kapcsolatos.

Ezt a videót tekintse meg a helyreállítás, tervezze meg a műveletet.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]
