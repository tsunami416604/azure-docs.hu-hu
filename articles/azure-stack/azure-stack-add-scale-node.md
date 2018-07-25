---
title: Az Azure Stack méretezési csomópontok hozzáadása |} A Microsoft Docs
description: Az Azure Stackben egységig csomópontok hozzáadása.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: brenduns
ms.reviewer: thoroet
ms.openlocfilehash: 15c46bb0fee4689b614636d8c9e01e36f7f0be3d
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39228087"
---
# <a name="add-additional-scale-unit-nodes-in-azure-stack"></a>Kiegészítő skálázási egység csomópontok hozzáadása az Azure Stackben

Az Azure Stack-operátorok egy további fizikai számítógép hozzáadása egy meglévő méretezési egység teljes kapacitás növelheti. A fizikai számítógép is nevezzük a skálázási egység csomópont. Minden új méretezési egység csomópontot ad hozzá a CPU típusát, memória, és a lemez számát & azokra csomópontokra, amelyeken már léteznek a skálázási egység mérete homogén kell lennie.  

> [!NOTE]  
Az Azure Stack 1807 vagy újabb verzió hozzáadása kiegészítő skálázási egység csomópontok kell futtatni.

A skálázási egység csomópont hozzáadásához járnak el az Azure Stackben, és futtassa a hardvert gyártó (OEM) az eszközök. Az OEM azokat az eszközöket a gazdagépen hardver életciklus (HLH.) Győződjön meg arról, hogy az új fizikai számítógép megegyezik a meglévő csomópontok azonos belső vezérlőprogram szinten fut.

Az alábbi folyamatábrája bemutatja az általános folyamat a skálázási egység csomópont hozzáadása.

![Adja hozzá a skálázási egység folyamat](media/azure-stack-add-scale-node/add-node-flow.png) &#42; *e OEM hardvergyártójához ír elő a fizikai kiszolgáló rack elhelyezése és a belső vezérlőprogram frissítése a támogatási szerződés alapján változik.*

A művelet egy új csomópont hozzáadása is igénybe vehet, néhány óra vagy nap végrehajtásához.

## <a name="add-scale-unit-nodes"></a>Skálázási egység csomópontok hozzáadása 
A következő lépéseket kell egy csomópont hozzáadása az magas szintű áttekintése. Ne kövesse az alábbi lépéseket az OEM által biztosított kapacitás bővítése dokumentáció első hivatkozó nélkül.

