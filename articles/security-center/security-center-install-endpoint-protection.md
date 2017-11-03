---
title: "Végpontvédelmi problémákkal az Azure Security Center kezelése |} Microsoft Docs"
description: "Megtudhatja, hogyan kezelheti a végpontvédelmi problémákkal az Azure Security Centerben."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/23/2017
ms.author: terrylan
ms.openlocfilehash: abbcb0a8e0206d78ca94520dfa81ab92506c47af
ms.sourcegitcommit: 4d90200f49cc60d63015bada2f3fc4445b34d4cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2017
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>Végpontvédelmi problémákkal az Azure Security Center kezelése
Az Azure Security Center szembeni védelem állapotát figyeli, és ez a jelentéseket a az Endpoint protection problémák panelen. A Security Center kiemeli a problémákat, például fenyegetést észlelt, és nem megfelelő védelemmel, amely biztosíthat a virtuális gépek (VM) és a számítógépek kártevőirtó fenyegetések kitéve. Csoportban szereplő információk segítségével **Végpontvédelmi problémákkal**, azonosíthatja a tervet minden azonosított problémát.

A Security Center jelentést a következő végpont védelmével kapcsolatos problémák:

- Az Azure virtuális gépeken futó – egy támogatott kártevőirtó megoldás nincs telepítve az Endpoint protection nincs telepítve az Azure virtuális gépeken.
- Az Endpoint protection az Azure-számítógépek – nincs telepítve egy támogatott kártevőirtó nincs telepítve a-Azure számítógépek.
- Az Endpoint protection állapotát:

   - Lejárt aláírás – egy kártevőirtó megoldás telepítve van a virtuális gépek és a számítógépek, de a megoldás nem rendelkezik a legújabb kártevőirtó-aláírások.
   - Nincs valós idejű védelem – egy kártevőirtó megoldás telepítve van a virtuális gépek és a számítógépek, de nincs konfigurálva a valós idejű védelem.   A szolgáltatás esetleg le lesznek tiltva, vagy előfordulhat, hogy a Security Center nem tudja megszerezni az állapot, mert a megoldás nem támogatott. Lásd: [integrációs partneri](security-center-partner-integration.md) támogatott megoldások listáját.
   - Jelentést nem készítő – egy kártevőirtó megoldás telepítve van, de nem küld adatokat.
   - Ismeretlen – egy kártevőirtó megoldás telepítve van, de állapota nem ismeretlen, vagy egy ismeretlen hibát jelez.

   > [!NOTE]
   > Lásd: [integrálhatja biztonsági megoldásokat](security-center-partner-integration.md#integrated-azure-security-solutions) integrálva van a Security Center végpontvédelmi biztonsági megoldások listáját.
   >
   >

## <a name="implement-the-recommendation"></a>A javaslat megvalósítása
Végpontvédelmi problémákkal jeleníti meg a Security Center ajánlást.  Ha a környezetében kitéve a kártevőirtó fenyegetéseket, ez a javaslat jelenik meg a **javaslatok** és a **számítási**. Hogy a **az Endpoint protection problémák irányítópult**, kövesse a számítási munkafolyamat.

A jelen példában használjuk **számítási**.  A kártevőirtó telepítése Azure virtuális gépeken, és az Azure-számítógépeken fog keresni.

## <a name="install-antimalware-on-azure-vms"></a>Kártevőirtó telepítése Azure virtuális gépeken

1. Válassza ki **számítási** alatt a Security Center főmenü vagy **áttekintése**.

   ![Válassza ki a számítási][1]

2. A **számítási**, jelölje be **Végpontvédelmi problémákkal**. A **Végpontvédelmi problémákkal** irányítópult megnyitása.

   ![Végpontvédelmi problémákkal kiválasztása][2]

   Az irányítópult tetején biztosítja:

   - Az endpoint protection szolgáltatók - listák a Security Center által azonosított különböző szolgáltatók telepítve.
   - Telepített endpoint protection állapota - virtuális gépek és a telepített végpontvédelmi megoldás rendelkező számítógépek állapotát jeleníti meg. A diagram a virtuális gépek és a számítógépek, amelyek megfelelő számát és a nem megfelelő védelemmel ellátott mutatja.
   - Kártevő észlelhető – a virtuális gépek számát jeleníti meg, és amennyiben az a Security Center jelentést készítő számítógépek kártevő észlelhető.
   - Megtámadott számítógépek – a virtuális gépek és ahol a Security Center az jelentéskészítési támadások kártevők által számítógépek számát jeleníti meg.

   Az irányítópult alsó nincs végpont listája védelmével kapcsolatos problémák, amelyek a következő információkat tartalmazza:  

   - **TELJES** -probléma által érintett virtuális gépek és a számítógépek számát.
   - A sáv összesítése a virtuális gépek és a probléma által érintett számítógépek számát. A sáv színeit prioritás határozza meg:

      - Piros - magas prioritás, azonnal foglalkozzon
      - Narancssárga - közepes prioritású virtuális gép és a lehető leghamarabb foglalkozzon

3. Válassza ki **Azure virtuális gépeken nincs telepítve az Endpoint protection**.

   ![Válassza ki az Endpoint protection nincs telepítve az Azure virtuális gépeken][3]

4. A **Azure virtuális gépeken nincs telepítve az Endpoint protection** Azure virtuális gépek, amelyeken nincs telepítve kártevőirtó listája.  Kiválaszthatja a telepíteni kívánt kártevőirtó a listában szereplő összes virtuális gép vagy az egyes virtuális gépek kártevőirtó telepítsen az adott virtuális gépre kattintva válassza ki.
5. A **válassza ki az Endpoint protection**, válassza ki a használni kívánt végpontvédelmi megoldás. Ebben a példában válassza **Microsoft Antimalware**.
6. További információt a végpontvédelmi megoldás jelenik meg. Kattintson a **Létrehozás** gombra.

## <a name="install-antimalware-on-non-azure-computers"></a>Kártevőirtó telepítése az Azure-számítógépeken

1. Lépjen vissza a **Végpontvédelmi problémákkal** válassza **nincs telepítve az Azure-számítógépeken az Endpoint protection**.

   ![Válassza ki az Endpoint protection nincs telepítve az Azure-számítógépeken][4]

2. A **nincs telepítve az Azure-számítógépeken az Endpoint protection**, válassza ki a munkaterületen. A munkaterület szűrve Naplóelemzési keresési lekérdezés megnyílik, és megjeleníti a számítógépek kártevőirtó hiányzik. További információ a listából válasszon ki egy számítógépet.

   ![A Naplóelemzési keresése][5]

Egy másik keresési eredmény információ csak az adott számítógépen szűrt nyílik meg.

  ![A Naplóelemzési keresése][6]

> [!NOTE]
> Azt javasoljuk, hogy az endpoint protection építhető ki az összes virtuális gépek és a számítógépek számára hatékonyabb azonosításához és eltávolításához a vírusok, kémprogramok és más, kártevő szoftverek.
>
>

## <a name="next-steps"></a>Következő lépések
Ez a cikk bemutatta megvalósításához a Security Center javaslat "Az Endpoint Protection telepítése." Az Azure-ban a Microsoft Antimalware engedélyezésével kapcsolatos további tudnivalókért tekintse meg a következő dokumentumban:

* [A Microsoft Antimalware Felhőszolgáltatásokhoz és virtuális gépek](../security/azure-security-antimalware.md) --telepítése a Microsoft Antimalware.

Security Centerrel kapcsolatos további tudnivalókért lásd a következő dokumentumokat:

* [Biztonsági szabályzatok beállítása az Azure Security Center](security-center-policies.md) – megtudhatja, hogyan konfigurálhatja a biztonsági szabályzatokat.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, miként könnyítik meg a javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának figyelésére.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
