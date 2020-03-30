---
title: Dynamics 365 megoldás előkészítése
description: Az összetevők csomagolásának, telepítésének és eltávolításának keretrendszere
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: ac1e4fa541e945f20904ced114a36b58d14585ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278586"
---
# <a name="dynamics-365-solution-preparation"></a>Dynamics 365 megoldás előkészítése

A Dynamics 365 megoldásrendszer olyan összetevők csomagolásának, telepítésének és eltávolításának keretrendszere, amelyek speciális üzleti funkciókat biztosítanak. A megoldásokat a független szoftverszállítók és más Microsoft Dynamics 365 partnerek használják az általuk létrehozott bővítmények terjesztésére.

Ha Ön már létező Dynamics 365 (xRM) független szoftverszoftver, valószínűleg már létrehozott egy felügyelt megoldást, és rendelkezik solution.zip fájllal. A megoldásban győződjön meg arról, hogy a "Megjelenítendő név" és a "Leírás" mezők tükrözik azt, amit az ügyfeleknek látniuk kell. Ezek a CRM Online Felügyeleti központban jelennek meg.

![CRMScreenShot1](media/CRMScreenShot1.png)

_**Megjegyzés:** A csomag példájában az alábbi példában azt feltételezzük, hogy a megoldás neve "SampleSolution.zip"_

Ha Ön új isv-szolgáltató, további részleteket kaphat a megoldás létrehozásáról itt:[https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Ha a megoldás alátámasztó adatokat igényel:

* A mintaadatok létrehozása a tesztkörnyezetben
* A Konfigurációs áttelepítés eszköz segítségével hozzon létre egy sémát az adatok összehasonlítási szabályaival.
* Mentse a konfigurációs sémát a projektfájlokkal. Erre később szüksége lesz, ha frissíti a konfigurációs adatokat.
* Exportálja a konfigurációs adatokat. Ügyeljen arra, hogy az exportfájlnak olyan nevet adjon, amely az exportálás számára jelentőségteljes.
