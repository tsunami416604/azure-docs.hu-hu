---
title: Telepítési tervek – Azure Active Directory | Microsoft dokumentumok
description: Teljes körű útmutatást számos Azure Active Directory-képességek üzembe helyezéséhez.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17e6708225262349d56c6e261895882e9c31677f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262216"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory-üzembehelyezési tervek
Teljes körű útmutatást keres az Azure Active Directory (Azure AD) képességeinek üzembe helyezéséhez? Az Azure AD üzembe helyezési tervei bemutatják az üzleti értéket, a tervezési szempontokat és a gyakori Azure AD-képességek sikeres üzembe helyezéséhez szükséges működési eljárásokat.

A tervoldalak bármelyikén használja a böngésző Nyomtatás PDF formátumba funkcióját a dokumentáció naprakész offline verziójának létrehozásához.
## <a name="include-the-right-stakeholders"></a>A megfelelő érdekelt felek felvétele

Az új képességek üzembe helyezésének megkezdésekor fontos, hogy a szervezet kulcsfontosságú érdekelt feleit is felhasználja. Javasoljuk, hogy azonosítsa és dokumentálja azt a személyt vagy személyeket, akik az alábbi szerepköröket teljesítik, és működjön együtt velük annak meghatározásában, hogy részt vettek-e a projektben.  

A szerepkörök a következőket tartalmazhatják: 

|Szerepkör |Leírás |
|-|-|
|Végfelhasználói|A felhasználók reprezentatív csoportja, amelyre a képesség megvalósul. Gyakran előnézetben a változások egy kísérleti program.
|Informatikai támogatási vezető|Informatikai támogatási szervezet képviselője, aki az ügyfélszolgálati szempontból tud hozzájárulni a módosítás támogathatóságához.  
|Identity Architect vagy Az Azure globális rendszergazdája|Identitáskezelő csapat képviselője, aki annak meghatározásáért felelős, hogy ez a változás hogyan igazodik a szervezet alapvető identitáskezelési infrastruktúrájához.|
|Alkalmazás tulajdonosának tulajdonosa |Az érintett alkalmazás(ok) teljes vállalkozástulajdonosa, amely magában foglalhatja a hozzáférés kezelését.A módosítás felhasználói élményét és hasznosságát is megadhata a végfelhasználó szemszögéből.
|Biztonsági tulajdonos|A biztonsági csapat képviselője, aki aláírhatja, hogy a terv megfelel a szervezet biztonsági követelményeinek.|
|Compliance Manager|A szervezeten belül a vállalati, ipari vagy kormányzati követelményeknek való megfelelés biztosításáért felelős személy.|

**A részvétel szintjei a következők lehetnek:**

- **A**projektterv és az eredmények végrehajtásához 

- **A**projektterv és az eredmény pprovalja 

- **C**ontributor a projekttervhez és az eredményhez 

- **Én**nformed a projekt terv és az eredmény


## <a name="best-practices-for-a-pilot"></a>Legjobb gyakorlatok egy kísérleti
A pilóta lehetővé teszi, hogy tesztelje egy kis csoport, mielőtt bekapcsolja a képesség mindenki számára. Győződjön meg arról, hogy a tesztelés részeként a szervezeten belüli minden egyes használati esetet alaposan tesztel. A legjobb, ha a kísérleti felhasználók egy adott csoportját célozza meg, mielőtt ezt a szervezet egészére kiküldené.

Az első hullámban célozza meg az informatikai, a használhatósági és más megfelelő felhasználókat, akik tesztelhetik és visszajelzést adhatnak. Ezt a visszajelzést a felhasználóknak küldött kommunikáció és utasítások továbbfejlesztésére, valamint a támogatási személyzet által tapasztalt problémák típusainak továbbfejlesztésére kell felhasználni. 

