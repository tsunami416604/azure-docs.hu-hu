---
title: IT-szolgáltatáskezelési összekötő az Azure Monitorban
description: Ez a cikk arról nyújt tájékoztatást, hogyan csatlakoztathatja az ITSM-termékeket/-szolgáltatásokat az ITSService Management Connector (ITSMC) az Azure Monitor központilag figyelheti és kezelheti az ITSM munkaelemek központilag figyelheti és kezelheti.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: eb3b09c6f349024d30d68a6c970770e2a78924ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132314"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>ITSM-termékek/szolgáltatások csatlakoztatása az IT Service Management Connector-hoz
Ez a cikk arról nyújt tájékoztatást, hogyan konfigurálhatja a kapcsolatot az ITSM-termék/szolgáltatás és az IT Service Management Connector (ITSMC) között a Log Analytics szolgáltatásban a munkaelemek központi kezeléséhez. Az ITSMC-ről további információt az Áttekintés című [témakörben talál.](../../azure-monitor/platform/itsmc-overview.md)

A következő ITSM termékek/szolgáltatások támogatottak. Válassza ki a terméket a termék ITSMC-hez való csatlakoztatásáról szóló részletes információk megtekintéséhez.

- [Rendszerközpont-szolgáltatáskezelő](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance között](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]
> 
> AZ ITSM-összekötő csak felhőalapú ServiceNow-példányokhoz tud csatlakozni. A helyszíni ServiceNow-példányok jelenleg nem támogatottak.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>A System Center Service Manager csatlakoztatása az Azure-beli IT-szolgáltatáskezelő-összekötőhöz

Az alábbi szakaszok részletesen ismertetik, hogyan csatlakoztathatja a System Center Service Manager-terméket az AZURE-beli ITSMC-hez.

### <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy a következő előfeltételek teljesülnek:

