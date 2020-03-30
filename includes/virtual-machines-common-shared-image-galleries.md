---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh
ms.custom: include file
ms.openlocfilehash: a477114bda7d138a6860d21f2fad75e27d968833
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80116990"
---
A Megosztott képtár egy olyan szolgáltatás, amely segít a felügyelt lemezképek köré szerveződés tészete és szervezeti felépítése. A megosztott képgalériák a következőket biztosítják:

- A képek felügyelt globális replikációja.
- A képek verziószámozása és csoportosítása a könnyebb kezelés érdekében.
- Magas rendelkezésre állású lemezképek zónaredundáns tárolás (ZRS) fiókokkal a rendelkezésre állási zónákat támogató régiókban. A ZRS jobb rugalmasságot kínál a zónahibákkal szemben.
- Az rbac használatával megosztja az előfizetések között, sőt az Active Directory (AD) bérlői között is.
- A központi telepítések méretezése az egyes régiókban rendszerképreplikákkal.

A megosztott képtár használatával megoszthatja a képeket a szervezeten belüli különböző felhasználókkal, egyszerű szolgáltatásokkal vagy AD-csoportokkal. A megosztott lemezképek több régióba is replikálhatók a központi telepítések gyorsabb méretezése érdekében.

A felügyelt lemezkép egy teljes virtuális gép (beleértve a csatlakoztatott adatlemezeket is) vagy csak az operációs rendszer lemezének másolata, attól függően, hogy hogyan hozza létre a lemezképet. Amikor virtuális gép a lemezképből, a virtuális merevlemezek egy példányát a rendszera rendszer az új virtuális gép lemezeinek létrehozásához. A felügyelt lemezkép a tárolóban marad, és újra és újra használható új virtuális gépek létrehozásához.

Ha nagy számú felügyelt lemezképet kell karbantartania, és szeretné elérhetővé tenni azokat a vállalat egész területén, használhat egy megosztott képgalériát tárházként, amely megkönnyíti a képek megosztását. 

A Megosztott képtár szolgáltatás több erőforrástípussal rendelkezik:

