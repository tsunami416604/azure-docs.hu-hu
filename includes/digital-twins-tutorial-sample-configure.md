---
author: baanders
description: fájl belefoglalása az Azure Digital Twins-oktatóanyagokhoz – a minta projekt konfigurálása
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 4ac748c606d8ec3c8ba754c34d9c9e7512344a83
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91292682"
---
## <a name="configure-the-sample-project"></a>A minta projekt konfigurálása

Ezután állítson be egy példa ügyfélalkalmazás-alkalmazást, amely az Azure Digital Twins-példánnyal fog működni.

Navigáljon a gépre az [*Azure Digital Twins-mintákból*](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples) korábban letöltött fájlra (és ha még nem tette meg, csomagolja ki).

A mappa belsejében navigáljon a _AdtSampleApp_. Nyissa meg a _**AdtE2ESample. SLN**_ alkalmazást a Visual Studio 2019-ben. 

A Visual Studióban a *megoldáskezelő* ablaktáblán hozza létre a _SampleClientApp >serviceConfig.jsmásolatát **. SABLONFÁJL** _ (a másoláshoz és a beillesztéshez használhatja a jobb gombbal kiválasztható menüket). Nevezze át a *serviceConfig.js*másolását. Ez egy előre beállított JSON-fájlként fog szolgálni a projekt futtatásához szükséges konfigurációs változók használatával.

Válassza ki a fájl *serviceConfig.jsét* , és nyissa meg a szerkesztési ablakban. Módosítsa az `tenantId` azonosítót a *címtár-azonosítóra*, az `clientId` alkalmazás- *azonosítóra*, valamint az `instanceUrl` Azure Digital Twins instance *hostName* URL-címére (a *https://* az alább látható módon).

```json
{
  "tenantId": "<your-directory-ID>",
  "clientId": "<your-application-ID>",
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```



Mentse és zárja be a fájlt. 

Ezután konfigurálja a fájl *serviceConfig.jsa* kimeneti könyvtárba a *SampleClientApp*létrehozásakor. Ehhez kattintson a jobb gombbal a *serviceConfig.js* fájlra, majd válassza a *Tulajdonságok elemet.* A *Tulajdonságok* ellenőrben módosítsa a *Másolás a kimeneti könyvtárba* tulajdonság értékét, *Ha újabb*.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Részletek a Visual Studio ablakból: a Megoldáskezelő panel, amelyen a Kiemelt serviceConfig.jslátható, a Tulajdonságok ablaktábla pedig a másolás a kimeneti könyvtárba tulajdonsága a másolás, ha újabb értékre van állítva." border="false":::

Tartsa meg a _**AdtE2ESample**_ projektet a Visual Studióban, és folytassa a használatát az oktatóanyagban.

