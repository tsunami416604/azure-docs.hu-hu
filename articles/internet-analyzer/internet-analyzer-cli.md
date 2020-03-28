---
title: Internetes elemzőteszt létrehozása cli használatával | Microsoft dokumentumok
description: Ebből a cikkből megtudhatja, hogyan hozhat létre az első Internet Analyzer tesztet.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: d474442086e2a114f26df279ab2682cd7628a5f5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74184271"
---
# <a name="create-an-internet-analyzer-test-using-cli-preview"></a>Internetes elemzőteszt létrehozása CLI (előzetes verzió) használatával

Az Internetes elemző erőforrás t két módon hozhat létre – az [Azure Portal használatával](internet-analyzer-create-test-portal.md) vagy a CLI használatával. Ez a szakasz segít létrehozni egy új Azure Internet Analyzer erőforrás thasználjuk cli tapasztalat. 


> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Előkészületek

A nyilvános előzetes verzió globálisan használható; azonban az adattárolás az *USA West 2-re* korlátozódik az előzetes verzió során.

## <a name="object-model"></a>Objektummodell
Az Internet Analyzer CLI a következő típusú erőforrásokat teszi elérhetővé:
* **Tesztek** – A teszt két internetvégpont (A és B) időbeli teljesítményét hasonlítja össze.
* **Profilok** – A tesztek internetes elemzőprofil alatt jönnek létre. A profilok lehetővé teszik a kapcsolódó vizsgálatok csoportosítását; egyetlen profil egy vagy több vizsgálatot tartalmazhat.
* **Előre konfigurált végpontok** – Különböző konfigurációkkal (régiók, gyorsítási technológiák stb.) állítottunk be végpontokat. Ezen előre konfigurált végpontok bármelyikét használhatja a tesztekben.
* **Scorecardok** – A scorecard gyors és értelmezhető összegzést ad a mérési eredményekről. Lásd [a scorecard értelmezése](internet-analyzer-scorecard.md).
* **Idősorozat** – Az idősorozat azt mutatja be, hogyan változik a metrika az idő múlásával.

## <a name="profile-and-test-creation"></a>Profil és teszt létrehozása
1. Az Internet Analyzer előzetes hozzáférése a **Hogyan vehetek részt az előzetes verzióban?** [Azure Internet Analyzer FAQ](internet-analyzer-faq.md)
2. [Telepítse az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
3. Cli-munkamenet indításához futtassa a `login` parancsot:
    ```azurecli-interactive
    az login
    ```

    Ha a CLI meg tudja nyitni az alapértelmezett böngészőt, akkor ezt megteszi, és betölt egy Azure-bejelentkezési lapot.
    Ellenkező esetben nyisson https://aka.ms/devicelogin meg egy böngészőlapot a terminálon, és adja meg az engedélyezési kódot.

4. A böngészőben jelentkezzen be fiókja hitelesítő adataival.

5. Válassza ki az előfizetési azonosítót, amely hozzáférést kapott az Internet Analyzer nyilvános előzetes verziójához.

    A bejelentkezés után megjelenik az Azure-fiókjához társított előfizetések listája. Az előfizetési `isDefault: true` adatok a bejelentkezés után az aktuálisan aktivált előfizetés. Másik előfizetés kiválasztásához használja az [az-fiókkészlet parancsot](https://docs.microsoft.com/cli/azure/account#az-account-set) az előfizetés-azonosítóval, amelyre váltani szeretne. Az előfizetések kiválasztásáról további információt a [Több Azure-előfizetés használata című témakörben talál.](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)

    Nem interaktív módok is rendelkezésre állnak a bejelentkezéshez, amelyekről részletesen [az Azure CLI-vel történő bejelentkezést](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) ismertető részben olvashat.

6. **[Nem kötelező]** Hozzon létre egy új Azure-erőforráscsoportot:
    ```azurecli-interactive
    az group create --location eastus --name "MyInternetAnalyzerResourceGroup"
    ```

7. Az Azure CLI Internet Analyzer extension telepítése:
     ```azurecli-interactive
    az extension add --name internet-analyzer
    ```

8. Új Internet Analyzer profil létrehozása:
    ```azurecli-interactive
    az internet-analyzer profile create --location eastus --resource-group "MyInternetAnalyzerResourceGroup" --name "MyInternetAnalyzerProfile" --enabled-state Enabled
    ```

9. Az újonnan létrehozott profil számára elérhető összes előre konfigurált végpont felsorolása:
    ```azurecli-interactive
    az internet-analyzer preconfigured-endpoint list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

10. Hozzon létre egy új tesztet az újonnan létrehozott InternetAnalyzer profil alatt:
    ```azurecli-interactive
    az internet-analyzer test create --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --endpoint-a-name "contoso" --endpoint-a-endpoint "www.contoso.com/some/path/to/trans.gif" --endpoint-b-name "microsoft" --endpoint-b-endpoint "www.microsoft.com/another/path/to/trans.gif" --name "MyFirstInternetAnalyzerTest" --enabled-state Enabled
    ```

    A fenti parancs feltételezi, hogy mindkettő, `www.contoso.com` és `www.microsoft.com` az egyéni elérési utak alatt az egy pixeles képet ([trans.gif)](https://fpc.msedge.net/apc/trans.gif)tartalmazza. Ha egy objektum elérési útja nincs megadva explicit `/apc/trans.gif` módon, az Internet Analyzer alapértelmezés szerint objektumelérési útként fog használni, ahol az előre beállított végpontok az egyképpontos képet üzemeltetik. Azt is vegye figyelembe, hogy a sémát (https/http) nem kell megadni; Az Internet Analyzer csak https-végpontokat támogat, így a HTTPS-t feltételezi.

11. Az új tesztnek az Internet Analyzer profil alatt kell megjelennie:
    ```azurecli-interactive
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

    Példa a kimenetre:
    ````
    [
        {
            "description": null,
            "enabledState": "Enabled",
            "endpointA": {
            "endpoint": "www.contoso.com/some/path/to/1k.jpg",
            "name": "contoso"
            },
            "endpointB": {
            "endpoint": "www.microsoft.com/another/path/to/1k.jpg",
            "name": "microsoft"
            },
            "id": "/subscriptions/faa9ddd0-9137-4659-99b7-cdc55a953342/resourcegroups/MyInternetAnalyzerResourceGroup/providers/Microsoft.Network/networkexperimentprofiles/MyInternetAnalyzerProfile/experiments/MyFirstInternetAnalyzerTest",
            "location": null,
            "name": "MyFirstInternetAnalyzerTest",
            "resourceGroup": "MyInternetAnalyzerResourceGroup",
            "resourceState": "Enabled",
            "scriptFileUri": "https://fpc.msedge.net/client/v2/d8c6fc64238d464c882cee4a310898b2/ab.min.js",
            "status": "Created",
            "tags": null,
            "type": "Microsoft.Network/networkexperimentprofiles/experiments"
        }
    ]
    ````

12. A mérések létrehozásának megkezdéséhez a teszt **parancsfájlja** által mutatott JavaScript-fájlt be kell ágyazni a webalkalmazásba. A konkrét utasítások az [Internet Analyzer Client beágyazása](internet-analyzer-embed-client.md) lapon találhatók.

13. Figyelemmel kísérheti a teszt előrehaladását az "állapot" értékének nyomon követésével:
    ```azurecli-interactive
    az internet-analyzer test show --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest"
    ```

14. A teszt összegyűjtött eredményeit idősorok vagy scorecardok létrehozásával vizsgálhatja meg:
    ```azurecli-interactive
    az internet-analyzer show-scorecard --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Daily" --end-date-time-utc "2019-10-24T00:00:00"
    ```

    ```azurecli-interactive
    az internet-analyzer show-timeseries --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Hourly" --start-date-time-utc "2019-10-23T00:00:00" --end-date-time-utc "2019-10-24T00:00:00" --timeseries-type MeasurementCounts
    ```


## <a name="next-steps"></a>További lépések

* A támogatott parancsok és használati példák teljes listájának tallózása az [Internet Analyzer CLI hivatkozásában.](https://docs.microsoft.com/cli/azure/ext/internet-analyzer/internet-analyzer?view=azure-cli-latest)
* Olvassa el az [Internet Analyzer GYIK](internet-analyzer-faq.md).
* További információ az [Internet Analyzer Client](internet-analyzer-embed-client.md) beágyazásáról és [az egyéni végpont létrehozásáról.](internet-analyzer-custom-endpoint.md) 
