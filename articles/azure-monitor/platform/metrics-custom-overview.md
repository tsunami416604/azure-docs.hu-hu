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
ms.openlocfilehash: aacfc98a9132a59c90a8f67d5a3877b75fa59dfe
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53605585"
---
# <a name="custom-metrics-in-azure-monitor"></a>Egyéni metrikák az Azure monitorban

Erőforrások és alkalmazások az Azure-beli üzembe helyezése során érdemes követésével fontos információkhoz juthat a teljesítményt és állapotot telemetriai adatok gyűjtésének megkezdéséhez. Az Azure elérhetővé teszi bizonyos metrikák, beépített. Ezek a metrikák standard vagy a platform nevezzük. Azonban azok szab jellegűek. Előfordulhat, hogy szeretné gyűjteni néhány egyéni teljesítménymutatók vagy business-specifikus metrikák részletesebb elemzéseket biztosít.
Ezek **egyéni** metrikák gyűjthetők a telemetriai, olyan ügynök, amely az Azure-erőforrások, vagy akár összetevős figyelési rendszert futtatja, és közvetlenül az Azure Monitor elküldött keresztül. Után azok van közzétéve, az Azure Monitor, keresse meg a lekérdezést, és riasztást az Azure-erőforrások és alkalmazások párhuzamosan lesz a standard mérőszámok az Azure által kibocsátott egyéni metrikákra vonatkozóan.

## <a name="send-custom-metrics"></a>Egyéni mérőszámok küldése
Egyéni metrikák az Azure Monitor használatával több módszert is lehet küldeni:
- Az Azure Application Insights SDK használatával az alkalmazás tagolása és egyéni telemetriát küldjön az Azure Monitor. 
- Telepítse a Windows Azure Diagnostics (WAD) bővítmény a [Azure virtuális gép](collect-custom-metrics-guestos-resource-manager-vm.md), [virtuálisgép-méretezési csoport](collect-custom-metrics-guestos-resource-manager-vmss.md), [klasszikus virtuális gép](collect-custom-metrics-guestos-vm-classic.md), vagy [klasszikus Cloud Services](collect-custom-metrics-guestos-vm-cloud-service-classic.md) és teljesítményszámlálók küldése az Azure monitornak. 
- Telepítse a [InfluxData Telegraf ügynök](collect-custom-metrics-linux-telegraf.md) az Azure Linux rendszerű virtuális gép és a küldési mérőszámok az Azure Monitor használatával kimeneti beépülő modult.
- Egyéni mérőszámok küldése [közvetlenül az Azure Monitor REST API a](../../azure-monitor/platform/metrics-store-custom-rest-api.md), `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`.

Ha egyéni mérőszámok küldése az Azure Monitor, minden egyes adatponthoz vagy jelentett értéket tartalmaznia kell a következő információkat.

