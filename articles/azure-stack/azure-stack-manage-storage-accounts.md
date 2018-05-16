---
title: A verem Azure storage-fiókok kezelése |} Microsoft Docs
description: Megtudhatja, hogyan keresése, kezelése, helyreállítás és VISSZAIGÉNYLÉSE verem Azure storage-fiókok
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 627d355b-4812-45cb-bc1e-ce62476dab34
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: 2ae2b628b2e61893a5289151c3b405e7412e7d13
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
---
# <a name="manage-storage-accounts-in-azure-stack"></a>A verem Azure storage-fiókok kezelése
Útmutató: Azure-készletben található, helyreállítására és az üzleti igények alapján tárolási kapacitás visszaigényléséhez storage-fiókok kezelése.

## <a name="find"></a>A storage-fiók található
A listában tárfiókok régióban Azure verem által tekintheti meg:

1. A böngészőben, keresse meg https://adminportal.local.azurestack.external.
2. Jelentkezzen be a verem Azure felügyeleti portálon, a felhő üzemeltetője (a telepítés során megadott hitelesítő adatok használata)
3. Keresse meg az alapértelmezett irányítópult – a **régió felügyeleti** listában, és válassza ki azt a régiót szeretné használni, például **(helyi**).
   
   ![](media/azure-stack-manage-storage-accounts/image1.png)
4. Válassza ki **tárolási** a a **erőforrás-szolgáltató** listája.
   
   ![](media/azure-stack-manage-storage-accounts/image2.png)
5. Most, a rendszergazda panelen storage erőforrás-szolgáltató – görgessen le a **tárfiókok** fülre, és válassza ki azt.
   
   ![](media/azure-stack-manage-storage-accounts/image3.png)
   
   Az eredményül kapott lap a listában tárfiókok az adott régióban.
   
   ![](media/azure-stack-manage-storage-accounts/image4.png)

Alapértelmezés szerint az első 10 fiókok jelennek meg. Dönthet úgy, több beolvasása kattintva a **betöltése több** hivatkozás a lista alján.

VAGY

Ha érdekli az adott tárfiókok – **szűrésére és a megfelelő fiókok fetch** csak.


**A fiókok szűrése:**

1. Válassza ki **szűrő** , ha a panel tetején.
2. A szűrő panelen, akkor megadhatja **fióknév**, ** előfizetés-azonosító, vagy **állapot** finomhangolását a megjelenítendő storage-fiókok listája. Használja őket.
3. Válassza ki **frissítés**. A lista ennek megfelelően kell frissíteni.
   
    ![](media/azure-stack-manage-storage-accounts/image5.png)
4. A szűrő alaphelyzetbe: válasszon **szűrő**, törölje ki a beállításokat, és frissítését.

A keresőmezőben (tetején a tárolási fiók lista ablaktáblájában) lehetővé teszi a kijelölt szöveg fiókok listájában jelölje ki. Ezzel a teljes név vagy azonosító nem könnyen elérhető esetén.

Itt szabad szöveg segítségével keresse meg a fiókot szeretné.

