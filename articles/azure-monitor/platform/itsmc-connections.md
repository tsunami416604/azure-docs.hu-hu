---
title: Támogatott kapcsolatok az IT Service Management-összekötő az Azure Log Analyticsben |} A Microsoft Docs
description: Ez a cikk ismerteti az ITSM-termékekkel/szolgáltatásokkal a az IT Service Management összekötő (ITSMC) az Azure monitorban való központilag felügyelhető és kezelhető az ITSM-munkatétel csatlakozni.
documentationcenter: ''
author: jyothirmaisuri
manager: riyazp
editor: ''
ms.assetid: 8231b7ce-d67f-4237-afbf-465e2e397105
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: v-jysur
ms.component: ''
ms.openlocfilehash: a7b24ff3f51cdd66391f8dd7c73598530f767420
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52865590"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>ITSM-termékekkel/szolgáltatásokkal csatlakozzon az IT Service Management-összekötő
Ez a cikk ismerteti az ITSM-termék vagy szolgáltatás és az IT Service Management Connector (ITSMC) közötti kapcsolat konfigurálása a Log Analytics központilag kezelheti a munkaelemeket. ITSMC kapcsolatos további információkért lásd: [áttekintése](../../azure-monitor/platform/itsmc-overview.md).

A következő ITSM termékek/szolgáltatások támogatottak. Válassza ki a terméket a termék kapcsolódás ITSMC kapcsolatos részletes információk megtekintéséhez.

- [A System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]

> ITSM-összekötő csak a ServiceNow-példány felhőalapú lehet csatlakozni. A helyszíni ServiceNow-példány jelenleg nem támogatott.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>Csatlakozás a System Center Service Manager IT Service Management-összekötő az Azure-ban

A következő szakaszok a System Center Service Manager termék csatlakozhat az Azure-ban ITSMC részleteit.

### <a name="prerequisites"></a>Előfeltételek

Ellenőrizze, hogy az alábbi előfeltételek teljesülését:

