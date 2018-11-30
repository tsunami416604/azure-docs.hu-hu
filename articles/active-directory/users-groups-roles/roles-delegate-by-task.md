---
title: Legalacsonyabb jogosultsági szintű szerepkörök delegálása az Azure Active Directoryban feladat |} A Microsoft Docs
description: Szerepkörök az Azure Active Directory identitáskezelési feladatok delegálása
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 11/08/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: bd63e8379051864a19c473779625a925446185f2
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52445021"
---
# <a name="administrator-roles-by-identity-task-in-azure-active-directory"></a>Rendszergazdai szerepkörök az Azure Active Directory identity feladat

Ebben a cikkben találja a felhasználó rendszergazdai jogosultságok korlátozása a legalacsonyabb jogosultsági szintű szerepkörök hozzárendelése az Azure Active Directory (Azure AD) által szükséges információkat. Tapasztalni fogja rendszergazdai feladatok területeket, valamint az egyes feladatok mellett további nem globális rendszergazda szerepkör által végrehajtható a tevékenység elvégzéséhez szükséges legalacsonyabb jogosultsági szintű szerepkör szerint vannak rendezve.

## <a name="application-proxy"></a>Alkalmazásproxy

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Application proxy-alkalmazás konfigurálása | Alkalmazás-rendszergazda | 
Összekötő tulajdonságainak konfigurálása | Alkalmazás-rendszergazda | 
Alkalmazás regisztrálása létrehozása, ha lehetővé teszi az összes felhasználó számára le van tiltva | Alkalmazásfejlesztő | Felhőalkalmazás-rendszergazda, alkalmazás-rendszergazda
Összekötőcsoport létrehozása | Alkalmazás-rendszergazda | 
Összekötőcsoport törlése | Alkalmazás-rendszergazda | 
Alkalmazásproxy letiltása | Alkalmazás-rendszergazda | 
Összekötőszolgáltatás letöltése | Alkalmazás-rendszergazda | 
Az összes konfiguráció olvasása | Alkalmazás-rendszergazda | 

## <a name="b2c"></a>B2C

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Azure AD B2C címtár létrehozása | Minden nem vendég felhasználók ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
A B2C-alkalmazások létrehozása | Globális rendszergazda | 
Vállalati alkalmazások létrehozása | Felhőalkalmazás-rendszergazda | Alkalmazás-rendszergazda
Létrehozása, olvasása, frissítése és törlése a B2C-szabályzatok | Globális rendszergazda | 
Létrehozása, olvasása, frissítése és törlése Identitásszolgáltatók | Globális rendszergazda | 
Létrehozása, olvasása, frissítése és törlése a jelszó alaphelyzetbe állítása felhasználókövetési adatai | Globális rendszergazda | 
Létrehozása, olvasása, frissítése és törlése Profilszerkesztési felhasználói folyamatok | Globális rendszergazda | 
Létrehozása, olvasása, frissítése és törlése a bejelentkezési felhasználói folyamatok | Globális rendszergazda | 
Létrehozása, olvasása, frissítése és -előfizetés felhasználói folyamat törlése |Globális rendszergazda | 
Létrehozása, olvasása, frissítése és törlése a felhasználói attribútumok | Globális rendszergazda | 
Létrehozása, olvasása, frissítése és törlése a felhasználók | Globális rendszergazda ([dokumentációjában](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs))
Az összes konfiguráció olvasása | Globális rendszergazda | 
Olvassa el a B2C-naplók | Globális rendszergazda ([dokumentációjában](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs)) | 

## <a name="company-branding"></a>Vállalati védjegyezés

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Vállalati arculat konfigurálása | Globális rendszergazda | 
Az összes konfiguráció olvasása | Címtárolvasók | Felhasználói szerepkör alapértelmezés ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="company-properties"></a>Vállalati tulajdonságai

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Vállalati tulajdonságainak konfigurálása | Globális rendszergazda | 

## <a name="connect"></a>Kapcsolódás

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Átmenő hitelesítés | Globális rendszergazda | 
Az összes konfiguráció olvasása | Globális rendszergazda | 
Közvetlen egyszeri bejelentkezés | Globális rendszergazda | 

