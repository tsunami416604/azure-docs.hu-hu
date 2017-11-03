---
title: "Adatforrások csatlakoztatása a Configuration Manager szolgáltatáshoz |} Microsoft Docs"
description: "Ez a cikk bemutatja a csatlakoztatása a Configuration Manager szolgáltatáshoz, és indítsa el az adatok elemzése lépéseket."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: banders
ms.openlocfilehash: 62d31ed486458245156f7fc832294d662c62991e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="connect-configuration-manager-to-log-analytics"></a>Adatforrások csatlakoztatása a Configuration Manager szolgáltatáshoz
Csatlakozhat a System Center Configuration Manager az OMS szolgáltatáshoz szinkronizálási eszköz gyűjtemény adatait. Így az adatok a Configuration Manager hierarchiából OMS érhető el.

## <a name="prerequisites"></a>Előfeltételek

A Naplóelemzési támogatja a System Center Configuration Manager aktuális ágának, 1606 vagy újabb verziója.  

## <a name="configuration-overview"></a>Konfigurálása – áttekintés
Az alábbi lépéseket foglalja össze a folyamat a Configuration Manager szolgáltatáshoz kapcsolódni.  

1. Az Azure felügyeleti portálon regisztrálja a Configuration Manager egy webalkalmazás és/vagy webes API-alkalmazást, és gondoskodjon arról, hogy az ügyfél-azonosító és a titkos ügyfélkulcsot a regisztráció az Azure Active Directoryból származó. Lásd: [erőforrásokat elérő alkalmazás és szolgáltatás egyszerű létrehozásához az Active Directory használata portal](../azure-resource-manager/resource-group-create-service-principal-portal.md) kapcsolatos részletes információkat ebben a lépésben megvalósításához.
2. Az Azure felügyeleti portálon [adja meg a Configuration Manager (a regisztrált webalkalmazás) OMS hozzáféréssel rendelkező](#provide-configuration-manager-with-permissions-to-oms).
3. A Configuration Managerben [-kapcsolatot az OMS-kapcsolat hozzáadása varázsló használatával](#add-an-oms-connection-to-configuration-manager).
4. A Configuration Managerben [a kapcsolat tulajdonságainak frissítéséhez](#update-oms-connection-properties) Ha a jelszó vagy ügyfél titkos kulcs soha lejár vagy elvész.
5. Az OMS-portálon adataival [töltse le és telepítse a Microsoft Monitoring Agent](#download-and-install-the-agent) futtató számítógépen a Configuration Manager service szolgáltatáskapcsolódási pont helyrendszer-szerepkörrel. Az ügynök küld OMS Configuration Manager-adatokat.
6. A Naplóelemzési [gyűjteményeket importálhat a Configuration Manager](#import-collections) , számítógépcsoportokat.
7. A Naplóelemzési, megtekintheti az adatokat a Configuration Manager alkalmazásból, [számítógépcsoportok](log-analytics-computer-groups.md).

További az OMS-be, a Configuration Manager összekapcsolásával kapcsolatos [szinkronizálni az adatokat a Configuration Manager a Microsoft Operations Management Suite](https://technet.microsoft.com/library/mt757374.aspx).

## <a name="provide-configuration-manager-with-permissions-to-oms"></a>Adja meg a Configuration Manager engedélyekkel az OMS-be
Az alábbi eljárás biztosít az Azure felügyeleti portálon, amelyik megfelelő engedélyekkel rendelkezik az OMS Szolgáltatáshoz. Pontosabban, meg kell adnia a *közreműködői szerepkör* a felhasználók számára az erőforráscsoportot, annak érdekében, hogy az Azure felügyeleti portál csatlakozni a Configuration Manager az OMS Szolgáltatáshoz.

> [!NOTE]
> A Configuration Manager OMS engedélyeket kell megadnia. Ellenkező esetben hibaüzenetet fog kapni, ha a konfigurációs varázsló a Configuration Manager alkalmazásban.
>
>

1. Nyissa meg a [Azure-portálon](https://portal.azure.com/) kattintson **Tallózás** > **Naplóelemzés (OMS)** Naplóelemzés (OMS) panel megnyitásához.  
2. Az a **Naplóelemzés (OMS)** panelen kattintson a **Hozzáadás** megnyitásához a **OMS-munkaterület** panelen.  
   ![OMS panel](./media/log-analytics-sccm/sccm-azure01.png)
3. Az a **OMS-munkaterület** panelen adja meg a következő adatokat, és kattintson a **OK**.

   * **OMS-munkaterület**
   * **Előfizetés**
   * **Erőforráscsoport**
   * **Hely**
   * **Tarifacsomag**  
     ![OMS panel](./media/log-analytics-sccm/sccm-azure02.png)  

     > [!NOTE]
     > A fenti példa létrehoz egy új erőforráscsoportot. Az erőforráscsoport csak segítségével adja meg a Configuration Manager az OMS-munkaterület ebben a példában engedélyt.
     >
     >
4. Kattintson a **Tallózás** > **erőforráscsoportok** megnyitásához a **erőforráscsoportok** panelen.
5. Az a **erőforráscsoportok** panel, az erőforráscsoport létrehozott fenti megnyitásához kattintson a &lt;erőforráscsoport-név&gt; beállítások panelen.  
   ![Erőforráscsoport beállítások panel](./media/log-analytics-sccm/sccm-azure03.png)
6. Az a &lt;erőforráscsoport-név&gt; beállítási paneljén hozzáférés-vezérlés (IAM) megnyitásához kattintson a &lt;erőforráscsoport-név&gt; felhasználók paneljét.  
   ![Erőforráscsoport felhasználók panel](./media/log-analytics-sccm/sccm-azure04.png)  
7. A a &lt;erőforráscsoport-név&gt; felhasználók paneljén kattintson **hozzáadása** megnyitásához a **hozzáférés hozzáadása** panelen.
8. A a **hozzáférés hozzáadása** panelen kattintson **Szerepkörválasztás**, majd válassza ki a **közreműködő** szerepkör.  
   ![szerepkör kiválasztása](./media/log-analytics-sccm/sccm-azure05.png)  
9. Kattintson a **felhasználók hozzáadása az**válassza ki a Configuration Manager felhasználói **válasszon**, és kattintson a **OK**.  
   ![felhasználók hozzáadása](./media/log-analytics-sccm/sccm-azure06.png)  

## <a name="add-an-oms-connection-to-configuration-manager"></a>Az OMS-kapcsolat hozzáadása a Configuration Managerhez
Ahhoz, hogy az OMS-kapcsolat hozzáadása, a Configuration Manager környezetet kell rendelkeznie a [szolgáltatáskapcsolódási pont](https://technet.microsoft.com/library/mt627781.aspx) online módot.

1. Az a **felügyeleti** munkaterület a Configuration Manager, válassza ki **OMS összekötő**. Ekkor megnyílik a **OMS kapcsolat varázsló**. Válassza ki **következő**.
2. Az a **általános** képernyőjén ellenőrizze, hogy elvégezte-e az alábbi műveleteket, és, hogy az egyes elemekről részletek rendelkezik, majd válassza ki, **következő**.

   1. Az Azure felügyeleti portálon, a webalkalmazás és/vagy webes API-alkalmazás néven már regisztrált a Configuration Manager, és hogy rendelkezik a [a regisztrációt az ügyfél-azonosító](../active-directory/active-directory-integrating-applications.md).
   2. Az Azure felügyeleti portálon létrehozott egy alkalmazás titkos kulcs a regisztrált alkalmazás Azure Active Directoryban.  
   3. Az Azure felügyeleti portálon a megadott a regisztrált web app az OMS hozzáférési engedélyt.  
      ![Kapcsolat az OMS varázsló Általános lapja](./media/log-analytics-sccm/sccm-console-general01.png)
3. A a **Azure Active Directory** képernyőn, a kapcsolat konfigurálása az OMS-be azáltal, hogy a **bérlői** , **ügyfél-azonosító** , és **titkos Ügyfélkulcsot** , majd jelölje be **következő**.  
   ![Kapcsolat az OMS varázsló Azure Active Directory lap](./media/log-analytics-sccm/sccm-wizard-tenant-filled03.png)
4. Ha Ön valósítható meg az egyéb eljárások sikeres legyen, majd az adatokat a a **OMS-kapcsolat konfigurációs** képernyő automatikusan meg fog jelenni ezen a lapon. A kapcsolódási beállítások adatait meg kell jelennie a a **Azure-előfizetés** , **Azure erőforráscsoport** , és **Operations Management Suite-munkaterület**.  
   ![Kapcsolat OMS varázsló OMS kapcsolati lapra](./media/log-analytics-sccm/sccm-wizard-configure04.png)
5. A varázsló csatlakozhat az OMS szolgáltatáshoz, a korábban megadott információk segítségével. Válassza ki az OMS szinkronizálni, és kattintson a kívánt eszközgyűjtemények **Hozzáadás**.  
   ![Gyűjtemények kiválasztása](./media/log-analytics-sccm/sccm-wizard-add-collections05.png)
6. Ellenőrizze, hogy a kapcsolat beállításait a **összegzés** képernyőt, majd válassza ki **következő**. A **folyamatban** képernyő kapcsolat állapotát jeleníti meg, majd kell **Complete**.

> [!NOTE]
> Csatlakoztatni kell OMS a legfelső szintű helyet a hierarchiában. Ha az OMS Szolgáltatáshoz kapcsolódni egy önálló elsődleges helyhez, és adja hozzá a központi adminisztrációs hely a környezetében, összekapcsolta, törölje és hozza létre újra az OMS-kapcsolatot az új hierarchiában.
>
>

Miután a Configuration Manager az OMS-be, adja hozzá vagy távolítson el gyűjteményt, és az OMS-kapcsolat tulajdonságainak megtekintéséhez.

## <a name="update-oms-connection-properties"></a>OMS kapcsolat tulajdonságainak frissítéséhez
Ha egy jelszót vagy az ügyfél titkos kulcs soha lejár vagy elvész, akkor frissítenie kell manuálisan az OMS-kapcsolat tulajdonságai.

1. Keresse meg a Configuration Managerben **Felhőszolgáltatások** , majd jelölje be **OMS-összekötő** megnyitásához a **OMS kapcsolat tulajdonságai** lap.
2. Ezen a lapon kattintson a **Azure Active Directory** fülre kattintva megtekintheti a **bérlői**, **ügyfél-azonosító**, **ügyfél titkos kulcs lejárati**. **Győződjön meg arról** a **titkos ügyfélkulcsot** , ha már lejárt.

## <a name="download-and-install-the-agent"></a>Töltse le és telepítse az ügynököt
1. Az OMS-portálon [töltse le az ügynököt telepítő fájl az OMS Szolgáltatáshoz](log-analytics-windows-agents.md#download-the-agent-setup-file-from-oms).
2. A következő módszerek valamelyikével telepíteni és konfigurálni az ügynököt azon a számítógépen, amelyen a Configuration Manager service szolgáltatáskapcsolódási pont helyrendszer-szerepkörrel:
   * [Telepítse az ügynököt, a telepítőprogram használatával](log-analytics-windows-agents.md#install-the-agent-using-setup)
   * [Telepítse az ügynököt, a parancssor használatával](log-analytics-windows-agents.md#install-the-agent-using-the-command-line)
   * [Telepítse az ügynököt az Azure Automation DSC használata](log-analytics-windows-agents.md#install-the-agent-using-dsc-in-azure-automation)

## <a name="import-collections"></a>Gyűjtemények importálása
Az OMS-kapcsolat hozzáadása a Configuration Manager és az ügynök telepítése után azon a számítógépen, amelyen a Configuration Manager szolgáltatáskapcsolódási pont helyrendszerszerepkör, a következő lépés annak gyűjteményeket importálhat a Configuration Manager az OMS, számítógépcsoportokat.

Miután importálását engedélyezve van, a gyűjtemény tagsági információk nélkül 3 óránként naprakészen tartása a gyűjtemény tagságát. Ha szeretné, bármikor importálását letiltása.

1. Az OMS-portálon kattintson **beállítások**.
2. Kattintson a **számítógépcsoportok** lapon, majd kattintson a **SCCM** fülre.
3. Válassza ki **importálása a Configuration Manager gyűjteménytagságok** majd **mentése**.  
   ![Számítógépcsoportok - SCCM lap](./media/log-analytics-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>A Configuration Manager alkalmazásból adatok megtekintése
Miután az OMS-kapcsolat hozzáadása a Configuration Manager és az ügynök telepítve azon a számítógépen, amelyen a Configuration Manager service szolgáltatáskapcsolódási pont helyrendszer-szerepkörrel, az ügynök adatküldést az OMS Szolgáltatáshoz. Az OMS-ben, a Configuration Manager-gyűjtemények jelennek meg [számítógépcsoportok](log-analytics-computer-groups.md). Megtekintheti a csoportok a **Configuration Manager** lapon az **számítógépcsoportok** a **beállítások**.

Miután a gyűjtemények importálása, lásd: gyűjteménytagságok, hogy hány számítógépen észlelt. Megtekintheti az importált gyűjtemények száma is.

![Számítógépcsoportok - SCCM lap](./media/log-analytics-sccm/sccm-computer-groups02.png)

Amikor egy kattint, keresési jelenik meg, amelyen az importált csoportok minden vagy minden csoporthoz tartozó összes számítógép. Használatával [naplófájl-keresési](log-analytics-log-searches.md), elindíthatja a Configuration Manager-adatokat részletes elemzéséhez.

## <a name="next-steps"></a>Következő lépések
* Használjon [naplófájl-keresési](log-analytics-log-searches.md) a Configuration Manager adatokkal kapcsolatos részletes információk megtekintéséhez.
