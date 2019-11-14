---
title: Mi a hely feltétele a feltételes hozzáférés Azure Active Directory? | Microsoft Docs
description: Megtudhatja, hogyan használhatja a hely feltételét a felhőalapú alkalmazásokhoz való hozzáférés vezérlésére a felhasználó hálózati helye alapján.
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
ms.openlocfilehash: 799a994e1351f62fac9f5a07060658cea60c9274
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74065731"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Mi a hely feltétele a feltételes hozzáférés Azure Active Directory? 

A [Azure Active Directory (Azure ad) feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md)segítségével szabályozhatja, hogy a jogosult felhasználók hogyan férhessenek hozzá a felhőalapú alkalmazásokhoz. A feltételes hozzáférési szabályzat helyének feltétele lehetővé teszi a hozzáférés-vezérlési beállítások összekapcsolását a felhasználók hálózati helyein.

Ez a cikk a hely feltételének konfigurálásához szükséges információkat tartalmazza.

## <a name="locations"></a>Helyek

Az Azure AD lehetővé teszi az egyszeri bejelentkezést az eszközökre, alkalmazásokra és szolgáltatásokra bárhonnan a nyilvános interneten. A hely feltételével a felhasználó hálózati helye alapján vezérelheti a felhőalapú alkalmazásokhoz való hozzáférést. A hely feltételének gyakori használati esetei a következők:

- Többtényezős hitelesítés megkövetelése a szolgáltatáshoz hozzáférő felhasználók számára a vállalati hálózaton kívülről.
- Hozzáférés letiltása adott országokból vagy régiókból származó szolgáltatáshoz hozzáférő felhasználók számára.

A hely egy olyan hálózati hely címkéje, amely vagy egy elnevezett hely vagy a multi-Factor Authentication megbízható IP-címei.

## <a name="named-locations"></a>Elnevezett helyszínek

Az elnevezett helyszínekkel logikai csoportosításokat hozhat létre az IP-címtartományok vagy országok és régiók számára.

A elnevezett helyekhez a feltételes hozzáférés lap **kezelés** szakaszában férhet hozzá.

![Nevesített helyszínek a feltételes hozzáférésben](./media/location-condition/02.png)

Egy elnevezett hely a következő összetevőket tartalmazhatja:

![Új elnevezett hely létrehozása](./media/location-condition/42.png)

- **Name (név** ) – egy elnevezett hely megjelenítendő neve.
- **IP-címtartományok** – egy vagy több IPv4-címtartomány CIDR formátumban. IPv6-címtartomány meghatározása nem támogatott.

   > [!NOTE]
   > Az IPv6-címtartományok jelenleg nem szerepelhetnek egy elnevezett helyen. Ez azt jelenti, hogy az IPv6-tartományok nem zárhatók ki feltételes hozzáférési szabályzatból.

- **Megjelölés megbízható helyként** – egy megnevezett helyhez beállítható jelző, amely megbízható helyet jelez. A megbízható helyek általában az IT-részleg által vezérelt hálózati területek. A feltételes hozzáférés mellett az Azure Identity Protection és az Azure AD biztonsági jelentései is felhasználják a megbízható névvel ellátott helyek használatát a [téves pozitív](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1)adatok csökkentése érdekében.
- **Országok/régiók** – ez a beállítás lehetővé teszi egy vagy több ország vagy régió kiválasztását egy elnevezett hely definiálásához.
- **Ismeretlen területek belefoglalása** – néhány IP-cím nincs leképezve egy adott országra vagy régióra. Ezzel a beállítással megadhatja, hogy ezek az IP-címek szerepeljenek-e a megnevezett helyen. Akkor használja ezt a beállítást, ha a nevesített helyet használó házirendet ismeretlen helyekre kell alkalmazni.

A konfigurálható elnevezett helyszínek számát a kapcsolódó objektum mérete korlátozza az Azure AD-ben. A következő korlátozások alapján állíthatja be a helyszíneket:

- Egy elnevezett hely, amely legfeljebb 1200 IP-tartománnyal rendelkezik.
- Legfeljebb 90 elnevezett helyet, amelyek mindegyike egy IP-tartománnyal van társítva.

A feltételes hozzáférési szabályzat az IPv4-és IPv6-forgalomra vonatkozik. A jelenleg elnevezett helyszínek nem teszik lehetővé IPv6-tartományok konfigurálását. Ez a korlátozás a következő helyzeteket okozza:

- A feltételes hozzáférési szabályzat nem célozhat meghatározott IPv6-tartományokat.
- A feltételes hozzáférési szabályzat nem tud bizonyos IPV6-tartományokat kizárni

Ha egy házirend úgy van konfigurálva, hogy a "bármely helyre" vonatkozzon, akkor az IPv4-és IPv6-forgalomra is érvényes lesz. A megadott országokban és régiókban konfigurált elnevezett helyszínek csak IPv4-címeket támogatnak. Az IPv6-forgalom csak abban az esetben szerepel, ha a "ismeretlen területek belefoglalása" lehetőséget választotta.

