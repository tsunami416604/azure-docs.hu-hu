---
title: "Virtuális gép méretének Azure-szolgáltatásokhoz |} Microsoft Docs"
description: "Azure cloud service webes és feldolgozói szerepkörök különböző virtuális gépek méretét (és azonosítók) sorolja fel."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 1127c23e-106a-47c1-a2e9-40e6dda640f6
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: a5ac8c46f17d2d1c2f20ed2cc2348f50b7739ddf
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="sizes-for-cloud-services"></a>A Felhőszolgáltatások mérete
Ez a témakör ismerteti az elérhető méretek és a felhőalapú szolgáltatás szerepkörpéldányokat (webes és feldolgozói szerepkörök) beállításokat. Telepítési szempontok kell ügyelnie, ha tervezi, hogy használja ezeket az erőforrásokat is biztosít. Minden méretét helyezett Azonosítóval rendelkezik a [szolgáltatásdefiníciós fájl](cloud-services-model-and-package.md#csdef). Egyes árak érhetők el a [Cloud Services díjszabása](https://azure.microsoft.com/pricing/details/cloud-services/) lap.

> [!NOTE]
> Kapcsolódó Azure korlátozását, olvassa el [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../azure-subscription-service-limits.md)
>
>

## <a name="sizes-for-web-and-worker-role-instances"></a>Webes és feldolgozói szerepkör-példányok méretek
Az Azure-ban több standard méret közül választhat. A méretek némelyikével kapcsolatos megfontolások a következők:

* A D-sorozat virtuális gépei nagyobb számítási teljesítményt és ideiglenes lemezteljesítményt igénylő alkalmazások futtatására lettek kialakítva. A D-sorozat virtuális gépei gyorsabb processzorokat, nagyobb magonkénti memóriaarányt, valamint az ideiglenes lemezteljesítményhez SSD meghajtókat kínálnak. Részletekért lásd az Azure blogon megjelent bejelentést [a D-sorozat új virtuális gépméreteit](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/) ismertető bejegyzésben.
* A Dv2-sorozat az eredeti D-sorozat újabb verziója, amely nagyobb teljesítményű processzorokat kínál. A Dv2-sorozathoz használt processzor mintegy 35%-kal gyorsabb, mint a D-sorozathoz használt processzorok. A legújabb generációs 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) processzoron alapul, és a teljesítménye az Intel Turbo Boost Technology 2.0 alkalmazásával akár 3,1 GHz-re is növelhető. A Dv2-sorozat ugyanolyan memória- és lemezkonfigurációkban érhető el, mint a D-sorozat.
* A G-sorozat virtuális gépei kínálják a legtöbb memóriát, és az Intel Xeon E5 V3 család processzorait tartalmazó gazdagépeken futnak.
* Az A-sorozatú virtuális gépek is telepíthető a különböző hardvertípusok, és a processzorok. A méret folyamatban van, a hardver kínálta konzisztens a processzor teljesítménye a futó példány, függetlenül a telepítették a hardver alapján. Az adott méretet futtató fizikai hardver meghatározásához kérdezze le a virtuális hardvert a virtuális gépen belülről.
* Az A0 méret esetében a fizikai hardvernek az ideálisnál több előfizetést kell kezelnie. Ennek az egy méretnek az esetében a többi felhasználói üzemelő példány befolyásolhatja a futó számítási feladat teljesítményét. A relatív teljesítmény várható alapértéke az alábbiak szerint alakul, hozzávetőleg 15 százalékos varianciával.

