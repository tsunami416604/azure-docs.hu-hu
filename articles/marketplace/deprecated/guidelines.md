---
title: Az Azure Piactér és az AppSource-közzétevő irányelvei | Azure
description: Az Azure Piactér és az AppSource irányelvei az alkalmazás- és szolgáltatásközzétevők számára
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/13/2018
ms.author: dsindona
ms.openlocfilehash: 9156126ff2a3dbe936dc426c6f5097c6bba7b7b7
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387605"
---
# <a name="guidelines"></a>Irányelvek  

<!--
## Guidelines for AppSource  
-->
---

## <a name="guidelines-for-azure-marketplace"></a>Az Azure Piactér irányelvei  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>A piactéri fiók kezeléséhez létrehozott Microsoft ID-k használatának irányelvei  
Ha egynél több személynek van szüksége a piactéri fiók létrehozásához használt Microsoft-azonosítóhoz való hozzáférésre, akkor kövesse az alábbi irányelveket a vállalati fiók létrehozásához. 

>[!IMPORTANT]
>Ahhoz, hogy több felhasználó számára is elérhető a Microsoft Developer Center (Dev Center) fiók, a Microsoft azt javasolja, hogy az Azure Active Directory (Azure AD) szerepkörök hozzárendelése az egyes felhasználók hoz. Minden felhasználónak hozzá kell férnie a fiókhoz, ha egyedi Azure AD-hitelesítő adatokkal jelentkezik be. Hozza létre Microsoft-azonosítóját egy, a vállalata által regisztrált tartományban található e-mail-cím használatával, a Microsoft azt javasolja, hogy az e-mailt ne rendeljék hozzá egy magánszemélynek. Például: `windowsapps@fabrikam.com`.  
>*   További információért keresse fel a [Probléma: Microsoft-azonosító egy Azure AD összevont tartomány szakaszban.](#issue-microsoft-id-in-an-azure-ad-federated-domain)  

*   Korlátozza a Microsoft-azonosítóhoz való hozzáférést a lehető legkevesebb fejlesztőre. 
*   Hozzon létre egy vállalati e-mail terjesztési listát (DL), amely tartalmazza mindenki, akinek hozzá kell férnie a fejlesztői központ fiókjához. Adja hozzá a DL e-mail címet a biztonsági adatokhoz. A dl lehetővé teszi, hogy a listán szereplő összes alkalmazott szükség esetén biztonsági kódokat kapjon, és kezelje a Microsoft-azonosító biztonsági adatait. Ha a terjesztési lista beállítása nem valósítható meg, akkor az egyes e-mail fiók tulajdonosának elérhetőnek kell lennie a biztonsági kód eléréséhez és megosztásához, amikor a rendszer kéri.  
    *   A rendszer például a tulajdonost kéri, amikor új biztonsági adatokat ad hozzá a Microsoft ID azonosítóhoz, vagy amikor a Microsoft-azonosítót új eszközről érik el.  
*   Adjon hozzá egy olyan vállalati telefonszámot, amelyhez nincs szükség bővítményre, és amely a csapat kulcstagjai számára elérhető.  
*   Általában meg kell követelnie a fejlesztőktől, hogy megbízható eszközöket használjanak a fejlesztői központ-fiókba való bejelentkezéshez. A csapat minden kulcsfontosságú tagjának hozzáféréssel kell rendelkeznie a megbízható eszközökhöz. A megbízható eszközök eléréséhez csökkenti a biztonsági kódok küldésének követelményét, amikor valaki a fejlesztői központ-fiókhoz fér hozzá.  
*   Ha nem megbízható számítógépről kell hozzáférést biztosítania a fejlesztői központ-fiókhoz, akkor legfeljebb öt fejlesztőre kell korlátoznia a hozzáférést. Ideális esetben a fejlesztőknek azonos földrajzi és hálózati helyű számítógépekről kell hozzáférniük a fiókhoz.  
*   Gyakran tekintse át és ellenőrizze a biztonsági adatokat.  
    *   A biztonsági információk megtekintéséhez látogasson el a [account.live.com/proofs/Manage](https://account.live.com/proofs/Manage).

A fejlesztői központ-fiók elsősorban megbízható számítógépekről érhető el. Fontos, hogy megbízható számítógépekről férjen hozzá, mert a fejlesztői központ-fiókonként hetente generált kódok száma korlátozva van. A megbízható számítógépek használata a legbiztonságosabb és legkonzisztensebb bejelentkezési élményt is lehetővé teszi. 
*   A fejlesztői központ fiókkal kapcsolatos további irányelveiről és biztonságáról a fejlesztői fiók megnyitása lapon talál [docs.microsoft.com/windows/uwp/publish/opening-a-developer-account.](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account) 

---

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Probléma: Microsoft-azonosító Azure AD összevont tartományban  
Előfordulhat, hogy a vállalati fiók az Azure Active Directoryn (Azure AD) keresztül kerül összevonásra. Ha az Azure AD-vel összevont vállalati e-mail-cím használatával próbál meg Microsoft-azonosítót létrehozni, hibaüzenetet kap. Ha hibaüzenetet kap, akkor kérdezze meg informatikai csapatát, hogy a fiók összevan-e vonva az Azure AD-n keresztül. Az Azure AD összevont e-mail egy ismert probléma, és a Microsoft dolgozik annak megoldásán.  
*   Az Azure AD-ről további információt az Azure Active Directory dokumentációs lapján [talál,](https://docs.microsoft.com/azure/active-directory)amely a docs.microsoft.com/azure/active-directory .

A Microsoft egy kerülő megoldást javasol. Az alábbi lépésekkel új e-mail címet hozhat létre a tartományban, `outlook.com` és létrehozhat egy szabályt a kommunikáció továbbítására.  
1.  Nyissa meg a Fiók létrehozása lapot, és kattintson az Új e-mail cím beküldése hivatkozásra. 
    *   A Microsoft-azonosító regisztrálásához látogasson el a [fiók](https://signup.live.com/signup)létrehozása lapra, amely a signup.live.com/signup .  
2.  Hozza létre az új e-mail címet, és adjon meg egy jelszót. Létrejön egy új Microsoft-azonosító `outlook.com` és egy e-mail postaláda a tartományban. Folytassa a regisztrációs folyamatot a fiók létrehozásáig.  

    >[!IMPORTANT]
    >A fejlesztői központban való regisztrációhoz microsoftos azonosítóként regisztrált e-mail címet vagy terjesztési listát kell használnia. A Microsoft azt javasolja, hogy terjesztési lista használatával távolítsa el az egyénektől való függőséget. Ha az e-mail címe vagy a terjesztési listája nincs regisztrálva, akkor most kell regisztrálnia.    

    >[!Important]
    >Ha bármely e-mail-cím `Microsoft` található a vállalati tartományban, majd nem tudja használni a regisztrációs a fejlesztői központban.  

3.  Miután létrehozta a Microsoft-azonosítót az Outlook e-mail címével, jelentkezzen be az Outlook-postaládába. Hozzon létre egy e-mail továbbítási szabályt. Az e-mail továbbítási szabály nak át kell helyeznie az Outlook-postaládában kapott összes e-mailt a piactéri fiók kezeléséhez létrehozott tartománye e-mail címére.  
    *   Az Outlook-postaládába való bejelentkezéshez látogasson el a [outlook.live.com/owa](https://outlook.live.com/owa).  
    *   A továbbítási szabályokról az Outlook Web App szabályok használata című oldalán talál, ha az üzeneteket automatikusan továbbítani szeretné egy másik, [support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed)található fióklapra.  

1.  A továbbítási szabály az Outlook e-mail fiókjában kapott összes e-mailt és kommunikációt a vállalata által regisztrált tartomány e-mail címére küldi. Az `outlook.com` e-mail címét a fejlesztői központban és a felhőpartneri portálon való hitelesítéshez kell használni.  

## <a name="next-steps"></a>További lépések

*   Keresse fel az Azure Piactér és az [AppSource Publisher Guide](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) lapot. 
 
---
