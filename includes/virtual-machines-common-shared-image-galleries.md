---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 0fe1de9bb674c66d1b665de25ee579bc86e42c75
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192364"
---
Megosztott lemezkép-katalógus szolgáltatása segít kialakítani a struktúra és a szervezet a felügyelt VM-rendszerképeit körül. Megosztott kép katalógusok adja meg:

- Képek felügyelt globális replikálását.
- Verziókezelés és a könnyebb kezelhetőség képek csoportosítása.
- Győződjön meg arról, a képek a Zónaredundáns Társzolgáltatási (ZRS) fiókokkal magas rendelkezésre állású rendelkezésre állási zónákat támogató régiókban. A ZRS nagyobb rugalmasság a zónaszintű meghibásodásokkal szemben kínál.
- Előfizetések között, és még a bérlők számára az RBAC használatával között megosztást.

Egy megosztott lemezkép-katalógus használatával megoszthatja a különböző felhasználók, az egyszerű szolgáltatások vagy AD-csoportokat a képeket a szervezeten belül. Megosztott lemezképeket több régióban, a gyorsabb skálázás az üzembe helyezést lehet replikálni.

Egy felügyelt rendszerképet egy példányát, vagy egy teljes virtuális Gépet (beleértve a bármely más csatolt lemez) vagy csak az operációsrendszer-lemez, attól függően, hogyan hoz létre a lemezképet. A lemezképből létrehozott virtuális gép, ha a virtuális merevlemezeket, a lemezkép egy példányát a lemezek létrehozása az új virtuális gép szolgálnak. A felügyelt rendszerkép tárolóban marad, és hozhat létre új virtuális gépeket és újra használható.

Ha felügyelt rendszerkép található, biztosítani kell, és szeretné elérhetővé teheti azokat a vállalat nagy számú, egy adattár, amely megkönnyíti a rendszerképek megosztani egy megosztott lemezkép-katalógusában is használhatja. 

A megosztott lemezkép-katalógusában a szolgáltatás több erőforrástípusok rendelkezik:

| Resource | Leírás|
|----------|------------|
| **Felügyelt rendszerkép** | Egy alapszintű rendszerképet, amely önmagában, vagy létrehozásához használt egy **lemezkép verziója** egy rendszerkép-katalógusában. Felügyelt lemezképek általánosított virtuális gépek jönnek létre. Egy felügyelt rendszerképet egy speciális típusú virtuális Merevlemezt, amely használható, hogy több virtuális gép, és most már használható létrehozásához megosztott kép verzió. |
| **Lemezkép-katalógusában** | Az Azure piactéren, például egy **lemezkép-katalógusában** egy adattár a kezelése és megosztása a rendszerképeket, de Ön szabályozza, ki férhet. |
| **Rendszerkép definíciójában** | Képek ugyanazon a katalóguson belül határozza meg, és végezze a lemezkép és a szervezeten belül vonatkozó követelményekhez. Tartalmaznak információkat, például, hogy-e a kép a Windows vagy Linux-alapú, minimális és maximális memóriára vonatkozó követelményeknek, és a kibocsátási megjegyzéseket. Egy lemezkép definíciója. |
| **Lemezkép verziója** | Egy **lemezkép verziója** meg használni a virtuális gép létrehozása katalógus használata során. Kép különböző verzióinak rendelkezhet saját környezetéhez szükséges módon. Használata esetén, egy felügyelt rendszerképet, például egy **lemezkép verziója** hozhat létre virtuális Gépet, a rendszerkép verziószámát új lemezeket a virtuális gép létrehozásához használt. Lemezkép verziója többször is használható. |

<br>


