---
title: Egyéni metrikák az Azure monitorban
description: Ismerje meg az Azure Monitor és modellezése hogyan egyéni metrikákat.
author: ancav
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: c136772e27dab014c22234f1ef1d2baddd2ffe58
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978080"
---
# <a name="custom-metrics-in-azure-monitor"></a>Egyéni metrikák az Azure monitorban

Erőforrások és alkalmazások az Azure-beli üzembe helyezése során érdemes követésével fontos információkhoz juthat a teljesítményt és állapotot telemetriai adatok gyűjtésének megkezdéséhez. Az Azure elérhetővé néhány metrika-a-beépített erőforrások üzembe helyezése során. Ezek az úgynevezett standard vagy a platform metrikákat. Ezek a metrikák azonban jellegű korlátozottak. Előfordulhat, hogy szeretne gyűjteni néhány egyéni teljesítménymutatók vagy business-specifikus metrikák részletesebb elemzéseket biztosít.
"Egyéni" metrikák a telemetriai, az Azure-erőforrások, vagy akár külső-a monitorozási rendszer futó ügynök keresztül gyűjtött lehetnek, és közvetlenül az Azure Monitor elküldve. Miután közzétette az Azure monitornak, Tallózás, lekérdezése és egyéni mérőszámok az Azure-erőforrások és alkalmazások egymás mellé a standard mérőszámok az Azure által kibocsátott riasztás.

## <a name="send-custom-metrics"></a>Egyéni mérőszámok küldése
Egyéni metrikák az Azure Monitor használatával többféle módszerrel lehet küldeni.
- Az Application Insights SDK használatával az alkalmazás tagolása és egyéni telemetriai adatok küldése az Azure monitornak 
- A Windows a diagnosztikai bővítmény telepíthető a [Azure virtuális gép](metrics-store-custom-guestos-resource-manager-vm.md), [virtuálisgép-méretezési](metrics-store-custom-guestos-resource-manager-vmss.md), [klasszikus virtuális gép](metrics-store-custom-guestos-classic-vm.md), vagy [klasszikus Felhőszolgáltatás](metrics-store-custom-guestos-classic-cloud-service.md)és teljesítményszámlálók küldése az Azure monitornak 
- Telepítse a [InfluxDB Telegraf ügynök](metrics-store-custom-linux-telegraf.md) az Azure Linux rendszerű virtuális gép és a küldési mérőszámok az Azure Monitor kimeneti beépülő modul használatával
- Egyéni mérőszámok küldése [közvetlenül az Azure Monitor REST API](metrics-store-custom-rest-api.md) https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics

Egyéni metrikák az Azure Monitor küld, ha egyes adatok pont (vagy értéket) jelentett tartalmaznia kell a következő információkat:

### <a name="authentication"></a>Hitelesítés
Egyéni metrikák az Azure monitornak küldje el a metrika elküldése entitás rendelkeznie kell egy érvényes Azure Active Directory-jogkivonat a "Tulajdonos" a kérelem fejlécében. Érvényes tulajdonosi jogkivonat-beszerzési néhány támogatott módja van:
1. [MSI (Felügyeltszolgáltatás-identitás)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) -identitást biztosít az Azure-erőforrás magát (például a virtuális Géphez). MSI úgy lett kialakítva, erőforrások hajthat végre az egyes műveletek – például lehetővé teszi a saját magáról metrikák kibocsátható egy erőforrást. Az erőforrás (vagy az MSI) "Figyelési metrikákat közzétevő" engedélyeket kaphatnak egy másik erőforrás, ezáltal az MSI kibocsátható, valamint egyéb erőforrások mérőszámait.
2. [AAD-szolgáltatásnév](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) -forgatókönyv itt az AAD-alkalmazás (szolgáltatás) lehet engedélyeket kibocsátható egy Azure-erőforrás metrikáit.
Hitelesíteni a kérelmet, az Azure Monitor ellenőrzi az alkalmazás jogkivonatát AAD nyilvános kulcsok használatával. A meglévő "Figyelési metrikákat közzétevő" szerepkör már rendelkezik ezzel az engedéllyel, amely elérhető az Azure Portalon. A szolgáltatásnév, attól függően, milyen erőforrásokat, azt fogja kibocsátó egyéni metrikákat, a "Figyelési metrikákat közzétevő" szerepkör a hatókörben szükséges (előfizetés, erőforráscsoport vagy adott erőforrás) kell megadni.

