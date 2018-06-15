---
title: Szolgáltatástérkép integráció a System Center Operations Manager |} Microsoft Docs
description: A Service Map az Azure egyik megoldása, amely automatikusan felderíti az alkalmazás-összetevőket Windows és Linux rendszereken, és feltérképezi a szolgáltatások közötti kommunikációt. Ez a cikk ismerteti, amelyek automatikusan létrehozása az Operations Manager elosztott alkalmazás diagramok a Szolgáltatástérkép használatával.
services: monitoring
documentationcenter: ''
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: bwren;dairwin
ms.openlocfilehash: 6fbc49584b040f952fdff147207864d2d1f6377e
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887873"
---
# <a name="service-map-integration-with-system-center-operations-manager"></a>Szolgáltatástérkép integráció a System Center Operations Manager
  > [!NOTE]
  > A funkció jelenleg nyilvános előzetes verziójához.
  > 
  
A Szolgáltatástérkép automatikusan felderíti az alkalmazás-összetevőket Windows és Linux rendszereken, és feltérképezi a szolgáltatások közötti kommunikációt. Szolgáltatástérkép lehetővé teszi a kiszolgálók úgy gondolja, hogy azok összekapcsolt rendszerekhez, hogy a kritikus szolgáltatások módja. Szolgáltatástérkép kiszolgálók, folyamatok és portok közötti kapcsolatok között bármely TCP-csatlakozóval csatlakoztatott architektúra, az ügynök telepítése mellett szükség konfigurálásra való jeleníti meg. További információkért lásd: a [Szolgáltatástérkép dokumentáció]( monitoring-service-map.md).

Ez az integráció Szolgáltatástérkép és a System Center Operations Manager között automatikusan az Operations Manager programban, a dinamikus függőségi maps a Szolgáltatástérkép alapuló hozhat létre elosztott alkalmazás diagramokat.

## <a name="prerequisites"></a>Előfeltételek
* Az Operations Manager felügyeleti csoport (2012 R2 vagy újabb), amely kezeli a kiszolgálók egy csoportja.
* A Service Map megoldás engedélyezve van a Naplóelemzési munkaterület.
* Kiszolgálók egy csoportja (legalább egyet), amely az Operations Manager és a Service Map adatokat küldő felügyeli. A Windows és Linux-kiszolgálókon támogatott.
* Egy egyszerű szolgáltatást az Azure-előfizetéshez társított a Naplóelemzési munkaterület hozzáférést. További információkért látogasson el [szolgáltatásnevet létrehozni](#creating-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>A Service Map felügyeleti csomag telepítéséhez
A Microsoft.SystemCenter.ServiceMap (Microsoft.SystemCenter.ServiceMap.mpb) felügyeleticsomag-nyaláb importálása az Operations Manager és a Service Map integrációjával engedélyezése. A felügyeleticsomag-nyaláb letöltheti a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=55763). A csomagot a következő felügyeleti csomagokat tartalmazza:
* A Microsoft szolgáltatás térkép alkalmazás nézetek
* A Microsoft System Center Service Map belső
* A Microsoft System Center Service Map felülbírálások
* A Microsoft System Center Service Map

## <a name="configure-the-service-map-integration"></a>A Service Map-integráció konfigurálása
Miután telepítette a Service Map felügyeleti csomagot, egy új csomópont **Szolgáltatástérkép**, jelenik meg **Operations Management Suite** a a **felügyeleti** ablaktáblán. 

Szolgáltatástérkép integráció konfigurálásához tegye a következőket:

1. A konfiguráció varázsló megnyitásához a **szolgáltatás – áttekintés** ablaktáblán kattintson a **munkaterület hozzáadása**.  

    ![Szolgáltatás – áttekintés ablaktáblán](media/monitoring-service-map/scom-configuration.png)

