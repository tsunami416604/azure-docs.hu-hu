---
title: Bevezetés az Azure Security Center Standard a fokozott biztonság |} A Microsoft Docs
description: " Ismerje meg, hogyan való előkészítése az Azure Security Center Standard a fokozott biztonság. "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/02/2018
ms.author: rkarlin
ms.openlocfilehash: d5a4cfa3a91b82aa0f5b1725f86c732fecf632d4
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2018
ms.locfileid: "49405446"
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Bevezetés az Azure Security Center Standard a fokozott biztonság
Frissítse a Security Center Standard kihasználásához fokozott biztonsági felügyeletet és fenyegetésvédelmet a hibrid felhőalapú számítási feladatokhoz.  Standard 60 napig ingyenesen kipróbálhatja. Tekintse meg a Security Center [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/security-center/) további információt.

A Security Center a következőket tartalmazza:

- **A hibrid biztonsági** – biztonsági egységes képet kaphat az összes helyszíni és felhőbeli számítási feladatok. Biztonsági házirendek vonatkoznak, és folyamatosan értékelni a biztonsági szabványoknak való megfelelőség biztosításához a hibrid felhőbeli számítási feladatok biztonságát. Többféle forrásból, például tűzfalakból és más partneri megoldásból származó biztonsági adatokat gyűjthet, kereshet és elemezhet.
- **Speciális fenyegetésészlelés** – fejlett analitika és a Microsoft Intelligent Security Graph segítségével kivédheti kibertámadásokat –.  A beépített viselkedéselemzés és a gépi tanulás használatával azonosíthatja a támadásokat és a biztonsági rések nulladik napi kihasználását. Figyelheti a bejövő támadásokat és az incidens utáni tevékenységeket a hálózatokon, a gépeken és a felhőszolgáltatásokon. Interaktív eszközökkel és fenyegetésekkel kapcsolatos környezetalapú tudásbázissal egyszerűsítheti a vizsgálatot.
- **Hozzáférés- és alkalmazásvezérlőket** -blokkolja a kártevőket és más nemkívánatos alkalmazásokat engedélyezési javaslatok alkalmazásával adott számítási feladatokhoz igazított, és a gépi tanuláson alapuló. A hálózat támadási felület csökkentése – igény szerinti, szabályozott hozzáféréssel rendelkező felügyeleti portjaihoz, az Azure virtuális gépekhez, kevésbé legyen kitéve a találgatásos támadásoknak és más hálózati támadásoknak.

## <a name="detecting-unprotected-resources"></a>Nem védett erőforrásokra észlelése     
A Security Center automatikusan észleli azokat az Azure-előfizetéseket vagy munkaterületeket, amelyeken nincs engedélyezve a Security Center Standard. Ez a Security Center ingyenes verzióját használó Azure-előfizetéseket és az olyan munkaterületeket is magában foglalja, amelyeken nincs engedélyezve a biztonsági megoldás.

A Standard szintre, amely örökli az előfizetés összes erőforrásának, frissítheti a teljes Azure-előfizetéssel, vagy megadhatja a frissítésére csak egy adott erőforráscsoportban egyedi házirendet. Ha az erőforráscsoport szabályzatbeállítások egyediek, a Security Center nem bírálja felül az árképzési házirendek az előfizetés Standard csomagra frissítésekor. Alkalmazása a Standard szint egy előfizetéshez csak a virtuális gépek az előfizetés a Security Center által létrehozott munkaterületeknek jelentő vonatkozik. Alkalmazása a Standard szint a munkaterületet a munkaterületre jelentő valamennyi erőforrására vonatkozik.

> [!NOTE]
> Érdemes a költségek kezelése és az ügynökök egy adott készletét korlátozásával megoldás összegyűjtött adatok mennyisége korlátozza. [Megoldás célcsoportjának](../operations-management-suite/operations-management-suite-solution-targeting.md) lehetővé teszi, hogy a megoldás hatókör vonatkozik, és célozhat meg a munkaterület számítógépek egy részének.  Célcsoport-kezelési megoldást használ, ha a Security Center a munkaterület a megoldás nem tartalmazóként sorolja fel.
>
>

## <a name="upgrade-an-azure-subscription-or-workspace"></a>Az Azure-előfizetés vagy a munkaterület frissítése
Standard egy előfizetést, vagy a munkaterület frissítése:
1. A Security Center főmenüjében válassza az **Első lépések** elemet.
  ![Bevezetés](./media/security-center-onboarding/get-started.png)
2. A **Frissítés** területen a Security Center felsorolja az előkészítésre jogosult előfizetéseket és munkaterületeket. 
   - A **Próbaverzió alkalmazása** elemre kattintva megtekintheti előfizetéseinek és munkaterületeinek listáját, valamint a próbaidőszakkal kapcsolatos jogosultsági állapotukat.
   -    Frissítheti azokat az előfizetéseket és munkaterületeket, amelyek nem jogosultak a próbaidőszakra.
   -    A próbaidőszak elkezdéséhez válassza ki az erre jogosult munkaterületeket és előfizetéseket.
