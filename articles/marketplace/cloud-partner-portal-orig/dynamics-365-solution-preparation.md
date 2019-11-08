---
title: Dynamics 365-megoldás előkészítése
description: Az összetevők csomagolására, telepítésére és eltávolítására szolgáló keretrendszer
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricarod.Villalobos
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: bcb3bb63f305aeb98efda3baf0f6661bd7f67a7c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824235"
---
# <a name="dynamics-365-solution-preparation"></a>Dynamics 365-megoldás előkészítése

A Dynamics 365 megoldási rendszer egy olyan keretrendszer, amely adott üzleti funkciókat biztosító összetevők csomagolását, telepítését és eltávolítását végzi. A megoldásokat az ISV-ket és más Microsoft Dynamics 365-partnerek használják a létrehozott bővítmények terjesztésére.

Ha Ön egy meglévő Dynamics 365 (xRM) ISV, akkor valószínűleg már létrehozott egy felügyelt megoldást, és a megoldás. zip fájllal rendelkezik. A megoldásban győződjön meg arról, hogy a "megjelenítendő név" és a "Leírás" mező azt tükrözi, hogy mit szeretne látni az ügyfeleknek. Ezek a CRM Online felügyeleti központban jelennek meg.

![CRMScreenShot1](media/CRMScreenShot1.png)

_**Megjegyzés:** A következő csomag példájában feltételezzük, hogy a megoldás neve "SampleSolution. zip" lesz._

Ha Ön új ISV-t használ, további részletekért tekintse meg a megoldás létrehozását: [https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Ha a megoldás támogatási adatait igényli:

* A mintaadatok létrehozása a tesztkörnyezetben
* A konfiguráció áttelepítési eszközével létrehozhat egy sémát az adataihoz tartozó összehasonlító szabályokkal.
* Mentse a konfigurációs sémát a Project-fájljaival. Ha frissíti a konfigurációs adatait, később szüksége lesz rá.
* Exportálja a konfigurációs adatait. Ügyeljen arra, hogy az exportfájl olyan nevet adjon, amely értelmes az exportáláshoz.