![](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Nézze meg fiókadatok
Miután megtalálta a fiókok megtekintése érdekli, megadhatja az adott fiókot bizonyos a részletek megtekintéséhez. Egy új panelen nyitja meg a fiók adatait, mint: a típusa, a fiók létrehozásának idejét, hely, stb.

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Helyreállítás törölt fiókhoz
Lehet, olyan helyzet, ahol kell helyreállítani egy törölt fiókhoz.

Azure-készletben egy egyszerű módja van:

1. Tallózással keresse meg a storage-fiókok listáján. Lásd: [található a tárfiók](#find) című témakörben talál további információt.
2. Keresse meg az adott fiók a listában. Szükség lehet szűrni.
3. Ellenőrizze a *állapot* fiók. Üzenetnek kell megjelennie **törölt**.
4. Válassza ki a fiókot, amely a fiók részletei ablaktábla megnyitása.
5. Fölött ezen az ablaktáblán keresse meg a **helyreállítása** gombra, majd válassza ki azt.
6. Válassza ki **Igen** megerősítéséhez.
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. A helyreállítási jelenleg *feldolgozása... várakozási* az arra utal, hogy sikeres volt-e azt.
   Igény szerint kiválaszthatja a portálra, ahol megtekintheti a folyamatban lévő jelzések tetején a "harang" ikonra.
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   Amikor a helyreállított fiók sikeresen szinkronizálta, azt az használható újra.

### <a name="some-gotchas"></a>Néhány Gotchas
* A törölt fiók állapota látható **kívül megőrzési**.
  
  Kívül megőrzési azt jelenti, hogy a törölt fiók túllépte a megőrzési idő, és nem használható helyreállításhoz.
* A törölt fiókhoz nem szerepelnek a fiókok listájában.
  
  Ha a törölt fiókhoz már megtörtént a szemétgyűjtő meg a fiók nem jelenik a fiókok listáján. Ebben az esetben nem állíthatók vissza. Lásd: [kapacitás visszaigényléséhez](#reclaim) ebben a témakörben.

## <a name="set-the-retention-period"></a>A megőrzési időszak beállítása
A megőrzési időszak beállítása lehetővé teszi, hogy a felhő üzemeltetője Ha meg szeretné adni egy adott időszakban között eltelt napok (0 és 9999 nap) amely során egy törölt fiókhoz potenciálisan állíthatók helyre. A megőrzési idő 0 nap van beállítva. Az érték "0" azt jelenti, hogy a törölt fiók azonnal megőrzési kívül esik, és rendszeres szemétgyűjtési.

**A megőrzési időtartam módosítása:**

1. A böngészőben, keresse meg https://adminportal.local.azurestack.external.
2. Jelentkezzen be a verem Azure felügyeleti portálon, a felhő üzemeltetője (a telepítés során megadott hitelesítő adatok használata)
3. Keresse meg az alapértelmezett irányítópult – a **régió felügyeleti** listában, és válassza ki azt a régiót szeretné használni – például **(helyi**).
4. Válassza ki **tárolási** a a **erőforrás-szolgáltató** listája.
5. Válassza ki **beállítások** megnyitásához a beállítás ablaktábla felső részén.
6. Válassza ki **konfigurációs** szerkessze az adatmegőrzési időszak értéket.

   Állítsa be a napok számát, és mentse azt.
   
   Ez az érték azonnal hatékonyan, és a teljes régió van beállítva.

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Kapacitás visszaigényléséhez
A mellékhatással működő, a megőrzési időszak egyike, hogy a fiók továbbra is kapacitás felhasználását, amíg a megőrzési időn kívül az ismét. A felhő felelősnek szükség lehet egy módszerre, amellyel a törölt fiók jusson, annak ellenére, hogy a megőrzési időszak még nem járt.

Kapacitás, a portál vagy a PowerShell használatával is visszaigényléséhez.

**A portál használatával kapacitás visszaigényléséhez:**
1. Nyissa meg a tárolási fiók panelen. Lásd: [található a tárfiók](#find).
2. Válassza ki **jusson** , ha a panel tetején.
3. Olvassa el az üzenetet, és válassza ki **OK**.

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. Várjon, amíg sikeres notification tekintse meg a portál a harang ikonra.

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. Frissítse a Storage-fiókok lapot. A törölt fiókok már nem jelennek meg a listában, mert azok kiürítése megtörtént.

A megőrzési időtartam explicit módon felülbírálására PowerShell is használható, és azonnal a kapacitás visszaigényléséhez.

**Kapacitás visszaigényléséhez PowerShell használatával:**   

1. Győződjön meg arról, hogy rendelkezik-e az Azure PowerShell telepítése és konfigurálása. Ha nem, kövesse az alábbi utasításokat: 
   * Telepítse a legújabb Azure PowerShell-verziót, és társítsa azt az Azure-előfizetése, [telepítése és konfigurálása az Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/).
   Azure Resource Manager parancsmagjainak kapcsolatos további információkért lásd: [az Azure PowerShell használata Azure Resource Managerrel](http://go.microsoft.com/fwlink/?LinkId=394767)
2. A következő parancsmagok futtatásához:

> [!NOTE]
> Ha ezen parancsmagok futtatásához véglegesen törli a fiókot, és annak tartalmát. Nincs helyreállítható. Ez körültekintően használja.

```PowerShell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
````

További információkért lásd: [verem PowerShell Azure dokumentációjában.](https://msdn.microsoft.com/library/mt637964.aspx)
 

## <a name="next-steps"></a>További lépések

 - Engedélyek kezelése információt [Manage Role-Based hozzáférés-vezérlés](azure-stack-manage-permissions.md).
 - Az Azure-verem kezelése tárolási kapacitás további információkért lásd: [kezelése az Azure-verem a tárolókapacitás](azure-stack-manage-storage-shares.md).