- ITSMC telepítve. További információ: [felvétele az IT Service Management Connector megoldás](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- A Service Manager-webalkalmazás (webalkalmazás) telepítve és konfigurálva van. Információ a Web app [Itt](#create-and-deploy-service-manager-web-app-service).
- Hibrid kapcsolat létrehozása és konfigurálása. További információ: [konfigurálása a hibrid kapcsolat](#configure-the-hybrid-connection).
- Támogatott verziók a Service Manager: 2012 R2 és 2016-ot.
- Felhasználói szerepkör: [speciális operátor](https://technet.microsoft.com/library/ff461054.aspx).

### <a name="connection-procedure"></a>Kapcsolat létesítése

A következő eljárással ITSMC csatlakozni a System Center Service Manager-példány:

1. Az Azure Portalon lépjen a **összes erőforrás** , és keressen **ServiceDesk(YourWorkspaceName)**

2.  A **MUNKATERÜLET ADATFORRÁSAI** kattintson **ITSM-kapcsolatok**.

    ![Új kapcsolat](media/itsmc-connections/add-new-itsm-connection.png)

3. Kattintson a jobb oldali ablaktábla tetején **Hozzáadás**.

4. Adja meg az adatokat, az alábbi táblázatban leírtak szerint, és kattintson a **OK** a kapcsolat létrehozásához.

> [!NOTE]

> Ezek a paraméterek kötelezőek.

| **Mező** | **Leírás** |
| --- | --- |
| **Kapcsolat neve**   | Adjon meg egy nevet, a System Center Service Manager-példány, amelyeket ITSMC kapcsolódni szeretne.  Használhatja a név később konfigurálja a munkaelemek ebben a példányban, / részletes a log analytics megtekintéséhez. |
| **Partner típusa**   | Válassza ki **a System Center Service Manager**. |
| **Kiszolgáló URL-címe**   | Írja be a webalkalmazás URL-címét a Service Manager. További információ a Service Manager webalkalmazás van [Itt](#create-and-deploy-service-manager-web-app-service).
| **Ügyfél-azonosító**   | Írja be az ügyfél-Azonosítót (az automatikus parancsfájl használatával) létrehozott webalkalmazás hitelesítéséhez. További információ az automatizált szkript van [itt.](../../azure-monitor/platform/itsmc-service-manager-script.md)|
| **Titkos Ügyfélkód**   | Írja be a titkos ügyfélkulcsot, jön létre a azonosítóval.   |
| **Adatszinkronizálás hatóköre**   | Válassza ki a Service Manager munkaelemek keresztül ITSMC szinkronizálni kívánt.  Ezen elemek is importálja a Log Analytics munka. **Beállítások:** incidensek, Változáskérések.|
| **Adatok szinkronizálása** | Írja be az adatokat a kívánt múltbeli napok száma. **Felső korlát**: 120 nap. |
| **Új konfigurációs elem létrehozása az ITSM-megoldással** | Válassza ezt a lehetőséget, ha azt szeretné, a konfigurációelemek létrehozása az ITSM-termékben. Kiválasztásakor a Log Analytics hoz létre az érintett Konfigurációelemek (esetén a nem létező CIs) konfigurációelemként a támogatott ITSM-rendszer. **Alapértelmezett**: le van tiltva. |

![Service manager-kapcsolat](media/itsmc-connections/service-manager-connection.png)

**Ha sikeresen csatlakoztatva és szinkronizálva**:

- Kiválasztott munkaelemek a Service Managerből az Azure-ba importált **Log Analytics.** Az összefoglalás ezeket is megtekintheti az IT Service Management-összekötő csempéjén munkaelemek.

- Az incidensek a Log Analytics-riasztásokból vagy rekordok naplózása vagy a Service Manager-példány az Azure-riasztások hozhat létre.


További információ: [az Azure-riasztások létrehozása ITSM-munkatétel](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Hozzon létre, és a Service Manager web app service üzembe helyezése

Az Azure-ban ITSMC kapcsolódni a helyi Service Manager, Microsoft hozott létre a Service Manager webes alkalmazás a githubon.

Állítsa be az ITSM-webalkalmazást a Service Manager, tegye a következőket:

- **A webes alkalmazás üzembe helyezése** – a webalkalmazás üzembe helyezése, állítsa be a tulajdonságokat, és az Azure AD-hitelesítést. A webalkalmazás segítségével telepíthet a [parancsfájl automatikus](../../azure-monitor/platform/itsmc-service-manager-script.md) , hogy a Microsoft adta meg.
- **A hibrid kapcsolat konfigurálása** - [e kapcsolat konfigurálására](#configure-the-hybrid-connection), manuálisan.

#### <a name="deploy-the-web-app"></a>A webes alkalmazás üzembe helyezése
Használja az automatizált [parancsfájl](../../azure-monitor/platform/itsmc-service-manager-script.md) a webes alkalmazás üzembe helyezése, állítsa be a tulajdonságokat, és az Azure AD-hitelesítést.

Futtassa a parancsfájlt azáltal, hogy a következő szükséges adatokat:

- Azure-előfizetés részletei
- Erőforráscsoport neve
- Hely
- A Service Manager-kiszolgálóadatok (kiszolgáló neve, tartomány, felhasználónév és jelszó)
- A webalkalmazás-hely előtagja
- A ServiceBus-Namespace.

A parancsfájl létrehozza a webalkalmazást, a név, amely a megadott (valamint néhány további karakterláncok egyedi legyen) használatával. Állít elő a **webes alkalmazás URL-cím**, **ügyfél-azonosító** és **titkos Ügyfélkód**.

Mentse az értékeket akkor használnia őket, amikor a ITSMC kapcsolatot hoz létre.

**Ellenőrizze a webalkalmazás telepítése**

1. Lépjen a **az Azure portal** > **erőforrások**.
2. Válassza ki a webalkalmazást, kattintson a **beállítások** > **Alkalmazásbeállítások**.
3. Erősítse meg, amely azt adja meg a parancsfájl segítségével az alkalmazás üzembe helyezése a Service Manager-példány adatait.

### <a name="configure-the-hybrid-connection"></a>A hibrid kapcsolat konfigurálása

A következő eljárás használatával állítsa be a hibrid kapcsolatot, hogy a Service Manager-példány összekapcsolja az Azure-ban ITSMC.

1. A Service Manager Web app alatt található **Azure-erőforrások**.
2. Kattintson a **beállítások** > **hálózatkezelés**.
3. A **hibrid kapcsolatok**, kattintson a **hibrid kapcsolati végpontok konfigurálása**.

    ![Hibrid kapcsolat hálózatkezelés](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. Az a **hibrid kapcsolatok** panelen kattintson a **hibrid kapcsolat hozzáadása**.

    ![Hibrid kapcsolat hozzáadása](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. Az a **adja hozzá a hibrid kapcsolatok** panelen kattintson a **hozzon létre új hibrid kapcsolat**.

    ![Új hibrid kapcsolat](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Írja be a következő értékeket:

    - **Végpont neve**: Adjon meg egy nevet az új hibrid kapcsolat.
    -  **Végponti gazdagép**: a Service Manager felügyeleti kiszolgáló teljes Tartománynevét.
    - **Végponti Port**: írja be az 5724-es
    - **A Servicebus névtér**: használjon egy meglévő servicebus-névtérből, vagy hozzon létre egy újat.
    - **Hely**: válassza ki azt a helyet.
    -  **Név**: Adja meg a nevet a servicebus létrehozásakor azt.

    ![Hibrid kapcsolat értékek](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Kattintson a **OK** gombra kattintva zárja be a **hibrid kapcsolat létrehozása** panelen, majd a hibrid kapcsolat létrehozásának indítása.

    A hibrid kapcsolat létrehozása után megjelenik a panelen.

7. A hibrid kapcsolat létrehozása után válassza ki a kapcsolatot, és kattintson a **hozzáadása a kiválasztott hibrid kapcsolat**.

    ![Új hibrid kapcsolat](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>A figyelő beállítása konfigurálása

Az alábbi eljárás segítségével konfigurálhatja a figyelő beállítása a hibrid kapcsolat.

1. Az a **hibrid kapcsolatok** panelen kattintson a **a Csatlakozáskezelő letöltése** , és telepítse a System Center Service Manager-példányt futtató számítógépen.

    A telepítés befejeződése után **Hibridkapcsolat-kezelő felhasználói felületén** beállítás érhető el **Start** menü.

2. Kattintson a **Hibridkapcsolat-kezelő felhasználói felületén** , meg kell adnia Azure hitelesítő adatait.

3. Bejelentkezés az Azure-beli hitelesítő adatokkal, és válassza ki az előfizetést, amelyben a hibrid kapcsolat jött létre.

4. Kattintson a **Save** (Mentés) gombra.

A hibrid kapcsolat sikeresen csatlakoztatva van.

![sikeres hibrid kapcsolat](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]

> Után a hibrid kapcsolat jön létre, győződjön meg arról, és tesztelje a kapcsolatot a Service Manager üzembe helyezett webalkalmazás felkeresésével. Győződjön meg arról, a kapcsolat létrejött, mielőtt megpróbál csatlakozni az Azure-ban ITSMC.

Az alábbi képet a sikeres kapcsolat részleteit jeleníti meg:

![Hibrid kapcsolat tesztelése](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>A ServiceNow csatlakoztatása a IT Service Management-összekötő az Azure-ban

A következő szakaszok a ServiceNow-termék csatlakozhat az Azure-ban ITSMC részleteit.

### <a name="prerequisites"></a>Előfeltételek
Ellenőrizze, hogy az alábbi előfeltételek teljesülését:
- ITSMC telepítve. További információ: [felvétele az IT Service Management Connector megoldás](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- A ServiceNow támogatott verziók: Kingston, Jakarta, Isztambul, Helsinki, Geneva.

**ServiceNow-rendszergazdák tegye a következőket a ServiceNow-példány az**:
- Ügyfél-Azonosítóját és ügyfélkulcsát a ServiceNow-termék létrehozásához. Hogyan hozhat létre az ügyfél-azonosítója és kulcsa a további információkért lásd: szükség szerint a következő információkat:

    - [OAuth-ot Kingston beállítása](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth-ot Dzsakarta beállítása](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth-ot Isztambul beállítása](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth-ot Helsinki beállítása](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth-ot Geneva beállítása](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)


- Telepítse a Microsoft Log Analytics-integráció (ServiceNow-alkalmazás) felhasználói alkalmazás. [További információk](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
- A felhasználó alkalmazás telepített integrációs felhasználói szerepkör létrehozása. Információ az integrációs felhasználói szerepkör létrehozása [Itt](#create-integration-user-role-in-servicenow-app).

### <a name="connection-procedure"></a>**Kapcsolat létesítése**
Az alábbi eljárással hozhat létre egy ServiceNow-csatlakoztatás:


1. Az Azure Portalon lépjen a **összes erőforrás** , és keressen **ServiceDesk(YourWorkspaceName)**

2.  A **MUNKATERÜLET ADATFORRÁSAI** kattintson **ITSM-kapcsolatok**.
    ![Új kapcsolat](media/itsmc-connections/add-new-itsm-connection.png)

3. Kattintson a jobb oldali ablaktábla tetején **Hozzáadás**.

4. Adja meg az adatokat, az alábbi táblázatban leírtak szerint, és kattintson a **OK** a kapcsolat létrehozásához.


> [!NOTE]
> Ezek a paraméterek kötelezőek.

| **Mező** | **Leírás** |
| --- | --- |
| **Kapcsolat neve**   | Adja meg a servicenow összekapcsolása ITSMC kívánt nevét.  Ez a név később a Log Analytics használni, amikor az ITSM munkadarabok konfigurálása, / részletes a log analytics megtekintéséhez. |
| **Partner típusa**   | Válassza ki **ServiceNow**. |
| **Felhasználónév**   | Írja be a integrációs felhasználónevet, a ServiceNow alkalmazás ITSMC kapcsolat támogatásához létrehozott. További információ: [létrehozása ServiceNow alkalmazás felhasználói szerepkör](#create-integration-user-role-in-servicenow-app).|
| **Jelszó**   | Írja be a felhasználónévhez tartozó jelszót. **Megjegyzés:**: felhasználónév és jelszó csak a hitelesítési tokenek létrehozásához használhatók, és nem bárhol tárolja a ITSMC szolgáltatásban.  |
| **Kiszolgáló URL-címe**   | Írja be az URL-címét, amelyhez csatlakozni kíván ITSMC ServiceNow-példány. |
| **Ügyfél-azonosító**   | Írja be az OAuth2-hitelesítéshez, amely a korábban létrehozott használni kívánt ügyfél-azonosító.  További információ az ügyfél-azonosító és titkos kulcs generálása: [OAuth telepítését](http://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Titkos Ügyfélkód**   | Írja be a titkos ügyfélkulcsot, jön létre a azonosítóval.   |
| **Adatszinkronizálás hatóköre**   | Válassza ki a ServiceNow-munkaelemek szinkronizálása az Azure Log Analyticshez a ITSMC keresztül kívánt.  A kiválasztott értékek is importálja a log analytics.   **Beállítások:** incidensek és Változáskérések.|
| **Adatok szinkronizálása** | Írja be az adatokat a kívánt múltbeli napok száma. **Felső korlát**: 120 nap. |
| **Új konfigurációs elem létrehozása az ITSM-megoldással** | Válassza ezt a lehetőséget, ha azt szeretné, a konfigurációelemek létrehozása az ITSM-termékben. Kiválasztásakor ITSMC hoz létre az érintett Konfigurációelemek (esetén a nem létező CIs) konfigurációelemként a támogatott ITSM-rendszerben. **Alapértelmezett**: le van tiltva. |

![ServiceNow-csatlakoztatás](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**Ha sikeresen csatlakoztatva és szinkronizálva**:

- Az Azure-ba importált ServiceNow-példány a kiválasztott munkaelemek **Log Analytics.** Az összefoglalás ezeket is megtekintheti az IT Service Management-összekötő csempéjén munkaelemek.

- Az incidensek a Log Analytics-riasztásokból vagy rekordok naplózása vagy a ServiceNow-példány az Azure-riasztások hozhat létre.

További információ: [az Azure-riasztások létrehozása ITSM-munkatétel](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-integration-user-role-in-servicenow-app"></a>Integráció felhasználói szerepkör létrehozása a ServiceNow-alkalmazás

A felhasználó az alábbi eljárást:

1.  Látogasson el a [ServiceNow store](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) , és telepítse a **felhasználói alkalmazás a ServiceNow és a Microsoft OMS-integráció** be a ServiceNow-példányát.
   
   >[!NOTE]
   >Az Azure Monitor folyamatos Váltás a Microsoft Operations Management Suite (OMS) részeként OMS most nevezzük a Log Analytics.     
2.  A telepítés után látogasson el a ServiceNow-példány, keresési, és válassza ki a Microsoft OMS támaszkodva a bal oldali navigációs sávon.  
3.  Kattintson a **telepítési ellenőrzőlista**.

    Az állapot elemnél **nem hajtható végre,** Ha a felhasználói szerepkör még létrehozni.

4.  A szövegmezőbe a **integrációs felhasználó létrehozása**, adja meg a felhasználót, hogy képes csatlakozni az Azure-ban ITSMC felhasználó nevét.
5.  Adja meg a jelszót a felhasználó számára, és kattintson a **OK**.  

>[!NOTE]

> Ezek a hitelesítő adatok használatával a ServiceNow-kapcsolat létrehozása az Azure-ban.

Az újonnan létrehozott felhasználónak jelenik meg az alapértelmezett szerepköröket hozzárendelni.

**Alapértelmezett szerepkörök**:
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   ITIL
-   template_editor
-   view_changer

Ha a felhasználó sikeresen létrejött, állapotának **ellenőrizze a telepítési ellenőrzőlista** befejezve, áthelyezi a felhasználói szerepkör a részleteket felsoroló hoz létre az alkalmazáshoz.

> [!NOTE]

> ITSM-összekötő telepítve van a ServiceNow-példány a bármely más modulok nélkül küldhet ServiceNow incidensek. Ha a ServiceNow-példány EventManagement modult használ, és események és riasztások létrehozása a ServiceNow-összekötő segítségével szeretné, adja hozzá a következő szerepkörök a integrációs felhasználónak:

>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>Csatlakozás Provance IT Service Management-összekötő az Azure-ban

A következő szakaszok a Provance termék csatlakozhat az Azure-ban ITSMC részleteit.


### <a name="prerequisites"></a>Előfeltételek

Ellenőrizze, hogy az alábbi előfeltételek teljesülését:


- ITSMC telepítve. További információ: [felvétele az IT Service Management Connector megoldás](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Provance alkalmazást kell regisztrálni az Azure AD - és ügyfél-Azonosítóját szeretné elérhetővé tenni. Részletes információkért lásd: [active directory-hitelesítés konfigurálása](../../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md).

- Felhasználói szerepkör: rendszergazda.

### <a name="connection-procedure"></a>Kapcsolat létesítése

A következő eljárással Provance kapcsolat létrehozása:

1. Az Azure Portalon lépjen a **összes erőforrás** , és keressen **ServiceDesk(YourWorkspaceName)**

2.  A **MUNKATERÜLET ADATFORRÁSAI** kattintson **ITSM-kapcsolatok**.
    ![Új kapcsolat](media/itsmc-connections/add-new-itsm-connection.png)

3. Kattintson a jobb oldali ablaktábla tetején **Hozzáadás**.

4. Adja meg az adatokat, az alábbi táblázatban leírtak szerint, és kattintson a **OK** a kapcsolat létrehozásához.

> [!NOTE]

> Ezek a paraméterek kötelezőek.

| **Mező** | **Leírás** |
| --- | --- |
| **Kapcsolat neve**   | Adjon meg egy nevet, amelyhez csatlakozni a ITSMC Provance-példány.  Használhatja a név később, az ITSM munkadarabok konfigurálása / részletes a log analytics megtekintéséhez. |
| **Partner típusa**   | Válassza ki **Provance**. |
| **Felhasználónév**   | Írja be a felhasználók kapcsolódhatnak az ITSMC.    |
| **Jelszó**   | Írja be a felhasználónévhez tartozó jelszót. **Megjegyzés:** felhasználónevet és jelszót csak a hitelesítési tokenek létrehozásához használhatók, és nem tárolja el bárhol a ITSMC szolgáltatás. _|
| **Kiszolgáló URL-címe**   | Írja be a példány URL-címét a Provance, amelyeket ITSMC csatlakozni szeretne. |
| **Ügyfél-azonosító**   | Írja be az ügyfél-Azonosítót ehhez a kapcsolathoz, amelyre Provance példányában létrehozott hitelesítéséhez.  További információ az ügyfél-azonosító, lásd: [active directory-hitelesítés konfigurálása](../../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md). |
| **Adatszinkronizálás hatóköre**   | Válassza ki az Azure Log Analyticshez, keresztül ITSMC szinkronizálni kívánt Provance munkaelemek.  Ezen elemek is importálja a log analytics munka.   **Beállítások:** incidensek, Változáskérések.|
| **Adatok szinkronizálása** | Írja be az adatokat a kívánt múltbeli napok száma. **Felső korlát**: 120 nap. |
| **Új konfigurációs elem létrehozása az ITSM-megoldással** | Válassza ezt a lehetőséget, ha azt szeretné, a konfigurációelemek létrehozása az ITSM-termékben. Kiválasztásakor ITSMC hoz létre az érintett Konfigurációelemek (esetén a nem létező CIs) konfigurációelemként a támogatott ITSM-rendszerben. **Alapértelmezett**: le van tiltva.|

![Provance kapcsolat](media/itsmc-connections/itsm-connections-provance-latest.png)

**Ha sikeresen csatlakoztatva és szinkronizálva**:

- Az Azure-ba importált kiválasztott munkaelemek jelen Provance példányából **Log Analytics.** Az összefoglalás ezeket is megtekintheti az IT Service Management-összekötő csempéjén munkaelemek.

- Incidensek a Log Analytics-riasztásokból vagy rekordok naplózása vagy Azure-riasztások ebben a példányban Provance hozhat létre.

További információ: [az Azure-riasztások létrehozása ITSM-munkatétel](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Csatlakozás Cherwell IT Service Management-összekötő az Azure-ban

A következő szakaszok a Cherwell termék csatlakozhat az Azure-ban ITSMC részleteit.

### <a name="prerequisites"></a>Előfeltételek

Ellenőrizze, hogy az alábbi előfeltételek teljesülését:

- ITSMC telepítve. További információ: [felvétele az IT Service Management Connector megoldás](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Létrehozott ügyfél-azonosító. További információ: [készítése az ügyfél-azonosító Cherwell](#generate-client-id-for-cherwell).
- Felhasználói szerepkör: rendszergazda.

### <a name="connection-procedure"></a>Kapcsolat létesítése

A következő eljárással Provance kapcsolat létrehozása:

1. Az Azure Portalon lépjen a **összes erőforrás** , és keressen **ServiceDesk(YourWorkspaceName)**

2.  A **MUNKATERÜLET ADATFORRÁSAI** kattintson **ITSM-kapcsolatok**.
    ![Új kapcsolat](media/itsmc-connections/add-new-itsm-connection.png)

3. Kattintson a jobb oldali ablaktábla tetején **Hozzáadás**.

4. Adja meg az adatokat, az alábbi táblázatban leírtak szerint, és kattintson a **OK** a kapcsolat létrehozásához.

> [!NOTE]

> Ezek a paraméterek kötelezőek.

| **Mező** | **Leírás** |
| --- | --- |
| **Kapcsolat neve**   | Adjon meg egy nevet, amelyhez csatlakozni kíván ITSMC Cherwell-példány.  Használhatja a név később, az ITSM munkadarabok konfigurálása / részletes a log analytics megtekintéséhez. |
| **Partner típusa**   | Válassza ki **Cherwell.** |
| **Felhasználónév**   | Írja be a Cherwell felhasználói ITSMC kapcsolódhatnak. |
| **Jelszó**   | Írja be a felhasználónévhez tartozó jelszót. **Megjegyzés:** felhasználónevet és jelszót csak a hitelesítési tokenek létrehozásához használhatók, és nem bárhol tárolja a ITSMC szolgáltatásban.|
| **Kiszolgáló URL-címe**   | Írja be a példány URL-címét a Cherwell, amelyeket ITSMC csatlakozni szeretne. |
| **Ügyfél-azonosító**   | Írja be az ügyfél-Azonosítót ehhez a kapcsolathoz, amelyre Cherwell példányában létrehozott hitelesítéséhez.   |
| **Adatszinkronizálás hatóköre**   | Válassza ki a Cherwell munkaelemek keresztül ITSMC szinkronizálni kívánt.  Ezen elemek is importálja a log analytics munka.   **Beállítások:** incidensek, Változáskérések. |
| **Adatok szinkronizálása** | Írja be az adatokat a kívánt múltbeli napok száma. **Felső korlát**: 120 nap. |
| **Új konfigurációs elem létrehozása az ITSM-megoldással** | Válassza ezt a lehetőséget, ha azt szeretné, a konfigurációelemek létrehozása az ITSM-termékben. Kiválasztásakor ITSMC hoz létre az érintett Konfigurációelemek (esetén a nem létező CIs) konfigurációelemként a támogatott ITSM-rendszerben. **Alapértelmezett**: le van tiltva. |


![Provance kapcsolat](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**Ha sikeresen csatlakoztatva és szinkronizálva**:

- A Cherwell példány a kiválasztott munkaelemek az Azure-ba importált **Log Analytics.** Az összefoglalás ezeket is megtekintheti az IT Service Management-összekötő csempéjén munkaelemek.

- Incidensek a Log Analytics-riasztásokból vagy rekordok naplózása vagy Azure-riasztások ebben a példányban Cherwell hozhat létre.

További információ: [az Azure-riasztások létrehozása ITSM-munkatétel](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>A Cherwell ügyfél-azonosító létrehozása

Az ügyfél-azonosítója és kulcsa Cherwell létrehozni, használja az alábbi eljárást:

1. Rendszergazdaként jelentkezzen be a Cherwell példányba
2. Kattintson a **biztonsági** > **szerkesztése a REST API-val ügyfélbeállítások**.
3. Válassza ki **hozzon létre új ügyfél** > **titkos Ügyfélkód**.

    ![Cherwell felhasználói azonosító](media/itsmc-connections/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>További lépések
 - [ITSM-munkatétel létrehozása az Azure-riasztások](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
