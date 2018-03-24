---
title: Az Azure Log Analytics-kapcsolatok informatikai szolgáltatás Management-összekötő támogatott |} Microsoft Docs
description: Ez a cikk tájékoztatást ad azokról a ITSM termékek vagy szolgáltatások összekapcsolása a a informatikai szolgáltatás Management Connector (ITSMC) az OMS szolgáltatáshoz központilag figyelheti és a ITSM munkaelemek kezelésére.
documentationcenter: ''
author: JYOTHIRMAISURI
manager: riyazp
editor: ''
ms.assetid: 8231b7ce-d67f-4237-afbf-465e2e397105
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: v-jysur
ms.openlocfilehash: 35d04fabc66ede309fe91969c5bec3131a282afb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Csatlakozás ITSM termékek vagy szolgáltatások IT Service Management-összekötő
Ez a cikk tájékoztatást ad azokról a ITSM termékszolgáltatásból és az informatikai szolgáltatás Management Connector (ITSMC) közötti kapcsolat konfigurálása a Log Analyticshez központilag a a munkaelemek kezeléséhez. ITSMC kapcsolatos további információkért lásd: [áttekintése](log-analytics-itsmc-overview.md).

A következő ITSM termékek vagy szolgáltatások támogatottak. Válassza ki azt a terméket a termék csatlakoztatása ITSMC részletes adatainak megtekintéséhez.

- [A System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]

> ITSM összekötő csak felhőalapú ServiceNow példányok csatlakozhat. A helyszíni ServiceNow példány jelenleg nem támogatottak.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>Csatlakozás a System Center Service Manager IT Service Management-összekötő az Azure-ban

A következő szakaszok részletesen bemutatják a System Center Service Manager termék csatlakoztatása az Azure-ban ITSMC.

### <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, a következő előfeltételek teljesülését:

- A ITSMC telepítve. További információ: [felvétele az IT Service Connector megoldás](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- A Service Manager webes alkalmazás (webalkalmazás) telepítve és konfigurálva. A webalkalmazás-információk [Itt](#create-and-deploy-service-manager-web-app-service).
- A hibrid kapcsolat létrehozása és konfigurálása. További információ: [konfigurálása a hibrid kapcsolat](#configure-the-hybrid-connection).
- Támogatott verziók a Service Manager: 2012 R2 vagy a 2016.
- Felhasználói szerepkör: [speciális kezelő](https://technet.microsoft.com/library/ff461054.aspx).

### <a name="connection-procedure"></a>Kapcsolat létesítése

A következő eljárással csatlakozzon a System Center Service Manager-példány ITSMC:

1. Azure-portálon lépjen **összes erőforrás** , és keressen **ServiceDesk(YourWorkspaceName)**

2.  A **MUNKATERÜLET adatforrások** kattintson **ITSM kapcsolatok**.

    ![Új kapcsolat](./media/log-analytics-itsmc/add-new-itsm-connection.png)

3. Kattintson a jobb oldali ablaktábla tetején **Hozzáadás**.

4. Adja meg az adatokat, a következő táblázatban ismertetett módon, és kattintson a **OK** a VPN-kapcsolat létrehozásához.

> [!NOTE]

> Ezek a paraméterek kötelezőek.

| **Mező** | **Leírás** |
| --- | --- |
| **Kapcsolat neve**   | Írja be, amelyhez csatlakozni ITSMC a System Center Service Manager-példány nevét.  Használja a név később munkaelemek konfigurálja ezt a példányt / részletes naplóelemzési megtekintése. |
| **Partner típusa**   | Válassza ki **a System Center Service Manager**. |
| **URL-címe**   | Írja be a Service Manager webalkalmazás URL-CÍMÉT. A Service Manager webalkalmazás bővebb információk [Itt](#create-and-deploy-service-manager-web-app-service).
| **Ügyfél-azonosító**   | A webalkalmazás hitelesítéséhez, írja be az ügyfél-azonosító, ami akkor jön létre (az automatikus parancsfájl használatával). További információk az automatizált parancsfájl [itt.](log-analytics-itsmc-service-manager-script.md)|
| **Ügyfélkulcs**   | Írja be a ügyfélkulcs jön létre a azonosítóját.   |
| **Adatok szinkronizálási hatókör**   | Válassza ki a Service Manager munkaelemek ITSMC keresztül szinkronizálni kívánt.  A munkahelyi elemeket a rendszer importálta a Naplóelemzési. **Beállítások:** incidensek, Változáskérések.|
| **Sync Data** | Írja be a hány napra visszamenőleg, amelyet az adatait. **Maximális**: 120 nap. |
| **Hozzon létre új konfigurációelemet ITSM megoldás** | Válassza ezt a lehetőséget, ha azt szeretné, hogy a konfigurációelemek létrehozása a ITSM termékben. Kiválasztásakor OMS hoz létre az érintett Konfigurációelemek (esetén nem létező CIs) konfigurációelemként a támogatott ITSM rendszerben. **Alapértelmezett**: le van tiltva. |

![Service manager-kapcsolat](./media/log-analytics-itsmc/service-manager-connection.png)

**Ha sikeresen csatlakoztatva lett, és szinkronizálja azt**:

- Kiválasztott munkaelemek a Service Manager a rendszer importálta Azure **Naplóelemzési.** Ezek az összegzés megtekintéséhez munkaelemek a IT Service Management-összekötő csempére.

- Események Log Analyticshez riasztások vagy naplóbejegyzést, vagy Azure riasztásokat a Service Manager-példány a hozhat létre.


További: [Naplóelemzési riasztások munkaelemek létrehozása ITSM](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-alerts), [munkaelemek létrehozása ITSM Naplóelemzési naplókból](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-log-records) és [munkaelemek ITSM létrehozása az Azure riasztásokból](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Létrehozhat és telepíthet a Service Manager web app service

Csatlakozás a helyi Service Manager ITSMC az Azure-ban, a Microsoft a Service Manager webes alkalmazás hozott létre a Githubon.

A ITSM webes alkalmazás beállítása a Service Manager, tegye a következőket:

- **A webalkalmazás telepítése** – a webes alkalmazás üzembe helyezése, állítsa be a tulajdonságokat, és a hitelesítéséhez az Azure ad-val. A web app használatával telepítheti a [parancsfájl automatikus](log-analytics-itsmc-service-manager-script.md) , hogy a Microsoft közzétett meg.
- **A hibrid kapcsolat beállítása** - [konfigurálja ezt a kapcsolatot](#configure-the-hybrid-connection), manuálisan.

#### <a name="deploy-the-web-app"></a>A webalkalmazás telepítése
Használja az automatizált [parancsfájl](log-analytics-itsmc-service-manager-script.md) a webes alkalmazás telepítése, állítsa be a tulajdonságokat, és a hitelesítéshez az Azure ad-val.

Futtassa a parancsfájlt a következő szükséges adatok megadásával:

- Azure-előfizetés részletei
- Erőforráscsoport neve
- Hely
- A Service Manager-kiszolgálóadatok (kiszolgáló neve, tartomány, felhasználónév és jelszó)
- A webalkalmazás hely előtagja
- ServiceBus Namespace.

A parancsfájl létrehozza a webalkalmazást, az Ön által megadott nevét (valamint néhány további karakterláncok, ami) használatával. Előállítja a **webes alkalmazás URL-címhez**, **ügyfél-azonosító** és **ügyfélkulcs**.

Mentse az értékeket használhatja őket a ITSMC kapcsolatot hoz létre.

**Ellenőrizze a webalkalmazás telepítése**

1. Ugrás a **Azure-portálon** > **erőforrások**.
2. Jelölje ki azt a webalkalmazást **beállítások** > **Alkalmazásbeállítások**.
3. Győződjön meg arról, hogy a parancsfájl az alkalmazás telepítése során megadott Service Manager-példány adatai.

### <a name="configure-the-hybrid-connection"></a>A hibrid kapcsolat konfigurálása

A következő eljárással konfigurálhatja a hibrid kapcsolat az Azure-ban ITSMC kapcsoló, a Service Manager-példány.

1. A Service Manager Web app alatt található **Azure-erőforrások**.
2. Kattintson a **beállítások** > **hálózati**.
3. A **hibrid kapcsolatok**, kattintson a **hibrid kapcsolati végpontok konfigurálása**.

    ![Hibrid kapcsolat hálózatkezelés](./media/log-analytics-itsmc/itsmc-hybrid-connection-networking-and-end-points.png)
4. Az a **hibrid kapcsolatok** panelen kattintson a **adja hozzá a hibrid kapcsolat**.

    ![A hibrid kapcsolat hozzáadása](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-add.png)

5. Az a **hibrid kapcsolatok hozzáadása** panelen kattintson a **hozzon létre új hibrid kapcsolat**.

    ![Új hibrid kapcsolat](./media/log-analytics-itsmc/itsmc-create-new-hybrid-connection.png)

6. Írja be a következő értékeket:

    - **A végpontnév**: Adjon meg egy nevet az új hibrid kapcsolat.
    -  **Végpont állomás**: a Service Manager felügyeleti kiszolgáló teljes Tartományneve.
    - **Végponti Port**: írja be az 5724-es
    - **A Szolgáltatásbusz-névtér**: egy meglévő szolgáltatásbusz-névtér használatára, vagy hozzon létre egy újat.
    - **Hely**: válassza ki azt a helyet.
    -  **Név**: Adjon meg egy a szolgáltatásbusz létrehozásakor azt.

    ![Hibrid kapcsolati értékek](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-values.png)
6. Kattintson a **OK** bezárásához a **hibrid kapcsolat létrehozása** panel megnyitásához, és a hibrid kapcsolat létrehozásának indítása.

    A hibrid kapcsolat létrehozása után a panel alatt jelenik meg.

7. A hibrid kapcsolat létrehozása után válassza ki a kapcsolatot, és kattintson a **hozzáadása a hibrid kapcsolat kijelölt**.

    ![Új hibrid kapcsolat](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>A figyelő telepítés konfigurálása

A következő eljárással konfigurálhatja a figyelő a telepítő a hibrid kapcsolat.

1. Az a **hibrid kapcsolatok** panelen kattintson a **töltse le a Csatlakozáskezelő** és telepítse a System Center Service Manager-példányt futtató számítógépen.

    Miután a telepítés befejeződött, **Hybrid Connection Manager felhasználói felületén** beállítás érhető el a **Start** menü.

2. Kattintson a **Hybrid Connection Manager felhasználói felületén** , kérni fogja az Azure hitelesítő adatait.

3. Jelentkezzen be Azure hitelesítő adatait, és jelölje ki az előfizetését, ahol a hibrid kapcsolat létrehozása történt.

4. Kattintson a **Save** (Mentés) gombra.

A hibrid kapcsolat sikeresen csatlakoztatva van.

![a sikeres a hibrid kapcsolat](./media/log-analytics-itsmc/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]

> A hibrid után létrejön a kapcsolat, ellenőrizze, és tesztelje a kapcsolatot érhetők el a telepített Service Manager webes alkalmazást. Győződjön meg arról, a kapcsolat, sikeres, mielőtt megpróbál csatlakozni az Azure-ban ITSMC.

Az alábbi minta kép a sikeres kapcsolat részleteit jeleníti meg:

![A hibrid kapcsolat ellenőrzése](./media/log-analytics-itsmc/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>A ServiceNow csatlakoztatása a IT Service Management-összekötő az Azure-ban

A következő szakaszok részletesen bemutatják a ServiceNow termék csatlakoztatása az Azure-ban ITSMC.

### <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, a következő előfeltételek teljesülését:
- A ITSMC telepítve. További információ: [felvétele az IT Service Connector megoldás](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- A ServiceNow támogatott verziók: Kingston, Dzsakarta, Isztambul, Helsinki, Geneva.

**A ServiceNow rendszergazdák a következőképpen kell a ServiceNow példányban**:
- Ügyfél-azonosító és a ServiceNow termék ügyfélkulcs létrehozása. Ügyfél-azonosító és a titkos kulcs létrehozása módjáról további információkért lásd: a következő információkat szükség szerint:

    - [A Kingston OAuth beállítása](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/concept/OAuth-setup.html)
    - [A Dzsakarta OAuth beállítása](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [A Isztambuli OAuth beállítása](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [A Helsinki OAuth beállítása](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Geneva az OAuth beállítása](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)


- Telepítse a Microsoft OMS-integráció (ServiceNow alkalmazás) felhasználói alkalmazás. [További információk](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
- A felhasználó az alkalmazás telepítve van az integráció felhasználói szerepkört létrehozni. Az integráció felhasználói szerepkör létrehozása található [Itt](#create-integration-user-role-in-servicenow-app).

### <a name="connection-procedure"></a>**Kapcsolat létesítése**
A következő eljárással ServiceNow VPN-kapcsolat létrehozásához:


1. Azure-portálon lépjen **összes erőforrás** , és keressen **ServiceDesk(YourWorkspaceName)**

2.  A **MUNKATERÜLET adatforrások** kattintson **ITSM kapcsolatok**.
    ![Új kapcsolat](./media/log-analytics-itsmc/add-new-itsm-connection.png)

3. Kattintson a jobb oldali ablaktábla tetején **Hozzáadás**.

4. Adja meg az adatokat, a következő táblázatban ismertetett módon, és kattintson a **OK** a VPN-kapcsolat létrehozásához.


> [!NOTE]
> Ezek a paraméterek kötelezőek.

| **Mező** | **Leírás** |
| --- | --- |
| **Kapcsolat neve**   | Írja be a servicenow ITSMC csatlakozás kívánt nevét.  Ez a név később az OMS használni, amikor a munkaelemek konfigurálja a ITSM / részletes naplóelemzési megtekintése. |
| **Partner típusa**   | Válassza ki **ServiceNow**. |
| **Felhasználónév**   | Adja meg az integrációs felhasználónevet, a kapcsolat ITSMC támogatásához a ServiceNow alkalmazásban létrehozott. További információ: [létrehozása ServiceNow alkalmazás felhasználói szerepkör](#create-integration-user-role-in-servicenow-app).|
| **Jelszó**   | Írja be a felhasználónévhez tartozó jelszót. **Megjegyzés:**: felhasználónév és jelszó generálásához. csak a hitelesítési tokenek használatát, és nem tárolja el bárhol a ITSMC szolgáltatás.  |
| **URL-címe**   | Írja be a servicenow ITSMC való csatlakozáshoz használni kívánt URL-CÍMÉT. |
| **Ügyfél-azonosító**   | Írja be a korábban létrehozott OAuth2 hitelesítéshez használni kívánt ügyfél-azonosító.  További információ az ügyfél-azonosító és a titkos kulcs létrehozása: [OAuth telepítését](http://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Ügyfélkulcs**   | Írja be a ügyfélkulcs jön létre a azonosítóját.   |
| **Adatok szinkronizálási hatókör**   | Válassza ki a ServiceNow munkaelemeket szeretné szinkronizálni az Azure Naplóelemzés szolgáltatáshoz, a ITSMC keresztül.  A kiválasztott értékét a rendszer importálta a naplóelemzési.   **Beállítások:** incidensek és Változáskérések.|
| **Sync Data** | Írja be a hány napra visszamenőleg, amelyet az adatait. **Maximális**: 120 nap. |
| **Hozzon létre új konfigurációelemet ITSM megoldás** | Válassza ezt a lehetőséget, ha azt szeretné, hogy a konfigurációelemek létrehozása a ITSM termékben. Kiválasztásakor ITSMC hoz létre az érintett Konfigurációelemek (esetén nem létező CIs) konfigurációelemként a támogatott ITSM rendszerben. **Alapértelmezett**: le van tiltva. |

![A ServiceNow kapcsolat](./media/log-analytics-itsmc/itsm-connection-servicenow-connection-latest.png)

**Ha sikeresen csatlakoztatva lett, és szinkronizálja azt**:

- A ServiceNow példányból kiválasztott munkaelemek importálása az Azure **Naplóelemzési.** Ezek az összegzés megtekintéséhez munkaelemek a IT Service Management-összekötő csempére.

- Incidensek Naplóelemzési riasztások vagy naplórekordokat, vagy a ServiceNow példány Azure riasztásokat hozhat létre.

További: [Naplóelemzési riasztások munkaelemek létrehozása ITSM](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-alerts), [munkaelemek létrehozása ITSM Naplóelemzési naplókból](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-log-records) és [munkaelemek ITSM létrehozása az Azure riasztásokból](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-integration-user-role-in-servicenow-app"></a>A ServiceNow app integrációs felhasználói szerepkör létrehozása

A felhasználó az alábbi eljárást:

1.  Látogasson el a [ServiceNow tároló](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) és telepítse a **felhasználói alkalmazás a ServiceNow és a Microsoft OMS-integráció** azokat a ServiceNow példányát.
2.  A telepítés után nyissa meg a bal oldali navigációs sávon a ServiceNow példány, a Keresés és a jelölje be a Microsoft OMS integráló.  
3.  Kattintson a **telepítési ellenőrzőlista**.

    Az állapot jelenik meg **nem hajtható végre,** esetén a felhasználói szerepkör még létrehozni.

4.  A beviteli mezők melletti **integráció a felhasználó létrehozása**, meg a felhasználónevet a felhasználót, hogy csatlakozni tud-e az Azure-ban ITSMC.
5.  A felhasználó adja meg a jelszót, és kattintson a **OK**.  

>[!NOTE]

> Ezek a hitelesítő adatok használatával a ServiceNow kapcsolat az Azure-ban.

Az újonnan létrehozott felhasználó megjelenik, amely az alapértelmezett szerepkörrel.

**Alapértelmezett szerepkörök**:
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   itil
-   template_editor
-   view_changer

Ha a felhasználó sikeresen létrejött, állapotának **ellenőrizze telepítési ellenőrzőlista** áthelyezése kész, a felhasználói szerepkör a részleteket felsoroló létre az alkalmazáshoz.

> [!NOTE]

> ITSM összekötő telepítve a ServiceNow-példányon más modulok nélkül küldhet a ServiceNow incidensek. Ha EventManagement modul a ServiceNow példányát használja, és létrehozza az események és riasztások a ServiceNow az összekötővel, a következő szerepkörök hozzáadása az integráció felhasználói: – evt_mgmt_integration - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>IT-szolgáltatás Provance csatlakozni az Azure-ban Management-összekötő

A következő szakaszok részletesen bemutatják a Provance termék csatlakoztatása az Azure-ban ITSMC.


### <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, a következő előfeltételek teljesülését:


- A ITSMC telepítve. További információ: [felvétele az IT Service Connector megoldás](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Provance App kell regisztrálnia az Azure AD - és ügyfél-azonosító szeretné elérhetővé tenni. Részletes információkért lásd: [active directory-hitelesítés konfigurálása](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

- Felhasználói szerepkör: rendszergazda.

### <a name="connection-procedure"></a>Kapcsolat létesítése

A következő eljárással Provance VPN-kapcsolat létrehozásához:

1. Azure-portálon lépjen **összes erőforrás** , és keressen **ServiceDesk(YourWorkspaceName)**

2.  A **MUNKATERÜLET adatforrások** kattintson **ITSM kapcsolatok**.
    ![Új kapcsolat](./media/log-analytics-itsmc/add-new-itsm-connection.png)

3. Kattintson a jobb oldali ablaktábla tetején **Hozzáadás**.

4. Adja meg az adatokat, a következő táblázatban ismertetett módon, és kattintson a **OK** a VPN-kapcsolat létrehozásához.

> [!NOTE]

> Ezek a paraméterek kötelezőek.

| **Mező** | **Leírás** |
| --- | --- |
| **Kapcsolat neve**   | Írja be, amelyhez csatlakozni a ITSMC Provance-példány nevét.  Használhatja a név később, a munkaelemek adja meg a ITSM / részletes naplóelemzési megtekintése. |
| **Partner típusa**   | Válassza ki **Provance**. |
| **Felhasználónév**   | Írja be a felhasználónevét, amely csatlakozni tudna ITSMC.    |
| **Jelszó**   | Írja be a felhasználónévhez tartozó jelszót. **Megjegyzés:** felhasználónév és jelszó generálásához. csak a hitelesítési tokenek használatát, és nem tárolja el bárhol a ITSMC szolgáltatás. _|
| **URL-címe**   | Írja be a Provance példányát ITSMC való csatlakozáshoz használni kívánt URL-CÍMÉT. |
| **Ügyfél-azonosító**   | Írja be az ügyfél-Azonosítót az ezt a kapcsolatot, a Provance példányt létrehozó hitelesítéséhez.  További információ az ügyfél-azonosító, lásd: [active directory-hitelesítés konfigurálása](../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md). |
| **Adatok szinkronizálási hatókör**   | Válassza ki a szinkronizálni kívánt Azure Naplóelemzés keresztül ITSMC kívánt Provance munkaelemek.  A munkahelyi elemeket a rendszer importálta a naplóelemzési.   **Beállítások:** incidensek, Változáskérések.|
| **Sync Data** | Írja be a hány napra visszamenőleg, amelyet az adatait. **Maximális**: 120 nap. |
| **Hozzon létre új konfigurációelemet ITSM megoldás** | Válassza ezt a lehetőséget, ha azt szeretné, hogy a konfigurációelemek létrehozása a ITSM termékben. Kiválasztásakor ITSMC hoz létre az érintett Konfigurációelemek (esetén nem létező CIs) konfigurációelemként a támogatott ITSM rendszerben. **Alapértelmezett**: le van tiltva.|

![Provance kapcsolat](./media/log-analytics-itsmc/itsm-connections-provance-latest.png)

**Ha sikeresen csatlakoztatva lett, és szinkronizálja azt**:

- Kiválasztott munkaelemek jelen Provance példányából a rendszer importálta Azure **Naplóelemzési.** Ezek az összegzés megtekintéséhez munkaelemek a IT Service Management-összekötő csempére.

- Események Log Analyticshez riasztások vagy naplóbejegyzést, vagy Azure riasztások az adott Provance példány hozhat létre.

További: [Naplóelemzési riasztások munkaelemek létrehozása ITSM](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-alerts), [munkaelemek létrehozása ITSM Naplóelemzési naplókból](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-log-records) és [munkaelemek ITSM létrehozása az Azure riasztásokból](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>IT-szolgáltatás Cherwell csatlakozni az Azure-ban Management-összekötő

A következő szakaszok részletesen bemutatják a Cherwell termék csatlakoztatása az Azure-ban ITSMC.

### <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, a következő előfeltételek teljesülését:

- A ITSMC telepítve. További információ: [felvétele az IT Service Connector megoldás](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Létrehozott ügyfél-azonosító. További információ: [készítése az ügyfél-azonosító Cherwell](#generate-client-id-for-cherwell).
- Felhasználói szerepkör: rendszergazda.

### <a name="connection-procedure"></a>Kapcsolat létesítése

A következő eljárással Provance VPN-kapcsolat létrehozásához:

1. Azure-portálon lépjen **összes erőforrás** , és keressen **ServiceDesk(YourWorkspaceName)**

2.  A **MUNKATERÜLET adatforrások** kattintson **ITSM kapcsolatok**.
    ![Új kapcsolat](./media/log-analytics-itsmc/add-new-itsm-connection.png)

3. Kattintson a jobb oldali ablaktábla tetején **Hozzáadás**.

4. Adja meg az adatokat, a következő táblázatban ismertetett módon, és kattintson a **OK** a VPN-kapcsolat létrehozásához.

> [!NOTE]

> Ezek a paraméterek kötelezőek.

| **Mező** | **Leírás** |
| --- | --- |
| **Kapcsolat neve**   | Írja be, amelyhez csatlakozni kíván ITSMC Cherwell-példány nevét.  Használhatja a név később, a munkaelemek adja meg a ITSM / részletes naplóelemzési megtekintése. |
| **Partner típusa**   | Válassza ki **Cherwell.** |
| **Felhasználónév**   | Írja be a Cherwell felhasználónevét, amely csatlakozni tudna ITSMC. |
| **Jelszó**   | Írja be a felhasználónévhez tartozó jelszót. **Megjegyzés:** felhasználónév és jelszó generálásához. csak a hitelesítési tokenek használatát, és nem tárolja el bárhol a ITSMC szolgáltatás.|
| **URL-címe**   | Írja be a Cherwell példányát ITSMC való csatlakozáshoz használni kívánt URL-CÍMÉT. |
| **Ügyfél-azonosító**   | Írja be az ügyfél-Azonosítót az ezt a kapcsolatot, a Cherwell példányt létrehozó hitelesítéséhez.   |
| **Adatok szinkronizálási hatókör**   | Válassza ki a Cherwell munkaelemek ITSMC keresztül szinkronizálni kívánt.  A munkahelyi elemeket a rendszer importálta a naplóelemzési.   **Beállítások:** incidensek, Változáskérések. |
| **Sync Data** | Írja be a hány napra visszamenőleg, amelyet az adatait. **Maximális**: 120 nap. |
| **Hozzon létre új konfigurációelemet ITSM megoldás** | Válassza ezt a lehetőséget, ha azt szeretné, hogy a konfigurációelemek létrehozása a ITSM termékben. Kiválasztásakor ITSMC hoz létre az érintett Konfigurációelemek (esetén nem létező CIs) konfigurációelemként a támogatott ITSM rendszerben. **Alapértelmezett**: le van tiltva. |


![Provance kapcsolat](./media/log-analytics-itsmc/itsm-connections-cherwell-latest.png)

**Ha sikeresen csatlakoztatva lett, és szinkronizálja azt**:

- Kiválasztott munkaelemek jelen Cherwell példányából a rendszer importálta Azure **Naplóelemzési.** Ezek az összegzés megtekintéséhez munkaelemek a IT Service Management-összekötő csempére.

- Események Log Analyticshez riasztások vagy naplóbejegyzést, vagy Azure riasztások az adott Cherwell példány hozhat létre.

További: [Naplóelemzési riasztások munkaelemek létrehozása ITSM](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-alerts), [munkaelemek létrehozása ITSM Naplóelemzési naplókból](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-log-records) és [munkaelemek ITSM létrehozása az Azure riasztásokból](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Ügyfél-azonosító Cherwell létrehozása

Az ügyfél-azonosító/kulcs Cherwell előállításához, a következő eljárással:

1. Rendszergazdaként jelentkezzen be a Cherwell példányát
2. Kattintson a **biztonsági** > **szerkesztése a REST API-t ügyfélbeállítások**.
3. Válassza ki **hozzon létre új ügyfél** > **ügyfélkulcs**.

    ![Cherwell felhasználói azonosító](./media/log-analytics-itsmc/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>További lépések
 - [Log Analytics-riasztások ITSM munkaelemek létrehozása](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-alerts)
 - [ITSM munkaelemek létrehozása Naplóelemzési naplóból rekordok naplók](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-log-records)
 - [Az Azure riasztásokból ITSM munkaelemek létrehozása](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