A virtuális gépek mérete befolyásolja az árképzést. A méret emellett hatással van a virtuális gép feldolgozási teljesítményére, valamint a memória- és tárhelykapacitására is. A tárolási díjak számítása ettől külön történik a tárfiókban használt oldalak mennyisége alapján. További információkért lásd: [Cloud Services díjszabása](https://azure.microsoft.com/pricing/details/cloud-services/) és [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/).

Az alábbiak segíthetnek a megfelelő méret kiválasztásában:

* Az A8–A11- és a H-sorozat méretei más néven *nagy számítási igényű példányokként* ismertek. Az ezeket a méreteket futtató hardver a nagy számítási és hálózatigényű alkalmazások futtatására lett kialakítva és optimalizálva, ide értve a nagy teljesítményű feldolgozási (HPC) fürtalkalmazásokat, a modellezést és a szimulációkat. Az A8–A11-sorozat Intel Xeon E5-2670 @ 2,6 GHz-es, a H-sorozat pedig Intel Xeon E5-2667 v3 @ 3,2 GHz-es processzorokat használ. Részletes információkat és szempontokat, ezek mérete használatáról, [nagy teljesítményű számítási Virtuálisgép-méretek](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Dv2-sorozat, D sorozatú, G-sorozat, alkalmazásokat, amelyek a gyorsabb CPU igény ideálisak, jobb helyi lemez teljesítményét, vagy hogy a nagyobb memória iránti igények kielégítése érdekében. Nagyon hatékony kombinációt kínálnak számos nagyvállalati szintű alkalmazáshoz.
* Az Azure-központokban lévő némelyik fizikai gazdagép nem képes futtatni a nagyobb virtuálisgép-méreteket, például az A5–A11 méreteket. Ennek eredményeképpen előfordulhat, hogy a hibaüzenet megtekintéséhez **nem sikerült konfigurálni a virtuális gép {számítógépnév}** vagy **nem sikerült létrehozni a virtuális gép {számítógépnév}** egy meglévő virtuális gépet egy új méretűre; átméretezésekor 2013. április 16.; előtt létrehozott virtuális hálózatban egy új virtuális gép létrehozása vagy egy új virtuális gép hozzáadása egy meglévő felhőszolgáltatáshoz. Lásd: [hiba: "Nem sikerült a virtuális gép konfigurálása"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) a támogatási fórum a lehetséges megoldások az egyes központi telepítési forgatókönyvek esetén.
* Az előfizetés is korlátozhatja az egyes családokban üzembe helyezhető magok mennyiségét. A kvóták növelésével kapcsolatban vegye fel a kapcsolatot az Azure ügyfélszolgálatával.

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások
A fogalom, az Azure számítási egység (ACU) lehetővé teszik az Azure termékváltozatok között (CPU) számítási teljesítmény összehasonlítása létrehoztunk és azonosítani tudja az az oka valószínűleg a teljesítmény kielégítéséhez az SKU-nak szüksége van.  Az ACU jelenlegi standard alapjaként a Kisméretű (Standard_A1) virtuális gép 100-as értéket képvisel, és a többi termékváltozat értéke ehhez képest jelöli, hogy mennyivel gyorsabban futtatja az adott termékváltozat a standard teljesítménytesztet.

> [!IMPORTANT]
> Az ACU csupán iránymutatóként szolgál. Az egyes számítási terhelések eredményei ettől eltérhetnek.
>
>

<br>

| Termékváltozat-család | ACU/mag |
| --- | --- |
| [ExtraSmall](#a-series) |50 |
| [Kis ExtraLarge](#a-series) |100 |
| [A5-7](#a-series) |100 |
| [Standard_A1-8v2](#av2-series) |100 |
| [Standard_A2m-8mv2](#av2-series) |100 |
| [A8-A11](#a-series) |225* |
| [D1-14](#d-series) |160 |
| [D1-15v2](#dv2-series) |210 - 250* |
| [G1-5](#g-series) |180 - 240* |
| [H](#h-series) |290 - 300* |

A * jelzésű ACU-értékek Intel® Turbo technológia használatával növelik a processzor órajelét, és ezáltal a teljesítményt. A növekedés mértéke a virtuális gép méretétől és terhelésétől, valamint az ugyanazon a gazdagépen futó számítási feladatoktól függően eltérő lehet.

## <a name="size-tables"></a>Mérettáblázatok
Az alábbi táblázatokban a méretek és azok kapacitásai láthatóak.

* A tárolókapacitás mértékegysége GiB (gibibájt = 1024^3 bájt). A gigabájtban (1000^3 bájt) és a gibibájtban (1024^3 bájt) mért meghajtók összehasonlításakor tartsa észben, hogy a GiB-ban kifejezett kapacitások kisebbnek tűnhetnek. Például: 1023 GiB = 1098,4 GB
* A lemezteljesítmény másodpercenkénti bemeneti/kimeneti műveletek (IOPS) mennyiségeként van kifejezve, valamint MBps-ben, ahol 1 MBps = 10^6 bájt/másodperc.
* Az adatlemezek gyorsítótárazott és gyorsítótárazás nélküli módban üzemelhetnek. Gyorsítótárazott adatlemezüzem esetében a gazdagép gyorsítótáras üzemmódja **ReadOnly** (Csak olvasás) vagy **ReadWrite** (Írás és olvasás) beállításra van konfigurálva. Gyorsítótárazás nélküli adatlemezüzem esetében a gazdagép gyorsítótáras üzemmódja **None** (Nincs) beállításra van konfigurálva.
* A maximális hálózati sávszélesség a virtuálisgép-típusonként kiosztott és hozzárendelt maximális összesített sávszélesség. A maximális sávszélesség iránymutatást nyújthat a megfelelő VM-típus kiválasztása során, hogy biztosítható legyen a megfelelő hálózati kapacitás rendelkezésre állása. Ha áthelyezése másik alacsony, közepes, magas és nagyon magas, az átviteli sebesség ennek megfelelően növekszik. A tényleges hálózati teljesítmény számos tényezőtől függ, többek közt a hálózat és az alkalmazás terhelésétől, valamint az alkalmazás hálózati beállításaitól.

## <a name="a-series"></a>A-sorozat
| Méret            | Processzormagok | Memória: GiB  | Helyi merevlemez: GiB       | Hálózati adapterek max. száma/hálózati sávszélesség |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| ExtraSmall      | 1         | 0.768        | 20                   | 1/alacsony |
| Kicsi           | 1         | 1.75         | 225                  | 1/közepes |
| Közepes          | 2         | 3,5 GB       | 490                  | 1/közepes |
| Nagy           | 4         | 7            | 1000                 | 2/magas |
| ExtraLarge      | 8         | 14           | 2040                 | 4/magas |
| A5              | 2         | 14           | 490                  | 1/közepes |
| A6              | 4         | 28           | 1000                 | 2/magas |
| A7              | 8         | 56           | 2040                 | 4/magas |

## <a name="a-series---compute-intensive-instances"></a>A-sorozat – nagy számítási igényű példányok
További tudnivalókat és szempontokat, ezek mérete használatáról, tekintse meg a [nagy teljesítményű számítási Virtuálisgép-méretek](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

| Méret            | Processzormagok | Memória: GiB  | Helyi merevlemez: GiB       | Hálózati adapterek max. száma/hálózati sávszélesség |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| A8 *             |8          | 56           | 1817                 | 2/magas |
| A9-ES *             |16         | 112          | 1817                 | 4/nagyon magas |
| A10             |8          | 56           | 1817                 | 2/magas |
| A11             |16         | 112          | 1817                 | 4/nagyon magas |

\*RDMA-kompatibilis

## <a name="av2-series"></a>Av2-sorozat

| Méret            | Processzormagok | Memória: GiB  | Helyi SSD: GiB       | Hálózati adapterek max. száma/hálózati sávszélesség |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_A1_v2  | 1         | 2            | 10                   | 1/közepes                 |
| Standard_A2_v2  | 2         | 4            | 20                   | 2/közepes                 |
| Standard_A4_v2  | 4         | 8            | 40                   | 4/magas                     |
| Standard_A8_v2  | 8         | 16           | 80                   | 8/magas                     |
| Standard_A2m_v2 | 2         | 16           | 20                   | 2/közepes                 |
| Standard_A4m_v2 | 4         | 32           | 40                   | 4/magas                     |
| Standard_A8m_v2 | 8         | 64           | 80                   | 8/magas                     |


## <a name="d-series"></a>D-sorozat
| Méret            | Processzormagok | Memória: GiB  | Helyi SSD: GiB       | Hálózati adapterek max. száma/hálózati sávszélesség |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1     | 1         | 3.5          | 50                   | 1/közepes |
| Standard_D2     | 2         | 7            | 100                  | 2/magas |
| Standard_D3     | 4         | 14           | 200                  | 4/magas |
| Standard_D4     | 8         | 28           | 400                  | 8/magas |
| Standard_D11    | 2         | 14           | 100                  | 2/magas |
| Standard_D12    | 4         | 28           | 200                  | 4/magas |
| Standard_D13    | 8         | 56           | 400                  | 8/magas |
| Standard_D14    | 16        | 112          | 800                  | 8/nagyon magas |

## <a name="dv2-series"></a>Dv2-sorozat
| Méret            | Processzormagok | Memória: GiB  | Helyi SSD: GiB       | Hálózati adapterek max. száma/hálózati sávszélesség |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1_v2  | 1         | 3.5          | 50                   | 1/közepes |
| Standard_D2_v2  | 2         | 7            | 100                  | 2/magas |
| Standard_D3_v2  | 4         | 14           | 200                  | 4/magas |
| Standard_D4_v2  | 8         | 28           | 400                  | 8/magas |
| Standard_D5_v2  | 16        | 56           | 800                  | 8/rendkívül magas |
| Standard_D11_v2 | 2         | 14           | 100                  | 2/magas |
| Standard_D12_v2 | 4         | 28           | 200                  | 4/magas |
| Standard_D13_v2 | 8         | 56           | 400                  | 8/magas |
| Standard_D14_v2 | 16        | 112          | 800                  | 8/rendkívül magas |
| Standard_D15_v2 | 20        | 140          | 1,000                | 8/rendkívül magas |

## <a name="g-series"></a>G-sorozat
| Méret            | Processzormagok | Memória: GiB  | Helyi SSD: GiB       | Hálózati adapterek max. száma/hálózati sávszélesség |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_G1     | 2         | 28           | 384                  |1/magas |
| Standard_G2     | 4         | 56           | 768                  |2/magas |
| Standard_G3     | 8         | 112          | 1,536                |4/nagyon magas |
| Standard_G4     | 16        | 224          | 3,072                |8/rendkívül magas |
| Standard szintű, G5     | 32        | 448          | 6,144                |8/rendkívül magas |

## <a name="h-series"></a>H-sorozat
Az Azure H-sorozat virtuális gépei a nagy teljesítményű feldolgozásra képes virtuális gépek következő generációja, amelyek olyan magas szintű számítási igényekre lettek kialakítva, mint a molekuláris modellezés vagy a folyadékdinamikai számítások. Ezek 8 és 16 mag virtuális gépek a Intel Haswell E5-2667 V3 processzor technológia DDR4 memória és a helyi SSD-alapú tárolási kiemeli épül.

A jelentős CPU-teljesítmény mellett a H-sorozat különféle lehetőségeket kínál a kis késésű RDMA-hálózatkezeléshez az FDR InfiniBand használatával, valamint számos memóriakonfigurációt is a memóriaigényes számítási követelmények támogatására.

| Méret            | Processzormagok | Memória: GiB  | Helyi SSD: GiB       | Hálózati adapterek max. száma/hálózati sávszélesség |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_H8     | 8         | 56           | 1000                 | 8/magas |
| Standard_H16    | 16        | 112          | 2000                 | 8/nagyon magas |
| Standard_H8m    | 8         | 112          | 1000                 | 8/magas |
| Standard_H16m   | 16        | 224          | 2000                 | 8/nagyon magas |
| Standard_H16r*  | 16        | 112          | 2000                 | 8/nagyon magas |
| Standard_H16mr* | 16        | 224          | 2000                 | 8/nagyon magas |

\*RDMA-kompatibilis

## <a name="configure-sizes-for-cloud-services"></a>A Felhőszolgáltatások mérete konfigurálása
A szolgáltatásmodell által ismertetett részeként is megadhat a szerepkör-példány a virtuális gép méretét a [szolgáltatásdefiníciós fájl](cloud-services-model-and-package.md#csdef). A szerepkör méretét határozza meg, a CPU magok száma, a memória kapacitás, és a helyi rendszer fájlméret, egy futó példány számára van lefoglalva. Válassza ki a szerepkör méretét, az alkalmazás erőforrás követelmény alapján.

A szerepkör méretét beállításához például [D2](#general-purpose-d) a webes szerepkör példány:

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2">
...
</WorkerRole>
```

## <a name="changing-the-size-of-an-existing-role"></a>Egy meglévő szerepkör méretének módosítása

A munkaterhelési változások vagy új Virtuálisgép-méretek rendelkezésre állására jellegétől, érdemes lehet a szerepkör méretének módosítása. Ehhez meg kell módosítsa a Virtuálisgép-méretet a szolgáltatásdefiníciós fájlban (a fentiek szerint), csomagolja újra a Felhőszolgáltatás és telepítheti azt. Nincs lehetőség a Virtuálisgép-méretek közvetlenül a portál vagy PowerShell vált.

>[!TIP]
> Érdemes lehet másik Virtuálisgép-méretek használja az adott szerepkörhöz különböző környezetekben (például) teszt vs éles környezet). Ehhez a egyirányú azt több szolgáltatás definíciós (.csdef) fájlok létrehozásához a projektben, majd hozzon létre másik felhőt / környezet szervizcsomagok során az automatizált build a CSPack eszközzel. Cloud services csomag és hogyan hozza létre a címzetteket elemeivel kapcsolatos további tudnivalókért lásd: [Mi az a cloud services-modell, és hogyan tegye csomag azt?](cloud-services-model-and-package.md)
>
>

## <a name="get-a-list-of-sizes"></a>Méretek listáját
PowerShell vagy a REST API használatával méretek listáját. A REST API dokumentált [Itt](https://msdn.microsoft.com/library/azure/dn469422.aspx). A következő kód egy PowerShell-parancsot, amely felsorolja a különböző méretű egy adott hely. 

```powershell
Get-AzureRmVMSize -Location 'West Europe'
```

## <a name="next-steps"></a>További lépések
* Ismerje meg [az Azure-előfizetések és -szolgáltatások korlátozásait, kvótáit és megkötéseit](../azure-subscription-service-limits.md).
* További [kapcsolatos nagy teljesítményű számítási a Virtuálisgép-méretek](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) HPC-munkaterhelések.
