---
title: Virtuális gépméretek az Azure Cloud-szolgáltatásokhoz | Microsoft dokumentumok
description: Felsorolja a különböző virtuális gép méretei (és azonosítók) az Azure felhőszolgáltatás webes és feldolgozói szerepkörök.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: 2549cb0408c9dad3e92f2cec9625757de45a10dc
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086249"
---
# <a name="sizes-for-cloud-services"></a>A felhőszolgáltatások méretei
Ez a témakör a Felhőszolgáltatás szerepkörpéldányai (webes szerepkörök és feldolgozói szerepkörök) rendelkezésre álló méreteit és beállításait ismerteti. Az erőforrások használatának tervezésekor is figyelembe veendő telepítési szempontokat is tartalmaz. Minden mérethez egy azonosító van, amelyet a [szolgáltatásdefiníciós fájlba](cloud-services-model-and-package.md#csdef)helyez. Az egyes méretek árai a [Cloud Services díjszabási](https://azure.microsoft.com/pricing/details/cloud-services/) oldalán érhetők el.

> [!NOTE]
> A kapcsolódó Azure-korlátok megtekintéséhez tekintse meg [az Azure-előfizetési és szolgáltatáskorlátokat, kvótákat és korlátozásokat.](../azure-resource-manager/management/azure-subscription-service-limits.md)
>
>

## <a name="sizes-for-web-and-worker-role-instances"></a>Webes és feldolgozói szerepkör-példányok méretei
Az Azure-ban több standard méret közül választhat. A méretek némelyikével kapcsolatos megfontolások a következők:

* A D-sorozat virtuális gépei nagyobb számítási teljesítményt és ideiglenes lemezteljesítményt igénylő alkalmazások futtatására lettek kialakítva. A D-sorozat virtuális gépei gyorsabb processzorokat, nagyobb magonkénti memóriaarányt, valamint az ideiglenes lemezteljesítményhez SSD meghajtókat kínálnak. Részletekért lásd az Azure blogon megjelent bejelentést [a D-sorozat új virtuális gépméreteit](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/) ismertető bejegyzésben.
* Dv3-sorozat, Dv2-sorozat, a follow-on, hogy az eredeti D-sorozat, tartalmaz egy erősebb CPU. A Dv2-sorozathoz használt processzor mintegy 35%-kal gyorsabb, mint a D-sorozathoz használt processzorok. A legújabb generációs 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) processzoron alapul, és a teljesítménye az Intel Turbo Boost Technology 2.0 alkalmazásával akár 3,1 GHz-re is növelhető. A Dv2-sorozat ugyanolyan memória- és lemezkonfigurációkban érhető el, mint a D-sorozat.
* A G-sorozat virtuális gépei kínálják a legtöbb memóriát, és az Intel Xeon E5 V3 család processzorait tartalmazó gazdagépeken futnak.
* Az A sorozatú virtuális gépek különböző hardvertípusokra és processzorokra telepíthetők. A méret a hardver alapján szabályozott, hogy a futó példány konzisztens processzorteljesítményét nyújtsa, függetlenül attól, hogy milyen hardveren van telepítve. Az adott méretet futtató fizikai hardver meghatározásához kérdezze le a virtuális hardvert a virtuális gépen belülről.
* Az A0 méret esetében a fizikai hardvernek az ideálisnál több előfizetést kell kezelnie. Ennek az egy méretnek az esetében a többi felhasználói üzemelő példány befolyásolhatja a futó számítási feladat teljesítményét. A relatív teljesítmény várható alapértéke az alábbiak szerint alakul, hozzávetőleg 15 százalékos varianciával.