> [!NOTE]
> Ha egyéni metrikákat kibocsátható egy AAD-jogkivonatot kér győződjön meg, hogy a célközönség és az erőforrások a jogkivonat a kérelem vonatkozik https://monitoring.azure.com/ (így feltétlenül foglalja bele a záró "/")

### <a name="subject"></a>Tárgy
Ez a tulajdonság melyik Azure-erőforrás azonosítója, az egyéni metrika jelentett rögzíti. Ez az információ kódolva legyen kerül sor az API-hívás URL-címét. Minden API csak küldhet metrikaértékek egy egyetlen Azure-beli erőforráshoz.

> [!NOTE]
> Egyéni metrikákat az erőforrás-azonosítója egy erőforráscsoportba vagy előfizetésbe nelze generovat.
>
>

### <a name="region"></a>Régió
Ez a tulajdonság az erőforrás metrikáit, vannak kibocsátó van üzembe helyezve az Azure régióját rögzíti. Metrikák kell bocsátja ki, az ugyanazon az Azure Monitor regionális végpont, mint a terület az erőforrás üzembe van helyezve. Például egyéni metrikákat az USA nyugati Régiójában vannak üzembe helyezve a virtuális gép kell küldeni a WestUS regionális Azure Monitor-végpontra. A terület adatait is kódolva, az API-hívás URL-címét.

> [!NOTE]
> A nyilvános előzetes verzió használata során az egyéni metrikák csak akkor használható az Azure-régiók részhalmazát. Támogatott régiók listáját egy későbbi szakaszban olvashat a jelen cikk ismertetését.
>
>

### <a name="timestamp"></a>Időbélyeg
Az Azure Monitor küldött egyes adatpontok egy időbélyeggel kell megjelölni. Az időbélyegző rögzíti, amikor a metrikaérték volt mérni/gyűjtött dátuma és időpontja. Az Azure Monitor fogad el metrikaadatok időbélyegzőnél, amennyire az elmúlt 20 perc, ami a jövőben 5 perc.

### <a name="namespace"></a>Névtér
Névterek kategorizálása vagy csoportba foglalhatók a hasonló mérőszámok módon. Névterek mérőszámok, amelyek különböző információkat és teljesítménymutatók gyűjtése előfordulhat, hogy Csoportváltás egységekre bonthatja teszi lehetővé. Például, hogy sikerült nevű névtér *ContosoMemoryMetrics* , amely használt nyomon követése memória használati metrikák, amely az alkalmazás és a egy másik névtér nevű profil *ContosoAppTransaction* , amely nyomon követi az összes az alkalmazás felhasználói tranzakciókkal kapcsolatos metrikákat.

### <a name="name"></a>Name (Név)
Jelentve metrika neve. A név általában elég leíró meghatározásához, mit mérése történik. Például egy metrika van bájt kihasználtságának egy adott virtuális Géphez a memória számának mérésére lehet például "Memória Memória\lefoglalt memória kihasználtsága" egy metrika neve.

### <a name="dimension-keys"></a>Dimenzió kulcsai
Egy dimenzió egy kulcs/érték pár, amely segít a további jellemzőit adja meg az összegyűjtött metrikát. A további jellemzőit engedélyezze a mérőszám, amely lehetővé teszi, hogy részletesebb elemzéseket további információkat gyűjt. Például a "Memória Memória\lefoglalt memória kihasználtsága" metrika lehet egy "Folyamat", hány bájt memóriát a virtuális gép minden egyes folyamat használ rögzítő nevű dimenzió-kulcsot. Ez lehetővé teszi, hogy a metrikát, tekintse meg, mennyi memóriára vonatkozó folyamatok használják, vagy az első 5 folyamat azonosíthatja a memóriahasználat szűrése.
Minden egyéni metrika dimenziók legfeljebb 10 lehet.

