---
title: Egy létező ajánlat frissítése az Azure Marketplace-en
description: Egy létező ajánlat frissítése az Azure Marketplace-en
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5633392bdf1293ee9196fafe67cf901e0d8c8014
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809809"
---
<a name="update-an-existing-offer-for-azure-marketplace"></a>Egy létező ajánlat frissítése az Azure Marketplace-en 
==============================================

Számos különböző típusú érdemes tennie ajánlatát amint élő frissítéseket.

1.  Új \"csomag\" egy meglévő termékváltozatra
2.  Új termékváltozatok hozzáadása egy meglévő ajánlatra
3.  Marketplace-en metaadatainak ajánlat/SKU frissítése

Szeretné frissíteni az ajánlatot a Cloud Partner portálra, és tegye közzé újra. Ez a cikk végigvezeti az Azure-alkalmazás ajánlat frissítése a különböző aspektusait.

<a name="unpermitted-changes-to-azure-application-offersku"></a>Azure-alkalmazás ajánlat/SKU engedett módosításai 
--------------------------------------------------

Nincsenek attribútumai egy Azure-alkalmazás ajánlat/SKU, amely után az ajánlat az Azure Marketplace-en élesíti nem lehet módosítani.

1.  Ajánlat azonosítója és az ajánlat Gyártóazonosítója.
2.  Meglévő termékváltozatok SKU-azonosítója.
3.  Közzétett csomag frissítése.

<a name="adding-a-new-package-to-an-existing-sku"></a>Új csomag hozzáadása egy meglévő Termékváltozat 
---------------------------------------

A közzétevő szeretné hozzáadni a csomag új verziója egy meglévő csomag frissítéséhez. Ezt megteheti egy új csomagot egy másik verziószámmal feltöltésével.

1.  Jelentkezzen be a [Cloud Partner Portal](http://cloudpartner.azure.com)
2.  Minden ajánlat keresse meg, amelyet szeretne frissíteni az ajánlatot
3.  Az SKU-k űrlapja, kattintson a termékváltozat akik\'kellett szeretne frissíteni s csomag
4.  Kattintson a \"új csomag\" , és adja meg az új verzió
5.  A frissített sablon fájlt tartalmazó új zip-fájl feltöltése
6.  Kattintson a közzététel felhőplatformos termékeiért az új Termékváltozat az élő esemény indításra van a közzététel a munkafolyamatot.

<a name="adding-a-new-sku-to-an-existing-offer"></a>Egy új Termékváltozatban hozzáadása egy meglévő ajánlatra
-------------------------------------

Dönthet, hogy egy új Termékváltozatban érhető el a létező ajánlat. Ennek engedélyezéséhez kövesse az alábbi lépéseket.

1.  Jelentkezzen be a [Cloud Partner Portal](http://cloudpartner.azure.com)
2.  Minden ajánlat keresse meg, amelyet szeretne frissíteni az ajánlatot
3.  A termékváltozatokban alkotnak, hozzáadása új termékváltozat clik, és adja meg a Termékváltozat azonosítója, az előugró.
4.  A többi megadott lépéseket követve [Itt](./cloud-partner-portal-managed-app-publish.md).
5.  Kattintson a közzététel felhőplatformos termékeiért az új Termékváltozat az élő esemény indításra van a közzététel a munkafolyamatot.

<a name="updating-offer-marketplace-metadata"></a>A Piactéri ajánlat metaadatok frissítése 
-----------------------------------

Előfordulhat, hogy a forgatókönyvek, ahol frissítenie kell az ajánlat például frissítheti a vállalati emblémát, és így tovább társított marketplace metaadatokat. Kövesse az alábbi lépéseket.

1.  Jelentkezzen be a Cloud Partner portálra
2.  Minden ajánlat keresse meg, amelyet szeretne frissíteni az ajánlatot
3.  Ugrás a Marketplace-en alkotnak, és kövesse az utasításokat [Itt](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-push-to-staging) módosításokat.
4.  Kattintson a közzététel indíthat a módosításokat az élő esemény indításra van a közzététel a munkafolyamatot.
