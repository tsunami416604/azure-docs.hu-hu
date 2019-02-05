---
title: Azure-szolgáltatások és alkalmazások grafanával figyelése
description: Útvonal az Azure Monitor és az Application Insights-adatok így meg tudja őket tekinteni a Grafana.
services: azure-monitor
keywords: ''
author: rboucher
ms.author: robb
ms.date: 11/06/2017
ms.topic: conceptual
ms.service: azure-monitor
ms.subservice: ''
ms.openlocfilehash: fc963987b45751aab33035a83b2b477129e9a756
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55730900"
---
# <a name="monitor-your-azure-services-in-grafana"></a>A Grafana az Azure-szolgáltatások figyelése
Figyelheti az Azure szolgáltatásainak és alkalmazásainak [Grafana](https://grafana.com/) használatával a [Azure Monitor adatforrás-beépülő modul](https://grafana.com/plugins/grafana-azure-monitor-datasource). A beépülő modul az Azure Monitor, beleértve a különböző naplókat és mérőszámokat által gyűjtött alkalmazásteljesítmény-adatokat gyűjt. Ezután a Grafana irányítópulton jeleníti meg ezeket az adatokat.

A beépülő modul jelenleg előzetes verzióban érhető el.

A következő lépések segítségével állítsa be a Grafana kiszolgálót és a metrikák és naplók irányítópultok létrehozása az Azure Monitor.

## <a name="set-up-a-grafana-server"></a>A Grafana kiszolgáló beállítása

### <a name="set-up-grafana-locally"></a>Helyileg állítsa be a Grafana
Egy helyi Grafana kiszolgáló beállításához [töltse le és telepítse a Grafana a helyi környezetben](https://grafana.com/grafana/download). Log Analytics-integráció a beépülő modul használatához telepítse a Grafana 5.3-as vagy újabb verziója.
### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Állítsa be a Grafana az Azure-ban az Azure Marketplace-en
1. Ugrás az Azure Marketplace-en, és válassza ki a Grafana Grafana Labs által.

2. Adja meg a neveket és a részleteket. Hozzon létre egy új erőforráscsoportot. Nyomon követheti, az értékek úgy dönt, hogy a virtuális gép felhasználónevét, a virtuális gép jelszava és a Grafana kiszolgáló-rendszergazdai jelszót.  

3. Válassza ki a virtuális gép mérete és a egy storage-fiókot.

4. Adja meg a hálózati konfigurációs beállításokat.

5. Az összegzés megtekintéséhez és kijelöléséhez **létrehozás** után elfogadja a használati feltételeket.

6. Az üzembe helyezés befejezése után jelölje be az **erőforráscsoport megnyitása**. Az újonnan létrehozott erőforrások listáját láthatja.

    ![Grafana erőforrás csoportobjektumokhoz](media/grafana-plugin/grafana1.png)

    Ha a hálózati biztonsági csoport (*grafana-nsg* ebben az esetben), láthatja, hogy port 3000 szolgál a Grafana kiszolgálóhoz való hozzáféréshez.

7. A Grafana kiszolgáló nyilvános IP-címének lekéréséhez – térjen vissza az erőforrásokat, és válassza ki a lista **nyilvános IP-cím**.

## <a name="log-in-to-grafana"></a>Jelentkezzen be a Grafana számára

1. A kiszolgáló IP-cím használatával, nyissa meg a bejelentkezési lapot a *http://\<IP-cím\>: 3000* vagy a  *\<DNSName >\:3000* a böngészőben. Az alapértelmezett port pedig a 3000 vegye figyelembe, előfordulhat, hogy kiválasztott egy másik portot a telepítés során. A Grafana kiszolgáló létrehozott egy bejelentkezési lapját kell megjelennie.

    ![Grafana bejelentkezési képernyő](./media/grafana-plugin/grafana-login-screen.png)

2. Jelentkezzen be a felhasználónevet *rendszergazdai* és a korábban létrehozott Grafana kiszolgáló-rendszergazdai jelszót. Egy helyi beállítást használja, ha az alapértelmezett jelszó lenne *rendszergazdai*, és szeretné kért változtatnia azt az első bejelentkezésnél.

## <a name="configure-data-source-plugin"></a>Adatforrás beépülő modul konfigurálása

Miután sikeresen bejelentkezett, láthatja, hogy az Azure Monitor adatforrás beépülő modul már megtalálható.

![Grafana az Azure Monitor beépülő modult is tartalmaz.](./media/grafana-plugin/grafana-includes-azure-monitor-plugin-dark.png)

1. Válassza ki **adatforrás hozzáadása** hozzáadása és konfigurálása az Azure Monitor az adatforrás.

2. Adjon meg egy nevet az adatforráshoz, és válassza ki **Azure Monitor** típust a legördülő listából.

3. Egyszerű szolgáltatás létrehozása – a Grafana használ egy Azure Active Directory-szolgáltatásnevet csatlakozhat az Azure Monitor API-k és adatok gyűjtéséhez. Hozzon létre, vagy használjon egy meglévő egyszerű szolgáltatást az Azure-erőforrásokhoz való hozzáférés kezelése.
    * Lásd: [ezek az utasítások](../../azure-resource-manager/resource-group-create-service-principal-portal.md) egy szolgáltatásnév létrehozásához. Másolja és mentse a bérlő azonosítója (címtár-azonosító), az ügyfél-azonosító (Alkalmazásazonosító) és a titkos Ügyfélkód (alkalmazás kulcs-érték).
    * Lásd: [alkalmazás hozzárendelése szerepkörhöz](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) az Olvasó szerepkör hozzárendelése az Azure Active Directory-alkalmazás az előfizetésre, erőforráscsoportra vagy erőforrásra figyelni kívánt. 
    A Log Analytics API megköveteli a [Log Analytics olvasó szerepkör](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader), amely tartalmazza az Olvasó szerepkörhöz az engedélyeket, és hozzáadja azt.

4. Adja meg a kapcsolati adatokat szeretné használni az API-k. Az összes vagy egyes azokat is lehet kapcsolódni. 
    * Ha csatlakozik az Azure Monitor (kíván gyűjteni) és az Azure Log Analytics (a naplózási adatokat), ugyanazokat a hitelesítő adatokat felhasználhatja kiválasztásával **azonos adatokkal az Azure Monitor API-ként**.
    * A beépülő modul konfigurálásakor adhatja meg, melyik Azure-felhő a monitor (nyilvános, az Azure US Government, Azure Germany vagy Azure China) beépülő modult szeretné.
    * Ha használja az Application Insights, az Application Insights API és az Alkalmazásazonosítót kíván gyűjteni az Application Insights-alapú is megadhatja. További információkért lásd: [az API-kulcs és az Alkalmazásazonosító beolvasása](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

        > [!NOTE]
        > Bizonyos adatok forrása mezők neve eltérően a kapcsolódó Azure-beállítások:
        >     * Bérlői azonosító az Azure címtár-azonosító
        >     * Ügyfél-azonosító az Azure Active Directory-Alkalmazásazonosító
        >     * Titkos Ügyfélkód az Azure Active Directory-alkalmazás kulcs értéke

5. Ha használja az Application Insights, az Application Insights API és az Alkalmazásazonosítót kíván gyűjteni az Application Insights-alapú is megadhatja. További információkért lásd: [az API-kulcs és az Alkalmazásazonosító beolvasása](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

6. Válassza ki **mentése**, és a Grafana teszteli a hitelesítő adatokat az egyes API. Egy a következőhöz hasonló üzenetnek kell megjelennie.  
    ![Grafana adatforrás-konfiguráció jóváhagyása](./media/grafana-plugin/grafana-data-source-config-approved-dark.png)

## <a name="build-a-grafana-dashboard"></a>A Grafana irányítópult létrehozása

1. Nyissa meg a Grafana kezdőlapjára, és válassza ki **új irányítópult**.

2. Az új irányítópultot, válassza ki a **Graph**. Próbálja meg más diagramkészítési lehetőségek, de ez a cikk *Graph* példaként.

3. Az irányítópulton megjelenik egy üres diagram. Kattintson a panel címe, és válassza ki **szerkesztése** , adja meg, hogy az adatok a graph-diagram a megjeleníteni kívánt.
    ![Grafana új gráf](./media/grafana-plugin/grafana-new-graph-dark.png)

4. Válassza ki az Azure Monitor adatforrás konfigurálta.
    * Gyűjtése az Azure Monitor - metrikák kiválasztása **Azure Monitor** a szolgáltatás legördülő listában. Választók mutatja be, ahol kiválaszthatja az erőforrások és -metrika figyelése ezen a diagramon listája. Virtuális gép adatainak begyűjtése, használja a névtér **Microsoft.Compute/VirtualMachines**. Miután kiválasztotta a virtuális gépek és a metrikák, indítsa el az adatok megtekintése az irányítópulton.
    ![Az Azure Monitor Grafana graph-konfiguráció](./media/grafana-plugin/grafana-graph-config-for-azure-monitor-dark.png)
    * Gyűjtése az Azure Log Analytics - adatok kiválasztása **Azure Log Analytics** a szolgáltatás legördülő listában. Válassza ki a munkaterületet, szeretné lekérdezni, és állítsa be a lekérdezés szövege. Másolhatja itt bármely Log Analytics-lekérdezés, már van, vagy hozzon létre egy újat. Írja be a lekérdezést, mert IntelliSense jelennek meg, és javasolt az automatikus kiegészítés beállításai. Válassza ki a Vizualizáció típusát a **Time series** **tábla**, és a lekérdezés futtatásához.
    
    > [!NOTE]
    >
    > A beépülő modul használatával a megadott alapértelmezett lekérdezés két makrók használja: "$__timeFilter() és $__interval. 
    > Ezek a makrók engedélyezése a Grafana dinamikusan alapján számítja ki az időtartományt és időfelbontási szint, ha ráközelít a diagram része. Ezek a makrók eltávolíthatja, és használjon például egy téli idő szűrővel *TimeGenerated > ago(1h)*, de ez azt jelenti, hogy a graph lenne támogatja a nagyítási funkció.
    
    ![Az Azure Log Analytics Grafana graph-konfiguráció](./media/grafana-plugin/grafana-graph-config-for-azure-log-analytics-dark.png)

5. Következő egy könnyen áttekinthető irányítópult használatával két diagram. A bal oldali mutatja, a két virtuális gépet a Processzorhasználat (%). A diagram a jobb oldalon a tranzakciók az Azure Storage-fiókot, a tranzakciós API-típus szerinti bontásban jeleníti meg.
    ![Grafana két diagramok példa](media/grafana-plugin/grafana6.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Nem kötelező: Nyomon követheti a egyéni metrikákat Grafana ugyanazon a kiszolgálón

Emellett telepíthet Telegraf és InfluxDB gyűjthet, és egyéni, mind az ügynök-alapú metrikákat jeleníti Grafana ugyanezen példányában. Nincsenek sok adat forrása beépülő modulok, amelyekkel használhatja ezeket a metrikákat egy irányítópulton egyesítheti.

Ez a Prometheus kiszolgálóról metrikákkal egészítheti ki beállítása is felhasználhatja. Használja a Prometheus adatforrás beépülő modul a Grafana a beépülő modul gyűjteménye.

Az alábbiakban útmutatással cikkek Telegraf, InfluxDB, Prometheus és a Docker használatával
 - [Az OSZTÁSJELEK Stack ubuntu 16.04 rendszer metrikák figyelése](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [A Grafana, InfluxDB és Telegraf Docker erőforrás-metrikák figyelése](https://blog.vpetkov.net/2016/08/04/monitor-docker-resource-metrics-with-grafana-influxdb-and-telegraf/)

 - [Docker-gazdagépek, a tárolók és a tárolóalapú szolgáltatásokat figyelési megoldást](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Íme egy teljes lesz a Grafana irányítópultja, amely rendelkezik az Azure Monitor és az Application Insights metrikáit képe.
![Grafana például metrikák](media/grafana-plugin/grafana8.png)

## <a name="advanced-grafana-features"></a>Grafana speciális funkciók

### <a name="variables"></a>Változók
Bizonyos lekérdezési értékeket felhasználói felület legördülő menük segítségével kiválasztott, és frissíti benne a lekérdezést. Példaként, vegye figyelembe a következő lekérdezést:
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

Konfigurálhat egy változó, amely felsorolja az összes rendelkezésre álló **megoldás** értéket, majd frissítse a lekérdezést a használatára.
Hozzon létre egy új változó, kattintson az irányítópult beállítások gomb jobb felső területén, jelölje be **változók**, majd **új**.
A változó lapon adja meg az adatforrás és lekérdezés fut, hogy az értékek listájának beolvasása.
![Grafana változó konfigurálása](./media/grafana-plugin/grafana-configure-variable-dark.png)

Létrehozása után módosíthatja a lekérdezést, hogy a kiválasztott érték(ek) használjon, és ennek megfelelően válaszol a diagramokat:
```
Usage 
| where $__timeFilter(TimeGenerated) and Solution in ($Solutions)
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```
    
![Grafana használata változók](./media/grafana-plugin/grafana-use-variables-dark.png)

### <a name="create-dashboard-playlists"></a>Irányítópult-listák létrehozása

A Grafana számos hasznos funkcióját egyik, az irányítópult-lista. Több irányítópultokat hozhat létre, és hozzáadhatja őket a lista konfigurálása mindegyik irányítópult megjelenítéséhez időköze. Válassza ki **lejátszása** megtekintéséhez az irányítópultok közötti váltáshoz. Érdemes egy nagy üzenőfal figyelő állapota tábla biztosít a csoport megjelenítendő őket.

![Példa a Grafana lejátszási lista](./media/grafana-plugin/grafana7.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a telepítő a Grafana környezet az Azure-ban, amikor a virtuális gépek futnak, akár használja őket, vagy nem számítunk fel. Felmerülő további költségek elkerülése érdekében, hogy törölni ebben a cikkben létrehozott erőforráscsoportot.

1. Az Azure Portal baloldali menüjében kattintson **erőforráscsoportok** majd **Grafana**.
2. Az erőforráscsoport oldalán kattintson **törlése**, típus **Grafana** a szövegmezőbe, és kattintson a **törlése**.

## <a name="next-steps"></a>További lépések
* [Az Azure Monitor-metrikák áttekintése](../../azure-monitor/platform/data-collection.md)

