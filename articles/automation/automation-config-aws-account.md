---
title: Hitelesítés konfigurálása az Amazon Web Services
description: Ez a cikk ismerteti, hogyan lehet létrehozni és megerősíteni egy AWS hitelesítést az Azure Automation forgatókönyveihez, amelyek az AWS-erőforrásokat kezelik.
keywords: aws-hitelesítés, aws konfigurálása
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e6a7f1758fd0a6fb4ce91c18f375dcf189becd41
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54435498"
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Forgatókönyvek hitelesítése az Amazon webszolgáltatásokkal

Az általános feladatoknak az Amazon webszolgáltatások (AWS) erőforrásaival történő automatizálása az Automation forgatókönyvekkel lehetséges az Azure szolgáltatásban. Sok feladatot automatizálhat az AWS-ben az Automation forgatókönyvek használatával, ugyanúgy, mint az Azure erőforrásaival. Mindössze két dologra van szükség:

* Egy AWS-előfizetésre és a hitelesítő adatokra. Konkréten az AWS-hozzáférési kulcsára és a titkos kulcsára. További információkért tekintse át az [AWS hitelesítő adatok használatával](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html) foglalkozó cikket.
* Egy Azure-előfizetésre és egy Automation-fiókra.

Az AWS használatával történő hitelesítéshez meg kell határozni az AWS hitelesítő adatokat az Azure Automation által futtatott forgatókönyvek hitelesítéséhez. Ha már rendelkezik egy létrehozott Automation-fiókot, és szeretné használni, amely AWS felé hitelesíteni, követheti a következő szakaszban ismertetett lépéseket: Ha meg szeretné üzemeltetni az AWS-erőforrásokat célzó runbookokhoz fiókot használja, akkor először hozzon létre egy új [Automation-fiók](automation-offering-get-started.md) (hagyja ki az egyszerű szolgáltatás létrehozását), és kövesse az alábbi lépéseket:

## <a name="configure-automation-account"></a>Automation-fiók konfigurálása

Ahhoz, hogy az Azure Automation és az AWS kommunikáljon egymással, először le kell kérnie AWS hitelesítő adatait, és objektumként kell tárolnia őket az Azure Automationben. Végezze el az [AWS-fiók elérési kulcsának kezelése](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) című dokumentumban leírt lépéseket egy elérési kulcs létrehozásához, és másolja át az **Elérési kulcs azonosítóját** és a **Titkos elérési kulcsot** (vagy le is töltheti kulcsfájlját, hogy egy másik, biztonságos helyen tárolja azt).

Miután létrehozta és átmásolta AWS biztonsági kulcsait, létre kell hoznia egy hitelesítőadat-objektumot egy Azure Automation-fiókhoz, hogy biztonságosan tudja tárolni őket, és hivatkozni tudjon rájuk a runbookokkal. A szakasz lépéseit kövesse: **Egy új hitelesítő adat létrehozása** a a [Azure Automation Hitelesítőeszközei](automation-credentials.md#to-create-a-new-credential-asset-with-the-azure-portal) című cikket, és adja meg a következőket:

1. A **Név** mezőbe írja be az **AWScred** nevet, vagy egy megfelelő értéket, amely követi az elnevezési szabványait.
2. A **Felhasználónév** mezőbe írja be az **Elérési azonosítóját**, a **Jelszó** és **Jelszó megerősítése** mezőkbe pedig a **Titkos elérési kulcsát**.

## <a name="next-steps"></a>További lépések

* Az AWS feladatainak automatizálására szolgáló runbookok létrehozására vonatkozó további információkért tekintse át a [virtuális gépek telepítésének Amazon webszolgáltatásokban történő automatizálásával](automation-scenario-aws-deployment.md) foglalkozó cikket.
