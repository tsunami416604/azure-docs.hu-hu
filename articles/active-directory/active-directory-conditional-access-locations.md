---
title: "Azure Active Directory feltételes hozzáférési feltételek hely |} Microsoft Docs"
description: "Útmutató a felhőalapú alkalmazások, a felhasználó hálózati helye alapján való hozzáférést a hely feltétel használatával."
services: active-directory
keywords: "alkalmazások, a feltételes hozzáférés az Azure ad-vel, a biztonságos hozzáférés a vállalati erőforrásokhoz, a feltételes hozzáférési házirendekkel a feltételes hozzáférés"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/11/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 47b4d70c991bd618ea4ea6e5d2fd1dea86911798
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="location-conditions-in-azure-active-directory-conditional-access"></a>Azure Active Directory feltételes hozzáférési feltételek helye 

A [Azure Active Directory (Azure AD) feltételes hozzáférés](active-directory-conditional-access-azure-portal.md), szabályozhatja, hogy hogyan jogosult felhasználók úgy férhetnek hozzá a felhőalapú alkalmazásokhoz. A feltételes hozzáférési házirend hely feltétele összekötését hozzáférés vezérlők annak a hálózati helyek, a felhasználók teszi lehetővé.

Ez a cikk azt ismerteti a konfigurálnia kell a helyre feltétel. 

## <a name="locations"></a>Helyek

Az Azure AD lehetővé teszi, hogy egyszeri bejelentkezéshez az eszközöket, alkalmazásokat, és szolgáltatásokhoz bárhonnan a nyilvános interneten. A hely a feltétellel a felhőalapú alkalmazásokhoz, a felhasználó hálózati helye alapján való szabályozhatja a hozzáférést. A hely feltétel gyakori alkalmazási esetei a következők:

- Többtényezős hitelesítés megkövetelése a felhasználók szolgáltatások elérésére, ha a vállalati hálózatból  

- Blokkolja a hozzáférést a felhasználók számára az adott országokban vagy régiókban szolgáltatások elérésére. 

Egy hely egy hálózati helyre, vagy az elnevezett hely vagy a multi-factor authentication jelöli megbízható IP-címek egy címke.


## <a name="named-locations"></a>Névvel ellátott helyek 

Elnevezett helyek hozhat létre IP-címtartományok, országokban és régiókban logikai csoportjai. 

 A név helye a következő részből áll:

![Helyek](./media/active-directory-conditional-access-locations/42.png)

- **Név** -névvel rendelkező hely megjelenítendő nevét.

- **IP-címtartományok** -egy vagy több IP-címtartományt a CIDR formátumban.

