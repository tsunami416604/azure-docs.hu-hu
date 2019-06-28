---
author: cynthn
ms.author: cynthn
ms.date: 04/30/2019
ms.topic: include
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: 5884bb5dc389e6abfd4fa23d28dd71ecd16ff423
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67347113"
---
Szabványos virtuális gép (VM) rendszerképek lehetővé teszik a szervezetek migrálása a felhőbe, és győződjön meg, hogy a központi telepítések egységességét. Képek rendszerint előre definiált biztonsági és a konfigurációs beállításokat és a szükséges szoftvereket. A saját lemezkép-készítési folyamat beállítása szükséges idő, az infrastruktúra és a telepítés, de az Azure virtuális gép az Image Builder, csak adja meg a lemezkép leíró egy egyszerű konfigurálás küldheti el a szolgáltatás és a kép gyártja és forgalmazza.
 
Az Azure virtuális gép az Image Builder (az Azure az Image Builder) lehetővé teszi egy Windows vagy Linux-alapú Azure Marketplace kép, már létező egyéni rendszerképek vagy a Red Hat Enterprise Linux (RHEL) ISO, és kezdje el saját egyéni. Mert az Image Builder épül [HashiCorp Packer](https://packer.io/), a meglévő Packer rendszerhéj webhelykiépítőt parancsfájlokat is importálhat. Megadhatja azt is, hova szeretné a lemezképek, szolgáltatott, az Azure Shared lemezkép-katalógusában (virtual-machines-common-shared-image-galleries.md), egy felügyelt rendszerképet, vagy egy virtuális Merevlemezt.

> [!IMPORTANT]
> Az Azure az Image Builder jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Előzetes verziójú funkciók

Az előzetes verzióra támogatja ezeket a szolgáltatásokat:

- Létrehozása arany alapkonfiguráció-lemezképek, amelyek a minimális biztonsági és a vállalati konfigurációkat tartalmazza, és lehetővé teszik a szervezeti egységek testreszabása végezhetők rajtuk az igényeiknek.
- Meglévő lemezképek javítása, az Image Builder lehetővé teszi, hogy a meglévő egyéni rendszerképek folyamatos javítása.
- Az Azure Shared lemezkép-katalógusában, integráció lehetővé teszi terjeszt, verzió, és a méretezési globálisan lemezképeket, és a egy kép felügyeleti rendszer biztosít.
- Integráció a meglévő lemezkép folyamatok alakíthatók ki, csak az Image Builder hívása a folyamatot, vagy az egyszerű előzetes képet Builder Azure DevOps feladat.
- Telepítse át egy meglévő lemezkép-testreszabási folyamatot az Azure-bA. A meglévő szkriptekre, parancsokat és folyamatok segítségével lemezképek testreszabása.
- Red Hat használata a saját előfizetés-támogatás használata. Hozzon létre Red Hat Enterprise lemezképek használatra jogosult, használaton kívüli Red Hat-előfizetéseit.
- A VHD formátum létrehozását.
 

## <a name="regions"></a>Regions
Az Azure lemezkép Builder Service előzetes verziója az ezekben a régiókban elérhető lesz. Ezekben a régiókban kívül lemezképek terjeszthetők ki.
- USA keleti régiója
- USA 2. keleti régiója
- USA nyugati középső régiója
- USA nyugati régiója
- USA nyugati régiója, 2.

## <a name="os-support"></a>Operációsrendszer-támogatást
AIB Azure Marketplace-en alap operációsrendszer-lemezképeket fogja támogatni:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6
- CentOS 7.6
- Windows 2016
- Windows 2019


## <a name="how-it-works"></a>Működés


![Az Azure az Image Builder elméleti rajza](./media/virtual-machines-image-builder-overview/image-builder.png)

Az Azure az Image Builder egy teljes körűen felügyelt Azure-szolgáltatás, amely egy Azure-erőforrás-szolgáltató által elérhető. Az Azure az Image Builder folyamat három fő részből: forrás, testreszabása és terjesztheti, ezek szerepelnek a sablonban. Az alábbi ábrán az összetevők, a hozzájuk tartozó tulajdonságok némelyike. 
 


**Image Builder-folyamat** 

![Az Azure az Image Builder folyamat elméleti rajza](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Hozzon létre egy .JSON kiterjesztésű fájlt a lemezkép-sablont. Ezt a .JSON kiterjesztésű fájlt a forrása, a testreszabások és a terjesztési információkat tartalmaz. Nincsenek a több példát a [Azure kép Builder GitHub-adattár](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
1. Küldheti el a szolgáltatást, ez egy kép sablon összetevőt hoz létre a megadott erőforráscsoport. A háttérben az Image Builder tölti le a forrás lemezkép vagy ISO és szkriptek igény szerint. Ezek vannak tárolva egy külön erőforráscsoportot, amely automatikusan jön létre az előfizetésében, a következő formátumban: IT_\<DestinationResourceGroup>_\<TemplateName>. 
1. A lemezkép-sablon létrehozása után a lemezkép ezután hozhat létre. A háttérben az Image Builder sablon és a forrás-fájlokat használja a IT_ létrehozása egy virtuális gép, hálózati és tárolási\<DestinationResourceGroup > _\<TemplateName > erőforráscsoportot.
1. A lemezkép létrehozásának részeként az Image builder osztja el a kép azt a sablont, majd törli a további erőforrások az IT_\<DestinationResourceGroup > _\<TemplateName > a létrehozott erőforráscsoport a folyamat.


## <a name="permissions"></a>Engedélyek

Ahhoz, hogy az Azure virtuális gép az Image Builder képek vagy a felügyelt képeket, vagy egy megosztott lemezkép-katalógus terjesztéséhez, meg kell megadnia az "Azure virtuális gép az Image Builder" szolgáltatás "Közreműködői" engedélyek (Alkalmazásazonosító: cf32a0cc-373c-47c9-9156-0db11f6a6dfc ) erőforráscsoportokról. 

Ha egy meglévő egyénileg kezelt rendszerképről vagy rendszerképverzió használ, az Azure az Image Builder ezen erőforráscsoportok "Olvasó" hozzáférési legalább lesz szüksége.

Hozzáférés az Azure CLI használatával rendelheti hozzá:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

Ha a fiók nem található, amely azt jelentheti, hogy az előfizetés, amelyben ad hozzá a szerepkör-hozzárendelés van még nem regisztrált az erőforrás-szolgáltató számára.


## <a name="costs"></a>Költségek
Egyes számítási, hálózatkezelési és tárolási költségek létrehozásakor, létrehozását és az Azure az Image Builder a rendszerképek tárolására számítunk fel. Ezeket a díjakat hasonlóak az egyéni lemezképek manuális létrehozása során felmerülő költségek. Az erőforrások akkor díjat számítunk fel az Azure díjszabása. 

A lemezkép létrehozása során fájlok letöltődnek és tárolja a `IT_<DestinationResourceGroup>_<TemplateName>` költségekkel fog egy kis méretű tároló erőforráscsoportot. f nem szeretné, hogy ezek a lemezkép sablon törlése után a rendszerkép összeállítását.
 
Az Image Builder létrehoz egy virtuális Gépet D1v2 virtuális gép mérete és a tárolási és hálózati szükséges a virtuális gép. Ezeket az erőforrásokat a buildelési folyamat időtartama az elmúlt lesz, és törlődik, miután az Image Builder befejeződött, a lemezkép létrehozása. 
 
Az Azure az Image Builder fog terjessze a lemezképet a kiválasztott régiókban, ahol a hálózati kimenő forgalmi költségek léphetnek fel.
 
## <a name="next-steps"></a>További lépések 
 
Próbálja ki az Azure az Image Builder, tekintse meg a az épület cikkeket [Linux](../articles/virtual-machines/linux/image-builder.md) vagy [Windows](../articles/virtual-machines/windows/image-builder.md) lemezképek.
 
 