| Erőforrás | Leírás|
|----------|------------|
| **Felügyelt kép** | Egyszerű kép, amely önmagában használható, vagy **képverzió** létrehozására használható egy képtárban. Felügyelt lemezképek [általános virtuális](#generalized-and-specialized-images) gépekből jönnek létre. A felügyelt lemezkép egy speciális típusú virtuális merevlemez, amely több virtuális gép létrehozásához használható, és most már használható megosztott lemezkép-verziók létrehozásához. |
| **Pillanatkép** | A virtuális merevlemez másolata, amely a **lemezkép-verzió**hozására használható. Pillanatképek készíthető egy [speciális](#generalized-and-specialized-images) virtuális gép (az egyik, hogy még nem általánosították), majd önmagában vagy adatlemezek pillanatképeivel, hozzon létre egy speciális lemezkép-verzió.
| **Képgaléria** | Az Azure Marketplace-hez hasonlóan a **képgaléria** is egy tárház a képek kezeléséhez és megosztásához, de ön szabályozza, hogy ki rendelkezik hozzáféréssel. |
| **Képdefiníció** | A képek egy galérián belül vannak definiálva, és a lemezképre és a szervezeten belüli használatra vonatkozó követelményekre vonatkozó információkat tartalmaznak. Olyan információkat is megadhat, mint például, hogy a lemezkép általános vagy specializált-e, az operációs rendszer, a minimális és maximális memóriakövetelmények, valamint a kiadási megjegyzések. Ez egy fajta kép definíciója. |
| **Kép verziószáma** | A **lemezkép-verzió** az, amit a virtuális gép létrehozásához használ katalógus használatakor. A környezetnek szüksége van egy lemezkép több verziójára is. A felügyelt lemezképhez hasonlóan, amikor egy **lemezkép-verziót** használ a virtuális gép létrehozásához, a rendszerkép-verzió a virtuális gép új lemezeinek létrehozásához használatos. A képverziótöbbször is használható. |

<br>

![A kép több verziójának a galériában való megjelenítését bemutató ábra](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Képdefiníciók

A képdefiníciók a kép verzióinak logikai csoportosítását képezik. A képdefiníció információkat tartalmaz arról, hogy miért jött létre a kép, milyen operációs rendszerre készült, valamint a kép használatáról. A képdefiníció olyan, mint egy terv az adott kép létrehozásának minden részletéhez. Nem telepíti a virtuális gép egy lemezkép-definíció, de a definícióból létrehozott rendszerkép-verzió.

Az egyes képdefiníciókhoz három paraméter használható együtt : **Publisher,** **Offer** és **Termékváltozat**. Ezek egy adott képdefiníció megkeresésére szolgálnak. Lehetnek olyan képverziók, amelyek egy vagy két, de nem mind a három értéket.  Az alábbiakban például három képdefiníciót és azok értékeit lehet megfogalmaszsalni:

|Rendszerkép-definíció|Közzétevő|Ajánlat|SKU|
|---|---|---|---|
|myImage1|Contoso|Pénzügy|Háttér|
|myImage2|Contoso|Pénzügy|Előtér|
|myImage3|Tesztelés|Pénzügy|Előtér|

Mindhárom egyedi értékkészlettel rendelkezik. A formátum hasonló ahhoz, ahogyan jelenleg megadhatja a közzétevő, ajánlat és termékváltozat [az Azure Marketplace-rendszerképek](../articles/virtual-machines/windows/cli-ps-findimage.md) az Azure PowerShell-ben a Marketplace-lemezkép legújabb verziójának beszerezéséhez. Minden képdefiníciónak egyedi értékkészletet kell rendelkeznie.

Az alábbi paraméterek et a képdefinícióban állíthatja be, így könnyebben nyomon követheti az erőforrásokat:

* Operációs rendszer állapota – Az operációs rendszer állapotát [általánossá vagy speciálisra állíthatja.](#generalized-and-specialized-images)
* Operációs rendszer - lehet Windows vagy Linux.
* Leírás – a leírás használatával részletesebb információt adhat arról, hogy miért létezik a képdefiníció. Előfordulhat például, hogy rendelkezik egy lemezkép-definícióval az előtér-kiszolgálóhoz, amelyen az alkalmazás előre telepítve van.
* Eula – a képdefinícióra jellemző végfelhasználói licencszerződésre mutathat.
* Adatvédelmi nyilatkozat és kiadási megjegyzések – az Azure storage-ban tárolja a kibocsátási megjegyzéseket és az adatvédelmi nyilatkozatokat, és adjon meg egy URI-t a képdefiníció részeként való elérésükhöz.
* Életciklus vége dátuma – az életciklus végéhez kapcsolódó dátum csatolása a képdefinícióhoz, hogy az automatizálás segítségével törölheti a régi képdefiníciókat.
* Címke – címkéket adhat hozzá a képdefiníció létrehozásakor. A címkékről további információt az [Erőforrások rendszerezése címkék használata című témakörben talál.](../articles/azure-resource-manager/management/tag-resources.md)
* Minimális és maximális vCPU- és memóriajavaslatok – ha a lemezkép vCPU- és memóriajavaslatokkal rendelkezik, csatolhatja ezeket az információkat a képdefinícióhoz.
* Nem engedélyezett lemeztípusok – a virtuális gép tárolási igényeinek megadásával szolgálhat. Ha például a kép nem alkalmas szabványos HDD-lemezekhez, hozzáadhatja őket a leengedési listához.

## <a name="generalized-and-specialized-images"></a>Általános és speciális képek

A Megosztott képtár két operációsrendszer-állapotot támogat. Általában a képek megkövetelik, hogy a lemezkép létrehozásához használt virtuális gép általánossá vált a lemezkép létrehozása előtt. Általánosítás egy olyan folyamat, amely eltávolítja a gép és a felhasználó-specifikus információkat a virtuális gép. Windows esetén a Sysprep is használatos. Linux esetén [használhatja waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` vagy `-deprovision+user` paramétereket.

A speciális virtuális gépek nem mentek keresztül a gépspecifikus információk és fiókok eltávolítására irányuló folyamaton. Emellett a speciális rendszerképekről létrehozott virtuális `osProfile` gépekhez nincs társítva. Ez azt jelenti, hogy a speciális képeknek lesznek bizonyos korlátai.

- Fiókok, amelyek a virtuális gépbe való bejelentkezéshez használható, bármely virtuális gépen is használhatók, amely az adott virtuális gépből létrehozott speciális lemezkép használatával jön létre.
- A virtuális gépek **számítógépneve** lesz annak a virtuális gépnek, amelyből a rendszerkép származik. Az ütközések elkerülése érdekében meg kell változtatnia a számítógép nevét.
- Az, `osProfile` hogy néhány bizalmas információt a virtuális `secrets`gép a használatával. Ez problémákat okozhat a KeyVault, a WinRM `osProfile`és más funkciók használatával, amelyek a alkalmazásban használatosak. `secrets` Bizonyos esetekben a felügyelt szolgáltatásidentitások (MSI) segítségével megkerülheti ezeket a korlátozásokat.

> [!IMPORTANT]
> A speciális képek jelenleg nyilvános előzetes verzióban jelennek meg.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)
>
> **Ismert előnézeti korlátozások** Virtuális gépek csak a portál vagy api használatával speciális lemezképek ből hozhatók létre. A nem CLI vagy PowerShell támogatja az előzetes verziót.


## <a name="regional-support"></a>Regionális támogatás

A forrásrégiókat az alábbi táblázat sorolja fel. Minden nyilvános régió lehet célrégió, de az Australia Central 2 és az Australia Central 2 verzióra való replikáláshoz az előfizetés engedélyezési listájára van szükség. Az engedélyezési lista igényléséhez nyissa meg a következő t:https://azure.microsoft.com/global-infrastructure/australia/contact/


| Forrásrégiók        |                   |                    |                    |
| --------------------- | ----------------- | ------------------ | ------------------ |
| Ausztrália középső régiója     | Kelet-Kína        | Dél-India        | Nyugat-Európa        |
| Ausztrália 2. középső régiója   | Kína Keleti 2      | Délkelet-Ázsia     | Az Egyesült Királyság déli régiója           |
| Kelet-Ausztrália        | Észak-Kína       | Kelet-Japán         | Az Egyesült Királyság nyugati régiója            |
| Délkelet-Ausztrália   | Kína Észak 2     | Nyugat-Japán         | US DoD – Középső régió     |
| Dél-Brazília          | Kelet-Ázsia         | Dél-Korea középső régiója      | US DoD – Kelet        |
| Közép-Kanada        | USA keleti régiója           | Dél-Korea déli régiója        | USA-beli államigazgatás – Arizona     |
| Kelet-Kanada           | USA 2. keleti régiója         | USA északi középső régiója   | USA-beli államigazgatás – Texas       |
| Közép-India         | USA keleti része 2 EUAP    | Észak-Európa       | USA-beli államigazgatás – Virginia    |
| USA középső régiója            | Közép-Franciaország    | USA déli középső régiója   | Nyugat-India         |
| USA középső régiója       | Dél-Franciaország      | USA nyugati középső régiója    | USA nyugati régiója            |
|                       |                   |                    | USA nyugati régiója, 2.          |



## <a name="limits"></a>Korlátok 

Előfizetésenként vannak korlátok a megosztott képgalériák használatával történő erőforrások üzembe helyezésére:
- 100 megosztott képgaléria előfizetésenként, régiónként
- 1000 képdefiníció előfizetésenként, régiónként
- 10 000 képverzió előfizetésenként, régiónként
- A lemezhez csatlakoztatott lemezeknek legfeljebb 1 TB méretűnek kell lenniük

További információ: [Erőforrás-használat ellenőrzése korlátok között](https://docs.microsoft.com/azure/networking/check-usage-against-limits) példákat az aktuális használat ellenőrzésére.
 
## <a name="scaling"></a>Méretezés
A megosztott képtár lehetővé teszi, hogy adja meg a replikák számát, amelyet az Azure-nak meg kell őriznie a képek között. Ez segít a több virtuális gép központi telepítési forgatókönyvek, mint a virtuális gép központi telepítések különböző replikákra átterjedhet, csökkentve annak az esélyét, hogy a példány létrehozása feldolgozásegyetlen replika túlterhelése miatt szabályozott.

A megosztott képtár, most már üzembe helyezheti akár egy 1000 virtuális gép példányok egy virtuális gép méretezési csoport (akár 600 felügyelt lemezképek). A lemezképek jobb üzembe helyezési teljesítményt, megbízhatóságot és konzisztenciát biztosítanak. Az egyes célrégiókban a régió méretezési igényei alapján különböző replikaszámot állíthat be. Mivel minden replika a lemezkép mélymásolata, ez segít a központi telepítések lineáris méretezésében minden további replika. Bár megértjük, hogy nincs két kép vagy régió azonos, az alábbiakban ismertetjük a replikák használatával kapcsolatos általános irányelveket egy régióban:

- A nem virtuális gép méretezési csoport (VMSS) központi telepítések – minden 20 virtuális gép, amely egyidejűleg létrehozott, azt javasoljuk, hogy egy replika. Ha például 120 virtuális gépet hoz létre egyidejűleg ugyanazon a régióban ugyanazt a lemezképet használva, javasoljuk, hogy legalább 6 replikáját tartsa meg a lemezkép. 
- A Virtuálisgép-méretezési csoport (VMSS) központi telepítések – Minden skálázáskészlet üzembe helyezését legfeljebb 600 példányok, azt javasoljuk, hogy legalább egy replika. Ha például egyidejűleg 5 méretezési csoport létrehozása, amelyek mindegyike 600 virtuálisgép-példányt használ ugyanazt a lemezképet egyetlen régióban, azt javasoljuk, hogy legalább 5 replikák a lemezkép. 

Mindig azt javasoljuk, hogy a replikák száma túlnagyodása tényezők, például a képméret, a tartalom és az operációs rendszer típusa miatt.

![Képek méretezését bemutató ábra](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>A képek magas rendelkezésre állásának lehetővé tévő megjelenítése

[Az Azure Zone Redundáns tárolás (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) rugalmasságot biztosít a régióban a rendelkezésre állási zóna hibájával szemben. A megosztott képtár általános elérhetőségével a zrs-fiókokban tárolhatja a képeket a rendelkezésre állási zónákkal rendelkező régiókban. 

Az egyes célrégiókhoz is kiválaszthatja a fiók típusát. Az alapértelmezett tárfiók típus Standard_LRS, de kiválaszthatja, Standard_ZRS a régiók rendelkezésre állási zónák. Ellenőrizze a ZRS regionális elérhetőségét [itt.](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)

![A ZRS-t ábrázoló ábra](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>Replikáció
A megosztott képtár lehetővé teszi a képek automatikus replikálását más Azure-régiókba is. Minden megosztott képverzió replikálható különböző régiókba attól függően, hogy mi értelme van a szervezet számára. Egy példa például a legújabb lemezkép replikálása több régióban, míg az összes régebbi verzió csak 1 régióban érhető el. Ez segíthet a megosztott lemezkép-verziók tárolási költségeinek megtakarításában. 

A megosztott lemezkép-verzió replikálása a létrehozási idő után frissíthető. A különböző régiókba való replikáláshoz szükséges idő a másolt adatok mennyiségétől és a verzió replikált területeinek számától függ. Ez bizonyos esetekben néhány órát is igénybe vehet. A replikáció ideje alatt régiónként megtekintheti a replikáció állapotát. Miután a rendszerkép-replikáció befejeződött egy régióban, majd üzembe helyezhet egy virtuális gép vagy méretezési csoport használatával, hogy a lemezkép-verzió a régióban.

![Képek replikálását bemutató ábra](./media/shared-image-galleries/replication.png)

## <a name="access"></a>Hozzáférés

Mivel a megosztott képtár, a képdefiníció és a képverzió mind erőforrások, a beépített natív Azure RBAC-vezérlőkkel oszthatók meg. Az RBAC használatával megoszthatja ezeket az erőforrásokat más felhasználókkal, egyszerű szolgáltatásokkal és csoportokkal. Akár azt is megoszthatja a hozzáférést az egyének kívül a bérlő, hogy hoztak létre belül. Miután a felhasználó hozzáfér a megosztott lemezkép verziójához, üzembe helyezhet egy virtuális gépet vagy egy virtuálisgép-méretezési készletet.  Itt van a megosztási mátrix, amely segít megérteni, hogy mi a felhasználó kap hozzáférést:

| Felhasználóval megosztva     | Megosztott rendszerkép-katalógus | Rendszerkép-definíció | Rendszerképverzió |
|----------------------|----------------------|--------------|----------------------|
| Megosztott rendszerkép-katalógus | Igen                  | Igen          | Igen                  |
| Rendszerkép-definíció     | Nem                   | Igen          | Igen                  |

Javasoljuk, hogy a legjobb élmény érdekében ossza meg a Galéria szintjén. Nem javasoljuk az egyes képverziók megosztását. Az RBAC-ról további információt az [Azure-erőforrásokhoz való hozzáférés kezelése az RBAC használatával című](../articles/role-based-access-control/role-assignments-portal.md)témakörben talál.

A képek is megoszthatók, a skála, még a bérlők között egy több-bérlős alkalmazás regisztrációja. A lemezképek bérlők közötti megosztásáról a [Katalógus virtuálisgép-lemezképeinek megosztása az Azure-bérlők között című témakörben](../articles/virtual-machines/linux/share-images-across-tenants.md)talál további információt.

## <a name="billing"></a>Számlázás
A Megosztott képtár szolgáltatás használatáért nem számítunk fel díjat. A következő erőforrásokért kell fizetnie:
- A megosztott lemezkép-verziók tárolásának tárolási költségei. A költség a lemezkép-verzió replikáinak számától és a verzió replikált területeinek számától függ. Ha például 2 lemezképpel rendelkezik, és mindkettő 3 régióra replikálódik, akkor a méretük alapján 6 felügyelt lemezért kell fizetnie. További információt a [Felügyelt lemezek díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/)című témakörben talál.
- Hálózati kimenő forgalom díjak replikációja az első képverzió a forrásrégióból a replikált régiók. A további replikák kezelése a régión belül, így nincsenek további költségek. 

## <a name="updating-resources"></a>Erőforrások frissítése

A létrehozás után módosíthatja a képgaléria erőforrásait. Ezek a következőkre korlátozódnak:
 
Megosztott képgaléria:
- Leírás

Képdefiníció:
- Ajánlott vCPU-k
- Ajánlott memória
- Leírás
- Az életciklus vége dátuma

Kép verziószáma:
- Regionális replikaszám
- Célrégiók
- Kizárás a legújabb
- Az életciklus vége dátuma

## <a name="sdk-support"></a>SDK-támogatás

A következő SDK-k támogatják a megosztott képgalériák létrehozását:

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/@azure/arm-compute)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Ugrás](https://docs.microsoft.com/azure/go/)

## <a name="templates"></a>Sablonok

A megosztott képtár erőforrássablonok használatával is létrehozható. Számos Azure-gyorsindítási sablon áll rendelkezésre: 

- [Megosztott képtár létrehozása](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Képdefiníció létrehozása megosztott képtárban](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Képverzió létrehozása megosztott képtárban](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Virtuális gép létrehozása a rendszerkép-verzióból](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Gyakori kérdések 

* [Hogyan sorolhatom fel az összes megosztott képtár-erőforrást az előfizetések között?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [Áthelyezhetem a meglévő képemet a megosztott képtárba?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [Létrehozhatok lemezképet egy speciális lemezről?](#can-i-create-an-image-version-from-a-specialized-disk)
* [Áthelyezhetem a Megosztott képtár erőforrást egy másik előfizetésbe a létrehozása után?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [Replikálhatom a lemezkép-verzióimat olyan felhők között, mint az Azure China 21Vianet vagy az Azure Germany vagy az Azure Government Cloud?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [Replikálhatom a lemezkép-verzióimat az előfizetések között?](#can-i-replicate-my-image-versions-across-subscriptions)
* [Megoszthatom a lemezkép-verziókat az Azure AD-bérlők között?](#can-i-share-image-versions-across-azure-ad-tenants)
* [Mennyi ideig tart a lemezkép-verziók replikálása a célrégiókközött?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [Mi a különbség a forrásrégió és a célrégió között?](#what-is-the-difference-between-source-region-and-target-region)
* [Hogyan adhatom meg a forrásterületet a lemezkép-verzió létrehozása közben?](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [Hogyan adhatom meg az egyes régiókban létrehozandó lemezverzió-replikák számát?](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [Létrehozhatom a megosztott képgalériát a képdefinícióés a képverzióhoz hasonló helyen?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [Milyen díjakkal kell megfizetni a megosztott képtár használatát?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [Milyen API-verziót használjak a megosztott képtár, valamint a képdefiníció és a képverzió létrehozásához?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [Milyen API-verziót kell használni a megosztott virtuális gép vagy a virtuálisgép-méretezési készlet létrehozásához a lemezkép-verzión?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>Hogyan sorolhatom fel az összes megosztott képtár-erőforrást az előfizetések között?

Az azure-portálon elérhető előfizetések összes megosztott képtár-erőforrásának listázásához kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
1. Nyissa meg a **Minden erőforrás t.**
1. Válassza ki az összes olyan előfizetést, amely alatt az összes erőforrást fel szeretné sorolni.
1. Keresse meg a Magángyűjtemény típusú **erőforrásokat.**
 
   A képdefiníciók és a képverziók megtekintéséhez válassza a **Rejtett típusok megjelenítése**lehetőséget is.
 
   Az összes megosztott képtár-erőforrás listázásához az előfizetések között, amelyekhez engedéllyel rendelkezik, használja a következő parancsot az Azure CLI-ben:

   ```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>Áthelyezhetem a meglévő képemet a megosztott képtárba?
 
Igen. A képek típusától függően 3 forgatókönyv létezik.

 1. forgatókönyv: Ha a SIG-vel azonos előfizetésben van felügyelt lemezkép, akkor létrehozhat egy lemezkép-definíciót és lemezkép-verziót belőle.

 2. forgatókönyv: Ha a SIG-vel azonos előfizetésben van egy nem felügyelt lemezkép, létrehozhat belőle egy felügyelt lemezképet, majd létrehozhat egy lemezkép- és lemezkép-verziót belőle. 

 3. forgatókönyv: Ha van egy virtuális merevlemez a helyi fájlrendszerben, majd fel kell töltenie a virtuális merevlemezt egy felügyelt lemezképbe, majd létrehozhat egy lemezkép-definíciót és a lemezkép-verziót.

- Ha a virtuális merevlemez Windows virtuális gépből áll, olvassa el [a VHD feltöltése](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)című témakört.
- Ha a virtuális merevlemez Linux virtuális géphez való, olvassa el [a Virtuális merevlemez feltöltése című témakört.](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>Létrehozhatok lemezképet egy speciális lemezről?

Igen, speciális lemezek támogatása, mivel a képek előzetes verzióban jelennek meg. Csak egy speciális rendszerképből hozhat létre virtuális gépet a portál ([Windows](../articles/virtual-machines/linux/shared-images-portal.md) vagy [Linux](../articles/virtual-machines/linux/shared-images-portal.md)) és API használatával. Nincs PowerShell-támogatás az előzetes verzióhoz.

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>Áthelyezhetem a Megosztott képtár erőforrást egy másik előfizetésbe a létrehozása után?

Nem, a megosztott képgaléria erőforrás nem helyezhető át egy másik előfizetésbe. Azonban képes lesz replikálni a képverziókat a katalógusban más régiókban szükség szerint.

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Replikálhatom a lemezkép-verzióimat olyan felhők között, mint az Azure China 21Vianet vagy az Azure Germany vagy az Azure Government Cloud?

Nem, a lemezkép-verziók nem replikálhatók felhők között.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>Replikálhatom a lemezkép-verzióimat az előfizetések között?

Nem, replikálhatja a lemezkép-verziók régiók között egy előfizetésben, és használja azt más előfizetések rBAC-on keresztül.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Megoszthatom a lemezkép-verziókat az Azure AD-bérlők között? 

Igen, az RBAC segítségével megoszthatja a bérlők közötti egyének. A [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) vagy a CLI használatával azonban megtekintheti a "Galériaképek megosztása az Azure-bérlők között" című [témakört.](../articles/virtual-machines/linux/share-images-across-tenants.md)

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>Mennyi ideig tart a lemezkép-verziók replikálása a célrégiókközött?

A lemezképverzió replikációs ideje teljes mértékben a lemezkép méretétől és a replikálandó területek számától függ. Ajánlott eljárásként azonban ajánlott a kép kicsiben tartása, valamint a forrás- és célterületek bezárása a legjobb eredmény érdekében. A replikáció állapotát a -ReplicationStatus jelző vel ellenőrizheti.

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>Mi a különbség a forrásrégió és a célrégió között?

A forrásrégió az a régió, amelyben a rendszerkép verziója létrejön, és a célterületek azok a régiók, ahol a rendszerkép-verzió egy példányát tárolják. Minden lemezkép-verzióhoz csak egy forrásrégió lehet. Győződjön meg arról is, hogy a rendszerkép-verzió létrehozásakor adja át a forrásrégió helyét a célterületek egyikeként.

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>Hogyan adhatom meg a forrásterületet a lemezkép-verzió létrehozása közben?

Lemezkép-verzió létrehozása kor használhatja a **--location** tag cli és a **-location** tag a PowerShell ben a forrásrégió megadásához. Győződjön meg arról, hogy a lemezkép-verzió létrehozásához alaplemezképként használt felügyelt lemezkép ugyanazon a helyen található, mint az a hely, ahol a lemezkép-verziót létre kívánja hozni. Győződjön meg arról is, hogy a rendszerkép-verzió létrehozásakor adja át a forrásrégió helyét a célterületek egyikeként.  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>Hogyan adhatom meg az egyes régiókban létrehozandó lemezverzió-replikák számát?

Az egyes régiókban létrehozandó lemezverzió-replikák számát kétféleképpen adhatja meg:
 
1. A régiós replikaszám, amely régiónként megadja a létrehozni kívánt kópiák számát. 
2. A gyakori replikaszám, amely régiónként alapértelmezett száma abban az esetben, ha a regionális replikaszám nincs megadva. 

A regionális replikaszám megadásához adja át a helyet az adott régióban létrehozni kívánt replikák számával együtt: "Dél-közép USA=2". 

Ha a regionális replikaszám nincs megadva az egyes helyeken, akkor a replikák alapértelmezett száma lesz a megadott közös replikaszám. 

A CLI-ben a közös replikaszám megadásához használja `az sig image-version create` a **parancs --replikaszámláló** argumentumát.

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>Létrehozhatom a megosztott képgalériát a képdefinícióés a képverzióhoz hasonló helyen?

Igen, ez lehetséges. Ajánlott eljárásként azonban azt javasoljuk, hogy az erőforráscsoportot, a megosztott képgalériát, a képdefiníciót és a képverziót ugyanazon a helyen tartsa.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>Milyen díjakkal kell megfizetni a megosztott képtár használatát?

A megosztott képtár-szolgáltatás használatáért nem számítunk fel díjat, kivéve a lemezképverziók és a hálózati kimenő forgalom tárolási díjait a lemezkép-verziók forrásrégióból a célrégiókba történő replikálásáért.

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Milyen API-verziót használjak a megosztott képtár, valamint a képdefiníció és a képverzió létrehozásához?

A megosztott képgalériákkal, képdefiníciókkal és képverziókkal való munkához javasoljuk, hogy a 2018-06-01-es API-verziót használja. A zónaredundáns tárolás (ZRS) 2019-03-01-es vagy újabb verziót igényel.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>Milyen API-verziót kell használni a megosztott virtuális gép vagy a virtuálisgép-méretezési készlet létrehozásához a lemezkép-verzión?

Virtuális gép és a virtuális gép méretezése készlet egy lemezkép-verzió használatával, javasoljuk, hogy az API 2018-04-01-es vagy újabb verziót használja.
