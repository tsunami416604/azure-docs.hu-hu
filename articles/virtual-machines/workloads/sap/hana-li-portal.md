---
title: Azure HANA nagyméretű példányok szabályozhatja az Azure portal használatával |} A Microsoft Docs
description: Útmutatás a úgy azonosíthatja, és kommunikálhat a HANA nagyméretű példányok az Azure-portálon keresztül
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b186aa2692033a774d1d8f294315fcc0f5e874d5
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58763189"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Az Azure HANA nagyméretű példányok vezérlés az Azure portal használatával
Ez a dokumentum ismerteti a módja hogyan [HANA nagyméretű példányok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) láthatók [az Azure portal](https://portal.azure.com) és milyen tevékenységeket is végezni a nagyméretű HANA-példány egységekkel az Ön számára üzembe helyezett Azure-portálon keresztül. Nagyméretű HANA-példányokhoz látható-e az Azure Portalon egy Azure-erőforrás-szolgáltatón keresztül biztosítunk HANA nagyméretű példányok, amely jelenleg nyilvános előzetes verzióban érhető el

## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Az Azure Portalon nagyméretű HANA-példány egység megjelenítése
Egy nagyméretű HANA-példány üzembe helyezési kérelem küldése, amikor a rendszer felkéri az Azure-előfizetést, amely kapcsolódik a HANA nagyméretű példányokhoz is. Javasoljuk, hogy ugyanahhoz az előfizetéshez, használja az SAP alkalmazásrétegre nagyméretű HANA-példány egységek együttműködő üzembe helyezéséhez használja.
Az első, HANA nagyméretű példányok első telepített, új [Azure-erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) jön létre az Azure-előfizetés a nagyméretű HANA-példány üzembe helyezési kérés elküldött.  Az új erőforráscsoport felsorolja az összes telepítette, az adott előfizetés a nagyméretű HANA-példány egységeket.

Annak érdekében, hogy keresse meg az új Azure-erőforráscsoportot, listázza az erőforráscsoport az előfizetésben az Azure Portal bal oldali navigációs sávon a

![Navigációs ablaktábla az Azure Portalon](./media/hana-li-portal/portal-resource-group.png)

Az erőforráscsoportok listája, első felsorolt, szüksége lehet az előfizetést, amellyel rendelkezik nagyméretű HANA-példányok üzembe helyezett szűrés

![Szűrés erőforráscsoportok az Azure Portalon](./media/hana-li-portal/portal-filtering-subscription.png)

Szűrés a megfelelő előfizetésre, után előfordulhat, hogy még egy hosszú azon erőforráscsoportok listája. Keresse meg a utáni javítás az egyikben **- Txxx** ahol "xxx" három tizedesjegyet, például **-T050**. 

Az erőforráscsoport található, listázza a részleteit. A kapott listán nézhet:

![Az Azure Portalon HLI listája](./media/hana-li-portal/portal-hli-units-list.png)

Felsorolt összes egységének jelölő van az előfizetésben telepített egyetlen nagyméretű HANA-példány egységet. Ebben az esetben célszerű figyelni nyolc különböző nagyméretű HANA-példány egység, amely lettek telepítve az előfizetésében.


## <a name="look-at-attributes-of-single-hli-unit"></a>Tekintse meg egyetlen HLI egység attribútumai
A nagyméretű HANA-példány egységek listájában kattintson az egyetlen, és a részletek a egyetlen nagyméretű HANA-példány egység beolvasása. 

Az Áttekintés képernyő, az azért kapta, ha az egység, amely a következőhöz hasonló:

![Egy HLI egység áttekintése](./media/hana-li-portal/portal-show-overview.png)

A különböző attribútumokat látható megnézzük, ezek az attribútumok máshogy néznek ki alig, mint az Azure-beli Virtuálisgép-attribútumok. A bal alsó oldalán fej, bemutatja az erőforráscsoport, az Azure-régióban, előfizetés neve és azonosítója, valamint néhány hozzáadott címkék. Alapértelmezés szerint a a nagyméretű HANA-példány egységek nem hozzárendelt címkével rendelkezik. A jobb oldalon a fejléc az egység a neve jelenik meg az üzembe helyezés végeztével hozzárendelt. Az operációs rendszer és az IP-cím jelenik meg. Mint a virtuális gép a HANA nagyméretű példány egység írja be a CPU-szám a szálak és a memória látható is. További részleteket a különböző nagyméretű HANA-példány egység, itt látható:

- [HLI-hez elérhető termékváltozatok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [SAP HANA (nagyméretű példányok) tároló-architektúra](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

A fejléc jobb oldali oszlopban egy további mező tájékoztatja a HANA nagyméretű példány egységek power állapotát.

> [!NOTE]
> Az áramellátási állapot ismerteti-e a hardver egység be vagy ki van-e kapcsolva. Nem biztosít mentése folyamatban van, és futó operációs rendszer adatait. Újraindítja a nagyméretű HANA-példány egység, mert egy kis idő, ahol a egység állapota megváltozik, a tapasztalható **kezdő** állapotát átkerülni **elindítva**. Folyamatban van a szerinti **elindítva** azt jelenti, hogy az operációs rendszer indítása folyamatban van, illetve, hogy az operációs rendszer elindult teljesen. Ennek eredményeképpen az egység egy újraindítás után nem várt azonnal jelentkezzen be az egységet, amint a állapotot vált, amennyiben az **elindítva**.
> 


## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Ellenőrizze a tevékenység egyetlen nagyméretű HANA-példány egység 
Azon túl, hogy a nagyméretű HANA-példány egységek áttekintését, ellenőrizheti az adott egység tevékenységeket. Egy tevékenységnapló nézhet:

![Navigációs ablaktábla az Azure Portalon](./media/hana-li-portal/portal-activity-list.png)

A fő tevékenységeket rögzíteni egyik olyan egység újraindul. Megjelenik a tevékenység, a tevékenység lett elindítva, időbélyegző az előfizetés-azonosító / állapotának szerepel, amelyhez a tevékenység lett elindítva, és az Azure-felhasználó a tevékenység indító. 

Egy másik tevékenység első rögzített módosítások az egység az Azure meta adatok. Amellett, hogy az újraindítást kezdeményezett, láthatja tevékenységének **írási HANAInstances**. Ilyen típusú tevékenység nem végez módosítást hajt végre a nagyméretű HANA-példány egység magát, de dokumentálja a módosítások az egység az Azure-ban a metaadatok. Abban az esetben, hogy hozzá, és egy címke törlése (lásd a következő szakaszban).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Adja hozzá, és a egy nagyméretű HANA-példány egységhez egy Azure-címke törlése
Van egy másik lehetőség az, hogy adjon hozzá egy [címke](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) egy nagyméretű HANA-példány egységhez. Első hozzárendelt címkék módja nem különbözik a címkék hozzárendelése a virtuális gépek. A címkék létezik, az Azure metaadatai és HANA nagyméretű példányokhoz, virtuális gépekkel van ugyanazok a korlátozások vonatkoznak, a címkéket a virtuális gépek.

Címkék törlése ugyanúgy működik, mint a virtuális gépeket. Mindkét műveletre használná, alkalmazása és a egy címke törlése megjelenik a különösen nagyméretű HANA-példány egységek a tevékenységnaplóban.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>Ellenőrizze a nagyméretű HANA-példány egységek tulajdonságai
A szakasz **tulajdonságok** , ha a példányok vannak adják át Önnek fontos információkat tartalmaz. Egy szakaszt, ahol Ön minden adatának lekérése, a támogatást igénylő sikerült esetek, vagy amelyre pillanatkép tárolókonfigurációt beállítása során kell. Ez a szakasz ilyen gyűjteménye, a példány, a kapcsolat az Azure és a tárolási háttéralkalmazások példány adatait. A szakasz tetején hasonlóan néz ki:


![az Azure Portalon HLI tulajdonságok felső része](./media/hana-li-portal/portal-properties-top.png)

Az első néhány adatelemek bemutatta az Áttekintés képernyő már. De adatok fontos része, ha az első telepített egységek gyorsítási értéknek, átadná kapott ExpressRoute Circuit ID. Támogatási bizonyos esetekben előfordulhat, hogy első kéri az adatokat. Fontos adatok bejegyzés alján, a képernyőképen látható. A megjelenített adatok az az IP-cím, amely elkülöníti a tárolás NFS tárolási vezetője a **bérlői** a nagyméretű HANA-példány veremben. Az IP-címet is van szükség, ha szerkeszti a [storage konfigurációs fájljának biztonsági mentések pillanatkép](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots). 

Görgessen le a tulajdonság panelen, ahogy további adatok, például a nagyméretű HANA-példány egység egy egyedi erőforrás-azonosító, vagy az előfizetés-azonosító, amely hozzá lett rendelve a központi telepítés kap.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Indítsa újra az Azure Portalon nagyméretű HANA-példány egység
A Linux operációs rendszer újraindítás kezdeményezése, különböző helyzetekben, ahol az operációs rendszer nem tudta befejezni egy újraindítás sikerült voltak. Annak érdekében, hogy a számítógép újraindítását kényszeríti, nyisson egy szolgáltatáskérést, hogy a nagyméretű HANA-példány egységek power újrainduljon-a Microsoft operations szükséges. A nagyméretű HANA-példány egységek power újraindítás funkcióit mostantól integrálva van az Azure Portalon. Akkor a nagyméretű HANA-példány egység áttekintése részében, láthatja a gomb felett az adatszakasz-újraindítás számára

![Indítsa újra a #1. lépés az Azure Portalon](./media/hana-li-portal/portal-restart-first-step.png)

Az Újraindítás gombot is lenyomásával, mivel megkérdezi, hogy valóban szeretné indítsa újra a egység. "Yes" gombjának megnyomásával erősítse meg, ahogy a egység újra fog indulni.

> [!NOTE]
> Az újraindítás folyamatban egy kis idő, ahol a egység állapota megváltozik, a tapasztalható **indítása** állapotát átkerülni **elindítva**. Folyamatban van a szerinti **elindítva** azt jelenti, hogy az operációs rendszer indítása folyamatban van, illetve, hogy az operációs rendszer elindult teljesen. Ennek eredményeképpen az egység egy újraindítás után nem várt azonnal jelentkezzen be az egységet, amint a állapotot vált, amennyiben az **elindítva**.


## <a name="open-a-support-request-for-hana-large-instances"></a>Nyisson egy támogatási kérelmet a nagyméretű HANA-példányok
Ki a nagyméretű HANA-példány egység az Azure portál megjelenítését kifejezetten a HANA nagyméretű példány egység is hozhat létre támogatási kérelmeket. A hivatkozás végrehajtásával lehetősége van **új támogatási kérelem** 

![kezdeményezheti a szolgáltatási kérelem 1. lépés # az Azure Portalon](./media/hana-li-portal/portal-initiate-support-request.png)

Az SAP HANA nagyméretű példányok a következő képernyőn szereplő szolgáltatás eléréséhez, előfordulhat, hogy ki kell választania "az összes szolgáltatás" alább látható módon

![Válassza ki az összes szolgáltatás az Azure Portalon](./media/hana-li-portal/portal-create-service-request.png)

A szolgáltatások listájában keresse meg a szolgáltatás **SAP HANA nagyméretű példányok**. Amikor a szolgáltatást, meghatározott probléma típusú látható módon választhatja ki:


![Válassza ki a problémát az osztály az Azure Portalon](./media/hana-li-portal/portal-select-problem-class.png)

Minden, a másik probléma típusú a probléma altípus ki kell választania a probléma további jellemezhető egy kijelölt áron érhető el. Miután kiválasztotta a altípusa, most nevet adhat a tulajdonos. Miután végzett a tanúsítványkiválasztási folyamat, továbbléphet a létrehozásakor a következő lépéssel. Az a **megoldások** szakaszban Ön hegyes dokumentáció HANA nagyméretű példányok körül, amely előfordulhat, hogy adjon mutató a probléma megoldáshoz. Ha a dokumentáció, javasolt a probléma nem talál megoldást, Ugrás a következő lépéssel. A következő lépésben fogja kérni a probléma van-e a virtuális gépek vagy a nagyméretű HANA-példány egységekkel. Ez segít a támogatási kérelmet a megfelelő szakemberei közvetlen. 

![Az Azure Portalon támogatási eset részletei](./media/hana-li-portal/portal-support-request-details.png)

Kérdéseire, és további részleteket a megadott, nyissa meg a következő lépés annak érdekében, hogy tekintse át a támogatási kérés és a Küldés azt.

## <a name="next-steps"></a>További lépések

- [Azure-beli SAP HANA (nagyméretű példányok) figyelése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [HANA-oldali monitorozás és hibaelhárítás](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)

