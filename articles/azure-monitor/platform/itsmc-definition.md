---
title: IT-szolgáltatásmenedzsmenti csatoló a Log Analytics
description: Ez a cikk áttekintést nyújt a IT-szolgáltatásmenedzsmenti csatolóokról (ITSMC), valamint arról, hogyan használható az alkalmazás a ITSM-munkaelemek figyelésére és felügyeletére Log Analytics és a problémák gyors megoldásához.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: b26643daede9e26f2bf1807ae99a6ced5d1cb08c
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901572"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>Az Azure és a ITSM-eszközök összekötése IT-szolgáltatásmenedzsmenti csatoló használatával

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Ez a cikk azt ismerteti, hogyan konfigurálható a IT-szolgáltatásmenedzsmenti csatoló (ITSMC) a Log Analytics a munkaelemek központilag kezelhetők.

## <a name="add-it-service-management-connector"></a>IT-szolgáltatásmenedzsmenti csatoló hozzáadása

A kapcsolatok létrehozása előtt hozzá kell adnia a ITSMC.

1. A Azure Portal válassza az **erőforrás létrehozása** lehetőséget:

   ![Az erőforrás létrehozása menüelemet megjelenítő képernyőkép.](media/itsmc-overview/azure-add-new-resource.png)

2. **It-szolgáltatásmenedzsmenti csatoló** keresése az Azure Marketplace-en. Válassza a **Létrehozás** lehetőséget:

   ![Képernyőkép, amely megjeleníti a létrehozás gombot az Azure piactéren.](media/itsmc-overview/add-itsmc-solution.png)

3. Az **La munkaterület** szakaszban válassza ki azt az Azure log Analytics-munkaterületet, ahol a ITSMC telepíteni kívánja.
   >[!NOTE]
   >
   > * A ITSMC csak Log Analytics munkaterületeken telepíthetők a következő régiókban: USA keleti régiója, USA 2. nyugati régiója, az USA déli középső régiója, az USA nyugati középső régiója, US Gov Arizona, US Gov Virginia, Közép-Kanada, Nyugat-Európa, Dél-Egyesült Királyság, Délkelet-Ázsia, Kelet-Japán, Közép-India és Délkelet-Ausztrália.

