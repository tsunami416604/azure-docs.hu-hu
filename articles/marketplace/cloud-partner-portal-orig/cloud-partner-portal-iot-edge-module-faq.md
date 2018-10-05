---
title: Az IoT Edge-modul piactér – gyakori kérdések |} A Microsoft Docs
description: Az IoT Edge-modul piactér – gyakori kérdések.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 2c2e7729eb70a5dd37dc4df10605eec9429e1043
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809769"
---
# <a name="iot-edge-module-marketplace-frequently-asked-questions"></a>Az IoT Edge-modul piactér – gyakori kérdések


## <a name="what-is-the-edge-module-marketplace"></a>Mi az az Edge-modul Marketplace-en?


Az IoT Edge modul Marketplace-en az listája *certified* előre elkészített az Edge-modulok, amelyek *felderíthető* az Azure piactéren.

![IoT Edge-modulok](./media/cloud-partner-portal-iot-edge-module-faq/iot-edge-modules.png)

## <a name="where-will-edge-modules-be-visible"></a>Ahol Edge-modulok lesznek láthatók? 


Az a [az Azure portal marketplace](https://ms.portal.azure.com/) (hitelesített felhasználói élményt), az eszközök internetes hálózata kategóriához tartozó "IoT Edge-modul" címkével.

Majd a a [az Azure piactér webhelyén](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/internet-of-things?page=1) (névtelen élmény) az eszközök internetes hálózata kategóriához tartozó "IoT Edge-modul" címkével.

## <a name="is-it-open-to-partners"></a>Ennyi az egész nyissa meg a partnerek számára?


még nem. Az IoT Edge szakaszban, a Piactér jelenleg csak a Microsoft által készített modulok tesszük közzé. 

## <a name="why-should-partners-publish-their-iot-edge-modules"></a>Miért kell közzétenniük, partnerek az IoT Edge-modulok?


-   Növelheti a termékláthatósági ezen a csatornán hozzáadásával piacra, és amely a megoldásaikat.

-   További érdeklődők beolvasása. Az Azure Marketplace-en részeként ki érdekli a megoldás részleteit is kapnak.

-   Hogy az elsők között több bevételt képességeit kihasználva.

## <a name="what-is-the-onboarding-process"></a>Mi az a bevezetési folyamat?


Amíg nem még megnyitva, a bevezetési folyamat az Azure Marketplace-en keresztül történik. A rendszer részletes útmutatásokat a [közzétételi útmutató az Azure Marketplace és AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide). Tekintse meg a transact-tárolók listájának típusa. 

Partnerek először az Azure piactér kiadói válik. Ők is látni fogják a munkába, majd az Edge-modulok keresztül a [Cloud Partner Portalon](./cloud-partner-portal-getting-started-with-the-cloud-partner-portal.md).

## <a name="are-there-any-monetization-capabilities"></a>Vannak-e bármilyen bevételszerzési lehetőségek?


Nem lehetőségeiből most. Első célunk, hogy nyissa meg a piactéren *ingyenes* vagy *saját licenc használata* modulok él. Bevételszerzés további funkciókat, például egy használatalapú számlázási modell adunk hozzá.

## <a name="what-is-bring-your-own-license-byol"></a>Mi a bring-your-saját licenc (használata BYOL)?


A hivatalos definíció, a [a Microsoft Azure Marketplace részvételi szabályzata](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) van:

- *Ügyfeleink az Azure piactéren kívül szerezheti be a jogot az ajánlat hozzáférési vagy használati, és nem terheli Azure Piactéri díj az ajánlat az Azure piactéren.*

A partnerek által licenc a szoftvereket és a bevétel összegyűjtése, van.

## <a name="can-partners-publish-a-freemium-module"></a>Közzéteheti a partnerek "freemium" modul?


Igen freemium modulokat, amelyek modullistából ingyenes, de bizonyos korlátozásokkal, mint bármely más kiadvány akkor minősül.

## <a name="is-intellectual-property-protected"></a>Szellemi tulajdon védelme?


Edge-modul Docker-kompatibilis tároló. A partnerek által védeni a szellemi tulajdonát (IP) az elosztott tárolókban csomagolt van.

## <a name="where-will-the-modules-be-hosted"></a>Ahol a modulok üzemeltetett?


IoT Edge-modulok egy Microsoft által birtokolt tároló-beállításjegyzéket, amelyekhez névtelenül lekérdezés-, például a Docker Hub-ban üzemeltetett alkalmazásban.

## <a name="what-are-the-integration-plans-between-the-azure-marketplace-and-the-azure-iot-tools"></a>Mik azok az integrációs csomagok az Azure Marketplace-en és az Azure IoT-eszközök között?

Között az Azure Marketplace-en és az Azure IoT-eszközeit szorosabban fog létrehozni. Mi is van tartva példák néhány tallózhat az IoT Edge-modul piactéren közvetlenül az IoT Hub-portálról, vagy közvetlenül a Visual Studio Code az engedélyezéséhez.

## <a name="which-operating-system-or-architecture-should-my-container-support"></a>Mely operációs rendszer vagy architektúra támogatnia kell a saját tároló?

IoT Edge-modulok támogatnia kell az ugyanazon operációs rendszer / architektúra mátrix IoT Edge, éles környezetben. Ez a lista [az Azure IoT Edge-támogatás](https://docs.microsoft.com/azure/iot-edge/support). Ha például egy modul jelenleg támogatnia kell Linux x64- és Linux ARM32.

## <a name="are-there-any-other-certification-constraints-to-be-aware-of"></a>Minden más hitelesítő korlátozást érdemes figyelembe vennie vannak?

Még dolgozunk a minősítési korlátozásokat pontos készletét. Az alapelvek a következők:

-   Minőségi elősegítő mennyiség keresztül.

-   IoT Edge-specifikus tárolók (nem véletlenszerű meglévőket).

-   Üzemi szintű.

-   1 kattintással (legalább egy alapértelmezett készletét a konfigurációs értékek biztosított).

## <a name="any-other-questions"></a>Vannak még kérdések?


Kapcsolattartó [az Azure IoT él, az előkészítési](mailto:azureiotedgeonboarding@service.microsoft.com).
