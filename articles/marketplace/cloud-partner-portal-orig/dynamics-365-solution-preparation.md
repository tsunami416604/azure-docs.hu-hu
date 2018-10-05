---
title: Dynamics 365 megoldás előkészítése |} A Microsoft Docs
description: A packaing, telepítése és unstalling összetevők Framewrok
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricarod.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: c1e9c831681867e6a6238159599af39cbab10b7e
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810869"
---
# <a name="dynamics-365-solution-preparation"></a>Dynamics 365 megoldás előkészítése

A Dynamics 365 solutioning rendszert egy olyan keretrendszer, csomagolása, telepítése és eltávolítása az összetevőket, amelyek konkrét üzleti funkciókat biztosít. Megoldások terjesztéséhez bővítményeket hozhatnak létre ISV-k és más Microsoft Dynamics 365-partnerek által használt.

Ha egy meglévő Dynamics 365 (xRM) ISV-k, valószínűleg már hozott létre a felügyelt megoldás, és rendelkezik egy solution.zip fájlt. A megoldásban győződjön meg arról, hogy a "megjelenített név" és "Description" mezők tükrözzék, mit szeretne ügyfelek tekintheti. Ezek a CRM Online felügyeleti központban jelennek meg.

![CRMScreenShot1](media/CRMScreenShot1.png)

_**Megjegyzés:** a csomagot a példában a következő indulunk ki a megoldás neve: "SampleSolution.zip"_

Ha Ön egy új ISV, itt megoldások létrehozásának további információt kaphat: [https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Ha a megoldáshoz szükséges adatok támogatása:

* A mintaadatok a tesztkörnyezet létrehozása
* A Configuration Migration eszköz használatával hozzon létre egy sémát az adatok összehasonlító szabályokkal.
* Mentse a projektfájlokat a konfigurációs sémáját. Később szüksége lesz ez a konfigurációs adatok frissítésekor.
* Exportálja a konfigurációs adatokat. Mindenképpen adjon az exportált fájlt az Exportálás számára jelentéssel bíró nevet.