4. A **log Analytics munkaterület** szakaszban válassza ki azt az erőforráscsoportot, amelyben létre kívánja hozni a ITSMC-erőforrást:

   ![A Log Analytics munkaterület szakaszt bemutató képernyőkép.](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >A Microsoft Operations Management Suite (OMS) és a Azure Monitor közötti folyamatos áttérés részeként a OMS-munkaterületek már *log Analytics munkaterületek*.

5. Válassza az **OK** lehetőséget.

A ITSMC-erőforrás telepítésekor az ablak jobb felső sarkában megjelenik egy értesítés.

## <a name="create-an-itsm-connection"></a>ITSM-kapcsolatok létrehozása

A ITSMC telepítése után létrehozhat egy-egy kapcsolatokat.

A kapcsolatok létrehozásához elő kell készítenie a ITSM eszközt, hogy engedélyezze a ITSMC való kapcsolódást.  

Azon ITSM-termék alapján, amelyhez csatlakozik, válassza a következő hivatkozások egyikét az utasításokhoz:

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

Miután elő a ITSM-eszközöket, hajtsa végre a következő lépéseket a kapcsolatok létrehozásához:

1. Az **összes erőforrás** területen keresse meg a **ügyfélszolgálati (*a munkaterület neve*)**:

   ![A Azure Portal legutóbbi erőforrásait bemutató képernyőkép.](media/itsmc-overview/itsm-connections.png)

1. A bal oldali ablaktábla **munkaterület-adatforrások** területén válassza a **ITSM-kapcsolatok** elemet:

   ![Képernyőkép, amely megjeleníti a ITSM-kapcsolatok menüpontot.](media/itsmc-overview/add-new-itsm-connection.png)
1. Válassza a **kapcsolatok hozzáadása** lehetőséget.

1. A ITSM termékek/szolgáltatások című témakörben leírtak szerint határozza meg a kapcsolódási beállításokat:

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   >
   > Alapértelmezés szerint a ITSMC 24 óránként egyszer frissíti a kapcsolatok konfigurációs szolgáltatásait. Ha azonnal frissíteni szeretné a kapcsolatok adatait, hogy azok megfeleljenek a módosítások vagy a sablonok frissítéseinek, válassza a **szinkronizálás** gombot a kapcsolatok paneljén:
   >
   > ![Képernyőkép, amely megjeleníti a szinkronizálás gombot a kapcsolatok panelen.](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="use-itsmc"></a>ITSMC használata

   A ITSMC használatával riasztásokat hozhat létre Azure Monitor riasztásokból a ITSM eszközre.

## <a name="create-itsm-work-items-from-azure-alerts"></a>ITSM-munkaelemek létrehozása az Azure-riasztásokból

A ITSM-kapcsolatok létrehozása után létrehozhat munkaelemeket a ITSM-eszközön az Azure-riasztások alapján. A munkaelemek létrehozásához használja a ITSM műveletet a műveleti csoportokban.

A műveleti csoportok moduláris és újrafelhasználható módot biztosítanak az Azure-riasztások műveleteinek elindításához. A műveleti csoportokat metrikus riasztásokkal, műveletnapló riasztásokkal és Azure Log Analytics riasztásokkal is használhatja a Azure Portal.

> [!NOTE]
> A ITSM-kapcsolatok létrehozása után a szinkronizálási folyamat befejezéséhez 30 percet kell várnia.

### <a name="template-definitions"></a>Sablon-definíciók

   Vannak olyan munkaelem-típusok, amelyek használhatják a ITSM eszköz által definiált sablonokat.
A sablonok használatával meghatározhatja azokat a mezőket, amelyeket a rendszer a műveleti csoport részeként definiált rögzített értékek alapján automatikusan kitölt. A sablonokat a ITSM eszközben definiálhatja.
Megadhatja, hogy melyik sablont szeretné használni a műveleti csoport definíciójának részeként.

Műveleti csoportok létrehozásához kövesse az alábbi eljárást:

1. A Azure Portal válassza a  **riasztások** elemet.
2. A képernyő felső részén található menüben válassza a **kezelés műveletek** elemet:

    ![Képernyőkép, amely a műveletek kezelése menüelemet jeleníti meg.](media/itsmc-overview/action-groups-selection-big.png)

   Megjelenik a **műveleti csoport létrehozása** ablak.

3. Válassza ki azt az **előfizetést** és **erőforráscsoportot** , amelyben létre szeretné hozni a műveleti csoportot. Adja meg a műveleti csoport **nevét** és **megjelenítendő nevét** . Válassza a Next (tovább) lehetőséget **: értesítések**.

    ![A műveleti csoport létrehozása ablakot megjelenítő képernyőkép.](media/itsmc-overview/action-groups-details.png)

4. Az értesítési listán válassza a **Tovább: műveletek** elemet.
5. A műveletek listában válassza a **ITSM** elemet a **Művelettípus** listában. Adja meg a művelet **nevét** . Válassza a **Szerkesztés részleteit** jelölő toll gombot.

    ![A műveleti csoport definícióját bemutató képernyőkép.](media/itsmc-definition/action-group-pen.png)

6. Az **előfizetés** listában válassza ki azt az előfizetést, amelyben a log Analytics munkaterület található. A **kapcsolatok** listájában válassza ki a ITSM-összekötő nevét. Ezt a munkaterület neve követi majd. Például: MyITSMConnector (Sajátmunkaterület).

7. Válasszon **munkaelem** -típust.

8. Ha rögzített értékekkel rendelkező, beépített mezőket szeretne kitölteni, válassza az **egyéni sablon használata** lehetőséget. Ellenkező esetben válasszon egy meglévő [sablont](#template-definitions) a **sablon** listáról, és adja meg a rögzített értékeket a sablon mezőiben.

9. A művelet ITSM-definíciójának utolsó szakaszában megadhatja, hogy az egyes riasztások hány riasztást hozzanak létre. Ez a szakasz csak a keresési riasztások naplózására vonatkozik.

    * Ha a munkaelem legördülő menüben a "incidens" vagy a "riasztás" lehetőséget választja:
        * Ha bejelöli az egyes **konfigurációs elemek egyéni** munkaelemek létrehozása jelölőnégyzetet, minden riasztásban minden konfigurációs elem új munkaelemet fog létrehozni. A ITSM-rendszeren több munkaelem is szerepelhet egy konfigurációs elemnél.

            Például:
            1) 1. riasztás 3 konfigurációs elemmel: A, B, C – 3 munkaelemet fog létrehozni.
            2) 2. riasztás 1 konfigurációs elemmel: D – 1 munkaelemet fog létrehozni.

                **A folyamat végén 4 riasztás jelenik meg**
        * Ha törli az egyes **konfigurációs elemek egyéni munkaelemeinek létrehozása** jelölőnégyzet jelölését, akkor olyan riasztások jelennek meg, amelyek nem hoznak létre új munkaelemet. a munkaelemek a riasztási szabály szerint lesznek egyesítve.

            Például:
            1) 1. riasztás 3 konfigurációs elemmel: A, B, C – 1 munkaelemet fog létrehozni.
            2) 2. riasztás ugyanahhoz a riasztási szabályhoz az 1. fázisban 1 konfigurációs elemmel: a D – egyesítve lesz az 1. fázisban lévő munkaelembe.
            3) 3. riasztás egy másik riasztási szabályhoz 1 konfigurációs elemmel: az E-1 munkaelemet hoz létre.

                **A folyamat végén 2 riasztás lesz**

       ![A ITSM incidens ablakát megjelenítő képernyőkép.](media/itsmc-overview/itsm-action-configuration.png)

    * Ha a munkaelem legördülő menüben a "esemény" lehetőséget választja:
        * Ha a választógombok kiválasztásakor az egyes **naplókhoz tartozó egyéni munkaelemek létrehozása** lehetőséget választja, a rendszer minden egyes sorban létrehoz egy riasztást a napló keresési riasztási lekérdezés keresési eredményei között. A riasztás hasznos adatai között a Description (Leírás) tulajdonság a keresési eredmények sorát fogja tartalmazni.
        * Ha a választógombok kiválasztásakor az egyes **konfigurációs elemek egyéni** munkaelemek létrehozása lehetőséget választja, akkor minden egyes riasztás minden konfigurációs eleme létrehoz egy új munkaelemet. A ITSM-rendszeren több munkaelem is szerepelhet egy konfigurációs elemnél. Ez ugyanaz lesz, mint az incidens/riasztás szakaszban lévő jelölőnégyzet ellenőrzése.
    ![Képernyőkép, amely a ITSM esemény ablakát jeleníti meg.](media/itsmc-overview/itsm-action-configuration-event.png)

10. Válassza az **OK** lehetőséget.

Azure-riasztási szabály létrehozásakor vagy szerkesztésekor használjon egy ITSM műveletet tartalmazó műveleti csoportot. A riasztás indításakor a rendszer létrehozza vagy frissíti a munkaelemet a ITSM eszközben.

> [!NOTE]
>
>- A ITSM művelet díjszabásával kapcsolatos információkért tekintse meg a műveleti csoportok [díjszabási lapját](https://azure.microsoft.com/pricing/details/monitor/) .
>
>
>- A riasztási szabály definíciójának rövid leírása mezője 40 karakterre van korlátozva, ha a ITSM művelettel küldi el.

## <a name="next-steps"></a>Következő lépések

* [Hibaelhárítás az ITSM-összekötőben](./itsmc-resync-servicenow.md)
