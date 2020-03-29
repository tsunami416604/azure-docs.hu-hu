---
title: A standard szintű fokozott biztonság – Azure Security Center
description: " Ismerje meg, hogyan lehet az Azure Security Center standard rakoncáta a fokozott biztonság érdekében. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: memildin
ms.openlocfilehash: be26a9d4c66412518079de303ac0764d979c3e7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77912052"
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Regisztráció az Azure Security Center Standardra a biztonság növelése érdekében
Frissítsen a Security Center Standard szolgáltatásra, hogy kihasználhassa a fokozott biztonsági felügyeletet és a fenyegetésvédelmet a hibrid felhőbeli számítási feladatokhoz. Kipróbálhatja standard ingyenes. További információt a Biztonsági központ [díjszabási oldalán](https://azure.microsoft.com/pricing/details/security-center/) talál.

A Security Center szabványos szintje a következőket tartalmazza:

- **Hibrid biztonság** – Egységes biztonságszemléletet kaphat az összes helyszíni és felhőbeli számítási feladatban. A biztonsági szabályzatokat és a hibrid felhőbeli számítási feladatok biztonságát folyamatosan felmérheti a biztonsági szabványoknak való megfelelés érdekében. Különböző forrásokból, például tűzfalakból és más partnermegoldásokból származó biztonsági adatok gyűjtése, keresése és elemzése.
- Biztonsági riasztások – Fejlett **elemzésekkel** és a Microsoft Intelligent Security Graph segítségével előnyt szerezhet a fejlődő kibertámadásokkal szemben. Használja ki a beépített viselkedéselemzéseket és a gépi tanulást a támadások és a nulladik napi támadások azonosításához. Figyelje a hálózatokat, gépeket és felhőszolgáltatásokat a bejövő támadások és a szabálysértést követő tevékenységek esetén. Egyszerűsítse a nyomozást interaktív eszközökkel és kontextuális fenyegetésekkel kapcsolatos intelligenciával.
- **Hozzáférés és alkalmazásvezérlők** – Blokkolja a rosszindulatú programokat és más nem kívánt alkalmazásokat az adott számítási feladatokhoz igazított és gépi tanulással működő engedélyezési javaslatok alkalmazásával. Csökkentse a hálózati támadási felületet az Azure-beli virtuális gépek felügyeleti portjaihoz való just-in-time, szabályozott hozzáféréssel, drasztikusan csökkentve a találgatásos és egyéb hálózati támadásoknak való kitettséget.

## <a name="detecting-unprotected-resources"></a>Nem védett erőforrások észlelése
A Security Center automatikusan észleli azokat az Azure-előfizetéseket vagy munkaterületeket, amelyeken nincs engedélyezve a Security Center Standard. Ez a Security Center ingyenes verzióját használó Azure-előfizetéseket és az olyan munkaterületeket is magában foglalja, amelyeken nincs engedélyezve a biztonsági megoldás.

Frissítheti a teljes Azure-előfizetést a standard szintre, amelyet az előfizetésen belüli összes támogatott erőforrás öröklődik. A Standard szint alkalmazása egy munkaterületre a munkaterületre jelentést tevő összes erőforrásra vonatkozik.

> [!NOTE]
> Előfordulhat, hogy szeretné kezelni a költségeket, és korlátozza a megoldáshoz gyűjtött adatok mennyiségét, ha az ügynökök egy adott készletére korlátozza azadatokat. [A megoldás célzása](../operations-management-suite/operations-management-suite-solution-targeting.md) lehetővé teszi, hogy hatókört alkalmazzon a megoldásra, és a munkaterület számítógépeinek egy részét célozza meg. Ha megoldáscélzást használ, a Security Center úgy sorolja fel a munkaterületet, hogy nincs megoldás.
>
>

## <a name="upgrade-an-azure-subscription-or-workspace"></a>Azure-előfizetés vagy munkaterület frissítése
Előfizetés vagy munkaterület szabványosra való frissítése:
1. A Security Center főmenüjében válassza az **Első lépések** elemet.
  ![Bevezetés](./media/security-center-onboarding/get-started.png)
2. A **Frissítés** területen a Security Center felsorolja az előkészítésre jogosult előfizetéseket és munkaterületeket. 
   - A **Próbaverzió alkalmazása** elemre kattintva megtekintheti előfizetéseinek és munkaterületeinek listáját, valamint a próbaidőszakkal kapcsolatos jogosultsági állapotukat.
   -    Frissítheti azokat az előfizetéseket és munkaterületeket, amelyek nem jogosultak a próbaidőszakra.
   -    A próbaidőszak elkezdéséhez válassza ki az erre jogosult munkaterületeket és előfizetéseket.
3.  A **Próbaverzió indítása** gombbal elindítja a próbaidőszakot a kiválasztott előfizetéseken.
  ![Előfizetés kiválasztása](./media/security-center-onboarding/select-subscription.png)


   > [!NOTE]
   > A Security Center ingyenes funkciói csak az Azure-beli virtuális gépekre és a VMSS-re vonatkoznak. Az ingyenes képességek nem vonatkoznak a nem Azure-beli számítógépekre. Ha a Standard lehetőséget választja, a standard képességek az összes Azure-beli virtuális gépre, virtuálisgép-méretezési csoportra és nem Azure-beli számítógépekre vonatkoznak, amelyek a munkaterületre kerülnek. Azt javasoljuk, hogy alkalmazza a Standard az Azure és a nem Azure-erőforrások speciális biztonságának biztosítása érdekében.
   >

## <a name="onboard-non-azure-computers"></a>Beépített, nem Azure-alapú számítógépek
A Security Center a nem Azure-számítógépek biztonsági állapotát is képes monitorozni, azonban ehhez elő kell készítenie ezeket az erőforrásokat. Nem Azure-beli számítógépeket az **Első lépések** panelről vagy a **Számítási** panelről adhat hozzá. Végigmegyünk mindkét módszeren.

### <a name="add-new-non-azure-computers-from-getting-started"></a>Új, nem Azure-beli számítógépek hozzáadása **az első lépésekből**

1. Visszatérés az **első lépésekhez**.
2. Válassza a **Bevezetés** lapot.

   ![Nem Azure-os](./media/security-center-onboarding/non-azure.png)

3. Kattintson az **Új nem Azure-beli számítógépek hozzáadása** pont alatt található **Konfigurálás** elemre. Megjelenik a Log Analytics-munkaterületek listája. Ha van ilyen, a lista tartalmazza azt az alapértelmezett munkaterületet is, amelyet a Security Center hozott létre, amikor az automatikus kiépítés engedélyezve volt. Válassza ki ezt a munkaterületet, vagy egy másik használni kívánt munkaterületet.

   ![Nem Azure-beli számítógép hozzáadása][7]

Ha már rendelkezik munkaterületekkel, azok az **Új, nem Azure-alapú számítógépek hozzáadása**csoportban jelennek meg. Számítógépeket hozzáadhat egy meglévő munkaterülethez, vagy létrehozhat egy új munkaterületet. Új munkaterület létrehozásához jelölje ki az **új munkaterület hozzáadása**hivatkozást .

### <a name="add-new-non-azure-computers-from-compute"></a>Új, nem Azure-alapú számítógépek hozzáadása **a Compute szolgáltatásból**

**Új munkaterület létrehozása és számítógép hozzáadása**

1. Az **Új, nem Azure-alapú számítógépek hozzáadása csoportban**válassza **az Új munkaterület hozzáadása**lehetőséget.

   ![Új munkaterület hozzáadása][4]

2. A **Biztonság és naplózás csoportban**válassza az **OMS-munkaterület** lehetőséget új munkaterület létrehozásához.
   > [!NOTE]
   > Az OMS-munkaterületeket mostantól Log Analytics-munkaterületeknek nevezzük.
3. Az **OMS-munkaterület csoportban**adja meg a munkaterület adatait.
4. Az **OMS-munkaterület csoportban**válassza az **OK lehetőséget.** Miután kiválasztotta az OK gombot, kap egy hivatkozást egy Windows- vagy Linux-ügynök letöltéséhez, valamint a munkaterület-azonosítóhoz használt kulcsokat az ügynök konfigurálásához.
5. A **Biztonság és naplózás csoportban**válassza az OK **gombot.**

**Meglévő munkaterület kijelölése és számítógép hozzáadása**

A számítógép hozzáadásához kövesse a bevezetés **munkafolyamatát**, ahogy fent látható. A számítógépet a Compute munkafolyamatának követésével is **hozzáadhatja.** Ebben a példában a **Compute**.

1. Visszatérés a Biztonsági központ főmenüjéhez és **az Áttekintés** irányítópulthoz.

   ![Áttekintés][5]

2. Válassza **a Számítási & alkalmazások lehetőséget.**
3. A **Számítási & alkalmazások csoportban**válassza a Számítógépek **hozzáadása**lehetőséget.

   ![Számítás panel][6]

4. Az **Új, nem Azure-alapú számítógépek hozzáadása csoportban**válassza ki azt a munkaterületet, amelyhez a számítógépet csatlakoztatni szeretné, és kattintson a **Számítógépek hozzáadása**gombra.

   ![Számítógépek hozzáadása][7]

   A **Direct Agent** panel egy windowsos vagy Linux-ügynök, valamint a munkaterület-azonosító és az ügynök konfigurálásához használható kulcsok letöltésére szolgáló hivatkozást biztosít.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan hozhatja be az Azure-t és a nem Azure-erőforrásokat a Security Center fejlett biztonságának kihasználása érdekében. Ha többet szeretne tenni a beépített erőforrásokkal, olvassa el a

- [Az adatgyűjtés engedélyezése](security-center-enable-data-collection.md)
- [Fenyegetésfelderítési jelentés](security-center-threat-report.md)
- [Virtuális gépek igény szerinti elérése](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-onboarding/onboard.png
[2]: ./media/security-center-onboarding/onboard-subscription.png
[3]: ./media/security-center-onboarding/get-started.png
[4]: ./media/security-center-onboarding/create-workspace.png
[5]: ./media/security-center-onboarding/overview.png
[6]: ./media/security-center-onboarding/compute-blade.png
[7]: ./media/security-center-onboarding/add-computer.png
[8]: ./media/security-center-onboarding/onboard-workspace.png
