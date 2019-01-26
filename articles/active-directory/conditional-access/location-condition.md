---
title: Mi az a helyszínre vonatkozó feltételek az Azure Active Directory feltételes hozzáférés? | Microsoft Docs
description: Megtudhatja, hogyan férhet hozzá a felhőalapú alkalmazások, a felhasználó hálózati helye alapján a hely feltétel használatával.
services: active-directory
keywords: feltételes hozzáférés az alkalmazásokhoz, feltételes hozzáférés az Azure AD-vel, biztonságos hozzáférés a vállalati erőforrásokhoz, feltételes hozzáférési szabályzatok
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/21/2019
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 26721aa0eac69875f6a3704025e6ab71a54a1e31
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55078100"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Mi a helyfeltétel az Azure Active Directory feltételes hozzáférés? 

A [Azure Active Directory (Azure AD) feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md), szabályozhatja, hogy jogosult felhasználók hozzáférhetnek a felhőbeli alkalmazásokat. A hely feltétel egy feltételes hozzáférési szabályzat segítségével elősegítsék a hálózati helyek, a felhasználók a hozzáférés vezérlők beállításai.

Ez a cikk a helyfeltétel konfigurálásához szükséges információkat biztosít. 

## <a name="locations"></a>Helyek

Az Azure AD lehetővé teszi, hogy egyszeri bejelentkezést az eszközöket, alkalmazásokat és szolgáltatásokhoz bárhonnan a nyilvános interneten. A hely a feltétellel a felhőalkalmazásokhoz, a felhasználó hálózati helye alapján szabályozhatja a hozzáférést. A hely feltétel gyakori alkalmazási helyzetek a következők:

- A felhasználók a szolgáltatás elérésére, amelyek a vállalati hálózatról a multi-factor authentication megkövetelése  

- Blokkolja a hozzáférést a felhasználók bizonyos országokban vagy régiókban a szolgáltatás elérésére. 

Egy hely a címkét, egy hálózati helyre, vagy jelöli egy elnevezett helyet, vagy a multi-factor authentication megbízható IP-címek.


## <a name="named-locations"></a>Névvel ellátott helyek 

Nevesített helyek, az IP-címtartományok, országok és régiók logikai csoportosításán hozhat létre. 

A nevesített helyek a érheti el a **kezelés** a feltételes hozzáférés szakaszának.

![Helyek](./media/location-condition/02.png)

 


Egy elnevezett helye a következő összetevőket tartalmazza:

![Helyek](./media/location-condition/42.png)

- **Név** -névvel rendelkező hely megjelenített neve.

- **IP-címtartományok** – legalább egy IPv4-címtartományokat CIDR formátumban. Egy Ipv6-címtartomány megadása nem támogatott.

- **Megjelölés megbízható helyként** -jelzőt beállíthat egy elnevezett helyet megbízható helyen jelzi. Megbízható helyek jellemzően az IT-részleg által vezérelt hálózati területek. Feltételes hozzáférés mellett megbízható nevesített helyek is használják az Azure Identity Protection és az Azure AD biztonsági jelentések csökkentése érdekében [vakriasztások](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1).

- **Ország / régió** – Ez a beállítás lehetővé teszi egy vagy több országban vagy régióban, adjon meg egy nevesített. 

- **Ismeretlen területek belefoglalása** -néhány IP-címek nincsenek leképezve egy adott országban. Ezzel a beállítással kiválaszthatja, ha az IP-címeket kell szerepelnie a nevű helyen. Ezek lehet ellenőrzés esetén a szabályzat segítségével az elnevezett hely alkalmazni kell az ismeretlen helyekkel.

Az Azure ad-ben a kapcsolódó objektum mérete konfigurálható nevesített helyek száma korlátozza. Konfigurálhatja:

- Az egyik nevű akár 1200-as IP-címtartományok helyét.

- Legfeljebb 90 nevesített helyek, a hozzájuk rendelt egy IP-címtartományt.




## <a name="trusted-ips"></a>Megbízható IP-címek

