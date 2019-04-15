---
title: Mi a helyfeltétel az Azure Active Directory feltételes hozzáférés? | Microsoft Docs
description: Megtudhatja, hogyan férhet hozzá a felhőalapú alkalmazások, a felhasználó hálózati helye alapján a hely feltétel használatával.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 04/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6771cf093f62ef7823e57ced8223e4cc6c0dc57e
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59563427"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Mi a helyfeltétel az Azure Active Directory feltételes hozzáférés? 

A [Azure Active Directory (Azure AD) feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md), szabályozhatja, hogy jogosult felhasználók hozzáférhetnek a felhőbeli alkalmazásokat. A hely feltétel egy feltételes hozzáférési szabályzat segítségével elősegítsék a hálózati helyek, a felhasználók a hozzáférés vezérlők beállításai.

Ez a cikk a helyfeltétel konfigurálásához szükséges információkat biztosít.

## <a name="locations"></a>Helyek

Az Azure AD lehetővé teszi, hogy egyszeri bejelentkezést az eszközöket, alkalmazásokat és szolgáltatásokhoz bárhonnan a nyilvános interneten. A hely a feltétellel a felhőalkalmazásokhoz, a felhasználó hálózati helye alapján szabályozhatja a hozzáférést. A hely feltétel gyakori alkalmazási helyzetek a következők:

- Többtényezős hitelesítés megkövetelése a felhasználók a szolgáltatás elérésére, amelyek a vállalati hálózatról.
- Blokkolja a hozzáférést a felhasználók bizonyos országokban vagy régiókban a szolgáltatás elérésére.

Egy hely a címkét, egy hálózati helyre, vagy jelöli egy elnevezett helyet, vagy a multi-factor authentication megbízható IP-címek.

## <a name="named-locations"></a>Névvel ellátott helyek

Nevesített helyek, az IP-címtartományok vagy más országokból és régiókból logikai csoportosításán hozhat létre.

A nevesített helyek a érheti el a **kezelés** a feltételes hozzáférés szakaszának.

![A feltételes hozzáférés nevesített helyek](./media/location-condition/02.png)

Egy elnevezett helye a következő összetevőket tartalmazza:

![Hozzon létre egy új névvel ellátott](./media/location-condition/42.png)

- **Név** -névvel rendelkező hely megjelenített neve.
- **IP-címtartományok** – legalább egy IPv4-címtartományokat CIDR formátumban. Egy IPv6-címtartomány megadása nem támogatott.

   > [!NOTE]
   > Egy elnevezett locationThis measn IPv6-címtartományok nem zárható ki a feltételes hozzáférési szabályzat jelenleg nem IPv6 cím rangess szerepelnek.

- **Megjelölés megbízható helyként** -jelzőt beállíthat egy elnevezett helyet megbízható helyen jelzi. Megbízható helyek jellemzően az IT-részleg által vezérelt hálózati területek. Feltételes hozzáférés mellett megbízható nevesített helyek is használják az Azure Identity Protection és az Azure AD biztonsági jelentések csökkentése érdekében [vakriasztások](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1).
- **Országok/régiók** – Ez a beállítás lehetővé teszi egy vagy több országban vagy régióban, adjon meg egy nevesített.
- **Ismeretlen területek belefoglalása** -néhány IP-címek nincsenek leképezve egy adott országban. Ezzel a beállítással kiválaszthatja, ha az IP-címeket kell szerepelnie a nevű helyen. Használja ezt a beállítást, ha ismeretlen helyekről alkalmazni kell a házirendet, a nevesített hely használatával.

Az Azure ad-ben a kapcsolódó objektum mérete konfigurálható nevesített helyek száma korlátozza. Konfigurálhatja a helyek alapján a következő korlátozásokat:

- Az egyik nevű akár 1200-as IP-címtartományok helyét.
- Legfeljebb 90 nevesített helyek, a hozzájuk rendelt egy IP-címtartományt.

Feltételes hozzáférési szabályzat vonatkozik az IPv4 és IPv6-forgalmat. Jelenleg nevesített helyek nem teszik lehetővé az IPv6-tartományok konfigurálását. Ez a korlátozás hatására a következő helyzetekben:

- Feltételes hozzáférési szabályzat nem tudja megcélozni megadott IPv6-tartományok
- Feltételes hozzáférési szabályzat nem zárhatók ki egyedi IPV6-tartományok

A házirend "Bármely helyre" alkalmazására van konfigurálva, ez a beállítás az IPv4 és IPv6-forgalomra érvényes lesz. Nevesített helyek megadott országban és régióban konfigurálva csak IPv4-címeket támogatja. IPv6-forgalom csak az "Ismeretlen területek belefoglalása" lehetőség választásakor tartalmazza.

## <a name="trusted-ips"></a>Megbízható IP-címek

