---
author: baanders
description: fájl belefoglalása az Azure Digital Twins-oktatóanyagokhoz – a minta projekt konfigurálása
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: f7b3bf347b15ebad2403f3aa64c0f3fd6c4be052
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613535"
---
## <a name="configure-the-sample-project"></a>A minta projekt konfigurálása

Ezután állítson be egy példa ügyfélalkalmazás-alkalmazást, amely az Azure Digital Twins-példánnyal fog működni. Ha még nem töltötte le a minta projektet, töltse le most [Az Azure Digital ikrek Samples REPOSITORY zip-fájlként való letöltésével](https://github.com/Azure-Samples/digital-twins-samples/archive/master.zip). 

Navigáljon a letöltött fájlhoz a gépen, és csomagolja ki.

A kibontott mappán belül navigáljon a _Digital-Twins-Samples-Master/AdtSampleApp/_. Nyissa meg a _**AdtE2ESample. SLN**_ alkalmazást a Visual Studio 2019-ben. 

A Visual Studióban a *megoldáskezelő* ablaktáblán hozza létre a _SampleClientApp-> **SERVICECONFIG. JSON. template** _ fájl másolatát (a másoláshoz és beillesztéshez a jobb gombbal válassza a menüket). Nevezze át a copy *serviceConfig. JSON*fájlt. Ez egy előre beállított JSON-fájlként fog szolgálni a projekt futtatásához szükséges konfigurációs változók használatával.

Válassza ki az új fájlt, és nyissa meg a szerkesztési ablakban. Módosítsa az `tenantId` azonosítót a *címtár-azonosítóra*, az `clientId` alkalmazás- *azonosítóra*, valamint az `instanceUrl` Azure Digital Twins instance *hostName* URL-címére (a *https://* az alább látható módon).

```json
{
  "tenantId": "<your-directory-ID>",
  "clientId": "<your-application-ID>",
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Mentse és zárja be a fájlt. 

Ezután konfigurálja a *serviceConfig. JSON* fájlt, amelyet a kimeneti könyvtárba kell másolni a *SampleClientApp*létrehozásakor. Ehhez kattintson a jobb gombbal a *serviceConfig. JSON* fájlra, és válassza a *Tulajdonságok lehetőséget.* A *Tulajdonságok* ellenőrben módosítsa a *Másolás a kimeneti könyvtárba* tulajdonság értékét, *Ha újabb*.

:::image type="content" source="../articles/digital-twins/media/include-tutorial/copy-config.png" alt-text="A Visual Studio ablakának kivonata, amely a serviceConfig. JSON kiterjesztésű Megoldáskezelő ablaktáblát jeleníti meg, a Tulajdonságok ablaktáblában pedig a "másolás a kimeneti könyvtárba" tulajdonságot a "Copy if újabb" értékre." border="false":::

Tartsa meg a _**AdtE2ESample**_ projektet a Visual Studióban, és folytassa a használatát az oktatóanyagban.

