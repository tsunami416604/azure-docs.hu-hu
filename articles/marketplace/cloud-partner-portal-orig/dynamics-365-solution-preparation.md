---
title: Dynamics 365-megoldás előkészítése
description: Az összetevők csomagolására, telepítésére és eltávolítására szolgáló keretrendszer
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: ac1e4fa541e945f20904ced114a36b58d14585ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80278586"
---
# <a name="dynamics-365-solution-preparation"></a>Dynamics 365-megoldás előkészítése

A Dynamics 365 megoldási rendszer egy olyan keretrendszer, amely adott üzleti funkciókat biztosító összetevők csomagolását, telepítését és eltávolítását végzi. A megoldásokat az ISV-ket és más Microsoft Dynamics 365-partnerek használják a létrehozott bővítmények terjesztésére.

Ha Ön egy meglévő Dynamics 365 (xRM) ISV, akkor valószínűleg már létrehozott egy felügyelt megoldást, és a megoldás. zip fájllal rendelkezik. A megoldásban győződjön meg arról, hogy a "megjelenítendő név" és a "Leírás" mező azt tükrözi, hogy mit szeretne látni az ügyfeleknek. Ezek a CRM Online felügyeleti központban jelennek meg.

![CRMScreenShot1](media/CRMScreenShot1.png)

_**Megjegyzés:** A következő csomag példájában feltételezzük, hogy a megoldás neve "SampleSolution. zip" lesz._

Ha Ön új ISV-t használ, további részletekért tekintse meg a megoldás létrehozását:[https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Ha a megoldás támogatási adatait igényli:

* A mintaadatok létrehozása a tesztkörnyezetben
* A konfiguráció áttelepítési eszközével létrehozhat egy sémát az adataihoz tartozó összehasonlító szabályokkal.
* Mentse a konfigurációs sémát a Project-fájljaival. Ha frissíti a konfigurációs adatait, később szüksége lesz rá.
* Exportálja a konfigurációs adatait. Ügyeljen arra, hogy az exportfájl olyan nevet adjon, amely értelmes az exportáláshoz.
