---
title: "Meglévő Azure-előfizetés hozzáadása az Azure AD-címtárhoz | Microsoft Docs"
description: "Meglévő előfizetés hozzáadása az Azure AD-címtárhoz"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: bc4773c2-bc4a-4d21-9264-2267065f0aea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/17/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: abf207a3ceec708a828170936f7dc7948ccf34a9
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2017
---
# <a name="how-to-add-an-azure-subscription-to-azure-active-directory"></a>Azure-előfizetés hozzáadása az Azure Active Directoryhoz
Ez a cikk az Azure-előfizetések és az Azure Active Directory (Azure AD) közötti kapcsolatról tartalmaz információt, illetve arról, hogy miként adható hozzá egy meglévő előfizetés az Azure AD-címtárhoz. Az Azure-előfizetés megbízhatósági kapcsolatban áll az Azure AD-vel, ami azt jelenti, hogy ezt a címtárat megbízhatónak tekinti a felhasználók, szolgáltatások és eszközök hitelesítéséhez. Több előfizetés is megbízhat ugyanabban a címtárban, de egy előfizetés csak egy megbízható címtárral rendelkezhet. 

Az előfizetés és a címtár közötti megbízhatósági kapcsolat nem olyan, mint ami az előfizetés és az Azure összes többi erőforrása (webhelyek, adatbázisok stb.) között áll fenn. Ha egy előfizetés lejár, akkor az előfizetéssel társított egyéb erőforrások hozzáférése is lejár. Az Azure AD-címtár azonban elérhető marad az Azure-ban, így társíthat hozzá egy másik előfizetést, és az új előfizetéssel kezelheti a címtár felhasználóit.

Minden felhasználó egyetlen saját címtárral rendelkezik, amely hitelesíti őt, de más címtárakban lehet vendég is. Az Azure AD-ben a felhasználó azokat a címtárakat látja, amelyeknek a felhasználói fiókja a tagja vagy vendége.

## <a name="to-add-an-existing-subscription-to-your-azure-ad-directory"></a>Meglévő előfizetés hozzáadása az Azure AD-címtárhoz
Egy olyan fiókkal kell bejelentkeznie, amely az előfizetéshez jelenleg társított címtárban és abban a címtárban is létezik, amelyhez az előfizetést hozzá szeretné rendelni. 

1. Jelentkezzen be az [Azure Account Centerbe](https://account.azure.com/Subscriptions) annak az előfizetésnek a fiókadminisztrátori fiókjával, amelynek a tulajdonjogát át szeretné adni.
2. Bizonyosodjon meg róla, hogy az előfizetés tulajdonosaként megadni kívánt felhasználó szerepel a megcélzott címtárban.
3. Kattintson az **Előfizetés átadása** elemre.
4. Adja meg a címzettet. A címzett automatikusan kapni fog egy elfogadási hivatkozást tartalmazó e-mailt.
5. A címzett a hivatkozásra való kattintás után követnie kell az utasításokat, többek között a fizetési információk megadásával. Ha ez sikeresen befejeződött, a rendszer átadja az előfizetést. 
6. Az előfizetés alapértelmezett címtára arra módosul, amelyikben a felhasználó szerepel.

További információért lásd: [Az Azure-előfizetés tulajdonjogának átadása másik fiók számára](../billing/billing-subscription-transfer.md).

## <a name="next-steps"></a>Következő lépések
* További információ az Azure-előfizetések rendszergazdáinak módosításáról: [Azure-előfizetés tulajdonjogának átruházása másik fiókra](../billing/billing-subscription-transfer.md)
* Az erőforrások hozzáférésének Microsoft Azure-ban történő kezeléséről további információért lásd: [Az erőforrások hozzáférésének megismerése az Azure-ban](active-directory-understanding-resource-access.md)
* A szerepkörök Azure AD-ben történő hozzárendeléséről további információért lásd: [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban](active-directory-assign-admin-roles-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG
