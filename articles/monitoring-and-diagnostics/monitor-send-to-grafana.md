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
ms.component: ''
ms.openlocfilehash: 75b1edf80f1dad5f0db48c11329effe080760820
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50413145"
---
# <a name="monitor-your-azure-services-in-grafana"></a>A Grafana az Azure-szolgáltatások figyelése
Most is figyelemmel kísérheti az Azure szolgáltatásainak és alkalmazásainak [Grafana](https://grafana.com/) használatával a [Azure Monitor adatforrás-beépülő modul](https://grafana.com/plugins/grafana-azure-monitor-datasource). A beépülő modul az Application Insights SDK által gyűjtött alkalmazásteljesítmény-adatokat, valamint az Azure Monitor által szolgáltatott infrastruktúra adatokat gyűjt. Ezután a Grafana irányítópulton jeleníti meg ezeket az adatokat.

A beépülő modul jelenleg előzetes verzióban érhető el.

A következő lépések segítségével állítja be a Grafana kiszolgálót az Azure Marketplace-ről és a metrikák irányítópultok létrehozása az Azure Monitor és az Application Insights.

## <a name="set-up-a-grafana-instance"></a>A Grafana példány beállítása
1. Ugrás az Azure Marketplace-en, és válassza ki a Grafana Grafana Labs által.

2. Adja meg a neveket és a részleteket. Hozzon létre egy új erőforráscsoportot. Nyomon követheti, az értékek úgy dönt, hogy a virtuális gép felhasználónevét, a virtuális gép jelszava és a Grafana kiszolgáló-rendszergazdai jelszót.  

3. Válassza ki a virtuális gép mérete és a egy storage-fiókot.

4. Adja meg a hálózati konfigurációs beállításokat.

5. Az összegzés megtekintéséhez és kijelöléséhez **létrehozás** után elfogadja a használati feltételeket.

## <a name="log-in-to-grafana"></a>Jelentkezzen be a Grafana számára
1. Az üzembe helyezés befejezése után jelölje be az **erőforráscsoport megnyitása**. Az újonnan létrehozott erőforrások listáját láthatja.

    ![Grafana erőforrás csoportobjektumokhoz](media/monitor-send-to-grafana/grafana1.png)

    Ha a hálózati biztonsági csoport (*grafana-nsg* ebben az esetben), láthatja, hogy port 3000 szolgál a Grafana kiszolgálóhoz való hozzáféréshez.

2. Lépjen vissza az erőforrásokat, és válassza ki a lista **nyilvános IP-cím**. Írja be az ezen a képernyőn található értékeket használja, *http://<IP address>: 3000* vagy a  *<DNSName>: 3000* a böngészőben. A bejelentkezési lapot a Grafana kiszolgáló csak beépített kell megjelennie.

    ![Grafana bejelentkezési képernyő](media/monitor-send-to-grafana/grafana2.png)

3. Jelentkezzen be a felhasználói nevet a következőként *rendszergazdai* és a korábban létrehozott Grafana kiszolgáló-rendszergazdai jelszót.

## <a name="configure-data-source-plugin"></a>Adatforrás beépülő modul konfigurálása

Miután sikeresen bejelentkezett, láthatja, hogy az Azure Monitor adatforrás beépülő modul már megtalálható.

![Grafana jeleníti meg az Azure Monitor beépülő modul](media/monitor-send-to-grafana/grafana3.png)

1. Válassza ki **adatforrás hozzáadása** konfigurálása az Azure Monitor és az Application Insights.

2. Adjon meg egy nevet az adatforráshoz, és válassza ki **Azure Monitor** az adatforrással, a legördülő listából.


## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Grafana egy Azure Active Directory-szolgáltatásnevet használatával csatlakozhat az Azure Monitor API-k és metrikák adatokat gyűjteni. Meg kell egy egyszerű szolgáltatás létrehozása az Azure-erőforrásokhoz való hozzáférés kezelése.

1. Lásd: [ezek az utasítások](../active-directory/develop/howto-create-service-principal-portal.md) egy szolgáltatásnév létrehozásához. Másolja és mentse a bérlő azonosítója, az ügyfél-Azonosítót és a egy ügyfélkulcsot.

2. Lásd: [alkalmazás hozzárendelése szerepkörhöz](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#assign-application-to-role) az Olvasó szerepkör hozzárendelése az Azure Active Directory-alkalmazás.     

3. Ha használja az Application Insights, az Application Insights API és az Alkalmazásazonosítót kíván gyűjteni az Application Insights-alapú is megadhatja. További információkért lásd: [az API-kulcs és az Alkalmazásazonosító beolvasása](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

4. Miután megadta az összes ezeket az adatokat, válassza ki a **mentése** és a Grafana teszteli, az API-t. Egy a következőhöz hasonló üzenetnek kell megjelennie.  

    ![Grafana jeleníti meg az Azure Monitor beépülő modul](media/monitor-send-to-grafana/grafana4-1.png)

> [!NOTE]
> A beépülő modul adhatja meg, melyik Azure-felhő (nyilvános, az Azure US Government, Azure Germany vagy Azure China) szeretné, hogy a beépülő modul ellen kell konfigurálni.
>
>

## <a name="build-a-grafana-dashboard"></a>A Grafana irányítópult létrehozása

1. Lépjen a kezdőlapra, és válassza **új irányítópult**.

2. Az új irányítópultot, válassza ki a **Graph**. Próbálja meg más diagramkészítési lehetőségek, de ez a cikk *Graph* példaként.

    ![Új lesz a Grafana irányítópultja](media/monitor-send-to-grafana/grafana5.png)

3. Az irányítópulton megjelenik egy üres diagram.

4. Kattintson a panel címe, és válassza ki **szerkesztése** , adja meg, hogy az adatok a graph-diagram a megjeleníteni kívánt.

5. Miután kiválasztotta a megfelelő virtuális gépek, elkezdheti a metrikák megtekintése az irányítópulton.

Következő egy könnyen áttekinthető irányítópult használatával két diagram. A bal oldali mutatja, a két virtuális gépet a Processzorhasználat (%). A diagram a jobb oldalon a tranzakciók az Azure Storage-fiókot, a tranzakciós API-típus szerinti bontásban jeleníti meg.

![Grafana két diagramok példa](media/monitor-send-to-grafana/grafana6.png)


## <a name="optional-create-dashboard-playlists"></a>Választható lehetőség: Irányítópult listák létrehozása

A Grafana számos hasznos funkcióját egyik, az irányítópult-lista. Több irányítópultokat hozhat létre, és hozzáadhatja őket a lista konfigurálása mindegyik irányítópult megjelenítéséhez időköze. Válassza ki **lejátszása** megtekintéséhez az irányítópultok közötti váltáshoz. Előfordulhat, hogy szeretné, adja meg a csoport egy "állapot"tábla nagy üzenőfal figyelő megjeleníti azokat.

![Példa a Grafana lejátszási lista](media/monitor-send-to-grafana/grafana7.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Választható lehetőség: Nyomon követheti a egyéni metrikákat Grafana ugyanazon a kiszolgálón

Telegraf és InfluxDB összegyűjtésére és jeleníti meg a Grafana példányt is egyéni és az ügynök-alapú mérőszámok is telepítheti. Nincsenek sok adat forrása beépülő modulok, amelyekkel használhatja ezeket a metrikákat egy irányítópulton egyesítheti.

Ez a Prometheus kiszolgálóról metrikákkal egészítheti ki beállítása is felhasználhatja. Használja a Prometheus adatforrás beépülő modul a Grafana a beépülő modul gyűjteménye.

Az alábbiakban útmutatással cikkek Telegraf, InfluxDB, Prometheus és a Docker használatával
 - [Az OSZTÁSJELEK Stack ubuntu 16.04 rendszer metrikák figyelése](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [A Grafana, InfluxDB és Telegraf Docker erőforrás-metrikák figyelése](https://blog.vpetkov.net/2016/08/04/monitor-docker-resource-metrics-with-grafana-influxdb-and-telegraf/)

 - [Docker-gazdagépek, a tárolók és a tárolóalapú szolgáltatásokat figyelési megoldást](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Íme egy teljes lesz a Grafana irányítópultja, amely rendelkezik az Azure Monitor és az Application Insights metrikáit képe.
![Grafana például metrikák](media/monitor-send-to-grafana/grafana8.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor a virtuális gépek futnak, akár használja őket, vagy nem számítunk fel. Felmerülő további költségek elkerülése érdekében, hogy törölni ebben a cikkben létrehozott erőforráscsoportot.

1. Az Azure Portal baloldali menüjében kattintson **erőforráscsoportok** majd **Grafana**.
2. Az erőforráscsoport oldalán kattintson **törlése**, típus **Grafana** a szövegmezőbe, és kattintson a **törlése**.

## <a name="next-steps"></a>További lépések
* [Az Azure Monitor-metrikák áttekintése](monitoring-overview-metrics.md)
