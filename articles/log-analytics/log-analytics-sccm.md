---
title: Adatforrások csatlakoztatása a Configuration Manager szolgáltatáshoz |} Microsoft Docs
description: Ez a cikk bemutatja a csatlakoztatása a Configuration Manager szolgáltatáshoz, és indítsa el az adatok elemzése lépéseket.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: magoedte
ms.openlocfilehash: 5ff0687fe99f0853e29e5f0d814a8555c367027c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
ms.locfileid: "30283996"
---
# <a name="connect-configuration-manager-to-log-analytics"></a>Adatforrások csatlakoztatása a Configuration Manager szolgáltatáshoz
A System Center Configuration Manager környezet az Azure Naplóelemzés szolgáltatáshoz csatlakozhat szinkronizálási eszköz gyűjtemény adatait, és ezeket a gyűjteményeket, Naplóelemzés és az Azure Automation hivatkozik.  

## <a name="prerequisites"></a>Előfeltételek

A Naplóelemzési támogatja a System Center Configuration Manager aktuális ágának, 1606 vagy újabb verziója.  

## <a name="configuration-overview"></a>Konfigurálása – áttekintés
Az alábbi lépéseket a Configuration Manager-integráció konfigurálása a Naplóelemzési lépéseket foglalja össze.  

1. Az Azure portálon regisztrálja a Configuration Manager egy webalkalmazás és/vagy webes API-alkalmazást, és gondoskodjon arról, hogy az ügyfél-azonosító és a titkos ügyfélkulcsot a regisztráció az Azure Active Directoryból származó. Lásd: [erőforrásokat elérő alkalmazás és szolgáltatás egyszerű létrehozásához az Active Directory használata portal](../azure-resource-manager/resource-group-create-service-principal-portal.md) részletesen bemutatja, hogyan hajthat végre ezt a lépést.
2. Az Azure-portálon [adja meg a Configuration Manager (a regisztrált webalkalmazás) Naplóelemzési hozzáféréssel rendelkező](#grant-configuration-manager-with-permissions-to-log-analytics).
3. A Configuration Managerben [-kapcsolatot az OMS-kapcsolat hozzáadása varázsló használatával](#add-an-oms-connection-to-configuration-manager).
4. A Configuration Managerben [a kapcsolat tulajdonságainak frissítéséhez](#update-oms-connection-properties) Ha a jelszó vagy ügyfél titkos kulcs soha lejár vagy elvész.
5. [Töltse le és telepítse a Microsoft Monitoring Agent](#download-and-install-the-agent) futtató számítógépen a Configuration Manager service szolgáltatáskapcsolódási pont helyrendszer-szerepkörrel. Az ügynök a Configuration Manager-adatokat küld a Naplóelemzési munkaterület.
6. A Naplóelemzési [gyűjteményeket importálhat a Configuration Manager](#import-collections) , számítógépcsoportokat.
7. A Naplóelemzési, megtekintheti az adatokat a Configuration Manager alkalmazásból, [számítógépcsoportok](log-analytics-computer-groups.md).

További az OMS-be, a Configuration Manager összekapcsolásával kapcsolatos [szinkronizálni az adatokat a Configuration Manager a Microsoft Operations Management Suite](https://technet.microsoft.com/library/mt757374.aspx).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Támogatás a Configuration Manager szolgáltatáshoz engedélyekkel
A következő eljárásban számára biztosítson a *közreműködő* a Naplóelemzési munkaterület az AD-alkalmazást és egy egyszerű szolgáltatást a korábban létrehozott a Configuration Manager szerepkör.  Ha még nem rendelkezik a munkaterületen, lásd: [munkaterület létrehozása az Azure Naplóelemzés](log-analytics-quick-create-workspace.md) a folytatás előtt.  Ez lehetővé teszi, hogy a Configuration Manager a hitelesítéshez és csatlakozáshoz a Naplóelemzési munkaterületet.  

> [!NOTE]
> Log Analytics a Configuration Manager engedélyeket kell megadnia. Ellenkező esetben hibaüzenetet kap, ha a konfigurációs varázsló a Configuration Manager alkalmazásban.
>

1. Az Azure portálon kattintson **minden szolgáltatás** bal felső sarokban található. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.<br><br> ![Azure Portal](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. A Naplóelemzési munkaterület, jelölje ki a módosítandó munkaterület.
3. A bal oldali ablaktáblán válassza ki a **hozzáférés-vezérlés (IAM)**.
4. A hozzáférés-vezérlést oldalon kattintson **Hozzáadás** és a **engedélyek hozzáadása** ablaktáblán jelenik meg.
5. Az a **engedélyek hozzáadása** ablaktáblán, a a **szerepkör** legördülő listában válassza ki a **közreműködő** szerepkör.  
6. Az a **való hozzáférés hozzárendelése** legördülő listában válassza ki a korábban létrehozott ad a Configuration Manager alkalmazást, és kattintson **OK**.  

## <a name="download-and-install-the-agent"></a>Töltse le és telepítse az ügynököt
A cikk [csatlakozás Windows-számítógépek számára az Azure Naplóelemzés szolgáltatás](log-analytics-agent-windows.md) megérteni a rendelkezésre álló módszerek a Configuration Manager szolgáltatást futtató számítógépen a Microsoft Monitoring Agent telepítése Szolgáltatáskapcsolódási pont helyrendszerszerepkör.  

## <a name="add-an-oms-connection-to-configuration-manager"></a>Az OMS-kapcsolat hozzáadása a Configuration Managerhez
Ahhoz, hogy az OMS-kapcsolat hozzáadása, a Configuration Manager környezetet kell rendelkeznie a [szolgáltatáskapcsolódási pont](https://technet.microsoft.com/library/mt627781.aspx) online módot.

1. Az a **felügyeleti** munkaterület a Configuration Manager, válassza ki **OMS összekötő**. Ekkor megnyílik a **OMS kapcsolat varázsló**. Kattintson a **Tovább** gombra.
2. Az a **általános** képernyőjén ellenőrizze, hogy elvégezte-e az alábbi műveleteket, és, hogy az egyes elemekről részletek rendelkezik, majd válassza ki, **következő**.

   1. Az Azure portálon, a webalkalmazás és/vagy webes API-alkalmazás néven már regisztrált a Configuration Manager, és hogy rendelkezik a [a regisztrációt az ügyfél-azonosító](../active-directory/active-directory-integrating-applications.md).
   2. Az Azure-portálon létrehozott egy alkalmazás titkos kulcs a regisztrált alkalmazás Azure Active Directoryban.  
   3. Az Azure portálon a megadott a regisztrált web app az OMS hozzáférési engedélyt.  
      ![Kapcsolat az OMS varázsló Általános lapja](./media/log-analytics-sccm/sccm-console-general01.png)
3. Az a **Azure Active Directory** képernyőn, a kapcsolat konfigurálása a Naplóelemzési azáltal, hogy a **bérlői**, **ügyfél-azonosító**, és **ügyfél Titkos kulcs**, majd jelölje be **következő**.  
   ![Kapcsolat az OMS varázsló Azure Active Directory lap](./media/log-analytics-sccm/sccm-wizard-tenant-filled03.png)
4. Ha Ön valósítható meg az egyéb eljárások sikeres legyen, majd az adatokat a a **OMS-kapcsolat konfigurációs** képernyő automatikusan meg fog jelenni ezen a lapon. A kapcsolódási beállítások adatait meg kell jelennie a a **Azure-előfizetés**, **Azure erőforráscsoport**, és **Operations Management Suite-munkaterület**.  
   ![Kapcsolat OMS varázsló OMS kapcsolati lapra](./media/log-analytics-sccm/sccm-wizard-configure04.png)
5. A varázsló a korábban megadott információk segítségével Naplóelemzés szolgáltatás csatlakozik. Válassza ki a szinkronizálás a szolgáltatással, és kattintson a kívánt eszközgyűjtemények **Hozzáadás**.  
   ![Gyűjtemények kiválasztása](./media/log-analytics-sccm/sccm-wizard-add-collections05.png)
6. Ellenőrizze, hogy a kapcsolat beállításait a **összegzés** képernyőt, majd válassza ki **következő**. A **folyamatban** képernyő kapcsolat állapotát jeleníti meg, majd kell **Complete**.

> [!NOTE]
> A legfelső szintű helyet a hierarchiában a Naplóelemzési kell csatlakoztatni. Ha önálló elsődleges hely kapcsolódni Naplóelemzési, és adja hozzá a központi adminisztrációs hely a környezetben, akkor törölje és hozza létre a kapcsolatot az új hierarchiában.
>
>

Miután a Configuration Manager szolgáltatáshoz, adja hozzá vagy távolítson el gyűjteményt, és a kapcsolat tulajdonságainak megtekintéséhez.

## <a name="update-log-analytics-connection-properties"></a>A Naplóelemzési kapcsolat tulajdonságainak frissítéséhez
Ha egy jelszót vagy az ügyfél titkos kulcs soha lejár vagy elvész, szüksége lesz manuálisan frissíteni a Naplóelemzési kapcsolat tulajdonságait.

1. Keresse meg a Configuration Managerben **Felhőszolgáltatások**, majd jelölje be **OMS-összekötő** megnyitásához a **OMS kapcsolat tulajdonságai** lap.
2. Ezen a lapon kattintson a **Azure Active Directory** fülre kattintva megtekintheti a **bérlői**, **ügyfél-azonosító**, **ügyfél titkos kulcs lejárati**. **Győződjön meg arról** a **titkos ügyfélkulcsot** , ha már lejárt.

## <a name="import-collections"></a>Gyűjtemények importálása
Az OMS-kapcsolat hozzáadása a Configuration Manager és az ügynök telepítése után azon a számítógépen, amelyen a Configuration Manager szolgáltatáskapcsolódási pont helyrendszerszerepkör, a következő lépés annak gyűjteményeket importálhat a Configuration Manager-, Log Analyticshez Számítógépcsoportok.

Miután végrehajtotta a kezdeti konfigurációs eszközgyűjtemények importálása a hierarchiában, a gyűjtemény tagsági információk nélkül 3 óránként tagságát naprakészen tartása. Ha szeretné, tiltsa le ezt bármikor.

1. Az Azure portálon kattintson **minden szolgáltatás** bal felső sarokban található. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
2. A Naplóelemzési munkaterület, jelölje ki a munkaterületen, a Configuration Manager regisztrálva van.  
3. Válassza ki a **Speciális beállítások** elemet.<br><br> ![A Log Analytics speciális beállításai](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Válassza ki **számítógépcsoportok** majd **SCCM**.  
5. Válassza ki **importálása a Configuration Manager gyűjteménytagságok** majd **mentése**.  
   ![Számítógépcsoportok - SCCM lap](./media/log-analytics-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>A Configuration Manager alkalmazásból adatok megtekintése
Miután az OMS-kapcsolat hozzáadása a Configuration Manager és az ügynök telepítve azon a számítógépen, amelyen a Configuration Manager service szolgáltatáskapcsolódási pont helyrendszer-szerepkörrel, az ügynök küld adatokat a Naplóelemzési. A Naplóelemzési, a Configuration Manager-gyűjtemények jelennek meg [számítógépcsoportok](log-analytics-computer-groups.md). Megtekintheti a csoportok a **Configuration Manager** lapon az **Settings\Computer csoportok**.

Miután a gyűjtemények importálása, lásd: gyűjteménytagságok, hogy hány számítógépen észlelt. Megtekintheti az importált gyűjtemények száma is.

![Számítógépcsoportok - SCCM lap](./media/log-analytics-sccm/sccm-computer-groups02.png)

Amikor egy kattint, keresési jelenik meg, amelyen az importált csoportok minden vagy minden csoporthoz tartozó összes számítógép. Használatával [naplófájl-keresési](log-analytics-log-searches.md), elindíthatja a Configuration Manager-adatokat részletes elemzéséhez.

## <a name="next-steps"></a>További lépések
* Használjon [naplófájl-keresési](log-analytics-log-searches.md) a Configuration Manager adatokkal kapcsolatos részletes információk megtekintéséhez.
