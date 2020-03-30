---
title: Az Azure HANA nagy példányok vezérelése az Azure Portalon keresztül | Microsoft dokumentumok
description: Bemutatja, hogyan azonosíthatja és kommunikálhatja az Azure HANA nagypéldányait a portálon keresztül
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c14ff9c4f6d2bc2b1a62d1874d01950d09491c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70099820"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Az Azure HANA nagyméretű példányok vezérlése az Azure Portalon keresztül
Ez a dokumentum bemutatja, hogyan [hana nagy példányok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) jelennek meg az [Azure Portalon,](https://portal.azure.com) és milyen tevékenységeket lehet végrehajtani az Azure Portalon keresztül hana nagy példány egységek, amelyek az Ön számára telepített. A HANA nagypéldányainak láthatósága az Azure Portalon egy Azure-erőforrás-szolgáltatón keresztül érhető el a HANA nagy példányok számára, amely jelenleg nyilvános előzetes verzióban érhető el

## <a name="register-hana-large-instance-resource-provider"></a>HANA nagypéldány-erőforrás-szolgáltató regisztrálása
Általában a HANA nagy példány okait használt Azure-előfizetés regisztrálva van a HANA nagy példány erőforrás-szolgáltató. Azonban ha nem látja üzembe hana nagy példány egységek, regisztrálnia kell az erőforrás-szolgáltató az Azure-előfizetésben. A HANA nagypéldány-erőforrás-szolgáltató regisztrálása kétféleképpen rendelkezik

### <a name="register-through-cli-interface"></a>Regisztráció cli interfészen keresztül
Be kell jelentkeznie az Azure-előfizetésbe, a HANA nagy példány üzembe helyezéséhez az Azure CLI felületen keresztül. Ezzel a paranccsal (újra)regisztrálhatja a HANA nagypéldány-szolgáltatót:
    
    az provider register --namespace Microsoft.HanaOnAzure

További információt az [Azure-erőforrás-szolgáltatók és -típusok](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli) című cikkben talál.


### <a name="register-through-azure-portal"></a>Regisztráció az Azure Portalon keresztül
(újra)regisztrálhatja a HANA nagy példány erőforrás-szolgáltató az Azure Portalon keresztül. Az előfizetést az Azure Portalon kell felsorolnia, és duplán kell kattintania az előfizetésre, amely a HANA nagy példányegység(ek) üzembe helyezéséhez volt használva. Az előfizetés áttekintő lapján válassza az "Erőforrás-szolgáltatók" lehetőséget az alábbi módon, és írja be a "HANA" kifejezést a keresési ablakba. 

![HlI RP regisztrálása az Azure portalon keresztül](./media/hana-li-portal/portal-register-hli-rp.png)

A megjelenített képernyőképen az erőforrás-szolgáltató már regisztrálva van. Abban az esetben, ha az erőforrás-szolgáltató még nincs regisztrálva, nyomja meg az "újraregisztráció" vagy a "regisztráció" gombot.

További információt az [Azure-erőforrás-szolgáltatók és -típusok](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell) című cikkben talál.


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Hana nagy példányegységek megjelenítése az Azure Portalon
Hana nagy példány központi telepítési kérelem küldésekor a rendszer kéri, hogy adja meg az Azure-előfizetést, amely a HANA nagy példányokhoz csatlakozik is. Ajánlott ugyanazt az előfizetést használni, amely a HANA nagy példány egységekkel működik az SAP-alkalmazásréteg üzembe helyezéséhez.
Az első HANA nagy példányok üzembe helyezésekor egy új [Azure-erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) jön létre a HANA nagy példány(ok) üzembe helyezési kérelemben elküldött Azure-előfizetésben.  Az új erőforráscsoport felsorolja az adott előfizetésben üzembe helyezett hana nagy példány egységek listáját.

Az új Azure-erőforráscsoport megkereséséhez az erőforráscsoportot az előfizetésben sorolja fel az Azure Portal bal oldali navigációs ablakában való navigálással.

![Navigációs ablak az Azure Portalon](./media/hana-li-portal/portal-resource-group.png)

Az erőforráscsoportok listájában, a listában, előfordulhat, hogy szűrnie kell az előfizetést, amelyen a HANA nagy példányok üzembe helyezése

![Erőforráscsoportok szűrése az Azure Portalon](./media/hana-li-portal/portal-filtering-subscription.png)

A megfelelő előfizetésre történő szűrés után továbbra is előfordulhat, hogy hosszú erőforráscsoportok listája van. Keresse meg az egyik utáni fix a **-Txxx,** ahol "xxx" három számjegyű, mint **a -T050**. 

Az erőforráscsoport adatainak felsorolása kor megjelenik a részletes adatok. A kapott lista így nézhet ki:

![HLI-lista az Azure Portalon](./media/hana-li-portal/portal-hli-units-list.png)

A felsorolt egységek egy hana nagy példány egység, amely az előfizetésben üzembe helyezett egyetlen. Ebben az esetben nyolc különböző HANA nagy példányegységek, amelyek az előfizetésben üzembe helyezett.

Ha több HANA nagypéldány-bérlőt telepített ugyanahhoz az Azure-előfizetéshez, több Azure-erőforráscsoportot is meg fog találni 


## <a name="look-at-attributes-of-single-hli-unit"></a>Nézd meg attribútumok egyetlen HLI egység
A HANA nagy példány egységek listájában rákattinthat egy egységre, és megkaphatja az egyetlen HANA nagy példány egység részleteit. 

Az áttekintő képernyőn, miután a "Továbbiak megjelenítése" gombra kattintott, megjelenik az egység bemutatása, amely így néz ki:

![HLI-egység áttekintésének megjelenítése](./media/hana-li-portal/portal-show-overview.png)

A különböző jellemzők látható, ezek az attribútumok meg alig különbözik az Azure VM-attribútumok. A bal oldali fejlécen az erőforráscsoport, az Azure-régió, az előfizetés neve és az azonosító, valamint néhány hozzáadott címkék et jelenít meg. Alapértelmezés szerint a HANA nagy példány egységek nincs címke hozzárendelve. A fejléc jobb oldalán az egység neve a központi telepítés befejeztével a hozzárendeltként jelenik meg. Megjelenik az operációs rendszer, valamint az IP-cím. A virtuális gépekhez is a HANA Large példányegység típusa a PROCESSZOR-szálak és a memória számával együtt is megjelenik. További részletek a különböző HANA nagy példány egységek, itt látható:

- [HLI-hez elérhető termékváltozatok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [SAP HANA (nagy példányok) tárolási architektúrája](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

További adatok a jobb alsó oldalon a hana nagy példány bélyegző felülvizsgálata. Lehetséges értékek:

- 3. módosítás
- 4.

4-es verzió a hana nagy példányok legújabb architektúrája, amely jelentős javulást a hálózati késés között az Azure virtuális gépek és a HANA Nagy példány egységek a 4-es verziójú bélyegzők vagy sorok között.
Egy másik nagyon fontos információ található a jobb alsó sarokban az áttekintés az Azure Proximity elhelyezési csoport, amely automatikusan létre jön az egyes üzembe helyezett HANA nagy példány egység. Ez a proximity elhelyezési csoport kell hivatkozni az SAP-alkalmazásréteget üzemeltető Azure virtuális gépek üzembe helyezésekor. A HANA nagy példány egységhez társított [Azure közelség-elhelyezési csoport](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) használatával győződjön meg arról, hogy az Azure virtuális gépek a HANA nagy példány egység közvetlen közelében vannak telepítve. Az Azure Közelségelhelyezési csoportok ismertetik, hogyan használható a közelségi elhelyezési csoportok az SAP-alkalmazásréteg megkeresésére ugyanabban az Azure-adatközpontban, mint a 4-es verzióban üzemeltetett HANA nagy példányegységek az [Azure Közelségelhelyezési csoportok ban, hogy optimális hálózati késést biztosíthasson az SAP-alkalmazásokkal.](sap-proximity-placement-scenarios.md)

A fejléc jobb oldali oszlopában egy további mező tájékoztatja a HANA Large példányegység energiaállapotát.

> [!NOTE]
> Az energiaállapot azt írja le, hogy a hardveregység be van-e kapcsolva vagy ki van kapcsolva. Nem ad információt az operációs rendszer működéséről. A HANA nagy példány egység újraindítása közben egy kis időt fog tapasztalni, amikor az egység állapota **Indításkor** az **Elindítva**állapotba kerül. Mivel az állapot az **elindítva** azt jelenti, hogy az operációs rendszer elindul, vagy hogy az operációs rendszer teljesen elindult. Ennek eredményeképpen az egység újraindítása után nem számíthat arra, hogy azonnal bejelentkezik az egységbe, amint az állapot **elindítva**vált.
> 

Ha megnyomja a "Továbbiak" gombot, további információk jelennek meg. Egy további információ a HANA nagy példány bélyegzőjének, az egység üzembe helyezése. Tekintse meg a [cikket, mi az SAP HANA az Azure-ban (nagy példányok)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) a hana nagy példányok bélyegek különböző verziói

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Egyetlen HANA nagypéldány-egység tevékenységeinek ellenőrzése 
A HANA nagypéldány-egységek áttekintésén túl ellenőrizheti az adott egység tevékenységeit. A tevékenységnapló így nézhet ki:

![Navigációs ablak az Azure Portalon](./media/hana-li-portal/portal-activity-list.png)

Az egyik fő tevékenység rögzített újraindítása egy egység. A felsorolt adatok közé tartozik a tevékenység állapota, a tevékenység aktiválása időbélyegző, az előfizetés-azonosító, amelyből a tevékenység elindult, és az Azure-felhasználó, aki elindította a tevékenységet. 

Egy másik tevékenység, amely a rendszer rögzítése az egység az Azure metaadatok módosításai. A kezdeményezett újraindítás mellett láthatja a **HANAInstances írási tevékenységét.** Ez a tevékenységtípus nem hajt végre módosításokat a HANA nagy példány egység maga, de dokumentálja az egység metaadatainak módosításait az Azure-ban. A felsorolt esetben hozzáadtunk és töröltünk egy címkét (lásd a következő szakaszt).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Azure-címke hozzáadása és törlése egy HANA nagy példányegységhez
Egy másik lehetőség van, hogy hozzá egy [címkét](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) a HANA nagy példány egység. A címkék hozzárendelésének módja nem különbözik a címkék virtuális gépekhez való hozzárendelésétől. A virtuális gépekhez is a címkék léteznek az Azure metaadatokban, és a HANA nagy példányok esetében ugyanazok a korlátozások, mint a virtuális gépek címkéi.

A címkék törlése ugyanúgy működik, mint a virtuális gépek. Mindkét tevékenység, a címke alkalmazása és törlése szerepelni fog az adott HANA nagy példány egység tevékenységnaplójában.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>Hana nagy példányegység tulajdonságainak ellenőrzése
A **Tulajdonságok** szakasz fontos információkat tartalmaz, amelyeket akkor kap, amikor a példányokat átadják Önnek. Ez egy olyan szakasz, ahol minden olyan információt megkap, amelyre támogatási esetekben szüksége lehet, vagy amelyekre a tárolási pillanatkép-konfiguráció beállításakor szüksége lehet. Ilyenként ez a szakasz a példány körül, a példány azure-hoz és a storage-háttérrendszerhez való kapcsolódása közötti adatok gyűjteménye. A szakasz teteje így néz ki:


![a HLI-tulajdonságok felső része az Azure Portalon](./media/hana-li-portal/portal-properties-top.png)

Az első néhány adatelem már az áttekintő képernyőn látható. De az adatok fontos része az ExpressRoute-áramköri azonosító, amelyet az első telepített egységek átadásakor kapott. Egyes támogatási esetekben előfordulhat, hogy a rendszer kéri ezeket az adatokat. Egy fontos adatbevitel jelenik meg a képernyőkép alján. A megjelenített adatok az NFS-tárolófej IP-címe, amely elkülöníti a tárolót a **hana** nagy példányveremben lévő bérlő számára. Erre az IP-címre akkor is szükség van, ha szerkeszti a [konfigurációs fájlt a tárolási pillanatkép biztonsági másolataihoz.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots) 

Ahogy görgessen le a tulajdonság ablaktáblán, további adatokat kap, például egy egyedi erőforrás-azonosítóa a HANA nagy példány egység, vagy az előfizetés-azonosító, amely hozzá van rendelve a központi telepítéshez.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Hana nagypéldány-egység újraindítása az Azure Portalon keresztül
A Linux operációs rendszer újraindításának megindulva különböző helyzetekben az operációs rendszer nem tudta sikeresen befejezni az újraindítást. Az újraindítás kényszerítéséhez meg kell nyitnia egy szolgáltatáskérelmet, hogy a Microsoft-műveletek a HANA nagy példány egység energia-újraindítását hajtsák végre. A hana nagy példány egység energia-újraindításának funkciója most már integrálva van az Azure Portalon. A HANA nagypéldány egység áttekintő részében látható az újraindítás gombja az adatszakasz tetején

![Az #1 lépés újraindítása az Azure Portalon](./media/hana-li-portal/portal-restart-first-step.png)

Az újraindítás gomb megnyomásakor a rendszer megkérdezi, hogy valóban újra szeretné-e indítani az egységet. Ahogy megerősíti az "Igen" gomb megnyomásával, az egység újraindul.

> [!NOTE]
> Az újraindítási folyamat során egy kis időt fog tapasztalni, amikor az egység állapota **Indításkor az** Elindítva állapotba való beköltözésre **változik.** Mivel az állapot az **elindítva** azt jelenti, hogy az operációs rendszer elindul, vagy hogy az operációs rendszer teljesen elindult. Ennek eredményeképpen az egység újraindítása után nem számíthat arra, hogy azonnal bejelentkezik az egységbe, amint az állapot **elindítva**vált.

> [!IMPORTANT]
> A HANA nagypéldány-egységben lévő memória mennyiségétől függően a hardver és az operációs rendszer újraindítása akár egy órát is igénybe vehet


## <a name="open-a-support-request-for-hana-large-instances"></a>Támogatási kérelem megnyitása hana nagy példányok számára
Az Azure Portalon a HANA nagy példányegységek megjelenítése, támogatási kérelmeket hozhat létre kifejezetten egy HANA nagy példány egység is. Ahogy követi a linket **Új támogatási kérelem** 

![szolgáltatáskérési lépés kezdeményezése #1 az Azure Portalon](./media/hana-li-portal/portal-initiate-support-request.png)

Annak érdekében, hogy a következő képernyőn felsorolt SAP HANA nagypéldányok szolgáltatását megkapja, előfordulhat, hogy ki kell választania az "Összes szolgáltatás" lehetőséget az alábbiak szerint

![Az azure-portál összes szolgáltatásának kijelölése](./media/hana-li-portal/portal-create-service-request.png)

A szolgáltatások listájában található a szolgáltatás **SAP HANA nagy példány.** A szolgáltatás kiválasztásakor az alábbi módon választhatja ki a következő problémákat:


![Problémaosztály kiválasztása az Azure Portalon](./media/hana-li-portal/portal-select-problem-class.png)

A különböző problématípusok mindegyikében a problémás altípusok közül választhat, amelyeket ki kell választania a probléma további jellemzéséhez. Az altípus kiválasztása után most elnevezheti a tárgyat. Miután végzett a kiválasztási folyamattal, átléphet a létrehozás következő lépésére. A **Megoldások** szakaszban a HANA nagy példányok körüli dokumentációra mutat, amely a probléma megoldását mutatómutatót adhat. Ha a javasolt dokumentációban nem talál megoldást a problémára, folytassa a következő lépéssel. A következő lépésben a rendszer megkérdezi, hogy a probléma a virtuális gépek vagy a HANA nagy példány egységekkel. Ez az információ segít a támogatási kérelem nek a megfelelő szakemberekhez irányítani. 

![A támogatási eset részletei az Azure Portalon](./media/hana-li-portal/portal-support-request-details.png)

Ahogy válaszolt a kérdésekre, és további részleteket, akkor megy a következő lépés, hogy vizsgálja felül a támogatási kérelmet, és a benyújtás.

## <a name="next-steps"></a>További lépések

- [Az SAP HANA (nagy példányok) figyelése az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [HANA-oldali monitorozás és hibaelhárítás](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)

