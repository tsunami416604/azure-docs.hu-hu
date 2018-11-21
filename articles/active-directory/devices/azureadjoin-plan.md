---
title: Az Azure Active Directory (Azure AD) való csatlakozás megvalósítás tervezése |} A Microsoft Docs
description: Ismerteti a lépéseket, amelyekre szükség van a megvalósítása az Azure AD-hez csatlakoztatott eszközök a környezetben.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.component: devices
ms.assetid: 81d4461e-21c8-4fdd-9076-0e4991979f62
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: fdc8b5db9316e463f8ec46ca5e235ea53cf44265
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275972"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Útmutató: az Azure AD join implementáció megtervezésébe


Az Azure AD join eszközök csatlakoztatása közvetlenül az Azure ad-ben csatlakoztatása a helyszíni Active Directory, miközben gondoskodik a felhasználók számára a hatékony és biztonságos nélkül teszi lehetővé.  

Ez a cikk feltételezi, hogy az Azure ad-vel ismerete és [Eszközállapotok az Azure ad-ben](overview.md). 

 

## <a name="review-your-scenarios"></a>A forgatókönyvek áttekintése 

Bár az Azure AD-csatlakozás egy szervezeti beállítás a legtöbb esetben a alkalmazni, előfordulhat, hogy egyes használati esetek, amikor érdemes lehet inkább üzembe helyezése hibrid Azure AD-csatlakozás lehet. Az Azure AD join a nagyvállalatok igényeire felkészített ipari méretekben vagy -hatókörön központi telepítésekhez. 

 
Fontolja meg az Azure AD-csatlakozás a szervezet számára a következő feltételek alapján:  

- A terv és a Windows-eszközök többsége rendelkezik a Windows 10-es. 

- Azt tervezi, hogy helyezze át a felhő alapú üzembe helyezési és a Windows-eszközök kezelése. 

- A helyszíni infrastruktúra korlátozott, vagy tervezi, hogy csökkenti a helyszíni adatmennyiséget. 

- A csoportházirendek eszközök kezeléséhez nincs túlzott függőséget. 

- A gép Active Directory-hitelesítést használó örökölt alkalmazások nem rendelkeznek. 

- Az összes vagy kiválasztott felhasználók a szervezet nem rendelkezik a szükséges alkalmazások és erőforrások elérésére a vállalati hálózaton kell.  

 

A forgatókönyvek alapján a következő értékelés áttekintése  

 

## <a name="assess-infrastructure"></a>Infrastruktúra felmérése  

 

### <a name="users"></a>Felhasználók 

