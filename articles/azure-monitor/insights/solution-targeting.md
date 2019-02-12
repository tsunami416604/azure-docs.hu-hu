---
title: Figyelési megoldások az Azure monitorban célzó |} A Microsoft Docs
description: Figyelési megoldások célzó lehetővé teszi, hogy az ügynökök meghatározott figyelési megoldások korlátozza.  Ez a cikk ismerteti, hogyan hozhat létre egy hatókör-konfigurációt, és alkalmazhatja azt egy megoldás.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: monitoring
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: d1d2dd689cb389b6adfe1dd534e7c73e17f755f5
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55989185"
---
# <a name="targeting-monitoring-solutions-in-azure-monitor-preview"></a>Célcsoport-kezelési monitorozás az Azure Monitor (előzetes verzió)
Egy figyelési megoldás hozzáadásakor az előfizetés automatikusan telepített összes Windows és Linux-ügynökök az a Log Analytics-munkaterülethez kapcsolódó alapértelmezés szerint.  Érdemes a költségek kezelése és az ügynökök egy adott készletét korlátozásával megoldás összegyűjtött adatok mennyisége korlátozza.  Ez a cikk ismerteti, hogyan használható **megoldás célcsoportjának** Ez a szolgáltatás lehetővé teszi, hogy a alkalmazni egy hatókört a megoldások.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="how-to-target-a-solution"></a>A megoldás célja annak
A célcsoport-kezelési megoldás a következő szakaszokban leírtak szerint három lépésből áll. 


### <a name="1-create-a-computer-group"></a>1. Számítógépcsoport létrehozása
Hozzon létre egy hatókört szerepeltetni kívánt számítógépek megad egy [számítógépcsoport](../platform/computer-groups.md) az Azure monitorban.  A számítógép (csoport) napló lekérdezés alapján lehet vagy más forrásokból, például az Active Directory vagy a WSUS-csoportok importálása. Mint [alább ismertetett](#solutions-and-agents-that-cant-be-targeted), csak az Azure Monitor közvetlenül csatlakozó számítógépek megtalálható a hatókörben.

Után a munkaterületen létrehozott számítógép csoportot, majd fog foglalni egy hatókör-konfigurációt, amely egy vagy több megoldásokat is alkalmazható.
 
 
 ### <a name="2-create-a-scope-configuration"></a>2. A hatókör-konfiguráció létrehozása
 A **hatókör-konfiguráció** tartalmaz egy vagy több számítógép csoport és a egy vagy több megoldásokat is alkalmazható. 
 
 Hozzon létre egy hatókör-konfigurációt a következő eljárással.  

 1. Az Azure Portalon lépjen **Log Analytics-munkaterületek** , és válassza ki a munkaterületet.
 2. A munkaterület alatt tulajdonságainak **munkaterület adatforrásai** kiválasztása **hatókör-konfigurációk**.
 3. Kattintson a **Hozzáadás** hozhat létre egy új hatókör-konfigurációt.
 4. Adjon meg egy **neve** a hatókör-konfiguráció számára.
 5. Kattintson a **válassza ki a számítógépcsoportokat**.
 6. Válassza ki a számítógépcsoportot, Ön által létrehozott és igény szerint bármely más csoport adja hozzá a konfigurációhoz.  Kattintson a **Kiválasztás** gombra.  
 6. Kattintson a **OK** létrehozni a hatókör-konfigurációt. 


 ### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. A megoldás a hatókör-konfiguráció vonatkozik.
Miután egy hatókör-konfigurációt, majd alkalmazhatja azt egy vagy több megoldások.  Vegye figyelembe, hogy egy hatókör-konfigurációt is használható a több megoldásból, míg egyes megoldások csak használhat egy hatókör-konfigurációt.

A alkalmazni egy hatókör-konfigurációt a következő eljárással.  

 1. Az Azure Portalon lépjen **Log Analytics-munkaterületek** , és válassza ki a munkaterületet.
 2. Válassza ki a munkaterület tulajdonságainak **megoldások**.
 3. Kattintson a kívánt hatókörhöz a megoldás.
 4. A megoldás alatt tulajdonságainak **munkaterület adatforrásai** kiválasztása **megoldás célcsoportjának**.  Ha a beállítás nem érhető majd [Ez a megoldás nem adható meg célként](#solutions-and-agents-that-cant-be-targeted).
 5. Kattintson a **hatókör-konfiguráció hozzáadása**.  Ha már rendelkezik egy konfigurációt, akkor ez a beállítás nem érhető el ebben a megoldásban alkalmazott.  A meglévő konfigurációt egy másik hozzáadása előtt el kell távolítania.
 6. Kattintson a hatókör-konfigurációt a létrehozott.
 7. Tekintse meg a **állapot** annak érdekében, hogy megjeleníti a konfiguráció **sikeres**.  Ha az állapot azt jelzi, hogy a hibát, majd kattintson a jobb oldalán a konfigurációt, és válassza a három pontra **hatókör-konfiguráció szerkesztése** módosításokat.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Megoldások és az ügynökök, amelyek nem adható meg célként
Az alábbiakban az ügynökök és a megoldásokat, amelyek nem használható a megoldás célcsoportjának feltételeit.

- Csak megoldás célcsoportjának vonatkozik, amely az ügynökök üzembe megoldásokat.
- Csak megoldás célcsoportjának vonatkozik a Microsoft által biztosított megoldások.  Nem alkalmazható megoldások [saját magának vagy partnerek által létrehozott](solutions-creating.md).
- Csak szűrhet ügynökök, amelyek közvetlenül csatlakozhat az Azure Monitor ki.  Megoldások automatikusan telepíti-e már szerepel a biztonsági hatókör-konfiguráció csatlakoztatott az Operations Manager felügyeleti csoport részét képező összes ügynököt.

### <a name="exceptions"></a>Kivételek
Megoldás célcsoportjának nem használható a következő megoldások annak ellenére, hogy illeszkedjenek a megadott feltételeknek.

- Az ügynök állapot értékelése

## <a name="next-steps"></a>További lépések
- További tudnivalók a figyelési megoldások többek között a megoldások telepítéséhez a környezetben jelenleg elérhető [hozzáadása az Azure Log Analytics figyelési megoldások a munkaterülethez](solutions.md).
- Tudjon meg többet, számítógépcsoportok létrehozását [számítógépcsoportokat az Azure monitorban lekérdezések naplózását](../platform/computer-groups.md).
