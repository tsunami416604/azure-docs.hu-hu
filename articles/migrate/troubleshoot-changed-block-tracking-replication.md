---
title: Az ügynök nélküli VMware VM-áttelepítés replikálási hibáinak elhárítása
description: Segítség kérése a replikálási ciklus hibáihoz
author: anvar-ms
ms.manager: bsiva
ms.author: anvar
ms.topic: troubleshooting
ms.date: 08/17/2020
ms.openlocfilehash: 55e79877fb186a5ba2aece316c61f542adeda60c
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88796935"
---
# <a name="troubleshooting-replication-issues-in-agentless-vmware-vm-migration"></a>Az ügynök nélküli VMware VM-áttelepítés replikálási hibáinak elhárítása

Ez a cikk néhány gyakori problémát és olyan hibát ismertet, amely akkor fordulhat elő, ha a helyszíni VMware virtuális gépeket a Azure Migrate: Server Migration ügynök nélküli módszerrel replikálja.

Ha az ügynök nélküli replikációs módszer használatával replikál egy VMware virtuális gépet, a virtuális gép&#39;s lemezéről (VMDK) származó adatok replikálódnak az Azure-előfizetésében lévő replika felügyelt lemezekre. Amikor a replikáció elindul egy virtuális gépen, a kezdeti replikálási ciklus jelenik meg, amelyben a lemezek teljes másolata replikálódik. A kezdeti replikálás befejeződése után a növekményes replikálási ciklusok rendszeres időközönként átkerülnek az előző replikálási ciklus óta bekövetkezett változásokra.

Időnként előfordulhat, hogy a replikálási ciklusok egy virtuális gépen sikertelenek lesznek. Ezek a hibák olyan okok miatt fordulnak elő, amelyek a helyszíni hálózati konfigurációban felmerülő problémáktól függenek a Azure Migrate Cloud Service-háttérrel kapcsolatos problémák esetén. Ebben a cikkben a következőket tesszük:

 - Megtudhatja, hogyan figyelheti a replikálási állapotot, és Hogyan oldhatók meg a hibák.
 - Sorolja fel a gyakran előforduló replikálási hibákat, és javasoljon további lépéseket a szervizeléshez.

## <a name="monitor-replication-status-using-the-azure-portal"></a>Replikáció állapotának figyelése a Azure Portal használatával

A következő lépések segítségével figyelheti a virtuális gépek replikálási állapotát:

  1. Nyissa meg a Azure Portal Azure Migrate kiszolgálók lapját.
  2. Navigáljon a "kiszolgálók replikálása" lapra a kiszolgáló áttelepítésének csempén a "kiszolgálók replikálása" elemre kattintva.
  3. Ekkor megjelenik a replikáló kiszolgálók listája, valamint további információk, például állapot, állapot, utolsó szinkronizálás időpontja stb. Az Állapot oszlopban a virtuális gép aktuális replikációs állapota látható. Az Állapot oszlopban szereplő "Critical'or" figyelmeztetési érték általában azt jelzi, hogy a virtuális gép előző replikációs ciklusa meghiúsult. További részletekért kattintson a jobb gombbal a virtuális gépre, és válassza a "hiba részletei" lehetőséget. A hiba részletei lap a hibával kapcsolatos információkat és a hibakeresési útmutató további részleteit tartalmazza. Ekkor megjelenik egy "legutóbbi események" hivatkozás is, amely a virtuális gép események lapjára mutat.
  4. Kattintson a "legutóbbi események" elemre a virtuális gép előző replikációs ciklusa során fellépő hibák megtekintéséhez. Az Events (események) lapon keresse meg a virtuális gép replikálási ciklusa nem sikerült vagy a lemez replikálási ciklusa nem sikerült.
  5. Kattintson az eseményre a hiba lehetséges okainak és a javasolt szervizelési lépések megismeréséhez. A hiba elhárításához és javításához használja a megadott információkat.
    
## <a name="common-replication-errors"></a>Gyakori replikációs hibák

