---
title: Szerepkörök delegálása rendszergazdai feladat szerint – Azure Active Directory | Microsoft Docs
description: Az identitási feladatokhoz delegált szerepkörök Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 05/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8ccd98553b06ef3fcd2241c8223889c0a86e331
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024656"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Rendszergazdai szerepkörök felügyeleti feladat szerint Azure Active Directory

Ebből a cikkből megtudhatja, hogy milyen információkra van szükség a felhasználók rendszergazdai engedélyeinek korlátozásához, ha a Azure Active Directory (Azure AD) szolgáltatásban a legkevésbé Kiemelt szerepköröket rendeli hozzá. Az egyes feladatok végrehajtásához szükséges rendszergazdai feladatokat és a minimálisan Kiemelt szerepkört, valamint a feladatot elvégző további nem globális rendszergazdai szerepköröket is megtalálja.

## <a name="application-proxy"></a>Alkalmazásproxy

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Alkalmazásproxy alkalmazásának konfigurálása | Alkalmazás-rendszergazda | 
Összekötő csoport tulajdonságainak konfigurálása | Alkalmazás-rendszergazda | 
Alkalmazás-regisztráció létrehozása, ha a képesség le van tiltva az összes felhasználónál | Alkalmazás-fejlesztő | Cloud Application Administrator, alkalmazás-rendszergazda
Összekötő-csoport létrehozása | Alkalmazás-rendszergazda | 
Összekötő-csoport törlése | Alkalmazás-rendszergazda | 
Alkalmazásproxy letiltása | Alkalmazás-rendszergazda | 
Összekötő szolgáltatás letöltése | Alkalmazás-rendszergazda | 
Az összes konfiguráció olvasása | Alkalmazás-rendszergazda | 

## <a name="b2c"></a>B2C

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Azure AD B2C könyvtárak létrehozása | Minden nem vendég felhasználó ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
B2C-alkalmazások létrehozása | Globális rendszergazda | 
Vállalati alkalmazások létrehozása | Cloud Application Administrator | Alkalmazás-rendszergazda
B2C-szabályzatok létrehozása, olvasása, frissítése és törlése | Globális rendszergazda | 
Identitás-szolgáltatók létrehozása, olvasása, frissítése és törlése | Globális rendszergazda | 
Jelszó-visszaállítási felhasználói folyamatok létrehozása, olvasása, frissítése és törlése | Globális rendszergazda | 
Profil-szerkesztési felhasználói folyamatok létrehozása, olvasása, frissítése és törlése | Globális rendszergazda | 
Bejelentkezési felhasználói folyamatok létrehozása, olvasása, frissítése és törlése | Globális rendszergazda | 
Regisztrációs felhasználói folyamat létrehozása, olvasása, frissítése és törlése |Globális rendszergazda | 
Felhasználói attribútumok létrehozása, olvasása, frissítése és törlése | Globális rendszergazda | 
Felhasználók létrehozása, olvasása, frissítése és törlése | Globális rendszergazda ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs))
Az összes konfiguráció olvasása | Globális rendszergazda | 
B2C-naplók olvasása | Globális rendszergazda ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs)) | 

> [!NOTE]
> Azure AD B2C globális rendszergazdák nem rendelkeznek ugyanazokkal az engedélyekkel, mint az Azure AD globális rendszergazdái. Ha Azure AD B2C globális rendszergazdai jogosultságokkal rendelkezik, győződjön meg róla, hogy Azure AD B2C könyvtárban van, és nem Azure AD-címtár.

## <a name="company-branding"></a>Vállalati arculat

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Vállalati arculat konfigurálása | Globális rendszergazda | 
Az összes konfiguráció olvasása | Directory-olvasók | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="company-properties"></a>Vállalat tulajdonságai

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Vállalati tulajdonságok konfigurálása | Globális rendszergazda | 

## <a name="connect"></a>Kapcsolódás

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Továbbító hitelesítés | Globális rendszergazda | 
Az összes konfiguráció olvasása | Globális rendszergazda | 
Zökkenőmentes egyszeri bejelentkezés | Globális rendszergazda | 

