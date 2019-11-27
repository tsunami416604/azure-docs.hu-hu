---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2019
ms.openlocfilehash: 5089af4a4e1714d49b844a1b6823487a3f6a8dcf
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483021"
---
Az alábbi Azure-erőforrások egyikének létrehozásával kezdje el használni az anomália-detektor szolgáltatást.

* <a href="https://azure.microsoft.com/try/cognitive-services/#decision" target="_blank" rel="noopener">Próbaverziós erőforrás létrehozása (új lapon nyílik meg)</a>
    * Nincs szükség Azure-előfizetésre: 
    * Hét napig ingyenesen használható. A regisztrációt követően a próbaverziós kulcs és a végpont elérhető lesz az [Azure webhelyén](https://azure.microsoft.com/try/cognitive-services/my-apis/). 
    * Ez nagyszerű megoldás, ha ki szeretné próbálni a anomália-detektort, de nem rendelkezik Azure-előfizetéssel.

* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank" rel="noopener">Anomália-detektor erőforrás létrehozása (új lapon nyílik meg)</a>:
    * A Azure Portalon keresztül érhető el, amíg nem törli az erőforrást.
    * Az ingyenes díjszabási csomaggal próbálja ki a szolgáltatást, és később frissítsen egy fizetős szintre az éles környezetben.



### <a name="create-an-environment-variable"></a>Környezeti változó létrehozása

>[!NOTE]
> Az 2019. július 1. után létrehozott, nem próbaverziós erőforrásokhoz használt végpontok az alább látható egyéni altartomány-formátumot használják. További információk és a regionális végpontok teljes listája: [Cognitive Services egyéni altartománynevei nevei](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Ha a kulcsot és a végpontot a létrehozott erőforrás alapján hozza létre, hozzon létre két környezeti változót a hitelesítéshez:

* `ANOMALY_DETECTOR_KEY` – a kérések hitelesítéséhez használt erőforrás-kulcs.
* `ANOMALY_DETECTOR_ENDPOINT` – az erőforrás-végpont API-kérelmek küldéséhez. A következőképpen fog kinézni: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Használja az operációs rendszerének utasításait.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx ANOMALY_DETECTOR_KEY <replace-with-your-anomaly-detector-key>
setx ANOMALY_DETECTOR_ENDPOINT <replace-with-your-anomaly-detector-endpoint>
```

A környezeti változó hozzáadása után indítsa újra a konzolablak ablakát.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

A környezeti változó hozzáadását követően futtassa a `source ~/.bashrc` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Szerkessze `.bash_profile`, és adja hozzá a környezeti változót:

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

A környezeti változó hozzáadását követően futtassa a `source .bash_profile` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

***