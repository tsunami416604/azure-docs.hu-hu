---
title: Egyéni metrikák a Azure Monitorban
description: Ismerkedjen meg az egyéni metrikákkal a Azure Monitorban és azok modellezésében.
author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 3e3f45c1802d501e2320930c35073ec89ff38124
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77662348"
---
# <a name="custom-metrics-in-azure-monitor"></a>Egyéni metrikák a Azure Monitorban

Amikor erőforrásokat és alkalmazásokat helyez üzembe az Azure-ban, érdemes elkezdeni a telemetria gyűjtését, hogy betekintést nyerjen a teljesítménybe és az állapotba. Az Azure néhány mérőszámot elérhetővé tesz a dobozból. Ezeket a metrikákat standard vagy platformnak nevezzük. Azonban a természetük korlátozott. Érdemes lehet összegyűjteni néhány egyéni teljesítménymutatót vagy üzleti specifikus mérőszámot a mélyebb elemzések biztosításához.
Ezek az **Egyéni** metrikák az alkalmazás telemetria, az Azure-erőforrásokon futtatott ügynökkel, vagy akár egy külső figyelési rendszerrel is összegyűjthetők, és közvetlenül a Azure monitorhoz lesznek elküldve. A Azure Monitor közzétételét követően az Azure-erőforrások és-alkalmazások egyéni metrikáit tallózással, lekérdezéssel és riasztással láthatja el egymás mellett az Azure által kibocsátott szabványos metrikákkal.

## <a name="send-custom-metrics"></a>Egyéni metrikák küldése
Az egyéni metrikák több módszer használatával is elküldhetők Azure Monitorba:
- Az alkalmazást az Azure Application Insights SDK használatával, az egyéni telemetria pedig a Azure Monitorba küldheti. 
- Telepítse a Windows Azure Diagnostics (WAD) bővítményt az [Azure](collect-custom-metrics-guestos-resource-manager-vm.md)-beli virtuális gépre, a [virtuálisgép-méretezési csoportra](collect-custom-metrics-guestos-resource-manager-vmss.md), a [klasszikus virtuális gépre](collect-custom-metrics-guestos-vm-classic.md)vagy a [klasszikus Cloud Servicesre](collect-custom-metrics-guestos-vm-cloud-service-classic.md) , és küldje el a teljesítményszámlálók Azure monitor. 
- Telepítse a [InfluxData-előgrafi ügynököt](collect-custom-metrics-linux-telegraf.md) az Azure Linux rendszerű virtuális gépre, és küldje el a metrikákat a Azure monitor kimeneti beépülő modullal.
- Egyéni metrikák küldése [közvetlenül a Azure Monitor REST API](../../azure-monitor/platform/metrics-store-custom-rest-api.md)`https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`.

Ha egyéni metrikákat küld Azure Monitorre, akkor a jelentett adatpontoknak vagy értékeknek a következő információkat kell tartalmazniuk.