### <a name="dimension-values"></a>Dimenzió értékei
Ha egy metrika datapoint minden dimenzió kulcs jelentett, a metrika a jelentéskészítő nincs a megfelelő dimenzióértékhez. Ha például szeretne jelentést a ContosoApp a virtuális gép által felhasznált memória:

* A metrika neve lenne *használatban lévő memória (bájt)*
* A dimenzió kulcs lenne *folyamat*
* A dimenzió érték lesz *ContosoApp.exe*

Ha egy metrikaérték közzététel, dimenzió kulcsonként dimenziót érték csak adható meg. Ha ugyanazt a memóriahasználat több folyamat számára a virtuális gépen, akkor az időbélyeg több metrikaértékek sikerült jelentést. Minden egyes metrikaérték kellene megadnia a folyamat dimenzió kulcs egy másik dimenzió értékét.

### <a name="metric-values"></a>Metrikaértékek
Az Azure Monitor minden metrika egy perces pontossággal időközönként tárolja. Tisztában vagyunk vele, hogy egy metrika kell mintát venni többször (például) CPU-kihasználtság) vagy mért számos diszkrét események (például. Jelentkezzen be a tranzakció késések) egy adott percben. Küldik, és az Azure monitorban fizetnie kell kerekített értékek számának korlátozásához, helyileg is előzetes összesítésre, és a gridre bocsáthatja ki az értékeket:

* Minimum: Összes samples/mérési értéket észlelt a percben minimálisan
* Maximális száma: Összes samples/mérési értéket észlelt a percben maximális
* Összeg: Az összegzési az összes minta/mérési megfigyelt értékek a percben
* Száma: A perc során vett minták/mérések száma

Például, ha 4 bejelentkezési tranzakciók során az alkalmazáshoz egy adott egy percet, és az amiatt végbemenő minden egyes mért késéseket is:

|1 tranzakció|2 tranzakció|Tranzakció 3|Tranzakció 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|
|

Az Azure Monitor az eredményül kapott metrika kiadvány a következő lesz:
* Min.: 4
* Maximális száma: 16
* Sum: 40
* Darabszám: 4

Ha az alkalmazás nem tudja, előzetes összesítésre helyileg, és minden diszkrét minta vagy a gyűjtemény követően azonnal event gridre bocsáthatja ki kell, a nyers mértékértékek el tudná küldeni.
Például minden alkalommal, amikor egy bejelentkezési tranzakció történt az alkalmazás közzé kívánja tenni a metrika az Azure monitornak csak egyetlen mérték az. Tehát jelentkezzen be a tranzakcióhoz igénybe vett 12 ms, majd a metrika kiadványban a következő lesz:
* Min.: 12
* Maximális száma: 12
* Sum: 12
* Szám: 1

Ez a folyamat lehetővé teszi gridre bocsáthatja ki az azonos metrika + dimenzió kombináció egy adott perc alatt több értéket. Az Azure Monitor ezután egy adott perc bocsátja ki nyers értékeit, és együtt összesített őket.

### <a name="sample-custom-metric-publication"></a>Egyéni metrika kiadvány minta
A következő példában létrehoz egy egyéni metrika neve "Memória Memória\lefoglalt memória kihasználtsága", a "Memória profil" virtuális gép metrikus névtérben. A metrika egy "Folyamat" nevű dimenziót tartalmaz. A megadott időbélyeget, az azt vannak kibocsátó két különböző folyamatok metrikaértékek:

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
> Az Application Insights, a Windows Azure Diagnostics bővítmény és a InfluxData Telegraf ügynök már konfigurálva vannak a megfelelő regionális végponthoz metrikaértékek küldik, és továbbítani az összes, a fenti tulajdonságokat az egyes kibocsátási.
>
>

