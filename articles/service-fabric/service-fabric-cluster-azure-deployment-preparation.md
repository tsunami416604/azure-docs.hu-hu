---
title: Azure Service Fabric-fürt telepítésének megtervezése
description: Ismerje meg az éles Service Fabric-fürt azure-beli üzembe helyezésének tervezését és előkészítését.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 1762a6975448301957579b3437a8af5c89b3accd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78193476"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Fürt telepítésének megtervezése és előkészítése

Az éles fürt üzembe helyezésének megtervezése és előkészítése nagyon fontos.  Sok tényezőt kell figyelembe venni.  Ez a cikk végigvezeti a fürt központi telepítésének előkészítésének lépéseit.

## <a name="read-the-best-practices-information"></a>Olvassa el a gyakorlati tanácsokra vonatkozó információkat
Az Azure Service Fabric-alkalmazások és -fürtök sikeres kezeléséhez vannak olyan műveletek, amelyeket erősen ajánlott végrehajtani az éles környezet megbízhatóságának optimalizálása érdekében.  További információkért olvassa el [a Service Fabric-alkalmazás és a fürt ajánlott eljárásait.](service-fabric-best-practices-overview.md)

## <a name="select-the-os-for-the-cluster"></a>A fürt operációs rendszerének kiválasztása
A Service Fabric lehetővé teszi a Service Fabric-fürtök létrehozását bármely virtuális gépen vagy Windows Server vagy Linux operációs rendszert futtató számítógépen.  A fürt telepítése előtt ki kell választania az operációs rendszert: Windows vagy Linux.  A fürt minden csomópontja (virtuális gépe) ugyanazt az operációs rendszert futtatja, nem keverheti a Windows és linuxos virtuális gépeket ugyanabban a fürtben.

## <a name="capacity-planning"></a>Kapacitástervezés
A kapacitástervezés az éles rendszerek üzembe helyezésének lényeges lépése. Az alábbiakban néhány dolog láthat, amelyet érdemes a folyamat részeként figyelembe vennie.

* A fürt csomóponttípusainak kezdeti száma 
* Az egyes csomóponttípusok tulajdonságai (méret, példányok száma, elsődleges, internetfelé néző, virtuális gépek száma stb.)
* A fürt megbízhatóságra és tartósságra vonatkozó jellemzői.

### <a name="select-the-initial-number-of-node-types"></a>A csomóponttípusok kezdeti számának kiválasztása
Először ki kell találnia, hogy a létrehozott fürt mire lesz használva. Milyen típusú alkalmazásokat tervez telepíteni a fürtbe? Az alkalmazás több szolgáltatással rendelkezik, és bármelyiknek nyilvánosnak vagy internettel szemben kell lennie? A szolgáltatások (amelyek az alkalmazást alkotják) különböző infrastruktúra-igényeket, például nagyobb RAM-mal vagy nagyobb CPU-ciklusokkal rendelkeznek? A Service Fabric-fürt egynél több csomóponttípusból állhat: egy elsődleges csomóponttípusból és egy vagy több nem elsődleges csomóponttípusból. Minden csomóponttípus egy virtuálisgép-méretezési csoporthoz van rendelve. Ezután mindegyik csomóponttípus egymástól függetlenül skálázható vertikálisan le vagy fel, eltérő nyitott portokkal rendelkezhet, és eltérő kapacitásmetrikái lehetnek. [A csomóponttulajdonságok és az elhelyezési megkötések][placementconstraints] beállíthatók úgy, hogy meghatározott szolgáltatásokat meghatározott csomóponttípusokra korlátozzanak.  További információ: A fürt által [elkezdett csomóponttípusok száma.](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with)

### <a name="select-node-properties-for-each-node-type"></a>Csomóponttulajdonságok kijelölése minden csomóponttípushoz
Csomóponttípusok határozzák meg a virtuális gép termékváltozatát, számát és tulajdonságait a virtuális gépek a társított méretezési csoportban.

A virtuális gépek minimális méretét az egyes csomóponttípusokhoz a csomóponttípushoz kiválasztott [tartóssági szint][durability] határozza meg.

Az elsődleges csomóponttípus virtuális gépeinek minimális számát a kiválasztott [megbízhatósági szint][reliability] határozza meg.

