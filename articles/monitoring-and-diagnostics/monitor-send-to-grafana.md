---
title: "Azure-szolgáltatások és az alkalmazások Grafana figyelése |} Microsoft Docs"
description: "Útvonal Azure figyelése és az Application Insights-adatok így meg lehet tekinteni őket Grafana."
services: monitoring-and-diagnostics
keywords: 
author: rboucher
ms.author: robb
ms.date: 11/06/2017
ms.topic: article
ms.service: monitoring-and-diagnostics
ms.openlocfilehash: c189e67c481239a8a68f2e2b30d05bb615cfa24e
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="monitor-your-azure-services-in-grafana"></a>Grafana az Azure-szolgáltatások figyelése
Most is figyelheti az Azure-szolgáltatások és alkalmazások [Grafana](https://grafana.com/) használatával a [Azure figyelő az adatforrás-beépülő modul](https://grafana.com/plugins/grafana-azure-monitor-datasource). A beépülő modul az Application Insights SDK által gyűjtött teljesítmény alkalmazásadatok, valamint az Azure-figyelő által szolgáltatott infrastruktúra adatokat gyűjt. Ezután az Grafana irányítópulton jeleníti meg ezeket az adatokat.

A beépülő modul jelenleg előzetes verzió.

Az alábbi lépések segítségével állítson be az Azure piactérről Grafana kiszolgálót és irányítópultok metrikáihoz Azure figyelése és az Application Insights felépíteni.

## <a name="set-up-a-grafana-instance"></a>Állítsa be a Grafana példánya
1. Nyissa meg az Azure piactéren, és válassza ki a Grafana Grafana Labs által.

2. Adja meg a nevét és részleteit. Hozzon létre egy új erőforráscsoportot. Nyomon követjük, hogy az értékek úgy dönt, hogy a virtuális gép felhasználónév, a virtuális gép jelszót és a Grafana kiszolgáló rendszergazdai jelszavát.  

3. Válassza ki a Virtuálisgép-méretet és a storage-fiók.

4. Konfigurálja a hálózati konfigurációs beállításait.

5. Az összegzés megtekintéséhez és kijelöléséhez **létrehozása** a használati feltételek elfogadása után.

## <a name="log-in-to-grafana"></a>Jelentkezzen be Grafana
1. A telepítés befejezése után válassza ki a **erőforráscsoport Ugrás**. Az újonnan létrehozott erőforrások listáját láthatja. 

    ![Grafana erőforrás csoportobjektumokhoz](.\media\monitor-how-to-grafana\grafana1.png) 

    Ha a hálózati biztonsági csoport (*grafana-nsg* ebben az esetben), láthatja, hogy port 3000 Grafana server eléréséhez használt. 

2. Lépjen vissza az erőforrások listáján válassza ki a **nyilvános IP-cím**. Ezen a képernyőn található értékekre segítségével írja be *http://<IP address>: 3000* vagy a  *<DNSName>: 3000* a böngészőben. A bejelentkezési oldal az imént létrehozott Grafana kiszolgáló kell megjelennie.
    
    ![Grafana bejelentkezési képernyő](.\media\monitor-how-to-grafana\grafana2.png) 

3. Jelentkezzen be a felhasználó felhasználóneve megegyezik *admin* és a korábban létrehozott Grafana kiszolgáló rendszergazdai jelszavát. 

## <a name="configure-data-source-plugin"></a>Adatforrás beépülő modul konfigurálása

Miután sikeresen bejelentkezett, megtekintheti, hogy az Azure-figyelő adatforrás beépülő modul is meg van adva.

![Grafana látható Azure figyelője beépülő modul](.\media\monitor-how-to-grafana\grafana3.png) 

1. Válassza ki **adatforrás hozzáadása** Azure figyelése és az Application Insights beállítása. 
    
2. Adjon meg egy nevet az adatforrás, és válassza ki **Azure figyelő** az adatforrással, a legördülő listából.
    
    
## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása 

Grafana használja az Azure Active Directory szolgáltatás egyszerű Azure figyelő API-k és metrikákat adatainak gyűjtéséről. Létre kell hoznia egy szolgáltatás egyszerű való hozzáférés az Azure-erőforrások kezelése.

1. Lásd: [ezeket az utasításokat](../azure-resource-manager/resource-group-create-service-principal-portal.md) egy egyszerű szolgáltatásnév létrehozásához. Másolja ki és mentse a bérlő azonosítója, ügyfél-azonosító és a titkos ügyfélkulcsot.

2. Lásd: [szerepkör alkalmazást](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#assign-application-to-role) az olvasó szerepkört rendelni az Azure Active Directory-alkalmazást.     

3. Az Application Insights használatakor is használható az Application Insights API és Alkalmazásazonosító Application Insights-alapú gyűjtéséhez. További információkért lásd: [az API-kulcs és az Alkalmazásazonosító](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

4. Miután megadta az összes ezeket az adatokat, válassza ki **mentése** és Grafana ellenőrzi az API-t. Megtekintheti az alábbihoz hasonló üzenet.  

    ![Grafana látható Azure figyelője beépülő modul](.\media\monitor-how-to-grafana\grafana4.png) 
    
## <a name="build-a-grafana-dashboard"></a>Grafana Irányítópult összeállítása

1. Ugrás a kezdőlapra, és válassza ki **új irányítópult**.

2. Az új irányítópult, válassza ki a **Graph**. Próbálja meg más diagramkészítési beállításokat, de ebben a cikkben az *Graph* példaként. 

    ![Új irányítópult Grafana](.\media\monitor-how-to-grafana\grafana5.png) 

3. Az irányítópulton megjelenik egy üres diagramon. 

4. Kattintson a panel címe, és válassza ki **szerkesztése** számára adja meg a graph-diagram megrajzolásához kívánt adatokat adatait.
    
5. Miután kiválasztotta a megfelelő virtuális gépeket, megkezdheti a metrikákat jeleníti meg az irányítópulton. 

Az alábbiakban látható egy egyszerű irányítópultot, amelynek két diagramot. A bal oldali ezen két virtuális gépek Processzor arányát tartalmazza. A diagram jobb tranzakciói jelennek meg az Azure Storage-fiók a tranzakció API típus szerinti bontásban.
    
![A két Grafana diagramok – példa](.\media\monitor-how-to-grafana\grafana6.png) 
    

## <a name="optional-create-dashboard-playlists"></a>Választható lehetőség: Hozzon létre irányítópult-listák

A számos hasznos funkciónak Grafana az egyik az irányítópult lista. Több irányítópultot létrehozni, és adja hozzá a lista minden irányítópult megjelenítéséhez időköze konfigurálása. Válassza ki **lejátszása** Váltás az irányítópultok megjelenítéséhez. Érdemes lehet egy nagy fali megjelenő "állapot board" adja meg a csoport megjelenítéséhez. 
    
![Grafana lista – példa](.\media\monitor-how-to-grafana\grafana7.png) 


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Választható lehetőség: Nyomon követheti a egyéni metrikákat Grafana kiszolgálón

Az adatgyűjtésre és egyéni mind ügynök-alapú metrikák Grafana ugyanezen példányában megrajzolásához Telegraf és InfluxDB is telepíthet. Nincsenek sok adatok forrás beépülő modulok, amelyek segítségével a metrikák egyesítik az irányítópulton. 
    
Ez a Prometheus kiszolgálóról metrikákkal beállítása is felhasználhatja. A Prometheus adatforrás beépülő modul használata Grafana a beépülő modul gyűjteménye.
    
Az alábbiakban a helyes hivatkozás cikkek Telegraf, InfluxDB, Prometheus és Docker használatával
 - [Az OSZTÁSJELEK verem Ubuntu 16.04 a rendszer metrikák figyelése](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Docker erőforrás metrikát Grafana, InfluxDB és Telegraf](https://blog.vpetkov.net/2016/08/04/monitor-docker-resource-metrics-with-grafana-influxdb-and-telegraf/)

 - [A Docker gazdagépek, a tárolók és a tárolóalapú szolgáltatások figyelési megoldás](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Ez egy teljes Grafana irányítópult, amelyen a metrikák Azure figyelése és az Application Insights képe.
![Grafana példa metrikák](.\media\monitor-how-to-grafana\grafana8.png) 


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a virtuális gépek futnak, függetlenül attól, hogy azokat, vagy nem van szó. További díjakat megcélzásával elkerülheti, törölje az erőforráscsoport létrehozásánál ebben a cikkben. 

1. Az Azure-portálon a bal oldali menüben kattintson a **erőforráscsoportok** majd **Grafana**. 
2. Az erőforrás csoport lapján kattintson a **törlése**, típus **Grafana** a szövegmezőbe, majd **törlése**.

## <a name="next-steps"></a>Következő lépések
* [Az Azure figyelő metrikák áttekintése](monitoring-overview-metrics.md)