Ez a szakasz néhány gyakori hibát ismertet, valamint azt, hogy miként lehet elhárítani őket.

## <a name="disposeartefactstimedout"></a>DisposeArtefactsTimedOut

**Hiba azonosítója:** 181008

**Hibaüzenet:** VM: VMName. Hiba: a (z) "DisposeArtefactsTimeout" időtúllépési esemény szerepel a (z) "[" átjáró. Service. StateMachine. SnapshotReplication. SnapshotReplicationEngine + WaitingForArtefactsDisposalPreCycle "(" WaitingForArtefactsDisposalPreCycle ")] & állapotban.

**Lehetséges okok:**

Az Azure-ba replikálni próbált összetevő vagy nem válaszol. A lehetséges okok a következők:

- Az Azure Migrate készüléken futó átjárószolgáltatás nem működik.
- Az átjárószolgáltatás csatlakozási problémákba ütközik Service Bus/Event hub-vagy berendezés-Storage-fiókkal.

**A DisposeArtefactsTimedOut és a megfelelő megoldás pontos okának meghatározása:**

1. Ellenőrizze, hogy az Azure Migrate-berendezés működik-e.
2. Ellenőrizze, hogy fut-e az átjáró szolgáltatás a készüléken:
   1.  Jelentkezzen be a Azure Migrate készülékre a távoli asztal használatával, és tegye a következőket.

   2.  Nyissa meg a Microsoft Services MMC beépülő modult (futtassa > Services. msc fájlt), és ellenőrizze, hogy fut-e a "Microsoft Azure Gateway Service". Ha a szolgáltatás leáll vagy nem fut, indítsa el a szolgáltatást. Azt is megteheti, hogy megnyithatja a parancssort vagy a PowerShellt, és megteheti: "net start asrgwy"

