---
title: A legkevésbé Kiemelt szerepkörök delegálása rendszergazdai feladat szerint – Azure Active Directory | Microsoft Docs
description: Az identitási feladatokhoz delegált szerepkörök Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 05/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62814bb1e2934c406dc5fb1eb3b3bc3f3d2ee3b7
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135551"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Rendszergazdai szerepkörök felügyeleti feladat szerint Azure Active Directory

Ebből a cikkből megtudhatja, hogy milyen információkra van szükség a felhasználók rendszergazdai engedélyeinek korlátozásához, ha a Azure Active Directory (Azure AD) szolgáltatásban a legkevésbé Kiemelt szerepköröket rendeli hozzá. Az egyes feladatok végrehajtásához szükséges rendszergazdai feladatokat és a minimálisan Kiemelt szerepkört, valamint a feladatot elvégző további nem globális rendszergazdai szerepköröket is megtalálja.

## <a name="application-proxy"></a>Alkalmazásproxy

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Alkalmazásproxy alkalmazásának konfigurálása | Alkalmazás-rendszergazda | 
Összekötő csoport tulajdonságainak konfigurálása | Alkalmazás-rendszergazda | 
Alkalmazás-regisztráció létrehozása, ha a képesség le van tiltva az összes felhasználónál | Alkalmazásfejlesztő | Cloud Application Administrator, alkalmazás-rendszergazda
Összekötő-csoport létrehozása | Alkalmazás-rendszergazda | 
Összekötő-csoport törlése | Alkalmazás-rendszergazda | 
Alkalmazásproxy letiltása | Alkalmazás-rendszergazda | 
Összekötőszolgáltatás letöltése | Alkalmazás-rendszergazda | 
Az összes konfiguráció olvasása | Alkalmazás-rendszergazda | 

## <a name="b2c"></a>B2C

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Azure AD B2C könyvtárak létrehozása | Minden nem vendég felhasználó ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)a dokumentációt) | 
B2C-alkalmazások létrehozása | Globális rendszergazda | 
Vállalati alkalmazások létrehozása | Felhőalkalmazás-rendszergazda | Alkalmazás-rendszergazda
B2C-szabályzatok létrehozása, olvasása, frissítése és törlése | Globális rendszergazda | 
Identitás-szolgáltatók létrehozása, olvasása, frissítése és törlése | Globális rendszergazda | 
Jelszó-visszaállítási felhasználói folyamatok létrehozása, olvasása, frissítése és törlése | Globális rendszergazda | 
Profil-szerkesztési felhasználói folyamatok létrehozása, olvasása, frissítése és törlése | Globális rendszergazda | 
Bejelentkezési felhasználói folyamatok létrehozása, olvasása, frissítése és törlése | Globális rendszergazda | 
Regisztrációs felhasználói folyamat létrehozása, olvasása, frissítése és törlése |Globális rendszergazda | 
Felhasználói attribútumok létrehozása, olvasása, frissítése és törlése | Globális rendszergazda | 
Felhasználók létrehozása, olvasása, frissítése és törlése | Globális rendszergazda ([lásd](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs)a dokumentációt)
Az összes konfiguráció olvasása | Globális rendszergazda | 
B2C-naplók olvasása | Globális rendszergazda ([lásd](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs)a dokumentációt) | 

> [!NOTE]
> Azure AD B2C globális rendszergazdák nem rendelkeznek ugyanazokkal az engedélyekkel, mint az Azure AD globális rendszergazdái. Ha Azure AD B2C globális rendszergazdai jogosultságokkal rendelkezik, győződjön meg róla, hogy Azure AD B2C könyvtárban van, és nem Azure AD-címtár.

## <a name="company-branding"></a>Vállalati védjegyezés

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Vállalati védjegyezés konfigurálása | Globális rendszergazda | 
Az összes konfiguráció olvasása | Címtárolvasók | Alapértelmezett felhasználói szerepkör ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)a dokumentációt)

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
Szolgáltatások hozzáadása vagy törlése | Tulajdonos ([lásd](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)a dokumentációt) | 
Hibajavítások alkalmazása szinkronizálási hiba esetén | Közreműködő ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)a dokumentációt) | Tulajdonos
Értesítések konfigurálása | Közreműködő ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)a dokumentációt) | Tulajdonos
Beállítások konfigurálása | Tulajdonos ([lásd](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)a dokumentációt) | 
Szinkronizálási értesítések konfigurálása | Közreműködő ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)a dokumentációt) | Tulajdonos
ADFS biztonsági jelentések beolvasása | Biztonsági olvasó | Közreműködő, tulajdonos
Az összes konfiguráció olvasása | Olvasó ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)a dokumentációt) | Közreműködő, tulajdonos
Szinkronizálási hibák olvasása | Olvasó ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)a dokumentációt) | Közreműködő, tulajdonos
Szinkronizálási szolgáltatások olvasása | Olvasó ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)a dokumentációt) | Közreműködő, tulajdonos
Metrikák és riasztások megtekintése | Olvasó ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)a dokumentációt) | Közreműködő, tulajdonos
Metrikák és riasztások megtekintése | Olvasó ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)a dokumentációt) | Közreműködő, tulajdonos
Szinkronizálási szolgáltatás metrikáinak és értesítéseinek megtekintése | Olvasó ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)a dokumentációt) | Közreműködő, tulajdonos