## <a name="trusted-ips"></a>Megbízható IP-címek

A [multi-Factor Authentication szolgáltatás beállításaiban](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx)konfigurálhatja a szervezet helyi intranetét JELKÉPEZŐ IP-címtartományt is. Ez a funkció lehetővé teszi, hogy legfeljebb 50 IP-címtartományt konfiguráljon. Az IP-címtartományok CIDR formátumúak. További információ: [megbízható IP](../authentication/howto-mfa-mfasettings.md#trusted-ips)-címek.  

Ha megbízható IP-címek vannak konfigurálva, a hely feltételének helyein **MFA megbízható IP** -címekként jelennek meg.

### <a name="skipping-multi-factor-authentication"></a>Multi-Factor Authentication kihagyása

A multi-Factor Authentication szolgáltatás beállításai lapon azonosíthatja a vállalati intranetes felhasználókat a **többtényezős hitelesítés kihagyása az összevont felhasználóktól az intraneten lévő kérelmekhez**lehetőség kiválasztásával. Ez a beállítás azt jelzi, hogy a AD FS által kiadott vállalati hálózati jogcímet megbízhatónak kell tekinteni, és a felhasználó azonosítására kell használni a vállalati hálózaton. További információ: [a megbízható IP-címek engedélyezése funkció feltételes hozzáférés használatával](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

A beállítás ellenőrzése után a megnevezett hely **MFA megbízható IP** -címei minden olyan házirendre érvényesek lesznek, amelyik ezt a lehetőséget választja.

A hosszú élettartamú munkamenetek élettartamát tartalmazó mobil-és asztali alkalmazások esetében a feltételes hozzáférés időszakosan újraértékelve lesz. Az alapértelmezett érték óránként egyszer. Ha a vállalati hálózati jogcímek csak a kezdeti hitelesítéskor jelennek meg, előfordulhat, hogy az Azure AD nem rendelkezik a megbízható IP-címtartományok listájával. Ebben az esetben nehezebb megállapítani, hogy a felhasználó továbbra is a vállalati hálózaton van-e:

1. Ellenőrizze, hogy a felhasználó IP-címe a megbízható IP-címtartományok egyikén van-e.
2. Győződjön meg arról, hogy a felhasználó IP-címének első három oktettje megegyezik-e a kezdeti hitelesítés IP-címének első három oktettével. A rendszer összehasonlítja az IP-címet a kezdeti hitelesítéssel, ha a belső vállalati hálózati jogcím eredetileg ki lett állítva, és a felhasználói hely ellenőrzése megtörtént.

Ha mindkét lépés meghiúsul, a rendszer a felhasználót már nem megbízható IP-címekre tekinti.

## <a name="location-condition-configuration"></a>Hely állapotának konfigurálása

A hely feltételének konfigurálásakor lehetősége van a következők megkülönböztetésére:

- Bármely hely
- Minden megbízható helyszín
- Kiválasztott helyszínek

![Hely állapotának konfigurálása](./media/location-condition/01.png)

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

> [!WARNING]
> Ha az eszköz csak IPv6-címekkel rendelkezik, a hely feltételének konfigurálása nem támogatott.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Elnevezett helyszínek tömeges feltöltése és letöltése

Ha elnevezett helyeket hoz létre vagy frissít, a tömeges frissítésekhez feltölthet vagy letölthet egy CSV-fájlt az IP-címtartományok használatával. A feltöltés felváltja a listában szereplő IP-tartományokat a fájlból származó értékekkel. A fájl minden sora egy IP-címtartományt tartalmaz CIDR formátumban.

### <a name="cloud-proxies-and-vpns"></a>Felhőalapú proxyk és VPN-EK

Ha felhőben üzemeltetett proxy-vagy VPN-megoldást használ, az Azure AD IP-címe a szabályzat kiértékelése során a proxy IP-címét használja. A felhasználó nyilvános IP-címét tartalmazó X-továbbított (XFF) fejléc nem használatos, mert nincs olyan érvényesítés, amely megbízható forrásból származik, ezért az IP-címek szimulálása egy metódust jelent.

Ha egy felhőalapú proxy van érvényben, a tartományhoz csatlakoztatott eszköz megköveteléséhez, illetve a AD FSon belüli Corpnet-jogcímek használatára vonatkozó szabályzatot lehet használni.

### <a name="api-support-and-powershell"></a>API-támogatás és PowerShell

Az API és a PowerShell még nem támogatott a nevesített helyszíneken, illetve a feltételes hozzáférési házirendekben.

## <a name="next-steps"></a>További lépések

- Ha tudni szeretné, hogyan konfigurálhat egy feltételes hozzáférési szabályzatot, tekintse meg a többtényezős hitelesítés [megkövetelése adott alkalmazásokhoz Azure Active Directory feltételes hozzáféréssel](app-based-mfa.md)című témakört.
- Ha készen áll a környezet feltételes hozzáférési házirendjeinek konfigurálására, tekintse meg az [ajánlott eljárásokat a feltételes hozzáféréshez Azure Active Directory](best-practices.md).