## <a name="custom-metric-definitions"></a>Egyéni Metrikadefinícióinak beolvasása
Hiba esetén nem kell előre meghatározni az Azure monitorban egyéni metrika, előtt, bocsásson ki. Mivel az egyes közzétett metrika adatpontok névtérben, a név és a dimenzió adatokat tartalmaz, először egy egyéni metrika bocsásson ki az Azure monitornak metrikai definíció automatikusan létrejön. Ez a metrika definíciója majd felderíthető található a mérőszám a metrikadefiníciók keresztül ellen rendelkezésre erőforrásokat.

> [!NOTE]
> Az Azure Monitor még nem támogatja az "Egység" meghatározása egy egyéni metrika.

## <a name="using-custom-metrics"></a>Egyéni metrikák használatával
Egyéni metrikák az Azure monitornak elküldése után keresse meg azokat az Azure Portalon keresztül, lekérdezheti, ha az Azure Monitor REST API-kon keresztül vagy riasztások létrehozása a őket, így értesítést kaphat bizonyos feltételek teljesülése esetén.
### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Keresse meg az egyéni metrikákat az Azure Portalon
1.  Nyissa meg az [Azure Portalt](https://portal.azure.com)
2.  Válassza ki a Monitor panel
3.  Kattintson a metrikák
4.  Egyéni metrikákat az kibocsátott erőforrás kiválasztása
5.  Válassza ki a metrikák névteret, az egyéni metrika
6.  Válassza ki az egyéni metrika

## <a name="supported-regions"></a>Támogatott régiók
A nyilvános előzetes során tegyenek közzé egyéni metrikák csak akkor használható az Azure-régiók részhalmazát. Ez azt jelenti, hogy metrikák csak a támogatott régiók egyikében erőforrások tehetők közzé. Az alábbi táblázat felsorolja a támogatott Azure-régiók egyéni metrikákkal és ezekben a régiókban erőforrások mérőszámait közzé kell tenni a megfelelő végpontra ki.

|Azure-régió|Regionális végpont előtagja|
|---|---|
|USA keleti régiója|https://eastus.monitoring.azure.com/|
|USA déli középső régiója|https://southcentralus.monitoring.azure.com/|
|USA nyugati középső régiója|https://westcentralus.monitoring.azure.com/|
|USA nyugati régiója, 2.|https://westus2.monitoring.azure.com/|
|Délkelet-Ázsia|https://southeastasia.monitoring.azure.com/|
|Észak-Európa|https://northeurope.monitoring.azure.com/|
|Nyugat-Európa|https://westeurope.monitoring.azure.com/|

## <a name="quotas-and-limits"></a>Kvóták és korlátozások
Az Azure Monitor ír elő a következő használati korlátokkal, egyéni metrikákra vonatkozóan.

|Kategória|Korlát|
|---|---|
|Sorozat/előfizetés/régió aktív idő|50,000|
|Dimenzió kulcsai metrikánként|10|
|Karakterlánc hosszának metrika névterek, a metrikák nevei, a dimenzió kulcsai és a dimenzió értékei|maximális hossz 256 karakter|
Egy aktív idősorozat számít, ha bármelyik metrika, a dimenzió kulcs, a dimenzió értékét, amely még az elmúlt 12 órában közzétett metrikaértékek egyedi kombinációja.

## <a name="next-steps"></a>További lépések
Egyéni metrikák a különböző szolgáltatások használata 
 - [Virtuális gép](metrics-store-custom-guestos-resource-manager-vm.md)
 - [Virtuálisgép-méretezési csoportot](metrics-store-custom-guestos-resource-manager-vmss.md)
 - [Virtuális gép (klasszikus)](metrics-store-custom-guestos-classic-vm.md)
 - [Linux rendszerű virtuális gép Telegraf ügynök használatával](metrics-store-custom-linux-telegraf.md)
 - [REST API](metrics-store-custom-rest-api.md)
 - [Felhőszolgáltatás (klasszikus)](metrics-store-custom-guestos-classic-cloud-service.md)
 