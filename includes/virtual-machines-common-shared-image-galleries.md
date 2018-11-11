---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 09/20/2018
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 8eca04478fd5aba292fcc47abac37b740b552dff
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51208965"
---
Megosztott lemezkép-katalógus szolgáltatása segít kialakítani a struktúra és a szervezet a VM-rendszerképeit körül. Megosztott képgyűjtemény három fő értéknövelő biztosítja:
- Egyszerű kezelés
- Az egyéni lemezképek méretezése
- A rendszerképek megosztása – megosztani a lemezképek különböző felhasználók, az egyszerű szolgáltatások vagy AD-csoportokat a szervezet, valamint a többrégiós replikációval régiókon belül

Egy felügyelt rendszerképet egy példányát, vagy egy teljes virtuális Gépet (beleértve a bármely más csatolt lemez) vagy csak az operációsrendszer-lemez, attól függően, hogyan hoz létre a lemezképet. A lemezképből létrehozott virtuális gép, ha a virtuális merevlemezeket, a lemezkép egy példányát a lemezek létrehozása az új virtuális gép szolgálnak. A felügyelt rendszerkép tárolóban marad, és hozhat létre új virtuális gépeket és újra használható.

Ha felügyelt rendszerkép található, biztosítani kell, és szeretné elérhetővé teheti azokat a vállalat nagy számú, egy adattár, amely megkönnyíti a frissítése, és megoszthatja képeit egy közös lemezkép-katalógusában is használhatja. Egy megosztott lemezkép-katalógus használatával díjai azok csak a lemezképek replikálásához a forrás-régióból a közzétett régiók szerint a képek, valamint minden olyan hálózati adatkimeneti költségek felhasznált tárhelyért költségek.

A megosztott lemezkép-katalógusában a szolgáltatás több erőforrástípusok rendelkezik:

| Erőforrás | Leírás|
|----------|------------|
| **Felügyelt rendszerkép** | Ez egy alapkonfiguráció-rendszerképet, amely önmagában, vagy több létrehozásához használt az **megosztott kép verziók** egy rendszerkép-katalógusában.|
| **Lemezkép-katalógusában** | Az Azure piactéren, például egy **lemezkép-katalógusában** egy adattár a kezelése és megosztása a rendszerképeket, de Ön szabályozza, ki férhet. |
| **Image z galerie** | Képek ugyanazon a katalóguson belül határozza meg, és a lemezkép és a belső útmutatójához követelményeivel kapcsolatos információkat. Ez magában foglalja, hogy a kép Windows vagy Linux rendszerű, kibocsátási megjegyzések és minimális és maximális memóriára vonatkozó követelményeknek. Ez a rendszerkép-típus egy erőforrást a Resource Manager üzembe helyezési modellen belül, de nem használta azt közvetlenül VM-EK létrehozása. Egy lemezkép definíciója. |
| **Megosztott lemezkép verziója** | Egy **lemezkép verziója** meg használni a virtuális gép létrehozása katalógus használata során. Kép különböző verzióinak rendelkezhet saját környezetéhez szükséges módon. Használata esetén, egy felügyelt rendszerképet, például egy **lemezkép verziója** hozhat létre virtuális Gépet, a rendszerkép verziószámát új lemezeket a virtuális gép létrehozásához használt. Lemezkép verziója többször is használható. |

<br>


![Hogyan lehet kép különböző verzióinak a katalógusban szereplő bemutató ábra](./media/shared-image-galleries/shared-image-gallery.png)

### <a name="regional-support"></a>Regionális támogatás

Megosztott kép katalógusok regionális támogatása korlátozott előzetes verzióban érhető el, de idővel bővülni fog. A korlátozott előzetes verzióra Íme, ahol létre katalógusok régiók listája, és régiók, ahol minden image z galerie replikálhatja a listában: 

| A katalógus létrehozása  | A verzió replikálása |
|--------------------|----------------------|
| USA nyugati középső régiója    |USA déli középső régiója|
| USA 2. keleti régiója          |USA keleti régiója|
| USA déli középső régiója   |USA 2. keleti régiója|
| Délkelet-Ázsia     |USA nyugati régiója|
| Nyugat-Európa        |USA nyugati régiója, 2.|
|                    |USA középső régiója|
|                    |USA északi középső régiója|
|                    |Közép-Kanada|
|                    |Kelet-Kanada|
|                    |Észak-Európa|
|                    |Nyugat-Európa|
|                    |Dél-India|
|                    |Délkelet-Ázsia|



