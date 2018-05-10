---
title: Az Amazon Web Services hitelesítés konfigurálása
description: Ez a cikk ismerteti, hogyan lehet létrehozni és megerősíteni egy AWS hitelesítést az Azure Automation forgatókönyveihez, amelyek az AWS-erőforrásokat kezelik.
keywords: aws-hitelesítés, aws konfigurálása
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/17/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: f8ac816e03df9bb41207a0463d3fa8aa58754943
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Forgatókönyvek hitelesítése az Amazon webszolgáltatásokkal

Az általános feladatoknak az Amazon webszolgáltatások (AWS) erőforrásaival történő automatizálása az Automation forgatókönyvekkel lehetséges az Azure szolgáltatásban. Sok feladatot automatizálhat az AWS-ben az Automation forgatókönyvek használatával, ugyanúgy, mint az Azure erőforrásaival. Mindössze két dologra van szükség:

* Egy AWS-előfizetésre és a hitelesítő adatokra. Konkréten az AWS-hozzáférési kulcsára és a titkos kulcsára. További információkért tekintse át az [AWS hitelesítő adatok használatával](http://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html) foglalkozó cikket.
* Egy Azure-előfizetésre és egy Automation-fiókra.

Az AWS használatával történő hitelesítéshez meg kell határozni az AWS hitelesítő adatokat az Azure Automation által futtatott forgatókönyvek hitelesítéséhez. Ha már rendelkezik egy Automation-fiók létrehozása és használandó, amely az AWS való hitelesítéshez szükséges, a következő részben ismertetett lépéseket követheti: Ha szeretne egy fiókot a célcsoport-kezelési AWS erőforrások runbookok fordítsanak, először készítsen egy új [ Automation-fiók](automation-offering-get-started.md) (kihagyása, létrehozhat egy egyszerű szolgáltatásnév), és kövesse az alábbi lépéseket:

## <a name="configure-automation-account"></a>Automation-fiók konfigurálása

Ahhoz, hogy az Azure Automation és az AWS kommunikáljon egymással, először le kell kérnie AWS hitelesítő adatait, és objektumként kell tárolnia őket az Azure Automationben. Végezze el az [AWS-fiók elérési kulcsának kezelése](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) című dokumentumban leírt lépéseket egy elérési kulcs létrehozásához, és másolja át az **Elérési kulcs azonosítóját** és a **Titkos elérési kulcsot** (vagy le is töltheti kulcsfájlját, hogy egy másik, biztonságos helyen tárolja azt).

Miután létrehozta és átmásolta AWS biztonsági kulcsait, létre kell hoznia egy hitelesítőadat-objektumot egy Azure Automation-fiókhoz, hogy biztonságosan tudja tárolni őket, és hivatkozni tudjon rájuk a runbookokkal. Kövesse a szakasz a: **új hitelesítő adat létrehozása** a a [hitelesítőadat-eszköz az Azure Automationben](automation-credentials.md#to-create-a-new-credential-asset-with-the-azure-portal) a következő cikket, és adja meg a következő adatokat:

1. A **Név** mezőbe írja be az **AWScred** nevet, vagy egy megfelelő értéket, amely követi az elnevezési szabványait.
2. A **Felhasználónév** mezőbe írja be az **Elérési azonosítóját**, a **Jelszó** és **Jelszó megerősítése** mezőkbe pedig a **Titkos elérési kulcsát**.

## <a name="next-steps"></a>További lépések

* Az AWS feladatainak automatizálására szolgáló runbookok létrehozására vonatkozó további információkért tekintse át a [virtuális gépek telepítésének Amazon webszolgáltatásokban történő automatizálásával](automation-scenario-aws-deployment.md) foglalkozó cikket.