A felhasználók nagyobb csoportjaira történő kiterjesztést a megcélzott csoport(ok) körének növelésével kell végrehajtani. Ez történhet [dinamikus csoporttagsággal,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)vagy a felhasználók manuális hozzáadásával a megcélzott csoport(ok)hoz.


## <a name="deploy-authentication"></a>Hitelesítés telepítése

| Képesség | Leírás|
| -| -|
| [Multi-Factor Authentication](https://aka.ms/deploymentplans/mfa)| Az Azure Multi-Factor Authentication (MFA) a Microsoft kétlépéses hitelesítési megoldása. A rendszergazda által jóváhagyott hitelesítési módszerek használatával az Azure MFA segít megőrizni az adatokhoz és alkalmazásokhoz való hozzáférést, miközben kielégíti az egyszerű bejelentkezési folyamat iránti igényt. |
| [Feltételes hozzáférés](https://aka.ms/deploymentplans/ca)| A Feltételes hozzáférés segítségével automatizált hozzáférés-vezérlési döntéseket hozhat arra vonatkozóan, hogy ki férhet hozzá a felhőalapú alkalmazásokhoz a feltételek alapján. |
| [Új jelszó önkiszolgáló kérése](https://aka.ms/deploymentplans/sspr)| Az önkiszolgáló jelszó-visszaállítás segítségével a felhasználók rendszergazdai beavatkozás nélkül, mikor és hol állíthatják vissza a jelszavukat. |
| [Jelszó nélküli](https://aka.ms/deploymentplans/passwordless) | Jelszó nélküli hitelesítés megvalósítása a Microsoft Authenticator alkalmazás vagy a FIDO2 biztonsági kulcsok használatával a szervezetben |

## <a name="deploy-application-management"></a>Alkalmazáskezelés telepítése

| Képesség | Leírás|
| -| - |
| [Egyszeri bejelentkezés](https://aka.ms/deploymentplans/sso)| Egyszeri bejelentkezéssel a felhasználók hozzáférhetnek az üzleti tevékenységhez szükséges alkalmazásokhoz és erőforrásokhoz, miközben csak egyszer jelentkeznek be. Miután bejelentkeztek, a Microsoft Office-tól a SalesForce-on át a Box-ba léphetnek anélkül, hogy másodszor is meg kell jenük adniuk a hitelesítő adatokat. |
| [Hozzáférési panel](https://aka.ms/deploymentplans/accesspanel)| A felhasználók számára egyszerű központot kínálhat az összes alkalmazás felfedezéséhez és eléréséhez. Lehetővé teszi számukra, hogy hatékonyabban önkiszolgáló funkciókkal, például az alkalmazásokhoz és csoportokhoz való hozzáférés kérésével, vagy az erőforrásokhoz való hozzáférés más nevében történő kezelésével hatékonyabbak legyenek. |


## <a name="deploy-hybrid-scenarios"></a>Hibrid forgatókönyvek telepítése

| Képesség | Leírás|
| -| -|
| [ADFS a jelszókivonat-szinkronizáláshoz](https://aka.ms/deploymentplans/adfs2phs)| A jelszókivonat-szinkronizálással a rendszer szinkronizálja a felhasználói jelszavak kivonatait a helyszíni Active Directoryból az Azure AD-be, így az Azure AD hitelesíti azokat a felhasználókat, akik nem tudnak interakciót a helyszíni Active Directoryval |
| [ADFS az átmenő hitelesítéshez](https://aka.ms/deploymentplans/adfs2pta)| Az Azure AD áthaladási hitelesítése segít a felhasználóknak, hogy jelentkezzen be mind a helyszíni, mind a felhőalapú alkalmazásokba ugyanazokat a jelszavakat használva. Ez a funkció jobb felhasználói élményt nyújt a felhasználóknak – eggyel kevesebb jelszót kell megjegyezniük –, és csökkenti az informatikai ügyfélszolgálati költségeket, mivel a felhasználók kevésbé valószínű, hogy elfelejtik a bejelentkezésmódját. Az Azure AD-vel való bejelentkezéskor a szolgáltatás közvetlenül a helyszíni Active Directoryban tárolt adatok alapján érvényesíti a felhasználói jelszavakat. |
| [Azure AD alkalmazásproxy](https://aka.ms/deploymentplans/appproxy)| Napjainkban a munkavállalók a legkülönfélébb helyeken, időpontokban és eszközökön szeretnek dolgozni. SaaS-alkalmazások eléréséhez a felhőben és a vállalati alkalmazások a helyszínen. Az Azure AD alkalmazásproxy lehetővé teszi ezt a robusztus hozzáférést költséges és összetett virtuális magánhálózatok (VPN) vagy demilitarizált zónák (DMZs) nélkül. |
| [Közvetlen egyszeri bejelentkezés](../hybrid/how-to-connect-sso-quick-start.md)| Az Azure Active Directory közvetlen egyszeri bejelentkezése (Azure AD közvetlen SSO) automatikusan bejelentkezteti a felhasználókat, ha azok a vállalati hálózatra csatlakozó vállalati eszközeiket használják. Ezzel a funkcióval a felhasználóknak nem kell beírniuk a jelszavukat az Azure AD-be való bejelentkezéshez, és általában nem kell megadniuk a felhasználónevüket. Ez a funkció egyszerű hozzáférést biztosít a jogosult felhasználók számára a felhőalapú alkalmazásokhoz anélkül, hogy további helyszíni összetevőkre lenne szükség. |

## <a name="deploy-user-provisioning"></a>Felhasználói kiépítés telepítése

| Képesség | Leírás|
| -| -|
| [Felhasználók regisztrálása](https://aka.ms/deploymentplans/userprovisioning)| Az Azure AD-vel automatizálhatja a felhasználói identitások létrehozását, karbantartását és eltávolítását a felhőalapú (SaaS-) alkalmazásokban, például a Dropboxban, a Salesforce-ban vagy a ServiceNow-ban. |
| [Felhőbeli HR-felhasználók kiépítése](https://aka.ms/deploymentplans/cloudhr)| Az Active Directoryba való felhőalapú HR-felhasználók kiépítése megteremti a folyamatos identitáskezelés alapjait, és javítja a mérvadó identitásadatokon alapuló üzleti folyamatok minőségét. Ha ezt a funkciót a felhőhr-termékkel, például a Workday-szel vagy a Successfactors-szal használja, zökkenőmentesen kezelheti az alkalmazottak és a függő dolgozók identitásának életciklusát a Joiner-Mover-Leaver folyamatokat (például Új bérlés, Megszüntetés, Átvitel) informatikai kiépítési műveletekre (például létrehozás, engedélyezés, letiltás) |

## <a name="deploy-governance-and-reporting"></a>Irányítás és jelentéskészítés telepítése

| Képesség | Leírás|
| -| -|
| [Privileged Identity Management](https://aka.ms/deploymentplans/pim)| Az Azure AD emelt szintű identitáskezelés (PIM) segítségével kezelheti a kiemelt felügyeleti szerepköröket az Azure AD, az Azure-erőforrások és más Microsoft Online Services szolgáltatásban. A PIM olyan megoldásokat kínál, mint a just-in-time hozzáférés, a jóváhagyási munkafolyamatok kérése és a teljes mértékben integrált hozzáférési felülvizsgálatok, így valós időben azonosíthatja, felfedheti és megakadályozhatja a kiemelt szerepkörök rosszindulatú tevékenységeit. |
| [Jelentéstétel és nyomon követés](https://aka.ms/deploymentplans/reporting)| Az Azure AD jelentéskészítési és figyelési megoldás ának kialakítása a jogi, biztonsági és működési követelményektől, valamint a meglévő környezettől és folyamatoktól függ. Ez a cikk bemutatja a különböző tervezési lehetőségeket, és végigvezeti a megfelelő telepítési stratégia. |
