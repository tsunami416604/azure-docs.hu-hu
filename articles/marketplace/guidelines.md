---
title: Irányelvek Azure piactér és AppSource közzétevő |} Azure
description: Az alkalmazás és szolgáltatás közzétevők Azure piactér és AppSource vonatkozó irányelvek
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: 4da6f5c4513477d1adbf4d7645a66de112eeab23
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36307485"
---
# <a name="guidelines"></a>Irányelvek  

<!--
## Guidelines for AppSource  
-->
---  

## <a name="guidelines-for-azure-marketplace"></a>Az Azure piactér vonatkozó irányelvek  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Piactér-fiók kezeléséhez a Microsoft ID létrehozására vonatkozó irányelvek  
Ha több személy a piactér-fiókja létrehozásához használt ugyanazon Microsoft ID hozzáférésre van szüksége, majd kövesse ezeket az irányelveket a vállalati fiók létrehozásához. 

>[!IMPORTANT]
>Több felhasználó a Microsoft Developer Center (fejlesztői központ) fiók elérésének engedélyezésére, a Microsoft azt javasolja, hogy Azure Active Directory (Azure AD) segítségével szerepkörök hozzárendelése az egyéni felhasználók számára. Minden felhasználóhoz egyedi bejelentkezik hozzáférést kell a fiókot az Azure AD hitelesítő adatait. Hozzon létre a Microsoft ID e-mail cím a tartományban regisztrálni a vállalat a Microsoft azt javasolja, hogy az e-mailt nem rendelhető hozzá egy adott. Például: `windowsapps@fabrikam.com`.  
>*   További tudnivalókért keresse fel a [probléma: az Azure AD-ben a Microsoft ID összevont tartományt](#issue-microsoft-id-in-an-azure-ad-federated-domain) szakasz.  

*   A Microsoft ID a lehető legkevesebb fejlesztők való hozzáférés korlátozására. 
*   Állítsa be a vállalati e-mailek terjesztési lista, amely tartalmazza az összes felhasználója számára hozzáférést kell a fejlesztői központban regisztrált fiókjában. Adja hozzá a DL e-mail címet biztonsági adataihoz. A kibontott terjesztési lista lehetővé teszi, hogy az alkalmazottak a listán szereplő megkapni a biztonsági kódokat, ha a kért, és kezelheti a biztonsági adatokat a Microsoft ID. mindegyikét Ha egy terjesztési listát beállítása esetén nem valósítható meg, majd az egyes e-mail fiók tulajdonosának elérhetőnek kell lennie eléréséhez és megosztása, amikor a rendszer kéri a biztonsági kódot.  
    *   Például a tulajdonos kér a Microsoft ID, vagy a Microsoft ID egy új eszközről való elérésekor a program hozzáadja a új biztonsági adatait.  
*   Adja hozzá a munkahelyi telefonszámot, amelyet a bővítmény nem igényel, és kulcs csapattagok számára érhető el.  
*   Általában a fejlesztők számára megbízható eszközeiket használják, jelentkezzen be a fejlesztői központban regisztrált fiókjában elvégzéséhez szükséges. Kulcs csapattagok kell a megbízható eszközökhöz való hozzáférést. Megbízható eszközök eléréséhez használt kevesebb küldéséhez a biztonsági kódokat, ha valaki fér hozzá a fejlesztői központban regisztrált fiókjában.  
*   Ha van szükség, hogy hozzáférést biztosítson a fejlesztői központban regisztrált fiókjában egy nem megbízható számítógépről, majd korlátozza legfeljebb öt fejlesztők számára a hozzáférést. Ideális esetben a fejlesztőket kell fér hozzá a fiókjához a számítógépekről, amelyek azonos földrajzi és a hálózati helyet.  
*   Gyakran tekintse át, és ellenőrizze a biztonsági adatait.  
    *   A biztonsági információk megtekintéséhez keresse fel a biztonsági beállítások lapon található [account.live.com/proofs/Manage](https://account.live.com/proofs/Manage).

A fejlesztői központban regisztrált fiókjában elsősorban legyenek elérhetők a megbízható számítógépekről. Fontos hozzáférni a megbízható számítógépről, mert hetente fejlesztői központban regisztrált fiókjában létrehozott kódok száma korlátozva van. Megbízható számítógépek használatával is lehetővé teszi, hogy a legbiztonságosabb és egységes bejelentkezés során tapasztal élmény. 
*   További fejlesztői központ fiók irányelvek és biztonságával kapcsolatban további információkért látogasson el a helyen található egy fejlesztői fiók lap megnyitásakor [docs.microsoft.com/windows/uwp/publish/opening-a-developer-account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account). 

---  

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Probléma: Microsoft ID összevont Azure AD-tartományban  
A vállalati fiók is lehet összevont Azure Active Directory (Azure AD) révén. Ha megpróbál létrehozni egy vállalati e-mail címet, amely össze van vonva az Azure AD használatával a Microsoft-ID, majd hibaüzenet. Ha hibaüzenetet kap, majd érdemes egyeztetni az informatikai csapat fiókja össze van vonva az Azure AD keresztül megerősítéséhez. Az Azure AD összevont e-mail egy ismert probléma, névfeloldása működik, a Microsoft.  
*   Az Azure AD kapcsolatos további információkért látogasson el az Azure Active Directory dokumentációs oldal helyen [docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory).

A Microsoft azt javasolja, hogy a megoldás. Hozzon létre egy új e-mail címet az alábbi lépéseket követve a `outlook.com` tartományhoz, és hozzon létre egy szabályt, amely továbbítja a kommunikációt.  
1.  Nyissa meg a fiók létrehozása oldal, és kattintson a Get az új e-mail cím hivatkozás. 
    *   Regisztrálhat a Microsoft ID, látogasson el a helyen hozza létre lapra [signup.live.com/signup](https://signup.live.com/signup).  
2.  Hozzon létre az új e-mail címet, és adjon meg egy jelszót. Egy új Microsoft-ID és az e-mail postaládájuk a a `outlook.com` tartományt hoz létre. Továbbra is a regisztrációs folyamat során, amíg ez a fiók nincs létrehozva.  

    >[!IMPORTANT]
    >Egy e-mail címet vagy terjesztési lista, amely a Microsoft ID regisztrálja a fejlesztői központban regisztrált kell használnia. A Microsoft azt javasolja, hogy egy terjesztési listát segítségével távolítsa el a függőségi az egyéni felhasználók számára. Ha az e-mail cím vagy terjesztési lista nincs regisztrálva, majd regisztrálnia kell most.    

    >[!Important]
    >Ha az összes e-mail cím a `Microsoft` vállalati tartományhoz, akkor nem használhatja azt a fejlesztői központjában regisztrációs áll.  

3.  Miután létrehozta a Microsoft ID az Outlook e-mail címmel, jelentkezzen be az Outlook-postaládájához. Hozzon létre egy szabály továbbítás e-mailt. Az e-mailek továbbítása szabály telepítse át a minden e-maileket az Outlook-postaládájához abban a tartományban, a piactér fiók kezeléséhez létrehozott e-mail címre érkezett.  
    *   Jelentkezzen be az Outlook-postaládájához, látogasson el az Outlook oldalon található [outlook.live.com/owa](https://outlook.live.com/owa).  
    *   Szabályok továbbítási kapcsolatos további információkért látogasson el a szabályok használata az Outlook Web App automatikusan továbbítsa az üzenetek egy másik helyen található lapra [support.office.com/article/ Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed).  

1.  A továbbítási szabály küld e-mailek és a kommunikáció az Outlook e-mail fiók egy tartományhoz a vállalati regisztrált e-mail címre érkezett. A `outlook.com` e-mail címet kell használni a fejlesztői központban és a Cloud Partner portálra hitelesítéséhez.  

## <a name="next-steps"></a>További lépések
*   Látogasson el a [AppSource Publisher útmutató és az Azure piactér](./marketplace-publishers-guide.md) lap.  
 
---  