3. Kapcsolódási problémák keresése Azure Migrate készülék és a berendezés Storage-fiókja között: 

    Futtassa a következő parancsot a azcopy letöltése után a Azure Migrate berendezésben:
    
    _azcopy pad https://[fiók]. blob. Core. Windows. net/[Container]? SAS_
    
    **A teljesítmény-teljesítményteszt teszt futtatásának lépései:**
    
      1. Azcopy [letöltése](https://go.microsoft.com/fwlink/?linkid=2138966)
        
      2. Keresse meg a készülék Storage-fiókját az erőforráscsoport csoportban. A Storage-fiók neve hasonlít a migrategwsa \* \* \* \* \* \* \* \* \* \* . A (z) [account] paraméter értéke a fenti parancsban.
        
      3. Keresse meg a Storage-fiókját a Azure Portalban. Győződjön meg arról, hogy a kereséshez használt előfizetés ugyanaz az előfizetés (célként megadott előfizetés), amelyben a Storage-fiókot létrehozták. Nyissa meg a tárolókat a blob szolgáltatás szakaszban. Kattintson a + tároló elemre, és hozzon létre egy tárolót. Hagyja a nyilvános hozzáférési szintet az alapértelmezett kijelölt értékre.
        
      4. Nyissa meg a közös hozzáférési aláírást a beállítások területen. Válassza ki a tárolót az "engedélyezett erőforrástípus" mezőben. Kattintson a SAS létrehozása és a kapcsolatok karakterlánca elemre. Másolja az SAS értéket.
        
      5. Hajtsa végre a fenti parancsot a parancssorban a fiók, a tároló, az SAS és a 2., 3. és 4. lépésben leírtak szerint.
        
      Azt is megteheti, hogy [letölti](https://go.microsoft.com/fwlink/?linkid=2138967) az Azure Storage-t a készülékre, és megpróbál 10 blobot feltölteni ~ 64 MB-ra a Storage-fiókokba. Ha nincs probléma, a feltöltésnek sikeresnek kell lennie.
        
    **Megoldás:** Ha ez a teszt sikertelen,&#39;s hálózati probléma van. A kapcsolódási problémák ellenőrzése érdekében folytassa a helyi hálózati csapattal. Jellemzően előfordulhat, hogy bizonyos tűzfalbeállítások okoznak hibákat.
    
4.  Azure Migrate készülék és Service Bus közötti kapcsolati problémák keresése:

    Ez a teszt ellenőrzi, hogy az Azure Migrate készülék tud-e kommunikálni a Azure Migrate Cloud Service-háttérrel. A készülék Service Bus és az Event hub üzenetsor-várólistáján keresztül kommunikál a szolgáltatás-háttérrel. Ha ellenőrizni szeretné a berendezés kapcsolatát a Service Bus, [töltse le](https://go.microsoft.com/fwlink/?linkid=2139104) a Service Bus Explorert, próbáljon meg csatlakozni a készülékhez Service Bus, és hajtson végre üzenetküldési/fogadási üzenetet. Ha nincs probléma, ennek sikeresnek kell lennie.

    **A teszt futtatásának lépései:**

    1. Másolja az áttelepítés projektben létrehozott Service Bus a kapcsolatok karakterláncát.
    2. A Service Bus Explorer megnyitása
    3. Ugrás a fájlra, majd a kapcsolódás
    4. Illessze be a kapcsolati karakterláncot, és kattintson a csatlakoztatás gombra
    5. Ekkor megnyílik Service Bus a névtér
    6. Válassza a Snapshot Manager lehetőséget a következő témakörben:. Kattintson a jobb gombbal a Snapshot Manager elemre, válassza az "üzenetek fogadása" lehetőséget > válassza a betekintés lehetőséget, majd kattintson az OK gombra.
    7. Ha a kapcsolatok sikeresek, a konzol kimenetén a "[x] fogadott üzenetek" üzenet jelenik meg. Ha a kapcsolatok sikertelenek, megjelenik egy üzenet, amely jelzi, hogy a csatlakoztatás nem sikerült
    
    **Megoldás:** Ha ez a teszt sikertelen, hálózati probléma van. A kapcsolódási problémák ellenőrzése érdekében folytassa a helyi hálózati csapattal. Jellemzően előfordulhat, hogy bizonyos tűzfalbeállítások okoznak hibákat.

5. Kapcsolódási problémák Azure Migrate berendezés és Azure Key Vault között:

    Ez a teszt ellenőrzi a Azure Migrate berendezés és a Azure Key Vault közötti kapcsolódási problémákat. A Key Vault a replikáláshoz használt Storage-fiókhoz való hozzáférés kezelésére szolgál.
    
    **A kapcsolat ellenőrzési lépései:**
    
    1. A Key Vault URI beolvasása a Azure Migrate projektnek megfelelő erőforráscsoport erőforrásainak listájából.
    
    1. Nyissa meg a PowerShellt a Azure Migrate berendezésben, és futtassa a következő parancsot:
    
    _test-NETCONNECTION Key Vault URI-P 443_
    
    Ez a parancs megkísérli a TCP-kapcsolatokat, és kimenetet ad vissza.
    
     - A kimenetben keresse meg a "_TcpTestSucceeded_" mezőt. Ha az érték "true" (_igaz_), nincs kapcsolati probléma a Azure Migrate berendezés és a Azure Key Vault között. Ha az érték "false" (hamis), akkor csatlakozási probléma van.
    
    **Megoldás:** Ha ez a teszt sikertelen, a Azure Migrate berendezés és a Azure Key Vault között csatlakozási probléma lép fel. A kapcsolódási problémák ellenőrzése érdekében folytassa a helyi hálózati csapattal. Jellemzően előfordulhat, hogy bizonyos tűzfalbeállítások okoznak hibákat.
    
## <a name="diskuploadtimedout"></a>DiskUploadTimedOut

**Hiba azonosítója:** 1011

**Hibaüzenet:** Adatok feltöltése a lemez DiskPath, a virtuális gép VMName; A VMId nem fejeződött be a várt időn belül.

Ez a hiba általában azt jelzi, hogy a replikálást végző Azure Migrate berendezés nem tud csatlakozni az Azure-Cloud Serviceshoz, vagy ha a replikáció lassan halad, ami időtúllépést okoz a replikálási ciklusban.

A lehetséges okok a következők:

- Az Azure Migrate készülék nem áll le.
- A rendszer nem futtatja a replikációs átjáró szolgáltatást a készüléken.
- A Replication Gateway szolgáltatás kapcsolódási problémákba ütközik az alábbi, a replikáláshoz használt Azure-szolgáltatások egyik összetevőjénél: Service Bus/Event hub/Azure gyorsítótár Storage-fiók/Azure Key Vault.
- A rendszer a vCenter szintjén szabályozza az átjáró szolgáltatást, miközben megpróbálja beolvasni a lemezt.

**Az alapvető ok azonosítása és a probléma megoldása:**

1. Ellenőrizze, hogy az Azure Migrate-berendezés működik-e.
2. Ellenőrizze, hogy fut-e az átjáró szolgáltatás a készüléken:
   1.  Jelentkezzen be a Azure Migrate készülékre a távoli asztal használatával, és tegye a következőket.

   2.  Nyissa meg a Microsoft Services MMC beépülő modult (futtassa > Services. msc fájlt), és ellenőrizze, hogy fut-e a "Microsoft Azure Gateway Service". Ha a szolgáltatás leáll vagy nem fut, indítsa el a szolgáltatást. Másik lehetőségként megnyithatja a parancssort vagy a PowerShellt, és megteheti a következőt: "net start asrgwy".


3. **Kapcsolódási problémák keresése Azure Migrate készülék és a gyorsítótár Storage-fiók között:** 

    Futtassa a következő parancsot a azcopy letöltése után a Azure Migrate berendezésben:
    
    _azcopy pad https://[fiók]. blob. Core. Windows. net/[Container]? SAS_
    
    **A teljesítmény-teljesítményteszt teszt futtatásának lépései:**
    
      1. Azcopy [letöltése](https://go.microsoft.com/fwlink/?linkid=2138966)
        
      2. Keresse meg a készülék Storage-fiókját az erőforráscsoport csoportban. A Storage-fiók neve hasonlít a migratelsa \* \* \* \* \* \* \* \* \* \* . A (z) [account] paraméter értéke a fenti parancsban.
        
      3. Keresse meg a Storage-fiókját a Azure Portalban. Győződjön meg arról, hogy a kereséshez használt előfizetés ugyanaz az előfizetés (célként megadott előfizetés), amelyben a Storage-fiókot létrehozták. Nyissa meg a tárolókat a blob szolgáltatás szakaszban. Kattintson a + tároló elemre, és hozzon létre egy tárolót. Hagyja a nyilvános hozzáférési szintet az alapértelmezett kijelölt értékre.
        
      4. Nyissa meg a közös hozzáférési aláírást a beállítások területen. Válassza ki a tárolót az "engedélyezett erőforrástípus" mezőben. Kattintson a SAS létrehozása és a kapcsolatok karakterlánca elemre. Másolja az SAS értéket.
        
      5. Hajtsa végre a fenti parancsot a parancssorban a fiók, a tároló, az SAS és a 2., 3. és 4. lépésben leírtak szerint.
        
      Azt is megteheti, hogy [letölti](https://go.microsoft.com/fwlink/?linkid=2138967) az Azure Storage-t a készülékre, és megpróbál 10 blobot feltölteni ~ 64 MB-ra a Storage-fiókokba. Ha nincs probléma, a feltöltésnek sikeresnek kell lennie.
        
    **Megoldás:** Ha ez a teszt sikertelen,&#39;s hálózati probléma van. A kapcsolódási problémák ellenőrzése érdekében folytassa a helyi hálózati csapattal. Jellemzően előfordulhat, hogy bizonyos tűzfalbeállítások okoznak hibákat.
                
4.  **Kapcsolódási problémák Azure Migrate berendezés és Azure Service Bus között:**

    Ez a teszt azt ellenőrzi, hogy az Azure Migrate készülék tud-e kommunikálni a Azure Migrate Cloud Service-háttérrel. A készülék Service Bus és az Event hub üzenetsor-várólistáján keresztül kommunikál a szolgáltatás-háttérrel. Ha ellenőrizni szeretné a berendezés kapcsolatát a Service Bus, [töltse le](https://go.microsoft.com/fwlink/?linkid=2139104) a Service Bus Explorert, próbáljon meg csatlakozni a készülékhez Service Bus, és hajtson végre üzenetküldési/fogadási üzenetet. Ha nincs probléma, ennek sikeresnek kell lennie.

    **A teszt futtatásának lépései:**
    
    1. Másolja a kapcsolódási karakterláncot a Azure Migrate projektnek megfelelő erőforráscsoporthoz létrehozott Service Bus.
    
    1. Service Bus Explorer megnyitása
    
    1. Ugrás a fájl > kapcsolódásra
    
    1. Illessze be az 1. lépésben átmásolt kapcsolati karakterláncot, majd kattintson a csatlakoztatás gombra.
    
    1. Ekkor megnyílik Service Bus névtér.
    
    1. A névtér témakörében válassza a Snapshot Manager elemet. Kattintson a jobb gombbal a Snapshot Manager elemre, válassza az "üzenetek fogadása" lehetőséget > válassza a "betekintés" lehetőséget, majd kattintson az OK gombra.
    
    Ha a kapcsolatok sikeresek, a konzol kimenetén a "[x] fogadott üzenetek" üzenet jelenik meg. Ha a kapcsolatok nem sikerültek, megjelenik egy üzenet, amely tájékoztatja, hogy a csatlakoztatás sikertelen volt.
    
    **Megoldás:** Ha ez a teszt sikertelen, a Azure Migrate berendezés és a Service Bus között csatlakozási probléma lép fel. A kapcsolódási problémák ellenőrzése érdekében folytassa a helyi hálózatkezelési csapatával. Jellemzően előfordulhat, hogy bizonyos tűzfalbeállítások okoznak hibákat.
    
 5. **Kapcsolódási problémák Azure Migrate berendezés és Azure Key Vault között:**

    Ez a teszt ellenőrzi a Azure Migrate berendezés és a Azure Key Vault közötti kapcsolódási problémákat. A Key Vault a replikáláshoz használt Storage-fiókhoz való hozzáférés kezelésére szolgál.
    
    **A kapcsolat ellenőrzési lépései:**
    
    1. A Key Vault URI beolvasása a Azure Migrate projektnek megfelelő erőforráscsoport erőforrásainak listájából.
    
    1. Nyissa meg a PowerShellt a Azure Migrate berendezésben, és futtassa a következő parancsot:
    
    _test-NETCONNECTION Key Vault URI-P 443_
    
    Ez a parancs megkísérli a TCP-kapcsolatokat, és kimenetet ad vissza.
    
    1. A kimenetben keresse meg a "_TcpTestSucceeded_" mezőt. Ha az érték "true" (_igaz_), nincs kapcsolati probléma a Azure Migrate berendezés és a Azure Key Vault között. Ha az érték "false" (hamis), akkor csatlakozási probléma van.
    
    **Megoldás:** Ha ez a teszt sikertelen, a Azure Migrate berendezés és a Azure Key Vault között csatlakozási probléma lép fel. A kapcsolódási problémák ellenőrzése érdekében folytassa a helyi hálózati csapattal. Jellemzően előfordulhat, hogy bizonyos tűzfalbeállítások okoznak hibákat.
    
## <a name="encountered-an-error-while-trying-to-fetch-changed-blocks"></a>Hiba történt a módosult blokkok beolvasására tett kísérlet során

Hibaüzenet: "hiba történt a változási blokkok beolvasására tett kísérlet során"

Az ügynök nélküli replikációs módszer a VMware módosított blokk-követési technológiáját (CBT) használja az Azure-ba történő adatreplikáláshoz. A CBT lehetővé teszi, hogy a kiszolgáló áttelepítési eszköze csak a legutóbbi replikációs ciklus óta megváltoztatott blokkokat kövesse nyomon és replikálja. Ez a hiba akkor fordul elő, ha alaphelyzetbe állítják a módosított blokkok követését a replikáló virtuális gépen, vagy ha a módosított blokkok nyomkövetési fájlja megsérül.

Ezt a hibát a következő két módon lehet feloldani:

- Ha a virtuális gép replikálásának elindítását követően az "igen" lehetőséget választotta a "replikáció automatikus javítása" beállításnál, az eszköz megpróbálja kijavítani. Kattintson a jobb gombbal a virtuális gépre, és válassza a "replikáció javítása" lehetőséget.
- Ha nem választotta ki a "replikáció automatikus javítása" vagy a fenti lépés nem működött, akkor állítsa le a virtuális gép replikálását, [állítsa alaphelyzetbe a megváltozott blokk nyomon követését](https://go.microsoft.com/fwlink/?linkid=2139203) a virtuális gépen, majd konfigurálja újra a replikációt.

Egy ilyen ismert probléma, amely a virtuális gép CBT-visszaállítását okozhatja VMware vSphere 5,5-es verzióban a [VMware KB 2048201: a megváltozott blokk követése](https://go.microsoft.com/fwlink/?linkid=2138888) alaphelyzetbe áll a vSphere 5. x vMotion művelete után. Amennyiben a VMware vSphere 5.5-ös verzióját használja, győződjön meg arról, hogy telepítve vannak a tudásbáziscikkben leírt frissítések.

Azt is megteheti, hogy [alaphelyzetbe állítja a VMware PowerCLI-t használó virtuális gépeken a VMware changed Block követését.

## <a name="an-internal-error-occurred"></a>Belső hiba történt

Időnként előfordulhat, hogy a VMware-környezet/API-ban felmerülő problémák miatt hiba lép fel. A következő hibákat azonosította a VMware környezettel kapcsolatos hibákkal. Ezek a hibák rögzített formátumúak.

_Hibaüzenet: belső hiba történt. [Hibaüzenet]_

Például: hibaüzenet: belső hiba történt. [A rendszer érvénytelen pillanatkép-konfigurációt észlelt.)

A következő szakasz a gyakran előforduló VMware-hibákat és azok enyhítését ismerteti.

### <a name="error-message-an-internal-error-occurred-server-refused-connection"></a>Hibaüzenet: belső hiba történt. [A kiszolgáló elutasította a kapcsolatokat]

A probléma egy ismert VMware-probléma, amely a VDDK 6,7-es verziójában fordul elő. Le kell állítania az átjáró szolgáltatást a Azure Migrate készüléken, le kell [töltenie egy frissítést a VMware kb-ból](https://go.microsoft.com/fwlink/?linkid=2138889), majd újra kell indítania az átjáró szolgáltatást.

Az átjáró szolgáltatás leállításának lépései:

1. Nyomja le a Windows + R, majd az Open Services. msc parancsot. Kattintson a "Microsoft Azure Gateway Service" (átjáró szolgáltatás) elemre, és állítsa le.
2. Másik lehetőségként megnyithatja a parancssort vagy a PowerShellt, és a következőket teheti: net stop asrgwy. Várjon, amíg meg nem jelenik az üzenet, hogy a szolgáltatás már nem fut.

Az átjáró szolgáltatás elindításának lépései:

1. Nyomja le a Windows + R, majd az Open Services. msc parancsot. Kattintson a jobb gombbal a "Microsoft Azure Gateway Service" elemre, és indítsa el.
2. Másik lehetőségként megnyithatja a parancssort vagy a PowerShellt, és a következőket teheti: net start asrgwy.

### <a name="error-message-an-internal-error-occurred-an-invalid-snapshot-configuration-was-detected"></a>Hibaüzenet: belső hiba történt. [' A rendszer érvénytelen pillanatkép-konfigurációt észlelt. ']

Ha több lemezzel rendelkező virtuális géppel rendelkezik, akkor ez a hiba akkor jelentkezhet, ha eltávolít egy lemezt a virtuális gépről. A probléma megoldásához tekintse meg a [VMware-cikkben](https://go.microsoft.com/fwlink/?linkid=2138890)található lépéseket.

### <a name="error-message-an-internal-error-occurred-generate-snapshot-hung"></a>Hibaüzenet: belső hiba történt. [Pillanatkép előállítása felfüggesztve]

Ez a probléma akkor fordul elő, ha a pillanatkép-létrehozás nem válaszol. Ha ez a probléma bekövetkezik, a pillanatkép létrehozása feladat leáll a 95%-os vagy a 99%-nál. A probléma megoldása érdekében tekintse meg ezt a [VMware kb-ot](https://go.microsoft.com/fwlink/?linkid=2138969) .

### <a name="error-message-an-internal-error-occurred-failed-to-consolidate-the-disks-on-vm-_reasons_"></a>Hibaüzenet: belső hiba történt. [Nem sikerült összevonni a lemezeket a virtuális gépen _[ok]_]

Ha a replikálási ciklus végén összesíti a lemezeket, a művelet sikertelen lesz. Kövesse a [VMware kb](https://go.microsoft.com/fwlink/?linkid=2138970) utasításait a probléma megoldásához szükséges _OK_ kiválasztásával.

A következő hibák történnek, amikor a VMware pillanatképekkel kapcsolatos műveletek – a lemezek létrehozása, törlése vagy konszolidálása meghiúsul. A hibák elhárításához kövesse a következő szakaszban található útmutatást:

### <a name="error-message-an-internal-error-occurred-another-task-is-already-in-progress"></a>Hibaüzenet: belső hiba történt. [Egy másik feladat már folyamatban van]

Ez a probléma akkor fordul elő, ha ütköző virtuálisgép-feladatok futnak a háttérben, vagy ha a vCenter Serveron belül egy feladat túllépi az időkorlátot. Kövesse a következő [VMware kb](https://go.microsoft.com/fwlink/?linkid=2138891)-ban megadott megoldást.

### <a name="error-message-an-internal-error-occurred-operation-not-allowed-in-current-state"></a>Hibaüzenet: belső hiba történt. [A művelet a jelenlegi állapotban nem engedélyezett]

Ez a probléma akkor fordul elő, ha vCenter Server felügyeleti ügynökök nem működnek. A probléma megoldásához tekintse meg a következő [VMware kb](https://go.microsoft.com/fwlink/?linkid=2138971)-ban található megoldást.

### <a name="error-message-an-internal-error-occurred-snapshot-disk-size-invalid"></a>Hibaüzenet: belső hiba történt. [A pillanatkép lemezének mérete érvénytelen]

Ez egy ismert VMware-probléma, amelyben a pillanatkép által jelzett lemez mérete nulla lesz. Kövesse a [VMware kb](https://go.microsoft.com/fwlink/?linkid=2138972)-ban megadott felbontást.

### <a name="error-message-an-internal-error-occurred-memory-allocation-failed-out-of-memory"></a>Hibaüzenet: belső hiba történt. [A memória kiosztása nem sikerült. Nincs elég memória.]

Ez akkor fordul elő, ha az NFC-gazdagép puffere nem elegendő a memóriához. A probléma megoldásához át kell helyeznie a virtuális gépet (számítási vMotion) egy másik gazdagépre, amely ingyenes erőforrásokkal rendelkezik.

## <a name="next-steps"></a>További lépések

Folytassa a virtuális gép replikálását, és végezzen [tesztelési áttelepítést](https://go.microsoft.com/fwlink/?linkid=2139333).