1. Az új fizikai kiszolgálót helyezze az állványra szerelt, és megfelelően kábelezése azt. 
2. Konfigurálja a megfelelő IP-címet az alaplapi felügyeleti vezérlőnek (BMC), és az OEM által biztosított dokumentáció minden BIOS-beállítások alkalmazásához.
3. Az aktuális belső vezérlőprogram alapterv alkalmazási minden összetevő számára, amelyek a HLH futnak, a hardver gyártója által biztosított eszközök segítségével.
4. Futtassa az Add csomópont műveletet az Azure Stack felügyeleti portálon.
5. Ellenőrizze, hogy a Hozzáadás csomópont művelet sikeres. Ehhez ellenőrizze a [ **állapot** a skálázási egység](#monitor-add-node-operations). 

## <a name="add-the-node"></a>A csomópont hozzáadása 
A felügyeleti konzol vagy a PowerShell használatával új csomópontokat. A Hozzáadás csomópont művelet először felveszi az új méretezési egység csomópont a rendelkezésre álló számítási kapacitást, és automatikusan kiterjeszti a tárolási kapacitást. A kapacitás automatikusan bontja ki, mert az Azure Stack egy hiperkonvergens rendszer ahol *számítási* és *tárolási* együtt méretezhető.

### <a name="use-the-admin-console"></a>A felügyeleti konzol használata
1. Jelentkezzen be az Azure Stack rendszergazdai portál az Azure Stack operátorait szerint.
2. Navigáljon a **régiók kezelése** > **egységig bővíthető**, majd válassza ki a skálázási egység, amelyeket szeretne a kapacitás bővítése céljából.  
   ![Válassza ki a skálázási egység](media/azure-stack-add-scale-node/select-node1.png)

   ![Skálázási egység részleteinek megtekintése](media/azure-stack-add-scale-node/select-node2.png)
 
3. A következő paraméterek szükségesek:  
   - **BMC IP-cím** egyes méretezési egység csomópontok hozzáadása. (Egy IP-cím minden sorában.) ![Csomópont hozzáadása](media/azure-stack-add-scale-node/add-node.png)

### <a name="use-powershell"></a>A PowerShell használata

Használja a **New-AzsScaleUnitNodeObject** parancsmag az egy vagy több csomópontot.  

Használja a következő PowerShell-példaszkriptekre egyikét, mielőtt cserélje le az értékeket *csomópontnevek* és *IP-címek* értékeit az Azure Stack környezettel.

**Az egyetlen csomópontba:**
  ```powershell
  ## Add a single Node 
  $NewNode=New-AzsScaleUnitNodeObject -computername "<name_of_new_node>" -BMCIPv4Address "<BMCIP_address_of_new_node>" 
 
  Add-AzsScaleUnitNode -NodeList $NewNode -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```  

**Több csomópont hozzáadása:**
  ```powershell
  ## Add multiple nodes 
  $NewNode1=New-AzsScaleUnitNodeObject -computername "<name_of_new_node01>"  -BMCIPv4Address "<BMCIP_address_of_new_node01>" 
 
  $NewNode2=New-AzsScaleUnitNodeObject -computername "<name_of_new_node02>" -BMCIPv4Address “<BMCIP_address_of_new_node02>”$ 
 
  Add-AzsScaleUnitNode -NodeList @($NewNode1,$NewNode2) -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```

## <a name="monitor-add-node-operations"></a>A figyelő csomópont hozzáadása műveletek 
A felügyeleti portálon vagy a PowerShell segítségével a Hozzáadás csomópont művelet állapotának beolvasása. Adja hozzá a csomópont műveletek végrehajtásához napra több óráig is tarthat.

### <a name="use-the-admin-console"></a>A felügyeleti konzol használata 
Egy új csomópont hozzáadása figyeléséhez, a felügyeleti portálon tekintse át a skálázási egység vagy egység csomópont objektumokat. Ehhez nyissa meg **régiók kezelése** > **egységig**. Ezután válassza ki a skálázási egység vagy meg szeretné tekinteni a skálázási egység csomópont. 

### <a name="use-powershell"></a>A PowerShell használata
A skálázási egység és a skálázási egység csomópontok állapota lehet beolvasni a következő PowerShell-lel:
  ```powershell
  #Retrieve Status for the Scale Unit
  Get-AzsScaleUnit|select name,state
 
  #Retrieve Status for each Scale Unit Node
  Get-AzsScaleUnitNode |Select Name, ScaleUnitNodeStatus
```

### <a name="status-for-the-add-node-operation"></a>A Hozzáadás csomópont művelet állapota 
**A skálázási egység:**
|status               |Leírás  |
|---------------------|---------|
|Fut              |A skálázási egység aktívan résztvevő összes csomópontot.|
|Leállítva              |A skálázási egység csomópont, lefelé vagy nem érhető el.|
|Kibontás            |Egy vagy több skálázási egység csomópont jelenleg jelennek meg a számítási kapacitást.|
|Tároló konfigurálása  |A számítási kapacitás ki lett terjesztve, és a tárolási konfiguráció fut-e.|
|Szervizelés szükséges |Hibát észlelt, amely megköveteli, hogy egy vagy több skálázási egység csomópont javítani kell.|


**Egy méretezési egység csomópont:**
|status                |Leírás  |
|----------------------|---------|
|Fut               |A csomópont tevékenyen részt vesz a skálázási egység.|
|Leállítva               |A csomópont nem érhető el.|
|Hozzáadás                |A csomópont aktívan ad hozzá a skálázási egység.|
|Javítás             |A csomópont aktívan javítása folyamatban van.|
|Karbantartás           |A csomópont fel van függesztve, és nincsenek aktív felhasználói munkaterhelés fut-e. |
|Szervizelés szükséges  |Hibát észlelt, amely megköveteli, hogy a csomópont nem működik helyesen.|


## <a name="troubleshooting"></a>Hibaelhárítás
Az alábbiakban a leggyakoribb problémáinak csomópont hozzáadásakor. 

**1. forgatókönyv:** a Hozzáadás skálázási egység csomópont művelet sikertelen lesz, de egy vagy több csomópont szereplő Leállítva állapotú.  
- Szervizelési: Egy vagy több csomópont használata a javítási művelet. Csak egyetlen javítási művelet is futtatható egyszerre.

**2. forgatókönyv:** egy vagy több skálázási egység csomópont már hozzá lett adva, de a tárolási bővítése nem sikerült. Ebben a forgatókönyvben a skálázási egység csomópont objektum állapotjelentést fut, de a tárolás konfigurálása a feladat nincs elindítva.  
- Szervizelési: A kiemelt végponthoz segítségével áttekintheti az storage a következő PowerShell-parancsmag futtatásával:
 
**3. forgatókönyv:** , amely azt jelzi, hogy a horizontális felskálázás feladat nem sikerült riasztást kapott.  
- Szervizelési: Ebben az esetben a tároló konfigurációs feladat nem sikerült. Ez a probléma megköveteli, hogy forduljon az ügyfélszolgálathoz.


## <a name="next-steps"></a>További lépések 
Felülvizsgálat [csomópont műveletek](azure-stack-node-actions.md) 
