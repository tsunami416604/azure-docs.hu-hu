---
title: Kód frissítése a legújabb platformot |} Az Azure Marketplace-en
description: Ez a témakör ismerteti a Microsoft Dynamics 365 for Operations platformverzió frissítése a legújabb platformot a kiadásra
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: aedc2c7474de0fe068a329eb2205e9bb08e62c3a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935281"
---
# <a name="upgrading-code-to-the-latest-platform"></a>A legújabb platformot a kód frissítése

Ez a cikk bemutatja, hogyan frissítse a Microsoft Dynamics 365 for Operations platformverzió a legújabb platformot.

## <a name="overview"></a>Áttekintés

A Microsoft Dynamics 365 for Operations platform az alábbi összetevőkből áll:

Dynamics 365 for Operations platform bináris fájlokat, például az objektum AOS Server (-), az adatok keretrendszert, a jelentéskészítési és üzleti intelligenciára épülő (BI) keretrendszer, fejlesztői eszközöket és elemzési szolgáltatásokat. A következő alkalmazás objektum fa Alkalmazásobjektum csomagok:

1. Alkalmazásplatform
2. Application Foundation
3. Alapvető szolgáltatások tesztelése

**Fontos**: Szeretne áthelyezni a legújabb Dynamics 365 for Operations platform, a Dynamics 365-műveletek végrehajtásához minden testre szabott elem (Felülrétegzési) bármely tartoznak a platform az alkalmazásobjektum-fa-csomagokat nem lehet. Ez a korlátozás bevezetett 3, frissítés, hogy a platform zökkenőmentes folyamatos frissítések módosíthatók. Ha futtat egy platform, amely régebbi, mint a platform 3, a frissítés egy korábbi build szakaszban Ez a cikk végén található 3-as frissítés platformra.

További információ a kód frissítése, tekintse meg [Itt](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update).