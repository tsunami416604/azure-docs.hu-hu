---
title: Dynamics 365 Solution Preparation
description: Keretrendszer csomagolása, telepítése és összetevők eltávolítása
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricarod.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 919feb39c9cd84f8da0ef89827ad3e83e0eb9bc5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935255"
---
# <a name="dynamics-365-solution-preparation"></a>Dynamics 365 Solution Preparation

A Dynamics 365 solutioning rendszert egy olyan keretrendszer, csomagolása, telepítése és eltávolítása az összetevőket, amelyek konkrét üzleti funkciókat biztosít. Megoldások terjesztéséhez bővítményeket hozhatnak létre ISV-k és más Microsoft Dynamics 365-partnerek által használt.

Ha egy meglévő Dynamics 365 (xRM) ISV-k, valószínűleg már hozott létre a felügyelt megoldás, és rendelkezik egy solution.zip fájlt. A megoldásban győződjön meg arról, hogy a "megjelenített név" és "Description" mezők tükrözzék, mit szeretne ügyfelek tekintheti. Ezek a CRM Online felügyeleti központban jelennek meg.

![CRMScreenShot1](media/CRMScreenShot1.png)

_**Megjegyzés:** A következő csomag példában indulunk ki a megoldás neve: "SampleSolution.zip"_

Ha Ön egy új ISV, itt megoldások létrehozásának további információt kaphat: [https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Ha a megoldáshoz szükséges adatok támogatása:

* A mintaadatok a tesztkörnyezet létrehozása
* A Configuration Migration eszköz használatával hozzon létre egy sémát az adatok összehasonlító szabályokkal.
* Mentse a projektfájlokat a konfigurációs sémáját. Később szüksége lesz ez a konfigurációs adatok frissítésekor.
* Exportálja a konfigurációs adatokat. Mindenképpen adjon az exportált fájlt az Exportálás számára jelentéssel bíró nevet.
