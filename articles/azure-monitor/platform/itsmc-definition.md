---
title: IT-szolgáltatásmenedzsmenti csatoló a Log Analytics
description: Ez a cikk áttekintést nyújt a IT-szolgáltatásmenedzsmenti csatolóokról (ITSMC), valamint arról, hogyan használható az alkalmazás a ITSM-munkaelemek figyelésére és felügyeletére Log Analytics és a problémák gyors megoldásához.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: c6bac44e6f7212344463665840f180732970c3d4
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657158"
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

3. A **OMS munkaterület** szakaszban válassza ki azt az Azure log Analytics-munkaterületet, ahol telepíteni szeretné a ITSMC-t.
   >[!NOTE]
   >
   > * A Microsoft Operations Management Suite (OMS) és a Azure Monitor közötti folyamatos áttérés részeként a OMS-munkaterületek már *log Analytics munkaterületek*.
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

- [System Center Service Manager](./itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](./itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Megjelenő](./itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](./itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Miután elő a ITSM-eszközöket, hajtsa végre a következő lépéseket a kapcsolatok létrehozásához:

1. Az **összes erőforrás** területen keresse meg a **ügyfélszolgálati (*a munkaterület neve*)**:

   ![A Azure Portal legutóbbi erőforrásait bemutató képernyőkép.](media/itsmc-overview/itsm-connections.png)

1. A bal oldali ablaktábla **munkaterület-adatforrások** területén válassza a **ITSM-kapcsolatok** elemet:

   ![Képernyőkép, amely megjeleníti a ITSM-kapcsolatok menüpontot.](media/itsmc-overview/add-new-itsm-connection.png)
   Ez az oldal a kapcsolatok listáját jeleníti meg.
1. Válassza a **kapcsolatok hozzáadása** lehetőséget.

4. Adja meg a [ITSM-termékekkel/szolgáltatásokkal kapcsolatos ITSMC-kapcsolatok konfigurálása](./itsmc-connections.md)című témakörben leírtak szerinti kapcsolatbeállításokat.

   > [!NOTE]
   >
   > Alapértelmezés szerint a ITSMC 24 óránként egyszer frissíti a kapcsolatok konfigurációs szolgáltatásait. Ha azonnal frissíteni szeretné a kapcsolatok adatait, hogy azok megfeleljenek a módosítások vagy a sablonok frissítéseinek, válassza a **szinkronizálás** gombot a kapcsolatok paneljén:
   >
   > ![Képernyőkép, amely megjeleníti a szinkronizálás gombot a kapcsolatok panelen.](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="use-itsmc"></a>ITSMC használata

   A ITSMC használatával munkaelemeket hozhat létre az Azure-riasztásokból, Log Analytics a riasztásokból, és Log Analytics naplóbejegyzéseket.

## <a name="template-definitions"></a>Sablon-definíciók

   Vannak olyan munkaelem-típusok, amelyek használhatják a ITSM eszköz által definiált sablonokat.
A sablonok használatával meghatározhatja azokat a mezőket, amelyeket a rendszer a műveleti csoport részeként definiált rögzített értékek alapján automatikusan kitölt. A sablonokat a ITSM eszközben definiálhatja.
Megadhatja, hogy melyik sablont szeretné használni a műveleti csoport definíciójának részeként.

## <a name="create-itsm-work-items-from-azure-alerts"></a>ITSM-munkaelemek létrehozása az Azure-riasztásokból

A ITSM-kapcsolatok létrehozása után létrehozhat munkaelemeket a ITSM-eszközön az Azure-riasztások alapján. A munkaelemek létrehozásához használja a ITSM műveletet a műveleti csoportokban.

A műveleti csoportok moduláris és újrafelhasználható módot biztosítanak az Azure-riasztások műveleteinek elindításához. A műveleti csoportokat metrikus riasztásokkal, műveletnapló riasztásokkal és Azure Log Analytics riasztásokkal is használhatja a Azure Portal.

> [!NOTE]
> A ITSM-kapcsolatok létrehozása után a szinkronizálási folyamat befejezéséhez 30 percet kell várnia.

Munkaelemek létrehozásához kövesse az alábbi eljárást:

1. A Azure Portal válassza a  **riasztások** elemet.
2. A képernyő felső részén található menüben válassza a **kezelés műveletek** elemet:

    ![Képernyőkép, amely a műveletek kezelése menüelemet jeleníti meg.](media/itsmc-overview/action-groups-selection-big.png)

   Megjelenik a **műveleti csoport létrehozása** ablak.

3. Válassza ki azt az **előfizetést** és **erőforráscsoportot** , amelyben létre szeretné hozni a műveleti csoportot. Adja meg a műveleti csoport **nevét** és **megjelenítendő nevét** . Válassza a Next (tovább) lehetőséget **: értesítések**.

    ![A műveleti csoport létrehozása ablakot megjelenítő képernyőkép.](media/itsmc-overview/action-groups-details.png)

4. Az értesítési listán válassza a **Tovább: műveletek** elemet.
5. A műveletek listában válassza a **ITSM** elemet a **Művelettípus** listában. Adja meg a művelet **nevét** . Válassza a **Szerkesztés részleteit** jelölő toll gombot.
6. Az **előfizetés** listában válassza ki azt az előfizetést, amelyben a log Analytics munkaterület található. A **kapcsolatok** listájában válassza ki a ITSM-összekötő nevét. Ezt a munkaterület neve követi majd. Például: MyITSMConnector (Sajátmunkaterület).

7. Válasszon **munkaelem** -típust.

8. Ha rögzített értékekkel rendelkező, beépített mezőket szeretne kitölteni, válassza az **egyéni sablon használata** lehetőséget. Ellenkező esetben válasszon egy meglévő [sablont](#template-definitions) a **sablon** listáról, és adja meg a rögzített értékeket a sablon mezőiben.

9. Ha **az egyes konfigurációs elemekhez az egyes** munkaelemek létrehozása lehetőséget választja, minden konfigurációs elemnek saját munkaeleme lesz. A konfigurációs elemek egy munkaelemet kapnak. A rendszer a létrehozandó riasztások alapján frissíti.

    * Abban az esetben, ha a munkaelem legördülő menüben a "incidens" vagy a "riasztás" lehetőséget választotta: Ha törli az egyes **konfigurációs elemek egyéni munkaelemeinek létrehozása** jelölőnégyzet jelölését, minden riasztás új munkaelemet fog létrehozni. Egy konfigurációs elemmel több riasztás is lehet.

       ![A ITSM incidens ablakát megjelenítő képernyőkép.](media/itsmc-overview/itsm-action-configuration.png)

    * Ha a munkaelem legördülő menüben a "esemény" lehetőséget választja: Ha a választógombok kiválasztásakor a különböző munkaelemek **létrehozása** lehetőséget választja, minden riasztás új munkaelemet fog létrehozni. Ha a választógombok kiválasztásakor az egyes **konfigurációs elemek egyéni** munkaelemek létrehozása lehetőséget választja, minden konfigurációs elemnek saját munkaeleme lesz.
   ![Képernyőkép, amely a ITSM esemény ablakát jeleníti meg.](media/itsmc-overview/itsm-action-configuration-event.png)

10. Válassza az **OK** lehetőséget.

Azure-riasztási szabály létrehozásakor vagy szerkesztésekor használjon egy ITSM műveletet tartalmazó műveleti csoportot. A riasztás indításakor a rendszer létrehozza vagy frissíti a munkaelemet a ITSM eszközben.

> [!NOTE]
>
>- A ITSM művelet díjszabásával kapcsolatos információkért tekintse meg a műveleti csoportok [díjszabási lapját](https://azure.microsoft.com/pricing/details/monitor/) .
>
>
>- A riasztási szabály definíciójának rövid leírása mezője 40 karakterre van korlátozva, ha a ITSM művelettel küldi el.

## <a name="additional-information"></a>További információ

### <a name="data-synced-from-your-itsm-product"></a>A ITSM-termékről szinkronizált adatok

Az incidensek és a módosítási kérelmek szinkronizálása a ITSM-termékről a Log Analytics munkaterületre a kapcsolatok konfigurációja alapján történik.

Ez a szakasz néhány példát mutat be a ITSMC által összegyűjtött adatokra.

A **ServiceDesk_CL** mezői a log Analyticsba importált munkaelem típusától függően változnak. A következő lista felsorolja a két munkaelem típusának mezőit:

**Munkaelem:** **incidensek**  
ServiceDeskWorkItemType_s = "incidens"

**Mezők**

- ServiceDeskConnectionName
- Ügyfélszolgálat azonosítója
- Állam
- Sürgősség
- Hatás
- Prioritás
- Eszkalálás
- Created By (Létrehozó)
- Feloldotta:
- Lezárta
- Forrás
- A jog birtokosa
- Kategória
- Cím
- Leírás
- Létrehozás dátuma
- Lezárás dátuma
- Megoldás dátuma
- Utolsó módosítás dátuma
- Computer

**Munkaelem:** **módosítási kérelmek**

ServiceDeskWorkItemType_s = "módosítási kérelem"

**Mezők**
- ServiceDeskConnectionName
- Ügyfélszolgálat azonosítója
- Created By (Létrehozó)
- Lezárta
- Forrás
- A jog birtokosa
- Cím
- Típus
- Kategória
- Állam
- Eszkalálás
- Ütközési állapot
- Sürgősség
- Prioritás
- Kockázat
- Hatás
- A jog birtokosa
- Létrehozás dátuma
- Lezárás dátuma
- Utolsó módosítás dátuma
- Kért dátum
- Tervezett kezdési dátum
- Tervezett befejezési dátum
- Munka kezdő dátuma
- Munka befejezési dátuma
- Description
- Computer

## <a name="output-data-for-a-servicenow-incident"></a>ServiceNow incidens kimeneti adatkészlete

| Log Analytics mező | ServiceNow mező |
|:--- |:--- |
| ServiceDeskId_s| Szám |
| IncidentState_s | Állam |
| Urgency_s |Sürgősség |
| Impact_s |Hatás|
| Priority_s | Prioritás |
| CreatedBy_s | Megnyitva |
| ResolvedBy_s | Feloldotta:|
| ClosedBy_s  | Lezárta |
| Source_s| Kapcsolattartó típusa |
| AssignedTo_s | Felelős  |
| Category_s | Kategória |
| Title_s|  Rövid leírás |
| Description_s|  Jegyzetek |
| CreatedDate_t|  Megnyitott |
| ClosedDate_t| lezárva|
| ResolvedDate_t|Feloldva|
| Computer  | Konfigurációelem |

## <a name="output-data-for-a-servicenow-change-request"></a>ServiceNow-módosítási kérelem kimeneti adatkészlete

| Log Analytics | ServiceNow mező |
|:--- |:--- |
| ServiceDeskId_s| Szám |
| CreatedBy_s | Kérelmező |
| ClosedBy_s | Lezárta |
| AssignedTo_s | Felelős  |
| Title_s|  Rövid leírás |
| Type_s|  Típus |
| Category_s|  Kategória |
| CRState_s|  Állam|
| Urgency_s|  Sürgősség |
| Priority_s| Prioritás|
| Risk_s| Kockázat|
| Impact_s| Hatás|
| RequestedDate_t  | Kérelem dátuma szerint |
| ClosedDate_t | Lezárt dátum |
| PlannedStartDate_t  | Tervezett kezdési dátum |
| PlannedEndDate_t  | Tervezett befejezési dátum |
| WorkStartDate_t  | Tényleges kezdési dátum |
| WorkEndDate_t | Tényleges befejezési dátum|
| Description_s | Description |
| Computer  | Konfigurációs tétel |

## <a name="next-steps"></a>Következő lépések

[ITSM-csatoló áttekintése](./itsmc-overview.md) 
 [ITSM-termékek/szolgáltatások hozzáadása a it-szolgáltatásmenedzsmenti csatolóhoz](./itsmc-connections.md) 
 [Hibaelhárítási problémák a ITSM-csatoló](./itsmc-resync-servicenow.md)
