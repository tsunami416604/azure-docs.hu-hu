---
title: Értékesítői Insights kibocsátási megjegyzések |} A Microsoft Docs
description: Információt nyújt az értékesítői elemzés szolgáltatás módosításait.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: ec3784a91f8aeb7f0fedd13c9ab86a844832a578
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809702"
---
<a name="seller-insights-release-notes"></a>Értékesítői Insights kibocsátási megjegyzései 
===============================
(Kiadás dátuma: 2018. július 28.)

Ez a cikk ismertetése az értékesítői elemzés szolgáltatás a módosítások a [Cloud Partner Portalon](https://cloudpartner.azure.com/#insights).


<a name="release-highlights"></a>Kiadási emeli ki.
------------------

-   *Az árak becsült* felhasználói díjak nézetét a pénznem adja meg a konverziós következményeiről.
-   *Előrejelzett kifizetések* lehetséges kifizetések egy korábbi betekintést nyújtanak.
-  *Használati útmutató azonosítók* kifizetések adathűséget ügyfelek általi használatot és a rendelések közötti biztosít
-   *Napi bontásban használatát* több beállítási lehetőséget és nagyobb betekintést ügyfelek általi használatot biztosít.


<a name="changes-to-data-structure-and-taxonomy"></a>Adatok szerkezetét és besorolás módosítása
--------------------------------------

A következő táblázat felsorolja, hogy a hozzáadott vagy közel teljes értékesítése miatt ebben a kiadásban megváltozott. 

| **Új kifejezés**                   |    **Definíció**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| Ár (CC)                     | Egy adott termékváltozat (az ügyfél pénznemben) használati egység díja.       |
| Becsült kiterjesztett költség (CC) | A becsült használati egységek kiterjesztett díj (az ügyfél pénznemben) egy adott termékváltozat. Ez az érték nem lehet pontos kerekítési vagy csonkolási hiba miatt.   |
| Közzétevő pénznem (számítógép)        | A közzétevő küldenie jóváhagyásra cége kifizetési az előnyben részesített pénznem.                               |
| Becsült díj (számítógép)           | Egy adott Termékváltozat deviza átalakítás a dátum-használat alapján Usage egység becsült díj kiszámítása a (a közzétevő pénznem). Ez az érték nem lehet pontos kerekítési vagy csonkolási hiba miatt.   |
| Becsült kiterjesztett költség (számítógép) | Becsült kiterjesztett használati egységek számára egy adott termékváltozat deviza átalakítás a dátum-használat alapján számítjuk fel (a közzétevő pénznemben). Ez az érték nem lehet pontos kerekítési vagy csonkolási hiba miatt. |
| Becsült küldenie jóváhagyásra cége kifizetési (számítógép)          | Egységek száma a becsült fizetési egy adott termékváltozat használati deviza átalakítás azon a napon, a használati kiszámítása a (a közzétevő pénznem) alapján. Ez az érték nem lehet pontos kerekítési vagy csonkolási hiba miatt.   |
| Használati útmutató                | Egy vagy több napot az ügyfelek általi használatot a társított bejegyzés az küldenie jóváhagyásra cége kifizetési jelentés az adott Termékváltozat azonosítója. |
|  |  |