A felhasználók jönnek létre a helyszíni Active Directoryban, szükség esetén az Azure AD-n keresztül az Azure AD Connect szinkronizálja. Felhasználók szinkronizálása kapcsolatos további információkért lásd: [Mi az Azure AD Connect?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity#what-is-azure-ad-connect) 

Ha a felhasználók közvetlenül az Azure AD-ban, nincs további telepítő nem szükséges 

 

Másodlagos felhasználói azonosítók nem támogatottak az Azure AD-csatlakoztatott eszközökön. A felhasználóknak az Azure ad-ben rendelkezniük kell egy érvényes egyszerű Felhasználónévvel.  

 

### <a name="identity-infrastructure"></a>Identitás-infrastruktúra 

Az Azure AD join felügyelt és a összevont környezetben működik.  

#### <a name="managed-environment"></a>Felügyelt környezetben 

Felügyelt környezetben a Jelszókivonat-szinkronizálás vagy hitelesítés zökkenőmentes egyszeri bejelentkezést az átengedési felületén keresztül is telepíthető. <read more here> 

#### <a name="federated-environment"></a>Összevont környezetben  

Egy összevont környezetben kell rendelkeznie, amely támogatja a WS-Trust, mind a WS-Fed protokollok Identitásszolgáltatóként. WS-Fed szükség, az eszköz csatlakoztatása az Azure ad-ben, és a WS-Trust kötelező bejelentkezni egy az Azure AD-csatlakoztatott eszközön. Tehát ha az identitásszolgáltató nem támogatja azokat, az Azure AD-csatlakozás nem működik. 


#### <a name="smartcards-and-certificate-based-authentication"></a>Intelligens kártyák és a Tanúsítványalapú hitelesítés 

 

Jelenleg intelligens kártyák és Tanúsítványalapú hitelesítés nem támogatottak az Azure ad-ben, így azok nem működik az Azure AD-csatlakoztatott eszközök 

 

### <a name="devices"></a>Eszközök 

 

#### <a name="supported-devices"></a>Támogatott eszközök 

Az Azure AD join exkluzív Windows 10 rendszerű eszközökre. A esetében nem érvényes Windows- vagy más operációs rendszer korábbi verzióiban. Ha továbbra is Windows 7/8.1, frissítenie kell az Azure AD-csatlakozás telepítése Windows 10-re 

 

Javaslat: Mindig használja a legújabb Windows 10-es kiadásra frissített funkciók 

 

### <a name="applications--other-resources"></a>Alkalmazások és más erőforrások 

Azt javasoljuk, hogy az alkalmazások jobb felhasználói élményt biztosít a felhő-és hozzáférés-vezérlés helyszíni végez áttelepítést. Azonban az Azure AD-hoz csatlakoztatott eszközök zökkenőmentes hozzáférést a helyszíni és felhőalapú alkalmazásokhoz. További információkért lásd: [hogyan a helyszíni erőforrások működik az Azure AD SSO-hez csatlakoztatott eszközök](azuread-join-sso.md).  


Az alábbiakban különböző típusú alkalmazások és erőforrások szempontjai 

 

- **Felhőalapú alkalmazások:** egy alkalmazást az Azure AD-alkalmazásgyűjtemény ad hozzá, ha felhasználók get egyszeri Bejelentkezéshez az Azure AD-hez csatlakoztatott eszközök. További konfiguráció nélkül nem szükséges 

 

- **A helyszíni webalkalmazások:** Ha alkalmazásai egyéni készített, illetve a helyileg üzemeltetett, hozzáadhatja őket a böngészőben a megbízható helyek kell. Ez lehetővé teszi a Windows-hitelesítést munka és a egy nem-kérés egyszeri Bejelentkezéses felhasználói élmény a felhasználók számára. Ha az AD FS használ, tekintse meg [ellenőrizze és felügyelete egyszeri bejelentkezéssel az AD FS-sel](https://docs.microsoft.com/en-us/previous-versions/azure/azure-services/jj151809(v%3dazure.100)) további információt. Javaslat: Fontolja meg a felhőben (például Azure) üzemeltetési és integrálása az Azure AD-hez jobb felhasználói élményt. 

- **A helyszíni alkalmazásokat, régebbi protokollokra hagyatkoznia:** felhasználók get egyszeri bejelentkezés az Azure AD-hez csatlakoztatott eszközök üzemel, a tartományvezérlő eszköz-e. Javaslat: A biztonságosan elérni ezeket az alkalmazásokat az Azure AD-alkalmazás proxy üzembe helyezése. További információkért lásd: [alkalmazásproxy jobb megoldás miért van?](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy#why-is-application-proxy-a-better-solution) 

- **A helyszíni hálózati megosztások:** felhasználók get, a hálózati megosztások egyszeri bejelentkezés az Azure ad-ből csatlakoztatott eszközök, amikor az eszköz rendelkezik egy tartományvezérlővel. További információkért lásd: [hogyan a helyszíni erőforrások működik az Azure AD SSO-hez csatlakoztatott eszközök](azuread-join-sso.md).

 

- **Nyomtatók:** üzembe helyezése hibrid felhőbeli nyomtatás Azure AD-ből nyomtatók felderítéséhez használt csatlakoztatott eszközök. Azt is megteheti felhasználók is használhatják a nyomtatók UNC elérési út közvetlenül adja hozzá őket. Nyomtatók nem található meg a csak felhőalapú környezetekben. 

 

- **A helyi számítógép-hitelesítés a függő alkalmazásokat:** ezeket az alkalmazásokat az Azure AD-csatlakoztatott eszközök nem támogatottak. Javaslat: Fontolja meg a kivonása ezeket az alkalmazásokat, és azok modern alternatívák áthelyezése.  

 

### <a name="device-management"></a>Eszközfelügyelet  

 

Az Azure AD-csatlakoztatott eszközök-kezelési elsősorban egy EMM-platform (például az Intune) és a mobileszköz-kezelési CSP-k keresztül történik. Windows 10-es rendelkezik beépített MDM-ügynököt, amely működik együtt az összes kompatibilis EMM-megoldáshoz.  

 

Csoportházirendek használata nem támogatott az Azure AD-csatlakoztatott eszközök nem csatlakoznak az Active Directoryhoz.  

 

Mobileszköz-kezelési házirend paritásos kiértékelése a csoportházirendek használatával a [MDM áttelepítési elemző eszköz [MMAT](https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/THR3002R). Tekintse át a támogatott és nem támogatott szabályzatok EMM-megoldással helyett csoportházirendek alkalmazhatóságát meghatározásához. Nem támogatott házirendeket érdemes lehet:  

- A nem támogatott házirendek szükségesek a felhasználók vagy eszközök Azure AD-csatlakozás telepítése? 

- Azok a nem támogatott szabályzatok alkalmazható a felhőben üzembe helyezési driven? 

 

Ha EMM-megoldását az Azure AD-alkalmazásgyűjtemény keresztül nem érhető el, hozzáadhatja azt a leírtak szerint [Azure Active Directory-integráció MDM-mel](https://docs.microsoft.com/windows/client-management/mdm/azure-active-directory-integration-with-mdm).

 

Megosztott kezelés, keresztül SCCM egyes szempontjai az eszközök kezeléséhez, amíg a EMM-platformon keresztül kézbesítési házirendek is használható. A Microsoft Intune lehetővé teszi, hogy a megosztott kezelés az SCCM. További információkért lásd: [megosztott kezelés Windows 10-es eszközök](https://docs.microsoft.com/sccm/core/clients/manage/co-management-overview). Ha nem az Intune-EMM termék használ, ellenőrizni kell a megfelelő közös felügyeleti forgatókönyvek EMM-szolgáltatónál.  

 

Az Azure AD-hez csatlakoztatott eszközök felügyeletét két széleskörű módja van: 

 

- **Csak a mobileszköz-kezelési** -eszköz kizárólag egy EMM-szolgáltató, például az Intune által felügyelt. Minden szabályzat az MDM-regisztrációs folyamat részeként érkeznek. Az Azure AD Premium vagy EMS-ügyfelek MDM-regisztráció az Azure AD-csatlakozás részeként automatizált. 

- **Megosztott kezelés** -eszközt egy időben kezeli a EMM-szolgáltató és az SCCM közül. Ebben a megközelítésben az SCCM-ügynök telepítve van egy MDM által felügyelt eszközök felügyeletéhez bizonyos szempontokat. 

Javaslat: MDM fontolja meg, csak a webszolgáltatások az Azure AD-hez csatlakoztatott eszközök.  

 

## <a name="configuration"></a>Konfiguráció 

 

Az Azure AD join néhány speciális beállítással alapján az Azure AD-portálon konfigurálható. Lépjen a `Devices -> Device settings` és konfigurálja a következő beállításokat:   

- Felhasználók eszközöket csatlakoztathatnak az Azure AD: állítsa ezt a beállítást "All" vagy "Kijelölt" a telepítés hatóköre alapján.  

- További helyi rendszergazdák az Azure AD-hez csatlakoztatott eszközök: állítsa a "Kijelölt" és a kijelölt felhasználók, akik az összes Azure AD a helyi rendszergazdák csoporthoz hozzáadni kívánt-hez csatlakoztatott eszközök a szervezetben telepített. További információkért lásd: [a helyi Rendszergazdák csoport kezelése az Azure AD-hez csatlakoztatott eszközök](assign-local-admin.md). 

- Eszközök csatlakoztatása a többtényezős hitelesítés megkövetelése: "Yes" értékre állítani, ha a felhasználók számára az Azure AD-eszközök csatlakoztatása közben hajthatok végre MFA van szüksége. A felhasználó, aki eszköz csatlakozik az Azure AD MFA használatával az eszköz már 2nd factor. 

Ha szeretné engedélyezni, állapothordozás az Azure ad-hez, hogy az eszközök szinkronizálhatók a beállítások, tekintse meg [Mi a vállalati állapothordozás?](enterprise-state-roaming-overview.md). Ajánlott engedélyezni ezt a beállítást akkor is a hibrid Azure AD-hez csatlakoztatott eszközök 

### <a name="deployment-options"></a>Üzembe helyezési beállítások 

 

Az Azure AD join három különböző megközelítések keresztül telepíthető:  

- **Önkiszolgáló OOBE/beállításaiban** - módban az önkiszolgáló felhasználók go keresztül az Azure AD-csatlakozás feldolgozása vagy során Windows Out kezdőélmény (OOBE) vagy a Windows beállításait.  

- **Windows Autopilot** – Windows Autopilot lehetővé teszi, hogy az üzem előtti konfigurációs eszközök az Azure AD-csatlakozás végrehajtásához OOBE egyenletesebb biztosítása érdekében.   

- **Csoportos regisztrálás** -tömeges beléptetéssel-driven rendszergazda az Azure AD-csatlakozás a tömeges kiépítési eszköz használatával konfigurálhatja az eszközöket.  


A következő három módszer összehasonlítása 

 
||A telepítő önkiszolgáló|Windows Autopilot|Csoportos regisztráció|
|---|---|---|---|
|Állítsa be a felhasználói műveletet igényel|Igen|Igen|Nem|
|Informatikai erőfeszítésekre van szükség|Nem|Igen|Igen|
|Alkalmazható folyamatok|Kezdőélmény & beállításai|Csak a Kezdőélmény|Csak a Kezdőélmény|
|Helyi rendszergazdai jogosultság szükséges az elsődleges felhasználó|Igen, alapértelmezés szerint|Konfigurálható|Nem|
|Eszköz OEM-támogatásra van szüksége|Nem|Igen|Nem|
|Támogatott verziók|1511-es +|1709-es +|1703-as +|
 
Válassza ki a központi telepítési módszer vagy módszerek áttekintése a fenti táblázatban, és mindkét módszerrel bevezetésének az alábbi szempontokat megtekintésével:  

- Azok a felhasználók műszaki lelkes haladhat végig a telepítő maguknak? 

    - Önkiszolgáló is működik ezeknek a felhasználóknak ajánlott. Fontolja meg a Windows Autopilot a felhasználói élmény fokozása érdekében.  

- Azok a felhasználók a távoli vagy helyi vállalati belül? 

    - Önkiszolgáló vagy a távoli felhasználók számára zökkenőmentes telepítéshez ajánlott Autopilot munka. 
 
- Részesíti előnyben a felhasználó közreműködésével vagy egy rendszergazda által felügyelt konfigurációt? 

    - Csoportos regisztrálás üzembe helyezés beállítása az eszközökön a felhasználók átadása előtt készített felügyeleti jobban működik.     

- Tegye vásárolhatja meg eszközök 1-2 számítógépgyártók (OEM), vagy rendelkezik egy széles terjesztési eszköz OEM?  

    - Ha vásárol a korlátozott OEM-ek is támogató Autopilot, szorosabban együttműködnek az Autopilot révén kihasználhatja. 
 

 


## <a name="next-steps"></a>További lépések

- [Eszközkezelés](overview.md)