## <a name="connect-health"></a>Connect Health

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Szolgáltatások hozzáadása vagy törlése | Tulajdonos ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Hibajavítások alkalmazása szinkronizálási hiba esetén | Közreműködő ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Tulajdonos
Értesítések konfigurálása | Közreműködő ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Tulajdonos
Beállítások konfigurálása | Tulajdonos ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Szinkronizálási értesítések konfigurálása | Közreműködő ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Tulajdonos
ADFS biztonsági jelentések beolvasása | Biztonsági olvasó | Közreműködő, tulajdonos
Az összes konfiguráció olvasása | Olvasó ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Közreműködő, tulajdonos
Szinkronizálási hibák olvasása | Olvasó ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Közreműködő, tulajdonos
Szinkronizálási szolgáltatások olvasása | Olvasó ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Közreműködő, tulajdonos
Metrikák és riasztások megtekintése | Olvasó ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Közreműködő, tulajdonos
Metrikák és riasztások megtekintése | Olvasó ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Közreműködő, tulajdonos
Szinkronizálási szolgáltatás metrikáinak és értesítéseinek megtekintése | Olvasó ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Közreműködő, tulajdonos


## <a name="custom-domain-names"></a>Egyéni tartománynevek

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Tartományok kezelése | Globális rendszergazda | 
Az összes konfiguráció olvasása | Directory-olvasók | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="domain-services"></a>Tartományi szolgáltatások

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Azure AD Domain Services példány létrehozása | Globális rendszergazda | 
Az összes Azure AD Domain Services feladat végrehajtása | Azure AD DC-rendszergazdák csoport ([lásd a dokumentációt](../../active-directory-domain-services/tutorial-create-management-vm.md#administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain)) | 
Az összes konfiguráció olvasása | AD DS szolgáltatást tartalmazó Azure-előfizetés olvasója | 

## <a name="devices"></a>Eszközök

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Eszköz letiltása | Felhőalapú eszköz rendszergazdája | 
Eszköz engedélyezése | Felhőalapú eszköz rendszergazdája | 
Alapszintű konfiguráció olvasása | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
BitLocker-kulcsok olvasása | Biztonsági olvasó | Jelszó-rendszergazda, biztonsági rendszergazda

## <a name="enterprise-applications"></a>Vállalati alkalmazások

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Beleegyezik a delegált engedélyekkel | Cloud Application Administrator | Alkalmazás-rendszergazda
Az alkalmazás engedélyeinek belefoglalása a Microsoft Graph vagy az Azure AD gráf nélkül | Cloud Application Administrator | Alkalmazás-rendszergazda
Az alkalmazás engedélyeinek Microsoft Graph vagy Azure AD Graphba való beleegyezett | Globális rendszergazda | 
A saját adatokhoz hozzáférő alkalmazások beleegyezett | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Vállalati alkalmazás létrehozása | Cloud Application Administrator | Alkalmazás-rendszergazda
Alkalmazásproxy kezelése | Alkalmazás-rendszergazda | 
Felhasználói beállítások kezelése | Globális rendszergazda | 
Csoport vagy alkalmazás hozzáférési felülvizsgálatának olvasása | Biztonsági olvasó | Biztonsági rendszergazda, felhasználói rendszergazda
Az összes konfiguráció olvasása | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Vállalati alkalmazás-hozzárendelések frissítése | Vállalati alkalmazás tulajdonosa ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Cloud Application Administrator, alkalmazás-rendszergazda
Vállalati alkalmazások tulajdonosainak frissítése | Vállalati alkalmazás tulajdonosa ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Cloud Application Administrator, alkalmazás-rendszergazda
Vállalati alkalmazás tulajdonságainak frissítése | Vállalati alkalmazás tulajdonosa ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Cloud Application Administrator, alkalmazás-rendszergazda
Vállalati alkalmazások üzembe helyezésének frissítése | Vállalati alkalmazás tulajdonosa ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Cloud Application Administrator, alkalmazás-rendszergazda
Vállalati alkalmazás önkiszolgáló frissítése | Vállalati alkalmazás tulajdonosa ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Cloud Application Administrator, alkalmazás-rendszergazda
Egyszeri bejelentkezési tulajdonságok frissítése | Vállalati alkalmazás tulajdonosa ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Cloud Application Administrator, alkalmazás-rendszergazda

## <a name="entitlement-management"></a>Jogosultságkezelés
Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Erőforrások hozzáadása egy katalógushoz | Felhasználói rendszergazda | A jogosultságok kezelése szolgáltatással delegálhatja ezt a feladatot a katalógus tulajdonosának ([lásd a dokumentációt](../governance/entitlement-management-catalog-create.md#add-additional-catalog-owners)).
SharePoint Online-webhelyek hozzáadása a katalógushoz | Globális rendszergazda


## <a name="groups"></a>Csoportok

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Licenc kiosztása | Felhasználói rendszergazda | 
Csoport létrehozása | Felhasználói rendszergazda | 
Csoport vagy alkalmazás hozzáférési felülvizsgálatának létrehozása, frissítése vagy törlése | Felhasználói rendszergazda | 
Csoport lejáratának kezelése | Felhasználói rendszergazda | 
Csoportbeállítások kezelése | Globális rendszergazda | 
Az összes konfiguráció olvasása (kivéve a rejtett tagságot) | Directory-olvasók | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Rejtett tagság olvasása | Csoporttag | Csoport tulajdonosa, jelszó-rendszergazda, Exchange-rendszergazda, SharePoint-rendszergazda, csapat rendszergazdája, felhasználói rendszergazda
Rejtett tagsággal rendelkező csoportok tagságának olvasása | Segélyszolgálat rendszergazdája | Felhasználói rendszergazda, csapat rendszergazdája
Licenc visszavonása | Licenc rendszergazdája | Felhasználói rendszergazda
Csoporttagság frissítése | Csoport tulajdonosa ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Felhasználói rendszergazda
Csoport tulajdonosainak frissítése | Csoport tulajdonosa ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Felhasználói rendszergazda
Csoport tulajdonságainak frissítése | Csoport tulajdonosa ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Felhasználói rendszergazda

## <a name="identity-protection"></a>Identity Protection

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Riasztási értesítések konfigurálása| Biztonsági rendszergazda | 
MFA-házirend konfigurálása és engedélyezése vagy letiltása| Biztonsági rendszergazda | 
A bejelentkezési kockázati házirend konfigurálása és engedélyezése vagy letiltása| Biztonsági rendszergazda | 
Felhasználói kockázati házirend konfigurálása és engedélyezése vagy letiltása | Biztonsági rendszergazda | 
Heti kivonatok konfigurálása | Biztonsági rendszergazda| 
Az összes kockázati észlelés bezárása | Biztonsági rendszergazda | 
Biztonsági rés javítása vagy bezárása | Biztonsági rendszergazda | 
Az összes konfiguráció olvasása | Biztonsági olvasó | 
Az összes kockázati észlelés olvasása | Biztonsági olvasó | 
Biztonsági rések beolvasása | Biztonsági olvasó | 

## <a name="licenses"></a>Licencek

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Licenc kiosztása | Licenc rendszergazdája | Felhasználói rendszergazda
Az összes konfiguráció olvasása | Directory-olvasók | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Licenc visszavonása | Licenc rendszergazdája | Felhasználói rendszergazda
Előfizetés kipróbálása vagy megvásárlása | Számlázási rendszergazda | 


## <a name="monitoring---audit-logs"></a>Figyelés – naplók

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Naplófájlok olvasása | Jelentések olvasója | Biztonsági olvasó, biztonsági rendszergazda

## <a name="monitoring---sign-ins"></a>Figyelés – bejelentkezések

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Bejelentkezési naplók olvasása | Jelentések olvasója | Biztonsági olvasó, biztonsági rendszergazda

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
A kiválasztott felhasználók által létrehozott összes meglévő alkalmazás jelszavának törlése | Globális rendszergazda | 
MFA letiltása | Globális rendszergazda | 
MFA engedélyezése | Globális rendszergazda | 
MFA szolgáltatás beállításainak kezelése | Globális rendszergazda | 
A kapcsolatfelvételi módszerek megadásának megkövetelése a kiválasztott felhasználóknak | Hitelesítés rendszergazdája | 
A többtényezős hitelesítés visszaállítása az összes megjegyzett eszközön  | Hitelesítés rendszergazdája | 

## <a name="mfa-server"></a>MFA-kiszolgáló

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Felhasználók blokkolása/feloldása | Globális rendszergazda | 
Fiókzárolási konfigurálása | Globális rendszergazda | 
Gyorsítótárazási szabályok konfigurálása | Globális rendszergazda | 
Csalási riasztás konfigurálása | Globális rendszergazda
Értesítések konfigurálása | Globális rendszergazda | 
Egyszeri Mellőzés konfigurálása | Globális rendszergazda | 
Telefonhívás beállításainak konfigurálása | Globális rendszergazda | 
Szolgáltatók konfigurálása | Globális rendszergazda | 
Kiszolgálóbeállítások konfigurálása | Globális rendszergazda | 
Tevékenység jelentésének olvasása | Globális rendszergazda | 
Az összes konfiguráció olvasása | Globális rendszergazda | 
Kiszolgáló állapotának olvasása | Globális rendszergazda |  

## <a name="organizational-relationships"></a>Szervezeti kapcsolatok

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Identitás-szolgáltatók kezelése | Globális rendszergazda | 
Beállítások kezelése | Globális rendszergazda | 
Használati feltételek kezelése | Globális rendszergazda | 
Az összes konfiguráció olvasása | Globális rendszergazda | 

## <a name="password-reset"></a>Új jelszó létrehozása

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Hitelesítési módszerek konfigurálása | Globális rendszergazda |
Testreszabás konfigurálása | Globális rendszergazda |
Értesítés konfigurálása | Globális rendszergazda |
Helyszíni integráció konfigurálása | Globális rendszergazda |
Jelszó-visszaállítási tulajdonságok konfigurálása | Felhasználói rendszergazda | Globális rendszergazda
Regisztráció konfigurálása | Globális rendszergazda |
Az összes konfiguráció olvasása | Biztonsági rendszergazda | Felhasználói rendszergazda |

## <a name="privileged-identity-management"></a>Privilegizált identitások kezelése

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Felhasználók társítása szerepkörökhöz | Kiemelt szerepkörű rendszergazda | 
Szerepkör-beállítások konfigurálása | Kiemelt szerepkörű rendszergazda | 
Naplózási tevékenység megtekintése | Biztonsági olvasó | 
Szerepkör-tagságok megtekintése | Biztonsági olvasó | 

## <a name="roles-and-administrators"></a>Szerepkörök és rendszergazdák

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Szerepkör-hozzárendelések kezelése | Kiemelt szerepkörű rendszergazda | 
Azure AD-szerepkör hozzáférési felülvizsgálatának olvasása  | Biztonsági olvasó | Biztonsági rendszergazda, Kiemelt szerepkörű rendszergazda
Az összes konfiguráció olvasása | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 

## <a name="security---authentication-methods"></a>Biztonság – hitelesítési módszerek

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Hitelesítési módszerek konfigurálása | Globális rendszergazda | 
Az összes konfiguráció olvasása | Globális rendszergazda | 

## <a name="security---conditional-access"></a>Biztonság – feltételes hozzáférés

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
MFA megbízható IP-címeinek konfigurálása | Feltételes hozzáférésű rendszergazda | 
Egyéni vezérlők létrehozása | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Elnevezett helyszínek létrehozása | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Szabályzatok létrehozása | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Használati feltételek létrehozása | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
VPN-kapcsolati tanúsítvány létrehozása | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Klasszikus szabályzat törlése | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Használati feltételek törlése | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
VPN-kapcsolat tanúsítványának törlése | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Klasszikus szabályzat letiltása | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Egyéni vezérlők kezelése | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Elnevezett helyszínek kezelése | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Használati feltételek kezelése | Feltételes hozzáférésű rendszergazda | Biztonsági rendszergazda
Az összes konfiguráció olvasása | Biztonsági olvasó | Biztonsági rendszergazda
Elnevezett helyeinek olvasása | Biztonsági olvasó | Feltételes hozzáférésű rendszergazda, biztonsági rendszergazda

## <a name="security---identity-security-score"></a>Biztonság – identitás biztonsági pontszáma

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök | 
---- | --------------------- | ----------------
Az összes konfiguráció olvasása | Biztonsági olvasó | Biztonsági rendszergazda
Biztonsági pontszám olvasása | Biztonsági olvasó | Biztonsági rendszergazda
Esemény állapotának frissítése | Biztonsági rendszergazda | 

## <a name="security---risky-sign-ins"></a>Biztonság – kockázatos bejelentkezések

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Az összes konfiguráció olvasása | Biztonsági olvasó | 
Kockázatos bejelentkezések olvasása | Biztonsági olvasó | 

## <a name="security---users-flagged-for-risk"></a>Biztonság – a kockázatnak megjelölt felhasználók

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Az összes esemény elvetését | Biztonsági rendszergazda | 
Az összes konfiguráció olvasása | Biztonsági olvasó | 
A kockázatnak megjelölt felhasználók olvasása | Biztonsági olvasó | 

## <a name="users"></a>Felhasználók

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Felhasználó hozzáadása a címtárbeli szerepkörhöz | Kiemelt szerepkörű rendszergazda | 
Felhasználó felvétele a csoportba | Felhasználói rendszergazda | 
Licenc kiosztása | Licenc rendszergazdája | Felhasználói rendszergazda
Vendég felhasználó létrehozása | Vendég meghívója | Felhasználói rendszergazda
Felhasználó létrehozása | Felhasználói rendszergazda | 
Felhasználók törlése | Felhasználói rendszergazda | 
Korlátozott rendszergazdák frissítési jogkivonatának érvénytelenítése (lásd a dokumentációt) | Felhasználói rendszergazda | 
Nem rendszergazdák frissítési tokenek érvénytelenítése (lásd a dokumentációt) | Jelszó-rendszergazda | Felhasználói rendszergazda
A Kiemelt rendszergazdák frissítési jogkivonatának érvénytelenítése (lásd: dokumentáció) | Globális rendszergazda | 
Alapszintű konfiguráció olvasása | Alapértelmezett felhasználói szerepkör ([lásd a dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Korlátozott rendszergazdák jelszavának alaphelyzetbe állítása (lásd: dokumentáció) | Felhasználói rendszergazda | 
Nem rendszergazdák jelszavának alaphelyzetbe állítása (lásd: dokumentáció) | Jelszó-rendszergazda | Felhasználói rendszergazda
Emelt szintű rendszergazdák jelszavának alaphelyzetbe állítása | Globális rendszergazda | 
Licenc visszavonása | Licenc rendszergazdája | Felhasználói rendszergazda
Az összes tulajdonság frissítése az egyszerű felhasználónév kivételével | Felhasználói rendszergazda | 
Egyszerű felhasználónevek frissítése korlátozott rendszergazdák számára (lásd: dokumentáció) | Felhasználói rendszergazda | 
Az egyszerű felhasználónév tulajdonság frissítése a Kiemelt rendszergazdáknál (lásd: dokumentáció) | Globális rendszergazda | 
Felhasználói beállítások frissítése | Globális rendszergazda | 


## <a name="support"></a>Támogatás

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Támogatási jegy beküldése | Szolgáltatás-rendszergazda | Alkalmazás-rendszergazda, Azure Information Protection rendszergazda, számlázási rendszergazda, Felhőbeli alkalmazás rendszergazdája, megfelelőségi rendszergazda, Dynamics 365-rendszergazda, asztali elemzési rendszergazda, Exchange-rendszergazda, jelszó Rendszergazda, Intune-rendszergazda, Skype vállalati rendszergazda, Power BI rendszergazda, emelt szintű hitelesítési rendszergazda, SharePoint-rendszergazda, csapat kommunikációs rendszergazda, csapat rendszergazdája, felhasználói rendszergazda, Munkahelyi elemzési rendszergazda

## <a name="next-steps"></a>Következő lépések

* [Azure AD-rendszergazdai szerepkörök kiosztása vagy eltávolítása](directory-manage-roles-portal.md)
* [Az Azure AD rendszergazdai szerepköreinek referenciája](directory-assign-admin-roles.md)
