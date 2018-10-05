---
title: Kód frissítése a legújabb platformot |} A Microsoft Docs
description: Ez a témakör ismerteti a Microsoft Dynamics 365 for Operations platformverzió frissítése a legújabb platformot a kiadásra
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 787d9ef509bcafeb6a21d4b0ec952ea1eb4fd52b
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810617"
---
# <a name="upgrading-code-to-the-latest-platform"></a>A legújabb platformot a kód frissítése

Ez a cikk bemutatja, hogyan frissítse a Microsoft Dynamics 365 for Operations platformverzió a legújabb platformot.

## <a name="overview"></a>Áttekintés

A Microsoft Dynamics 365 for Operations platform az alábbi összetevőkből áll:

Dynamics 365 for Operations platform bináris fájlokat, például az objektum AOS Server (-), az adatok keretrendszert, a jelentéskészítési és üzleti intelligenciára épülő (BI) keretrendszer, fejlesztői eszközöket és elemzési szolgáltatásokat. A következő alkalmazás objektum fa Alkalmazásobjektum csomagok:

1. Alkalmazásplatform
2. Alkalmazás Foundation
3. Alapvető szolgáltatások tesztelése

**Fontos**: szeretne áthelyezni a legújabb Dynamics 365 for Operations platform, a Dynamics 365-műveletek végrehajtásához, az alkalmazásobjektum-fa csomagok tartozó platform a testreszabások (overlayering) nem lehet. Ez a korlátozás bevezetett 3, frissítés, hogy a platform zökkenőmentes folyamatos frissítések módosíthatók. Ha futtat egy platform, amely régebbi, mint a platform 3, a frissítés egy korábbi build szakaszban Ez a cikk végén található 3-as frissítés platformra.

További információ a kód frissítése, tekintse meg [Itt](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update).