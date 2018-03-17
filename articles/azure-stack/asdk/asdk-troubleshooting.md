---
title: "A Microsoft Azure verem hibaelhárítása |} Microsoft Docs"
description: "Az Azure verem Development Kit (ASDK) hibaelhárítási információkat."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 6ed3fb7c7c4de9edbf31fb2c47290e2e39ceadcd
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="microsoft-azure-stack-development-kit-asdk-troubleshooting"></a>A Microsoft Azure verem Development Kit (ASDK) hibaelhárítása
Ez a dokumentum a ASDK általános hibaelhárítási információkat nyújt. Ha nem tárgyalt problémát tapasztalja, ellenőrizze, hogy a [Azure verem MSDN fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) további segítséget vagy arról információkat.  

> [!IMPORTANT]
> Mivel a ASDK egy kiértékelési környezet, nincs érhető el a Microsoft támogatja a szolgáltatások Ügyféltámogatási hivatalos támogatás.

Ebben a szakaszban ismertetett hibaelhárításával kapcsolatos ajánlások több forrásból származnak, és előfordulhat, hogy, vagy nem lehet feloldani az adott problémát. Kódminták "adott állapotban" kapnak, és nem lehet garantálni a kívánt eredmény elérése érdekében. Ez a szakasz az gyakori módosításokat és frissítések, a termék fejlesztései valósíthatók meg.

## <a name="deployment"></a>Környezet
### <a name="deployment-failure"></a>Központi telepítési problémái
Hiba a telepítés során tapasztal, indíthatja el a központi telepítést a sikertelen lépés használata a telepítési parancsfájl a - Újrafuttatás lehetőséget.  

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>A telepítés végén a PowerShell-munkamenet még meg nyitva, és nem jelenik meg a kimenetet
Ez a viselkedés esetén valószínűleg csak egy PowerShell-parancsablakban alapértelmezett viselkedése eredményét van kijelölve. A development kit központi telepítés sikeresen befejeződött, de a parancsfájl az ablak kiválasztásakor szünetel. Ellenőrizheti a telepítés befejeződött, a word keresi, "select" a parancs ablakának címsorában. Az ESC billentyű kikapcsolni azt, és az üzenet megjelenjenek-e után azt.

## <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
### <a name="default-image-and-gallery-item"></a>Alapértelmezett kép és a gyűjtemény elem
Virtuális gépek Azure-készletben telepítése előtt hozzá kell adni egy Windows Server kép és a galériabeli elemet.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>A saját Azure verem állomás az újraindítás után néhány virtuális gép esetleg nem indul el automatikusan.
Miután a gazdagép újraindul, azt tapasztalhatja, Azure verem szolgáltatások nem érhetők el azonnal. Ennek az az oka az Azure a verem [infrastruktúra virtuális gépek](asdk-architecture.md#virtual-machine-roles) RPs hajtsa végre a megfelelő néhány konzisztenciájának ellenőrzése időben, de végül indul el automatikusan.

Előfordulhat, hogy a bérlői virtuális gépek nem indul el automatikusan a rendszer az Azure verem development kit gazdagép újraindítása után is. Ez egy ismert probléma, és online állapotba kerüljön néhány manuális szükséges:

1.  Indítsa el az Azure verem development kit állomás **Feladatátvevőfürt-kezelő** a Start menüből.
2.  Válassza ki a fürtöt **S-Cluster.azurestack.local**.
3.  Válassza ki **szerepkörök**.
4.  Bérlői virtuális gépek szerepelnek a *mentett* állapotát. Miután minden infrastruktúra virtuális gépek futnak, kattintson a jobb gombbal a bérlői virtuális gépek, és válassza ki **Start** folytatni a virtuális Gépet.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Tudom néhány virtuális gépnél, hogy törölték, de továbbra is tekintse meg a VHD-fájlok a lemezen. Ez a viselkedés várható?
Igen, ez az elvárt viselkedés. Az ezzel a módszerrel úgy lett kialakítva, mert:

* Ha töröl egy virtuális Gépet, a VHD-k nem törlődnek. Lemezek külön erőforrások az erőforráscsoportban.
* A storage-fiók törlésekor a lekérdezi a törlés látható azonnal Azure Resource Manageren keresztül, de a lemezek tartalmazhat továbbra is őrizze meg tárolási szemétgyűjtés futtatásáig.

Ha azt látja, hogy "árva" VHD-k, fontos tudni, hogy ha a mappa a tárfiókon törölt részét képezik. A tárfiók nem lett törölve, akkor szokásos azok továbbra is létezik.

További az adatmegőrzési küszöbértékének és az igény visszaigénylését konfigurálásával kapcsolatos [storage-fiókok kezelése](.\.\azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Tárolás
### <a name="storage-reclamation"></a>Tárolási visszaigénylését
Megjelennek a portálon regenerált kapacitást 14 órát is igénybe vehet. Terület-visszanyerést attól függ, hogy számos tényező befolyásolja, többek között a használat százalékos belső tároló fájlok blokk blob-tárolóban. Ezért attól függően, hogy mennyi adat törlődik, nincs garancia a szemétgyűjtő futtatásakor kell visszaigényelt terület mennyisége.

## <a name="next-steps"></a>További lépések
[Látogasson el az Azure-verem támogatási fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)

