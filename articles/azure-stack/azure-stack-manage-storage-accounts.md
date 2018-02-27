---
title: "A verem Azure storage-fiókok kezelése |} Microsoft Docs"
description: "Megtudhatja, hogyan keresése, kezelése, helyreállítás és VISSZAIGÉNYLÉSE verem Azure storage-fiókok"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 627d355b-4812-45cb-bc1e-ce62476dab34
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/6/2017
ms.author: brenduns
ms.reviewer: anirudha
ms.openlocfilehash: 3ef9a66095d0ed5fc865dc3c22961f9f7bdcedd9
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="manage-storage-accounts-in-azure-stack"></a>A verem az Azure Storage-fiókok kezelése
Útmutató: Azure-készletben található, helyreállítására és az üzleti igények alapján tárolási kapacitás visszaigényléséhez storage-fiókok kezelése.

## <a name="find"></a>A storage-fiók található
A listában tárfiókok régióban Azure verem által tekintheti meg:

1. Egy webböngészőben nyissa meg a https://adminportal.local.azurestack.external.
2. Jelentkezzen be a verem Azure felügyeleti portálon, a felhő üzemeltetője (a telepítés során megadott hitelesítő adatok használata)
3. Keresse meg az alapértelmezett irányítópult – a **régió felügyeleti** listán, és kattintson a régiót szeretné használni. Például **(helyi**).
   
   ![](media/azure-stack-manage-storage-accounts/image1.png)
4. Válassza ki **tárolási** a a **erőforrás-szolgáltató** listája.
   
   ![](media/azure-stack-manage-storage-accounts/image2.png)
5. Most, a rendszergazda panelen storage erőforrás-szolgáltató – görgessen le a **tárfiókok** fülre, és kattintson rá.
   
   ![](media/azure-stack-manage-storage-accounts/image3.png)
   
   Az eredményül kapott lap a listában tárfiókok az adott régióban.
   
   ![](media/azure-stack-manage-storage-accounts/image4.png)

Alapértelmezés szerint az első 10 fiókok jelennek meg. Dönthet úgy, több beolvasása kattintva a **betöltése több** hivatkozás a lista alján.

VAGY

Ha érdekli az adott tárfiókok – **szűrésére és a megfelelő fiókok fetch** csak.


**A fiókok szűrése:**

1. Kattintson a **szűrő** a panel tetején.
2. A szűrő panelre, akkor megadhatja **fióknév**, **előfizetés-azonosító** vagy **állapot** finomhangolását a megjelenítendő storage-fiókok listája. Használja őket.
3. Kattintson a **frissítés**. A lista ennek megfelelően kell frissíteni.
   
    ![](media/azure-stack-manage-storage-accounts/image5.png)
4. A szűrő alaphelyzetbe: kattintson a **szűrő**, törölje ki a beállításokat, és frissítését.

A keresőmezőben (tetején a tárolás fiókok listája panel) lehetővé teszi a kijelölt szöveg fiókok listájában jelölje ki. Ez az valóban lesz szüksége, abban az esetben, ha a teljes név vagy azonosító nem lennének könnyen elérhetőek.

Itt szabad szöveg segítségével keresse meg a fiókot szeretné.