## <a name="scaling"></a>Méretezés
Megosztott lemezkép-katalógus lehetővé teszi, hogy meg szeretné tartani a rendszerképek Azure replikák száma. Ez segít több virtuális gépre kiterjedő központi telepítési forgatókönyvei szerint is oszlanak meg a virtuális gépek üzembe helyezése különböző replikába csökkenti az esélyét, hogy a példány létrehozása feldolgozása egyetlen replika túlterhelés miatt szabályozás alatt áll.

![Hogyan méretezhetők a képek bemutató ábra](./media/shared-image-galleries/scaling.png)


## <a name="replication"></a>Replikáció
Megosztott lemezkép-katalógus lehetővé teszi a képek automatikusan replikálja más Azure-régiókban. Minden megosztott lemezkép verziója függően mi értelme a szervezet különböző régiókban lehet replikálni. Egy példa arra, hogy a legújabb lemezkép mindig replikálása több régióban, amíg az összes korábbi verziói csak érhető el 1 régió van. Ez segít mentse a megosztott lemezkép-verziók a tárolási költségekre. A rendszer replikálja egy megosztott lemezkép verziója régiók létrehozásának időpontja után lehet frissíteni. A különböző régióban való replikálásához szükséges idő attól függ, másolását adatok mennyisége és a verzió a rendszer replikálja régiók száma. Ez eltarthat néhány óráig bizonyos esetekben. A replikáció történik, amíg a replikáció állapotát megtekintheti a régiónként. A kép replikáció befejezése után egy régióban, telepítheti a virtuális gép vagy VMSS lemezkép verzió használatával a régióban.

![Hogyan replikálhat lemezképek bemutató ábra](./media/shared-image-galleries/replication.png)


## <a name="access"></a>Hozzáférés
Mivel a megosztott lemezkép-katalógusában, a megosztott kép és a megosztott lemezkép verziója összes erőforrást, azok megoszthatók a beépített natív Azure RBAC-vezérlők használatával. Az RBAC használatával megoszthatja ezeket az erőforrásokat más felhasználók, az egyszerű szolgáltatások és csoportokat a szervezetben. Ezeket az erőforrásokat megosztó hatókörén belül az Azure AD-bérlőhöz van. Miután egy felhasználó hozzáfér a megosztott lemezkép verziója, akkor egy virtuális Gépet, vagy a Virtual Machine Scale Set bármely belül ugyanazt az Azure AD-bérlőben megosztott kép verzióval hozzáféréssel rendelkeznek az előfizetések telepítheti.  A megosztási mátrix, amely segít megérteni, hogy mi a felhasználó hozzáférést kap a következő:

| Felhasználóval megosztott     | Megosztott rendszerkép-katalógus | Megosztott kép | Megosztott lemezkép verziója |
|----------------------|----------------------|--------------|----------------------|
| Megosztott rendszerkép-katalógus | Igen                  | Igen          | Igen                  |
| Megosztott kép         | Nem                   | Igen          | Igen                  |
| Megosztott lemezkép verziója | Nem                   | Nem           | Igen                  |



## <a name="billing"></a>Számlázás
Nem jár extra megosztott Képkatalógus szolgáltatásunkat. Meg kell fizetni az alábbi forrásanyagokat:
- Tárolási költségek, a megosztott kép verziók tárolásához. Ez a verzió replikák száma és a verzió a rendszer replikálja régiók számának függ.
- A hálózati kimenő forgalom költségeit a replikáció forrás-régióból a verzió a replikált régiókban.

## <a name="frequently-asked-questions"></a>Gyakori kérdések 

**K.** Hogyan tudok regisztrálni a megosztott kép tár nyilvános előzetes verziója?
 
 A. A funkció az egyes, amelyben létre egy megosztott lemezkép-katalógusában, rendszerkép definíciójában vagy verzió képerőforrások szeretne az előfizetések a következő parancsok futtatásával regisztrálja kell regisztrálni a lemezkép-katalógusában megosztott nyilvános előzetes verzióban, és Emellett ahol szeretné telepíteni a lemezkép-verziókat használó virtuális gépek.

**PARANCSSORI FELÜLET**: 

```bash 
az feature register --namespace Microsoft.Compute --name GalleryPreview
az provider register -name Microsoft.Compute
```

**PowerShell**: 

