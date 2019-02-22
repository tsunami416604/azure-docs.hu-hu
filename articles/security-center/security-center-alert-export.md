---
title: Riasztási naplók exportálása az Azure Log Analytics-munkaterülettel |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan exportálhatja a riasztásokat, amelyek az Azure Security Center által létrehozott Log Analytics-munkaterületet.
services: security-center
documentationcenter: na
author: monhaber
manager: mbaldwin
editor: ''
ms.assetid: 2bc67ce5-ec3a-49df-afc3-b4bad5d8ce21
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/13/2019
ms.author: monhaber
ms.openlocfilehash: 12b8b376a0ff149cbfafc7fe69dd8da636280de8
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56653713"
---
# <a name="export-azure-security-center-alerts"></a>Azure Security Center-riasztások exportálása
Ez a cikk bemutatja, hogyan exportálhat egy olyan helyre, egyéb eszközökkel is használható az Azure Security Center által létrehozott riasztásokról.

A Security Center ügyfelek némelyike kifejezte egy központi helyen, amely tartalmazza az összes, az összesített és feldolgozott adatok (például a fenyegetésészlelési riasztásokat, ajánlott biztonsági szabályzatok, lefedettséggel, a Security Center által létrehozott adatok felhasználásához szükség biztonságos pontszám stb.). Ez lehetővé teszi az adatok további elemzése és feldolgozása történhet. Vagy programozott módon fel az adatokat, a Security Center portál használata helyett egy másik módja is lehet.

Mivel az Azure Log Analytics-munkaterületek segítségével tárolja és feldolgozza a nyers adatokat, virtuális gépekről gyűjtött a **a Log Analyticshez való exportálás** funkció minden megoldást kínál ennek az igénynek úgy, hogy egy felhasználó által definiált munkaterülethez streamadatok.

## <a name="prerequisites"></a>Előfeltételek
Szüksége van egy Log Analytics-munkaterületet. Azt javasoljuk, hogy használja-e az azonos Log Analytics-munkaterületet, amelyet a gyűjtemény-beállítások a megadott. Ha egy az adatgyűjtés meghatározása. [Log Analytics-munkaterület-gyűjtemény](security-center-enable-data-collection.md)


## <a name="export-the-alerts"></a>A riasztások exportálása
1. Kattintson a **biztonsági házirend**.
1. Kattintson a használni kívánt előfizetés sorban **beállításainak szerkesztése**.
  ![Beállítások szerkesztése](./media/security-center-alert-export/edit_settings.png "beállításainak szerkesztése")
1. Kattintson a **adatgyűjtés**.
1. Görgessen le a **Store a Security Center adatokat bővített** váltása lehetőség kattintson **a**.

   ![Beállítás Store](./media/security-center-alert-export/store_data_option.png "biztonsági házirend")
1. Válassza ki a munkaterületet, amelyet szeretne exportálni a riasztásokat.

    > [!NOTE]
    > Riasztások nem exportálhatja a Security Center alapértelmezett munkaterületre. Említetteknek megfelelően az [Előfeltételek](#Prerequisites), kell használnia a Log Analytics-munkaterületet.

1. Kattintson a **mentése** (a képernyő tetején).