![](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Nézze meg fiókadatok
Miután megtalálta a fiókok megtekintése érdekli, kattintson az adott fiók bizonyos a részletek megtekintéséhez. Egy új panel nyílik meg a fiók adatait, mint: a típusa, a fiók létrehozásának idejét, hely, stb.

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Helyreállítás törölt fiókhoz
Lehet, olyan helyzet, ahol kell helyreállítani egy törölt fiókhoz.

Azure-készletben egy nagyon egyszerű módja van:

1. Tallózással keresse meg a storage-fiókok listáján. Lásd: [található a tárfiók](#find) című témakörben talál további információt.
2. Keresse meg az adott fiók a listában. Szükség lehet szűrni.
3. Ellenőrizze a *állapot* fiók. Üzenetnek kell megjelennie **törölt**.
4. Kattintson a fiók, amely a fiók részletei panel nyílik meg.
5. Fölött ezen a panelen keresse meg a **helyreállítása** gombra, majd kattintson rá.
6. Kattintson a **Yes** (Igen) gombra a megerősítéshez.
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. A helyreállítási jelenleg *feldolgozása... várakozási* az arra utal, hogy sikeres volt-e azt.
   A portálra, ahol megtekintheti a folyamatban lévő jelzések tetején a "harang" ikonra is kattinthat.
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   Amikor a helyreállított fiók sikeresen szinkronizálta, azt az használható újra.

### <a name="some-gotchas"></a>Néhány Gotchas
* A törölt fiók állapota látható **kívül megőrzési**.
  
  Ez azt jelenti, hogy a törölt fiók túllépte a megőrzési idő, és nem használható helyreállításhoz.
* A törölt fiókhoz nem szerepelnek a fiókok listájában.
  
  Ez azt jelentheti, hogy a törölt fiók szemétgyűjtő már megtörtént. Ebben az esetben nem állíthatók vissza. Lásd: [kapacitás visszaigényléséhez](#reclaim) ebben a témakörben.

## <a name="set-the-retention-period"></a>A megőrzési időszak beállítása
A megőrzési időszak beállítása lehetővé teszi, hogy a felhő üzemeltetője Ha meg szeretné adni egy adott időszakban között eltelt napok (0 és 9999 nap) amely során egy törölt fiókhoz potenciálisan állíthatók helyre. A megőrzési idő van beállítva, és 15 nap. Az érték "0" azt jelenti, hogy a törölt fiók azonnal megőrzési kívül esik, és rendszeres szemétgyűjtési.

**A megőrzési időtartam módosítása:**

1. Egy webböngészőben nyissa meg a https://adminportal.local.azurestack.external.
2. Jelentkezzen be a verem Azure felügyeleti portálon, a felhő üzemeltetője (a telepítés során megadott hitelesítő adatok használata)
3. Keresse meg az alapértelmezett irányítópult – a **régió felügyeleti** listán, és kattintson a felfedezés – például kívánt régiót **(helyi**).
4. Válassza ki **tárolási** a a **erőforrás-szolgáltató** listája.
5. Kattintson a **beállítások** felső beállítás panel megnyitásához.
6. Kattintson a **konfigurációs** szerkessze az adatmegőrzési időszak értéket.

   Állítsa be a napok számát, és mentse azt.
   
   Ez az érték azonnal hatékonyan, és a teljes régió van beállítva.

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Kapacitás visszaigényléséhez
A mellékhatással működő, a megőrzési időszak egyike, hogy a fiók továbbra is kapacitás felhasználását, amíg a megőrzési időn kívül az ismét. A felhő felelősnek szükség lehet egy módszerre, amellyel a törölt fiók jusson, annak ellenére, hogy a megőrzési időszak még nem járt.

Kapacitás, a portál vagy a PowerShell használatával is visszaigényléséhez.

**A portál használatával kapacitás visszaigényléséhez:**
1. Nyissa meg a storage-fiókok panelen. Lásd: [található a tárfiók](#find).
2. Kattintson a **jusson** a panel tetején.
3. Olvassa el az üzenetet, és kattintson a **OK**.

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. Várjon, amíg sikeres notification tekintse meg a portál a harang ikonra.

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. Frissítse a Storage-fiókok lapot. A törölt fiókok már nem jelennek meg a listában, mert azok kiürítése megtörtént.

A megőrzési időtartam explicit módon felülbírálására PowerShell is használható, és azonnal a kapacitás visszaigényléséhez.

**Kapacitás visszaigényléséhez PowerShell használatával:**   

1. Győződjön meg arról, hogy rendelkezik-e az Azure PowerShell telepítése és konfigurálása. Ha nem, kövesse az alábbi utasításokat: 
   * Telepítse a legújabb Azure PowerShell-verziót, és társítsa azt az Azure-előfizetése, [telepítése és konfigurálása az Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/).
   Azure Resource Manager parancsmagjainak kapcsolatos további információkért lásd: [az Azure PowerShell használata Azure Resource Managerrel](http://go.microsoft.com/fwlink/?LinkId=394767)
2. Futtassa a következő parancsmagot:

> [!NOTE]
> Ez a parancsmag futtatásakor véglegesen törli a fiókot, és annak tartalmát. Nincs helyreállítható. Ez körültekintően használja.


        Clear-ACSStorageAccount -ResourceGroupName system.local -FarmName <farm ID>


További részletekért tekintse meg a [Azure verem powershell dokumentációját.](https://msdn.microsoft.com/library/mt637964.aspx)
 

## <a name="migrate-a-container"></a>A tároló áttelepítése
Bérlők egyenetlen tárolóhely-használat, mert a felhő üzemeltetője lehet található ilyen, vagy több alapul szolgáló bérlői közösen használja, mint mások több helyet használ. Ha ez történik, a felhő üzemeltetője megpróbálhatja szabadítson fel helyet a teherhordó megosztáson manuálisan át kell telepítenie néhány blob-tárolók egy másik megosztásba. 

Tárolók áttelepítése PowerShell kell használnia.
> [!NOTE]
>A BLOB-tároló áttelepítése nem támogatja az élő áttelepítést, és jelenleg offline művelet. Az áttelepítés során, és mindaddig, amíg nem fejeződik be az alapul szolgáló BLOB a tárolóban nem használható, és a "kapcsolat nélküli". 

**PowerShell-lel tárolók áttelepítéséhez:**

1. Győződjön meg arról, hogy rendelkezik-e az Azure PowerShell telepítése és konfigurálása. Ha nem, kövesse az alábbi utasításokat:
    * Telepítse a legújabb Azure PowerShell-verziót, és társítsa azt az Azure-előfizetése, [telepítése és konfigurálása az Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/). Azure Resource Manager parancsmagjainak kapcsolatos további információkért lásd: [az Azure PowerShell használata Azure Resource Managerrel](http://go.microsoft.com/fwlink/?LinkId=394767)
2. Töltse le a farm neve: 
      
      `$farm = Get-ACSFarm -ResourceGroupName system.local`
3. Töltse le a megosztások: 

   `$shares = Get-ACSShare -ResourceGroupName system.local -FarmName $farm.FarmName`

4. A tárolók beolvasása a megadott megosztás. Vegye figyelembe, hogy száma és a leképezés-e a választható paraméterek:
            
   `$containers = Get-ACSContainer -ResourceGroupName system.local -FarmName $farm.FarmName -ShareName $shares[0].ShareName -Count 4 -Intent Migration`  

   Vizsgálja meg $containers:

   `$containers`

    ![](media/azure-stack-manage-storage-accounts/image13.png)
5. A legjobb cél megosztások érhető el a tároló áttelepítése:

    `$destinationshares= Get-ACSSharesForMigration  -ResourceGroupName system.local -FarmName $farm.farmname -SourceShareName $shares[0].ShareName`

    Vizsgálja meg $destinationshares:

    `$destinationshares`

    ![](media/azure-stack-manage-storage-accounts/image14.png)
6. Egy tároló áttelepítési indítsa, figyelje meg, ez a helyzet aszinkron implementációja, egy olyan megosztáson található összes tárolók hurokba, és nyomon követheti a állapotát, a visszaadott feladat azonosítójával.

    `$jobId = Start-ACSContainerMigration -ResourceGroupName system.local -FarmName $farm.farmname -ContainerToMigrate $containers[1] -DestinationShareUncPath $destinationshares.UncPath`

    Vizsgálja meg $jobId:

   ```
   $jobId
   d1d5277f-6b8d-4923-9db3-8bb00fa61b65
   ```
7. Az áttelepítési feladat állapotának ellenőrzése a feladat azonosítóval. A tároló áttelepítésének befejezése után MigrationStatus "Kész" értékre van beállítva.

    `Get-ACSContainerMigrationStatus -ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId`

    ![](media/azure-stack-manage-storage-accounts/image15.png)

8. Egy folyamatban lévő áttelepítési feladat megszakítása Ez újra aszinkron művelet, és a $jobid használatával követhető nyomon:

    `Stop-ACSContainerMigration-ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId-Verbose`

    ![](media/azure-stack-manage-storage-accounts/image16.png)

    Az áttelepítés Mégse állapotának újra ellenőrizheti:

    `Get-ACSContainerMigrationStatus-ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId`

    ![](media/azure-stack-manage-storage-accounts/image17.png)




  
  