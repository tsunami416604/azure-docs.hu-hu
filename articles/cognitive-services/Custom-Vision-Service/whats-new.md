---
title: A Custom Vision újdonságai
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Custom Visionával kapcsolatos híreket tartalmaz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 10/23/2020
ms.author: pafarley
ms.openlocfilehash: 030b09dae9db11fb14defecde3d14e949b9e6748
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412731"
---
# <a name="whats-new-in-custom-vision"></a>A Custom Vision újdonságai

Ismerje meg a szolgáltatás újdonságait. Ezek az elemek kibocsátási megjegyzések, videók, blogbejegyzések és más típusú információk lehetnek. A lapon lévő könyvjelzővel naprakészen tarthatja a szolgáltatást.


## <a name="october-2020"></a>2020. október 

### <a name="custom-base-model"></a>Egyéni alapmodell

- Egyes alkalmazások nagy mennyiségű közös képzési adattal rendelkeznek, de a modelljeiket külön kell finomítani; Ez jobb teljesítményt eredményez a különböző forrásokból származó rendszerképeknél kisebb eltérésekkel. Ebben az esetben az első modellt a szokásos módon, nagy mennyiségű betanítási adattal is betaníthatja. Ezután hívja meg a **TrainProject** -t az 3,4 nyilvános előzetes API-ban, a kérés törzsében a _CustomBaseModelInfo_ használatával, hogy az első fázisban betanított modellt használja az alsóbb rétegbeli projektek alapmodellje. Ha a forrás-és az alárendelt célként megadott projekt hasonló rendszerképeket tartalmaz, akkor jobb teljesítményt várhat. 

### <a name="new-domain-information"></a>Új tartományi információk

- A Custom Vision 3,4 nyilvános előzetes verziójú API **GetDomains** által visszaadott tartományi információk mostantól támogatott exportálható platformokat, a modell architektúrájának rövid leírását, valamint a kompakt tartományokhoz tartozó modell méretét is tartalmazzák.

### <a name="training-divergence-feedback"></a>Az eltérési visszajelzések betanítása

- A Custom Vision Custom Vision 3,4 nyilvános előzetes API mostantól visszaadja a **TrainingErrorDetails** a **GetIteration** -hívásból. A sikertelen iterációk során ez azt mutatja, hogy a hibát a betanítási eltérés okozta-e, amely több és jobb minőségű betanítási adattal is orvosolható.

## <a name="july-2020"></a>2020. július

### <a name="azure-role-based-access-control"></a>Azure-beli szerepköralapú hozzáférés-vezérlés

* Custom Vision támogatja az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC), az Azure-erőforrásokhoz való egyéni hozzáférés kezelésére szolgáló engedélyezési rendszert. A Custom Vision-projektekhez való hozzáférés kezelésének megismeréséhez tekintse meg az [Azure szerepköralapú hozzáférés-vezérlés](./role-based-access-control.md)című témakört.

### <a name="subset-training"></a>A betanítás részhalmaza

* Egy objektum-észlelési projekt betanításakor igény szerint betaníthatja az alkalmazott címkék egy részhalmazát. Ezt akkor érdemes megtenni, ha még nem alkalmazta elég bizonyos címkéket, de másokkal is rendelkezik. További információért kövesse a C# vagy a Python [ügyféloldali kódtár](./quickstarts/object-detection.md) útmutatóját.

### <a name="azure-storage-notifications"></a>Azure Storage-értesítések

* A Custom Vision-projektet egy Azure Blob Storage-várólistával integrálva leküldéses értesítéseket kaphat a projekt betanítási/exportálási tevékenységéről és a közzétett modellek biztonsági másolatáról. Ez a funkció akkor hasznos, ha el szeretné kerülni a szolgáltatás folyamatos lekérdezését az eredményekhez, amikor a hosszú műveletek futnak. Ehelyett a tárolási üzenetsor-értesítéseket integrálhatja a munkafolyamatba. További információért lásd a [Storage integrációs](./storage-integration.md) útmutatóját.

### <a name="copy-and-move-projects"></a>Projektek másolása és áthelyezése

* Mostantól egy Custom Vision-fiókból is másolhat projekteket másokba. Érdemes lehet egy projektet a fejlesztésből éles környezetbe áthelyezni, vagy egy projektről egy másik Azure-régióban lévő fiókra biztonsági másolatot készíteni az adatbiztonság növelése érdekében. További információkért tekintse meg a [projektek másolása és áthelyezése](./copy-move-projects.md) című útmutatót.

## <a name="september-2019"></a>2019. szeptember

### <a name="suggested-tags"></a>Javasolt Címkék

* A [Custom Vision webhelyének](https://www.customvision.ai/) intelligens Labeler eszköze a betanítási lemezképekhez javasolt címkéket hoz létre. Ez lehetővé teszi, hogy a Custom Vision-modellek betanítása során gyorsabban címkézze fel a képek nagy számát. A funkció használatáról a következő témakörben talál útmutatást: [javasolt címkék](./suggested-tags.md).

## <a name="cognitive-service-updates"></a>A kognitív szolgáltatás frissítései

[Cognitive Services Azure Update-hirdetmények](https://azure.microsoft.com/updates/?product=cognitive-services)