### <a name="authentication"></a>Hitelesítés
Egyéni metrikák az Azure monitornak elküldeni az entitást, amely elküldi a metrika egy érvényes Azure Active Directory (Azure AD-) token kell a **tulajdonosi** a kérelem fejlécében. Érvényes tulajdonosi jogkivonat-beszerzési néhány támogatott módja van:
1. [Felügyelt identitások az Azure-erőforrások](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Hozzáférést biztosít az identitás egy Azure-erőforrás, például egy virtuális Gépet. Felügyelt Felügyeltszolgáltatás-identitás (MSI) célja az erőforrásoknak engedélyek bizonyos műveletek elvégzésére. Példa engedélyezi-e saját magáról metrikák kibocsátható egy erőforrást. Az erőforrás vagy az MSI-vel is megadható **figyelési metrikákat közzétevő** engedélyeit egy másik erőforrás. Ezzel az engedéllyel az MSI kibocsátható, valamint egyéb erőforrások mérőszámait.
2. [Az Azure AD-szolgáltatásnév](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals). Ebben a forgatókönyvben, egy Azure AD-alkalmazást vagy szolgáltatást rendelhetők engedélyek kibocsátható egy Azure-erőforrás metrikáit.
Hitelesíteni a kérelmet, az Azure Monitor ellenőrzi az alkalmazás jogkivonatát nyilvános kulcsok az Azure AD használatával. A meglévő **figyelési metrikákat közzétevő** szerepkör már rendelkezik ezzel az engedéllyel. Érhető el az Azure Portalon. A szolgáltatásnév, attól függően, milyen erőforrásokat, bocsát ki, egyéni metrikákat is megadható a **figyelési metrikákat közzétevő** szerepkör szükséges a hatókörben. A példák egy előfizetés, erőforráscsoport vagy adott erőforráshoz.

> [!NOTE]  
> Ha egy Azure ad-ben jogkivonat kibocsátható egyéni metrikákat kér, gondoskodjon arról, hogy a közönség, vagy a jogkivonatot a rendszer lekéri az erőforrás https://monitoring.azure.com/. Így feltétlenül foglalja bele a záró "/"karaktert.

### <a name="subject"></a>Tárgy
Ez a tulajdonság melyik Azure-erőforrás azonosítója, az egyéni metrika jelentett rögzíti. Ez az információ kódolva legyen kerül sor az API-hívás URL-címét. Minden API csak küldhet metrikaértékek egy egyetlen Azure-beli erőforráshoz.

> [!NOTE]  
> Egyéni metrikákat az erőforrás-azonosítója egy erőforráscsoportba vagy előfizetésbe nelze generovat.
>
>

### <a name="region"></a>Régió
Ez a tulajdonság az erőforrás metrikáit, már úgy van üzembe helyezve az Azure régióját rögzíti. Metrikák kell bocsátja ki, az ugyanazon az Azure Monitor regionális végpont, mint a terület az erőforrás üzembe van helyezve. Például az USA nyugati RÉGIÓJA üzembe helyezett virtuális gépek egyéni metrikákat kell küldeni a WestUS regionális Azure Monitor-végpontra. A terület adatait is kódolva, az API-hívás URL-címét.

> [!NOTE]  
> A nyilvános előzetes során egyéni metrikákat csak válnak elérhetővé az Azure-régiók részhalmazát. Támogatott régiók listáját egy későbbi szakaszban olvashat a jelen cikk ismertetését.
>
>

### <a name="timestamp"></a>Időbélyeg
Az Azure Monitor küldött egyes adatpontok egy időbélyeggel kell megjelölni. Az időbélyegző rögzíti, amikor a metrikaérték mért vagy gyűjtött dátuma és időpontja. Az Azure Monitor fogad el metrikaadatok időbélyegzőnél szerint 20 perc a múltban és a jövőben 5 perc.

### <a name="namespace"></a>Névtér
Névterek kategorizálása vagy csoportba foglalhatók a hasonló mérőszámok módon. Névterek használatával érheti el, amelyek különböző információkat és teljesítménymutatók gyűjt metrikákat csoportjait elkülönítését. Például előfordulhat, hogy rendelkezik egy nevű névtér **ContosoMemoryMetrics** , amely nyomon követi a használható memória metrikák, amely az alkalmazás profilját. Egy másik névtér nevű **ContosoAppTransaction** előfordulhat, hogy nyomon követheti az alkalmazás felhasználói tranzakció összes metrikákat.

### <a name="name"></a>Name (Név)
**Név** jelentve metrika neve. A név általában elég leíró meghatározásához, mit kell mérni. Ilyen például, egy metrikát, amely egy adott virtuális gép a használt memória (bájt) számát méri. Előfordulhat, hogy a metrika neve például **Memória\lefoglalt memória**.

### <a name="dimension-keys"></a>Dimenzió kulcsai
Egy dimenzió egy kulcs vagy érték pár, amely segít a további jellemzőit adja meg az összegyűjtött metrikát. A további jellemzőit használatával is összegyűjtheti a további információ a mérőszám, amely lehetővé teszi, hogy részletesebb elemzéseket. Például a **Memória\lefoglalt memória** metrika előfordulhat, hogy a dimenzió nevű kulcsot **folyamat** , amely rögzíti, hány bájt memóriát a virtuális gép minden egyes folyamat használ fel. Ez a kulcs használatával szűrheti a metrika mennyi memóriát meghatározott folyamatok használnak vagy a felső öt folyamatok azonosíthatja a memóriahasználat.
Minden egyéni metrika dimenziók legfeljebb 10 lehet.

### <a name="dimension-values"></a>Dimenzió értékei
Ha egy metrika adatpont minden dimenzió kulcs jelentett, a metrika a reporting nincs a megfelelő dimenzióértékhez. Például akkor előfordulhat, hogy szeretne jelentést készíteni a ContosoApp a virtuális gép által felhasznált memória:

* A metrika neve lenne **használatban lévő memória (bájt)**.
* A dimenzió kulcs lenne **folyamat**.
* A dimenzió érték lesz **ContosoApp.exe**.

Ha egy metrikaérték közzététel, dimenzió kulcsonként dimenziót érték csak adható meg. Ha a virtuális gépen több folyamatok ugyanazt a memóriahasználat, az időbélyeg több metrikaértékek jelentést. Minden egyes metrikaérték lehet megadni másik dimenzió értékét a **folyamat** dimenzió kulcsot.

### <a name="metric-values"></a>Metrikaértékek
Az Azure Monitor minden metrika egy perces pontossággal időközönként tárolja. Tisztában vagyunk vele, hogy egy adott percben metrika előfordulhat, hogy kell többször kell mintát venni. Ilyen például a CPU-kihasználtság. Vagy előfordulhat, hogy számos különálló események mért van szükség. Ilyen például, jelentkezzen be a tranzakció késéseket. Küldik, és az Azure monitorban fizetnie kell kerekített értékek számának korlátozásához, helyileg is előzetes összesítésre, és a gridre bocsáthatja ki az értékeket:

* **Min**: A minimális érték a mintákat és mérést a percben figyelhető meg.
* **Maximális**: A maximális érték a mintákat és mérést a percben figyelhető meg.
* **Sum**: Az összegzés a mintákat és mérést megfigyelt értékek az percben.
* **Száma**: Minták és a percben mérések száma.

Például, ha 4 bejelentkezési tranzakciók során az alkalmazáshoz egy adott percenként, a létrejövő mért késéseket az egyes a következő lehet:

|1 tranzakció|2 tranzakció|Tranzakció 3|Tranzakció 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|
|

Ezután az Azure Monitor az eredményül kapott metrika kiadvány a következők lennének:
* Min.: 4
* Maximális száma: 16
* Összeg: 40
* Száma: 4

Ha az alkalmazás nem tudja, előzetes összesítésre helyileg, és minden diszkrét minta vagy a gyűjtemény követően azonnal event gridre bocsáthatja ki kell, a nyers mértékértékek el tudná küldeni. Például minden alkalommal, amikor egy bejelentkezési tranzakciónak az alkalmazáshoz, a metrika csak egyetlen mérték az Azure Monitor tesz közzé. Így bejelentkezési tranzakció igénybe vett 12 ms, a metrika kiadvány a következő lesz:
* Min.: 12
* Maximális száma: 12
* Összeg: 12
* Száma: 1

Ez a folyamat a gridre bocsáthatja ki az azonos metrika, valamint a dimenzió kombináció egy adott perc alatt több érték. Az Azure Monitor majd bocsátja ki egy adott perc kerekített értékek vesz igénybe, és azokat összesítve együtt.

### <a name="sample-custom-metric-publication"></a>Egyéni metrika kiadvány minta
A következő példában hoz létre egy egyéni metrika nevű **használatban lévő memória (bájt)** a metrika névtérben **memória profil** egy virtuális géphez. A metrika rendelkezik egy dimenziót nevű **folyamat**. A megadott időbélyeg küldik azt két különböző folyamatok metrikaértékek:

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
> Az Application Insights, a diagnosztikai bővítmény és a InfluxData Telegraf ügynök már konfigurálva vannak a megfelelő regionális végponthoz metrikaértékek küldik, és továbbítani az összes, az előző tulajdonságai az egyes kibocsátási.
>
>

## <a name="custom-metric-definitions"></a>Egyéni metrikadefinícióinak beolvasása
Hiba esetén nem kell hardverszállítók egy egyéni metrika az Azure monitorban, mielőtt azt bocsásson ki. Egyes közzétett metrika adatpontok névtér, a neve, és a dimenzió adatokat tartalmaz. Először egy egyéni metrika bocsásson ki az Azure monitornak, így a rendszer automatikusan létrehoz egy metrika definíciója. Ez a metrika definíciója majd felderíthető található erőforrásokat a mérőszám percentként ellen a metrikadefiníciók keresztül.

> [!NOTE]  
> Az Azure Monitor még nem támogatja a definiálása **egységek** egy egyéni metrika.

## <a name="using-custom-metrics"></a>Egyéni metrikák használatával
Egyéni metrikák az Azure Monitor az elküldésüket követően keresse meg azokat az Azure Portalon, és lekérdezheti, ha az Azure Monitor REST API-kon keresztül. Létrehozhat riasztásokat is arra az esetre, ha bizonyos feltételek teljesülnek-e rajtuk.
### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Keresse meg az egyéni metrikákat az Azure Portalon
1.  Nyissa meg az [Azure Portal](https://portal.azure.com).
2.  Válassza ki a **figyelő** ablaktáblán.
3.  Válassza a **Metrika** lehetőséget.
4.  Válassza ki, hogy kibocsátott egyéni metrikákat az erőforrás.
5.  Válassza ki a metrikák névteret, az egyéni metrika.
6.  Válassza ki az egyéni metrika.

## <a name="supported-regions"></a>Támogatott régiók
A nyilvános előzetes során tegyenek közzé egyéni metrikákat csak egy Azure-régiók részhalmazát érhető el. Ez a korlátozás, az azt jelenti, hogy csak az erőforrások a támogatott régiók egyikében metrikák tehetők közzé. Az alábbi táblázat a támogatott Azure-régióban a egyéni metrikákat készletét. Felsorolja a megfelelő végpontok, amelyek ezekben a régiókban erőforrások mérőszámait közzé kell tenni:

|Azure-régió|Regionális végpont előtagja|
|---|---|
|USA keleti régiója|https://eastus.monitoring.azure.com/|
|USA déli középső régiója|https://southcentralus.monitoring.azure.com/|
|USA nyugati középső régiója|https://westcentralus.monitoring.azure.com/|
|USA nyugati régiója, 2.|https://westus2.monitoring.azure.com/|
|Délkelet-Ázsia|https://southeastasia.monitoring.azure.com/|
|Észak-Európa|https://northeurope.monitoring.azure.com/|
|Nyugat-Európa|https://westeurope.monitoring.azure.com/|

## <a name="quotas-and-limits"></a>Kvóták és korlátok
Az Azure Monitor ír elő a következő használati korlátokkal, egyéni metrikákra vonatkozóan:

|Kategória|Korlát|
|---|---|
|Adatsorozat/előfizetés/régió aktív idő|50,000|
|Dimenzió kulcsai metrikánként|10|
|Karakterlánc hosszának metrika névterek, a metrikák nevei, a dimenzió kulcsai és a dimenzió értékei|maximális hossz 256 karakter|
Egy aktív idősorozat számít, ha bármelyik metrika, a dimenzió kulcsot vagy a dimenzió értékét, amely még az elmúlt 12 órában közzétett metrikaértékek egyedi kombinációja.

## <a name="next-steps"></a>További lépések
Egyéni metrikák a különböző szolgáltatásokat használja: 
 - [Virtuális gépek](collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Virtuálisgép-méretezési csoportot](collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Azure-beli virtuális gépek (klasszikus)](collect-custom-metrics-guestos-vm-classic.md)
 - [Linux rendszerű virtuális gép Telegraf ügynök használatával](collect-custom-metrics-linux-telegraf.md)
 - [REST API](../../azure-monitor/platform/metrics-store-custom-rest-api.md)
 - [Klasszikus Cloud Services](collect-custom-metrics-guestos-vm-cloud-service-classic.md)
 