---
title: Azure Automation-forgatókönyvek hitelesítése az Amazon Web Services segítségével
description: Ez a cikk ismerteti, hogyan lehet létrehozni és megerősíteni egy AWS hitelesítést az Azure Automation forgatókönyveihez, amelyek az AWS-erőforrásokat kezelik.
keywords: aws-hitelesítés, aws konfigurálása
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 92919d2e0cc7ca685d2b60a8e7a8cf20433bbefc
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994716"
---
# <a name="authenticate-azure-automation-runbooks-with-amazon-web-services"></a>Azure Automation-forgatókönyvek hitelesítése az Amazon Web Services segítségével

Az általános feladatoknak az Amazon webszolgáltatások (AWS) erőforrásaival történő automatizálása az Automation forgatókönyvekkel lehetséges az Azure szolgáltatásban. Sok feladatot automatizálhat az AWS-ben az Automation forgatókönyvek használatával, ugyanúgy, mint az Azure erőforrásaival. Mindössze két dologra van szükség:

* Egy AWS-előfizetésre és a hitelesítő adatokra. Konkréten az AWS-hozzáférési kulcsára és a titkos kulcsára. További információkért tekintse át az [AWS hitelesítő adatok használatával](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html) foglalkozó cikket.
* Egy Azure-előfizetésre és egy Automation-fiókra.

Az AWS használatával történő hitelesítéshez meg kell határozni az AWS hitelesítő adatokat az Azure Automation által futtatott forgatókönyvek hitelesítéséhez. Ha már létrehozott egy Automation-fiókot, és annak használatával szeretne AWS felé hitelesíteni, kövesse a következő szakaszban leírt lépéseket. Ha az AWS-erőforrásokra vonatkozó runbookok fiókot szeretne hozzárendelni, először hozzon létre egy új [Automation-fiókot](automation-create-standalone-account.md) , és hagyja ki a lépést a futtató fiók létrehozásához. A fiók létrehozása után kövesse az alábbi lépéseket a konfiguráció befejezéséhez.

## <a name="configure-automation-account"></a>Automation-fiók konfigurálása

Ahhoz, hogy az Azure Automation és az AWS kommunikáljon egymással, először le kell kérnie AWS hitelesítő adatait, és objektumként kell tárolnia őket az Azure Automationben. Végezze el az [AWS-fiók elérési kulcsának kezelése](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) című dokumentumban leírt lépéseket egy elérési kulcs létrehozásához, és másolja át az **Elérési kulcs azonosítóját** és a **Titkos elérési kulcsot** (vagy le is töltheti kulcsfájlját, hogy egy másik, biztonságos helyen tárolja azt).

Miután létrehozta és átmásolta AWS biztonsági kulcsait, létre kell hoznia egy hitelesítőadat-objektumot egy Azure Automation-fiókhoz, hogy biztonságosan tudja tárolni őket, és hivatkozni tudjon rájuk a runbookokkal. Kövesse a következő szakaszban ismertetett lépéseket: **új hitelesítő adatok létrehozása** a [hitelesítő adatokban Azure Automation](shared-resources/credentials.md#create-a-new-credential-asset-with-the-azure-portal) cikkben, és adja meg az alábbi adatokat:

1. A **Név** mezőbe írja be az **AWScred** nevet, vagy egy megfelelő értéket, amely követi az elnevezési szabványait.
2. A **Felhasználónév** mezőbe írja be a **hozzáférési azonosítót** és a **titkos elérési kulcsot** a **jelszó** és **Jelszó megerősítése** mezőbe.

## <a name="next-steps"></a>További lépések

* Tekintse át a [virtuális gép üzembe helyezésének automatizálását Amazon Web Servicesban](automation-scenario-aws-deployment.md) , hogy megtudja, hogyan hozhat létre RUNBOOKOK az AWS-feladatok automatizálásához.