2. Az a **kapcsolat konfigurációs** ablak, írja be a bérlő neve vagy azonosítója, azonosítója (más néven a felhasználónév vagy a clientID) és jelszavát, amely a szolgáltatás egyszerű, és kattintson **következő**. További információkért látogasson el [szolgáltatásnevet létrehozni](#creating-a-service-principal).

    ![A kapcsolat konfigurációja ablak](media/monitoring-service-map/scom-config-spn.png)

3. Az a **előfizetés kijelölés** ablakban válassza ki az Azure-előfizetés, a Azure erőforráscsoport (azt, amelyik tartalmazza a Naplóelemzési munkaterület) és a Naplóelemzési munkaterület, és kattintson **következő**.

    ![Az Operations Manager konfigurációs munkaterület](media/monitoring-service-map/scom-config-workspace.png)

4. Az a **gép Csoportkijelölés** ablak, akkor válassza ki a szolgáltatás térkép gép szeretné szinkronizálni az Operations Manager alkalmazáshoz. Kattintson a **hozzáadása gép csoportok**, csoportok listájából válassza **elérhető számítógép-csoportok**, és kattintson a **hozzáadása**.  Ha befejezte a csoportok kiválasztásával, kattintson a **Ok** befejezéséhez.
    
    ![Az Operations Manager konfigurációs gép csoportok](media/monitoring-service-map/scom-config-machine-groups.png)
    
5. Az a **kiszolgáló kiválasztása** ablak, konfigurálja a térkép kiszolgálók csoport az Operations Manager és a Service Map közötti szinkronizálni kívánt kiszolgálón. Kattintson a **kiszolgálók hozzáadása**.   
    
    Elosztott alkalmazás diagram kiszolgáló felépítése az integráció a kiszolgálónak kell lennie:

    * Operations Manager által felügyelt
    * Szolgáltatástérkép kezeli
    * A térkép kiszolgálók csoportban felsorolt

    ![Az Operations Manager konfigurációs csoport](media/monitoring-service-map/scom-config-group.png)

6. Választható lehetőség: Jelölje ki a felügyeleti kiszolgálókat tartalmazó erőforráskészlet Naplóelemzési kommunikálni, és kattintson **hozzáadása munkaterület**.

    ![Az Operations Manager konfigurációs erőforráskészlet](media/monitoring-service-map/scom-config-pool.png)

    Konfigurálása és regisztrálása a Naplóelemzési munkaterület egy percig is eltarthat. Beállítások konfigurálása után az Operations Manager indít el az első Service Map szinkronizálása.

    ![Az Operations Manager konfigurációs erőforráskészlet](media/monitoring-service-map/scom-config-success.png)


## <a name="monitor-service-map"></a>A figyelő Szolgáltatástérkép
A Naplóelemzési munkaterület csatlakoztatása után egy új mappát, a Service Map, megjelenik a **figyelés** az Operations Manager konzol ablaktáblájában.

![Az Operations Manager figyelés ablaktáblán](media/monitoring-service-map/scom-monitoring.png)

A Service Map mappa négy csomópont rendelkezik:
* **Aktív riasztások**: az Operations Manager és a Service Map közötti kommunikáció kapcsolatos összes aktív riasztás sorolja fel.  Vegye figyelembe, hogy ezek a riasztások nem Naplóelemzési riasztást küld, és az Operations Manager szinkronizálás alatt áll. 

* **Kiszolgálók**: Megjeleníti a figyelt kiszolgálók konfigurált a szinkronizálás a Szolgáltatástérkép.

    ![Az Operations Manager figyelési kiszolgálók ablaktábla](media/monitoring-service-map/scom-monitoring-servers.png)

* **Számítógép-függőség Csoportnézeteket**: a Service Map szinkronizált összes számítógép-csoportok listája. Bármely csoportot és olvassa el az elosztott alkalmazás diagram gombra.

    ![Az Operations Manager elosztott alkalmazás diagramja](media/monitoring-service-map/scom-group-dad.png)

* **Kiszolgáló függőségi nézetek**: minden olyan kiszolgáló, a Service Map szinkronizált sorolja fel. Bármely kiszolgáló, az elosztott alkalmazás diagram megtekintése gombra.

    ![Az Operations Manager elosztott alkalmazás diagramja](media/monitoring-service-map/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Szerkesztheti és törölheti a munkaterület
Ön módosíthatja és törölheti a konfigurált munkaterületen a **szolgáltatás – áttekintés** ablaktáblán (**felügyeleti** ablaktábla > **Operations Management Suite** > **Szolgáltatástérkép**). Csak egy Naplóelemzési munkaterület most konfigurálhatja.

![Az Operations Manager szerkesztése munkaterület ablaktáblán](media/monitoring-service-map/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Szabályok és felülbírálások konfigurálása
A szabály _Microsoft.SystemCenter.ServiceMapImport.Rule_, rendszeres időközönként beolvasása az információt a Service Map jön létre. Szinkronizálási időzítés módosításához felülbírálást a szabály konfigurálható (**szerzői műveletek** ablaktábla > **szabályok** > **Microsoft.SystemCenter.ServiceMapImport.Rule**).

![Az Operations Manager felülbírálások tulajdonságai ablakban](media/monitoring-service-map/scom-overrides.png)

* **Engedélyezett**: engedélyezheti vagy tilthatja le az automatikus frissítések. 
* **IntervalMinutes**: alaphelyzetbe állítani a frissítések között eltelt idő. Az alapértelmezett program óránként. Ha a kiszolgáló maps gyakrabban szinkronizálni kívánt, az érték módosítására képes.
* **TimeoutSeconds**: alaphelyzetbe állítja a mennyi idő után a kérelem időkorlátja lejár. 
* **TimeWindowMinutes**: az adatok lekérdezése időkerete alaphelyzetbe. Alapértelmezett érték 60 perc ablak. Szolgáltatástérkép által engedélyezett maximális értéke 60 perc.

## <a name="known-issues-and-limitations"></a>Ismert problémák és korlátozások

Az aktuális terv mutatja be, a következő problémák és korlátozások:
* Csak egyetlen Naplóelemzési munkaterület csatlakoznak.
* Bár a térkép kiszolgálók csoport manuálisan a kiszolgálókat is hozzáadhat a **szerzői műveletek** ablaktáblán, az adott kiszolgálókon a maps azonnal nem lett szinkronizálva.  Ezek lesznek szinkronizálva a Szolgáltatástérkép során a következő szinkronizálási ciklusban.
* Ha végzett módosításokat a felügyeleti csomag által létrehozott elosztott alkalmazás a diagramokra, ezeket a módosításokat valószínűleg felülírja a Szolgáltatástérkép következő szinkronban.

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása
Hivatalos Azure dokumentációjában az egyszerű szolgáltatás létrehozása lásd:
* [Egy egyszerű szolgáltatás létrehozása a PowerShell használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Hozzon létre egy egyszerű Azure parancssori felület használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Egy egyszerű szolgáltatás létrehozása az Azure-portál használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Visszajelzés
Van olyan visszajelzést az USA Szolgáltatástérkép és a jelen dokumentáció? Látogasson el a [felhasználói véleményekkel foglalkozó weblapunkra](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), ahol felajánlja a szolgáltatások és arra való szavazás a meglévő javaslatok.