```powershell
Register-AzureRmProviderFeature -FeatureName GalleryPreview -ProviderNamespace Microsoft.Compute
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

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


**K.** Hogyan oszthatom meg saját lemezképek több előfizetés között?
 
 A. Képek megoszthatja a szerepkör alapú hozzáférés-vezérlés (RBAC) segítségével előfizetések között. Azok a felhasználók, az olvasási engedéllyel rendelkezik az egy kép verzióra még több előfizetés, helyezhet üzembe egy virtuális gépet, a lemezkép-verzió lesz.


**K.** Áthelyezhető-e a meglévő rendszerképet a megosztott lemezkép-katalógusban?
 
 A. Igen. A képek lehet típusú alapján 3 forgatókönyv közül választhat.

 1. példa: Ha egy felügyelt rendszerképet, majd létrehozhat egy rendszerkép definíciójában és lemezkép verziója azt.

 2. forgatókönyv: Ha általánosított nem felügyelt rendszerkép, is létrehoz egy felügyelt rendszerképet belőle, és majd hozzon létre belőle egy rendszerkép definíciójában és lemezkép verziója. 

 3. forgatókönyv: Ha egy virtuális Merevlemezt a helyi fájlrendszerbe is van, akkor szüksége töltse fel a VHD-t, hozzon létre egy felügyelt rendszerképet, majd hozhat létre és definíció és származó lemezkép verziója. 
    - Windows virtuális gépek a virtuális merevlemez esetén olvassa el [általános VHD feltöltése](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
    - Ha a VHD-t egy Linux virtuális gép számára, lásd: [VHD feltöltése](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)


**K.** Létrehozhatok egy lemezkép verziója specializált lemezből?

 A. Nem, hogy jelenleg nem támogatják speciális lemezről képek. Ha egy specializált lemezzel rendelkezik, akkor [virtuális gép létrehozása virtuális merevlemezről](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal#create-a-vm-from-a-disk) a speciális lemez csatlakoztatásával egy új virtuális géphez. Ha már rendelkezik egy futó virtuális Gépre, kövesse az utasításokat, létrehozhat egy felügyelt rendszerképet, a kell a [Windows virtuális gép](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-custom-images) vagy [Linux rendszerű virtuális gép](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Ha már rendelkezik egy általános felügyelt rendszerkép, elindíthatja a folyamatot, hozzon létre egy megosztott kép leírását és a lemezkép-verzió.


**K.** Egy megosztott lemezkép-katalógusában, a rendszerkép definíciójában és a lemezkép verziója az Azure Portalon hozhatnak létre?

 A. Nem, jelenleg nem támogatjuk a az Azure Portalon keresztül megosztott lemezkép-katalógusában erőforrások létrehozását. A lemezkép-katalógusában a megosztott erőforrások – parancssori felület, sablonok és SDK-k létrehozása támogatjuk. PowerShell is elérhető lesz elérhető.

 
**K.** Létrehozása után is frissíthetők a rendszerkép definíciójában vagy a rendszerkép verziószámát? Milyen típusú részleteket is módosíthatja?

 A. Frissítheti az egyes erőforrások részleteit alább szerepelnek:
 
Megosztott képgyűjtemény:
- Leírás

kép definíciója:
- Ajánlott vcpu-k
- Memory (Memória)
- Leírás
- Élettartam dátum vége

Lemezkép verziója:
- Regionális replikáinak száma
- Célrégiók
- Legújabb kizárása
- Élettartam dátum vége


**K.** Létrehozása után lehet váltani a lemezkép-katalógusában a megosztott erőforrás egy másik előfizetésbe?

 A. Egy másik előfizetésbe nem, nem helyezhetők át a megosztott kép galéria-erőforrásokat. Azonban akkor fogja tudni replikálni a katalógusban a rendszerkép verziója a más régiókban, szükség szerint.

**K.** Replikálhatok a lemezkép-verziók felhőkben – Azure China 21Vianet, az Azure Germany és az Azure Government Cloud? 

 A. Lemezkép-verzió nem, több felhő között nem lehet replikálni.

**K.** A kép verziók replikálhatok előfizetések között? 

 A. Nem, előfordulhat, hogy a lemezkép-verzió replikálja egy adott előfizetés régióban, és használhatja más előfizetések RBAC-n keresztül.

**K.** Az Azure AD-bérlőre kiterjedő is megoszthatok lemezkép verziója? 

 A. Nem, jelenleg megosztott lemezkép-katalógus nem támogatja a lemezkép-verzió megosztása az Azure AD-bérlőn. Azonban előfordulhat, hogy szolgáltatással privát ajánlatok az Azure piactéren, ennek érdekében.


**K.** Mennyi ideig tart a lemezkép-verzió replikálása a célként megadott régióban?

 A. A lemezkép verzió replikációs idő az teljes mértékben függ a lemezkép és régiók, replikálódnak számának méretét. Azonban ajánlott eljárásként javasoljuk, hogy kis tartania a rendszerképet, és a forrás és cél régiókat zárja be a legjobb eredmények elérése érdekében. Ellenőrizheti, hogy a replikáció állapota – jelző használatával a replikáció állapotát.


**K.** Hány megosztott kép katalógusok is hozhatok létre egy előfizetésben?

 A. Az alapértelmezett kvóta van: 
- 10 megosztott kép minták száma előfizetésenként és régiónként
- 200 lemezkép-definíciókat, előfizetésenként és régiónként
- 2000 lemezkép-verzió előfizetésenként és régiónként


**K.** Mi a különbség a forrásrégióban és célrégió?

 A. Forrásrégió a régió, amelyben a lemezkép verziója jön létre, célrégiók pedig a régió, amelyben tárolni egy példányát a lemezkép-verzió. Minden rendszerkép verzióhoz csak rendelkezhet egy forrásrégióban. Bizonyosodjon meg róla, hogy át kell adnia a terület forráshelyen a célrégiók egyik egy lemezkép verziója létrehozásakor.  


**K.** Hogyan határozhatom meg a forrásrégióban a lemezkép-verzió létrehozása során?

 A. Egy lemezkép verziója létrehozásakor használhatja a **--hely** címke CLI-ben és a **-hely** címke a PowerShell használatával adja meg a forrásrégióban. Győződjön meg a felügyelt rendszerképet, amely az alaprendszerképet, a lemezkép-verzió létrehozásához használja a helyét, amelyben szeretné létrehozni a lemezkép verziója ugyanazon a helyen. Bizonyosodjon meg róla, hogy át kell adnia a terület forráshelyen a célrégiók egyik egy lemezkép verziója létrehozásakor.  


**K.** Hogyan határozhatom meg a rendszerkép verziója replikák hozhatók létre az egyes régiókban számát?

 A. Lemezkép verziója replikák hozhatók létre az egyes régiókban számát is megadhat két módja van:
 
1. A regionális replikáinak száma, amely azt adja meg a létrehozandó régiónként replikák száma. 
2. A közös replikáinak száma ez az alapértelmezett beállítás szerint régiók száma abban az esetben, ha regionális replikáinak száma nincs megadva. 

Adja meg a regionális replika, át kell adnia a helyet szeretne létrehozni az adott régióban hasonló replikák számával együtt: "USA déli középső RÉGIÓJA = 2". 

Ha regionális replikáinak száma nincs megadva, az egyes helyeken, replikák száma alapértelmezés szerint a megadott közös replikáinak száma lesz. 

Adja meg a közös replika parancssori felületen, használja a **--replika-count** argumentumot a `az sig image-version create` parancsot.


**K.** Létrehozhatom a megosztott lemezkép-katalógusában egy másik helyet a kívánt hozhat létre a rendszerkép definíciójában és lemezkép verziója fut, a?

 A. Igen, ez a lehetőség. Azonban ajánlott eljárásként javasoljuk, hogy az erőforráscsoport, a megosztott lemezkép-katalógusában, a rendszerkép definíciójában és a lemezkép-verzió megtartása ugyanazon a helyen.


**K.** Mik azok a megosztott lemezkép-katalógus használatával kell fizetni?

 A. Nem számítunk fel díjat a megosztott lemezkép-katalógus szolgáltatás, kivéve a tárolási díjakat a lemezkép-verzió és a hálózati kimenő forgalom költségeit való replikálásához a lemezkép-verzió a forrásrégióban célrégiók tárolására.

**K.** Milyen API-verziót használjam hozhat létre közös lemezkép-katalógusában, a rendszerkép definíciójában, a lemezkép verziója és a VM/VMSS kívül a rendszerkép verziószámát?

 A. A VM és a Virtual Machine Scale Set-környezetek kép verzióját használja, azt javasoljuk, használjon API-verzió a 2018-04-01-es vagy újabb verziója. Megosztott kép katalógusok, a lemezkép-definíciók és a lemezkép-verzió használatát javasoljuk, használjon API-verzió a 2018-06-01. 
