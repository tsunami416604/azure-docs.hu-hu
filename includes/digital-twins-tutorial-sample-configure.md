---
author: baanders
description: fájl belefoglalása az Azure Digital Twins-oktatóanyagokhoz – a minta projekt konfigurálása
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: d442c5782af1f55cd5d894823945d517dbc48091
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92323010"
---
## <a name="configure-the-sample-project"></a>A minta projekt konfigurálása

Ezután állítson be egy példa ügyfélalkalmazás-alkalmazást, amely az Azure Digital Twins-példánnyal fog működni.

Navigáljon a gépre az [*Azure Digital Twins teljes körű mintáit*](/samples/azure-samples/digital-twins-samples/digital-twins-samples) korábban letöltött fájlra (és ha még nem tette meg, bontsa ki).

A mappa belsejében navigáljon a _AdtSampleApp_. Nyissa meg a _**AdtE2ESample. SLN**_ alkalmazást a Visual Studio 2019-ben. 

A Visual Studióban a *megoldáskezelő* ablaktáblán hozza létre a _SampleClientApp >serviceConfig.jsmásolatát **. SABLONFÁJL** _ (a másoláshoz és a beillesztéshez használhatja a jobb gombbal kiválasztható menüket). Nevezze át a *serviceConfig.js*másolását. Ez egy előre beállított JSON-fájlként fog szolgálni a projekt futtatásához szükséges konfigurációs változók használatával.

Válassza ki a fájl *serviceConfig.jsét* , és nyissa meg a szerkesztési ablakban. Módosítsa az `instanceUrl` Azure-beli digitális Twins-példány *állomásnév* -URL-címét (a *https://* az alább látható módon).

```json
{
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```



Mentse és zárja be a fájlt. 

Ezután konfigurálja a fájl *serviceConfig.jsa* kimeneti könyvtárba a *SampleClientApp*létrehozásakor. Ehhez kattintson a jobb gombbal a *serviceConfig.js* fájlra, majd válassza a *Tulajdonságok elemet.* A *Tulajdonságok* ellenőrben módosítsa a *Másolás a kimeneti könyvtárba* tulajdonság értékét, *Ha újabb*.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Részletek a Visual Studio ablakból: a Megoldáskezelő panel, amelyen a Kiemelt serviceConfig.jslátható, a Tulajdonságok ablaktábla pedig a &quot;másolás a kimeneti könyvtárba&quot; tulajdonsága a &quot;másolás, ha újabb&quot; értékre van állítva." border="false":::

Tartsa meg a _**AdtE2ESample**_ projektet a Visual Studióban, és folytassa a használatát az oktatóanyagban.