### <a name="authentication"></a>Authentication
Ha egyéni metrikákat szeretne beküldeni a Azure Monitorba, a metrikát elküldő entitásnak érvényes Azure Active Directory (Azure AD) tokenre van szüksége a kérelem **tulajdonosi** fejlécében. Az érvényes tulajdonosi jogkivonatok beszerzésének néhány támogatott módja:
1. [Felügyelt identitások az Azure-erőforrásokhoz](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Identitást ad az Azure-erőforrásokhoz, például egy virtuális géphez. Managed Service Identity (MSI) úgy van kialakítva, hogy erőforrás-engedélyeket biztosítson bizonyos műveletek végrehajtásához. Ilyen például, ha egy erőforrás saját magáról bocsát ki mérőszámokat. Egy erőforrás vagy annak MSI-je egy másik erőforráson is megadhat **figyelési metrikák közzétevői** engedélyeit. Ezzel az engedéllyel az MSI más erőforrásokhoz is kibocsáthat metrikákat.
2. [Azure ad-szolgáltatásnév](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals). Ebben a forgatókönyvben egy Azure AD-alkalmazás vagy-szolgáltatás engedélyeket rendelhet az Azure-erőforrásokra vonatkozó mérőszámok kibocsátásához.
A kérelem hitelesítéséhez Azure Monitor érvényesíti az alkalmazás tokenjét az Azure AD nyilvános kulcsaival. A meglévő **figyelési metrikák közzétevői** szerepköre már rendelkezik ezzel az engedéllyel. Ez elérhető a Azure Portalban. Az egyszerű szolgáltatásnév attól függően, hogy milyen erőforrásokra bocsát ki egyéni metrikákat, megadhatja a **figyelési metrikák közzétevői** szerepkörét a szükséges hatókörben. Ilyenek például az előfizetés, az erőforráscsoport vagy az adott erőforrás.

> [!NOTE]  
> Ha Azure AD-jogkivonatot kér egyéni metrikák kibocsátására, győződjön meg arról, hogy a tokent kérő célközönség vagy erőforrás https://monitoring.azure.com/. Ügyeljen arra, hogy tartalmazza a "/" zárót.

### <a name="subject"></a>Tárgy
Ez a tulajdonság rögzíti, hogy az egyéni metrika melyik Azure-erőforrás-AZONOSÍTÓval van jelezve. Ezek az adatok az API-hívás URL-címében lesznek kódolva. Az egyes API-k csak egyetlen Azure-erőforráshoz küldhetnek metrikai értékeket.

> [!NOTE]  
> Egy erőforráscsoport vagy előfizetés erőforrás-azonosítójával nem lehet egyéni metrikákat kibocsátani.
>
>

### <a name="region"></a>Régió
Ez a tulajdonság azt az Azure-régiót rögzíti, amelybe a metrikákat kibocsátó erőforrást telepíti. A metrikákat ugyanarra a Azure Monitor regionális végpontra kell kiadni, mint az erőforrás üzembe helyezésének régióját. Az USA nyugati régiójában üzembe helyezett virtuális gépek egyéni metrikáit például a WestUS regionális Azure Monitor végpontjának kell elküldeni. A régió információi az API-hívás URL-címében is kódolva vannak.

> [!NOTE]  
> A nyilvános előzetes verzióban az egyéni metrikák csak az Azure-régiók egy részhalmazában érhetők el. A támogatott régiók listáját a cikk egy későbbi szakaszában dokumentáljuk.
>
>

### <a name="timestamp"></a>Időbélyeg
A Azure Monitor elküldett minden adatpontot időbélyeg-ként kell megjelölni. Ez az időbélyeg rögzíti azt a dátumot, amikor a metrika értékét méri vagy gyűjti. A Azure Monitor a metrikákkal rendelkező metrikus adatokat az elmúlt 20 percen belül, a későbbiekben pedig 5 percet fogad el. Az időbélyegnek ISO 8601 formátumúnak kell lennie.

### <a name="namespace"></a>Névtér
A névterek segítségével kategorizálhatja vagy csoportosíthatja a hasonló metrikákat. A névterek segítségével elkülönítheti a metrikák csoportjai között, amelyek különböző adatokat gyűjthetnek vagy teljesítménymutatókat. Előfordulhat például, hogy rendelkezik egy **contosomemorymetrics** nevű névtérrel, amely nyomon követi az alkalmazás profilját használó memóriahasználat mérőszámait. Egy másik, **contosoapptransaction** nevű névtér nyomon követheti az alkalmazás felhasználói tranzakcióinak összes mérőszámát.

### <a name="name"></a>Name (Név)
A **név** a jelentett metrika neve. Általában a név elég leíró a mért értékek azonosításához. Egy példa egy metrika, amely az adott virtuális gépen használt memória bájtjainak számát méri. Lehet, hogy a metrika neve például a **használatban lévő memória bájtjai**.

### <a name="dimension-keys"></a>Dimenzió kulcsai
A dimenzió egy kulcs vagy érték pár, amely segít a begyűjtött metrika további jellemzőinek leírásában. A további jellemzők használatával további információkat gyűjthet a metrikáról, ami mélyebb elemzéseket tesz lehetővé. Előfordulhat például, hogy a **használatban lévő memória bájtjainak** egy **folyamat** nevű dimenzió kulcsa van, amely rögzíti, hogy a virtuális gép minden egyes folyamata hány bájt memóriát használ fel. Ennek a kulcsnak a használatával szűrheti a metrikát, hogy megtudja, milyen mennyiségű memória-specifikus folyamat használja, vagy az első öt folyamatot a memóriahasználat alapján azonosítsa.
A méretek nem kötelezőek, nem minden metrika rendelkezhet dimenziókkal. Egy egyéni metrika legfeljebb 10 dimenzióval rendelkezhet.

### <a name="dimension-values"></a>Dimenzió értékei
A metrikai adatpontok jelentésekor a jelentésben szereplő összes dimenzió kulcshoz tartozik egy megfelelő dimenzióérték. Előfordulhat például, hogy a virtuális gépen a ContosoApp által használt memóriát szeretné jelenteni:

* A metrika neve a **használatban lévő memória bájtjai**.
* A dimenzió kulcsa **feldolgozható**.
* A dimenzió értéke **ContosoApp. exe**lenne.

Metrikus érték közzétételekor csak egy dimenzió érték adható meg dimenzió-kulcsként. Ha a virtuális gépen több folyamat esetében is ugyanazt a memóriát keresi, több metrikai értéket is jelenthet az adott időbélyeghez. Minden metrika értéke eltérő dimenzióértéket határozhat meg a **folyamat** dimenzió kulcsához.
A méretek nem kötelezőek, nem minden metrika rendelkezhet dimenziókkal. Ha egy metrikai bejegyzés definiálja a dimenzió kulcsait, a megfelelő dimenzióértékeket kötelező megadni.

### <a name="metric-values"></a>Metrika értékei
Azure Monitor az összes mérőszámot egyperces részletességi intervallumokban tárolja. Tisztában vagyunk azzal, hogy egy adott percben egy mérőszámot többször is fel kell venni. Ilyen például a CPU kihasználtsága. Vagy előfordulhat, hogy sok különálló esemény esetében is mérhetőnek kell lennie. Ilyen például a bejelentkezési tranzakció késése. Ha korlátozni szeretné a Azure Monitor által kibocsátani és kifizetni kívánt nyers értékek számát, helyileg előre összesítheti és kibocsáthatja az értékeket:

* **Min**: a percben a minták és a mérések minimálisan megfigyelt értéke.
* **Max**: a percenként megfigyelt értékek maximális száma az összes mintából és mérésből.
* **Sum**: az összes mintából és mérésből származó összes megfigyelt érték összegzése a percben.
* **Darabszám**: a percben végrehajtott minták és mérések száma.

Ha például egy percen belül 4 bejelentkezési tranzakció lett az alkalmazásba, akkor az eredményül kapott mért késések a következők:

|1\. tranzakció|2\. tranzakció|3\. tranzakció|4\. tranzakció|
|---|---|---|---|
|7 MS|4 MS|13 MS|16 MS|
|

Ezután az eredményül kapott metrikai kiadvány Azure Monitor a következő lesz:
* Minimum: 4
* Max.: 16
* Összeg: 40
* Darabszám: 4

Ha az alkalmazás nem tud helyileg előre összesíteni, és minden különálló mintát vagy eseményt azonnal ki kell bocsátani a gyűjteményből, a nyers mérték értékeit is kibocsáthatja. Például minden alkalommal, amikor bejelentkezik egy bejelentkezési tranzakció az alkalmazáson, egy mérőszámot tesz közzé, amely csak egyetlen méréssel Azure Monitor. Tehát egy 12 ms-os bejelentkezési tranzakció esetében a metrikus kiadvány a következő lesz:
* Perc: 12
* Max.: 12
* Összeg: 12
* Darabszám: 1

Ezzel a folyamattal több értéket is kibocsáthat ugyanahhoz a metrikai és dimenzió kombinációhoz egy adott percben. Azure Monitor ezután végrehajtja az adott percenként kiállított összes nyers értéket, és összesíti azokat.

### <a name="sample-custom-metric-publication"></a>Példa egyéni metrika közzétételére
A következő példában létrehozunk egy, a virtuális gép metrikai névtérének **memóriájában** **használatos memória-bájtok** nevű egyéni metrikát. A metrika egyetlen, **folyamat**nevű dimenzióval rendelkezik. Az adott időbélyeg esetében két különböző folyamat metrikáit bocsátjuk ki:

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
> A Application Insights, a diagnosztikai bővítmény és a InfluxData-Graf ügynök már úgy van konfigurálva, hogy metrikai értékeket állítson ki a megfelelő regionális végponton, és az összes korábbi tulajdonságot az összes kibocsátásban elvégezze.
>
>

## <a name="custom-metric-definitions"></a>Egyéni metrika-definíciók
A kibocsátása előtt nem kell előre definiálni egy egyéni metrikát Azure Monitor. Minden közzétett metrikai adatpont névteret, nevet és dimenzió információt tartalmaz. Tehát amikor a rendszer először egy egyéni metrikát bocsát ki Azure Monitorre, a rendszer automatikusan létrehoz egy metrika-definíciót. Ez a metrikai definíció ezt követően minden olyan erőforráson felderíthető, amelyet a metrika a metrika-definíciók használatával bocsát ki.

> [!NOTE]  
> A Azure Monitor még nem támogatja az egyéni metrika **egységének** definiálását.

## <a name="using-custom-metrics"></a>Egyéni metrikák használata
Miután elküldte az egyéni metrikákat Azure Monitorre, böngészheti őket a Azure Portal, és a Azure Monitor REST API-kon keresztül kérdezheti le őket. Riasztásokat is létrehozhat rajtuk, hogy értesítést kapjon, ha bizonyos feltételek teljesülnek.
### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Egyéni metrikák böngészése az Azure Portal használatával
1.  Nyissa meg az [Azure Portalt](https://portal.azure.com).
2.  Válassza a **figyelés** ablaktáblát.
3.  Válassza a **Metrika** lehetőséget.
4.  Válassza ki azt az erőforrást, amelyről egyéni metrikákat adott ki.
5.  Válassza ki az egyéni metrika metrikai névterét.
6.  Válassza ki az egyéni metrikát.

## <a name="supported-regions"></a>Támogatott régiók
A nyilvános előzetes verzióban az egyéni metrikák közzétételének lehetősége csak az Azure-régiók egy részhalmazában érhető el. Ez a korlátozás azt jelenti, hogy a metrikák csak az egyik támogatott régió erőforrásai számára tehetők közzé. A következő táblázat felsorolja az egyéni metrikák támogatott Azure-régióinak készletét. Emellett felsorolja azokat a végpontokat is, amelyek az adott régiókban lévő erőforrások metrikáit teszik közzé:

|Azure-régió |Regionális végpont előtagja|
|---|---|
| **Egyesült Államok és Kanada** | |
|USA nyugati középső régiója | https:\//westcentralus.monitoring.azure.com/ |
|USA nyugati régiója, 2.       | https:\//westus2.monitoring.azure.com/ |
|USA északi középső régiója | https:\//northcentralus.monitoring.azure.com
|USA déli középső régiója| https:\//southcentralus.monitoring.azure.com/ |
|USA középső régiója      | https:\//centralus.monitoring.azure.com |
|Közép-Kanada | https:\//canadacentral.monitoring.Azure.comc
|USA keleti régiója| https:\//eastus.monitoring.azure.com/ |
| **Európa** | |
|Észak-Európa    | https:\//northeurope.monitoring.azure.com/ |
|Nyugat-Európa     | https:\//westeurope.monitoring.azure.com/ |
|Az Egyesült Királyság déli régiója | https:\//uksouth.monitoring.azure.com
|Közép-Franciaország | https:\//francecentral.monitoring.azure.com |
| **Afrika** | |
|Dél-Afrika északi régiója | https:\//southafricanorth.monitoring.azure.com
| **Ázsia** | |
|Közép-India | https:\//centralindia.monitoring.azure.com
|Kelet-Ausztrália | https:\//australiaeast.monitoring.azure.com
|Kelet-Japán | https:\//japaneast.monitoring.azure.com
|Délkelet-Ázsia  | https:\//southeastasia.monitoring.azure.com |
|Kelet-Ázsia | https:\//eastasia.monitoring.azure.com
|Dél-Korea középső régiója   | https:\//koreacentral.monitoring.azure.com


## <a name="quotas-and-limits"></a>Kvóták és korlátozások
A Azure Monitor a következő használati korlátokat szabja meg az egyéni metrikák esetében:

|Kategória|Korlát|
|---|---|
|Aktív idősorozat/előfizetések/régió|50 000|
|Dimenzió kulcsok/metrika|10|
|A metrikai névterek, a metrikák nevei, a dimenzió kulcsai és a dimenzió értékeinek karakterlánc-hossza|256 karakter|

Az aktív idősorozatok a metrika, a dimenzióérték vagy a dimenzió érték bármely egyedi kombinációja, amely az elmúlt 12 órában közzétett metrikai értékeket tartalmaz.

## <a name="next-steps"></a>Következő lépések
Egyéni metrikák használata különböző szolgáltatásokból: 
 - [Virtual Machines](collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Virtuálisgép-méretezési csoport](collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Azure Virtual Machines (klasszikus)](collect-custom-metrics-guestos-vm-classic.md)
 - [Linux rendszerű virtuális gép a saját Graf ügynök használatával](collect-custom-metrics-linux-telegraf.md)
 - [REST API](../../azure-monitor/platform/metrics-store-custom-rest-api.md)
 - [Klasszikus Cloud Services](collect-custom-metrics-guestos-vm-cloud-service-classic.md)
 
