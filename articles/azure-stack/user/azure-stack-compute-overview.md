---
title: Bevezetés az Azure Stack-alapú virtuális gépekbe
description: Azure Stack virtual machines bemutatása
services: azure-stack
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.topic: get-started-article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: kivenkat
ms.openlocfilehash: d478ccd0895ad067657bce56469a3a61d4ea0e17
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139540"
---
# <a name="introduction-to-azure-stack-virtual-machines"></a>Bevezetés az Azure Stack-alapú virtuális gépekbe

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack kínál megoldást a virtuális gépek (VM) egy igény szerinti, méretezhető számítási erőforrás egy típusa. Választhat egy virtuális Gépet, amikor szüksége van a számítási környezet jobban szabályozhatja, mint az egyéb lehetőségek. Ez a cikk részletesen a virtuális gép létrehozása előtt.

Azure Stack virtuális gép rugalmasságot biztosít a virtualization fürtök vagy az egyes gépek kezelése nélkül. Azonban akkor is szükséges műveleteket, mint a konfigurálását, javítását és a rajta futó szoftver telepítése a virtuális gép karbantartásához.

Azure Stack virtuális gépek különféle módon használhatja. Példa:

- **Fejlesztés és tesztelés**  
    Az Azure Stack-beli virtuális gépek egy számítógép létrehozásához szükséges kódot adott konfigurációval, és az alkalmazás tesztelése gyors és egyszerű lehetőséget biztosít.

- **Alkalmazásokat a felhőben**  
    Mivel az alkalmazások iránti igény ingadozhaz, célszerű gazdasági virtuális gépen az Azure Stack futtatásához. A további virtuális gépekért csak akkor kell fizetnie, amikor szüksége van rájuk, amikor pedig nincs, akkor leállíthatja őket.

- **Bővített adatközpont**  
    Az Azure Stack virtuális hálózatban a virtuális gépek könnyedén összekapcsolhatók a vállalati hálózaton vagy az Azure-bA.

A virtuális gépek, amelyek a kérelem által használt vertikális vagy horizontális felskálázás függetlenül szükség az igényeinek.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Mit kell átgondolnom egy virtuális gép létrehozása előtt?

Nincsenek mindig számos tervezési szempontok alkalmazás-infrastruktúrák, az Azure Stackben készítése során. Virtuális gépek következő tulajdonságait fontos átgondolni, az infrastruktúra létrehozásának megkezdése előtt:

- Az alkalmazás erőforrásainak nevei.
- A virtuális gép méretét.
- A létrehozható virtuális gépek maximális számát.
- A virtuális Gépen futó operációs rendszert.
- A konfiguráció a virtuális gép elindítása után.
- A kapcsolódó erőforrásokat, a virtuális gépet.

### <a name="naming"></a>Elnevezés

Egy virtuális gép rendelkezik egy névvel, és a számítógép nevét, az operációs rendszer részeként konfigurálva van. A virtuális gépek neve legfeljebb 15 karakter hosszú lehet.

Azure Stack használatával az operációsrendszer-lemez létrehozása, ha a számítógép nevét és a virtuális gép neve azonosak. Ha feltöltése és a saját egy korábban konfigurált operációs rendszert tartalmazó lemezképek használata, és ezzel hozzon létre egy virtuális gépet, a neveket eltérő lehet. Ha saját rendszerképet fájlt tölt fel, végezze el az operációs rendszer a számítógép nevét, és a virtuális gép neve ugyanaz, mint az ajánlott eljárás.

### <a name="vm-size"></a>Virtuális gép mérete

A használt virtuális gép méretét a számítási feladat, amelyet futtatni szeretne határozza meg. A választott méret határoz meg olyan tényezőket, mint a feldolgozókapacitás, a memória és a tárolókapacitás. Az Azure stackhez számos különböző méretet különféle felhasználási módokhoz.

### <a name="vm-limits"></a>A virtuális gépekre korlátai

Az előfizetés alapértelmezett kvótakorlátozások vonatkoznak, amely hatással lehet a projektjéhez nagy számú virtuális gépet a központi telepítés rendelkezik. A jelenlegi határérték minden előfizetés esetében régiónként 20 virtuális gép.

### <a name="operating-system-disks-and-images"></a>Operációsrendszer-lemezek és -rendszerképek

A virtuális gépek virtuális merevlemezeken (VHD-k) tárolják az operációs rendszereket és az adatokat. A VHD-ken találhatók az operációs rendszer telepítéséhez kiválasztható rendszerképek is.
Az Azure Stack biztosít különböző verzióit és típusait az operációs rendszerek használata a piactéren. Marketplace-rendszerképek azonosítják a rendszerkép közzétevője, ajánlat, termékváltozat és verzió (általában verzió van megadva, a legújabb.)

Az alábbi táblázatban bemutat néhány módszert, hogy a lemezképre vonatkozó információkat talál:

|Módszer|Leírás|
|---------|---------|
|Az Azure Stack portálon|Az értékek a rendszerképek kiválasztásakor automatikusan megjelennek.|
|Azure Stack PowerShell|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|REST API-k     |[Rendszerkép-közzétevők listázása](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<br>[Rendszerkép-ajánlatok listázása](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[Kép termékváltozatok listázása](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

Kiválaszthatja, hogy fel és saját rendszerképét használja. Ha így tesz, a közzétevő neve, az ajánlat és a termékváltozat nem használható.

### <a name="extensions"></a>Bővítmények

A Virtuálisgép-bővítmények lehetővé teszik a virtuális gép további képességeket üzembe helyezést követő konfigurálás és automatizált feladatok útján.
A bővítményekkel a következő gyakori feladatok végezhetők el:

- **Egyéni szkriptek futtatása**  
    Az egyéni Szkriptbővítmény segítségével a virtuális gép számítási feladatainak konfigurálásában a parancsfájl futtatásával, ha a virtuális gép ki van építve.

- **Üzembe helyezés és kezelés konfigurációk**  
    A PowerShell Desired State Configuration (DSC) bővítmény segítségével állítsa be a virtuális gép DSC konfigurációk és környezetek kezeléséhez.

- **Diagnosztikai adatok gyűjtése**  
    Az Azure Diagnostics bővítmény segítségével konfigurálhatja az alkalmazás állapotának figyelésére használható diagnosztikai adatok gyűjtésére a virtuális Gépet.

### <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)

Az alábbi táblázatban az erőforrásokat a virtuális gép által használt, és létezik, vagy hozható létre a virtuális gép létrehozásakor kell.


|Erőforrás|Szükséges|Leírás|
|---------|---------|---------|
|Erőforráscsoport|Igen|A virtuális gépnek egy erőforráscsoport tagjának kell lennie.|
|Tárfiók|Igen|A virtuális gépnek szüksége van egy tárfiókra a virtuális merevlemezek tárolásához.|
|Virtuális hálózat|Igen|A virtuális gépnek egy virtuális hálózat tagjának kell lennie.|
|Nyilvános IP-cím|Nem|A virtuális gép rendelkezhet hozzárendelt nyilvános IP-címmel, hogy távolról is el lehessen érni.|
|Hálózati illesztő|Igen|A virtuális gépnek szüksége van a hálózati illesztőre a hálózattal való kommunikációhoz.|
|Adatlemezek|Nem|A virtuális gépek a tárolókapacitást bővítő adatlemezeket is tartalmazhatnak.|

## <a name="create-your-first-vm"></a>Az első virtuális gép létrehozása

Lehetősége van több virtuális gép létrehozásához. A választott környezettől függ.
A következő táblázat információkat az első lépések a virtuális gép létrehozása.


|Módszer|Cikk|
|---------|---------|
|Az Azure Stack portálon|Egy Windows virtuális gép létrehozása az Azure Stack portálon<br>[Az Azure Stack portal használata Linux rendszerű virtuális gép létrehozása](azure-stack-quick-linux-portal.md)|
|Sablonok|Az Azure Stack-gyorsindítási sablonok a következő helyen találhatók:<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates)|
|PowerShell|[Windows virtuális gép létrehozása az Azure Stack PowerShell használatával](azure-stack-quick-create-vm-windows-powershell.md)<br>[Linux rendszerű virtuális gép létrehozása az Azure Stack PowerShell használatával](azure-stack-quick-create-vm-linux-powershell.md)|
|parancssori felület|[Windows virtuális gép létrehozása az Azure Stack parancssori felület használatával](azure-stack-quick-create-vm-windows-cli.md)<br>[Linux rendszerű virtuális gép létrehozása az Azure Stack parancssori felület használatával](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="manage-your-vm"></a>A virtuális gép kezelése

Virtuális gépek egy böngészőalapú portállal, parancsfájlkezelést, vagy közvetlenül az API-kon keresztül támogató parancssori eszközökkel használata kezelheti. Néhány tipikus felügyeleti feladatokhoz, előfordulhat, hogy végre a következők:

- Virtuális gép adatainak beolvasása
- Csatlakozás virtuális géphez
- Rendelkezésre állás kezelése
- Biztonsági mentések készítése

### <a name="get-information-about-your-vm"></a>A virtuális gép adatainak lekérése

Az alábbi táblázat bemutatja, egy virtuális gép információkat szerezhet a több szempontból.


|Módszer|Leírás|
|---------|---------|
|Az Azure Stack portálon|A központi menüben kattintson a virtuális gépeket, és válassza ki a virtuális Gépet a listából. A lapon a virtuális gép elérhető lesz az információk áttekintését, értékek beállításait és a mérőszámok figyelését.|
|Azure PowerShell|Virtuális gépek kezelése a hasonló az Azure és az Azure Stackben. PowerShell-lel kapcsolatos további információkért lásd a következő Azure témakör:<br>[Hozzon létre és kezelheti a Windows virtuális gépek az Azure PowerShell modullal](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes)|
|Ügyfél-SDK-k|A virtuális gépek kezelése a C# használatával hasonlít az Azure és az Azure Stackben. További információkért tekintse meg a következő Azure témakör:<br>[Létrehozása és kezelése Windows virtuális gépek az Azure-ban C# használatával](https://docs.microsoft.com/azure/virtual-machines/windows/csharp)|

### <a name="connect-to-your-vm"></a>Csatlakozzon a virtuális Géphez

Használhatja a **Connect** gomb az Azure Stack portálon a virtuális Géphez való csatlakozáshoz.

## <a name="next-steps"></a>További lépések

- [Szempontok a virtuális gépek az Azure Stackben](azure-stack-vm-considerations.md)