Tekintse meg az [elsődleges csomóponttípusokra](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance), a [nem elsődleges csomóponttípusok állapotmentes munkaterheléseire](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads), valamint a [nem elsődleges csomóponttípusok állapot nélküli munkaterhelésére](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads)vonatkozó minimális javaslatokat .

A csomópontok minimális számánál nagyobb számnak az ebben a csomóponttípusban futtatni kívánt alkalmazás/szolgáltatások replikáinak számán kell alapulnia.  [A Service Fabric-alkalmazások kapacitástervezése](service-fabric-capacity-planning.md) segít megbecsülni az alkalmazások futtatásához szükséges erőforrásokat. A fürt bármikor fel- vagy leskálázható később az alkalmazás munkaterhelésének módosításához. 

#### <a name="use-ephemeral-os-disks-for-virtual-machine-scale-sets"></a>Ideiglenes operációsrendszer-lemezek használata virtuálisgép-méretezési készletekhez

*Az ideiglenes operációsrendszer-lemezek* a helyi virtuális gépen (VM) létrehozott tárolók, és nem kerülnek mentésre a távoli Azure Storage-ba. Ezek minden Service Fabric-csomóponttípushoz (elsődleges és másodlagos) ajánlottak, mivel a hagyományos állandó operációsrendszer-lemezekhez, a rövid élettartamú operációsrendszer-lemezekhez képest:

* Az olvasási/írási késés csökkentése az operációs rendszer lemezére
* Gyorsabb alaphelyzetbe állítási/újraképezési csomópontkezelési műveletek engedélyezése
* Csökkentse a teljes költségeket (a lemezek ingyenesek, és nincsenek további tárolási költségek)

Az ideiglenes operációsrendszer-lemezek nem egy adott Service Fabric-szolgáltatás, hanem az Azure *virtuálisgép-méretezési készletek,* amelyek a Service Fabric-csomóponttípusok le vannak képezve. A Service Fabric használatával a fürt Azure Resource Manager-sablonjában a következő t igényel:

1. Győződjön meg arról, hogy a csomóponttípusok megadják a [támogatott Azure virtuálisgép-méreteket](../virtual-machines/windows/ephemeral-os-disks.md) az ideiglenes operációsrendszer-lemezekhez, és hogy a virtuális gép mérete elegendő gyorsítótármérettel rendelkezik-e az operációs rendszer lemezméretének támogatásához (lásd az alábbi *megjegyzést.)* Például:

    ```xml
    "vmNodeType1Size": {
        "type": "string",
        "defaultValue": "Standard_DS3_v2"
    ```

    > [!NOTE]
    > Győződjön meg arról, hogy válasszon egy virtuális gép méretét, amelynek gyorsítótármérete megegyezik vagy nagyobb, mint maga a virtuális gép operációs rendszer lemezmérete, ellenkező esetben az Azure-központi telepítés hibát okozhat (még akkor is, ha eredetileg elfogadott).

2. Adja meg a virtuálisgép-méretezési csoport verzióját (`vmssApiVersion`) a következő ből: `2018-06-01`

    ```xml
    "variables": {
        "vmssApiVersion": "2018-06-01",
    ```

3. A központi telepítési sablon virtuálisgép-méretezési `Local` csoport `diffDiskSettings`szakaszában adja meg a következő beállítást:

    ```xml
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
        "virtualMachineProfile": {
            "storageProfile": {
                "osDisk": {
                        "caching": "ReadOnly",
                        "createOption": "FromImage",
                        "diffDiskSettings": {
                            "option": "Local"
                        },
                }
            }
        }
    ```

További információkért és további konfigurációs lehetőségekért tekintse [meg az Ideiglenes operációsrendszer-lemezek et az Azure virtuális gépekhez](../virtual-machines/windows/ephemeral-os-disks.md) 


### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Válassza ki a fürt tartóssági és megbízhatósági szintjeit
A tartóssági szint jelzi a rendszer számára a virtuális gépek az alapul szolgáló Azure-infrastruktúrával rendelkező jogosultságokat. Az elsődleges csomópont típusában ez a jogosultság lehetővé teszi, hogy a Service Fabric szüneteltesse a virtuális gép szintű infrastruktúra-kérelmek (például a virtuális gép újraindítása, virtuális gép újraképzése vagy virtuális gép áttelepítése), amelyek hatással vannak a rendszerszolgáltatások és az állapotalapú szolgáltatások kvórumkövetelményeinek. A nem elsődleges csomóponttípusok, ez a jogosultság lehetővé teszi, hogy a Service Fabric szüneteltesse a virtuálisgép-szintű infrastruktúra-kérelmek (például a virtuális gép újraindítása, virtuális gép újraképe és a virtuális gép áttelepítése), amelyek hatással vannak az állapotalapú szolgáltatások kvórumkövetelményeinek.  A különböző szintek előnyeiről és a használni uk i szintre vonatkozó ajánlásokról lásd [a fürt tartóssági jellemzőit.][durability]

A megbízhatósági szint a fürtben az elsődleges csomóponttípuson futtatni kívánt rendszerszolgáltatások replikáinak számát állítja be. Minél több a replikák száma, annál megbízhatóbbak a rendszerszolgáltatások a fürtben.  A különböző szintek előnyeit és a használni és mikor megfogalmazott javaslatokat [lásd: A fürt megbízhatósági jellemzői][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Fordított proxy és/vagy DNS engedélyezése
A fürtön belül egymáshoz csatlakozó szolgáltatások általában közvetlenül hozzáférhetnek más szolgáltatások végpontjaihoz, mivel a fürt csomópontjai ugyanazon a helyi hálózaton vannak. A szolgáltatások közötti csatlakozás megkönnyítése érdekében a Service Fabric további szolgáltatásokat nyújt: [DNS-szolgáltatás](service-fabric-dnsservice.md) és [fordított proxyszolgáltatás.](service-fabric-reverseproxy.md)  Mindkét szolgáltatás engedélyezhető fürt telepítésekor.

Mivel számos szolgáltatás, különösen a konténeres szolgáltatások, rendelkezhetnek egy meglévő URL-névvel, mivel ezeket a szabványos DNS protokoll használatával (nem pedig az elnevezési szolgáltatás protokoll) kényelmesen fel tudja oldani, különösen az alkalmazások "lift and shift" forgatókönyveiben. Pontosan ezt teszi a DNS-szolgáltatás. Lehetővé teszi a DNS-nevek leképezése egy szolgáltatásnévhez, és így feloldhatja a végpont IP-címeit.

A fordított proxy a fürtben lévő, HTTP-végpontokat (beleértve a HTTPS-t is) elérhető szolgáltatásokat címezi. A fordított proxy nagyban leegyszerűsíti más szolgáltatások hívását egy adott URI-formátum biztosításával.  A fordított proxy is kezeli a feloldási, csatlakozás, és újra szükséges lépéseket az egyik szolgáltatás kommunikálni egy másik.

## <a name="prepare-for-disaster-recovery"></a>Felkészülés vészhelyreállításra
A magas rendelkezésre állás biztosításának kritikus része annak biztosítása, hogy a szolgáltatások túléljék a különböző típusú hibákat. Ez különösen fontos a nem tervezett és az ön ellenőrzésén kívül álló hibák esetén. [Felkészülés a vészhelyreállításra](service-fabric-disaster-recovery.md) néhány gyakori hibamódot ír le, amelyek katasztrófák lehetnek, ha nem modellezik és nem megfelelően kezelik. Azt is tárgyalja mérséklések és intézkedéseket kell tenni, ha a katasztrófa történt egyébként.

## <a name="production-readiness-checklist"></a>Termelési készenlét ellenőrzőlistája
Az alkalmazás és a fürt készen áll az éles forgalom ra? A fürt éles környezetben történő üzembe helyezése előtt futtassa végig az [Éles készültségi ellenőrzőlistát.](service-fabric-production-readiness-checklist.md) Az ellenőrzőlista elemeinek átdolgozásával zökkenőmentesen futtathatja az alkalmazást és a fürtöt. Javasoljuk, hogy ezeket a cikkeket ki kell pipálni a termelés előtt.

## <a name="next-steps"></a>További lépések
* [Windows rendszerű Service Fabric-fürt létrehozása](service-fabric-best-practices-overview.md)
* [Linuxot futtató Service Fabric-fürt létrehozása](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster