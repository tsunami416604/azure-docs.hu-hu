---
title: Egyéni metrikák az Azure Monitorban
description: Ismerje meg az egyéni metrikák az Azure Monitorban, és hogyan vannak modellezve.
author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 099ab150cde763551c2ad10a4e9159909ccff4dd
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270706"
---
# <a name="custom-metrics-in-azure-monitor"></a>Egyéni metrikák az Azure Monitorban

Amikor erőforrásokat és alkalmazásokat telepít az Azure-ban, érdemes elkezdeni a telemetriai adatok gyűjtését, hogy betekintést nyerjen azok teljesítményébe és állapotába. Az Azure néhány metrikát elérhetővé tesz a dobozból. Ezeket a mutatókat [szabványos nak vagy platformnak](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)nevezzük. Azonban, ők korlátozott jellegű. Érdemes lehet gyűjteni néhány egyéni teljesítménymutatók vagy üzleti-specifikus mutatókat, hogy mélyebb betekintést.
Ezek **az egyéni** metrikák az alkalmazás telemetriai adatok, az Azure-erőforrásokon futó ügynök, vagy akár egy külső figyelési rendszer, és közvetlenül az Azure Monitornak küldött keresztül. Miután közzétették őket az Azure Monitorban, tallózhat, lekérdezhet és riasztást adhat az Azure-erőforrások és -alkalmazások egyéni metrikáiról az Azure által kibocsátott szabványos metrikákkal együtt.

## <a name="methods-to-send-custom-metrics"></a>Egyéni mutatók küldésének módszerei

Az egyéni metrikák több módszerrel is elküldhetők az Azure Monitornak:
- Az Azure Application Insights SDK használatával instrument elsajáthatja az alkalmazást, és egyéni telemetriai adatokat küldhet az Azure Monitornak. 
- Telepítse a Windows Azure Diagnostics (WAD) bővítményt az [Azure Virtuálisgépre,](collect-custom-metrics-guestos-resource-manager-vm.md) [a virtuális gép méretezésére,](collect-custom-metrics-guestos-resource-manager-vmss.md) [a klasszikus virtuális gépre](collect-custom-metrics-guestos-vm-classic.md)vagy a [klasszikus felhőszolgáltatásokra,](collect-custom-metrics-guestos-vm-cloud-service-classic.md) és küldjön teljesítményszámlálókat az Azure Monitornak. 
- Telepítse az [InfluxData Telegraf-ügynököt](collect-custom-metrics-linux-telegraf.md) az Azure Linux os virtuális gépére, és küldje el a metrikákat az Azure Monitor kimeneti beépülő modulhasználatával.
- Egyéni metrikák küldése [közvetlenül az Azure Monitor REST API-](../../azure-monitor/platform/metrics-store-custom-rest-api.md)ba `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`.

## <a name="pricing-model"></a>Díjszabási modell

Az Azure Monitor metrikák áruházába történő szabványos metrikák (platformmetrikák) betöltése nem jár költséggel. Az Azure Monitor metrikák tárolóba betöltött egyéni metrikák at mbyte-enként kell fizetni minden egyes egyéni metrika adatpont írt 8 bájt méretű. Az összes bevitt metrikák 90 napig megmaradnak.

Metrika lekérdezések a standard API-hívások száma alapján kerül felszámolásra. A szabványos API-hívás egy olyan hívás, amely 1440 adatpontot elemez (1440 egyben a metrika/naponta tárolható adatpontok teljes száma is). Ha egy API-hívás több mint 1440 adatpontot elemez, akkor több szabványos API-hívásnak számít. Ha egy API-hívás 1440-nél kevesebb adatpontot elemez, akkor egynél kevesebb API-hívásnak számít. A szabványos API-hívások számát a rendszer minden nap kiszámítja, mivel a naponta elemzett adatpontok teljes száma osztva 1440-re.

Az egyéni metrikák és metrikalekérdezések konkrét árrészletei az [Azure Monitor díjszabási oldalán](https://azure.microsoft.com/pricing/details/monitor/)érhetők el.

> [!NOTE]  
> Az Application Insights SDK-n keresztül az Azure Monitornak küldött metrikák at a rendszer betöltési naplóadatokként számítja fel, és csak akkor számítfel további metrikákat, ha az Application Insights funkció [az egyéni metrikadimenziók riasztásának engedélyezése](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics#custom-metrics-dimensions-and-pre-aggregation) lehetőség van kiválasztva. További információ az [Application Insights díjszabási modelljéről](https://docs.microsoft.com/azure/azure-monitor/app/pricing#pricing-model) és [a régióban lévő árakról.](https://azure.microsoft.com/pricing/details/monitor/)

> [!NOTE]  
> Ellenőrizze az [Azure Monitor díjszabási oldalon](https://azure.microsoft.com/pricing/details/monitor/) a részleteket, amikor a számlázás engedélyezve lesz az egyéni metrikák és metrikák lekérdezések. 

## <a name="how-to-send-custom-metrics"></a>Egyéni mutatók küldése

Amikor egyéni metrikákat küld az Azure Monitornak, minden jelentett adatpontnak vagy értéknek tartalmaznia kell a következő információkat.

### <a name="authentication"></a>Hitelesítés
Egyéni metrikák az Azure Monitor, az entitás, amely beküldi a metrikát szüksége van egy érvényes Azure Active Directory (Azure AD) token a **bemutatóra** fejlécében a kérelem. Az érvényes tulajdonosi jogkivonat beszerzésének több támogatott módja is van:
1. [Felügyelt identitások az Azure-erőforrásokhoz.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) Identitást ad magának az Azure-erőforrásnak, például egy virtuális gépnek. A felügyelt szolgáltatásidentitás (MSI) célja, hogy bizonyos műveletek elvégzéséhez engedélyeket adjon az erőforrásoknak. Egy példa, amely lehetővé teszi, hogy egy erőforrás metrikákat bocsátjon ki magáról. Egy erőforrás, vagy annak MSI, kaphat **figyelési metrikák közzétevőengedélyeket** egy másik erőforrás. Ezzel az engedéllyel az MSI más erőforrásokhoz is képes metrikákat kibocsátani.
2. [Az Azure AD szolgáltatásának egyszerű szolgáltatása](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals). Ebben a forgatókönyvben egy Azure AD-alkalmazás vagy szolgáltatás, lehet hozzárendelni engedélyeket egy Azure-erőforrás metrikák.
A kérelem hitelesítéséhez az Azure Monitor érvényesíti az alkalmazás jogkivonatát az Azure AD nyilvános kulcsok használatával. A meglévő **figyelési metrikák közzétevői** szerepkör már rendelkezik ezzel az engedéllyel. Az Azure Portalon érhető el. Az egyszerű szolgáltatás, attól függően, hogy milyen erőforrásokat bocsát ki egyéni metrikák, meg adható a **monitoring metrikák közzétevői** szerepkör a szükséges hatókörön. Ilyenek például egy előfizetés, erőforráscsoport vagy adott erőforrás.

> [!TIP]  
> Amikor egy Azure AD-jogkivonatot kér az egyéni metrikák kitöltéséhez, győződjön meg arról, hogy a jogkivonat által kért közönség vagy erőforrás a. `https://monitoring.azure.com/` Ügyeljen arra, hogy tartalmazza a záró "/".

### <a name="subject"></a>Tárgy
Ez a tulajdonság rögzíti, hogy melyik Azure-erőforrás-azonosító az egyéni metrika jelentett. Ez az információ a folyamatban lévő API-hívás URL-címében lesz kódolva. Minden API csak egyetlen Azure-erőforrás metrikaértékeit küldheti el.

> [!NOTE]  
> Egyéni metrikákat nem bocsáthat ki egy erőforráscsoport vagy előfizetés erőforrás-azonosítójára.


### <a name="region"></a>Régió
Ez a tulajdonság rögzíti, hogy milyen Azure-régióban az erőforrás, amelynek metrikákat bocsát ki, üzembe helyezése. Metrikákat kell kibocsátani az Azure Monitor regionális végpont, mint a régió, amelyben az erőforrás üzembe helyezett. Például egy USA nyugati részén üzembe helyezett virtuális gép egyéni metrikáit kell küldeni a WestUS regionális Azure Monitor végpontra. A régióadatok is az API-hívás URL-címében vannak kódolva.

> [!NOTE]  
> A nyilvános előzetes verzió során az egyéni metrikák csak az Azure-régiók egy részhalmazában érhetők el. A támogatott régiók listáját a cikk egy későbbi szakasza dokumentálja.
>
>

### <a name="timestamp"></a>Időbélyeg
Az Azure Monitornak küldött minden egyes adatpontot időbélyeggel kell megjelölni. Ez az időbélyeg azt a DateTime-ot rögzíti, amelyen a metrikaértékét mérik vagy gyűjtik. Az Azure Monitor a múltban 20 perc, a jövőben pedig 5 perc időbélyeggel fogad el metrikaadatokat. Az időbélyegnek ISO 8601 formátumúnak kell lennie.

### <a name="namespace"></a>Névtér
A névterek a hasonló metrikák kategorizálásának vagy csoportosításának egyik módja. A névterek használatával elérheti a metrikák csoportjai közötti elkülönítést, amely különböző elemzési adatokat vagy teljesítménymutatókat gyűjthet. Előfordulhat például, hogy rendelkezik egy **contosomemorymetrics** nevű névtérrel, amely nyomon követi az alkalmazást profillal profillal tartalmazó memóriahasználati mutatókat. Egy másik névtér nevű **contosoapptransaction** nyomon követheti az alkalmazás felhasználói tranzakcióival kapcsolatos összes metrikát.

### <a name="name"></a>Név
**A jelentésalatt** a jelentési mutató neve. Általában a név elég leíró ahhoz, hogy segítsen azonosítani, hogy mit mértek. Egy példa egy metrika, amely méri az adott virtuális gépen használt memóriabájtok számát. Lehet, hogy egy metrika neve, mint **a memória bájt használatban**.

### <a name="dimension-keys"></a>Méretbillentyűk
A dimenzió egy kulcs vagy értékpár, amely segít leírni az összegyűjtött metrika további jellemzőit. A további jellemzők használatával további információkat gyűjthet a metrikáról, amely lehetővé teszi a mélyebb elemzéseket. Például a használatban lévő **memóriabájtok** metrika rendelkezhet egy **folyamat** nevű dimenziókulcs, amely rögzíti, hogy hány bájt nyi memóriát az egyes folyamatok egy virtuális gép használ. Ezzel a kulccsal szűrheti a metrikát, hogy megállapíthassa, mennyi memóriát használnak az adott folyamatok, vagy azonosíthatja az első öt folyamatot a memóriahasználat alapján.
A dimenziók nem kötelezőek, nem minden mutató rendelkezhet dimenziókkal. Egy egyéni metrika legfeljebb 10 dimenzióval rendelkezhet.

### <a name="dimension-values"></a>Dimenzióértékek
Metrika-adatpont jelentésénekénként a jelentett metrika minden dimenziókulcsához tartozik egy megfelelő dimenzióérték. Előfordulhat például, hogy a ContosoApp által használt memóriát szeretné jelenteni a virtuális gépen:

* A metrika neve használatban lévő **memóriabájtok lesz.**
* A dimenzió kulcs lenne **folyamat**.
* A dimenzióértéke **ContosoApp.exe**lesz.

Metrikaérték közzétételekor dimenziókulcsonként csak egy dimenzióértéket adhat meg. Ha ugyanazt a memóriakihasználtságot gyűjti a virtuális gép több folyamatához, több metrikaértéket is jelenthet az adott időbélyeghez. Minden metrikaérték más dimenzióértéket **ad** meg a Folyamat dimenziókulcshoz.
A dimenziók nem kötelezőek, nem minden mutató rendelkezhet dimenziókkal. Ha egy metrikabejegyzés dimenziókulcsokat határoz meg, a megfelelő dimenzióértékek kötelezőek.

### <a name="metric-values"></a>Metrika értékei
Az Azure Monitor az összes metrikát egyperces részletességi időközönként tárolja. Tisztában vagyunk azzal, hogy egy adott percben előfordulhat, hogy egy metrika többször is mintavételezésre kerülhet. Ilyen például a PROCESSZOR-kihasználtság. Vagy lehet, hogy meg kell mérni a sok diszkrét események. Erre példa a bejelentkezési tranzakció késése. Az Azure Monitorban kibocsátandó és fizetendő nyers értékek számának korlátozásához helyileg előre összesítheti és kibocsáthatja az értékeket:

* **Min**: A perc során az összes mintából és mérésből megfigyelt legkisebb megfigyelt érték.
* **Max**: A perc ben az összes mintából és mérésből megfigyelt legnagyobb érték.
* **Összeg**: Az összes megfigyelt érték összegzése az összes mintából és mérésből a perc ben.
* **Darabszám**: A perc alatt vett minták és mérések száma.

Ha például egy adott perc alatt négy bejelentkezési tranzakció volt az alkalmazásban, az eredményül kapott mért késések a következők lehetnek:

|1. tranzakció|2. tranzakció|3. tranzakció|4. tranzakció|
|---|---|---|---|
|7 ezres|4 ezres|13 ezres|16 ezres|
|

Ezután az Azure Monitor nak a következő metrikus kiadvány a következő:
* Min. 4.
* Max: 16
* Összeg: 40
* Gróf: 4

Ha az alkalmazás nem tudja előre összesíteni helyileg, és minden egyes különálló mintát vagy eseményt azonnal ki kell jene nie a gyűjtésután, kibocsáthatja a nyers mérték értékeket. Például minden alkalommal, amikor egy bejelentkezési tranzakció történik az alkalmazásban, akkor közzé tesz egy metrikát az Azure Monitor csak egyetlen méréssel. Így egy 12 ms-t igénybe vett bejelentkezési tranzakció esetén a metrikaközzététele a következő:
* Min: 12
* Max: 12
* Összeg: 12
* Gróf: 1

Ezzel a folyamattal egy adott percben több értéket is kibocsáthat ugyanahhoz a metrikához és dimenziókombinációhoz. Az Azure Monitor ezután egy adott percben kibocsátott összes nyers értéket egy összegződik.

### <a name="sample-custom-metric-publication"></a>Példa egyéni metrikus kiadványra
A következő példában egy egyéni **metrika nevű memóriabájtok használatban** a metrika névtér **memóriaprofil** egy virtuális gép. A mérőszám egyetlen dimenzióval **rendelkezik,** a Folyamat . Az adott időbélyegnél két különböző folyamat metrikaértékeit bocsátjuk ki:

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]  
> Application Insights, a diagnosztikai bővítmény, és az InfluxData Telegraf ügynök már be van állítva, hogy metrikaértékeket a megfelelő regionális végpont, és az összes előző tulajdonságok minden egyes kibocsátás ban.
>
>

## <a name="custom-metric-definitions"></a>Egyéni metrikadefiníciók
Nem kell előre definiálnia egy egyéni metrikát az Azure Monitorban, mielőtt azt kibocsátanák. Minden közzétett metrikus adatpont névtér-, név- és dimenzióadatokat tartalmaz. Így az első alkalommal, amikor egy egyéni metrika kerül kibocsátásra az Azure Monitor, a metrika definícióautomatikusan létrejön. Ez a metrika-definíció ezután felderíthető bármely erőforrás, amely a metrika által kibocsátott a metrika definíciók on keresztül.

> [!NOTE]  
> Az Azure Monitor még nem támogatja az egyéni metrika **egységek** definiálását.

## <a name="using-custom-metrics"></a>Egyéni mutatók használata
Miután az egyéni metrikák elküldése az Azure Monitor, böngészhet az Azure Portalon keresztül, és lekérdezheti őket az Azure Monitor REST API-kon keresztül. Riasztásokat is létrehozhat róluk, hogy értesítse Önt, ha bizonyos feltételek teljesülnek.

> [!NOTE]
> Az egyéni mutatók megtekintéséhez olvasónak vagy közreműködői szerepkörnek kell lennie.

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Az egyéni metrikák böngészése az Azure Portalon keresztül
1.    Nyissa meg az [Azure Portalt.](https://portal.azure.com)
2.    Jelölje ki a **Figyelő** ablaktáblát.
3.    Válassza a **Metrika** lehetőséget.
4.    Válassza ki azt az erőforrást, amelyhez egyéni mutatókat bocsátott ki.
5.    Válassza ki a metrikák névtér az egyéni metrika.
6.    Válassza ki az egyéni metrikát.

## <a name="supported-regions"></a>Támogatott régiók
A nyilvános előzetes verzió során az egyéni metrikák közzétételének lehetősége csak az Azure-régiók egy részhalmazában érhető el. Ez a korlátozás azt jelenti, hogy a metrikák csak a támogatott régiók egyikének erőforrásaihoz tehetők közzé. Az alábbi táblázat felsorolja a támogatott Azure-régiók egyéni metrikák készletét. Felsorolja továbbá azokat a megfelelő végpontokat is, amelyekhez az adott régiókban lévő erőforrások metrikáit közzé kell tenni a következő célokra:

|Azure-régió |Regionális végpontelőtag|
|---|---|
| **USA és Kanada** | |
|USA nyugati középső régiója | https:\//westcentralus.monitoring.azure.com/ |
|USA nyugati régiója, 2.       | https:\//westus2.monitoring.azure.com/ |
|USA északi középső régiója | https:\//northcentralus.monitoring.azure.com
|USA déli középső régiója| https:\//southcentralus.monitoring.azure.com/ |
|USA középső régiója      | https:\//centralus.monitoring.azure.com |
|Közép-Kanada | https:\//canadacentral.monitoring.azure.comc
|USA keleti régiója| https:\//eastus.monitoring.azure.com/ |
| **Európa** | |
|Észak-Európa    | https:\//northeurope.monitoring.azure.com/ |
|Nyugat-Európa     | https:\//westeurope.monitoring.azure.com/ |
|Az Egyesült Királyság déli régiója | https:\//uksouth.monitoring.azure.com
|Közép-Franciaország | https:\//francecentral.monitoring.azure.com |
| **Afrika** | |
|Dél-Afrika Észak-Afrika | https:\//southafricanorth.monitoring.azure.com
| **Ázsia** | |
|Közép-India | https:\//centralindia.monitoring.azure.com
|Kelet-Ausztrália | https:\//australiaeast.monitoring.azure.com
|Kelet-Japán | https:\//japaneast.monitoring.azure.com
|Délkelet-Ázsia  | https:\//southeastasia.monitoring.azure.com |
|Kelet-Ázsia | https:\//eastasia.monitoring.azure.com
|Dél-Korea középső régiója   | https:\//koreacentral.monitoring.azure.com


## <a name="quotas-and-limits"></a>Kvóták és korlátok
Az Azure Monitor a következő használati korlátozásokat írja elő az egyéni metrikákra:

|Kategória|Korlát|
|---|---|
|Aktív idősorok/előfizetések/régió|50 000|
|Dimenziókulcsok metrikaszerint|10|
|Metrikanévterek, metrikanevek, dimenziókulcsok és dimenzióértékek karakterlánchossza|256 karakter|

Az aktív idősorok a metrika, a dimenziókulcs vagy a dimenzióérték bármely olyan egyedi kombinációjaként vannak definiálva, amelynek az elmúlt 12 órában közzétett metrikaértékeket tettek közzé.

## <a name="next-steps"></a>További lépések
Különböző szolgáltatások egyéni mutatóinak használata: 
 - [Virtuális gépek](collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Virtuálisgép-méretezési csoport](collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Azure virtuális gépek (klasszikus)](collect-custom-metrics-guestos-vm-classic.md)
 - [Linux virtuális gép a Telegraf ügynök használatával](collect-custom-metrics-linux-telegraf.md)
 - [REST API](../../azure-monitor/platform/metrics-store-custom-rest-api.md)
 - [Klasszikus felhőszolgáltatások](collect-custom-metrics-guestos-vm-cloud-service-classic.md)
 