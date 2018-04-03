---
title: Azure verem Development Kit alapok |} Microsoft Docs
description: Ismerteti, hogyan lehet az Azure verem Development Kit (ASDK) az alapvető felügyeleti feladatok elvégzéséhez.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 05dd42b049c75b9ea592ffe341f44e3b02b9757f
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="asdk-administration-basics"></a>ASDK Adminisztráció alapjai 
Számos szempontot Ha most ismerkedik az Azure verem Development Kit (ASDK) felügyeleti tudnia kell. Ez az útmutató áttekintést nyújt az Azure verem kezelőként a kiértékelési környezet a szerepkör, és gyorsan ahhoz, hogy a felhasználók tesztelése hogyan válhat hatékonyabbá.

Először tekintse át a [Mi az Azure verem szoftverfejlesztői készlet?](asdk-what-is.md) tudja a ASDK és korlátokkal célja, hogy a cikk. Célszerű használni a csomag "védőfalat," ahol kiértékelheti az Azure-verem fejlesztéséhez és teszteléséhez az alkalmazások nem éles környezetben. 

Például az Azure-ban Azure verem innovates gyorsan, azt fogja rendszeresen kiadási a ASDK új verziói. Azonban a ASDK nem frissíthető, például integrált Azure verem rendszerek központi telepítéseket is. Ezért, ha azt szeretné, a legújabb buildjével áthelyezése, kell teljesen [telepítse újra a ASDK](asdk-redeploy.md). Nem alkalmazhat a frissítési csomagok. Ez a folyamat időt vesz igénybe, de az az előnye, hogy próbálhatja ki a legújabb szolgáltatásokhoz, amint azok elérhetővé válnak. 

## <a name="what-account-should-i-use"></a>Milyen fiókot érdemes használni?
Nincsenek néhány fiókokkal kapcsolatos megfontolások kell ügyelnie, ha az Azure-verem kezelése. Különösen a központi telepítések segítségével a Windows Server Active Directory összevonási szolgáltatások (AD FS) helyett az Azure Active Directory (Azure AD) identitás-szolgáltatóként. A következő fiókokkal kapcsolatos megfontolások integrált Azure verem rendszerek és ASDK központi telepítéseket is vonatkozik:

|Fiók|Azure AD|AD FS|
|-----|-----|-----|
|Helyi rendszergazdai (. \Administrator)|ASDK állomás rendszergazda|ASDK állomás rendszergazda|
|AzureStack\AzureStackAdmin|ASDK állomás rendszergazda<br><br>Jelentkezzen be a verem Azure felügyeleti portálján is használható<br><br>Olvashatják és felügyelheti a Service Fabric körök|ASDK állomás rendszergazda<br><br>Nem lehet hozzáférni a verem Azure felügyeleti portálon<br><br>Olvashatják és felügyelheti a Service Fabric körök<br><br>Már nem tulajdonosa annak az alapértelmezett szolgáltató előfizetés (terjesztési pontok)|
|AzureStack\CloudAdmin|Majd futtassa a Rendszerjogosultságú végpont belül engedélyezett parancsok is|Majd futtassa a Rendszerjogosultságú végpont belül engedélyezett parancsok is<br><br>Nem jelentkezhetnek be a ASDK állomás<br><br>Az alapértelmezett szolgáltató előfizetés (terjesztési pontok) tulajdonosa|
|Azure AD globális rendszergazda|Telepítés során használt<br><br>Az alapértelmezett szolgáltató előfizetés (terjesztési pontok) tulajdonosa|Nem alkalmazható|
|

## <a name="what-tools-do-i-use-to-manage"></a>Milyen eszközök használható kezeléséhez?
Használhatja a [Azure verem adminisztrációs portálhoz](https://adminportal.local.azurestack.external) vagy a PowerShell Azure verem kezeléséhez. Az alapvető fogalmakat további legkönnyebben a portálon keresztül. Ha azt szeretné, ha a PowerShell segítségével, telepítendő [PowerShell Azure verem](asdk-post-deploy.md#install-azure-stack-powershell) és [töltse le az Azure-verem eszközök a Githubról](asdk-post-deploy.md#download-the-azure-stack-tools).

Azure verem Azure Resource Manager használ az alapul szolgáló telepítés, a felügyeleti és a szervezet mechanizmusként. Ha Azure verem kezelésére, és segítségével a felhasználók támogatására, elsajátíthatja kell kapcsolatos Azure Resource Manager. Olvasásával többet is megtudhat a [Ismerkedés az Azure Resource Manager tanulmány](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf).

## <a name="your-typical-responsibilities"></a>A tipikus feladatkörei
A felhasználók érdemes szolgáltatásokat használni. A szempontjából a fő szerepköre számukra elérhetővé ezeket a szolgáltatásokat. Használja a ASDK, áttekintheti, hogy mely szolgáltatásokat nyújtanak, és azon szolgáltatások által elérhető [terveket, ajánlatok és kvóták létrehozása](asdk-offer-services.md). Is szüksége elemek hozzáadására a piactéren, például virtuálisgép-lemezképeket. A legegyszerűbb módja [töltse le a Piactéri elemek](asdk-marketplace-item.md) Azure veremnek az Azure-ból.

> [!NOTE]
> Ha szeretné a tervek, ajánlatok és szolgáltatások, használja a [felhasználói portál](https://portal.local.azurestack.external); nem a [adminisztrációs portálhoz](https://adminportal.local.azurestack.external).

Szolgáltatásokat nyújtó mellett összes rendszeres feladatot Azure verem operátor tartani a ASDK működik, és el kell végeznie. Ezek a feladatai közé tartozik többek között a következőket:
- Felhasználói fiókok hozzáadása vagy az Azure Active Directory (Azure AD), vagy az Active Directory összevonási szolgáltatások (AD FS) központi telepítések.
- Szerepköralapú hozzáférés-vezérlést (RBAC) szerepkörök (ez nem csak a rendszergazdák korlátozni) hozzárendelése
- Infrastruktúra állapotának figyelése
- Hálózati és tárolási erőforrások kezelése
- Cserélje le a hibás development kit gazdahardverre számítógép 

## <a name="where-to-get-support"></a>Honnan szerezhetők be támogatása
A csomag, a támogatási egyetlen választása marad: kérje meg a támogatási kapcsolatos kérdések a [Microsoft Azure verem fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Ha a felügyeleti portál jobb felső sarkában kattintson a Súgó és támogatás ikonra (kérdőjel), és kattintson a **új támogatja a kérelem**, ekkor megnyílik a fórumok hely közvetlenül. Ezek a fórumok rendszeresen figyeli. 

> [!IMPORTANT]
> Mivel a ASDK egy kiértékelési környezet, nincs érhető el a Microsoft támogatja a szolgáltatások Ügyféltámogatási hivatalos támogatás.

## <a name="next-steps"></a>További lépések
[A ASDK telepítése](asdk-deploy.md)

