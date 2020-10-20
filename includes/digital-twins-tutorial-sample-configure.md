---
author: baanders
description: fájl belefoglalása az Azure Digital Twins-oktatóanyagokhoz – a minta projekt konfigurálása
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 2a62cae025be05a8f5a6ef4407737ab8da36a951
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210776"
---
## <a name="configure-the-sample-project"></a>A minta projekt konfigurálása

Ezután állítson be egy példa ügyfélalkalmazás-alkalmazást, amely az Azure Digital Twins-példánnyal fog működni.

Navigáljon a gépre az [*Azure Digital Twins teljes körű mintáit*](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples) korábban letöltött fájlra (és ha még nem tette meg, bontsa ki).

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