A szervezet helyi intranet jelölő IP-címtartományok is konfigurálhatja a [multi-factor authentication szolgáltatás beállításainak](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Ez a funkció lehetővé teszi legfeljebb 50 IP-címtartományok. Az IP-címtartományok a CIDR-formátumban vannak. További információkért lásd: [megbízható IP-címek](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

A megbízható IP-címek konfigurálása, ha azok megjelenjen **MFA megbízható IP-CÍMEK** az a hely feltétel helyek listáját.

### <a name="skipping-multi-factor-authentication"></a>A multi-factor authentication kihagyása

A beállítások lapon a multi-factor authentication szolgáltatás, azonosíthatók vállalati intranetes felhasználók kiválasztásával **multi-factor authentication kihagyása az összevont felhasználók intranetről**. Ez a beállítás azt jelzi, hogy a belső vállalati hálózati jogcímet, amely az AD FS által kiadott, megbízható és kell, hogy a vállalati hálózaton a felhasználó azonosítására szolgál. További információkért lásd: [a megbízható IP-címek funkció engedélyezése a feltételes hozzáférés használatával](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Ezt a beállítást az ellenőrzés után többek között a névvel rendelkező hely **MFA megbízható IP-CÍMEK** ezt a jelölőnégyzetet minden olyan szabályzatok vonatkoznak.

Az asztali és mobil alkalmazások, amelyek hosszú éltek meg a munkamenetek élettartamát, a feltételes hozzáférés rendszeresen újraértékeli. Az alapértelmezett érték egyszer egy óra. Ha a belső vállalati hálózathoz csak jogcímet az első hitelesítés alkalmával, előfordulhat, hogy az Azure AD nem rendelkezik a megbízható IP-címtartományok listáját. Ebben az esetben nehezebb annak megállapításához, hogy a felhasználó továbbra is a vállalati hálózaton:

1. Ellenőrizze, hogy a felhasználó IP-cím van-e a megbízható IP-címtartományok egyikében.
2. Ellenőrizze, hogy megfelel-e a felhasználó IP-cím első három oktettjének az első három oktettjének az első hitelesítés IP-címét. Az IP-címet a rendszer összehasonlítja a kezdeti hitelesítést, amikor a vállalati belső hálózati jogcím eredetileg adta ki, és a felhasználó tartózkodási lett érvényesítve.

Ha mindkét lépés sikertelen, a felhasználó már nem kell a megbízható IP-cím minősül.

## <a name="location-condition-configuration"></a>A feltétel a tartalomhely konfigurációját

Amikor konfigurálja a hely feltétel, lehetősége van között:

- Bármely hely
- Minden megbízható hely
- Kijelölt helyek

![A feltétel a tartalomhely konfigurációját](./media/location-condition/01.png)

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
- Egy mobil- vagy asztali alkalmazás, amely a modern hitelesítést használ, a frissítési jogkivonatok segítségével egy új hozzáférési jogkivonat beszerzése. Alapértelmezés szerint ez az ellenőrzés az egyszer egy óra.

Ez az ellenőrzés azt jelenti, hogy a mobile, és a modern hitelesítést használó asztali alkalmazásokhoz hely módosítása lenne észlelhető a hálózati hely módosítása egy órán belül. A nem modern hitelesítést használó asztali és mobil alkalmazások a szabályzat érvényes token kéréseknek. A kérelem gyakorisága az alkalmazás függően változhat. Ehhez hasonlóan az webalkalmazások esetén a szabályzat első bejelentkezéskor alkalmazza, és a munkamenet élettartamára rögzülnek jó a webalkalmazás. A munkamenet élettartama alkalmazások közötti különbségek, mert a szabályzat-kiértékelés közötti idő is változnak. Minden alkalommal, amikor az alkalmazás új bejelentkezési tokent kér, a szabályzat érvényes.

Alapértelmezés szerint az Azure AD kibocsát egy token óránként. Helyezze át a vállalati hálózatról, egy órán belül a házirend érvényesítve van a modern hitelesítést használó alkalmazások számára.

### <a name="user-ip-address"></a>Felhasználói IP-cím

Az IP-címet, amely a szabályzat-kiértékelés szolgál a felhasználó a nyilvános IP-címe. Egy magánhálózaton lévő eszközök esetében ez IP-cím nem az ügyfél IP-címét a felhasználó eszközén az intraneten, a cím a nyilvános internethez való kapcsolódáshoz a hálózat által használt.

> [!WARNING]
> Ha az eszköz egy csak IPv6-cím, a hely feltétel konfigurálása nem támogatott.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Tömeges feltöltés és a nevesített helyek letöltése

Hozzon létre vagy frissítésekor nevesített helyek tömeges frissítéshez feltölthet vagy IP-címtartományokkal CSV-fájl letöltése. Feltöltéskor a lista IP-címtartományokkal lecseréli azokat a fájlból. A fájl minden sorában egy IP-címtartomány CIDR formátumban tartalmazza.

### <a name="cloud-proxies-and-vpns"></a>Felhőbeli proxyk és a VPN-EK

Egy felhőben üzemeltetett proxy- vagy VPN-megoldást használ, az IP-cím az Azure AD használja pedig egy házirend kiértékelése a proxy IP-címét. A X-Forwarded-For (XFF) fejlécet, amely tartalmazza a nyilvános IP-cím a felhasználó nem használja, mivel nem történik ellenőrzés, így az IP-cím faking módszert bemutatja egy megbízható forrásból származik.

Ha egy felhőbeli proxy van-e, olyan szabályzatot, amely használható egy tartományba léptetett eszköz megkövetelése szolgál, vagy a belső vállalati hálózaton kívüli jogcímek az AD FS.

### <a name="api-support-and-powershell"></a>API-támogatás és a PowerShell használatával

API és PowerShell még nem támogatott nevesített helyek vagy feltételes hozzáférési szabályzatokat.

## <a name="next-steps"></a>További lépések

- Ha azt szeretné tudni, hogyan lehet feltételes hozzáférési szabályzat konfigurálása, lásd: [többtényezős hitelesítés megkövetelése az Azure Active Directory feltételes hozzáférés az adott alkalmazások](app-based-mfa.md).
- Ha kész feltételes hozzáférési szabályzatokat konfigurálni a környezetében, tekintse át [az Azure Active Directory feltételes hozzáféréssel kapcsolatos ajánlott eljárásait](best-practices.md).
