---
title: "Hitelesítés konfigurálása az Amazon webszolgáltatásokkal (AWS) | Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan lehet létrehozni és megerősíteni egy AWS hitelesítést az Azure Automation forgatókönyveihez, amelyek az AWS-erőforrásokat kezelik."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
keywords: "aws-hitelesítés, aws konfigurálása"
ms.assetid: b6dde4bb-26ac-4876-9aa9-e586bed30d6b
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/11/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 843f0862f9e4d8dc1d40e110710e48ad66716168
ms.openlocfilehash: fe590e7fc551c175d2f41f5b98e1558a756df806


---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Forgatókönyvek hitelesítése az Amazon webszolgáltatásokkal
Az általános feladatoknak az Amazon webszolgáltatások (AWS) erőforrásaival történő automatizálása az Automation forgatókönyvekkel lehetséges az Azure szolgáltatásban.  Sok feladatot automatizálhat az AWS-ben az Automation forgatókönyvek használatával, ugyanúgy, mint az Azure erőforrásaival.  Mindössze két dologra van szükség:

* Egy AWS-előfizetésre és a hitelesítő adatokra.  Konkréten az AWS-hozzáférési kulcsára és a titkos kulcsára.  További információkért tekintse át az [AWS hitelesítő adatok használata](http://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html) című cikket.
* Egy Azure-előfizetésre és egy Automation-fiókra.  Egy Azure Automation-fiók beállításáról további információkért tekintse át az [Azure-beli futtató fiók konfigurálása](automation-sec-configure-azure-runas-account.md) című cikket.  

Az AWS használatával történő hitelesítéshez meg kell határozni az AWS hitelesítő adatokat az Azure Automation által futtatott forgatókönyvek hitelesítéséhez. Ha már létrehozott egy Automation-fiókot, és annak használatával szeretne AWS felé hitelesíteni, kövesse a következő szakaszban leírt lépéseket.  Ha szeretne az AWS-erőforrásokat célzó forgatókönyvekhez egy dedikált fiókot használni, először hozzon létre egy új [Automation futtató fiókot](automation-sec-configure-azure-runas-account.md) (hagyja ki az egyszerű szolgáltatás létrehozásának lehetőségét), majd kövesse az alábbi lépéseket.

## <a name="configure-automation-account"></a>Automation-fiók konfigurálása
Ahhoz, hogy az Azure Automation és az AWS kommunikáljon egymással, először le kell kérnie AWS hitelesítő adatait, és objektumként eltárolni őket az Azure Automation szolgáltatásban.  Végezze el az [AWS-fiók elérési kulcsának kezelése](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) című dokumentumban leírt lépéseket egy elérési kulcs létrehozásához, és másolja át az **Elérési kulcs azonosítóját** és a **Titkos elérési kulcsot** (vagy le is töltheti kulcsfájlját, hogy egy másik, biztonságos helyen tárolja azt).

Miután létrehozta ás átmásolta AWS biztonsági kulcsait, létre kell hoznia egy hitelesítési objektumot egy Azure Automation fiókhoz, hogy biztonságosan tárolja őket, és hivatkozni tudjon rájuk a forgatókönyvekkel.  Kövesse az [Azure Automation hitelesítő objektumai](automation-credentials.md) című cikk **Új hitelesítő objektum létrehozása** szakaszában leírt lépéseket, és adja meg a következő információkat:

1. A **Név** mezőbe írja be az **AWScred** nevet, vagy egy megfelelő értéket, amely követi az elnevezési szabványait.  
2. A **Felhasználónév** mezőbe írja be az **Elérési azonosítóját** és a **Titkos elérési kulcsát** a **Jelszó** és **Jelszó megerősítése** mezőbe.   

## <a name="next-steps"></a>Következő lépések
* Az AWS feladatainak automatizálására szolgáló forgatókönyvek létrehozásáról további információkért tekintse át a [VM telepítésének automatizálása az Amazon webszolgáltatásokban](automation-scenario-aws-deployment.md) című megoldásbemutató cikket.




<!--HONumber=Nov16_HO3-->