A virtuális gépek mérete befolyásolja az árképzést. A méret emellett hatással van a virtuális gép feldolgozási teljesítményére, valamint a memória- és tárhelykapacitására is. A tárolási díjak számítása ettől külön történik a tárfiókban használt oldalak mennyisége alapján. További információt a [Felhőszolgáltatások díjszabási részletei](https://azure.microsoft.com/pricing/details/cloud-services/) és [az Azure Storage-díjszabás című témakörben talál.](https://azure.microsoft.com/pricing/details/storage/)

Az alábbiak segíthetnek a megfelelő méret kiválasztásában:

* Az A8–A11- és a H-sorozat méretei más néven *nagy számítási igényű példányokként* ismertek. Az ezeket a méreteket futtató hardver a nagy számítási és hálózatigényű alkalmazások futtatására lett kialakítva és optimalizálva, ide értve a nagy teljesítményű feldolgozási (HPC) fürtalkalmazásokat, a modellezést és a szimulációkat. Az A8–A11-sorozat Intel Xeon E5-2670 @ 2,6 GHz-es, a H-sorozat pedig Intel Xeon E5-2667 v3 @ 3,2 GHz-es processzorokat használ. Ezek a méretek használatával kapcsolatos részletes információkért és szempontokért tekintse meg a [Nagy teljesítményű számítási virtuálisgépek méreteit.](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* A Dv3 sorozatú, Dv2-es, D-sorozatú, G-sorozatú alkalmazások ideálisak olyan alkalmazásokhoz, amelyek gyorsabb processzorokat, jobb helyi lemezteljesítményt vagy nagyobb memóriaigényt igényelnek. Nagyon hatékony kombinációt kínálnak számos nagyvállalati szintű alkalmazáshoz.
* Az Azure-központokban lévő némelyik fizikai gazdagép nem képes futtatni a nagyobb virtuálisgép-méreteket, például az A5–A11 méreteket. Ennek eredményeképpen a **(Nem sikerült) hibaüzenet a(z) {gépnév} vagy** a **(Nem sikerült) virtuálisgép létrehozása ({gépnév) nem sikerült egy** meglévő virtuális gép új méretre történő átméretezésekor. 2013. április 16.-a előtt létrehozott virtuális hálózatban új virtuális gép létrehozása; vagy új virtuális gép hozzáadása egy meglévő felhőszolgáltatáshoz. [Lásd: Hiba: "Nem sikerült konfigurálni](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) a virtuális gépet" a támogatási fórumon az egyes telepítési forgatókönyvek kerülő megoldásait.
* Az előfizetés is korlátozhatja az egyes családokban üzembe helyezhető magok mennyiségét. A kvóták növelésével kapcsolatban vegye fel a kapcsolatot az Azure ügyfélszolgálatával.

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások
Az Azure Compute Unit (ACU) koncepcióját azért hoztuk létre, hogy módot biztosítsunk a számítási (CPU) teljesítmény összehasonlítására az Azure Termékváltozatokban, és hogy meghatározzuk, mely termékváltozat oka a legnagyobb valószínűséggel elégíti ki a teljesítményigényeket.  Az ACU jelenlegi standard alapjaként a Kisméretű (Standard_A1) virtuális gép 100-as értéket képvisel, és a többi termékváltozat értéke ehhez képest jelöli, hogy mennyivel gyorsabban futtatja az adott termékváltozat a standard teljesítménytesztet.

> [!IMPORTANT]
> Az ACU csupán iránymutatóként szolgál. Az egyes számítási terhelések eredményei ettől eltérhetnek.
>
>

<br>

| Termékváltozat-család | ACU/mag |
| --- | --- |
| [ExtraKicsi](#a-series) |50 |
| [Kis-ExtraNagy](#a-series) |100 |
| [A5-7](#a-series) |100 |
| [A8-A11](#a-series) |225* |
| [A v2](#av2-series) |100 |
| [D](#d-series) |160 |
| [D v2](#dv2-series) |160 - 190* |
| [D v3](#dv3-series) |160 - 190* |
| [E v3](#ev3-series) |160 - 190* |
| [G](#g-series) |180 - 240* |
| [H](#h-series) |290 - 300* |

A * jelzésű ACU-értékek Intel® Turbo technológia használatával növelik a processzor órajelét, és ezáltal a teljesítményt. A növekedés mértéke a virtuális gép méretétől és terhelésétől, valamint az ugyanazon a gazdagépen futó számítási feladatoktól függően eltérő lehet.

## <a name="size-tables"></a>Mérettáblázatok
Az alábbi táblázatokban a méretek és azok kapacitásai láthatóak.

* A tárolókapacitás mértékegysége GiB (gibibájt = 1024^3 bájt). A gigabájtban (1000^3 bájt) és a gibibájtban (1024^3 bájt) mért meghajtók összehasonlításakor tartsa észben, hogy a GiB-ban kifejezett kapacitások kisebbnek tűnhetnek. Például: 1023 GiB = 1098,4 GB
* A lemezteljesítmény másodpercenkénti bemeneti/kimeneti műveletek (IOPS) mennyiségeként van kifejezve, valamint MBps-ben, ahol 1 MBps = 10^6 bájt/másodperc.
* Az adatlemezek gyorsítótárazott és gyorsítótárazás nélküli módban üzemelhetnek. Gyorsítótárazott adatlemezüzem esetében a gazdagép gyorsítótáras üzemmódja **ReadOnly** (Csak olvasás) vagy **ReadWrite** (Írás és olvasás) beállításra van konfigurálva. Gyorsítótárazás nélküli adatlemezüzem esetében a gazdagép gyorsítótáras üzemmódja **None** (Nincs) beállításra van konfigurálva.
* A maximális hálózati sávszélesség a virtuálisgép-típusonként kiosztott és hozzárendelt maximális összesített sávszélesség. A maximális sávszélesség iránymutatást nyújthat a megfelelő VM-típus kiválasztása során, hogy biztosítható legyen a megfelelő hálózati kapacitás rendelkezésre állása. Az Alacsony, Közepes, Magas és Nagyon Magas közötti váltáskor az átviteli feszültség ennek megfelelően növekszik. A tényleges hálózati teljesítmény számos tényezőtől függ, többek közt a hálózat és az alkalmazás terhelésétől, valamint az alkalmazás hálózati beállításaitól.

## <a name="a-series"></a>A-sorozat
| Méret            | Processzormagok | Memória: GiB  | Ideiglenes tárolás: GiB       | Hálózati adapterek max. száma/hálózati sávszélesség |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| ExtraKicsi      | 1         | 0.768        | 20                   | 1/alacsony |
| Kicsi           | 1         | 1.75         | 225                  | 1/közepes |
| Közepes          | 2         | 3.5          | 490                  | 1/közepes |
| Nagy           | 4         | 7            | 1000                 | 2/magas |
| ExtraNagy      | 8         | 14           | 2040                 | 4/magas |
| A5              | 2         | 14           | 490                  | 1/közepes |
| A6              | 4         | 28           | 1000                 | 2/magas |
| A7-              | 8         | 56           | 2040                 | 4/magas |

## <a name="a-series---compute-intensive-instances"></a>A-sorozat – nagy számítási igényű példányok
A méretek használatával kapcsolatos további tudnivalókért a [Nagy teljesítményű számítási virtuálisgépek méretei](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)című témakörben talál további információt.

| Méret            | Processzormagok | Memória: GiB  | Ideiglenes tárolás: GiB       | Hálózati adapterek max. száma/hálózati sávszélesség |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| A8*             |8          | 56           | 1817                 | 2/magas |
| A9- es, Nem hiszem, hogy ez jó             |16         | 112          | 1817                 | 4/nagyon magas |
| A10-as             |8          | 56           | 1817                 | 2/magas |
| A11             |16         | 112          | 1817                 | 4/nagyon magas |

\*RDMA képes

## <a name="av2-series"></a>Av2-sorozat

| Méret            | Processzormagok | Memória: GiB  | Ideiglenes tárolás (SSD): GiB       | Hálózati adapterek max. száma/hálózati sávszélesség |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_A1_v2  | 1         | 2            | 10                   | 1/közepes                 |
| Standard_A2_v2  | 2         | 4            | 20                   | 2/közepes                 |
| Standard_A4_v2  | 4         | 8            | 40                   | 4/magas                     |
| Standard_A8_v2  | 8         | 16           | 80                   | 8/magas                     |
| Standard_A2m_v2 | 2         | 16           | 20                   | 2/közepes                 |
| Standard_A4m_v2 | 4         | 32           | 40                   | 4/magas                     |
| Standard_A8m_v2 | 8         | 64           | 80                   | 8/magas                     |


## <a name="d-series"></a>D-sorozat
| Méret            | Processzormagok | Memória: GiB  | Ideiglenes tárolás (SSD): GiB       | Hálózati adapterek max. száma/hálózati sávszélesség |
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
| Méret            | Processzormagok | Memória: GiB  | Ideiglenes tárolás (SSD): GiB       | Hálózati adapterek max. száma/hálózati sávszélesség |
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

## <a name="dv3-series"></a>Dv3-sorozat

| Méret            | Processzormagok | Memória: GiB   | Ideiglenes tárolás (SSD): GiB       | Hálózati adapterek max. száma/hálózati sávszélesség |
|---------------- | --------- | ------------- | -------------------- | ---------------------------- |
| Standard_D2_v3  | 2         | 8             | 50                   | 2/közepes |
| Standard_D4_v3  | 4         | 16            | 100                  | 2/magas |
| Standard_D8_v3  | 8         | 32            | 200                  | 4/magas |
| Standard_D16_v3 | 16        | 64            | 400                  | 8/rendkívül magas |
| Standard_D32_v3 | 32        | 128           | 800                  | 8/rendkívül magas |
| Standard_D48_v3 | 48        | 192           | 1200                 | 8/rendkívül magas |
| Standard_D64_v3 | 64        | 256           | 1600                 | 8/rendkívül magas |

## <a name="ev3-series"></a>Ev3-sorozat

| Méret            | Processzormagok | Memória: GiB   | Ideiglenes tárolás (SSD): GiB       | Hálózati adapterek max. száma/hálózati sávszélesség |
|---------------- | --------- | ------------- | -------------------- | ---------------------------- |
| Standard_E2_v3  | 2         | 16            | 50                   | 2/közepes |
| Standard_E4_v3  | 4         | 32            | 100                  | 2/magas |
| Standard_E8_v3  | 8         | 64            | 200                  | 4/magas |
| Standard_E16_v3 | 16        | 128           | 400                  | 8/rendkívül magas |
| Standard_E32_v3 | 32        | 256           | 800                  | 8/rendkívül magas |
| Standard_E48_v3 | 48        | 384           | 1200                 | 8/rendkívül magas |
| Standard_E64_v3 | 64        | 432           | 1600                 | 8/rendkívül magas |


## <a name="g-series"></a>G-sorozat
| Méret            | Processzormagok | Memória: GiB  | Ideiglenes tárolás (SSD): GiB       | Hálózati adapterek max. száma/hálózati sávszélesség |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_G1     | 2         | 28           | 384                  |1/magas |
| Standard_G2     | 4         | 56           | 768                  |2/magas |
| Standard_G3     | 8         | 112          | 1,536                |4/nagyon magas |
| Standard_G4     | 16        | 224          | 3,072                |8/rendkívül magas |
| Standard_G5     | 32        | 448          | 6,144                |8/rendkívül magas |

## <a name="h-series"></a>H-sorozat
Az Azure H-sorozat virtuális gépei a nagy teljesítményű feldolgozásra képes virtuális gépek következő generációja, amelyek olyan magas szintű számítási igényekre lettek kialakítva, mint a molekuláris modellezés vagy a folyadékdinamikai számítások. Ezek a 8 és 16 magos virtuális gépek az Intel Haswell E5-2667 V3 processzortechnológiára épülnek, amely DDR4 memóriával és helyi SSD-alapú tárolóval rendelkezik.

A jelentős CPU-teljesítmény mellett a H-sorozat különféle lehetőségeket kínál a kis késésű RDMA-hálózatkezeléshez az FDR InfiniBand használatával, valamint számos memóriakonfigurációt is a memóriaigényes számítási követelmények támogatására.

| Méret            | Processzormagok | Memória: GiB  | Ideiglenes tárolás (SSD): GiB       | Hálózati adapterek max. száma/hálózati sávszélesség |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_H8     | 8         | 56           | 1000                 | 8/magas |
| Standard_H16    | 16        | 112          | 2000                 | 8/nagyon magas |
| Standard_H8m    | 8         | 112          | 1000                 | 8/magas |
| Standard_H16m   | 16        | 224          | 2000                 | 8/nagyon magas |
| Standard_H16r*  | 16        | 112          | 2000                 | 8/nagyon magas |
| Standard_H16mr* | 16        | 224          | 2000                 | 8/nagyon magas |

\*RDMA képes

## <a name="configure-sizes-for-cloud-services"></a>A felhőszolgáltatások méretének konfigurálása
A szerepkörpéldány virtuálisgép-méretét a [szolgáltatásdefiníciós fájl](cloud-services-model-and-package.md#csdef)által leírt szolgáltatásmodell részeként adhatja meg. A szerepkör mérete határozza meg a processzormagok számát, a memóriakapacitást és a helyi fájlrendszer méretét, amely et egy futó példányhoz lefoglalt. Válassza ki a szerepkör méretét az alkalmazás erőforrásigénye alapján.

Íme egy példa arra, hogy a szerepkör méretét Standard_D2 egy webes szerepkör-példányesetén:

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2">
...
</WorkerRole>
```

## <a name="changing-the-size-of-an-existing-role"></a>Meglévő szerepkör méretének módosítása

A számítási feladatok módosítása vagy az új virtuális gép mérete elérhetővé válik, érdemes lehet módosítani a szerepkör méretét. Ehhez módosítania kell a virtuális gép méretét a szolgáltatásdefiníciós fájlban (a fentiek szerint), újra kell csomagolnia a Felhőszolgáltatást, és telepítenie kell.

>[!TIP]
> Előfordulhat, hogy különböző virtuálisgép-méreteket szeretne használni a szerepkörhöz a különböző környezetekben (pl. teszt vs termelés). Ennek egyik módja, ha több szolgáltatásdefiníciós (.csdef) fájlt hoz létre a projektben, majd a CSPack eszköz segítségével az automatikus összeállítás során környezetenként különböző felhőszolgáltatási csomagokat hoz létre. Ha többet szeretne megtudni a felhőszolgáltatási csomag elemeiről és azok létrehozásáról, olvassa el [a Mi a felhőszolgáltatási modell, és hogyan csomagolhatom?](cloud-services-model-and-package.md)
>
>

## <a name="get-a-list-of-sizes"></a>A méretek listájának beépítése
A PowerShell vagy a REST API segítségével lehívhatja a méretek listáját. A REST API [itt](/previous-versions/azure/reference/dn469422(v=azure.100))van dokumentálva. A következő kód egy PowerShell-parancs, amely felsorolja a Felhőszolgáltatások számára elérhető összes méretet. 

```powershell
Get-AzureRoleSize | where SupportedByWebWorkerRoles -eq $true | select InstanceSize, RoleSizeLabel
```

## <a name="next-steps"></a>További lépések
* Ismerje meg [az Azure-előfizetések és -szolgáltatások korlátozásait, kvótáit és megkötéseit](../azure-resource-manager/management/azure-subscription-service-limits.md).
* További információ a HPC-számítási feladatok [nagy teljesítményű számítási virtuálisgép-méreteiről.](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)