- **Megbízható helyen megjelölés** -jelző adhatja meg egy nevesített helyet a megbízható helyének jelzése. Megbízható helyek általában az informatikai részleg által vezérelt hálózati területek. Feltételes hozzáférés mellett megbízható elnevezett helyeken is használják, és az Azure AD Azure Identity Protection biztonsági jelentések csökkentése érdekében [téves](active-directory-reporting-risk-events.md#impossible-travel-to-atypical-locations-1).

- **Ország / régió** – Ez a beállítás lehetővé teszi egy vagy több országban vagy régióban elnevezett hely meghatározásához. 

- **Ismeretlen területek** -Some IP-címek nem egy adott országban vannak leképezve. Ezt a beállítást válassza, ha a következő IP-címek fel kell venni a névvel rendelkező hely teszi lehetővé. Jelölőnégyzet lehetnek, ha a házirend az elnevezett hely segítségével ismeretlen helyekhez kell alkalmazni.

Elnevezett helyek konfigurálhatja az Azure ad-ben a kapcsolódó objektum mérete korlátozza. Konfigurálhatja:

- Egy nevű hellyel, amely legfeljebb 1200-as IP-címtartományok.

- Legfeljebb egy IP-címtartomány hozzájuk rendelt 90 elnevezett helyekre.




## <a name="trusted-ips"></a>Megbízható IP-címek

IP-címtartományok képviselő a szervezet helyi intranet az is beállítható a [multi-factor authentication szolgáltatás beállításainak](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Ez a funkció lehetővé teszi, hogy legfeljebb 50 IP-címtartományok konfigurálhatja. Az IP-címtartományt CIDR-formátumban vannak. További információkért lásd: [megbízható IP-címek](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).  

Ha a konfigurált IP-címek megbízhatónak, azok megjelennek, a **MFA megbízható IP-CÍMEK** helyét. a hely feltétel közül.   

### <a name="skipping-multi-factor-authentication"></a>Többtényezős hitelesítés kihagyása

A multi-factor authentication szolgáltatás beállításainak lapján azonosíthatja, vállalati intranetes felhasználók kiválasztásával **összevont felhasználók intranetről érkező kérelmeket a multi-factor authentication kihagyása**. Ez a beállítás jelzi, hogy a belső vállalati hálózati jogcímek, az AD FS által kibocsátott, legyen megbízható, és azonosítja a felhasználót, hogy a vállalati hálózaton. További információkért lásd: [a megbízható IP-címek szolgáltatás engedélyezze a feltételes hozzáférés](../multi-factor-authentication/multi-factor-authentication-whats-next.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Ezen beállítás után például elnevezett helye **MFA megbízható IP-CÍMEK** a kijelölt összes házirend érvényes lesz.

Hordozható és asztali alkalmazások, amelyek rendelkeznek a munkamenet-élettartamok hosszabb élettartamú, a feltételes hozzáférés rendszeresen újraértékelése is megtörténik. Az alapértelmezett érték egyszer egy óra. Ha a belső vállalati hálózaton jogcímet, és csak az első hitelesítés alkalmával ki, az Azure AD nem lehet a megbízható IP-címtartománylista. Ebben az esetben azonban nehezebb annak megállapításához, hogy a felhasználó továbbra is a vállalati hálózaton:

1. Ellenőrizze, hogy a felhasználó IP-cím van-e a megbízható IP-címtartományok egyikében.

2. Ellenőrizze, hogy az első három oktettjének meg a felhasználó IP-cím egyezik-e az első 3 oktettjének meg az első hitelesítés IP-címét. Az IP-cím összeveti az első hitelesítés mivel ha a belső vállalati hálózaton jogcímek eredetileg adta ki, és a felhasználó helye lett érvényesítve.

Ha két lépést nem sikerül, akkor a felhasználó akkor tekinthető többé nem egy megbízható IP-címen.



## <a name="location-condition-configuration"></a>Az állapot konfigurációját

Amikor konfigurálja a hely feltétel, lehetősége van megkülönböztetésére:

- Bármely hely 
- Minden megbízható hely
- Kijelölt helyek

![Helyek](./media/active-directory-conditional-access-locations/01.png)

### <a name="any-location"></a>Bármely hely

Alapértelmezés szerint kiválasztásával **bárhova** hatására az összes IP-címet, alkalmazandó egy házirendet, amely azt jelenti, hogy a cím az interneten. Ez a beállítás nem nincs korlátozva elnevezett helyként konfigurált IP-címeket. Ha bejelöli **bárhova**, továbbra is kizárhatja az adott helyeken egy házirend. Például alkalmazhat egy házirendet az összes hely excepts a megbízható helyek hatókörének beállításához az helyekre, kivéve a vállalati hálózathoz.

### <a name="all-trusted-locations"></a>Minden megbízható hely

Ez a beállítás a következőkre vonatkozik:

- Megbízható helyként jelölt összes hely
- **Többtényezős hitelesítés megbízható IP-CÍMEK** (Ha be van állítva)


### <a name="selected-locations"></a>Kijelölt helyek

Ezt a lehetőséget válassza ki egy vagy több elnevezett helye. Ezt a beállítást alkalmazza a házirend a felhasználó nem csatlakoztatja a a kiválasztott helyeken. Kattint Whe **kiválasztása** nevesített hálózatok listáját jeleníti meg elnevezett hálózati kijelölés vezérlő nyílik meg. A lista mutatja azokat is, ha a hálózati hely jelölte meg megbízhatóként. Az elnevezett hely nevű **MFA megbízható IP-címek** használható az IP-beállításait a multi-factor authentication szolgáltatás beállítás lapján konfigurálható.

## <a name="what-you-should-know"></a>Tudnivalók

### <a name="when-is-a-location-evaluated"></a>Ha értékeli ki a helyet?

Feltételes hozzáférési házirendek kiértékelése során: 

- Egy felhasználó először jelentkezik be 

- Az Azure AD kibocsát egy token a cloud app a feltételes hozzáférési házirend be van állítva. 

Alapértelmezés szerint az Azure AD kibocsát egy token óránként. Helyezze át a vállalati hálózatból, egy órán belül a házirend érvényesítve van a modern hitelesítést használó alkalmazások.


### <a name="user-ip-address"></a>User IP address

A házirendek értékelésének használt IP-címét a felhasználó a nyilvános IP-címe. Egy magánhálózaton eszközök esetében ez nem az ügyfél IP-címét a felhasználó eszközén az intraneten, hogy a cím, a nyilvános internethez való kapcsolódáshoz a hálózat által használt. 

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Tömeges feltöltése és letöltéséhez elnevezett helyek

Amikor létrehozása vagy frissítése tömeges frissíti, a helyek nevű feltöltheti, vagy az IP-címtartományai a CSV-fájl letöltése. Feltöltés a listán az IP-címtartományok lecseréli azokat a fájlból. A fájl minden sora egy IP-címtartományt CIDR formátumban. 


### <a name="cloud-proxies-and-vpns"></a>Felhő proxyk és virtuális magánhálózatok 

A felhőben üzemeltetett proxy- vagy VPN-megoldás használata esetén az IP-cím az Azure AD használja, míg házirend kiértékelése a proxy IP-címét. A X-Forwarded-For (XFF) fejlécet tartalmazta, amely tartalmazza a felhasználók nyilvános IP-címet nem használja, mert nem lehet érvényesíteni, amely egy megbízható forrásból származik, bemutatja egy módszert faking IP-címet. 

Ha egy felhőbeli proxy van-e, egy házirendet, amely egy tartományhoz csatlakoztatott eszközről használható megkövetelésére szolgál, vagy a belső vállalati hálózat jogcímek az AD FS.



### <a name="api-support-and-powershell"></a>API-támogatása és a PowerShell 

API és PowerShell jelenleg nem támogatott elnevezett helyeket, vagy a feltételes hozzáférési házirendekhez.





## <a name="next-steps"></a>További lépések

- Ha meg szeretné ismerni a feltételes hozzáférési házirend konfigurálása tudnivalókat [Ismerkedés a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal-get-started.md).

- Ha készen áll a környezet feltételes hozzáférési házirend-beállításokkal, tekintse meg a [ajánlott eljárások a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-best-practices.md). 