- ITSMC telepítve. További információ: [Az IT-szolgáltatáskezelés összekötő megoldás hozzáadása](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- A Service Manager webalkalmazás (webalkalmazás) telepítve és konfigurálva van. A webalkalmazással kapcsolatos információk [itt](#create-and-deploy-service-manager-web-app-service)vannak.
- Hibrid kapcsolat létrehozva és konfigurálva. További információ: [Konfigurálja a hibrid kapcsolatot.](#configure-the-hybrid-connection)
- A Service Manager támogatott verziói: 2012 R2 vagy 2016.
- Felhasználói szerepkör: [Speciális operátor](https://technet.microsoft.com/library/ff461054.aspx).

### <a name="connection-procedure"></a>Csatlakozási eljárás

A System Center Service Manager-példány az ITSMC konzolhoz való csatlakoztatásához kövesse az alábbi eljárást:

1. Az Azure Portalon nyissa meg **az Összes erőforrás t,** és keresse meg a **ServiceDesk(YourWorkspaceName)**

2.  A **MUNKATERÜLETADATFORRÁSOK CSOPORTban** kattintson **az ITSM-kapcsolatok gombra.**

    ![Új kapcsolat](media/itsmc-connections/add-new-itsm-connection.png)

3. A jobb oldali ablaktábla tetején kattintson a **Hozzáadás**gombra.

4. Adja meg az alábbi táblázatban leírt információkat, majd a kapcsolat létrehozásához kattintson az **OK** gombra.

> [!NOTE]
> 
> Mindezek a paraméterek kötelezőek.

| **Mező** | **Leírás** |
| --- | --- |
| **Kapcsolat neve**   | Írja be annak a System Center Service Manager-példánynak a nevét, amelyet csatlakozni szeretne az ITSMC konzolhoz.  Ezt a nevet később használja, amikor munkaelemeket konfigurál ebben a példányban/ részletes naplóelemzésmegtekintéséhez. |
| **Partner típusa**   | Válassza a **System Center Service Manager lehetőséget.** |
| **Kiszolgáló URL-címe**   | Írja be a Service Manager webalkalmazás URL-címét. A Service Manager webalkalmazásról itt [olvashat](#create-and-deploy-service-manager-web-app-service)bővebben.
| **Ügyfélazonosító**   | Írja be a webalkalmazás hitelesítéséhez létrehozott ügyfélazonosítót (az automatikus parancsfájl használatával). További információ az automatizált szkript [itt.](../../azure-monitor/platform/itsmc-service-manager-script.md)|
| **Ügyfél titok**   | Írja be az azonosítóhoz létrehozott ügyféltitkot.   |
| **Adatok szinkronizálása**   | Jelölje ki azt a Service Manager-munkaelemet, amelyet az ITSMC-n keresztül szeretne szinkronizálni.  Ezeket a munkaelemeket a rendszer importálja a Log Analytics programba. **Beállítások:**  Incidensek, változáskérések.|
| **Adatszinkronizálási hatókör** | Írja be az elmúlt napok számát, amelyekből az adatokat el szeretné látni. **Maximális korlát:** 120 nap. |
| **Új konfigurációs elem létrehozása az ITSM-megoldásban** | Akkor válassza ezt a lehetőséget, ha az ITSM-termék konfigurációs elemeit szeretné létrehozni. Ha be van jelölve, a Log Analytics konfigurációs elemként hozza létre az érintett hitelesítési szinteket (nem létező hálózati adatok esetén) a támogatott ITSM-rendszerben. **Alapértelmezett**: letiltva. |

![Szolgáltatáskezelő-kapcsolat](media/itsmc-connections/service-manager-connection.png)

**Sikeres csatlakoztatás és szinkronizálás esetén:**

- A Service Manager kijelölt munkaelemeit a rendszer importálja az Azure **Log Analytics szolgáltatásba.** Ezeknek a munkaelemeknek az összegzését az IT Service Management Connector csempén tekintheti meg.

- Ebben a Service Manager-példányban a Log Analytics-riasztásokból vagy a naplóbejegyzésekből, illetve az Azure-riasztásokból hozhat létre incidenseket.


További információ: [ITSM-munkaelemek létrehozása az Azure-riasztásokból.](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)

### <a name="create-and-deploy-service-manager-web-app-service"></a>Service Manager webalkalmazás-szolgáltatás létrehozása és telepítése

A helyszíni szolgáltatáskezelő és az AZURE-beli ITSMC csatlakoztatásához a Microsoft létrehozott egy Service Manager webalkalmazást a GitHubon.

A Service Manager ITSM webalkalmazásának beállításához tegye a következőket:

- **Telepítse a webalkalmazást** – telepítse a webalkalmazást, állítsa be a tulajdonságokat, és hitelesítse magát az Azure AD-vel. A webalkalmazást a Microsoft által biztosított [automatikus parancsfájl](../../azure-monitor/platform/itsmc-service-manager-script.md) használatával telepítheti.
- **A hibrid kapcsolat** - [konfigurálása: A kapcsolat manuális konfigurálása.](#configure-the-hybrid-connection)

#### <a name="deploy-the-web-app"></a>A webalkalmazás telepítése
Az automatikus [parancsfájl](../../azure-monitor/platform/itsmc-service-manager-script.md) használatával telepítheti a webalkalmazást, beállíthatja a tulajdonságokat, és hitelesítheti magát az Azure AD-vel.

Futtassa a parancsfájlt a következő szükséges adatok megadásával:

- Az Azure-előfizetés részletei
- Erőforráscsoport neve
- Hely
- A Szolgáltatáskezelő kiszolgálójának adatai (kiszolgálónév, tartomány, felhasználónév és jelszó)
- A webalkalmazás webhelynév-előtagja
- ServiceBus névtér.

A parancsfájl a megadott névvel hozza létre a webalkalmazást (néhány további karakterlánccal együtt, hogy egyedivé tegye). Létrehozza a **webalkalmazás URL-címét**, **az ügyfélazonosítót** és **az ügyféltitkos kulcsot.**

Mentse az értékeket, akkor használja őket, amikor kapcsolatot hoz létre az ITSMC-vel.

**A webalkalmazás telepítésének ellenőrzése**

1. Nyissa meg az **Azure Portal** > **Erőforrásait.**
2. Jelölje ki a webalkalmazást, **és** > kattintson**az Alkalmazás beállításai parancsra.**
3. Erősítse meg a Service Manager-példányadatait, amelyeket az alkalmazás parancsfájlon keresztüli üzembe helyezésekor adott meg.

### <a name="configure-the-hybrid-connection"></a>A hibrid kapcsolat konfigurálása

Az alábbi eljárással konfigurálhatja azt a hibrid kapcsolatot, amely a Service Manager-példányt az Azure-beli ITSMC-vel kapcsolja össze.

1. Keresse meg a Service Manager webalkalmazást az **Azure Resources területen.**
2. Kattintson a **Beállítások** > **hálózata gombra.**
3. A **Hibrid kapcsolatok csoportban**kattintson **a Hibrid kapcsolatvégpontok konfigurálása**elemre.

    ![Hibrid kapcsolathálózat](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. A **Hibrid kapcsolatok** panelen kattintson a **Hibrid kapcsolat hozzáadása gombra.**

    ![Hibrid kapcsolat hozzáadása](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. A **Hibrid kapcsolatok hozzáadása** panelen kattintson az **Új hibrid kapcsolat létrehozása gombra.**

    ![Új hibrid kapcsolat](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Írja be a következő értékeket:

   - **Végpont neve**: Adja meg az új hibrid kapcsolat nevét.
   - **EndPoint Host**: A Service Manager felügyeleti kiszolgáló teljes tartományszáma.
   - **Végpontport**: 5724-es típus
   - **Servicebus-névtér**: Használjon egy meglévő servicebus névteret, vagy hozzon létre egy újat.
   - **Hely**: válassza ki a helyet.
   - **Név**: Ha létrehozod, adja meg a servicebus nevét.

     ![Hibrid kapcsolati értékek](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Kattintson az **OK** gombra a Hibrid kapcsolat létrehozása panel **bezárásához** és a hibrid kapcsolat létrehozásának megkezdéséhez.

    A hibrid kapcsolat létrehozása után a panel alatt jelenik meg.

7. A hibrid kapcsolat létrehozása után jelölje ki a kapcsolatot, és kattintson a **Kijelölt hibrid kapcsolat hozzáadása gombra.**

    ![Új hibrid kapcsolat](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>A figyelő beállításának konfigurálása

Az alábbi eljárással konfigurálhatja a figyelő beállítását a hibrid kapcsolathoz.

1. A **Hibrid kapcsolatok** panelen kattintson **a Csatlakozáskezelő letöltése** elemre, és telepítse azokra a gépekre, ahol a System Center Service Manager-példány fut.

    A telepítés befejezése után a **Hibrid csatlakozáskezelő felhasználói** felülete elérhető a **Start** menüben.

2. Kattintson **a Hibrid kapcsolatkezelő felhasználói felületére,** és a rendszer kéri az Azure-hitelesítő adatok megadását.

3. Jelentkezzen be az Azure-hitelesítő adatokkal, és válassza ki az előfizetést, ahol a hibrid kapcsolat jött létre.

4. Kattintson a **Mentés** gombra.

A hibrid kapcsolat sikeresen csatlakozott.

![sikeres hibrid kapcsolat](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> A hibrid kapcsolat létrehozása után ellenőrizze és tesztelje a kapcsolatot a telepített Service Manager webalkalmazás felkeresésével. Győződjön meg arról, hogy a kapcsolat sikeres, mielőtt megpróbálcsatlakozni az ITSMC-hez az Azure-ban.

Az alábbi mintakép a sikeres kapcsolat részleteit mutatja be:

![Hibrid kapcsolat tesztelése](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>A ServiceNow csatlakoztatása az AZURE-beli IT-szolgáltatáskezelő-összekötőhöz

Az alábbi szakaszok részletesen ismertetik, hogyan csatlakoztathatja a ServiceNow-terméket az AZURE-beli ITSMC-hez.

### <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy a következő előfeltételek teljesülnek:
- ITSMC telepítve. További információ: [Az IT-szolgáltatáskezelés összekötő megoldás hozzáadása](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- ServiceNow támogatott verziók: New York, Madrid, London, Kingston, Jakarta, Isztambul, Helsinki, Genf.
> [!NOTE]
> Az ITSMC csak a Service Now hivatalos SaaS-ajánlatát támogatja. A Service Now privát telepítései nem támogatottak. 

**A ServiceNow rendszergazdáinak a következőt kell tenniük a ServiceNow példányban:**
- Ügyfélazonosító és ügyféltitok létrehozása a ServiceNow termékhez. Az ügyfélazonosító és a titkos titok létrehozásáról az alábbi információk ban talál tájékoztatást:

    - [OAuth beállítása New York-ba](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Az OAuth beállítása Madrid számára](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth beállítása London számára](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth beállítása Kingstonhoz](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth beállítása Jakartába](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth beállítása Isztambul számára](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Az OAuth beállítása Helsinkibe](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth beállítása Genfbe](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)


- Telepítse a Felhasználói alkalmazást a Microsoft Log Analytics-integrációhoz (ServiceNow alkalmazás). [További információ](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
- Integrációs felhasználói szerepkör létrehozása a telepített felhasználói alkalmazáshoz. Az integrációs felhasználói szerepkör létrehozásáról itt [olvashat.](#create-integration-user-role-in-servicenow-app)

### <a name="connection-procedure"></a>**Csatlakozási eljárás**
ServiceNow-kapcsolat létrehozásához kövesse az alábbi eljárást:


1. Az Azure Portalon nyissa meg **az Összes erőforrás t,** és keresse meg a **ServiceDesk(YourWorkspaceName)**

2.  A **MUNKATERÜLETADATFORRÁSOK CSOPORTban** kattintson **az ITSM-kapcsolatok gombra.**
    ![Új kapcsolat](media/itsmc-connections/add-new-itsm-connection.png)

3. A jobb oldali ablaktábla tetején kattintson a **Hozzáadás**gombra.

4. Adja meg az alábbi táblázatban leírt információkat, majd a kapcsolat létrehozásához kattintson az **OK** gombra.


> [!NOTE]
> Mindezek a paraméterek kötelezőek.

| **Mező** | **Leírás** |
| --- | --- |
| **Kapcsolat neve**   | Írja be annak a ServiceNow példánynak a nevét, amelyet csatlakozni szeretne az ITSMC-hez.  Ezt a nevet később használja a Log Analytics-ben, ha munkaelemeket konfigurál ebben az ITSM/részletes naplóelemzésben. |
| **Partner típusa**   | Válassza a **ServiceNow**lehetőséget. |
| **Felhasználónév**   | Írja be a ServiceNow alkalmazásban létrehozott integrációs felhasználónevet az ITSMC-vel való kapcsolat támogatásához. További információ: [A ServiceNow alkalmazás felhasználói szerepkörének létrehozása.](#create-integration-user-role-in-servicenow-app)|
| **Jelszó**   | Írja be a felhasználónévhez társított jelszót. **Megjegyzés:** A felhasználónév és a jelszó csak hitelesítési jogkivonatok létrehozásához használatos, és az ITSMC szolgáltatáson belül sehol sem tárolódnak.  |
| **Kiszolgáló URL-címe**   | Írja be annak a ServiceNow példánynak az URL-címét, amelyet csatlakozni szeretne az ITSMC-hez. Az URL-címnek egy támogatott SaaS-verzióra kell mutatnia, amelynek utótagja ".servicenow.com".|
| **Ügyfélazonosító**   | Írja be az OAuth2 hitelesítéshez használni kívánt ügyfélazonosítót, amelyet korábban hozott létre.  További információ az ügyfélazonosító és a titkos adat létrehozásáról: [OAuth Setup](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Ügyfél titok**   | Írja be az azonosítóhoz létrehozott ügyféltitkot.   |
| **Adatszinkronizálási hatókör**   | Válassza ki a ServiceNow munkaelemeket, amelyeket szinkronizálni szeretne az Azure Log Analytics szolgáltatással az ITSMC-n keresztül.  A kijelölt értékek et a rendszer importálja a naplóelemzésbe.   **Beállítások:**  Incidensek és változáskérések.|
| **Adatok szinkronizálása** | Írja be az elmúlt napok számát, amelyekből az adatokat el szeretné látni. **Maximális korlát:** 120 nap. |
| **Új konfigurációs elem létrehozása az ITSM-megoldásban** | Akkor válassza ezt a lehetőséget, ha az ITSM-termék konfigurációs elemeit szeretné létrehozni. Ha be van jelölve, az ITSMC az érintett hitelesítéshelyeket konfigurációs elemként hozza létre (nem létező hitelesítés- ek esetén) a támogatott ITSM-rendszerben. **Alapértelmezett**: letiltva. |

![ServiceNow kapcsolat](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**Sikeres csatlakoztatás és szinkronizálás esetén:**

- A ServiceNow-példány kijelölt munkaelemei t importálnak az Azure **Log Analytics alkalmazásba.** Ezeknek a munkaelemeknek az összegzését az IT Service Management Connector csempén tekintheti meg.

- Incidenseket hozhat létre a Log Analytics-riasztásokból vagy a naplóbejegyzésekből, illetve az Azure-riasztásokból ebben a ServiceNow-példányban.

További információ: [ITSM-munkaelemek létrehozása az Azure-riasztásokból.](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)

### <a name="create-integration-user-role-in-servicenow-app"></a>Integrációs felhasználói szerepkör létrehozása a ServiceNow alkalmazásban

Felhasználó a következő eljárást:

1. Keresse fel a [ServiceNow áruházat,](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) és telepítse a ServiceNow felhasználói alkalmazását és a **Microsoft OMS-integrációt** a ServiceNow-példányba.
   
   >[!NOTE]
   >A Microsoft Operations Management Suite (OMS) szolgáltatásról az Azure Monitorra való folyamatos áttérés részeként az OMS-t mostantól Log Analytics-nek nevezzük.     
2. A telepítés után keresse fel a ServiceNow példány bal oldali navigációs sávját, keressen rá, és válassza a Microsoft OMS-integrátor lehetőséget.  
3. Kattintson **a Telepítési ellenőrzőlista gombra.**

   Az állapot **nem teljes** állapotban jelenik meg, ha a felhasználói szerepkör még nem jött létre.

4. A szövegmezőkben az **Integrációs felhasználó létrehozása**csoportban adja meg annak a felhasználónak a felhasználó nevét, aki csatlakozhat az ITSMC-hez az Azure-ban.
5. Adja meg a felhasználó jelszavát, majd kattintson az **OK gombra.**  

> [!NOTE]
> 
> Ezeket a hitelesítő adatokat használja a ServiceNow-kapcsolat azure-beli hozlétre.

Az újonnan létrehozott felhasználó az alapértelmezett szerepkörökkel van elosztva.

**Alapértelmezett szerepkörök**:
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   Itil
-   template_editor
-   view_changer

A felhasználó sikeres létrehozása után a **Telepítés ellenőrzése ellenőrzőlista** állapota a Befejezett elemre kerül, és felsorolja az alkalmazáshoz létrehozott felhasználói szerepkör részleteit.

> [!NOTE]
> 
> Az ITSM-összekötő incidenseket küldhet a ServiceNow-nak anélkül, hogy a ServiceNow-példányra más modulok at települne. Ha EventManagement modult használ a ServiceNow-példányban, és az összekötő használatával szeretne eseményeket vagy riasztásokat létrehozni a ServiceNow-ban, adja hozzá a következő szerepköröket az integrációs felhasználóhoz:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>A Provance csatlakoztatása az AZURE-beli IT-szolgáltatáskezelő összekötőhöz

Az alábbi szakaszok részletesen ismertetik, hogyan csatlakoztathatja provance-termékét az AZURE-beli ITSMC-hez.


### <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy a következő előfeltételek teljesülnek:


- ITSMC telepítve. További információ: [Az IT-szolgáltatáskezelés összekötő megoldás hozzáadása](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- A Provance-alkalmazást regisztrálni kell az Azure AD-vel, és elérhetővé kell tenni az ügyfélazonosítót. További információt az [active directory hitelesítés konfigurálásáról](../../app-service/configure-authentication-provider-aad.md)talál.

- Felhasználói szerepkör: Rendszergazda.

### <a name="connection-procedure"></a>Csatlakozási eljárás

Provance-kapcsolat létrehozásához kövesse az alábbi eljárást:

1. Az Azure Portalon nyissa meg **az Összes erőforrás t,** és keresse meg a **ServiceDesk(YourWorkspaceName)**

2.  A **MUNKATERÜLETADATFORRÁSOK CSOPORTban** kattintson **az ITSM-kapcsolatok gombra.**
    ![Új kapcsolat](media/itsmc-connections/add-new-itsm-connection.png)

3. A jobb oldali ablaktábla tetején kattintson a **Hozzáadás**gombra.

4. Adja meg az alábbi táblázatban leírt információkat, majd a kapcsolat létrehozásához kattintson az **OK** gombra.

> [!NOTE]
> 
> Mindezek a paraméterek kötelezőek.

| **Mező** | **Leírás** |
| --- | --- |
| **Kapcsolat neve**   | Írja be annak a Provance-példánynak a nevét, amelyet csatlakozni szeretne az ITSMC-hez.  Ezt a nevet később akkor használja, amikor munkaelemeket konfigurál ebben az ITSM/részletes naplóelemzés megtekintéséhez. |
| **Partner típusa**   | Válassza **a Provance**lehetőséget. |
| **Felhasználónév**   | Írja be az ITSMC konzolhoz csatlakozni képes felhasználónevet.    |
| **Jelszó**   | Írja be a felhasználónévhez társított jelszót. **Megjegyzés:** A felhasználónév és a jelszó csak hitelesítési jogkivonatok létrehozására szolgál, és az ITSMC szolgáltatáson belül sehol nem tárolódnak._|
| **Kiszolgáló URL-címe**   | Írja be az ITSMC-hez csatlakozni kívánt Provance-példány URL-címét. |
| **Ügyfélazonosító**   | Írja be a kapcsolat hitelesítéséhez az ügyfélazonosítót, amelyet a Provance-példányban hozott létre.  Az ügyfélazonosítóról további információt az [active directory hitelesítéskonfigurálásáról](../../app-service/configure-authentication-provider-aad.md)talál. |
| **Adatszinkronizálási hatókör**   | Válassza ki a Provance munkaelemeket, amelyeket szinkronizálni szeretne az Azure Log Analytics szolgáltatással az ITSMC-n keresztül.  Ezeket a munkaelemeket a rendszer importálja a naplóelemzésbe.   **Beállítások:**   Incidensek, változáskérések.|
| **Adatok szinkronizálása** | Írja be az elmúlt napok számát, amelyekből az adatokat el szeretné látni. **Maximális korlát:** 120 nap. |
| **Új konfigurációs elem létrehozása az ITSM-megoldásban** | Akkor válassza ezt a lehetőséget, ha az ITSM-termék konfigurációs elemeit szeretné létrehozni. Ha be van jelölve, az ITSMC az érintett hitelesítéshelyeket konfigurációs elemként hozza létre (nem létező hitelesítés- ek esetén) a támogatott ITSM-rendszerben. **Alapértelmezett**: letiltva.|

![Provance csatlakozás](media/itsmc-connections/itsm-connections-provance-latest.png)

**Sikeres csatlakoztatás és szinkronizálás esetén:**

- A Provance-példány ból kiválasztott munkaelemek et importálja az Azure **Log Analytics.** Ezeknek a munkaelemeknek az összegzését az IT Service Management Connector csempén tekintheti meg.

- Ebben a Provance-példányban hozhat létre incidenseket a Log Analytics-riasztásokból vagy a naplóbejegyzésekből vagy az Azure-riasztásokból.

További információ: [ITSM-munkaelemek létrehozása az Azure-riasztásokból.](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Csatlakoztassa a Cherwellt az AZURE-beli IT-szolgáltatáskezelő összekötőhöz

Az alábbi szakaszok részletesen ismertetik, hogyan csatlakoztathatja a Cherwell-terméket az AZURE-beli ITSMC-hez.

### <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy a következő előfeltételek teljesülnek:

- ITSMC telepítve. További információ: [Az IT-szolgáltatáskezelés összekötő megoldás hozzáadása](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Ügyfélazonosító létrehozva. További információ: [Ügyfélazonosító létrehozása a Cherwell számára.](#generate-client-id-for-cherwell)
- Felhasználói szerepkör: Rendszergazda.

### <a name="connection-procedure"></a>Csatlakozási eljárás

Provance-kapcsolat létrehozásához kövesse az alábbi eljárást:

1. Az Azure Portalon nyissa meg **az Összes erőforrás t,** és keresse meg a **ServiceDesk(YourWorkspaceName)**

2.  A **MUNKATERÜLETADATFORRÁSOK CSOPORTban** kattintson **az ITSM-kapcsolatok gombra.**
    ![Új kapcsolat](media/itsmc-connections/add-new-itsm-connection.png)

3. A jobb oldali ablaktábla tetején kattintson a **Hozzáadás**gombra.

4. Adja meg az alábbi táblázatban leírt információkat, majd a kapcsolat létrehozásához kattintson az **OK** gombra.

> [!NOTE]
> 
> Mindezek a paraméterek kötelezőek.

| **Mező** | **Leírás** |
| --- | --- |
| **Kapcsolat neve**   | Írja be annak a Cherwell-példánynak a nevét, amelyet csatlakozni szeretne az ITSMC-hez.  Ezt a nevet később akkor használja, amikor munkaelemeket konfigurál ebben az ITSM/részletes naplóelemzés megtekintéséhez. |
| **Partner típusa**   | Válassza **a Cherwell lehetőséget.** |
| **Felhasználónév**   | Írja be az ITSMC konzolhoz csatlakozni képes Cherwell-felhasználónevet. |
| **Jelszó**   | Írja be a felhasználónévhez társított jelszót. **Megjegyzés:** A felhasználónév és a jelszó csak hitelesítési jogkivonatok létrehozására szolgál, és az ITSMC szolgáltatáson belül sehol sem tárolódnak.|
| **Kiszolgáló URL-címe**   | Írja be a Cherwell-példány URL-címét, amelyet csatlakozni szeretne az ITSMC-hez. |
| **Ügyfélazonosító**   | Írja be a kapcsolat hitelesítéséhez az ügyfélazonosítót, amelyet a Cherwell-példányban hozott létre.   |
| **Adatszinkronizálási hatókör**   | Jelölje ki az ITSMC-n keresztül szinkronizálni kívánt Cherwell munkaelemeket.  Ezeket a munkaelemeket a rendszer importálja a naplóelemzésbe.   **Beállítások:**  Incidensek, változáskérések. |
| **Adatok szinkronizálása** | Írja be az elmúlt napok számát, amelyekből az adatokat el szeretné látni. **Maximális korlát:** 120 nap. |
| **Új konfigurációs elem létrehozása az ITSM-megoldásban** | Akkor válassza ezt a lehetőséget, ha az ITSM-termék konfigurációs elemeit szeretné létrehozni. Ha be van jelölve, az ITSMC az érintett hitelesítéshelyeket konfigurációs elemként hozza létre (nem létező hitelesítés- ek esetén) a támogatott ITSM-rendszerben. **Alapértelmezett**: letiltva. |


![Provance csatlakozás](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**Sikeres csatlakoztatás és szinkronizálás esetén:**

- A Cherwell-példány ból kiválasztott munkaelemek et importálja az Azure **Log Analytics.** Ezeknek a munkaelemeknek az összegzését az IT Service Management Connector csempén tekintheti meg.

- Ebben a Cherwell-példányban létrehozhat incidenseket a Log Analytics-riasztásokból vagy a naplóbejegyzésekből, illetve az Azure-riasztásokból.

További információ: [ITSM-munkaelemek létrehozása az Azure-riasztásokból.](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)

### <a name="generate-client-id-for-cherwell"></a>Ügyfélazonosító létrehozása a Cherwell számára

A Cherwell ügyfélazonosítójának/kulcsának létrehozásához kövesse az alábbi eljárást:

1. Jelentkezzen be a Cherwell példány admin.
2. Kattintson **a Security** > **Edit REST API ügyfélbeállításaigombra.**
3. Válassza az Új**ügyfélügyfél-titok** **Create new client** > létrehozása lehetőséget.

    ![Cherwell felhasználói azonosító](media/itsmc-connections/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>További lépések
 - [ITSM-munkaelemek létrehozása az Azure-riasztásokból](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