## <a name="custom-domain-names"></a>Egyéni tartománynevek

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Tartományok kezelése | Globális rendszergazda | 
Az összes konfiguráció olvasása | Címtárolvasók | Alapértelmezett felhasználói szerepkör ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)a dokumentációt)

## <a name="domain-services"></a>Tartományi szolgáltatások

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Azure AD Domain Services példány létrehozása | Globális rendszergazda | 
Az összes Azure AD Domain Services feladat végrehajtása | Azure AD DC-rendszergazdák csoport ([lásd](../../active-directory-domain-services/tutorial-create-management-vm.md#administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain)a dokumentációt) | 
Az összes konfiguráció olvasása | AD DS szolgáltatást tartalmazó Azure-előfizetés olvasója | 

## <a name="devices"></a>Eszközök

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Eszköz letiltása | Felhőeszköz-rendszergazda | 
Eszköz engedélyezése | Felhőeszköz-rendszergazda | 
Alapszintű konfiguráció olvasása | Alapértelmezett felhasználói szerepkör ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)a dokumentációt) | 
BitLocker-kulcsok olvasása | Biztonsági olvasó | Jelszó-rendszergazda, biztonsági rendszergazda

## <a name="enterprise-applications"></a>Vállalati alkalmazások

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Beleegyezik a delegált engedélyekkel | Felhőalkalmazás-rendszergazda | Alkalmazás-rendszergazda
Az alkalmazás engedélyeinek belefoglalása a Microsoft Graph vagy az Azure AD gráf nélkül | Felhőalkalmazás-rendszergazda | Alkalmazás-rendszergazda
Az alkalmazás engedélyeinek Microsoft Graph vagy Azure AD Graphba való beleegyezett | Globális rendszergazda | 
A saját adatokhoz hozzáférő alkalmazások beleegyezett | Alapértelmezett felhasználói szerepkör ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)a dokumentációt) | 
Vállalati alkalmazás létrehozása | Felhőalkalmazás-rendszergazda | Alkalmazás-rendszergazda
Alkalmazásproxy kezelése | Alkalmazás-rendszergazda | 
Felhasználói beállítások kezelése | Globális rendszergazda | 
Csoport vagy alkalmazás hozzáférési felülvizsgálatának olvasása | Biztonsági olvasó | Biztonsági rendszergazda, felhasználói rendszergazda
Az összes konfiguráció olvasása | Alapértelmezett felhasználói szerepkör ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)a dokumentációt) | 
Vállalati alkalmazás-hozzárendelések frissítése | Vállalati alkalmazás tulajdonosa ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)a dokumentációt) | Cloud Application Administrator, alkalmazás-rendszergazda
Vállalati alkalmazások tulajdonosainak frissítése | Vállalati alkalmazás tulajdonosa ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)a dokumentációt) | Cloud Application Administrator, alkalmazás-rendszergazda
Vállalati alkalmazás tulajdonságainak frissítése | Vállalati alkalmazás tulajdonosa ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)a dokumentációt) | Cloud Application Administrator, alkalmazás-rendszergazda
Vállalati alkalmazások üzembe helyezésének frissítése | Vállalati alkalmazás tulajdonosa ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)a dokumentációt) | Cloud Application Administrator, alkalmazás-rendszergazda
Vállalati alkalmazás önkiszolgáló frissítése | Vállalati alkalmazás tulajdonosa ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)a dokumentációt) | Cloud Application Administrator, alkalmazás-rendszergazda
Egyszeri bejelentkezési tulajdonságok frissítése | Vállalati alkalmazás tulajdonosa ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)a dokumentációt) | Cloud Application Administrator, alkalmazás-rendszergazda



## <a name="groups"></a>Csoportok

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Licenc hozzárendelése | Felhasználói adminisztrátor | 
Csoport létrehozása | Felhasználói adminisztrátor | 
Csoport vagy alkalmazás hozzáférési felülvizsgálatának létrehozása, frissítése vagy törlése | Felhasználói adminisztrátor | 
Csoport lejáratának kezelése | Felhasználói adminisztrátor | 
Csoportbeállítások kezelése | Globális rendszergazda | 
Az összes konfiguráció olvasása (kivéve a rejtett tagságot) | Címtárolvasók | Alapértelmezett felhasználói szerepkör ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)a dokumentációt)
Rejtett tagság olvasása | Csoporttag | Csoport tulajdonosa, jelszó-rendszergazda, Exchange-rendszergazda, SharePoint-rendszergazda, csapat rendszergazdája, felhasználói rendszergazda
Rejtett tagsággal rendelkező csoportok tagságának olvasása | Ügyfélszolgálati adminisztrátor | Felhasználói rendszergazda, csapat rendszergazdája
Licenc visszavonása | Licencadminisztrátor | Felhasználói adminisztrátor
Csoporttagság frissítése | Csoport tulajdonosa ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)a dokumentációt) | Felhasználói adminisztrátor
Csoport tulajdonosainak frissítése | Csoport tulajdonosa ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)a dokumentációt) | Felhasználói adminisztrátor
Csoport tulajdonságainak frissítése | Csoport tulajdonosa ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)a dokumentációt) | Felhasználói adminisztrátor

## <a name="identity-protection"></a>Identitásvédelem

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
Licenc hozzárendelése | Licencadminisztrátor | Felhasználói adminisztrátor
Az összes konfiguráció olvasása | Címtárolvasók | Alapértelmezett felhasználói szerepkör ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)a dokumentációt)
Licenc visszavonása | Licencadminisztrátor | Felhasználói adminisztrátor
Előfizetés kipróbálása vagy megvásárlása | Számlázási adminisztrátor | 


## <a name="monitoring---audit-logs"></a>Figyelés – naplók

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Naplófájlok olvasása | Jelentésolvasó | Biztonsági olvasó, biztonsági rendszergazda

## <a name="monitoring---sign-ins"></a>Figyelés – bejelentkezések

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Bejelentkezési naplók olvasása | Jelentésolvasó | Biztonsági olvasó, biztonsági rendszergazda

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

## <a name="organizational-relationships"></a>Céges kapcsolatok

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Identitásszolgáltatók kezelése | Globális rendszergazda | 
Beállítások kezelése | Globális rendszergazda | 
Használati feltételek kezelése | Globális rendszergazda | 
Az összes konfiguráció olvasása | Globális rendszergazda | 

## <a name="password-reset"></a>Jelszó alaphelyzetbe állítva

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Hitelesítési módszerek konfigurálása | Globális rendszergazda |
Testreszabás konfigurálása | Globális rendszergazda |
Értesítés konfigurálása | Globális rendszergazda |
Helyszíni integráció konfigurálása | Globális rendszergazda |
Jelszó-visszaállítási tulajdonságok konfigurálása | Felhasználói rendszergazda | Globális rendszergazda
Regisztráció beállítása | Globális rendszergazda |
Az összes konfiguráció olvasása | Biztonsági rendszergazda | Felhasználói rendszergazda |

## <a name="privileged-identity-management"></a>Privilegizált identitások kezelése

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Felhasználók társítása szerepkörökhöz | Kiemelt szerepkörgazda | 
Szerepkör-beállítások konfigurálása | Kiemelt szerepkörgazda | 
Naplózási tevékenység megtekintése | Biztonsági olvasó | 
Szerepkör-tagságok megtekintése | Biztonsági olvasó | 

## <a name="roles-and-administrators"></a>Szerepkörök és a rendszergazdák

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Szerepkör-hozzárendelések kezelése | Kiemelt szerepkörgazda | 
Azure AD-szerepkör hozzáférési felülvizsgálatának olvasása  | Biztonsági olvasó | Biztonsági rendszergazda, Kiemelt szerepkörű rendszergazda
Az összes konfiguráció olvasása | Alapértelmezett felhasználói szerepkör ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)a dokumentációt) | 

## <a name="security---authentication-methods"></a>Biztonság – hitelesítési módszerek

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Hitelesítési módszerek konfigurálása | Globális rendszergazda | 
Az összes konfiguráció olvasása | Globális rendszergazda | 

## <a name="security---conditional-access"></a>Biztonság – feltételes hozzáférés

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
MFA megbízható IP-címeinek konfigurálása | Feltételes hozzáférési rendszergazda | 
Egyéni vezérlők létrehozása | Feltételes hozzáférési rendszergazda | Biztonsági rendszergazda
Elnevezett helyszínek létrehozása | Feltételes hozzáférési rendszergazda | Biztonsági rendszergazda
Szabályzatok létrehozása | Feltételes hozzáférési rendszergazda | Biztonsági rendszergazda
Használati feltételek létrehozása | Feltételes hozzáférési rendszergazda | Biztonsági rendszergazda
VPN-kapcsolati tanúsítvány létrehozása | Feltételes hozzáférési rendszergazda | Biztonsági rendszergazda
Klasszikus szabályzat törlése | Feltételes hozzáférési rendszergazda | Biztonsági rendszergazda
Használati feltételek törlése | Feltételes hozzáférési rendszergazda | Biztonsági rendszergazda
VPN-kapcsolat tanúsítványának törlése | Feltételes hozzáférési rendszergazda | Biztonsági rendszergazda
Klasszikus szabályzat letiltása | Feltételes hozzáférési rendszergazda | Biztonsági rendszergazda
Egyéni vezérlők kezelése | Feltételes hozzáférési rendszergazda | Biztonsági rendszergazda
Elnevezett helyszínek kezelése | Feltételes hozzáférési rendszergazda | Biztonsági rendszergazda
Használati feltételek kezelése | Feltételes hozzáférési rendszergazda | Biztonsági rendszergazda
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
Összes esemény bezárása | Biztonsági rendszergazda | 
Az összes konfiguráció olvasása | Biztonsági olvasó | 
A kockázatnak megjelölt felhasználók olvasása | Biztonsági olvasó | 

## <a name="users"></a>Felhasználók

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Felhasználó hozzáadása a címtárbeli szerepkörhöz | Kiemelt szerepkörgazda | 
Tag hozzáadása a csoporthoz | Felhasználói adminisztrátor | 
Licenc hozzárendelése | Licencadminisztrátor | Felhasználói adminisztrátor
Vendég felhasználó létrehozása | Vendég meghívója | Felhasználói adminisztrátor
Felhasználó létrehozása | Felhasználói adminisztrátor | 
Felhasználók törlése | Felhasználói adminisztrátor | 
Korlátozott rendszergazdák frissítési jogkivonatának érvénytelenítése (lásd a dokumentációt) | Felhasználói adminisztrátor | 
Nem rendszergazdák frissítési tokenek érvénytelenítése (lásd a dokumentációt) | Jelszókezelő | Felhasználói adminisztrátor
A Kiemelt rendszergazdák frissítési jogkivonatának érvénytelenítése (lásd: dokumentáció) | Globális rendszergazda | 
Alapszintű konfiguráció olvasása | Alapértelmezett felhasználói szerepkör ([lásd](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) a dokumentációt | 
Korlátozott rendszergazdák jelszavának alaphelyzetbe állítása (lásd: dokumentáció) | Felhasználói adminisztrátor | 
Nem rendszergazdák jelszavának alaphelyzetbe állítása (lásd: dokumentáció) | Jelszókezelő | Felhasználói adminisztrátor
Emelt szintű rendszergazdák jelszavának alaphelyzetbe állítása | Globális rendszergazda | 
Licenc visszavonása | Licencadminisztrátor | Felhasználói adminisztrátor
Az összes tulajdonság frissítése az egyszerű felhasználónév kivételével | Felhasználói adminisztrátor | 
Egyszerű felhasználónevek frissítése korlátozott rendszergazdák számára (lásd: dokumentáció) | Felhasználói adminisztrátor | 
Az egyszerű felhasználónév tulajdonság frissítése a Kiemelt rendszergazdáknál (lásd: dokumentáció) | Globális rendszergazda | 
Felhasználói beállítások frissítése | Globális rendszergazda | 


## <a name="support"></a>Támogatás

Tevékenység | Legkevésbé Kiemelt szerepkör | További szerepkörök
---- | --------------------- | ----------------
Támogatási jegy beküldése | Szolgáltatás-rendszergazda | Alkalmazás-rendszergazda, Azure Information Protection rendszergazda, számlázási rendszergazda, Felhőbeli alkalmazás rendszergazdája, megfelelőségi rendszergazda, Dynamics 365-rendszergazda, asztali elemzési rendszergazda, Exchange-rendszergazda, jelszó Rendszergazda, Intune-rendszergazda, Skype vállalati rendszergazda, Power BI rendszergazda, emelt szintű hitelesítési rendszergazda, SharePoint-rendszergazda, csapat kommunikációs rendszergazda, csapat rendszergazdája, felhasználói rendszergazda, Munkahelyi elemzési rendszergazda

## <a name="next-steps"></a>További lépések

* [Azure AD-rendszergazdai szerepkörök kiosztása vagy eltávolítása](directory-manage-roles-portal.md)
* [Az Azure AD rendszergazdai szerepköreinek referenciája](directory-assign-admin-roles.md)
