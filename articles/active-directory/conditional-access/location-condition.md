---
title: A hely feltétele Azure Active Directory feltételes hozzáférésben
description: Megtudhatja, hogyan használhatja a hely feltételét a felhőalapú alkalmazásokhoz való hozzáférés vezérlésére a felhasználó hálózati helye alapján.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 05/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 781d8b89dd1b7fa6b2ed9707f6d4c485b4abdf20
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2020
ms.locfileid: "84220612"
---
# <a name="using-the-location-condition-in-a-conditional-access-policy"></a>A hely feltételének használata feltételes hozzáférési házirendben 

Ahogy az [áttekintő cikkben](overview.md) is látható, a feltételes hozzáférési szabályzatok az alapszintű egy if-then utasításban a jelek kombinálásával, a döntések elvégzéséhez és a szervezeti szabályzatok betartatásához szükségesek. A döntéshozatali folyamatba beépíthető jelek egyike a hálózati hely.

![Koncepcionális feltételes jel plusz döntés a kényszerítés beszerzéséhez](./media/location-condition/conditional-access-signal-decision-enforcement.png)

A szervezetek a következő általános feladatokhoz használhatják ezt a hálózati helyet: 

- Többtényezős hitelesítés megkövetelése a szolgáltatáshoz hozzáférő felhasználók számára a vállalati hálózaton kívülről.
- Hozzáférés letiltása adott országokból vagy régiókból származó szolgáltatáshoz hozzáférő felhasználók számára.

A hálózati helyet az ügyfél által a Azure Active Directory számára biztosított nyilvános IP-cím határozza meg. A feltételes hozzáférési szabályzatok alapértelmezés szerint az összes IPv4-és IPv6-címre érvényesek. 

