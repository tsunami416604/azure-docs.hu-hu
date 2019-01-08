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
ms.openlocfilehash: 2bfa10441cf5531c9383527a21b033da26322b34
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54073238"
---
<a name="update-an-existing-offer-for-azure-marketplace"></a>Egy létező ajánlat frissítése az Azure Marketplace-en 
==============================================

Nincsenek frissítések, amelyek érdemes tennie ajánlatát amint élő, például a különféle:

1.  Új \"csomag\" egy meglévő termékváltozatra
2.  Új termékváltozatok hozzáadása egy meglévő ajánlatra
3.  Marketplace-en metaadatainak ajánlat/SKU frissítése

Használja a Cloud Partner portálra ezen módosítások végrehajtásához lesz, majd közzé az ajánlatot még. Ez a cikk végigvezeti az Azure-alkalmazás ajánlat frissítése a különböző aspektusait.

<a name="unpermitted-changes-to-azure-application-offer"></a>Az Azure application ajánlat engedett módosításai 
-----------------------------------------------

Nincsenek attribútumai egy Azure-alkalmazás ajánlat/SKU, amely után az ajánlat az Azure Marketplace-en élesíti nem lehet módosítani.

* Ajánlat azonosítója és az ajánlat Gyártóazonosítója.
* Meglévő termékváltozatok SKU-azonosítója.
* Közzétett csomag frissítése.

<a name="adding-a-new-package-to-an-existing-sku"></a>Új csomag hozzáadása egy meglévő Termékváltozat 
---------------------------------------

A közzétevő szeretné hozzáadni a csomag új verziója egy meglévő csomag frissítéséhez. A Hozzáadás teheti meg, fel kell töltenie egy új csomagot egy másik verziószámmal.

1.  Jelentkezzen be a [Cloud Partner Portal](http://cloudpartner.azure.com)
2.  A **minden ajánlat**, keresse meg, amelyet szeretne frissíteni az ajánlatot
3.  Az a **termékváltozatok** alkotnak, kattintson a Termékváltozat a akik\'kellett szeretne frissíteni s csomag
4.  Kattintson a \"új csomag\" , és adja meg az új verzió
5.  A frissített sablon fájlt tartalmazó új zip-fájl feltöltése
6.  Kattintson a közzététel felhőplatformos termékeiért az új Termékváltozat az élő esemény indításra van a közzététel a munkafolyamatot.

<a name="adding-a-new-sku-to-an-existing-offer"></a>Egy új Termékváltozatban hozzáadása egy meglévő ajánlatra
-------------------------------------

Kiválaszthatja, hogy egy új Termékváltozatban érhető el a létező ajánlat az alábbi lépéseket:

1.  Jelentkezzen be a [Cloud Partner Portal](http://cloudpartner.azure.com)
2.  A **minden ajánlat**, keresse meg, amelyet szeretne frissíteni az ajánlatot
3.  Az a **termékváltozatok** alkotnak, kattintson a Hozzáadás, új Termékváltozat, és adja meg a Termékváltozat azonosítója, az előugró.
4.  A többi megadott lépéseket követve [Itt](./cloud-partner-portal-managed-app-publish.md).
5.  Kattintson a **közzététel** megkezdéséhez az új Termékváltozat az élő esemény indításra van a közzététel a munkafolyamatot.

<a name="updating-offer-marketplace-metadata"></a>A Piactéri ajánlat metaadatok frissítése 
-----------------------------------

Előfordulhat, hogy a forgatókönyvek, ahol frissítenie kell az ajánlat például frissítheti a vállalati emblémát, és így tovább társított marketplace metaadatokat. Kövesse az alábbi lépéseket.

1.  Jelentkezzen be a Cloud Partner portálra.
2.  A **minden ajánlat**, keresse meg, amelyet szeretne frissíteni az ajánlatot
3.  Ugrás a Marketplace-en alkotnak, és kövesse az utasításokat [Itt](../cloud-partner-portal/azure-applications/cpp-marketplace-tab.md) módosításokat.
4.  Kattintson a közzététel indíthat a módosításokat az élő esemény indításra van a közzététel a munkafolyamatot.
