---
title: "Az Azure Active Directoryban egyéni tartománynevek elméleti áttekintése |} Microsoft Docs"
description: "Ismerteti a fogalmi keretet biztosít az Azure Active Directoryban, beleértve az egyszeri bejelentkezés összevonási egyéni tartománynevek használatával"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: fd0c5def-0da2-43af-81bc-76f4cfe86afd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: curtand
ms.openlocfilehash: 3c591680160101a91174868714392674c9aa7178
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="conceptual-overview-of-custom-domain-names-in-azure-active-directory"></a>Az Azure Active Directoryban egyéni tartománynevek elméleti áttekintése
A tartomány neve lehet sok címtárerőforrásokkal fontos azonosítója részeként:

* A felhasználói név vagy e-mail cím egy felhasználó számára
* A cím egy csoporthoz
* A app ID URI az alkalmazáshoz

Az Azure Active Directory (Azure AD) erőforrás neve, a rendszer már ellenőrzi a könyvtárban, amely tartalmazza az erőforrás tulajdonosa lehet tartalmazhatnak. Csak egy globális rendszergazda Azure AD-ben végrehajthat tartomány feladatokat.

> [!IMPORTANT]
> A Microsoft javasolja, hogy az Azure Portalon található [Azure AD felügyeleti központból](https://aad.portal.azure.com) kezelje az Azure AD-t az ebben a cikkben javasolt klasszikus Azure portál helyett. Az Azure AD felügyeleti központban a tartománynevek kezelése című történő [az Azure Active Directoryban egyéni tartománynevek kezelése](active-directory-domains-manage-azure-portal.md).

Az Azure AD-tartománynevek nem globálisan egyedi. Egy egyéni tartománynevet egyszerre csak egy Azure AD-bérlő által is használt. Ha ellenőrizte, hogy az Azure AD-címtár egy tartomány nevét, majd más Azure AD-címtár is győződjön meg arról, vagy használja ugyanazt a tartománynevet.

## <a name="initial-and-custom-domain-names"></a>Kezdeti és egyéni tartománynevek
Minden Azure AD-ben tartománynév, vagy egy kezdeti tartománynevet, vagy egy egyéni tartománynevet.

Minden Azure AD egy kezdeti tartománynevet az űrlap contoso.onmicrosoft.com tartalmaz. A harmadik szintű tartománynevet, ebben a példában "contoso.onmicrosoft.com," a címtár létrehozása után általában a rendszergazda, aki létrehozta a könyvtár által jött létre. A címtár eredeti tartománynevét nem módosítható vagy törölhető. A kezdeti tartománynév teljesen működőképes, amíg olyan elsősorban használható bootstrapping mechanizmusaként, amíg az egyéni tartománynév ellenőrzése.

A legtöbb éles környezetben egy könyvtárat legalább egy ellenőrzött és érvényes egyéni tartománnyal rendelkezik, például "contoso.com", és látható a végfelhasználók számára, hogy egyéni tartományhoz. Egy egyéni tartománynév megadása egy tartomány nevét, amely birtokolt, és használják az adott szervezet, például "contoso.com", például a webhely üzemeltetési célból. Ez a tartománynév nem ismeri a dolgozók, mert része a felhasználó nevét, jelentkezzen be a vállalati hálózathoz, vagy amelyekkel küldhessen és kérdezhessen le az e-mailek.

Az Azure AD által használt, az egyéni tartománynevet kell ad a címtárhoz és ellenőrzése.

## <a name="verified-and-unverified-domain-names"></a>Ellenőrzött és nem ellenőrzött tartomány nevét
A címtár eredeti tartománynevét implicit módon értékeli az Azure AD által ellenőrzött. Ha a rendszergazda az Azure AD hozzáadja egy egyéni tartománynevet, először egy nem ellenőrzött állapotban van. Az Azure AD nem engedélyezi a bármely directory erőforrásokat egy nem ellenőrzött tartomány nevét. Ez biztosítja, hogy csak egy címtárban használhat egy adott tartomány nevét, és, hogy a szervezetnek a nevét használja ténylegesen tulajdonosa a tartománynevet.

Az Azure AD egy tartománynevet tulajdonjogát keresi, a tartomány nevét (DNS) zóna fájlban a tartománynév egy adott belépési ellenőrzi. Tartománynév tulajdonjogát ellenőrzéséhez egy rendszergazda lekérése a DNS-bejegyzést az Azure AD, hogy az Azure AD keresi, és a tartománynév DNS zónafájljában ad hozzá a bejegyzést. A DNS-zónafájlját az adott tartomány regisztrációs tartja fenn. A tartomány ellenőrzésének lépéseit megjelennek-e a cikket [az egyéni tartománynév hozzáadása az Azure AD-címtár](active-directory-add-domain.md).

A zóna fájlt a tartománynévhez tartozó DNS-bejegyzés hozzáadása nem befolyásolja a más tartományi szolgáltatások, például az e-mailben vagy webtároláshoz.

## <a name="federated-and-managed-domain-names"></a>Összevont és a kezelt tartománynevek
Az Azure AD egy egyéni tartománynevet beállítható úgy, hogy biztosítsa a felhasználók számára egy összevont bejelentkezési élményt nyújt a helyszíni Active Directory és az Azure AD között. Frissítések privilegizált erőforrásokhoz az Azure ad-ben és a Windows Server Active Directory összevonási tartománynév beállítása szükséges. Egy összevont tartományt el kell végezni az Azure AD Connect konfigurálása, vagy a PowerShell használatával. Egyéni tartomány összevonását nem kezdeményezhető, a klasszikus Azure portálon. [További információt az AD FS felhasználói bejelentkezés az Azure AD Connect konfigurálása a videó](http://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect).

Tartományok, amelyek nem összevont felügyelt tartományok is nevezik. Az Azure AD-címtár kezdeti tartomány egy felügyelt tartomány implicit módon történik.

## <a name="primary-domain-names"></a>Elsődleges tartománynevek
A elsődleges neve (könyvtár) a tartománynév, amely előre kiválasztott, akár az alapérték a "" tartományrésze a felhasználónevet, amikor a rendszergazda létrehoz egy új felhasználó a [Azure-portálon](https://portal.azure.com/), vagy egy másik portált, mint a Office 365 felügyeleti portál vagy a Microsoft Intune-portál. A könyvtár csak egy elsődleges tartománynév lehet. A rendszergazda módosíthatja bármely egyéni tartományt, amely nem össze van vonva, ellenőrizni kell az elsődleges tartománynevet, vagy a kezdeti tartomány.

## <a name="domain-names-in-azure-ad-and-other-microsoft-online-services"></a>Az Azure AD-tartománynevek és más Microsoft Online Services
Az Azure AD egy másik Microsoft Online szolgáltatás például az Exchange Online, SharePoint online-hoz és a Intune használata előtt ellenőrizni kell a tartomány nevét. Ezek a szolgáltatások használatához általában szükség van egy vagy több DNS-bejegyzéseit, amelyek a szolgáltatás adott hozzá a rendszergazda.

Azure-webalkalmazás ellenőrzésére tartomány tulajdonjogát a saját mechanizmust alkalmaz. Egy tartomány ellenőrizni kell az Azure ad-vel használja akkor is, ha korábban ellenőrzését használható Azure-webalkalmazás egy előfizetésben található, amely támaszkodik, hogy az Azure AD által. Azure-webalkalmazás egy tartomány nevét, amely egy másik könyvtár azon címtárból, amellyel biztonságossá teszi a webalkalmazás ellenőrzése után használhatja.

## <a name="managing-domain-names"></a>Tartománynevek kezelése
Tartományi felügyeleti feladatok is elvégezhető, a klasszikus Azure portálra, és a Powershellből. Számos feladatot az Azure AD Graph API segítségével kell végrehajtani.

* [Hozzáadásával és az egyéni tartománynév ellenőrzése](active-directory-add-domain.md)
* [A klasszikus Azure portálon tartományok kezelése](active-directory-add-manage-domain-names.md)
* [Tartománynevek kezelése az Azure AD PowerShell segítségével](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Az Azure AD Graph API segítségével tartománynevek kezelése az Azure ad-ben](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