> [!TIP]
> Az IPV6-tartományok csak a **[nevesített hely (előzetes verzió)](#preview-features)** felületén támogatottak. 

## <a name="named-locations"></a>Nevesített helyek

A helyszínek a Azure Portal **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférés**  >  **névvel ellátott helyei**területen vannak kijelölve. Ezek a nevesített hálózati helyek olyan helyekhez tartozhatnak, mint például a szervezeti központ hálózati tartományai, a VPN-hálózatok tartományai vagy a blokkolni kívánt tartományok. 

![Elnevezett helyszínek a Azure Portal](./media/location-condition/new-named-location.png)

A hely konfigurálásához meg kell adnia legalább egy **nevet** és az IP-címtartományt. 

A konfigurálható elnevezett helyszínek számát a kapcsolódó objektum mérete korlátozza az Azure AD-ben. A következő korlátozások alapján állíthatja be a helyszíneket:

- Egy elnevezett hely legfeljebb 1200 IPv4-tartománnyal.
- Legfeljebb 90 elnevezett helyet, amelyek mindegyike egy IP-tartománnyal van társítva.

> [!TIP]
> Az IPV6-tartományok csak a **[nevesített hely (előzetes verzió)](#preview-features)** felületén támogatottak. 

### <a name="trusted-locations"></a>Megbízható helyek

Hálózati hely létrehozásakor a rendszergazdának lehetősége van a hely megbízható helyként való megjelölésére. 

![Megbízható helyek a Azure Portal](./media/location-condition/new-trusted-location.png)

Ez a beállítás a feltételes hozzáférési házirendekben fordulhat elő, ahol például a megbízható hálózati helyről való regisztráció megkövetelése a többtényezős hitelesítéshez. Emellett a Azure AD Identity Protection kockázati számítását is figyelembe veszi, ami csökkenti a felhasználók bejelentkezési kockázatát, ha a megbízhatóként megjelölt helyről érkezik.

### <a name="countries-and-regions"></a>Országok és régiók

Egyes szervezetek dönthetnek úgy, hogy a feltételes hozzáférési szabályzatok elnevezett helyei szerint határozzák meg az összes országot vagy régióbeli IP-határokat. Ezeket a helyeket akkor használhatják, amikor blokkolják a szükségtelen forgalmat, ha tudják, hogy az érvényes felhasználók soha nem olyan helyről származnak, mint az Észak-Korea. Az IP-cím és az ország közötti leképezések rendszeresen frissülnek. 

> [!NOTE]
> Az országok nem tartalmaznak IPv6-címtartományt, csak az ismert IPv4-címtartományt.

![Új ország vagy régió alapú hely létrehozása a Azure Portal](./media/location-condition/new-named-location-country-region.png)

#### <a name="include-unknown-areas"></a>Ismeretlen területek belefoglalása

Néhány IP-cím nincs leképezve egy adott országra vagy régióra. Ezeknek az IP-helyeknek a rögzítéséhez jelölje be az **ismeretlen területek** megadása a hely meghatározásakor jelölőnégyzetet. Ezzel a beállítással megadhatja, hogy ezek az IP-címek szerepeljenek-e a megnevezett helyen. Akkor használja ezt a beállítást, ha a nevesített helyet használó házirendet ismeretlen helyekre kell alkalmazni.

### <a name="configure-mfa-trusted-ips"></a>MFA megbízható IP-címeinek konfigurálása

A [multi-Factor Authentication szolgáltatás beállításaiban](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx)konfigurálhatja a szervezet helyi intranetét JELKÉPEZŐ IP-címtartományt is. Ez a funkció lehetővé teszi, hogy legfeljebb 50 IP-címtartományt konfiguráljon. Az IP-címtartományok CIDR formátumúak. További információ: [megbízható IP](../authentication/howto-mfa-mfasettings.md#trusted-ips)-címek.  

Ha megbízható IP-címek vannak konfigurálva, a hely feltételének helyein **MFA megbízható IP** -címekként jelennek meg.

### <a name="skipping-multi-factor-authentication"></a>Multi-Factor Authentication kihagyása

A multi-Factor Authentication szolgáltatás beállításai lapon azonosíthatja a vállalati intranetes felhasználókat a **többtényezős hitelesítés kihagyása az összevont felhasználóktól az intraneten lévő kérelmekhez**lehetőség kiválasztásával. Ez a beállítás azt jelzi, hogy a AD FS által kiadott vállalati hálózati jogcímet megbízhatónak kell tekinteni, és a felhasználó azonosítására kell használni a vállalati hálózaton. További információ: [a megbízható IP-címek engedélyezése funkció feltételes hozzáférés használatával](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

A beállítás ellenőrzése után a megnevezett hely **MFA megbízható IP** -címei minden olyan házirendre érvényesek lesznek, amelyik ezt a lehetőséget választja.

A hosszú élettartamú munkamenetek élettartamát tartalmazó mobil-és asztali alkalmazások esetében a feltételes hozzáférés időszakosan újraértékelve lesz. Az alapértelmezett érték óránként egyszer. Ha a vállalati hálózati jogcímek csak a kezdeti hitelesítéskor jelennek meg, előfordulhat, hogy az Azure AD nem rendelkezik a megbízható IP-címtartományok listájával. Ebben az esetben nehezebb megállapítani, hogy a felhasználó továbbra is a vállalati hálózaton van-e:

1. Ellenőrizze, hogy a felhasználó IP-címe a megbízható IP-címtartományok egyikén van-e.
1. Győződjön meg arról, hogy a felhasználó IP-címének első három oktettje megegyezik-e a kezdeti hitelesítés IP-címének első három oktettével. A rendszer összehasonlítja az IP-címet a kezdeti hitelesítéssel, ha a belső vállalati hálózati jogcím eredetileg ki lett állítva, és a felhasználói hely ellenőrzése megtörtént.

Ha mindkét lépés meghiúsul, a rendszer a felhasználót már nem megbízható IP-címekre tekinti.

## <a name="preview-features"></a>Előzetes verziójú funkciók

Az általánosan elérhető elnevezett Location funkció mellett egy megnevezett hely is található (előzetes verzió). A megnevezett hely villámnézetét az aktuális elnevezett hely panel tetején található szalagcím használatával érheti el.

![Az elnevezett helyszínek előzetes verziójának kipróbálása](./media/location-condition/preview-features.png)

A megnevezett hely előzetes verziójával lehetősége van

- Akár 195 elnevezett helyszín konfigurálása
- Akár 2000 IP-tartomány konfigurálása egy nevesített helyen
- IPv6-címek konfigurálása

További ellenőrzéseket is felvettünk, amelyek segítenek csökkenteni a helytelen konfiguráció változását.

- A magánhálózati IP-címtartományok már nem konfigurálhatók.
- A tartományba felvehető IP-címek száma korlátozott. IP-címtartomány konfigurálásakor csak a/8-nál nagyobb CIDR-maszkok engedélyezettek.

Az előzetes verzióban mostantól két létrehozási lehetőség közül választhat: 

- **Országok helye**
- **IP-címtartományok helye**

> [!NOTE]
> Az országok nem tartalmaznak IPv6-címtartományt, csak az ismert IPv4-címtartományt.

![Elnevezett helyszínek előzetes kezelőfelülete](./media/location-condition/named-location-preview.png)

## <a name="location-condition-in-policy"></a>Hely feltétele a házirendben

A hely feltételének konfigurálásakor lehetősége van a következők megkülönböztetésére:

- Bármely hely
- Minden megbízható helyszín
- Kiválasztott helyszínek

### <a name="any-location"></a>Bármely hely

Alapértelmezés szerint a **bármely hely** kiválasztásakor a rendszer minden IP-címre alkalmazza a házirendet, ami bármilyen címet jelent az interneten. Ez a beállítás nem korlátozódik az elnevezett helyként konfigurált IP-címekre. Ha **bármelyik helyet**kijelöli, akkor továbbra is kizárhat bizonyos helyeket a szabályzatból. Például alkalmazhat egy házirendet az összes helyszínre, kivéve a megbízható helyek beállítást a hatókör minden helyszínre való beállításához, a vállalati hálózat kivételével.

### <a name="all-trusted-locations"></a>Minden megbízható helyszín

Ez a beállítás a következőkre vonatkozik:

- Minden olyan hely, amely megbízható helyként van megjelölve
- **MFA megbízható IP** -címei (ha be van állítva)

### <a name="selected-locations"></a>Kiválasztott helyszínek

Ezzel a beállítással egy vagy több elnevezett helyet választhat ki. Ahhoz, hogy egy házirend alkalmazza ezt a beállítást, a felhasználónak csatlakoznia kell a kiválasztott helyekről. Ha a megnevezett hálózati kijelölési vezérlő **kijelölése** elemre kattint, megjelenik a megnevezett hálózatok listája. A lista azt is megjeleníti, hogy a hálózati hely megbízhatóként van-e megjelölve. Az **MFA megbízható IP** -címek nevű hely a multi-Factor Authentication szolgáltatás beállítási oldalán konfigurálható IP-beállításokat tartalmazza.

## <a name="what-you-should-know"></a>Alapismeretek

### <a name="when-is-a-location-evaluated"></a>Mikor kerül kiértékelésre a hely?

A feltételes hozzáférési szabályzatokat a rendszer a következők szerint értékeli ki:

- A felhasználó először bejelentkezik egy webalkalmazásba, mobilba vagy asztali alkalmazásba.
- A modern hitelesítést használó mobil-vagy asztali alkalmazások frissítési tokent használnak új hozzáférési jogkivonat beszerzéséhez. Alapértelmezés szerint ez az ellenőrzés óránként egyszer történik.

Ez az ellenőrzési módszer a modern hitelesítést használó mobil-és asztali alkalmazások esetén a hely változását a hálózati hely módosítása után egy órán belül észleli. A modern hitelesítést nem használó mobil-és asztali alkalmazások esetén a szabályzatot minden jogkivonat-kérelemre alkalmazza a rendszer. A kérés gyakorisága az alkalmazástól függően változhat. Hasonlóképpen, a webalkalmazások esetében a szabályzatot a rendszer az első bejelentkezéskor alkalmazza, és jó választás a webalkalmazásban található munkamenet élettartamára. Az alkalmazások munkamenet-élettartama közötti különbségek miatt a szabályzat kiértékelése közötti idő is változhat. Minden alkalommal, amikor az alkalmazás új bejelentkezési jogkivonatot kér, a rendszer alkalmazza a házirendet.

Alapértelmezés szerint az Azure AD egy tokent bocsát ki óránként. A vállalati hálózat kikapcsolását követően egy órán belül kikényszeríti a szabályzatot a modern hitelesítést használó alkalmazásokhoz.

### <a name="user-ip-address"></a>Felhasználói IP-cím

A házirend kiértékeléséhez használt IP-cím a felhasználó nyilvános IP-címe. A magánhálózaton lévő eszközök esetében ez az IP-cím nem az intraneten lévő felhasználó eszközének ügyfél-IP-címe, hanem a hálózat által a nyilvános internethez való kapcsolódáshoz használt cím.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Elnevezett helyszínek tömeges feltöltése és letöltése

Ha elnevezett helyeket hoz létre vagy frissít, a tömeges frissítésekhez feltölthet vagy letölthet egy CSV-fájlt az IP-címtartományok használatával. A feltöltés felváltja a listában szereplő IP-tartományokat a fájlból származó tartományokkal. A fájl minden sora egy IP-címtartományt tartalmaz CIDR formátumban.

### <a name="cloud-proxies-and-vpns"></a>Felhőalapú proxyk és VPN-EK

Ha felhőben üzemeltetett proxy-vagy VPN-megoldást használ, az Azure AD IP-címe a szabályzat kiértékelése során a proxy IP-címét használja. A felhasználó nyilvános IP-címét tartalmazó X-továbbított (XFF) fejléc nem használatos, mert nincs olyan érvényesítés, amely megbízható forrásból származik, ezért az IP-címek szimulálása egy metódust jelent.

Ha egy felhőalapú proxy van érvényben, a tartományhoz csatlakoztatott eszköz megköveteléséhez, illetve a AD FSon belüli Corpnet-jogcímek használatára vonatkozó szabályzatot lehet használni.

### <a name="api-support-and-powershell"></a>API-támogatás és PowerShell

Az API és a PowerShell még nem támogatott a nevesített helyein.

## <a name="next-steps"></a>További lépések

- Ha tudni szeretné, hogyan kell konfigurálni a feltételes hozzáférési szabályzatot, tekintse meg a [feltételes hozzáférési szabályzat létrehozásával](concept-conditional-access-policies.md)foglalkozó cikket.
- A hely feltételét használó példát keres? Tekintse meg a következő cikket [: feltételes hozzáférés letiltása hely szerint](howto-conditional-access-policy-location.md)
