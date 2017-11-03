---
title: "Bevezetés az Azure Security Center szabványos a fokozott biztonság |} Microsoft Docs"
description: " Ismerje meg, hogyan való érheti el a Azure Security Center szabványos a fokozott biztonság. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: terrylan
ms.openlocfilehash: a6394b1b02ebfe518dc2f2b7f65eb677bb0ba5f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Bevezetés az Azure Security Center szabványos a fokozott biztonság
Frissítse a Security Center szabványos biztonságosabb kezelés és a hibrid felhő munkaterhelések veszélyforrások elleni védelem előnyeinek kihasználása érdekében.  Standard szabad 60 napig próbálkozhat. Tekintse meg a Security Center [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/security-center/) további információt.

A Security Center Standard tartalmazza:

- **Hibrid biztonsági** – összes az helyszíni biztonsági egyesítve láthassák beolvasása és a felhő-munkaterhelések. Biztonsági házirendeket alkalmazhat, és folyamatosan felmérheti a hibrid felhő-munkaterhelések biztonsági előírásoknak való megfelelés biztosításához biztonságát. Többféle forrásból, például tűzfalakból és más partneri megoldásból származó biztonsági adatokat gyűjthet, kereshet és elemezhet.
- **A fenyegetésészlelés speciális** -speciális elemzés és a Microsoft intelligens biztonsági diagramot úgy, hogy él beolvasása fejlődnek számítógépes-támadások során használja.  A beépített viselkedéselemzés és a gépi tanulás használatával azonosíthatja a támadásokat és a biztonsági rések nulladik napi kihasználását. Figyelheti a bejövő támadásokat és az incidens utáni tevékenységeket a hálózatokon, a gépeken és a felhőszolgáltatásokon. Interaktív eszközökkel és fenyegetésekkel kapcsolatos környezetalapú tudásbázissal egyszerűsítheti a vizsgálatot.
- **Hozzáférés és az alkalmazás** -blokk kártevő szoftverek és más nemkívánatos alkalmazások által engedélyezett javaslatok alkalmazása az adott munkaterhelés konkrét igazodó, és gépi tanulási technológiával. A hálózati támadási felület csökkentése just-in-time, ellenőrzött hozzáféréssel rendelkező felügyeleti portokhoz Azure virtuális gépeken, ami rendkívüli mértékben csökkenti a találgatásos és egyéb hálózati támadásoktól.

## <a name="detecting-unprotected-resources"></a>Nem védett erőforrások észlelése     
A Security Center automatikusan észleli a bármely Azure-előfizetésekre vagy nincs engedélyezve a Security Center szabványos munkaterületek. Ez magában foglalja a Security Center szabad és munkaterületek, amelyek nem rendelkeznek a biztonsági megoldás használata Azure-előfizetések.

Teljes Azure-előfizetés váltson Standard csomagra, amely örökli az előfizetésen belüli összes erőforrást, vagy megadhat egy egyedi házirendet csak egy meghatározott erőforrás csoport frissítése. Ha az erőforráscsoport házirend-beállítások egyedi, a Security Center nem bírálja felül a árképzési házirendek Ha az előfizetés váltson Standard csomagra. A Standard alkalmazása réteg egy előfizetéshez csak a virtuális gépek az előfizetéshez a Security Center által létrehozott munkaterületek jelentő vonatkozik. Alkalmazza a Standard szint a munkaterületre vonatkozik a munkaterületre reporting összes erőforrást.

> [!NOTE]
> Érdemes lehet a költségek kezelésére, és egy meghatározott ügynökök korlátozásával megoldás gyűjtött adatok mennyiségét. [Célcsoport-kezelési megoldás](../operations-management-suite/operations-management-suite-solution-targeting.md) lehetővé teszi a hatókört alkalmazza a megoldáshoz, és a cél számítógépek a munkaterület egy részét.  Célcsoport-kezelési megoldást használ, ha a Security Center a munkaterületen a megoldás nem tartalmazóként sorolja fel.
>
>

## <a name="upgrade-an-azure-subscription"></a>Frissítés Azure-előfizetés
Standard minden előfizetés frissítése:
1. Az a Security Center fő menüben válassza a **bevezetési**.
2. A **speciális biztonsági bevezetési**, a Security Center a jogosult a bevezetési előfizetések sorolja fel. Frissítheti az összes felsorolt előfizetések kiválasztásával **alkalmazása Standard csomag**.

  ![Minden előfizetés frissítése][1]

Az egyes előfizetői a Standard frissítése: frissítheti egy előfizetés **bevezetési** kiválasztásával **Standard csomagra vonatkoznak**. A Standard egy erőforráscsoportot az előfizetéshez tartozó frissítéséhez válassza ki az előfizetést:
1. Válasszon egy előfizetést.  **Biztonsági házirend** információt nyújt az erőforráscsoportot, az előfizetés szerepel.
2. Válassza ki az előfizetés vagy egy erőforráscsoportot.

  ![Minden előfizetés frissítése][2]

3. Válassza ki **szabványos** Standard szabad frissítésével.
4. Kattintson a **Mentés** gombra.

