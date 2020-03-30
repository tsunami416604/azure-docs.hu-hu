---
title: Az Azure-szolgáltatások és -alkalmazások figyelése a Grafana használatával
description: Irányítsa az Azure Monitor és az Application Insights-adatokat, hogy megtekinthesse őket a Grafanában.
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/06/2017
ms.openlocfilehash: 142e3e19c13710963d239a75bc237b63713c29cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672208"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Az Azure-szolgáltatások figyelése a Grafanában
Most már figyelheti az Azure-szolgáltatásokat és -alkalmazásokat a [Grafana-ból](https://grafana.com/) az [Azure Monitor adatforrásbeépülő](https://grafana.com/plugins/grafana-azure-monitor-datasource)moduljával. A bővítmény összegyűjti az Azure Monitor által gyűjtött alkalmazásteljesítmény-adatokat, beleértve a különböző naplókat és mutatókat. Ezeket az adatokat ezután megjelenítheti a Grafana irányítópultján.

Az alábbi lépésekkel állítsa be a Grafana-kiszolgálót, és hozzon létre irányítópultokat az Azure Monitor metrikáihoz és naplóihoz.

## <a name="set-up-a-grafana-server"></a>Grafana-kiszolgáló beállítása

### <a name="set-up-grafana-locally"></a>A Grafana helyi beállítása
Helyi Grafana-kiszolgáló beállításához [töltse le és telepítse a Grafana-t a helyi környezetben.](https://grafana.com/grafana/download) A bővítmény Azure Monitor-integrációjának használatához telepítse a Grafana 5.3-as vagy újabb verzióját.

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>A Grafana beállítása az Azure-on keresztül az Azure Piactéren
1. Nyissa meg az Azure Piacteret, és válassza ki a Grafana Labs Grafana-ját.

2. Töltse ki a neveket és részleteket. Hozzon létre egy új erőforráscsoportot. Kövesse nyomon a virtuális gép felhasználónevéhez, a virtuális gép jelszavához és a Grafana kiszolgáló felügyeleti jelszavához választott értékeket.  

3. Válassza ki a virtuális gép méretét és a tárfiókot.

4. Adja meg a hálózati konfigurációs beállításokat.

5. Tekintse meg az összegzést, és válassza a **Létrehozás** lehetőséget a használati feltételek elfogadása után.

6. A telepítés befejezése után válassza az **Ugrás az erőforráscsoportra**lehetőséget. Megjelenik az újonnan létrehozott erőforrások listája.

    ![Grafana erőforráscsoport-objektumok](media/grafana-plugin/grafana1.png)

    Ha a hálózati biztonsági csoportot (ebben az esetben*grafana-nsg) választja,* láthatja, hogy a Grafana kiszolgáló eléréséhez a 3000-es port ot használja.

7. Szerezd meg a Grafana-kiszolgáló nyilvános IP-címét – lépjen vissza az erőforrások listájára, és válassza a **Nyilvános IP-cím**lehetőséget.

## <a name="sign-in-to-grafana"></a>Bejelentkezés a Grafanába

1. A kiszolgáló IP-címét használva nyissa meg a Bejelentkezési lapot *http://\<IP-címen\>:3000* vagy a * \<DNSName>\:3000* címen a böngészőben. Bár a 3000 az alapértelmezett port, előfordulhat, hogy a telepítés során másik portot választott. Meg kell jelennie egy bejelentkezési oldal a Grafana szerver épített.

    ![Grafana bejelentkezési képernyő](./media/grafana-plugin/grafana-login-screen.png)

2. Jelentkezzen be a *felhasználónév-rendszergazdával* és a korábban létrehozott Grafana-kiszolgáló rendszergazdai jelszavával. Ha helyi beállítást használ, az alapértelmezett jelszó az *adminisztrátor*lesz, és az első bejelentkezéskor meg kell változtatnia.

## <a name="configure-data-source-plugin"></a>Adatforrás-bővítmény konfigurálása

Miután sikeresen bejelentkezett, látnia kell, hogy az Azure Monitor adatforrás beépülő modul már szerepel.

![A Grafana tartalmazza az Azure Monitor beépülő modulját](./media/grafana-plugin/grafana-includes-azure-monitor-plugin-dark.png)

1. Válassza **az Adatforrás hozzáadása** lehetőséget az Azure Monitor adatforrásának hozzáadásához és konfigurálásához.

2. Válasszon nevet az adatforrás, és válassza az **Azure Monitor** típusként a legördülő menüből.

3. Egyszerű szolgáltatás létrehozása – A Grafana egy Azure Active Directory-szolgáltatásnév használatával csatlakozik az Azure Monitor API-khoz, és adatokat gyűjt. Az Azure-erőforrásokhoz való hozzáférés kezeléséhez létre kell hoznia, vagy egy meglévő egyszerű szolgáltatást kell használnia.
    * Tekintse meg [ezeket](../../azure-resource-manager/resource-group-create-service-principal-portal.md) az utasításokat egy egyszerű szolgáltatás létrehozásához. Másolja és mentse a bérlői azonosítót (címtárazonosító), az ügyfélazonosítót (alkalmazásazonosító) és az ügyféltitkos kulcsot (Alkalmazáskulcs-érték).
    * Lásd: [Alkalmazás hozzárendelése szerepkörhöz](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) az Olvasó szerepkör hozzárendeléséhez az Azure Active Directory-alkalmazáshoz a figyelni kívánt előfizetésen, erőforráscsoporton vagy erőforráson. 
    A Log Analytics API-hoz a [Log Analytics-olvasó szerepkör](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader)szükséges, amely tartalmazza az Olvasó szerepkör engedélyeit, és hozzáadja azt.

4. Adja meg a kapcsolat részleteit a használni kívánt API-khoz. Az összeshez vagy néhányhoz csatlakozhat. 
    * Ha az Azure Monitorban is csatlakozik a metrikákhoz és a naplókhoz, újra felhasználhatja ugyanazokat a hitelesítő adatokat, ha ugyanazokat a részleteket választja, **mint az Azure Monitor API.**
    * A bővítmény konfigurálásakor jelezheti, hogy melyik Azure Cloud-ot szeretné figyelni (Nyilvános, Azure US Government, Azure Germany vagy Azure China).
    * Ha az Application Insights, is szerepelhet az Application Insights API-t és az alkalmazásazonosító t az Application Insights-alapú metrikák gyűjtéséhez. További információ: [Az API-kulcs és az alkalmazásazonosító beszerzése.](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)

        > [!NOTE]
        > Egyes adatforrásmezők neve eltér a korrelált Azure-beállításoktól:
        > * A bérlőazonosító az Azure Directory-azonosító
        > * Az ügyfélazonosító az Azure Active Directory alkalmazásazonosítója
        > * Az ügyféltitok az Azure Active Directory alkalmazáskulcs értéke

5. Ha az Application Insights, is szerepelhet az Application Insights API-t és az alkalmazásazonosító t az Application Insights-alapú metrikák gyűjtéséhez. További információ: [Az API-kulcs és az alkalmazásazonosító beszerzése.](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)

6. Válassza a **Mentés**lehetőséget, és a Grafana teszteli az egyes API-k hitelesítő adatait. A következőhöz hasonló üzenetet kell látnia.  
    ![Grafana adatforrás konfiguráció javunkra jóváhagyva](./media/grafana-plugin/grafana-data-source-config-approved-dark.png)

## <a name="build-a-grafana-dashboard"></a>Grafana irányítópult létrehozása

1. Nyissa meg a Grafana kezdőlapját, és válassza az **Új irányítópult lehetőséget.**

2. Az új irányítópulton válassza a **Grafikon lehetőséget.** Megpróbálhat más diagramkészítési beállításokat is, de ez a cikk példaként a *Graph* ot használja.

3. Egy üres grafikon jelenik meg az irányítópulton. Kattintson a panel címére, és válassza a **Szerkesztés** lehetőséget a diagramdiagramon ábrázolni kívánt adatok részleteinek megadásához.
    ![Grafana új grafikon](./media/grafana-plugin/grafana-new-graph-dark.png)

4. Válassza ki a konfigurált Azure Monitor adatforrást.
   * Az Azure Monitor-metrikák gyűjtése – válassza az **Azure Monitor** a szolgáltatás legördülő menüben. Megjelenik a választók listája, ahol kiválaszthatja a diagramon figyelni kívánt erőforrásokat és mérőszámokat. Metrikák gyűjtéséhez egy virtuális gép, használja a névtér **Microsoft.Compute/VirtualMachines**. Miután kiválasztotta a virtuális gépeket és a mérőszámokat, megkezdheti az adatok megtekintését az irányítópulton.
     ![Grafana graph config for Azure Monitor](./media/grafana-plugin/grafana-graph-config-for-azure-monitor-dark.png)
   * Az Azure Monitor naplóadatainak gyűjtése – válassza az **Azure Log Analytics** lehetőséget a szolgáltatás legördülő menüben. Jelölje ki a lekérdezni kívánt munkaterületet, és állítsa be a lekérdezés szövegét. Ide másolhat minden olyan naplólekérdezést, amely már rendelkezik, vagy létrehozhat egy újat. A lekérdezés beírásakor az IntelliSense megjelenik, és automatikus kiegészítési beállításokat javasol. Válassza ki a vizualizáció típusát, **az Idősorozat-táblát** **Table**, és futtassa a lekérdezést.
    
     > [!NOTE]
     >
     > A bővítményhez mellékelt alapértelmezett lekérdezés két makrót használ: "$__timeFilter() és $__interval. 
     > Ezek a makrók lehetővé teszik a Grafana számára, hogy dinamikusan kiszámítsa az időtartományt és az idősécét, amikor ráközelít a diagram egy részére. Eltávolíthatja ezeket a makrókat, és használhat egy szabványos időszűrőt, például *a TimeGenerated > ezelőtt(1h)*( de ez azt jelenti, hogy a grafikon nem támogatja a nagyítási funkciót.
    
     ![Grafana graph config az Azure Log Analytics szolgáltatáshoz](./media/grafana-plugin/grafana-graph-config-for-azure-log-analytics-dark.png)

5. A következő egy egyszerű irányítópult két diagrammal. A bal oldali két virtuális gép processzorszázalékos százalékát mutatja. A jobb oldali diagram az Azure Storage-fiók tranzakcióit jeleníti meg a Transaction API típusa szerinti bontásban.
    ![Grafana két grafikon példa](media/grafana-plugin/grafana6.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Nem kötelező: Az egyéni metrikák figyelése ugyanabban a Grafana-kiszolgálón

A Telegraf és az InfluxDB telepítése egyéni és ügynökalapú metrikák összegyűjtésére és ábrázolására is telepíthető. Számos adatforrás-beépülő modul létezik, amelyekkel összehozhatja ezeket a mutatókat egy irányítópulton.

Ezt a beállítást a Prometheus kiszolgálóról származó mérőszámok felvételéhez is felhasználhatja. Használja a Prometheus adatforrás-bővítményt a Grafana plugin galériájában.

Az alábbiakban a Telegraf, az InfluxDB, a Prometheus és a Docker használatáról szóló, jó referenciacikkeket olvashat.
 - [A rendszermérőszámok figyelése az Ubuntu 16.04 TICK veremével](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Figyelési megoldás a Docker-gazdagépekhez, tárolókhoz és konténeres szolgáltatásokhoz](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Az alábbiakban egy teljes Grafana-irányítópult ot, amely az Azure Monitor és az Application Insights metrikákat tartalmaz.
![Grafana példa mérőszámok](media/grafana-plugin/grafana8.png)

## <a name="advanced-grafana-features"></a>Fejlett Grafana funkciók

### <a name="variables"></a>Változók
Egyes lekérdezési értékek a felhasználói felület legördülő menüiben választhatók ki, és a lekérdezésben frissíthetők. Vegyük példaként a következő lekérdezést:
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

Konfigurálhat egy változót, amely felsorolja az összes elérhető **Megoldás** értéket, majd frissíti a lekérdezést annak használatára.
Új változó létrehozásához kattintson az irányítópult Beállítások gombjára a jobb felső sarokban, válassza a **Változók**, majd az **Új**lehetőséget.
A változó lapon adja meg a futtatni hozandó adatforrást és lekérdezést az értékek listájának lekérése érdekében.
![Grafana konfigurálása változó](./media/grafana-plugin/grafana-configure-variable-dark.png)

A létrehozás után állítsa be a lekérdezést úgy, hogy a kijelölt érték(ek)et használja, és a diagramok ennek megfelelően válaszolnak:
```
Usage 
| where $__timeFilter(TimeGenerated) and Solution in ($Solutions)
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```
    
![Grafana használja változók](./media/grafana-plugin/grafana-use-variables-dark.png)

### <a name="create-dashboard-playlists"></a>Irányítópult-listák létrehozása

A Grafana számos hasznos funkciójának egyike a műszerfal lejátszási listája. Több irányítópultot is létrehozhat, és hozzáadhatja őket egy lejátszási listához, amely minden egyes irányítópult megjelenítéséhez időközt konfigurál. Válassza a **Lejátszás** lehetőséget az irányítópultok váltásának megtekintéséhez. Előfordulhat, hogy egy nagy fali monitoron szeretné megjeleníteni őket, hogy állapottáblát biztosítson a csoport számára.

![Példa grafana lejátszási listára](./media/grafana-plugin/grafana7.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha beállított egy Grafana-környezetet az Azure-ban, akkor a virtuális gépek futtatásakor díjat számítunk fel, függetlenül attól, hogy használja-e őket vagy sem. További költségek elkerülése érdekében törölje a cikkben létrehozott erőforráscsoportot.

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok,** majd a **Grafana parancsra.**
2. Az erőforráscsoport lapján kattintson a **Törlés**gombra, írja be a **Grafana** parancsot a szövegmezőbe, majd kattintson a **Törlés gombra.**

## <a name="next-steps"></a>További lépések
* [Az Azure Monitor metrikák áttekintése](data-platform.md)

