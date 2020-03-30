---
title: Az AWS CloudTrail csatlakoztatása az Azure Sentinelhez | Microsoft dokumentumok
description: Ismerje meg, hogyan csatlakoztathatja az AWS CloudTrail-adatokat az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 5cbef1f31ea7088d4fab4888f5630af1b765a910
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588654"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Az Azure Sentinel csatlakoztatása az AWS CloudTrail hez

Az AWS-összekötő használatával az összes AWS CloudTrail-eseményt az Azure Sentinelbe streamelheti. Ez a csatlakozási folyamat az Azure Sentinel hozzáférését az AWS erőforrásnaplókhoz delegálja, és bizalmi kapcsolatot hoz létre az AWS CloudTrail és az Azure Sentinel között. Ez az AWS-en érhető el egy olyan szerepkör létrehozásával, amely engedélyt ad az Azure Sentinelnek az AWS-naplók elérésére.

## <a name="prerequisites"></a>Előfeltételek

Írási engedéllyel kell rendelkeznie az Azure Sentinel-munkaterülethez.

> [!NOTE]
> Az Azure Sentinel minden régióból gyűjti a CloudTrail-eseményeket. Javasoljuk, hogy ne streameljen eseményeket egyik régióból a másikba.

## <a name="connect-aws"></a>Az AWS csatlakoztatása 


1. Az Azure Sentinelben válassza az **Adatösszekötők** lehetőséget, majd válassza az **Amazon Web Services** vonalat a táblázatban és az AWS ablaktáblában a jobb oldalon, kattintson az Összekötő megnyitása **lapra.**

1. Kövesse a **Konfiguráció** csoport utasításait a következő lépésekkel.
 
1.  Az Amazon Web Services konzol **biztonság, identitás & megfelelőség csoportban**válassza az **IAM**lehetőséget.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  Válassza **a Szerepkörök** lehetőséget, és válassza **a Szerepkör létrehozása**lehetőséget.

    ![AWS2](./media/connect-aws/aws-2.png)

1.  Válasszon **másik AWS-fiókot.** A **Fiókazonosító** mezőben adja meg a **Microsoft-fiók azonosítóját** (**123412341234),** amely az Azure Sentinel portál AWS-összekötőlapján található.

    ![AWS3 között](./media/connect-aws/aws-3.png)

1.  Győződjön meg arról, **hogy a Külső azonosító megkövetelése** ki van jelölve, majd adja meg a külső azonosítót (munkaterület-azonosító), amely megtalálható az AWS-összekötő lap az Azure Sentinel portálon.

    ![AWS4 között](./media/connect-aws/aws-4.png)

1.  Az **Engedélyek csatolása házirend csoportban** válassza az **AWSCloudTrailReadOnlyAccess lehetőséget.**

    ![AWS5](./media/connect-aws/aws-5.png)

1.  Írjon be egy címkét (nem kötelező).

    ![AWS6 között](./media/connect-aws/aws-6.png)

1.  Ezután adja meg **a szerepkör nevét,** és válassza a **Szerepkör létrehozása** gombot.

    ![AWS7 között](./media/connect-aws/aws-7.png)

1.  A Szerepkörök listában válassza ki a létrehozott szerepkört.

    ![AWS8 között](./media/connect-aws/aws-8.png)

1.  Másolja az **ARN szerepkört**. Az Azure Sentinel portálon az Amazon Web Services-összekötő képernyőjén illessze be a szerepkörbe a **hozzáadáshoz** mezőbe, és kattintson a **Hozzáadás**gombra.

    ![AWS9 között](./media/connect-aws/aws-9.png)

1. Ha a megfelelő sémát szeretné használni az AWS-események naplóanalytics szolgáltatásában, keresse meg az **AWSCloudTrail**kifejezést.



## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja az AWS CloudTrail-t az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)
- Az adatok figyeléséhez [használjon munkafüzeteket.](tutorial-monitor-your-data.md)

