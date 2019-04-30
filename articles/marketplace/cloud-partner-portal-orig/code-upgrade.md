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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776811"
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