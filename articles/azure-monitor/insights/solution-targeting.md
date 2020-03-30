---
title: Figyelési megoldások célzása az Azure Monitorban | Microsoft dokumentumok
description: A figyelési megoldások lehetővé teszik, hogy a figyelési megoldásokat az ügynökök egy adott készletére korlátozza.  Ez a cikk ismerteti, hogyan hozhat létre hatókör-konfigurációt, és alkalmazza azt a megoldásra.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: dd3279db67fb45aee43cf1b0ef1bebf49433eef4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663232"
---
# <a name="targeting-monitoring-solutions-in-azure-monitor-preview"></a>Figyelési megoldások célzása az Azure Monitorban (előzetes verzió)
Amikor figyelőmegoldást ad hozzá az előfizetéséhez, az alapértelmezés szerint automatikusan telepítve lesz a Log Analytics-munkaterülethez csatlakoztatott összes Windows- és Linux-ügynökre.  Előfordulhat, hogy szeretné kezelni a költségeket, és korlátozza a megoldáshoz gyűjtött adatok mennyiségét, ha az ügynökök egy adott készletére korlátozza azadatokat.  Ez a cikk ismerteti, hogyan **használhatja a megoldás célzása,** amely egy olyan funkció, amely lehetővé teszi, hogy a hatókör alkalmazása a megoldásokra.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="how-to-target-a-solution"></a>Hogyan célozzunk meg egy megoldást?
A következő szakaszokban ismertetett megoldás célzásának három lépése van. 


### <a name="1-create-a-computer-group"></a>1. Számítógép-csoport létrehozása
Adja meg a számítógépeket, amelyeket szeretne felvenni a hatókörben egy [számítógépcsoport](../platform/computer-groups.md) az Azure Monitorban.  A számítógépcsoport alapulhat naplólekérdezésen, vagy importálható más forrásokból, például az Active Directory ból vagy a WSUS-csoportokból. Az [alábbiakban leírtak](#solutions-and-agents-that-cant-be-targeted)szerint csak az Azure Monitorhoz közvetlenül kapcsolódó számítógépek fognak szerepelni a hatókörben.

Miután létrehozta a számítógépcsoportot a munkaterületen, akkor azt egy vagy több megoldásra alkalmazható hatókörkonfigurációba fogja foglalni.
 
 
### <a name="2-create-a-scope-configuration"></a>2. Hatókör-konfiguráció létrehozása
 A **Hatókör-konfiguráció** egy vagy több számítógépcsoportot tartalmaz, és egy vagy több megoldásra alkalmazható. 
 
 Hozzon létre egy hatókörkonfigurációt a következő folyamat tal.  

 1. Az Azure Portalon keresse meg a **Log Analytics-munkaterületeket,** és válassza ki a munkaterületet.
 2. A munkaterület tulajdonságai csoportban: **Munkaterületadatforrások,** válassza a **Hatókörkonfigurációk**lehetőséget.
 3. Új hatókörkonfiguráció létrehozásához kattintson a **Hozzáadás** gombra.
 4. Írja be a hatókör **konfigurációjának nevét.**
 5. Kattintson **a Számítógépcsoportok kijelölése gombra.**
 6. Jelölje ki a létrehozott számítógépcsoportot, és tetszés szerint a konfigurációhoz hozzáadni kívánt csoportokat.  Kattintson a **Kiválasztás** gombra.  
 6. A hatókör konfigurációjának létrehozásához kattintson az **OK** gombra. 


### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. Alkalmazza a hatókör konfigurációját egy megoldásra.
Miután rendelkezik egy hatókör konfigurációval, akkor alkalmazhatja azt egy vagy több megoldásra.  Vegye figyelembe, hogy bár egyetlen hatókörkonfiguráció több megoldással is használható, minden megoldás csak egy hatókörkonfigurációt használhat.

Hatókör-konfiguráció alkalmazása a következő folyamat tal.  

 1. Az Azure Portalon keresse meg a **Log Analytics-munkaterületeket,** és válassza ki a munkaterületet.
 2. A munkaterület tulajdonságai között válassza a **Megoldások**lehetőséget.
 3. Kattintson a hatókörbe kívánt megoldásra.
 4. A megoldás tulajdonságai **csoportban: Munkaterületadatforrások,** válassza a **Megoldáscélzás lehetőséget.**  Ha a lehetőség nem érhető el, akkor [ez a megoldás nem célozható meg.](#solutions-and-agents-that-cant-be-targeted)
 5. Kattintson **a Hatókör-konfiguráció hozzáadása gombra.**  Ha már rendelkezik konfigurációval erre a megoldásra, akkor ez a beállítás nem lesz elérhető.  Egy másik hozzáadása előtt el kell távolítania a meglévő konfigurációt.
 6. Kattintson a létrehozott hatókörkonfigurációra.
 7. Tekintse meg a konfiguráció **állapotát,** és győződjön meg arról, hogy **a sikeres**.  Ha az állapot hibát jelez, kattintson a konfigurációtól jobbra található ellipszisre, és a módosítások módosításához válassza a **Hatókör-konfiguráció szerkesztése** lehetőséget.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Nem célzott megoldások és ügynökök
Az alábbiakban az ügynökök és megoldások, amelyek nem használhatók a megoldás célzása feltételeket.

- A megoldáscélzás csak az ügynökökre üzembe helyezett megoldásokra vonatkozik.
- A megoldáscélzás csak a Microsoft által biztosított megoldásokra vonatkozik.  Nem vonatkozik az [ön által vagy partnerei által létrehozott](solutions-creating.md)megoldásokra.
- Csak az Azure Monitorhoz közvetlenül kapcsolódó ügynököket szűrheti ki.  A megoldások automatikusan települnek a csatlakoztatott Operations Manager felügyeleti csoport részét képező ügynökökre, függetlenül attól, hogy szerepelnek-e a hatókör-konfigurációban.

### <a name="exceptions"></a>Kivételek
A megoldáscélzás nem használható a következő megoldásokkal, még akkor sem, ha megfelelnek a megadott feltételeknek.

- Ügynök állapotfelmérése

## <a name="next-steps"></a>További lépések
- További információ a figyelési megoldásokról, beleértve a környezetében telepíthető megoldásokat [az Azure Log Analytics figyelési megoldások hozzáadása a munkaterülethez című témakörben.](solutions.md)
- További információ a számítógépcsoportok létrehozásáról az [Azure Monitor naplólekérdezéseiben.](../platform/computer-groups.md)
