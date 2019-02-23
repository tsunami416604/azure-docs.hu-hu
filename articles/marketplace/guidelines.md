---
title: Útmutató az Azure Marketplace és AppSource publisher |} Az Azure
description: Irányelvek Azure Marketplace és appsource-ban az alkalmazás és szolgáltatás-közzétevők
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
ms.openlocfilehash: a0ab88b1375f5178ca6f41689de8d2f30f8d7808
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728131"
---
# <a name="guidelines"></a>Irányelvek  

<!--
## Guidelines for AppSource  
-->
---

## <a name="guidelines-for-azure-marketplace"></a>Útmutató az Azure Marketplace-en  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>A marketplace-fiók kezelése a Microsoft ID létrehozására vonatkozó irányelvek  
Ha egynél több személy a marketplace-fiók létrehozásához használja az ugyanazon Microsoft ID hozzáférésre van szüksége, majd kövesse ezeket az irányelveket hozhat létre egy munkahelyi fiókot. 

>[!IMPORTANT]
>Engedélyezéséhez több felhasználó számára a Microsoft Developer Center (Dev Center) fiókjába, a Microsoft azt javasolja, hogy az Azure Active Directory (Azure AD) használatával szerepköröket rendelhet felhasználókhoz. Minden felhasználó a bejelentkezés után egyes-fiókja eléréséhez kell az Azure AD hitelesítő adatait. Az e-mail-cím egy tartományban hozzon létre a Microsoft ID regisztrálva a vállalatnál a Microsoft azt javasolja, hogy az e-mailt nem lehet hozzárendelni egy egyéni. Például: `windowsapps@fabrikam.com`.  
>*   További információért látogasson el a [problémát: Az Azure AD-Microsoft ID összevont tartomány](#issue-microsoft-id-in-an-azure-ad-federated-domain) szakaszban.  

*   A Microsoft ID, a lehető legkevesebb fejlesztők való hozzáférés korlátozásához. 
*   Állítsa be a vállalati e-mailek terjesztési lista, amely tartalmazza az mindenki, aki a fejlesztői központban lévő fiókjához kell elérniük. Adja hozzá a terjesztési listához e-mail címet biztonsági adatait. A terjesztési listához lehetővé teszi, hogy az alkalmazottak a listán szereplő megkapni a biztonsági kódokat, ha a kért és kezelése a Microsoft ID. tartozó biztonsági információkat az összes Ha a terjesztési lista beállítása nem megvalósítható, az egyéni e-mail fiók tulajdonosának hozzáférni és megosztani azokat a biztonsági kódot, amikor a rendszer kéri a rendelkezésre álló kell.  
    *   A tulajdonos például csak akkor kell megadnia, ha új biztonsági adatok bekerül a Microsoft ID vagy a Microsoft ID új eszközökről való hozzáféréskor.  
*   Adjon hozzá egy munkahelyi telefonszám, amely nem igényel bővítmény és key csapat tagjai számára elérhető.  
*   Általában érdemes beállítani a fejlesztők megbízható eszközök használatával jelentkezzen be a fejlesztői központban lévő fiókjához. Az összes fő csapat tagjai a megbízható eszközökhöz való hozzáféréssel kell rendelkeznie. Megbízható eszközökkel való elérésében kevesebb küldi a biztonsági kódokat, amikor valaki a fejlesztői központban lévő fiókjához fér hozzá.  
*   Ha a hozzáférési jogot a fejlesztői központban lévő fiókjához egy nem megbízható számítógép szükséges, majd korlátozza legfeljebb öt fejlesztők számára a hozzáférést. Ideális esetben a fejlesztők kell eléréséhez a fióknak számítógépeket, amelyek azonos földrajzi és a hálózati hely.  
*   Gyakran tekintse át, és ellenőrizni biztonsági adatait.  
    *   Saját biztonsági adatok megtekintéséhez látogasson el a biztonsági beállítások lapon található [account.live.com/proofs/Manage](https://account.live.com/proofs/Manage).

A fejlesztői központban lévő fiókjához kell elsősorban elérhető megbízható számítógépekhez. Rendkívül fontos elérését megbízható számítógépekhez, mert kiszolgálónként hetente fejlesztői központban regisztrált fiókjában létrehozott kódok száma korlátozva van. Megbízható számítógépek használatával is lehetővé teszi, hogy a legbiztonságosabb és egységes bejelentkezési élményére. 
*   További fejlesztői központ fiók irányelvek és biztonságával kapcsolatban további információkért látogasson el a helyen található egy fejlesztői fiók lap megnyitásakor [docs.microsoft.com/windows/uwp/publish/opening-a-developer-account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account). 

---

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Probléma: Az Azure AD összevont tartományban Microsoft ID  
Az Ön vállalati fiókjával is összevonni kezeli az Azure Active Directory (Azure AD). Ha megpróbál létrehozni egy Microsoft-ID használatával egy vállalati e-mail-címet, amely össze van vonva az Azure ad-vel, majd hibaüzenetet kap. Ha hibaüzenetet kap, majd ellenőrizze az informatikai csapat annak ellenőrzéséhez, hogy a fiók össze van vonva az Azure AD-n keresztül. Az Azure AD összevont e-mailben egy ismert probléma, a Microsoft dolgozik azon, hogy névfeloldása.  
*   Azure AD-ről további információért látogasson el az Azure Active Directory dokumentációs oldalon található [docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory).

A Microsoft azt javasolja, hogy egy megkerülő megoldás. Hozzon létre egy új e-mail címet az alábbi lépéseket követve a `outlook.com` tartományhoz, és hozzon létre egy szabályt, amely továbbítja a kommunikáció.  
1.  Nyissa meg a fiók létrehozása oldal, és kattintson a Get egy új e-mail cím hivatkozást. 
    *   Regisztráljon a Microsoft ID, látogasson el a létrehozás-fiók oldalán található [signup.live.com/signup](https://signup.live.com/signup).  
2.  Hozzon létre az új e-mail címet, és adjon meg egy jelszót. Egy új Microsoft-ID és a egy e-mail-postaládával a `outlook.com` tartományt hoz létre. Elindítjuk a regisztráció folyamatát, mindaddig, amíg a fiók létrejön.  

    >[!IMPORTANT]
    >Egy e-mail-cím vagy terjesztési lista, amely regisztrálva van egy Microsoft ID a fejlesztői központban való regisztrálásához kell használnia. A Microsoft javasolja, hogy egy terjesztési lista függőség eltávolítása egyéni felhasználók számára. Ha az e-mail-cím vagy terjesztési lista nincs regisztrálva, majd regisztrálnia kell most.    

    >[!Important]
    >Ha az összes e-mail-cím található a `Microsoft` vállalati tartományt, majd Ön nem használhatja azt a regisztráció, a fejlesztői központban.  

3.  Miután létrehozta a Microsoft ID az Outlook e-mail-címmel, jelentkezzen be az Outlook-postaládájához. Hozzon létre szabályt továbbítás e-mailt. Az e-mailben továbbítási szabály helyezze át az összes e-mailek, az Outlook postaládájában az e-mail-címre, a tartományban létrehozta a marketplace-fiók kezelése kapott.  
    *   Jelentkezzen be az Outlook-postaládájához, látogasson el az Outlook oldal található [outlook.live.com/owa](https://outlook.live.com/owa).  
    *   Továbbítási szabályok kapcsolatos további információkért látogasson el az Outlook Web App, egy másik fiók oldalán található üzenetek automatikus továbbítása a szabályok használata [support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed).  

1.  A továbbítási szabályt küld minden e-mailek és az Outlook e-mail-fiók olyan tartományban, a vállalat regisztrált e-mail címre kapott kommunikációt. A `outlook.com` e-mail cím Dev Center és a Cloud Partner portálra is hitelesítést kell használni.  

## <a name="next-steps"></a>További lépések
*   Látogasson el a [Azure Marketplace és AppSource útmutató Közzétevőknek](./marketplace-publishers-guide.md) lapot.  
 
---
