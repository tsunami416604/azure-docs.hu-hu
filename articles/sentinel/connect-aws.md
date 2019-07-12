---
title: Symantec AWS adatok csatlakozhat az Azure-on Előzetesben Sentinel-|} A Microsoft Docs
description: Ismerje meg, hogyan kell csatlakozni a Symantec AWS adatok Azure-Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2019
ms.author: rkarlin
ms.openlocfilehash: 214269bc5c854aa4d3bfd508b0adb5a53ec096df
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673972"
---
# <a name="connect-azure-sentinel-to-aws"></a>Csatlakozás Azure Sentinel AWS

Az AWS-összekötő használatával az AWS – CloudTrail-események streamelése az Azure-Sentinel. A kapcsolódási folyamat hozzáférést ad az AWS erőforrás naplók, az AWS CloudTrail és Sentinel-Azure közötti megbízhatósági kapcsolat létrehozása az Azure-Sentinel. Mindez AWS egy szerepkör, amely engedélyt ad az Azure az AWS-naplók eléréséhez Sentinel létrehozásával.

## <a name="prerequisites"></a>Előfeltételek

Az Azure-Sentinel-munkaterületen írási engedéllyel kell rendelkeznie.

## <a name="connect-aws"></a>Az AWS csatlakoztatása 
 
1.  Az Amazon Web Services konzolon alatt **Security, Identity & Compliance**, kattintson a **IAM**.

    ![AWS1](./media/connect-aws/aws-1.png)

2.  Válasszon **szerepkörök** kattintson **szerepkör létrehozása**.

    ![AWS2](./media/connect-aws/aws-2.png)

3.  Válasszon **egy másik AWS-fiók.** Az a **Fiókazonosító** mezőbe írja be a **Microsoft Fiókazonosító** (**123412341234**), amelyek az AWS-összekötő oldaláról az Sentinel-az Azure Portalon található.

    ![AWS3](./media/connect-aws/aws-3.png)

4.  Győződjön meg arról, hogy **külső azonosító szükséges** van kiválasztva, majd adja meg a külső azonosító (a munkaterület azonosítója), amelyek az AWS-összekötő oldaláról az Sentinel-az Azure Portalon tekintheti meg.

    ![AWS4](./media/connect-aws/aws-4.png)

5.  A **engedélyek szabályzat csatolása** kiválasztása **AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

6.  Adjon meg egy címkét (nem kötelező).

    ![AWS6](./media/connect-aws/aws-6.png)

7.  Ezután írja be egy **szerepkörnév** , és kattintson a **szerepkör létrehozása** gombra.

    ![AWS7](./media/connect-aws/aws-7.png)

8.  A szerepkörök listájában válassza ki a létrehozott szerepkört.

    ![AWS8](./media/connect-aws/aws-8.png)

9.  Másolás a **szerepkör információ** és illessze be azt a **szerepkör hozzáadásához** mezőt az Azure-Sentinel-portálon.

    ![AWS9](./media/connect-aws/aws-9.png)

10. A megfelelő sémát használ a Log Analytics az AWS-események, keresse meg **AWSCloudTrail**.



## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtudhatta, hogyan Sentinel-Azure AWS CloudTrail csatlakozni. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).

