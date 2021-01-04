---
title: ServiceNow összekötése IT-szolgáltatásmenedzsmenti csatoló
description: Ez a cikk azt ismerteti, hogyan ServiceNow a IT-szolgáltatásmenedzsmenti csatoló (ITSMC) Azure Monitor a ITSM-munkaelemek központilag figyeléséhez és kezeléséhez.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 662c36e4f0082c376a6e250e9a0885f0cd225964
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2020
ms.locfileid: "97729658"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>ServiceNow összekötése IT-szolgáltatásmenedzsmenti csatoló

Ez a cikk azt ismerteti, hogyan konfigurálható a ServiceNow-példány és a IT-szolgáltatásmenedzsmenti csatoló (ITSMC) közötti kapcsolat a munkaelemek központilag felügyelhető Log Analyticsban.

A következő szakaszokban részletesen ismertetjük, hogyan csatlakoztathatók a ServiceNow-termékek az Azure-beli ITSMC.

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:
- A ITSMC telepítve van. További információ: [a it-szolgáltatásmenedzsmenti csatoló megoldás hozzáadása](./itsmc-definition.md#add-it-service-management-connector).
- ServiceNow támogatott verziók: Orlando, New York, Madrid, London, Kingston, Jakarta, Isztambul, Helsinki, Genf.
- A Azure Monitor által elküldett riasztások ma a következő elemek egyikének ServiceNow hozhatók létre: események, incidensek vagy riasztások.
> [!NOTE]
> A ITSMC csak a szolgáltatás hivatalos SaaS-ajánlatát támogatja. A szolgáltatás privát telepítései jelenleg nem támogatottak. 

A **ServiceNow-rendszergazdáknak a következőket kell tenniük a ServiceNow-példányban**:
- Ügyfél-azonosító és ügyfél-titkos kód előállítása a ServiceNow termékhez. Az ügyfél-azonosító és a titkos kulcs létrehozásával kapcsolatos információkért tekintse meg a szükséges információkat a következő információk alapján:

    - [OAuth beállítása az Orlando-hoz](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [A New York-i OAuth beállítása](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth beállítása Madridhoz](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [A OAuth beállítása Londonban](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [A OAuth beállítása a Kingston számára](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth beállítása a Jakarta számára](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Az OAuth beállítása Isztambulhoz](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [A OAuth beállítása Helsinkihez](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [A OAuth beállítása Genfben](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)
> [!NOTE]
> A "OAuth beállítása" definíciójának részeként az alábbiakat javasoljuk:
>
> 1) Frissítse **a frissítési token élettartamát 90 napra (7 776 000 másodperc):** A [OAuth beállításának](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_SettingUpOAuth.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696739125&sdata=Q7mF6Ej8MCupKaEJpabTM56EDZ1T8vFVyihhoM594aA%3D&reserved=0) részeként a 2. fázisban: [hozzon létre egy végpontot ahhoz, hogy az ügyfelek hozzáférhessenek a példányhoz](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_CreateEndpointforExternalClients.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696749123&sdata=hoAJHJAFgUeszYCX1Q%2FXr4N%2FAKiFcm5WV7mwR2UqeWA%3D&reserved=0) a végpont definíciója után, a ServiceNow panelen keresse meg a rendszer OAuth, mint az alkalmazás beállításjegyzékének kiválasztása lehetőséget. Válassza ki a definiált OAuth nevét, és frissítse a frissítési jogkivonat élettartamának mezőjét 7 776 000-re (90 nap másodpercben).
> A végén kattintson a frissítés gombra.
> 2) **Javasoljuk, hogy hozzon létre egy belső eljárást annak biztosítására, hogy a kapcsolat életben maradjon:** A jogkivonat frissítésének élettartama alapján. Győződjön meg arról, hogy a következő műveletek végrehajtása a korábbi frissítési jogkivonat várható lejárati ideje (néhány nappal azelőtt, hogy a frissítési jogkivonat élettartama lejár):
>
>     1. [ITSM-összekötő konfigurálásának manuális szinkronizálási folyamatának befejezése](./itsmc-resync-servicenow.md)
>     2. Vonja vissza a régi frissítési tokent, mivel a régi kulcsok biztonsági okokból való megőrzése nem ajánlott. A ServiceNow panelen keresse meg a rendszer OAuth, mint a tokenek kezelése lehetőséget. A OAuth neve és a lejárati dátum szerint válassza ki a régi tokent a listából.
> ![A SNOW System OAuth definíciója](media/itsmc-connections/snow-system-oauth.png)
>     3. Kattintson a hozzáférés visszavonása, majd a visszavonás lehetőségre.

- Telepítse a Microsoft Log Analytics Integration (ServiceNow app) felhasználói alkalmazását. [További információ](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
> [!NOTE]
> A ITSMC csak a ServiceNow áruházból letöltött Microsoft Log Analytics integrációs szolgáltatáshoz használható hivatalos felhasználói alkalmazást támogatja. A ITSMC nem támogatja a kód betöltését a ServiceNow oldalon vagy a hivatalos ServiceNow-megoldás részét nem képező alkalmazásban. 
- Integrációs felhasználói szerepkör létrehozása a telepített felhasználói alkalmazáshoz. Az integrációs felhasználói szerepkör létrehozásával kapcsolatos információkat [itt](#create-integration-user-role-in-servicenow-app)találja.

## <a name="connection-procedure"></a>**Csatlakoztatási eljárás**
ServiceNow-kapcsolatok létrehozásához kövesse az alábbi eljárást:


1. A Azure Portalban lépjen a **minden erőforrás** elemre, és keresse meg a **ügyfélszolgálati (YourWorkspaceName)**

2.  A **munkaterület-ADATforrások** területen kattintson az **ITSM-kapcsolatok** elemre.
    ![Új kapcsolat](media/itsmc-connections/add-new-itsm-connection.png)

3. A jobb oldali ablaktábla tetején kattintson a **Hozzáadás** gombra.

4. Adja meg az adatokat az alábbi táblázatban leírtak szerint, majd kattintson az **OK** gombra a kapcsolódás létrehozásához.


> [!NOTE]
> Ezeket a paramétereket kötelező megadni.

| **Mező** | **Leírás** |
| --- | --- |
| **Kapcsolat neve**   | Írja be annak a ServiceNow-példánynak a nevét, amelyhez csatlakozni kíván a ITSMC.  Ezt a nevet később Log Analytics, ha munkaelemeket konfigurál ebben a ITSM/részletes log Analytics nézetben. |
| **Partner típusa**   | Válassza a **ServiceNow** lehetőséget. |
| **Felhasználónév**   | Írja be a ServiceNow alkalmazásban létrehozott integrációs felhasználónevet, hogy támogassa a ITSMC való kapcsolódást. További információ: [ServiceNow-alkalmazás felhasználói szerepkör létrehozása](#create-integration-user-role-in-servicenow-app).|
| **Jelszó**   | Írja be a felhasználónévhez tartozó jelszót. **Megjegyzés**: a felhasználónevet és a jelszót csak a hitelesítési jogkivonatok létrehozásához használja a rendszer, és a ITSMC szolgáltatásban bárhol tárolja őket.  |
| **Kiszolgáló URL-címe**   | Írja be annak az ServiceNow-példánynak az URL-címét, amelyhez csatlakozni szeretne a ITSMC. Az URL-címnek a ". servicenow.com" utótaggal rendelkező, támogatott SaaS-verzióra kell mutatnia.|
| **Ügyfél-azonosító**   | Írja be a korábban létrehozott OAuth2-hitelesítéshez használni kívánt ügyfél-azonosítót.  További információ az ügyfél-azonosító és a titkos kulcs létrehozásáról:   [OAuth Setup](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Ügyfél titka**   | Adja meg az AZONOSÍTÓhoz generált ügyfél-titkot.   |
| **Adatszinkronizálási hatókör**   | Válassza ki azokat a ServiceNow munkaelemeket, amelyeket szinkronizálni szeretne az Azure Log Analytics a ITSMC keresztül.  A kiválasztott értékeket a rendszer a log analyticsbe importálja.   **Beállítások:**  Incidensek és módosítási kérelmek.|
| **Adatszinkronizálás** | Adja meg az elmúlt napok számát, amelyből az adatok származnak. **Maximális korlát**: 120 nap. |
| **Új konfigurációs elemek létrehozása a ITSM-megoldásban** | Akkor válassza ezt a lehetőséget, ha a ITSM termékben szeretné létrehozni a konfigurációs elemeket. Ha be van jelölve, a ITSMC létrehozza az érintett CIs-t konfigurációs elemekként (nem létező CIs esetén) a támogatott ITSM-rendszeren. **Alapértelmezett**: letiltva. |

![ServiceNow-kapcsolatok](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**Sikeres csatlakozás és szinkronizálás**:

- A ServiceNow-példány kiválasztott munkaelemeit az Azure **log Analyticsba** importálja a rendszer. A munkaelemek összegzését a IT-szolgáltatásmenedzsmenti csatoló csempén tekintheti meg.

- Incidenseket hozhat létre Log Analytics riasztásokból, illetve a naplóbejegyzésekből vagy az Azure-riasztásokból ebben az ServiceNow-példányban.

> [!NOTE]
> A ServiceNow-ben a kérelmek óránkénti száma. A korlát konfigurálásához használja a "bejövő REST API arány korlátozása" kifejezést a ServiceNow-példányban.

## <a name="create-integration-user-role-in-servicenow-app"></a>Integrációs felhasználói szerepkör létrehozása a ServiceNow alkalmazásban

A felhasználó az alábbi eljárást:

1. Látogasson el a [ServiceNow áruházba](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) , és telepítse a **ServiceNow és a Microsoft OMS-integrációs felhasználói alkalmazást** a ServiceNow-példányba.
   
   >[!NOTE]
   >A Microsoft Operations Management Suite (OMS) és a Azure Monitor közötti folyamatos áttérés részeként a OMS-et már Log Analyticsnak nevezzük.     
2. A telepítést követően nyissa meg a ServiceNow-példány bal oldali navigációs sávját, keresse meg és válassza a Microsoft OMS-integrátor elemet.  
3. Kattintson a **telepítési ellenőrzőlista** elemre.

   Ha a felhasználói szerepkör még létre van hozva, az állapot  **nem fejeződött be** .

4. Az **integrációs felhasználó létrehozása** elem melletti szövegmezőbe írja be annak a felhasználónak a felhasználónevét, aki CSATLAKOZHAT a ITSMC az Azure-ban.
5. Adja meg a felhasználó jelszavát, majd kattintson **az OK** gombra.  

> [!NOTE]
> Ezeket a hitelesítő adatokat használja a ServiceNow-kapcsolatok az Azure-ban való létrehozásához.

Az újonnan létrehozott felhasználó megjelenik a hozzárendelt alapértelmezett szerepkörökkel.

**Alapértelmezett szerepkörök**:
- personalize_choices
- import_transformer
-   x_mioms_microsoft. user
-   ITIL
-   template_editor
-   view_changer

A felhasználó sikeres létrehozása után a **telepítési ellenőrzőlista** állapotának állapota befejeződött, amely felsorolja az alkalmazáshoz létrehozott felhasználói szerepkör részleteit.

> [!NOTE]
> A ITSM-csatoló a ServiceNow-példányra telepített más modulok nélkül is küldhet incidenseket a ServiceNow. Ha a ServiceNow-példányban EventManagement-modult használ, és az összekötő használatával szeretne eseményeket vagy riasztásokat létrehozni a ServiceNow-ben, adja hozzá a következő szerepköröket az integrációs felhasználóhoz:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="next-steps"></a>További lépések

* [ITSM-csatoló áttekintése](itsmc-overview.md)
* [ITSM-munkaelemek létrehozása az Azure-riasztásokból](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Hibaelhárítás az ITSM-összekötőben](./itsmc-resync-servicenow.md)