![Hogyan lehet kép különböző verzióinak a katalógusban szereplő bemutató ábra](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Lemezkép-definíciók

Lemezkép-definíciókat kép verziói logikai jellegű csoportosítását. A rendszerkép definíciójában miért a rendszerkép létrejött kapcsolatos információkat, mely szolgáltatást, operációs rendszer és a lemezképpel kapcsolatos információkat tartalmazza. Egy rendszerkép definíciójában úgy működik, mint az összes részletes adat alkalmazásszabályok létrehozása egy adott rendszerkép egy tervet. Egy rendszerkép definíciójában, de a rendszerkép verziószámát a definíció alapján létrehozott virtuális gép nem kell telepítenie.


Minden rendszerkép definíciójában három kombinációja – a használt paraméterek **közzétevő**, **ajánlat** és **Termékváltozat**. Ezek használhatók a keresés egy adott rendszerkép definíciójában. Lemezkép verziója, amelyek egy vagy két, de nem minden három érték lehet.  Ha például az alábbiakban három rendszerkép-definíciók és azok értékeit:

|Rendszerkép-definíció|Közzétevő|Ajánlat|SKU|
|---|---|---|---|
|myImage1|Contoso|Pénzügy|Háttérszolgáltatás|
|myImage2|Contoso|Pénzügy|Előtér|
|myImage3|Tesztelés|Pénzügy|Előtér|

Az alábbi három egyedi értékek amelyekre. A formátum hasonlít a hogyan jelenleg megadhat közzétevő, ajánlat és a Termékváltozat [Azure Marketplace-rendszerképek](../articles/virtual-machines/windows/cli-ps-findimage.md) az Azure PowerShell, a Piactéri lemezképet legújabb verziójának beszerzéséhez. Minden rendszerkép definíciójában rendelkeznie kell egy egyedi készletét ezeket az értékeket.

Más paramétereket, amelyek is beállíthat a rendszerkép definíciójában, hogy könnyebben tudja követni az erőforrások a következők:

* Operációs rendszer állapot – az operációs rendszer állapotáról és megadható általánosítva vagy specializált, de csak általánosítva jelenleg támogatott. Képek kell létrehozni, amely a Windows Sysprep használatával általánosított virtuális gépeket vagy `waagent -deprovision` Linux rendszeren.
* Operációs rendszer – lehet Windows vagy Linux.
* Leírás – miért a rendszerkép definíciójában létezik-e további részletes információkat biztosíthat leírás. Egy rendszerkép definíciójában Előfordulhat például, az előtér-kiszolgáló, amely rendelkezik az alkalmazás előre telepítve van.
* Végfelhasználói licencszerződés - használható, hogy egy adott a rendszerkép definíciójában a végfelhasználói licencszerződés mutasson.
* Adatvédelmi nyilatkozat és a kibocsátási megjegyzések – kibocsátási megjegyzések és adatvédelmi nyilatkozatai az Azure storage tárolja, és adjon meg egy URI-t a rendszerkép definíciójában részeként eléréséhez szükséges.
* Teljes életciklusa dátum – egy teljes életciklusa dátum csatolása a rendszerkép definíciójában ahhoz, hogy törli a régi lemezképet definíciók automation használatával.
* Címke - adhat hozzá címkéket a rendszerkép definíciójában létrehozásakor. Címkékkel kapcsolatos további információkért lásd: [az erőforrások rendszerezése címkék használatával](../articles/azure-resource-manager/resource-group-using-tags.md)
* Minimális és maximális vCPU és javaslatok memória - vCPU, memória és javaslatokat, a lemezkép-e csatlakoztathat ezt az információt a rendszerkép definíciójában.
* Nem engedélyezett a lemeztípusok – megadhatja a szükséges tárhelyet a virtuális gép kapcsolatos információkat. Például ha a kép nem alkalmas HDD a standard szintű lemezek, hozzá őket a letiltás lista.


## <a name="regional-support"></a>Regionális támogatás

Forrásrégiók az alábbi táblázatban láthatók. Az összes nyilvános régióban is lehetnek célrégiók, de Ausztrália középső régiója és Ausztrália 2. középső régiója replikálni kell rendelkeznie az előfizetésben szerepel az engedélyezési listán. Az engedélyezési kérést, nyissa meg: https://www.microsoft.com/en-au/central-regions-eligibility/


| Forrás régiók |
|---------------------|-----------------|------------------|-----------------|
| Ausztrália középső régiója   | USA középső régiója – EUAP | Korea középső régiója    | Egyesült Királyság 2. déli régiója      |
| Ausztrália 2. középső régiója | Kelet-Ázsia       | Korea déli régiója      | Az Egyesült Királyság nyugati régiója         |
| Kelet-Ausztrália      | USA keleti régiója         | USA északi középső régiója | USA nyugati középső régiója |
| Délkelet-Ausztrália | USA 2. keleti régiója       | Észak-Európa     | Nyugat-Európa     |
| Dél-Brazília        | USA 2. keleti régiója – EUAP  | USA déli középső régiója | Nyugat-India      |
| Közép-Kanada      | Közép-Franciaország  | Dél-India      | USA nyugati régiója         |
| Kelet-Kanada         | Dél-Franciaország    | Délkelet-Ázsia   | USA nyugati régiója         |
| Közép-India       | Kelet-Japán      | Egyesült Királyság északi régiója         | USA nyugati régiója, 2.       |
| USA középső régiója          | Nyugat-Japán      | Az Egyesült Királyság déli régiója         |                 |



## <a name="limits"></a>Limits 

Nincsenek korlátai, előfizetésenként, megosztott kép katalógusok-erőforrások üzembe helyezéséhez:
- megosztott kép 100 katalógusok esetén előfizetésenként és régiónként
- 1000 lemezkép-definíciókat, előfizetésenként és régiónként
- 10 000 lemezkép-verzió, előfizetésenként és régiónként

További információkért lásd: [ellenőrizze az erőforrás-használati korlátozások](https://docs.microsoft.com/azure/networking/check-usage-against-limits) hogyan ellenőrizheti az aktuális használati példák.
 

## <a name="scaling"></a>Méretezés
Megosztott lemezkép-katalógus lehetővé teszi, hogy meg szeretné tartani a rendszerképek Azure replikák száma. Ez segít több virtuális gépre kiterjedő központi telepítési forgatókönyvei szerint is oszlanak meg a virtuális gépek üzembe helyezése különböző replikába csökkenti az esélyét, hogy a példány létrehozása feldolgozása egyetlen replika túlterhelés miatt szabályozás alatt áll.

![Hogyan méretezhetők a képek bemutató ábra](./media/shared-image-galleries/scaling.png)


## <a name="replication"></a>Replikáció
Megosztott lemezkép-katalógus lehetővé teszi a képek automatikusan replikálja más Azure-régiókban. Minden megosztott lemezkép verziója függően mi értelme a szervezet különböző régiókban lehet replikálni. Egy példa arra, hogy a legújabb lemezkép mindig replikálása több régióban, amíg az összes korábbi verziói csak érhető el 1 régió van. Ez segít mentse a megosztott lemezkép-verziók a tárolási költségekre. 

A rendszer replikálja egy megosztott lemezkép verziója régiók létrehozásának időpontja után lehet frissíteni. A különböző régióban való replikálásához szükséges idő attól függ, másolását adatok mennyisége és a verzió a rendszer replikálja régiók száma. Ez eltarthat néhány óráig bizonyos esetekben. A replikáció történik, amíg a replikáció állapotát megtekintheti a régiónként. A kép replikáció befejezése után egy régióban, telepítheti a virtuális gép vagy a méretezési csoportot, hogy a lemezkép-verziót használó a régióban.

![Hogyan replikálhat lemezképek bemutató ábra](./media/shared-image-galleries/replication.png)


## <a name="access"></a>Hozzáférés

Mivel a megosztott lemezkép-katalógusában, a megosztott kép és a megosztott lemezkép verziója összes erőforrást, azok megoszthatók a beépített natív Azure RBAC-vezérlők használatával. Az RBAC használatával megoszthatja ezeket az erőforrásokat más felhasználók, az egyszerű szolgáltatások és csoportokat. A hozzáférést a bérlőn belül létrehozták őket kívüli személyek is megoszthatja. Miután egy felhasználó hozzáfér a megosztott lemezkép verziója, azok telepíthet egy virtuális Gépet, vagy egy virtuálisgép-méretezési csoportban.  A megosztási mátrix, amely segít megérteni, hogy mi a felhasználó hozzáférést kap a következő:

| Felhasználóval megosztott     | Megosztott rendszerkép-katalógus | Megosztott kép | Megosztott lemezkép verziója |
|----------------------|----------------------|--------------|----------------------|
| Megosztott rendszerkép-katalógus | Igen                  | Igen          | Igen                  |
| Megosztott kép         | Nem                   | Igen          | Igen                  |
| Megosztott lemezkép verziója | Nem                   | Nem           | Igen                  |

Azt javasoljuk, hogy a gyűjtemény szintjén, a legjobb élmény megosztása. RBAC kapcsolatos további információkért lásd: [RBAC használatával Azure-erőforrásokhoz való hozzáférés kezelése](../articles/role-based-access-control/role-assignments-portal.md).

Képek is megoszthatók, ipari méretekben használatával egy több-bérlős alkalmazás regisztrációját a bérlők között. Bérlők között képek megosztásával kapcsolatos további információkért lásd: [katalógus Virtuálisgép-rendszerképek megosztása Azure bérlők](../articles/virtual-machines/linux/share-images-across-tenants.md).

## <a name="billing"></a>Számlázás
Nem jár extra megosztott Képkatalógus szolgáltatásunkat. Meg kell fizetni az alábbi forrásanyagokat:
- Tárolási költségek, a megosztott kép verziók tárolásához. Költsége attól függ, a verzió a rendszer replikálja régiók számának és a rendszerkép verziószámát replikáin száma. Például ha mindkét replikálja a rendszer 3 régió 2 képek is vannak, majd, módosítani kell 6 felügyelt lemezek mérete alapján. További információkért lásd: [Managed Disks díjszabását ismertető](https://azure.microsoft.com/pricing/details/managed-disks/).
- A hálózati kimenő forgalom költségeit, az első rendszerkép verziója a forrásrégióban a replikált régiókban való replikálásra. További replikák kezeli a régión belül, így nem vonatkozik külön díj. 

## <a name="updating-resources"></a>Erőforrások frissítése

Létrehozása után módosíthatja bizonyos, a lemezkép galéria-erőforrásai. Ezek korlátozva:
 
Megosztott képgyűjtemény:
- Leírás

kép definíciója:
- Ajánlott vcpu-k
- Ajánlott memóriaméret
- Leírás
- Élettartam dátum vége

Lemezkép verziója:
- Regionális replikáinak száma
- Célrégiók
- Legújabb kizárása
- Élettartam dátum vége


## <a name="sdk-support"></a>SDK-támogatás

A következő SDK-k támogatják a megosztott kép katalógusok létrehozása:

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/azure-arm-compute/?view=azure-node-latest)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/go/azure/)

## <a name="templates"></a>Sablonok

Lemezkép-katalógusában a megosztott erőforrás-sablonok használatával is létrehozhat. Nincsenek elérhető számos Azure gyorsindítási sablonok: 

- [Hozzon létre egy megosztott lemezkép-katalógusában](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Kép definíció létrehozása egy megosztott rendszerkép-katalógusában](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Hozzon létre egy lemezkép verziója egy megosztott rendszerkép-katalógusában](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Lemezkép verziója egy virtuális gép létrehozása](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Gyakori kérdések 

**K.** Hogyan lehet az megosztott lemezkép-katalógusában-erőforrások listájának előfizetések között? 
 
 A. Annak érdekében, hogy a lista összes rendszerkép-katalógusában a megosztott erőforrást, hogy rendelkezik-e a hozzáférést az Azure Portal előfizetések között, kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
1. Lépjen a **összes erőforrás**.
1. Válassza ki az összes olyan előfizetést, amelyben szeretné minden erőforrás listában.
1. Keresse meg az típusú erőforrások **privát katalógust**.
 
   A lemezkép-definíciók és a lemezkép-verziók megtekintéséhez is választhat **rejtett típusok megjelenítése**.
 
   Lemezkép-katalógusában a megosztott erőforrások között, amely rendelkezik engedélyekkel az előfizetések listázásához használja a következő parancsot az Azure CLI-ben:

   ```bash
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```


**K.** Áthelyezhető-e a meglévő rendszerképet a megosztott lemezkép-katalógusban?
 
 A. Igen. A képek lehet típusú alapján 3 forgatókönyv közül választhat.

 1. forgatókönyv: Ha egy felügyelt rendszerképet, majd létrehozhat egy rendszerképet definíció- és lemezkép verziója azt.

 2. forgatókönyv: Ha általánosított nem felügyelt rendszerkép, létrehozhat egy felügyelt rendszerképet belőle, és majd hozzon létre belőle egy rendszerkép definíciójában és lemezkép verziója. 

 3. forgatókönyv: Ha egy virtuális Merevlemezt a helyi fájlrendszerbe is van, akkor szüksége töltse fel a VHD-t, hozzon létre egy felügyelt rendszerképet, majd hozhat létre és definíció és származó lemezkép verziója.
- Windows virtuális gépek a virtuális merevlemez esetén olvassa el [általános VHD feltöltése](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Ha a VHD-t egy Linux virtuális gép számára, lásd: [VHD feltöltése](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)


**K.** Létrehozhatok egy lemezkép verziója specializált lemezből?

 A. Nem, hogy jelenleg nem támogatják speciális lemezről képek. Ha egy specializált lemezzel rendelkezik, akkor [virtuális gép létrehozása virtuális merevlemezről](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal#create-a-vm-from-a-disk) a speciális lemez csatlakoztatásával egy új virtuális géphez. Ha már rendelkezik egy futó virtuális Gépre, kövesse az utasításokat, létrehozhat egy felügyelt rendszerképet, a kell a [Windows virtuális gép](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-custom-images) vagy [Linux rendszerű virtuális gép](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Ha már rendelkezik egy általános felügyelt rendszerkép, elindíthatja a folyamatot, hozzon létre egy megosztott kép leírását és a lemezkép-verzió.

 
**K.** Létrehozása után lehet váltani a lemezkép-katalógusában a megosztott erőforrás egy másik előfizetésbe?

 A. Egy másik előfizetésbe nem, nem helyezhetők át a megosztott kép galéria-erőforrásokat. Azonban akkor fogja tudni replikálni a katalógusban a rendszerkép verziója a más régiókban, szükség szerint.

**K.** Replikálhatok a lemezkép-verziók felhőkben – Azure China 21Vianet, az Azure Germany és az Azure Government Cloud? 

 A. Lemezkép-verzió nem, több felhő között nem lehet replikálni.

**K.** A kép verziók replikálhatok előfizetések között? 

 A. Nem, előfordulhat, hogy a lemezkép-verzió replikálja egy adott előfizetés régióban, és használhatja más előfizetések RBAC-n keresztül.

**K.** Az Azure AD-bérlőre kiterjedő is megoszthatok lemezkép verziója? 

 A. Igen, az RBAC használatával egyéni felhasználók számára történő megosztása bérlők. De bármilyen méretekben és megosztásához lásd: "megosztás katalógus lemezképeket az Azure-bérlőre kiterjedő" használatával [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) vagy [CLI](../articles/virtual-machines/linux/share-images-across-tenants.md).


**K.** Mennyi ideig tart a lemezkép-verzió replikálása a célként megadott régióban?

 A. A lemezkép verzió replikációs idő az teljes mértékben függ a lemezkép és régiók, replikálódnak számának méretét. Azonban ajánlott eljárásként javasoljuk, hogy kis tartania a rendszerképet, és a forrás és cél régiókat zárja be a legjobb eredmények elérése érdekében. Ellenőrizheti, hogy a replikáció állapota – jelző használatával a replikáció állapotát.


**K.** Mi a különbség a forrásrégióban és célrégió?

 A. Forrásrégió a régió, amelyben a lemezkép verziója jön létre, célrégiók pedig a régió, amelyben tárolni egy példányát a lemezkép-verzió. Minden rendszerkép verzióhoz csak rendelkezhet egy forrásrégióban. Bizonyosodjon meg róla, hogy át kell adnia a terület forráshelyen a célrégiók egyik egy lemezkép verziója létrehozásakor.  


**K.** Hogyan határozhatom meg a forrásrégióban a lemezkép-verzió létrehozása során?

 A. Egy lemezkép verziója létrehozásakor használhatja a **--hely** címke CLI-ben és a **-hely** címke a PowerShell használatával adja meg a forrásrégióban. Győződjön meg a felügyelt rendszerképet, amely az alaprendszerképet, a lemezkép-verzió létrehozásához használja a helyét, amelyben szeretné létrehozni a lemezkép verziója ugyanazon a helyen. Bizonyosodjon meg róla, hogy át kell adnia a terület forráshelyen a célrégiók egyik egy lemezkép verziója létrehozásakor.  


**K.** Hogyan határozhatom meg a rendszerkép verziója replikák hozhatók létre az egyes régiókban számát?

 A. Lemezkép verziója replikák hozhatók létre az egyes régiókban számát is megadhat két módja van:
 
1. A regionális replikáinak száma, amely azt adja meg a létrehozandó régiónként replikák száma. 
2. A közös replikáinak száma ez az alapértelmezett beállítás szerint régiók száma abban az esetben, ha regionális replikáinak száma nincs megadva. 

A regionális replikáinak száma megadásához adja át a helyet szeretne létrehozni az adott régióban replikák számával együtt: "USA déli középső RÉGIÓJA = 2". 

Ha regionális replikáinak száma nincs megadva, az egyes helyeken, replikák száma alapértelmezés szerint a megadott közös replikáinak száma lesz. 

Adja meg a közös replika parancssori felületen, használja a **--replika-count** argumentumot a `az sig image-version create` parancsot.


**K.** Létrehozhatom a megosztott lemezkép-katalógusában egy másik helyet a kívánt hozhat létre a rendszerkép definíciójában és lemezkép verziója fut, a?

 A. Igen, ez lehetséges. Azonban ajánlott eljárásként javasoljuk, hogy tartsa az erőforráscsoport, a megosztott lemezkép-katalógusában, a rendszerkép definíciójában és a lemezkép verziója ugyanazon a helyen.


**K.** Mik azok a megosztott lemezkép-katalógus használatával kell fizetni?

 A. Nem számítunk fel díjat a megosztott lemezkép-katalógus szolgáltatás, kivéve a tárolási díjakat a lemezkép-verzió és a hálózati kimenő forgalom költségeit való replikálásához a lemezkép-verzió a forrásrégióban célrégiók tárolására.

**K.** Milyen API-verziót használjam hozhat létre közös lemezkép-katalógusában, a rendszerkép definíciójában, a lemezkép verziója és a VM/VMSS kívül a rendszerkép verziószámát?

 A. A VM és a Virtual Machine Scale Set-környezetek kép verzióját használja, azt javasoljuk, használjon API-verzió a 2018-04-01-es vagy újabb verziója. Megosztott kép katalógusok, a lemezkép-definíciók és a lemezkép-verzió használatát javasoljuk, használjon API-verzió a 2018-06-01. 
