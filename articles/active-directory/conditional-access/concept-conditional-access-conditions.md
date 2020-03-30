---
title: Feltételek a feltételes hozzáférési szabályzatban – Azure Active Directory
description: Milyen feltételek egy Azure AD feltételes hozzáférési szabályzatban?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb14369275a111476867f2263766e1bb87b7c87d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295336"
---
# <a name="conditional-access-conditions"></a>Feltételes hozzáférés: Feltételek

A feltételes hozzáférési szabályzaton belül a rendszergazda használhatja a feltételekből származó jeleket, például a kockázatot, az eszközplatformot vagy a helyet a házirend-döntések javítása érdekében. 

![Feltételes hozzáférési házirend definiálása és feltételek megadása](./media/concept-conditional-access-conditions/conditional-access-conditions.png)

Több feltétel kombinálható a részletes és konkrét feltételes hozzáférési házirendek létrehozásához.

Például egy bizalmas alkalmazás elérésekor a rendszergazda is tényező bejelentkezési kockázati adatokat identity protection és helyét a hozzáférési döntés mellett más vezérlők, például a többtényezős hitelesítés.

## <a name="sign-in-risk"></a>Bejelentkezési kockázat

[Az identitásvédelemhez](../identity-protection/overview-identity-protection.md)hozzáféréssel rendelkező ügyfelek számára a bejelentkezési kockázat feltételes hozzáférési szabályzat részeként értékelhető ki. A bejelentkezési kockázat annak a valószínűségét jelzi, hogy egy adott hitelesítési kérelmet az identitás tulajdonosa nem engedélyez. A bejelentkezési kockázatról további információt a [Cikkek, Mi a kockázat](../identity-protection/concept-identity-protection-risks.md#sign-in-risk) és [hogyan: A kockázati házirendek konfigurálása és engedélyezése.](../identity-protection/howto-identity-protection-configure-risk-policies.md)

## <a name="device-platforms"></a>Eszközplatformok

Az eszközplatformot az eszközön futó operációs rendszer jellemzi. Az Azure AD azonosítja a platformot az eszköz által megadott információk, például a felhasználói ügynök karakterláncok használatával. Mivel a felhasználói ügynök karakterláncai módosíthatók, ez az információ nem ellenőrzött. Az eszközplatformot a Microsoft Intune-eszközmegfelelőségi szabályzataival összhangban vagy egy blokknyilatkozat részeként kell használni. Az alapértelmezett beállítás az összes eszközplatformra vonatkozik.

Az Azure AD feltételes hozzáférés a következő eszközplatformokat támogatja:

- Android
- iOS
- Windows Phone
- Windows
- macOS

Ha letiltja az örökölt hitelesítést az **Egyéb ügyfelek** feltétellel, beállíthatja az eszköz platformfeltételét is.

## <a name="locations"></a>Helyek

A hely feltételként való konfigurálásakor a szervezetek dönthetnek úgy, hogy helyeket foglalnak bele vagy zárnak ki. Ezek a megnevezett helyek tartalmazhatnak nyilvános IPv4-hálózati információkat, országot vagy régiót, vagy akár ismeretlen területeket is, amelyek nem bizonyos országokhoz vagy régiókhoz vannak leképezve. Csak az IP-tartományok jelölhetők meg megbízható helyként.

Ha bármilyen helyet is **megad,** ez a beállítás nem csak az elnevezett helyek konfigurálása az interneten található IP-címet tartalmazza. Bármely **hely**kiválasztásakor a rendszergazdák kizárhatják **az összes megbízható** vagy kijelölt **helyet**.

Előfordulhat például, hogy egyes szervezetek úgy döntenek, hogy nem igényelnek többtényezős hitelesítést, ha a felhasználók megbízható helyen, például fizikai központjukban csatlakoznak a hálózathoz. A rendszergazdák létrehozhatnak egy házirendet, amely bármilyen helyet tartalmaz, de kizárja a központi hálózatok kijelölt helyeit.

További információ a helyekről a cikkben található: [Mi a helyfeltétel az Azure Active Directory feltételes hozzáférésében.](location-condition.md)

## <a name="client-apps-preview"></a>Ügyfélalkalmazások (előzetes verzió)

A feltételes hozzáférési házirendek alapértelmezés szerint a böngészőalapú alkalmazásokra és a modern hitelesítési protokollokat használó alkalmazásokra vonatkoznak. Ezen alkalmazások mellett a rendszergazdák választhatnak, hogy az Exchange ActiveSync-ügyfeleket és más, örökölt protokollokat használó ügyfeleket is bevonják.Addition to these applications, administrators can choose to include Exchange ActiveSync clients and other clients that do't utilize legacy protocols.

- Böngésző
   - Ezek közé tartoznak a webalapú alkalmazások, amelyek olyan protokollokat használnak, mint az SAML, a WS-Federation, az OpenID Connect vagy az OAuth bizalmas ügyfélként regisztrált szolgáltatások.
- Mobilalkalmazások és asztali ügyfelek
   - Modern hitelesítési ügyfelek
      - Ez a beállítás olyan alkalmazásokat is tartalmaz, mint az asztali Office- és a telefonalkalmazások.
   - Exchange ActiveSync-ügyfelek
      - Alapértelmezés szerint ez magában foglalja az Exchange ActiveSync (EAS) protokoll összes használatát. A **Csak a támogatott platformokra vonatkozó házirend alkalmazása** lehetőség az olyan támogatott platformokra korlátozódik, mint az iOS, az Android és a Windows.
      - Ha a házirend letiltja az Exchange ActiveSync használatát, az érintett felhasználó egyetlen karanténe-mailt kap. Ez az e-mail tájékoztatást nyújt arról, hogy miért vannak blokkolva, és ha képes, javítási utasításokat is tartalmaz.
   - Más ügyfelek
      - Ez a beállítás azokat az ügyfeleket foglalja magában, amelyek olyan alapszintű/örökölt hitelesítési protokollokat használnak, amelyek nem támogatják a modern hitelesítést.
         - Hitelesített SMTP – A POP és az IMAP-ügyfél által használt e-mail üzenetek küldésére.
         - Automatikus észlelés – Az Outlook és az EAS-ügyfelek segítségével kereshet postaládákat az Exchange Online-ban, és csatlakozhat azokhoz.
         - Exchange Online PowerShell – Az Exchange Online-hoz távoli PowerShell használatával való csatlakozáshoz használható. Ha letiltja az Exchange Online PowerShell alapszintű hitelesítését, a csatlakozáshoz az Exchange Online PowerShell-modult kell használnia. További információt a [Csatlakozás az Exchange Online PowerShellhez többtényezős hitelesítéssel című](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)témakörben talál.
         - Exchange Web Services (EWS) – Az Outlook, a Mac Outlook és a külső alkalmazások által használt programozási felület.
         - IMAP4 - Az IMAP levelezőügyfelei használják.
         - MAPI HTTP-n keresztül (MAPI/HTTP) – Az Outlook 2010 és újabb verziói használják.
         - Kapcsolat nélküli címjegyzék (OAB) – Az Outlook által letöltött és használt címlista-gyűjtemények másolata.
         - Outlook Anywhere (HTTP-n keresztüli RPC) – Az Outlook 2016-ban és korábban használt.
         - Outlook-szolgáltatás – A Windows 10 Posta és Naptár alkalmazása használja.
         - POP3 - A POP e-mail ügyfelek használják.
         - Jelentéskészítő webszolgáltatások – jelentésadatok beolvasására szolgál az Exchange Online-ban.

Ezeket a feltételeket gyakran használják felügyelt eszköz megkövetelése, az örökölt hitelesítés blokkolása és a webalkalmazások blokkolása, de a mobil- vagy asztali alkalmazások engedélyezése esetén.

### <a name="supported-browsers"></a>Támogatott böngészők

Ez a beállítás minden böngészővel működik. Az eszközházirendek, például a megfelelő eszközkövetelmények teljesítése érdekében azonban a következő operációs rendszerek és böngészők támogatottak:

| Operációs rendszer | Böngészők |
| :-- | :-- |
| Windows 10 | Microsoft Edge, Internet Explorer, Chrome |
| Windows 8 / 8.1 | Internet Explorer, Chrome |
| Windows 7 | Internet Explorer, Chrome |
| iOS | Microsoft Edge, Intune felügyelt böngésző, Safari |
| Android | Microsoft Edge, Intune felügyelt böngésző, Chrome |
| Windows Phone | Microsoft Edge, Internet Explorer |
| Windows Server 2019 | Microsoft Edge, Internet Explorer, Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Króm, Szafari |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Miért jelenik meg a tanúsítványkérdés a böngészőben?

Windows 7, iOS, Android és macOS Azure AD azonosítja az eszközt egy ügyfél-tanúsítvány, amely ki van építve, ha az eszköz regisztrálva van az Azure AD.  Amikor egy felhasználó először jelentkezik be a böngészőn keresztül, a rendszer kéri, hogy válassza ki a tanúsítványt. A felhasználónak a böngésző használata előtt ki kell választania ezt a tanúsítványt.

#### <a name="chrome-support"></a>A Chrome támogatása

A Windows **10 Alkotók frissítéséhez (1703-as** vagy újabb verzió) telepített Chrome-támogatásért telepítse a [Windows 10-fiókok bővítményt.](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) Erre a bővítményre akkor van szükség, ha egy feltételes hozzáférési szabályzat eszközspecifikus részleteket igényel.

Ha ezt a bővítményt automatikusan telepíteni szeretné a Chrome böngészőkben, hozza létre a következő beállításkulcsot:

|    |    |
| --- | --- |
| Útvonal | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Név | 1 |
| Típus | REG_SZ (karakterlánc) |
| Adatok | ppnbnpeolgkicgegkbkbjmhlideopiji;https\://clients2.google.com/service/update2/crx |

A Windows **8.1 és 7**rendszerben a Chrome-támogatásérdekében hozza létre a következő beállításkulcsot:

|    |    |
| --- | --- |
| Útvonal | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Név | 1 |
| Típus | REG_SZ (karakterlánc) |
| Adatok | {"pattern":"""filter":{"ISSUER":{"CN":"MS-Organization-Access"}}}https://device.login.microsoftonline.com |

Ezek a böngészők támogatják az eszközhitelesítést, lehetővé téve az eszköz azonosítását és érvényesítését egy házirend alapján. Az eszközellenőrzés sikertelen, ha a böngésző privát módban fut.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Támogatott mobilalkalmazások és asztali ügyfelek

A szervezetek kiválaszthatják **a mobilalkalmazásokat és az asztali ügyfeleket** ügyfélalkalmazásként.

Ez a beállítás hatással van a következő mobilalkalmazásokból és asztali ügyfelekből származó hozzáférési kísérletekre:

| Ügyfélalkalmazások | Célszolgáltatás | Platform |
| --- | --- | --- |
| Dynamics CRM alkalmazás | Dynamics CRM | Windows 10, Windows 8.1, iOS és Android |
| Levelezés/naptár/kapcsolatok alkalmazás, Outlook 2016, Outlook 2013 (modern hitelesítéssel)| Office 365 Exchange Online | Windows 10 |
| Az alkalmazások többfa- és helyházirendje. Az eszközalapú házirendek nem támogatottak.| Bármely Saját alkalmazások alkalmazásszolgáltatás | Android és iOS |
| Microsoft Teams Services – ez vezérli a Microsoft Teamst támogató összes szolgáltatást és annak összes ügyfélalkalmazását – Windows Asztali, iOS, Android, WP és webes ügyfélalkalmazásokat | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android és macOS |
| Office 2016-alkalmazások, Office 2013 (modern hitelesítéssel), [OneDrive szinkronizálási ügyfél](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| Office 2016-alkalmazások, Univerzális Office-alkalmazások, Office 2013 (modern hitelesítéssel), [OneDrive szinkronizálási ügyfélprogram](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (csak Word, Excel, PowerPoint, OneNote esetén). | Office 365 SharePoint Online | macOS |
| Office 2019| Office 365 SharePoint Online | Windows 10, macOS |
| Office-mobilappok | Office 365 SharePoint Online | Android, iOS |
| Office Yammer alkalmazás | Office 365 Yammer | Windows 10, iOS, Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016 (macOS Office) | Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (modern hitelesítéssel), Skype Vállalati verzió (modern hitelesítéssel) | Office 365 Exchange Online | Windows 8.1, Windows 7 |
| Outlook-mobilalkalmazás | Office 365 Exchange Online | Android, iOS |
| Power BI alkalmazás | Power BI szolgáltatás | Windows 10, Windows 8.1, Windows 7, Android és iOS |
| Skype Vállalati verzió | Office 365 Exchange Online| Android, iOS |
| Visual Studio Team Services alkalmazás | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS és Android |

### <a name="exchange-activesync-clients"></a>Exchange ActiveSync-ügyfelek

- A szervezetek csak akkor választhatják ki az Exchange ActiveSync-ügyfeleket, ha házirendet rendelnek felhasználókhoz vagy csoportokhoz. A **Minden felhasználó**, a Minden **vendég- és külső felhasználó**vagy a **Címtárszerepkörök** jelölőnégyzet bejelölése miatt az összes felhasználó letiltásra kerül.
- Az Exchange ActiveSync-ügyfelekhez rendelt házirend létrehozásakor az **Office 365 Exchange Online** legyen az egyetlen felhőalapú alkalmazás, amely a házirendhez van rendelve. 
- A szervezetek az **Eszközplatformok** feltétel használatával leszűkíthetik a szabályzat hatókörét adott platformokra.

Ha a házirendhez rendelt hozzáférés-vezérlés a **Jóváhagyott ügyfélalkalmazás megkövetelése**eszközt használja, a felhasználó az Outlook mobilügyfél telepítésére és használatára utasítja. Abban az esetben, ha **többtényezős hitelesítésre** van szükség, az érintett felhasználók blokkolva vannak, mert az alapfokú hitelesítés nem támogatja a többtényezős hitelesítést.

További információkért tekintse át a következő cikkeket:

- [Örökölt hitelesítés blokkolása feltételes hozzáféréssel](block-legacy-authentication.md)
- [Jóváhagyott ügyfélalkalmazások megkövetelése feltételes hozzáféréssel](app-based-conditional-access.md)

### <a name="other-clients"></a>Más ügyfelek

Az **Egyéb ügyfelek**lehetőséget választva olyan feltételt adhat meg, amely hatással van az okra az egyszerű hitelesítést használó alkalmazásokra olyan levelezőprotokollokkal, mint az IMAP, MAPI, POP, SMTP és régebbi Office-alkalmazások, amelyek nem használnak modern hitelesítést.

## <a name="device-state-preview"></a>Eszköz állapota (előzetes verzió)

Az eszköz állapotfeltétele a hibrid Azure AD-hez csatlakozott és/vagy a Microsoft Intune megfelelőségi szabályzatnak megfelelőként megjelölt eszközök kizárására használható a szervezet feltételes hozzáférési szabályzataiból.

Például *minden felhasználó,* aki hozzáfér a *Microsoft Azure Management* felhőalkalmazáshoz, beleértve az összes **eszközállapotot,** kivéve **az Eszközhibrid Azure AD-t,** és **az Eszköz megfelelőként van megjelölve,** és az *Access-vezérlők*számára **, Blokk**. 
   - Ebben a példában egy szabályzatot hoz létre, amely csak a hibrid Azure AD-hez csatlakozott és/vagy megfelelőként megjelölt eszközökről engedélyezi a Microsoft Azure Management elérését.

## <a name="next-steps"></a>További lépések

- [Feltételes hozzáférés: Támogatás](concept-conditional-access-grant.md)

- [Feltételes hozzáférés közös házirendjei](concept-conditional-access-policy-common.md)
