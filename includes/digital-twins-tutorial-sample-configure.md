---
author: baanders
description: fájl belefoglalása az Azure Digital Twins-oktatóanyagokhoz – a minta projekt konfigurálása
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: ad9eb95c9e3a21cd33a367cc864a4d791888afb9
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87124558"
---
## <a name="configure-the-sample-project"></a>A minta projekt konfigurálása

Ezután állítson be egy példa ügyfélalkalmazás-alkalmazást, amely az Azure Digital Twins-példánnyal fog működni. Ha még nem töltötte le a minta projektet, szerezze be most az [*Azure Digital ikrek Samples*](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples) kezdőlapján, ehhez válassza a cím alatt található *zip letöltése* gombot.

Navigáljon a letöltött fájlhoz a gépen, és csomagolja ki.

A kibontott mappán belül navigáljon a _AdtSampleApp/_. Nyissa meg a _**AdtE2ESample. SLN**_ alkalmazást a Visual Studio 2019-ben. 

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

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Részletek a Visual Studio ablakból: a Megoldáskezelő panel, amelyen a Kiemelt serviceConfig.jslátható, a Tulajdonságok ablaktábla pedig a "másolás a kimeneti könyvtárba" tulajdonsága a "másolás, ha újabb" értékre van állítva." border="false":::

Tartsa meg a _**AdtE2ESample**_ projektet a Visual Studióban, és folytassa a használatát az oktatóanyagban.