A szervezet helyi intranet jelölő IP-címtartományok is konfigurálhatja a [multi-factor authentication szolgáltatás beállításainak](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Ez a funkció lehetővé teszi legfeljebb 50 IP-címtartományok. Az IP-címtartományok a CIDR-formátumban vannak. További információkért lásd: [megbízható IP-címek](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

A megbízható IP-címek konfigurálása, ha azok megjelenjen **MFA megbízható IP-CÍMEK** az a hely feltétel helyek listáját.   

### <a name="skipping-multi-factor-authentication"></a>A multi-factor authentication kihagyása

A beállítások lapon a multi-factor authentication szolgáltatás, azonosíthatók vállalati intranetes felhasználók kiválasztásával **multi-factor authentication kihagyása az összevont felhasználók intranetről**. Ez a beállítás azt jelzi, hogy a belső vállalati hálózati jogcímet, amely az AD FS által kiadott, megbízható és kell, hogy a vállalati hálózaton a felhasználó azonosítására szolgál. További információkért lásd: [a megbízható IP-címek funkció engedélyezése a feltételes hozzáférés használatával](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Ezt a beállítást az ellenőrzés után többek között a névvel rendelkező hely **MFA megbízható IP-CÍMEK** a kiválasztott bármely szabályzat érvényes lesz.

Asztali és mobil alkalmazások, amelyek hosszú éltek meg a munkamenetek élettartamát, a feltételes hozzáférés rendszeres időközönként újraértékelése is megtörténik. Az alapértelmezett érték egyszer egy óra. Ha a belső vállalati hálózaton jogcím, és csak az első hitelesítés alkalmával kiállított, az Azure AD nem rendelkezik a megbízható IP-címtartományok listáját. Ebben az esetben nehezebb annak megállapításához, hogy a felhasználó továbbra is a vállalati hálózaton:

1. Ellenőrizze, hogy a felhasználó IP-cím van-e a megbízható IP-címtartományok egyikében.

2. Ellenőrizze, hogy megfelel-e a felhasználó IP-cím első három oktettjének az első hitelesítés IP-címét, az első 3 bájt. Az IP-cím a rendszer összehasonlítja a kezdeti hitelesítéshez, mivel ez mikor a belső vállalati hálózaton jogcímek eredetileg adta ki, és a felhasználó tartózkodási lett érvényesítve.

Ha mindkét lépés sikertelen, a felhasználó már nem kell a megbízható IP-cím minősül.



## <a name="location-condition-configuration"></a>A feltétel a tartalomhely konfigurációját

Amikor konfigurálja a hely feltétel, lehetősége van között:

- Bármely hely 
- Minden megbízható hely
- Kijelölt helyek

![Helyek](./media/location-condition/01.png)

### <a name="any-location"></a>Bármely hely

Alapértelmezés szerint kiválasztja **bármely helyre** alkalmazható összes IP-címet egy szabályzatot, amely azt jelenti, hogy az interneten bármely cím okoz. Ez a beállítás nem korlátozott elnevezett helyként konfigurált IP-címeket. Ha bejelöli **bármely helyre**, adott helyeken továbbra is kizárhat a szabályzat alól. Például egy házirendet alkalmazhatja a hatókör beállítása mindenhol, kivéve a vállalati hálózathoz való megbízható helyek kivételével az összes hely.

### <a name="all-trusted-locations"></a>Minden megbízható hely

Ez a beállítás a következőkre vonatkozik:

- Megbízható helyként megjelölt összes hely
- **MFA megbízható IP-CÍMEK** (Ha be van állítva)


### <a name="selected-locations"></a>Kijelölt helyek

Ezzel a beállítással kiválaszthat egy vagy több nevesített helyek. Egy felhasználó egy házirendet a alkalmazni ezt a beállítást, a kijelölt helyek bármelyikét csatlakozni kell. Amikor rákattint **kiválasztása** megnyílik a megnevezett hálózati kiválasztása vezérlőelem, amely a nevesített hálózatok listáját jeleníti meg. A lista mutatja azokat is, ha a hálózati hely van megjelölve megbízhatóként. A nevesített hely nevű **MFA megbízható IP-címek** szolgál, hogy tartalmazzák az IP-beállításait a multi-factor authentication szolgáltatás beállítások oldalon konfigurálható.

## <a name="what-you-should-know"></a>Alapismeretek

### <a name="when-is-a-location-evaluated"></a>Amikor egy hely abban az esetben?

Feltételes hozzáférési házirendek kiértékelése során: 

- Egy felhasználó először jelentkezik be egy web app-, mobil- és asztali alkalmazást. 

- Egy mobil- vagy asztali alkalmazás, amely a modern hitelesítést használ, a frissítési jogkivonatok segítségével egy új hozzáférési jogkivonat beszerzése. Alapértelmezés szerint ez a egyszer egy óra. 

Ez azt jelenti, hogy a mobile és a modern hitelesítést használó asztali alkalmazásokhoz hely módosítása lenne észlelhető a hálózati hely módosítása egy órán belül. A nem modern hitelesítést használó asztali és mobil alkalmazások a szabályzat érvényes token kéréseknek. A kérelem gyakorisága az alkalmazás függően változhat. Ehhez hasonlóan az webalkalmazások esetén a szabályzat első bejelentkezéskor alkalmazza, és a munkamenet élettartamára rögzülnek jó a webalkalmazás. A munkamenet élettartama alkalmazások közötti különbségek, mert a szabályzat-kiértékelés közötti idő is változnak. Minden alkalommal, amikor az alkalmazás új bejelentkezési tokent kér, a szabályzat érvényes.


Alapértelmezés szerint az Azure AD kibocsát egy token óránként. Helyezze át a vállalati hálózatról, egy órán belül a házirend érvényesítve van a modern hitelesítést használó alkalmazások számára.


### <a name="user-ip-address"></a>Felhasználói IP-cím

Az IP-címet, amely a szabályzat-kiértékelés szolgál a felhasználó a nyilvános IP-címe. Egy magánhálózaton lévő eszközök esetében ez nem az ügyfél IP-címét a felhasználó eszközén az intraneten, hogy a cím a nyilvános internethez való kapcsolódáshoz a hálózat által használt. 

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Tömeges feltöltés és a nevesített helyek letöltése

Hozzon létre vagy frissítésekor nevesített helyek tömeges frissítéshez feltölthet vagy IP-címtartományokkal CSV-fájl letöltése. Feltöltéskor a lista IP-címtartományokkal lecseréli azokat a fájlból. A fájl minden sorában egy IP-címtartomány CIDR formátumban tartalmazza. 


### <a name="cloud-proxies-and-vpns"></a>Felhőbeli proxyk és a VPN-EK 

Egy felhőben üzemeltetett proxy- vagy VPN-megoldást használ, az IP-cím az Azure AD használja pedig egy házirend kiértékelése a proxy IP-címét. A nyilvános IP-címet nem használja, mert nem történik ellenőrzés, amely egy megbízható forrásból származik a felhasználókat tartalmazó X-Forwarded-For (XFF) fejléc így jelentene IP-cím faking módját. 

Ha egy felhőbeli proxy van-e, olyan szabályzatot, amely használható egy tartományba léptetett eszköz megkövetelése szolgál, vagy a belső vállalati hálózaton kívüli jogcímek az AD FS.



### <a name="api-support-and-powershell"></a>API-támogatás és a PowerShell használatával 

API és PowerShell még nem támogatott nevesített helyek vagy feltételes hozzáférési szabályzatokat.





## <a name="next-steps"></a>További lépések

- Ha azt szeretné tudni, hogyan lehet feltételes hozzáférési szabályzat konfigurálása, lásd: [többtényezős hitelesítés megkövetelése az Azure Active Directory feltételes hozzáférés az adott alkalmazások](app-based-mfa.md).

- Ha kész feltételes hozzáférési szabályzatokat konfigurálni a környezetében, tekintse át [az Azure Active Directory feltételes hozzáféréssel kapcsolatos ajánlott eljárásait](best-practices.md). 