> [!NOTE]
> Előfizetés frissítése a Standard kapcsol be [automatikus kiépítés](security-center-enable-data-collection.md) Ha korábban le volt tiltva. Azt javasoljuk, hogy a figyelés ügynökök automatikus kiépítés.
>
>

## <a name="upgrade-a-workspace"></a>A munkaterület frissítése
A munkaterület jelentéskészítés összes erőforrás vonatkozik Standard alkalmazása a munkaterületre.

1. Lépjen vissza a **bevezetési** panelen.
2. Jelöljön ki egy munkaterületet.

  ![A munkaterület frissítése][8]

3. Válassza ki **szabványos** frissítéséhez.  
4. Kattintson a **Mentés** gombra.

   > [!NOTE]
   > Nincs olyan forgatókönyvekben, ahol nincs szabad vagy Standard alkalmazza a munkaterületre. Ha ingyenes, a Security Center szabad képességek a rendszer az az Azure virtuális gépek csak alkalmazza. A szabad képességek az-Azure számítógépek nem érvényesek. Ha Standard lehetőséget választja, a szabványos képességek minden Azure virtuális gépek és az Azure-küldő számítógépek hardverhibái is a munkaterület érvényesek. Azt javasoljuk, hogy a speciális biztonsági biztosít az Azure és az-Azure erőforrások Standard alkalmazásra.
   >
   >

## <a name="onboard-non-azure-computers"></a>A bevezetni az Azure-számítógépek
A Security Center a nem Azure-számítógépek biztonsági állapotát is képes monitorozni, azonban ehhez elő kell készítenie ezeket az erőforrásokat. -Azure számítógépeket adhat hozzá a **bevezetési** panel vagy a **számítási** panelen. Mindkét módszer végigvezetjük.

### <a name="add-new-non-azure-computers-from-onboarding"></a>Új-Azure számítógépek hozzáadása a bevezetése

1. Lépjen vissza **bevezetési**.   
2. Válassza ki **kívánja hozzáadni az új-Azure számítógépek**.

  ![-Azure számítógép hozzáadása][3]

Ha meglévő munkaterületek, alatt vannak felsorolva **nem Azure-új számítógépek hozzáadása**. Számítógépek hozzáadása egy meglévő munkaterülettel, vagy hozzon létre egy új munkaterületet. Hozzon létre egy új munkaterületet, kattintson a hivatkozásra **adja hozzá az új munkaterületet**.

Mindkét módszer végigvesszük:

- Hozzon létre egy új munkaterületet, és a számítógép hozzáadása
- Válasszon egy meglévő munkaterületet, és a számítógép hozzáadása

**Hozzon létre egy új munkaterületet, és a számítógép hozzáadása**

1. A **adja hozzá az új-Azure számítógépek**, jelölje be **adja hozzá az új munkaterületet**.

   ![Új munkaterület hozzáadása][4]

2. A **biztonsági és naplózási**, jelölje be **OMS-munkaterület** számára hozzon létre egy új munkaterületet.
3. A **OMS-munkaterület**, írja be a munkaterület adatait.
4. A **OMS-munkaterület**, jelölje be **OK**.  Miután kiválasztotta az OK gombra, elérhetővé válik egy hivatkozást egy Windows vagy Linux-ügynök és a kulcsok a letölthető a az ügynök konfigurálásáról a használatára a munkaterület azonosítója.
5. A **biztonsági és naplózási**, jelölje be **OK**.

**Válasszon egy meglévő munkaterületet, és a számítógép hozzáadása**

A számítógépet hozzáadhatja a munkafolyamatot a következő **bevezetési**, ahogy fent látható. Számítógép hozzáadása a munkafolyamatot a következő **számítási**. A jelen példában használjuk **számítási**.

1. Térjen vissza a főmenübe Security Center és **áttekintése** irányítópult.

   ![Áttekintés][5]

2. Válassza ki a **számítási** csempére.
3. A **számítási**, jelölje be **vesz fel számítógépeket**.

   ![Számítási panel][6]

4. A **adja hozzá az új-Azure számítógépek**, csatlakoztassa a számítógépet, és kattintson a munkaterület kiválasztása **számítógépek hozzáadása**.

   ![Számítógépek hozzáadása][7]

 A **közvetlen ügynök** panel hivatkozást biztosít egy Windows vagy Linux-ügynök letöltése és a kulcsok az használható az ügynök konfigurálásáról a munkaterület azonosítója.   

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta, hogyan előkészítésére Azure és az Azure-erőforrások ahhoz, hogy a Security Center speciális biztonsági.  Több, az ehhez a előkészítve erőforrások, lásd:

- [Az adatgyűjtés engedélyezése](security-center-enable-data-collection.md)
- [Fenyegetésfelderítési jelentés](security-center-threat-report.md)
- [Csak a virtuális gép elérhető](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-onboarding/onboard.png
[2]: ./media/security-center-onboarding/onboard-subscription.png
[3]: ./media/security-center-onboarding/add-non-azure-resource.png
[4]: ./media/security-center-onboarding/create-workspace.png
[5]: ./media/security-center-onboarding/overview.png
[6]: ./media/security-center-onboarding/compute-blade.png
[7]: ./media/security-center-onboarding/add-non-azure-computer.png
[8]: ./media/security-center-onboarding/onboard-workspace.png
