---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2019
ms.openlocfilehash: 5089af4a4e1714d49b844a1b6823487a3f6a8dcf
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74483021"
---
Kezdje el használni az Anomália-detektor szolgáltatást az alábbi Azure-erőforrások egyikének létrehozásával.

* <a href="https://azure.microsoft.com/try/cognitive-services/#decision" target="_blank" rel="noopener">Próba-erőforrás létrehozása (új lapon nyílik meg)</a>
    * Nincs szükség Azure-előfizetésre: 
    * Hét napig érvényes, ingyen. A regisztráció után egy próbakulcs és egy végpont lesz elérhető az [Azure webhelyén.](https://azure.microsoft.com/try/cognitive-services/my-apis/) 
    * Ez egy nagyszerű lehetőség, ha szeretné kipróbálni az Anomália-detektort, de nem rendelkezik Azure-előfizetéssel.

* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank" rel="noopener">Anomáliadetektor-erőforrás létrehozása (új lapon nyílik meg):</a>
    * Az Azure Portalon keresztül érhető el, amíg nem törli az erőforrást.
    * Az ingyenes tarifacsomag használatával próbálja ki a szolgáltatást, és frissítsen később egy fizetett szintre éles környezetben.



### <a name="create-an-environment-variable"></a>Környezeti változó létrehozása

>[!NOTE]
> július 1-je után létrehozott nem próbaerőforrások végpontjai az alábbi egyéni altartomány-formátumot használják. További információt és a regionális végpontok teljes listáját a [Cognitive Services egyéni altartománynevei című témakörben talál.](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains) 

A létrehozott erőforrás kulcsának és végpontjának használatával hozzon létre két környezeti változót a hitelesítéshez:

* `ANOMALY_DETECTOR_KEY`- A kérések hitelesítéséhez.
* `ANOMALY_DETECTOR_ENDPOINT`- Az erőforrás-végpont API-kérések küldéséhez. Így fog kinézni: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Használja az operációs rendszerre vonatkozó utasításokat.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx ANOMALY_DETECTOR_KEY <replace-with-your-anomaly-detector-key>
setx ANOMALY_DETECTOR_ENDPOINT <replace-with-your-anomaly-detector-endpoint>
```

A környezeti változó hozzáadása után indítsa újra a konzolablakot.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

A környezeti változó hozzáadását követően futtassa a `source ~/.bashrc` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

#### <a name="macos"></a>[Macos](#tab/unix)

A program `.bash_profile`szerkesztése és a környezeti változó hozzáadása:

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

A környezeti változó hozzáadását követően futtassa a `source .bash_profile` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

***