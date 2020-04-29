---
title: Útmutató az Azure Marketplace-hez és a AppSource-közzétevőhöz | Azure
description: Útmutató az Azure Marketplace-hez és a AppSource az alkalmazások és szolgáltatások közzétevői számára
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/13/2018
ms.author: dsindona
ms.openlocfilehash: 9156126ff2a3dbe936dc426c6f5097c6bba7b7b7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80387605"
---
# <a name="guidelines"></a>Irányelvek  

<!--
## Guidelines for AppSource  
-->
---

## <a name="guidelines-for-azure-marketplace"></a>Útmutató az Azure Marketplace-hez  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Útmutató Microsoft-azonosító létrehozásához piactér-fiók kezeléséhez  
Ha egynél több személy számára szükséges a piactér-fiók létrehozásához használt Microsoft-AZONOSÍTÓhoz való hozzáférés, kövesse az alábbi irányelveket a vállalati fiók létrehozásához. 

>[!IMPORTANT]
>Annak engedélyezéséhez, hogy több felhasználó hozzáférhessen a Microsoft fejlesztői központ (fejlesztői központ) fiókjához, a Microsoft javasolja, hogy az Azure Active Directory (Azure AD) használatával rendeljen hozzá szerepköröket az egyes felhasználókhoz. Minden felhasználónak hozzá kell férnie a fiókhoz az egyes Azure AD-beli hitelesítő adatokkal való bejelentkezéssel. Hozza létre a Microsoft ID-t a vállalata Microsoft által regisztrált tartomány e-mail-címének használatával, ami azt jelenti, hogy az e-mailt nem rendeli hozzá a rendszer. Például: `windowsapps@fabrikam.com`.  
>*   További információ [: Microsoft ID egy Azure ad összevont tartomány](#issue-microsoft-id-in-an-azure-ad-federated-domain) szakaszban.  

*   Korlátozza a Microsoft ID elérését a lehető legkisebb számú fejlesztőhöz. 
*   Állítson be egy vállalati e-mail-terjesztési listát (DL), amely tartalmazza mindazokat, akiknek hozzá kell férniük a Fejlesztői központ fiókjához. Adja hozzá a DL e-mail-címét a biztonsági adataihoz. A DL lehetővé teszi, hogy a listán szereplő összes alkalmazott biztonsági kódokat kapjon a kéréskor, valamint a Microsoft-azonosító biztonsági adatainak kezeléséhez. Ha a terjesztési lista beállítása nem valósítható meg, akkor az egyéni e-mail-fiók tulajdonosának elérhetőnek kell lennie a hozzáféréshez és a biztonsági kód megosztásához, amikor a rendszer kéri.  
    *   A rendszer a tulajdonost például arra kéri, hogy új biztonsági adatokat adjanak hozzá a Microsoft ID-hez, vagy ha a Microsoft-azonosító új eszközről érhető el.  
*   Adjon hozzá egy olyan vállalati telefonszámot, amelyhez nincs szükség bővítményre, és elérhető a legfontosabb csapattagok számára.  
*   Általánosságban megkövetelheti, hogy a fejlesztők megbízható eszközöket használjanak a Fejlesztői központ fiókjába való bejelentkezéshez. Az összes kulcsfontosságú csapat tagjának hozzáféréssel kell rendelkeznie a megbízható eszközökhöz. A megbízható eszközök hozzáférésének használata csökkenti a biztonsági kódok küldésének követelményét, ha valaki hozzáfér a Fejlesztői központ fiókjához.  
*   Ha nem megbízható számítógépről kell hozzáférést biztosítania a Fejlesztői központ fiókjához, akkor legfeljebb öt fejlesztőnek kell korlátoznia a hozzáférést. Ideális esetben a fejlesztőknek el kell érniük a fiókot azokról a számítógépekről, amelyek ugyanazt a földrajzi és hálózati helyet használják.  
*   A biztonsági adatok gyakori áttekintése és ellenőrzése.  
    *   A biztonsági adatok megtekintéséhez látogasson el a biztonsági beállítások lapra, amely a következő helyen található: [Account.Live.com/Proofs/Manage](https://account.live.com/proofs/Manage).

A Fejlesztői központ fiókjának elsődlegesen megbízható számítógépekről kell elérhetőnek lennie. Kritikus fontosságú, hogy a megbízható számítógépektől hozzáférjen, mivel a Fejlesztői központ által létrehozott kódok száma hetente van korlátozva. A megbízható számítógépek használata a legbiztonságosabb és egységes bejelentkezési élményt is lehetővé teszi. 
*   További információ a Fejlesztői központ fiókokra vonatkozó irányelveiről és biztonságáról: [docs.microsoft.com/Windows/uwp/publish/Opening-a-Developer-Account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account)címen található fejlesztői fiók megnyitása oldalon. 

---

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Probléma: Microsoft ID egy Azure AD összevont tartományban  
Előfordulhat, hogy a vállalati fiók összevont Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel összevont vállalati e-mail-címmel próbál létrehozni egy Microsoft-azonosítót, hibaüzenetet kap. Ha hibaüzenetet kap, ellenőrizze az IT-csapattal, hogy a fiókját az Azure AD-ben összevonta-e. Az Azure AD összevont e-mailek ismert probléma, és a Microsoft dolgozik a megoldásban.  
*   Az Azure AD-vel kapcsolatos további információkért látogasson el a Azure Active Directory dokumentációs oldalra, amely a következő címen található: [docs.microsoft.com/Azure/Active-Directory](https://docs.microsoft.com/azure/active-directory).

A Microsoft megkerülő megoldást javasol. Kövesse az alábbi lépéseket egy új e-mail-cím `outlook.com` létrehozásához a tartományban, és hozzon létre egy szabályt a kommunikáció továbbításához.  
1.  Lépjen a fiók létrehozása lapra, és kattintson az új e-mail-cím beolvasása hivatkozásra. 
    *   Ha regisztrálni szeretné a Microsoft-AZONOSÍTÓját, látogasson el a fiók létrehozása lapra a következő helyen: [signup.Live.com/signup](https://signup.live.com/signup).  
2.  Hozza létre az új e-mail-címet, és adjon meg egy jelszót. Létrejön egy új Microsoft-azonosító és egy e- `outlook.com` mail-postaláda a tartományban. A fiók létrehozása előtt folytassa a regisztrációs folyamatot.  

    >[!IMPORTANT]
    >A fejlesztői központban regisztrálni kívánt e-mail-címet vagy terjesztési listát kell használnia, amely Microsoft-AZONOSÍTÓként van regisztrálva. A Microsoft azt javasolja, hogy terjesztési lista használatával távolítsa el az egyéni függőségeket. Ha az e-mail-cím vagy a terjesztési lista nincs regisztrálva, akkor regisztrálnia kell most.    

    >[!Important]
    >Ha e-mail-címe a `Microsoft` vállalati tartományban található, akkor nem használhatja azt a fejlesztői központban való regisztrációhoz.  

3.  Miután létrehozta a Microsoft ID-t az Outlook e-mail-címével, jelentkezzen be az Outlook-postaládába. Hozzon létre egy e-mail továbbítási szabályt. Az e-mail továbbítási szabálynak az Outlook-postaládában fogadott összes e-mail-címet át kell helyeznie a saját tartományában, amelyet a piactér-fiók kezeléséhez hozott létre.  
    *   Az Outlook-postaládába való bejelentkezéshez látogasson el a [Outlook.Live.com/OWA](https://outlook.live.com/owa)címen található Outlook-oldalra.  
    *   A továbbítási szabályokkal kapcsolatos további információkért tekintse meg a használati szabályok az Outlook Web App alkalmazásban című témakört, amely automatikusan továbbítja az üzeneteket a [support.Office.com/article/use-Rules-in-Outlook-Web-App-to-automatically-Forward-messages-to-another-Account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed)címen található másik fiók oldalára.  

1.  A továbbítási szabály az Outlook e-mail-fiókjában kapott összes e-mailt és kommunikációt elküldi az e-mail-címre a vállalatnál regisztrált tartományban. Az `outlook.com` e-mail-címét a fejlesztői központban és a Cloud Partner Portalban történő hitelesítéshez kell használni.  

## <a name="next-steps"></a>További lépések

*   Látogasson el az [Azure Marketplace és a AppSource kiadói útmutató](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) oldalára. 
 
---