## <a name="connect-health"></a>Connect Health

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Hozzáadhat vagy törölhet szolgáltatások | Tulajdonos ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
A szinkronizálási hiba javításához | Közreműködő ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Tulajdonos
Értesítések konfigurálása | Közreműködő ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Tulajdonos
Beállítások konfigurálása | Tulajdonos ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Szinkronizálási értesítések konfigurálása | Közreműködő ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Tulajdonos
Olvasási ADFS biztonsági jelentések | Biztonsági olvasó | Közreműködő, tulajdonos
Az összes konfiguráció olvasása | Olvasó ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Közreműködő, tulajdonos
Olvassa el a szinkronizálási hibák | Olvasó ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Közreműködő, tulajdonos
Olvassa el a társzinkronizálási szolgáltatás | Olvasó ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Közreműködő, tulajdonos
Metrikák megtekintése és riasztások | Olvasó ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Közreműködő, tulajdonos
Metrikák megtekintése és riasztások | Olvasó ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Közreműködő, tulajdonos
Szinkronizálási szolgáltatás metrikáinak megtekintése és riasztások | Olvasó ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Közreműködő, tulajdonos


## <a name="custom-domain-names"></a>Egyéni tartománynevek

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Tartományok kezelése | Globális rendszergazda | 
Az összes konfiguráció olvasása | Címtárolvasók | Felhasználói szerepkör alapértelmezés ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="domain-services"></a>Tartományi szolgáltatások

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Az Azure AD Domain Services-példány létrehozása | Globális rendszergazda | 
Minden Azure AD tartományi szolgáltatások feladatok végrehajtása | Az Azure AD DC rendszergazdák csoportjának ([dokumentációjában](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-administer-domain#administrative-tasks-you-can-perform-on-a-managed-domain)) | 
Az összes konfiguráció olvasása | A rendszer Active Directory Tartományi szolgáltatásokat tartalmazó Azure-előfizetés | 

## <a name="devices"></a>Eszközök

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Eszköz letiltása | Felhőbeli eszközrendszergazda | 
Eszköz engedélyezése | Felhőbeli eszközrendszergazda | 
Alapszintű konfiguráció olvasása | Felhasználói szerepkör alapértelmezés ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Olvassa el a Bitlocker-kulcsok | Biztonsági olvasó | Jelszókezelő, a biztonsági rendszergazda

## <a name="enterprise-applications"></a>Vállalati alkalmazások

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Hozzájárulás megadása a delegált engedélyek | Felhőalkalmazás-rendszergazda | Alkalmazás-rendszergazda
Hozzájárul a Microsoft Graph vagy Azure AD Graph Alkalmazásengedélyek | Felhőalkalmazás-rendszergazda | Alkalmazás-rendszergazda
Hozzájárulás megadása a Microsoft Graph vagy Azure AD Graph Alkalmazásengedélyek | Globális rendszergazda | 
Hozzájárulás megadása az alkalmazásoknak a saját adatok elérése | Felhasználói szerepkör alapértelmezés ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
A vállalati alkalmazás létrehozása | Felhőalkalmazás-rendszergazda | Alkalmazás-rendszergazda
Az alkalmazásproxy kezelése | Alkalmazás-rendszergazda | 
Felhasználói beállítások kezelése | Globális rendszergazda | 
Egy csoport vagy alkalmazás olvasási hozzáférés felülvizsgálata | Biztonsági olvasó | Biztonsági rendszergazda felhasználó rendszergazda
Az összes konfiguráció olvasása | Felhasználói szerepkör alapértelmezés ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Vállalati alkalmazás-hozzárendelések frissítése | Vállalati alkalmazás tulajdonosa ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Felhőalkalmazás-rendszergazda, alkalmazás-rendszergazda
Vállalati alkalmazástulajdonosok frissítése | Vállalati alkalmazás tulajdonosa ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Felhőalkalmazás-rendszergazda, alkalmazás-rendszergazda
Vállalati alkalmazás tulajdonságainak frissítése | Vállalati alkalmazás tulajdonosa ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Felhőalkalmazás-rendszergazda, alkalmazás-rendszergazda
Frissítse a vállalati alkalmazások üzembe helyezése | Vállalati alkalmazás tulajdonosa ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Felhőalkalmazás-rendszergazda, alkalmazás-rendszergazda
Önkiszolgáló vállalati alkalmazás frissítése | Vállalati alkalmazás tulajdonosa ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Felhőalkalmazás-rendszergazda, alkalmazás-rendszergazda
Egyszeri bejelentkezés tulajdonságainak frissítése | Vállalati alkalmazás tulajdonosa ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Felhőalkalmazás-rendszergazda, alkalmazás-rendszergazda

## <a name="groups"></a>Csoportok

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Licenc hozzárendelése | Felhasználóifiók-adminisztrátor | 
Csoport létrehozása | Felhasználóifiók-adminisztrátor | 
Létrehozása, frissítése vagy egy csoport vagy alkalmazás hozzáférési felülvizsgálat törlése | Felhasználóifiók-adminisztrátor | 
Csoport lejáratának kezelése | Felhasználóifiók-adminisztrátor | 
Csoportbeállítások kezelése | Globális rendszergazda | 
Olvassa el az összes konfigurációs (kivéve a rejtett tagsági) | Címtárolvasók | Felhasználói szerepkör alapértelmezés ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Rejtett olvasási tagság | A csoporttag | Csoporttulajdonos, jelszókezelő, Exchange-rendszergazda, SharePoint-rendszergazda, Teams-rendszergazda, felhasználóifiók-adminisztrátor
Olvassa el a rejtett tagsággal rendelkező csoportok tagsága | Ügyfélszolgálati adminisztrátor | Felhasználóifiók-adminisztrátor, rendszergazda csapatok
Licenc visszavonása | Licencadminisztrátor | Felhasználóifiók-adminisztrátor
Frissíteni a csoporttagságot | Csoporttulajdonos ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Felhasználóifiók-adminisztrátor
Csoporttulajdonosok frissítése | Csoporttulajdonos ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Felhasználóifiók-adminisztrátor
Frissítési csoport tulajdonságai | Csoporttulajdonos ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Felhasználóifiók-adminisztrátor

## <a name="identity-protection"></a>Identity Protection

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Riasztási értesítések beállítása| Biztonsági rendszergazda | 
Konfigurálása és engedélyezze vagy tiltsa le a többtényezős hitelesítési szabályzat| Biztonsági rendszergazda | 
Konfigurálása és engedélyezze vagy tiltsa le a bejelentkezési kockázati házirend| Biztonsági rendszergazda | 
Konfigurálhatja, és engedélyezze, vagy letilthatja a felhasználói kockázati házirend | Biztonsági rendszergazda | 
Heti emésztett konfigurálása | Biztonsági rendszergazda| 
Az összes kockázati esemény elvetését | Biztonsági rendszergazda | 
Javítsa ki vagy a biztonsági rések bezárása | Biztonsági rendszergazda | 
Az összes konfiguráció olvasása | Biztonsági olvasó | 
Olvassa el az összes kockázati események | Biztonsági olvasó | 
Olvassa el a biztonsági rések | Biztonsági olvasó | 

## <a name="licenses"></a>Licencek

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Licenc hozzárendelése | Licencadminisztrátor | Felhasználóifiók-adminisztrátor
Az összes konfiguráció olvasása | Címtárolvasók | Felhasználói szerepkör alapértelmezés ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Licenc visszavonása | Licencadminisztrátor | Felhasználóifiók-adminisztrátor
Próbálja ki, vagy előfizetés vásárlása | Számlázási rendszergazda | 


## <a name="monitoring---audit-logs"></a>Monitoring - naplók

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Olvassa el a naplók riasztásaihoz | Jelentésolvasó | Biztonsági olvasó, a biztonsági rendszergazda

## <a name="monitoring---sign-ins"></a>Monitoring - bejelentkezések

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Olvassa el a bejelentkezési naplók | Jelentésolvasó | Biztonsági olvasó, a biztonsági rendszergazda

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Minden, a kiválasztott felhasználók által létrehozott alkalmazásjelszó törlése | Globális rendszergazda | 
Többtényezős hitelesítés letiltása | Globális rendszergazda | 
MFA engedélyezése | Globális rendszergazda | 
Az MFA Szolgáltatásbeállítások kezelése | Globális rendszergazda | 
A kapcsolattartási mód újbóli megadásának kérése a kiválasztott felhasználóktól | Globális rendszergazda | 
A többtényezős hitelesítés visszaállítása az összes megjegyzett eszközön  | Globális rendszergazda | 

## <a name="mfa-server"></a>MFA-kiszolgáló

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Felhasználók blokkolása/feloldása | Globális rendszergazda | 
Fiókzárolás konfigurálása | Globális rendszergazda | 
Gyorsítótárazási szabályok konfigurálása | Globális rendszergazda | 
Visszaélési riasztás konfigurálása | Globális rendszergazda
Értesítések konfigurálása | Globális rendszergazda | 
Az egyszeri Mellőzés konfigurálása | Globális rendszergazda | 
Telefonhívás beállításainak konfigurálása | Globális rendszergazda | 
Szolgáltatók konfigurálása | Globális rendszergazda | 
Kiszolgáló beállításainak konfigurálása | Globális rendszergazda | 
Olvassa el a tevékenységgel kapcsolatos jelentés | Globális rendszergazda | 
Az összes konfiguráció olvasása | Globális rendszergazda | 
Olvassa el a kiszolgáló állapota | Globális rendszergazda |  

## <a name="organizational-relationships"></a>Céges kapcsolatok

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Identitásszolgáltatók kezelése | Globális rendszergazda | 
Beállítások kezelése | Globális rendszergazda | 
Használati feltételek kezelése | Globális rendszergazda | 
Az összes konfiguráció olvasása | Globális rendszergazda | 

## <a name="password-reset"></a>Új jelszó létrehozása

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Hitelesítési módszerek konfigurálása | Globális rendszergazda | 
Testreszabás konfigurálása | Globális rendszergazda | 
Értesítések konfigurálása | Globális rendszergazda | 
Helyszíni integráció beállítása | Globális rendszergazda | 
Jelszó alaphelyzetbe állítása tulajdonságainak konfigurálása | Globális rendszergazda | 
Regisztráció beállítása | Globális rendszergazda | 
Az összes konfiguráció olvasása | Biztonsági rendszergazda felhasználó rendszergazda | 

## <a name="privileged-identity-management"></a>Privileged identity management

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Felhasználók szerepkörökhöz rendelése | Kiemelt szerepkörű rendszergazda | 
Szerepkör-beállítások konfigurálása | Kiemelt szerepkörű rendszergazda | 
Naplózási tevékenység megtekintése | Biztonsági olvasó | 
Nézet szerepkörtagságai | Biztonsági olvasó | 

## <a name="roles-and-administrators"></a>Szerepkörök és a rendszergazdák

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Szerepkör-hozzárendelések kezelése | Kiemelt szerepkörű rendszergazda | 
Azure AD-szerepkörhöz olvasási hozzáférési felülvizsgálata  | Biztonsági olvasó | Biztonsági rendszergazda, a kiemelt szerepkörű rendszergazda
Az összes konfiguráció olvasása | Felhasználói szerepkör alapértelmezés ([dokumentációjában](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 

## <a name="security---authentication-methods"></a>Biztonság – hitelesítési módszerek

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Hitelesítési módszerek konfigurálása | Globális rendszergazda | 
Az összes konfiguráció olvasása | Globális rendszergazda | 

## <a name="security---conditional-access"></a>Biztonság – feltételes hozzáférés

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Multi-factor Authentication megbízható IP-címek | Feltételes hozzáférésű rendszergazda | 
Hozzon létre egyéni vezérlők | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Nevesített helyek létrehozása | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Szabályzatok létrehozása | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Használati feltételek létrehozása | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
VPN-kapcsolati tanúsítványának létrehozása | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Klasszikus szabályzat törlése | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Használati feltételek törlése | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
VPN-kapcsolati tanúsítvány törlése | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Klasszikus szabályzat letiltása | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Egyéni vezérlők kezeléséhez | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Nevesített helyek kezelése | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Használati feltételek kezelése | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Az összes konfiguráció olvasása | Biztonsági olvasó | Biztonsági rendszergazda
Olvasási nevesített helyek | Biztonsági olvasó | Feltételes hozzáférés rendszergazdai, biztonsági rendszergazda

## <a name="security---identity-security-score"></a>Biztonság – identitás biztonsági pontszám

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök | 
---- | --------------------- | ----------------
Az összes konfiguráció olvasása | Biztonsági olvasó | Biztonsági rendszergazda
Olvasási biztonsági pontszám | Biztonsági olvasó | Biztonsági rendszergazda
Eseményállapot módosítása | Biztonsági rendszergazda | 

## <a name="security---risky-sign-ins"></a>Biztonság – a kockázatos bejelentkezések

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Az összes konfiguráció olvasása | Biztonsági olvasó | 
Olvassa el a kockázatos bejelentkezések | Biztonsági olvasó | 

## <a name="security---users-flagged-for-risk"></a>Biztonság – kockázatosként megjelölt felhasználók

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Az összes esemény elvetését | Biztonsági rendszergazda | 
Az összes konfiguráció olvasása | Biztonsági olvasó | 
Olvassa el a kockázatosként megjelölt felhasználók | Biztonsági olvasó | 

## <a name="users"></a>Felhasználók

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Felhasználó hozzáadása a címtárbeli szerepkört | Kiemelt szerepkörű rendszergazda | 
Tag hozzáadása a csoporthoz | Felhasználóifiók-adminisztrátor | 
Licenc hozzárendelése | Licencadminisztrátor | Felhasználóifiók-adminisztrátor
Vendég-felhasználó létrehozása | Vendég meghívója | Felhasználóifiók-adminisztrátor
Felhasználó létrehozása | Felhasználóifiók-adminisztrátor | 
Felhasználók törlése | Felhasználóifiók-adminisztrátor | 
A korlátozott rendszergazdák (lásd a dokumentációt) frissítési biztonsági jogkivonat érvénytelenítése | Felhasználóifiók-adminisztrátor | 
A nem rendszergazda (lásd a dokumentációt) frissítési biztonsági jogkivonat érvénytelenítése | Jelszókezelő | Felhasználóifiók-adminisztrátor
Az emelt szintű rendszergazdák száma (lásd a dokumentációt) frissítési biztonsági jogkivonat érvénytelenítése | Globális rendszergazda | 
Alapszintű konfiguráció olvasása | Felhasználói szerepkör alapértelmezés ([dokumentációja](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
(Lásd a dokumentációt) korlátozott rendszergazdák jelszavának alaphelyzetbe állítása | Felhasználóifiók-adminisztrátor | 
Jelszó alaphelyzetbe állítása, nem rendszergazda (lásd a dokumentációt) | Jelszókezelő | Felhasználóifiók-adminisztrátor
Jelszó alaphelyzetbe állítása az emelt szintű rendszergazdák száma | Globális rendszergazda | 
Licenc visszavonása | Licencadminisztrátor | Felhasználóifiók-adminisztrátor
Egyszerű felhasználónév kivételével az összes tulajdonságainak frissítése | Felhasználóifiók-adminisztrátor | 
Egyszerű felhasználónév (lásd a dokumentációt) korlátozott rendszergazdák frissítése | Felhasználóifiók-adminisztrátor | 
Egyszerű felhasználónév tulajdonság frissítése az emelt szintű rendszergazdák (lásd a dokumentációt) | Globális rendszergazda | 
Felhasználói beállítások frissítése | Globális rendszergazda | 


## <a name="support"></a>Támogatás

Tevékenység | Legalacsonyabb jogosultsági szintű szerepkör | További szerepkörök
---- | --------------------- | ----------------
Támogatási jegy küldése | Szolgáltatás-rendszergazda | Alkalmazás-rendszergazda, számlázási rendszergazda, a Felhőalkalmazás-rendszergazda, a megfelelőségi rendszergazda, a Dynamics 365-rendszergazda, asztali Analytics rendszergazdája, Exchange-rendszergazda, jelszókezelő, az Information Protection Rendszergazda, az Intune-rendszergazda, a rendszergazda, a Power BI rendszergazdai, kiemelt hitelesítést rendszergazda, SharePoint-rendszergazda, Teams kommunikációs rendszergazda, csapatok rendszergazda, a rendszergazda felhasználó, Skype Workplace Analytics rendszergazdája

## <a name="next-steps"></a>További lépések

* [Rendelje hozzá, vagy távolítsa el az azure AD rendszergazdai szerepkörei](directory-manage-roles-portal.md)
* [Azure AD rendszergazdai szerepkörei hivatkozhat.](directory-assign-admin-roles.md)
