---
title: "Bevezetés az Azure-verem virtuális gépek"
description: "További tudnivalók a verem Azure virtuális gépek"
services: azure-stack
author: anjayajodha
ms.service: azure-stack
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: victorh
ms.openlocfilehash: 68da653052d0e3dfd66d6b65958046e42cefce73
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-stack-virtual-machines"></a>Bevezetés az Azure-verem virtuális gépek

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

## <a name="overview"></a>Áttekintés
Egy Azure verem virtuális gép (VM) egy típusú igény szerinti, méretezhető számítási erőforrások Azure verem kínál. Virtuális gépet általában akkor érdemes választani, ha a számítási környezet átfogóbb vezérlésére van szüksége annál, amelyet az egyéb lehetőségek kínálnak. Ez a cikk bemutatja, hogy mit kell szem előtt tartania egy virtuális gép létrehozása előtt, valamint hogy hogyan hozhatja létre és kezelheti azt.

Egy Azure verem virtuális gép szerződés keretein belül a virtualizáció anélkül, hogy egyes fürtök vagy gépek kezeléséhez. A virtuális gép karbantartásához azonban így is szükséges elvégeznie néhány feladatot, például a virtuális gépen futó szoftver konfigurálását, javítását és telepítését.

Az Azure verem virtuális gépek különböző módokon is használható. Példa:

* **Fejlesztési és tesztelési** – Azure verem virtuális gépek gyors kínálnak, és egyszerű módon, hogy hozzon létre egy számítógép adott konfigurációval szükséges kód és alkalmazás.

* **A felhőalapú alkalmazások** – az alkalmazás iránti igény is ingadozik, mert ésszerű gazdasági a verem Azure virtuális gép futtatásához. A további virtuális gépekért csak akkor kell fizetnie, amikor szüksége van rájuk, amikor pedig nincs, akkor leállíthatja őket.

* **Datacenter kiterjesztett** – egy Azure verem virtuális hálózatban lévő virtuális gépek egyszerűen csatlakozhat a szervezet hálózati vagy Azure.

Az alkalmazás által használt virtuális gépek száma az Ön igényei szerint skálázható.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Mit kell átgondolnom egy virtuális gép létrehozása előtt?

Mindig akadnak kialakítási szempontok számos ki egy Azure-készletben infrastruktúrát összeállításakor. A kezdés előtt a virtuális gépek következő tulajdonságait fontos átgondolni:

- Az alkalmazás erőforrásainak nevei
- A virtuális gép mérete
- A létrehozható virtuális gépek maximális száma
- A virtuális gépen futó operációs rendszer
- A virtuális gép indítás utáni konfigurációja 
- A virtuális gép által igényelt kapcsolódó erőforrások

### <a name="naming"></a>Elnevezés

A virtuális gépek számára kijelölt névvel rendelkezik, és a számítógép neve, az operációs rendszer részeként konfigurálva van. A virtuális gépek neve legfeljebb 15 karakter hosszú lehet.

Az operációsrendszer-lemez létrehozásához használt Azure-vermet, ha a számítógép és a virtuális gép nevét azonosak. Ha feltöltése és a korábban konfigurált operációs rendszert tartalmazó saját rendszerkép használata, és annak segítségével hozzon létre egy virtuális gépet, a neveket lehetnek. A saját képfájl feltöltésekor érdekében, hogy a számítógép az operációs rendszer, és a virtuális gép neve megegyezik az ajánlott eljárás.

### <a name="vm-size"></a>Virtuális gép mérete

A használt virtuális gép méretét, amely futtatja a munkaterhelés szerint határozza meg. A választott méret határoz meg olyan tényezőket, mint a feldolgozókapacitás, a memória és a tárolókapacitás. Az Azure verem számos különféle felhasználási módokhoz számos különböző méretet kínál.

### <a name="vm-limits"></a>Virtuális gép korlátok

Az előfizetés rendelkezik alapértelmezett kvótát, amely hatással lehet a projekthez sok virtuális gép telepítését. A jelenlegi határérték minden előfizetés esetében régiónként 20 virtuális gép.

### <a name="operating-system-disks-and-images"></a>Operációsrendszer-lemezek és -rendszerképek

A virtuális gépek virtuális merevlemezeken (VHD-k) tárolják az operációs rendszereket és az adatokat. A VHD-ken találhatók az operációs rendszer telepítéséhez kiválasztható rendszerképek is.
Azure verem használható különböző verziói és operációs rendszerek piactéren biztosít. A piactérről származó rendszerképek azonosítása a rendszerkép közzétevője, ajánlat, termékváltozat és verzió alapján lehetséges (a verzió általában mint „legfrissebb” van megadva).

Az alábbi táblázat néhány módját, hogy a kép adatai található:


|Módszer|Leírás|
|---------|---------|
|Verem Azure portálon|Az értékek a rendszerképek kiválasztásakor automatikusan megjelennek.|
|Azure Stack PowerShell|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|REST API-k     |[Rendszerkép-közzétevők listázása](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<br>[Rendszerkép-ajánlatok listázása](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[Lista kép termékváltozatok](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

Ha szeretné, töltse fel, és saját rendszerkép használata. Ha így tesz, a közzétevő neve, az ajánlat és a termékváltozat nem használható.

### <a name="extensions"></a>Bővítmények

Virtuálisgép-bővítmények a feladás egy vagy több központi telepítési konfigurációs és automatikus feladatokat a virtuális gép további képességeket biztosítják.
A bővítményekkel a következő gyakori feladatok végezhetők el:

* Egyéni parancsfájlok – futtassa az egyéni parancsprogramok futtatására szolgáló bővítmény segít munkaterhelések konfigurálja a virtuális Gépen a parancsfájl futtatásával, amikor a virtuális gép ki van építve.
* Telepíthetnek és kezelhetnek olyan konfigurációk – a PowerShell kívánt állapot konfigurációs szolgáltatása (DSC) kiterjesztés segítséget nyújt a virtuális gép DSC beállítása konfigurációk és a környezeteket.
* A gyűjtés diagnosztikai adatainak – az Azure Diagnostics bővítmény segít konfigurálja a virtuális gép diagnosztikai adatokat gyűjthet, amelyek az alkalmazás állapotának figyelésére használható.

### <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)

Az erőforrások az alábbi táblázat a virtuális gép által használt, és vagy a virtuális gép létrehozásakor létrehozása léteznie kell.


|Erőforrás|Kötelező|Leírás|
|---------|---------|---------|
|Erőforráscsoport|Igen|A virtuális gépnek egy erőforráscsoport tagjának kell lennie.|
|Tárfiók|Igen|A virtuális gépnek szüksége van egy tárfiókra a virtuális merevlemezek tárolásához.|
|Virtuális hálózat|Igen|A virtuális gépnek egy virtuális hálózat tagjának kell lennie.|
|Nyilvános IP-cím|Nem|A virtuális gép rendelkezhet hozzárendelt nyilvános IP-címmel, hogy távolról is el lehessen érni.|
|Hálózati illesztő|Igen|A virtuális gépnek szüksége van a hálózati illesztőre a hálózattal való kommunikációhoz.|
|Az adatlemezek|Nem|A virtuális gépek a tárolókapacitást bővítő adatlemezeket is tartalmazhatnak.|

## <a name="how-do-i-create-my-first-vm"></a>Hogyan hozhatom létre az első virtuális gépemet?

Virtuális gép létrehozása több lehetősége van. A kiválasztott környezettől függ.
A következő táblázat információkat az első lépések a virtuális gép létrehozása.


|Módszer|Cikk|
|---------|---------|
|Verem Azure portálon|Windows rendszerű virtuális gép létrehozása a verem Azure portállal<br>[A verem Azure portál használatával Linux virtuális gép létrehozása](azure-stack-quick-linux-portal.md)|
|Sablonok|Az Azure verem gyorsindítási sablonok találhatók:<br> [https://github.com/Azure/AzureStack-quickstart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates)|
|PowerShell|[Windows virtuális gép létrehozása Azure-készletben a PowerShell használatával](azure-stack-quick-create-vm-windows-powershell.md)<br>[Linux virtuális gép létrehozása Azure-készletben a PowerShell használatával](azure-stack-quick-create-vm-linux-powershell.md)|
|parancssori felület|[Windows virtuális gép létrehozása Azure-készletben parancssori felület használatával](azure-stack-quick-create-vm-windows-cli.md)<br>[Linux virtuális gép létrehozása Azure-készletben parancssori felület használatával](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="how-do-i-manage-the-vm-that-i-created"></a>Hogyan kezelhetem a létrehozott virtuális gépet?

A virtuális gépek felügyelhetők egy böngészőalapú portállal, parancsfájlkezelést támogató parancssori eszközökkel, vagy közvetlenül az API-kon keresztül. A tipikus kezelési feladatok közé tartozik a virtuális gépek adatainak lekérése, a virtuális gépekre való bejelentkezés, a rendelkezésre állás kezelése és a biztonsági mentések készítése.

### <a name="get-information-about-a-vm"></a>Virtuális gép adatainak lekérése

A következő táblázat néhány virtuális gép információt kaphat módot.


|Módszer|Leírás|
|---------|---------|
|Verem Azure portálon|A központ menüben kattintson a virtuális gépeket, és válassza ki a virtuális Gépet a listából. A virtuális gép lapon hozzáférhet áttekintő információkat, a beállítás értéke, és figyelési metrikákat.|
|Azure PowerShell|Virtuális gépek kezelése az Azure-ban és Azure verem hasonló. További információ a PowerShell használatával a következő témakör Azure:<br>[Hozzon létre és a Windows virtuális gépek kezelése az Azure PowerShell modul](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes)|
|Ügyfél-SDK-k|A C# használatával felügyeli a virtuális gépek az Azure-ban és Azure verem hasonló. További információ a következő témakör Azure:<br>[Létrehozása és kezelése Windows-alapú virtuális gépek az Azure-ban C#](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/csharp)|

### <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

Használhatja a **Connect** a verem Azure portál csatlakozni a virtuális gép gombjára.

## <a name="next-steps"></a>Következő lépések
* [Virtuális gépek Azure-készletben szempontjai](azure-stack-vm-considerations.md)

