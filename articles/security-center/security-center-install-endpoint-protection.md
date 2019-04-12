---
title: Az Azure Security Center végpontvédelem kezeléséhez |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti a végpontvédelem az Azure Security Centerben.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: rkarlin
ms.openlocfilehash: 882d4e0592b74e8af30ff5bf110a41e403c3bf7d
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59494030"
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>Az Azure Security Center végpontvédelmi problémák kezelése
Az Azure Security Center figyeli a kártevőkkel szembeni védelem állapotát, és ezt jelzi az Endpoint protection problémák panel alatt. A Security Center problémákról, például az észlelt fenyegetések és a nem megfelelő védelemmel, amely sérülékennyé teheti a virtuális gépek (VM) és a számítógépek kártevőirtó fenyegetések emeli ki. Az információk használatával **végpontvédelem**, azonosíthatja a terv azonosított problémák elhárítása.

A Security Center a következő végpontvédelmi problémákkal jelentések:

- Az Endpoint protection nincs telepítve az Azure VM-eken – egy támogatott kártevőirtó megoldás ezeket Azure virtuális gépeken nincs telepítve.
- Az Endpoint protection nincs telepítve a nem Azure-beli számítógépek – egy támogatott kártevőirtó ezeken a nem Azure-beli számítógépeken nincs telepítve.
- Endpoint protection állapota:

  - Lejárt aláírás – egy kártevőirtó megoldást a virtuális gépeken és számítógépeken települ, de a megoldás nem rendelkezik a legújabb kártevőirtó-aláírásokat.
  - Nincs valós idejű védelem – egy kártevőirtó megoldást a virtuális gépeken és számítógépeken telepítve van, de a valós idejű védelem nincs konfigurálva.   Letilthatja a szolgáltatást, vagy lehet, hogy a Security Center nem olvasható be az állapot, mert a megoldás nem támogatott. Lásd: [partnerintegráció](security-center-os-coverage.md#supported-endpoint-protection-solutions) támogatott megoldások listáját.
  - Jelentést nem küldő – egy kártevőirtó megoldás telepítve van, de nem küld adatokat.
  - Ismeretlen – egy kártevőirtó megoldás telepítve van, de annak állapota ismeretlen, vagy egy ismeretlen hibát jelez.

    > [!NOTE]
    > Lásd: [biztonsági megoldások integrálása](security-center-os-coverage.md#supported-endpoint-protection-solutions) integrálhatók a Security Centerrel végpontvédelmi biztonsági megoldások listáját.
    >
    >

## <a name="implement-the-recommendation"></a>A javaslatok megvalósítása
Végpontvédelmi problémákkal jeleníti meg a Security Center egy javaslatot.  A környezet kitéve a kártevőirtó fenyegetésekkel szemben, ha a javaslat területen látható **javaslatok** és **számítási**. Megtekintheti a **az Endpoint protection problémák irányítópult**, el kell végezni a számítási munkafolyamat.

Ebben a példában használjuk **számítási**.  Áttekintjük, hogyan telepítheti a kártevőirtó Azure virtuális gépeken, és nem Azure-beli számítógépeken.

## <a name="install-antimalware-on-azure-vms"></a>Kártevőirtó telepítése Azure virtuális gépeken

1. Válassza ki **számítás és alkalmazások** a Security Center főmenüjében vagy **áttekintése**.

   ![Válassza ki a számítási][1]

2. A **számítási**válassza **végpontvédelem**. A **végpontvédelem** irányítópult nyílik meg.

   ![Válassza ki a Végpontvédelmi problémák][2]

   Az irányítópult tetején biztosítja:

   - Telepített végpontvédelmi szolgáltatók – a Security Center által azonosított különböző szolgáltatók listája.
   - Telepített végpontvédelem állapota – a virtuális gépeket és számítógépeket, amelyek rendelkeznek telepített végpontvédelmi megoldás állapotát jeleníti meg. A diagram bemutatja a virtuális gépek és számítógépek, amelyek a megfelelő és nem megfelelő védelemmel a számát.
   - Kártevő észlelve – virtuális gépek számát jeleníti meg, és hol van a Security Center jelentést készítő számítógépek kártevő észlelhető.
   - Megtámadott számítógépek – virtuális gépek és számítógépek, ahol a Security Center jelent támadások kártevő számát jeleníti meg.

   Az irányítópult alján nincs végpont listáját védelmével kapcsolatos problémák, amely tartalmazza a következő információkat:  

   - **TELJES** – a probléma által érintett virtuális gépek és számítógépek számát.
   - A sáv összevonása a virtuális gépek és a probléma által érintett számítógépek számát. A színeket a sáv a prioritás határozza meg:

      - Piros - magas prioritás, azonnal foglalkozzon
      - Narancssárga – közepes szintű prioritás, és a lehető leghamarabb foglalkozzon

3. Válassza ki **Endpoint protection nincs telepítve az Azure virtuális gépekhez**.

   ![Válassza ki az Endpoint protection nincs telepítve az Azure virtuális gépekhez][3]

4. A **Endpoint protection nincs telepítve az Azure virtuális gépekhez** az Azure virtuális gépek, amelyeken nincs telepítve kártevőirtó listája.  A listában lévő összes virtuális gép kártevőirtó telepítse, vagy válassza ki az egyes virtuális gépekhez kártevőirtó telepíthető az adott virtuális Gépre kattintva lehet váltani.
5. A **válassza ki az Endpoint protection**, válassza ki a használni kívánt végpontvédelmi megoldás. Ebben a példában válassza **Microsoft Antimalware**.
6. További információ az endpoint protection megoldás jelenik meg. Kattintson a **Létrehozás** gombra.

## <a name="install-antimalware-on-non-azure-computers"></a>Az Azure-beli számítógépek kártevőirtó telepítése

1. Lépjen vissza a **végpontvédelem** válassza **nem Azure-beli számítógépeken nincs telepítve az Endpoint protection**.

   ![Válassza ki a nem Azure-beli számítógépeken nincs telepítve az Endpoint protection][4]

2. A **nem Azure-beli számítógépeken nincs telepítve az Endpoint protection**, válasszon ki egy munkaterületet. Az Azure Monitor naplók keresési lekérdezés szűri, hogy a munkaterület megnyílik, és megjeleníti a számítógépek kártevőirtó hiányzik. További információ a listából válasszon ki egy számítógépet.

   ![Az Azure Monitor-naplók keresése][5]

Csak az adott számítógépen a szűrt adatokat egy másik keresési eredmény nyílik meg.

  ![Az Azure Monitor-naplók keresése][6]

> [!NOTE]
> Azt javasoljuk, hogy az endpoint protection ki kell építeni az összes virtuális gép és számítógép azonosításához, és távolítsa el a vírusok, kémprogramok és más, kártevő szoftverek.
>
>

## <a name="next-steps"></a>További lépések
Ez a cikk bemutatta, hogyan valósíthat meg a Security Center javaslatait "Az Endpoint Protection telepítése." A Microsoft Antimalware az Azure-ban engedélyezésével kapcsolatos további tudnivalókért tekintse meg a következő dokumentumot:

* [A Microsoft Antimalware Cloud Services és Virtual Machines](../security/azure-security-antimalware.md) – ismerje meg, hogyan helyezhet üzembe a Microsoft Antimalware-t.

A Security Centerrel kapcsolatos további tudnivalókért tekintse meg a következő dokumentumokat:

* [Biztonsági szabályzatok beállítása az Azure Security Center](tutorial-security-policy.md) – ismerje meg, hogyan konfigurálhat biztonsági házirendeket.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, hogyan javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának monitorozásához.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések az Azure biztonsági és megfelelőségi.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
