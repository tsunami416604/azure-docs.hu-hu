---
title: Endpoint Protection-problémák kezelése a Azure Security Centerkal | Microsoft Docs
description: Ismerje meg, hogyan kezelheti Azure Security Center az Endpoint Protection szolgáltatással kapcsolatos problémákat.
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
ms.openlocfilehash: 4274b3684a2e9e79385a561b1cad426b4ffd954e
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68609941"
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>Az Endpoint Protection szolgáltatással kapcsolatos problémák kezelése Azure Security Center
Azure Security Center figyeli a kártevők elleni védelem állapotát, és jelentést készít az Endpoint Protection-problémák paneljén. Security Center kiemeli a problémákat, például az észlelt fenyegetéseket és a nem megfelelő védelmet, amely lehetővé teszi, hogy a virtuális gépek (VM) és a számítógépek sebezhetőek legyenek a kártevők elleni fenyegetésekkel szemben. Az **Endpoint Protection-problémák**alatt lévő információk használatával meghatározhatja az azonosított problémák megoldására szolgáló tervet.

Security Center a következő Endpoint Protection-problémákat jelenti:

- Az Endpoint Protection nincs telepítve az Azure-beli virtuális gépeken – ezen az Azure-beli virtuális gépeken nincs telepítve támogatott antimalware-megoldás.
- Az Endpoint Protection nincs telepítve a nem Azure-beli számítógépeken – A rendszer nem telepíti a támogatott antimalware-t ezeken a nem Azure-beli számítógépeken.
- Endpoint Protection állapota:

  - Az aláírás elavult – antimalware-megoldás van telepítve ezekre a virtuális gépekre és számítógépekre, de a megoldás nem rendelkezik a legújabb antimalware-aláírásokkal.
  - Nincs valós idejű védelem – antimalware-megoldás van telepítve ezekre a virtuális gépekre és számítógépekre, de nincs beállítva a valós idejű védelemre.   Lehet, hogy a szolgáltatás le van tiltva, vagy előfordulhat, hogy a Security Center nem tudja megszerezni az állapotot, mert a megoldás nem támogatott. A támogatott megoldások listáját lásd: [partner-integráció](security-center-os-coverage.md#supported-endpoint-protection-solutions) .
  - Nem jelentéskészítés – a rendszer egy antimalware-megoldást telepít, de nem küld jelentést.
  - Ismeretlen – az antimalware megoldás telepítve van, de az állapota ismeretlen, vagy ismeretlen hibát jelentett.

    > [!NOTE]
    > Lásd: a [biztonsági megoldások integrálása](security-center-os-coverage.md#supported-endpoint-protection-solutions) a Security Center integrált Endpoint Protection biztonsági megoldások listájára.
    >
    >

## <a name="implement-the-recommendation"></a>A javaslat implementálása
Az Endpoint Protection szolgáltatással kapcsolatos problémák Security Centerban való javaslatként jelennek meg.  Ha a környezete sebezhető a kártevők elleni fenyegetésekkel szemben, ez a javaslat a **javaslatok** és a **számítás**területen jelenik meg. Az **Endpoint Protection-problémák irányítópultjának**megtekintéséhez követnie kell a számítási munkafolyamatot.

Ebben a példában a **számítást**fogjuk használni.  Bemutatjuk, hogyan telepíthet antimalware-t az Azure-beli virtuális gépeken és a nem Azure-beli számítógépeken.

## <a name="install-antimalware-on-azure-vms"></a>Antimalware telepítése Azure-beli virtuális gépeken

1. Válassza a Security Center főmenü vagy az **Áttekintés**területen a **számítási & alkalmazások** lehetőséget.

   ![Számítás kiválasztása][1]

2. A **számítás**területen válassza az **Endpoint Protection-problémák**elemet. Megnyílik az **Endpoint Protection-problémák** irányítópult.

   ![Endpoint Protection-problémák kiválasztása][2]

   Az irányítópult tetején a következőket nyújtja:

   - Telepített Endpoint Protection-szolgáltatók – felsorolja a Security Center által azonosított különböző szolgáltatókat.
   - Telepített Endpoint Protection-állapot – megjeleníti azon virtuális gépek és számítógépek állapotát, amelyeken telepítve van az Endpoint Protection megoldás. A diagram a virtuális gépek és a kifogástalan állapotú számítógépek számát jeleníti meg, valamint a nem megfelelő védelemmel ellátott számot.
   - Kártevő észlelve – megjeleníti azoknak a virtuális gépeknek és számítógépeknek a számát, ahol a Security Center kártevőt észlelt.
   - Megtámadott számítógépek – azoknak a virtuális gépeknek és számítógépeknek a számát jeleníti meg, amelyeken a Security Center kártevők általi támadásokat jelentett.

   Az irányítópult alján található az Endpoint Protection azon problémáinak listája, amelyek a következő információkat tartalmazzák:  

   - **Összesen** – a probléma által érintett virtuális gépek és számítógépek száma.
   - A probléma által érintett virtuális gépek és számítógépek számát összesítő sáv. A sáv színei azonosítják a prioritást:

      - Piros-magas prioritás, és azonnal foglalkozni kell
      - Narancssárga – közepes prioritás, és a lehető leghamarabb meg kell oldani

3. Válassza ki **Az Azure-beli virtuális gépeken nincs telepítve az Endpoint Protection**szolgáltatást.

   ![Az Azure-beli virtuális gépeken nem telepített Endpoint Protection kiválasztása][3]

4. Az **Azure-beli virtuális gépeken nincs telepítve az Endpoint Protection** azon Azure-beli virtuális gépek listája, amelyeken nincs telepítve az antimalware.  Dönthet úgy, hogy a listában szereplő összes virtuális gépen telepíti az antimalware szoftvert, vagy az egyes virtuális gépeket az adott virtuális gépre kattintva telepítheti az antimalware-t.
5. Az **Endpoint Protection kiválasztása**területen válassza ki a használni kívánt Endpoint Protection-megoldást. Ebben a példában válassza a **Microsoft antimalware**elemet.
6. Az Endpoint Protection-megoldással kapcsolatos további információk jelennek meg. Kattintson a **Létrehozás** gombra.

## <a name="install-antimalware-on-non-azure-computers"></a>Antimalware telepítése nem Azure-beli számítógépeken

1. Térjen vissza az **Endpoint Protection** -problémákhoz, és válassza ki **a nem Azure-beli számítógépeken nincs telepítve az Endpoint Protection**szolgáltatást.

   ![A nem Azure-beli számítógépeken nem telepített Endpoint Protection kiválasztása][4]

2. A **nem Azure-beli számítógépeken nincs telepítve az Endpoint Protection**területen válasszon ki egy munkaterületet. Megnyílik egy Azure Monitor naplók keresési lekérdezése, amely a munkaterületre szűrve jelenik meg, és felsorolja a hiányzó antimalware-számítógépeket. További információért válasszon egy számítógépet a listából.

   ![Azure Monitor naplók keresése][5]

Megnyílik egy másik keresési eredmény, amely csak az adott számítógéphez szűrt adatokat.

  ![Azure Monitor naplók keresése][6]

> [!NOTE]
> Javasoljuk, hogy az Endpoint Protectiont az összes virtuális gép és számítógép számára kiépítve azonosítsa és távolítsa el a vírusokat, kémprogramokat és egyéb kártevő szoftvereket.
>
>

## <a name="next-steps"></a>További lépések
Ez a cikk bemutatja, hogyan implementálhatja a "install Endpoint Protection" Security Center javaslatot. Ha többet szeretne megtudni a Microsoft antimalware használatának engedélyezéséről az Azure-ban, tekintse meg a következő dokumentumot:

* [Microsoft antimalware Cloud Services és Virtual Machineshoz](../security/fundamentals/antimalware.md) – megtudhatja, hogyan helyezheti üzembe a Microsoft antimalware-t.

Ha többet szeretne megtudni a Security Centerről, tekintse meg a következő dokumentumokat:

* [Biztonsági szabályzatok beállítása Azure Security Centerban](tutorial-security-policy.md) – útmutató a biztonsági házirendek konfigurálásához.
* [Biztonsági javaslatok kezelése Azure Security Centerban](security-center-recommendations.md) – megtudhatja, hogyan segítheti az ajánlásokat az Azure-erőforrások védelmében.
* [Biztonsági állapot figyelése Azure Security Centerban](security-center-monitoring.md) – megtudhatja, hogyan figyelheti az Azure-erőforrások állapotát.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
