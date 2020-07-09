---
title: Standard szintű, fokozott biztonságú Azure Security Center
description: " Megtudhatja, hogyan hozhatja ki a Azure Security Center Standard szintűt a fokozott biztonság érdekében. "
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77912052"
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Regisztráció az Azure Security Center Standardra a biztonság növelése érdekében
Frissítsen Security Center szabványra, hogy kihasználhassa a fokozott biztonság kezelése és a fenyegetések elleni védelem előnyeit a hibrid felhőalapú számítási feladatokhoz. Megpróbálkozhat a standard szintű ingyenes kipróbálással is. További információért tekintse meg a Security Center [díjszabási oldalát](https://azure.microsoft.com/pricing/details/security-center/) .

A standard szintű Security Center a következőket tartalmazza:

- **Hibrid biztonság** – az összes helyszíni és Felhőbeli számítási feladatra kiterjedő egységes biztonsági nézet. Alkalmazza a biztonsági házirendeket, és folyamatosan mérje fel a hibrid Felhőbeli számítási feladatok biztonságát a biztonsági normáknak való megfelelés biztosítása érdekében. Különböző forrásokból, például tűzfalakból és egyéb partneri megoldásokból származó biztonsági adatokat gyűjthet, kereshet és elemez.
- **Biztonsági riasztások** – speciális elemzések és a Microsoft intelligens biztonsági gráf használata a fejlődő cyber-támadásokkal szemben. A támadásokat és a napi használatot lehetővé téve a beépített viselkedés-elemzés és a gépi tanulás kihasználása. Figyelheti a hálózatokat, a gépeket és a Felhőbeli szolgáltatásokat a beérkező támadásokhoz és a szabálysértés utáni tevékenységekhez. Egyszerűsítse a nyomozást az interaktív eszközökkel és a kontextusbeli fenyegetések intelligenciával.
- **Hozzáférés és alkalmazás-vezérlőelemek** – a kártevők és más nemkívánatos alkalmazások letiltása az adott számítási feladatokhoz igazított engedélyezési Javaslatok alkalmazásával és a gépi tanulással. Csökkentse a hálózati támadási felületet az Azure-beli virtuális gépeken futó felügyeleti portok igény szerinti, felügyelt hozzáférésével, drasztikusan csökkentve a találgatásos és más hálózati támadásokkal szembeni kitettséget.

## <a name="detecting-unprotected-resources"></a>Nem védett erőforrások észlelése
A Security Center automatikusan észleli azokat az Azure-előfizetéseket vagy munkaterületeket, amelyeken nincs engedélyezve a Security Center Standard. Ez a Security Center ingyenes verzióját használó Azure-előfizetéseket és az olyan munkaterületeket is magában foglalja, amelyeken nincs engedélyezve a biztonsági megoldás.

A teljes Azure-előfizetést a standard szintre is frissítheti, amelyet az előfizetés összes támogatott erőforrása örököl. A standard szint munkaterületre való alkalmazása a munkaterületre jelentett összes erőforrásra vonatkozik.

> [!NOTE]
> Érdemes lehet felügyelni a költségeket, és korlátozni a megoldáshoz összegyűjtött adatok mennyiségét, ha korlátozza azt egy adott ügynökre. A [megoldás célcsoportja](../operations-management-suite/operations-management-suite-solution-targeting.md) lehetővé teszi, hogy hatókört alkalmazzon a megoldásra, és a munkaterületen lévő számítógépek egy részhalmazát célozza meg. Ha megoldás-célcsoportot használ, Security Center listázza a munkaterületet úgy, hogy ne legyen megoldás.
>
>

## <a name="upgrade-an-azure-subscription-or-workspace"></a>Azure-előfizetés vagy-munkaterület frissítése
Előfizetés vagy munkaterület frissítése a standard szintre:
1. A Security Center főmenüjében válassza az **Első lépések** elemet.
  ![Bevezetés](./media/security-center-onboarding/get-started.png)
2. A **Frissítés** területen a Security Center felsorolja az előkészítésre jogosult előfizetéseket és munkaterületeket. 
   - A **Próbaverzió alkalmazása** elemre kattintva megtekintheti előfizetéseinek és munkaterületeinek listáját, valamint a próbaidőszakkal kapcsolatos jogosultsági állapotukat.
   -    Frissítheti azokat az előfizetéseket és munkaterületeket, amelyek nem jogosultak a próbaidőszakra.
   -    A próbaidőszak elkezdéséhez válassza ki az erre jogosult munkaterületeket és előfizetéseket.
3.  A **Próbaverzió indítása** gombbal elindítja a próbaidőszakot a kiválasztott előfizetéseken.
  ![Előfizetés kiválasztása](./media/security-center-onboarding/select-subscription.png)


   > [!NOTE]
   > A Security Center ingyenes képességei csak az Azure-beli virtuális gépekre és VMSS vonatkoznak. Az ingyenes képességek nincsenek alkalmazva a nem Azure-beli számítógépekre. Ha a standard lehetőséget választja, a rendszer az összes Azure-beli virtuális GÉPRE, virtuálisgép-méretezési csoportra és a munkaterületnek jelentő nem Azure-beli számítógépekre alkalmazza a standard képességeket. Javasoljuk, hogy az Azure-ban és a nem Azure-beli erőforrásokhoz való fokozott biztonság érdekében alkalmazzon standard szintű megoldást.
   >

## <a name="onboard-non-azure-computers"></a>Nem Azure-beli számítógépek előkészítése
A Security Center a nem Azure-számítógépek biztonsági állapotát is képes monitorozni, azonban ehhez elő kell készítenie ezeket az erőforrásokat. A nem Azure-beli számítógépeket az **első lépések** panelen vagy a **számítás** panelen adhatja hozzá. Mindkét módszert bemutatjuk.

### <a name="add-new-non-azure-computers-from-getting-started"></a>Új nem Azure-beli számítógépek hozzáadása az **első lépésekhez**

1. Visszatérés a **kezdéshez**.
2. Válassza a **Bevezetés** lapot.

   ![Nem Azure](./media/security-center-onboarding/non-azure.png)

3. Kattintson az **Új nem Azure-beli számítógépek hozzáadása** pont alatt található **Konfigurálás** elemre. Megjelenik a Log Analytics-munkaterületek listája. Ha van ilyen, a lista tartalmazza azt az alapértelmezett munkaterületet is, amelyet a Security Center hozott létre, amikor az automatikus kiépítés engedélyezve volt. Válassza ki ezt a munkaterületet, vagy egy másik használni kívánt munkaterületet.

   ![Nem Azure-beli számítógép hozzáadása][7]

Ha már van munkaterülete, akkor az új, **nem Azure-beli számítógépek hozzáadása**szakaszban szerepel. Hozzáadhat számítógépeket egy meglévő munkaterülethez, vagy létrehozhat egy új munkaterületet. Új munkaterület létrehozásához kattintson az **Új munkaterület hozzáadása**hivatkozásra.

### <a name="add-new-non-azure-computers-from-compute"></a>Új nem Azure-beli számítógépek hozzáadása a **számítási** feladatokhoz

**Új munkaterület létrehozása és számítógép hozzáadása**

1. Az **új nem Azure-beli számítógépek hozzáadása**területen válassza az **Új munkaterület hozzáadása**lehetőséget.

   ![Új munkaterület hozzáadása][4]

2. Az **Security and Audit**területen válassza az **OMS-munkaterület** lehetőséget egy új munkaterület létrehozásához.
   > [!NOTE]
   > Az OMS-munkaterületeket mostantól Log Analytics-munkaterületeknek nevezzük.
3. A **OMS munkaterületen**adja meg a munkaterület adatait.
4. A **OMS munkaterületen**kattintson **az OK gombra**. Miután kiválasztotta az OK gombot, megjelenik egy hivatkozás, amely a munkaterület-AZONOSÍTÓhoz tartozó Windows-vagy Linux-ügynök és-kulcsok letöltésére használható az ügynök konfigurálásához.
5. A **Security and Audit**alatt kattintson **az OK gombra**.

**Válasszon ki egy meglévő munkaterületet, és adja hozzá a számítógépet**

A következő ábrán látható módon adhat hozzá számítógépet a **Onboarding**munkafolyamatból. Számítógépet a **számítási**munkafolyamat követésével is hozzáadhat. Ebben a példában a **számítást**használjuk.

1. Térjen vissza Security Center főmenüjéhez és az **Áttekintés** irányítópulthoz.

   ![Áttekintés][5]

2. Válassza a **számítási & alkalmazások**lehetőséget.
3. A **számítás & alkalmazások**területen válassza a **számítógépek hozzáadása**elemet.

   ![Számítás panel][6]

4. Az **új nem Azure-beli számítógépek hozzáadása**területen válassza ki azt a munkaterületet, amelyhez a számítógépet hozzá szeretné kapcsolni, és kattintson a **számítógépek hozzáadása**lehetőségre.

   ![Számítógépek hozzáadása][7]

   A **közvetlen ügynök** panel egy hivatkozást tartalmaz egy Windows-vagy Linux-ügynök letöltésére, valamint az ügynök konfigurálásához használandó munkaterület-azonosítóra és kulcsokra.

## <a name="next-steps"></a>További lépések
Ebből a cikkből megtudhatta, hogyan végezheti el az Azure-beli és nem Azure-erőforrások bevezetését az Security Center fokozott biztonságának kihasználása érdekében. A beérkező erőforrásokkal kapcsolatos további információkért lásd:

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
