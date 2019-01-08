---
title: Hozzon létre a Dynamics 365 Customer Engagement technikai eszközök – Azure Marketplace-en |} A Microsoft Docs
description: A technikai eszközök létrehozása a Dynamics 365 Customer Engagement-alkalmazás ajánlat.
services: Dynamics 365 for Customer Engagement, Azure, Marketplace, Cloud Partner Portal, AppSource
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 12/29/2018
ms.author: pbutlerm
ms.openlocfilehash: 50c4fd512206cdf17ebb555acb88de2a3f74c2bd
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54082978"
---
# <a name="create-technical-assets-for-azure-application-offer"></a>Technikai eszközök ajánlat Azure-alkalmazás létrehozása

Megoldások fejlesztését általában a [készült Dynamics 365 Customer Engagement-alkalmazások SDK](https://docs.microsoft.com/dynamics365/customer-engagement/developer/get-started-sdk).  Megoldások igénybe az űrlapot, különböző leírtak szerint [programozási modellek a Dynamics 365 for Customer Engagement alkalmazásokat](https://docs.microsoft.com/dynamics365/customer-engagement/developer/programming-models).  Válassza a képernyő, amely a legjobban megfelel a megoldás követelményeinek.  Amikor a megoldás kifejlesztése számos meg kell oldania, például a bővítési lehetőségek, a megoldás-összetevőket és a verziókompatibilitás problémák.  További információkért lásd: [Bevezetés a megoldásokba](https://docs.microsoft.com/dynamics365/customer-engagement/developer/introduction-solutions).

Az appsource-on közzétett Dynamics 365-megoldások többsége terjesztett csomag fájljait, és a felügyelt alkalmazások.


## <a name="creating-and-storing-the-package"></a>Létrehozás és a csomag tárolásához

A szakaszban található létrehozása a Dynamics 365 for Customer Engagement számos lehetőséget kínál a párhuzamos dokumentáció [tegye közzé alkalmazását az appsource-on](https://docs.microsoft.com/dynamics365/customer-engagement/developer/publish-app-appsource).  A következő található témakörök részletes a megoldás alkalmazáscsomag-fájl létrehozása, és töltse fel az Azure storage:

- [4. lépés: Az alkalmazás az appsource-ban csomag létrehozása](https://docs.microsoft.com/dynamics365/customer-engagement/developer/create-package-app-appsource) -azt ismerteti, hogyan hozzon létre egy tömörített (zip) fájlt, amely a felügyelt alkalmazást, és tartalmazza: eszközök megoldásmappára, egyéni kód DLL, MIME-típus információs fájl, az AppSource-csomag ikonja, licenc feltételek (HTML) fájlt, és a tartalom fájl (XML).
- [5. lépés: Az appsource-ban csomag Store az Azure Storage és a egy URL-cím létrehozhat egy SAS-kulcsának](https://docs.microsoft.com/dynamics365/customer-engagement/developer/store-appsource-package-azure-storage) – ismerteti az appsource-ban alkalmazáscsomag-fájl tárolása a Microsoft Azure Blob storage-fiók, és az alkalmazáscsomag-fájl megosztása egy közös hozzáférésű Jogosultságkód (SAS) kulcs segítségével. Az alkalmazáscsomag-fájl veszi át az Azure tárolási hely minősítésre, majd az AppSource kísérletek és közzététele.


## <a name="next-steps"></a>További lépések

Ha Ön még nem tette, [létrehozása a Dynamics 365 Customer Engagement ajánlat](./cpp-create-offer.md).  Készen áll a fog [az ajánlat közzététele](./cpp-publish-offer.md).