3.  A **Próbaverzió indítása** gombbal elindítja a próbaidőszakot a kiválasztott előfizetéseken.
  ![Válasszon előfizetést](./media/security-center-onboarding/select-subscription.png)


   > [!NOTE]
   > A Security Center ingyenes képességek csak az Azure virtuális gépekre érvényesek. Az ingyenes funkciókat a rendszer nem alkalmazza a nem Azure-beli számítógépeket. Ha standard szintű, a standard szintű funkciók lépnek minden Azure-beli és nem Azure-beli számítógépek hardverhibái a munkaterület. Azt javasoljuk, hogy a speciális biztonsági biztosít az Azure- és -Azure erőforrások Standard vonatkoznak.
   >
   >

## <a name="onboard-non-azure-computers"></a>Nem Azure-beli számítógépek felvétele
A Security Center a nem Azure-számítógépek biztonsági állapotát is képes monitorozni, azonban ehhez elő kell készítenie ezeket az erőforrásokat. Nem Azure-beli számítógépeket is hozzáadhat a **bevezetés** panel vagy a **számítási** panelen. Végigvezetjük a két módszert.

### <a name="add-new-non-azure-computers-from-getting-started"></a>Az új nem Azure-beli számítógépek hozzáadása **első lépések**

1. Lépjen vissza **bevezetés**.   
2. Válassza a **Bevezetés** lapot.

  ![Nem Azure](./media/security-center-onboarding/non-azure.png)

3. Kattintson az **Új nem Azure-beli számítógépek hozzáadása** pont alatt található **Konfigurálás** elemre. Megjelenik a Log Analytics-munkaterületek listája. Ha van ilyen, a lista tartalmazza azt az alapértelmezett munkaterületet is, amelyet a Security Center hozott létre, amikor az automatikus kiépítés engedélyezve volt. Válassza ki ezt a munkaterületet, vagy egy másik használni kívánt munkaterületet.

  ![Nem Azure-beli számítógép hozzáadása][7]

Ha rendelkezik meglévő munkaterületek, alatt vannak felsorolva **új nem Azure-beli számítógépek hozzáadása**. Számítógépek hozzáadása egy meglévő munkaterületet, vagy hozzon létre egy új munkaterületet. Hozzon létre egy új munkaterületet, jelölje be a hivatkozás **adjon hozzá egy új munkaterületet**.

### <a name="add-new-non-azure-computers-from-compute"></a>Az új nem Azure-beli számítógépek hozzáadása **Compute**

**Hozzon létre egy új munkaterületet, és a számítógép hozzáadása**

1. A **új nem Azure-beli számítógépek hozzáadása**válassza **adjon hozzá egy új munkaterületet**.

   ![Adjon hozzá újat][4]

2. A **biztonsági és auditálási**válassza **OMS-munkaterület** hozhat létre egy új munkaterületet.
> [!NOTE]
> OMS-munkaterülete mostantól Log Analytics-munkaterületek nevezik.
3. A **OMS-munkaterület**, írja be a munkaterület adatait.
4. A **OMS-munkaterület**válassza **OK**.  Után kattintson az OK gombra, kap egy Windows vagy Linux-ügynököt és a kulcsok letöltési hivatkozást tartalmaz az ügynök konfigurálásához használja a munkaterület-azonosító.
5. A **biztonsági és auditálási**válassza **OK**.

**Válasszon ki egy meglévő munkaterületet, és a számítógép hozzáadása**

A munkafolyamat a következő is hozzáadhat egy számítógép **bevezetési**, ahogy fent látható. Számítógép hozzáadása a munkafolyamat a következő **számítási**. Ebben a példában használjuk **számítási**.

1. Térjen vissza a Security Center főmenüjébe, és **áttekintése** irányítópultot.

   ![Áttekintés][5]

2. Válassza ki **számítás és alkalmazások**.
3. A **számítás és alkalmazások**válassza **vesz fel számítógépeket**.

   ![Számítás panel][6]

4. A **új nem Azure-beli számítógépek hozzáadása**, válasszon ki egy munkaterületet, csatlakoztassa a számítógépet, és kattintson a **számítógépek hozzáadása**.

   ![Számítógépek hozzáadása][7]

 A **közvetlen ügynök** panel egy hivatkozást kínál letöltésével egy Windows vagy Linux-ügynököt, valamint a munkaterület-Azonosítót és kulcsok használata az ügynök telepítése.   

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan előkészítése az Azure és annak érdekében, hogy a Security Center fejlett biztonsági kihasználhatják a nem Azure-erőforrások.  A regisztrált erőforrás lásd: a további lehetőségek

- [Az adatgyűjtés engedélyezése](security-center-enable-data-collection.md)
- [Fenyegetésfelderítési jelentés](security-center-threat-report.md)
- [Igény szerinti virtuális gépek elérése](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-onboarding/onboard.png
[2]: ./media/security-center-onboarding/onboard-subscription.png
[3]: ./media/security-center-onboarding/get-started.png
[4]: ./media/security-center-onboarding/create-workspace.png
[5]: ./media/security-center-onboarding/overview.png
[6]: ./media/security-center-onboarding/compute-blade.png
[7]: ./media/security-center-onboarding/add-computer.png
[8]: ./media/security-center-onboarding/onboard-workspace.png
