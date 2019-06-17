---
title: Alapkonfiguráció szabályzat letiltása örökölt hitelesítés (előzetes verzió) – az Azure Active Directory
description: Feltételes hozzáférési szabályzatot az örökölt hitelesítési protokollok letiltása
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: e55bf4a66dcd8c1c7cfbc881d6f93c3a12f5dc00
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112323"
---
# <a name="baseline-policy-block-legacy-authentication-preview"></a>Alapvető házirendet: Az örökölt hitelesítés letiltása (előzetes verzió)

A felhasználók egyszerűen hozzáférhetnek számára a felhőalapú alkalmazások, Azure Active Directory (Azure AD) széles hitelesítési protokollok, beleértve az örökölt hitelesítési támogatja. Az örökölt hitelesítési olyan hitelesítési kérelmet által végzett hivatkozik:

* Régebbi Office-ügyfelek, amelyek a modern hitelesítés nélküli (például az Office 2010-ügyfél)
* Örökölt levelezési protokollok, mint például a IMAP/SMTP/POP3 használó ügyfelek

Minden megalkuvás bejelentkezési kísérletek többsége még ma, régebbi hitelesítési származnak. Az örökölt hitelesítés nem támogatja a többtényezős hitelesítés (MFA). Ha már rendelkezik egy többtényezős hitelesítési szabályzat engedélyezve van a címtárban, kiderítsék hitelesítés örökölt protokoll használatával, és többtényezős hitelesítés kihagyása.

A legjobb módszer a rosszindulatú hitelesítési kérést hoz létre örökölt protokollok fiókja védelme érdekében, hogy minden egy helyen letiltja ezeket a kísérleteket. Hogy egyszerűbb legyen, régebbi protokollokra által végrehajtott összes bejelentkezési kérés, létrehozott egy alapvető házirendet, amelyet éppen ezt.

![Az örökölt hitelesítés letiltása a feltételes hozzáférés](./media/howto-baseline-protect-legacy-auth/baseline-policy-block-legacy-authentication.png)

**Az örökölt hitelesítés letiltása** van [alapvető házirendet](concept-baseline-protection.md) , amely blokkolja a régebbi protokollokkal végzett minden hitelesítési kérelemre. Sikeresen jelentkezzen be az összes felhasználó számára a modern hitelesítést kell használni. A referenciakonfiguráció házirendek együtt használja, régebbi protokollokra érkező összes kérés le lesz tiltva, és minden felhasználó kell többtényezős hitelesítés minden alkalommal, amikor szükséges. Ez a szabályzat nem tiltja le az Exchange ActiveSync.

## <a name="identify-legacy-authentication-use"></a>Azonosítsa az örökölt hitelesítés használata

Örökölt hitelesítési letilthatja a címtárban, meg kell hogy először tájékozódjon, ha a felhasználók rendelkeznek-e az örökölt hitelesítési és milyen hatással van a teljes címtár használó alkalmazások. Az Azure AD bejelentkezési naplók segítségével megismerheti, hogy használ-e el az örökölt hitelesítés.

1. Keresse meg a **az Azure portal** > **Azure Active Directory** > **bejelentkezések**.
1. Az ügyfélalkalmazás oszlop hozzáadását, ha nem látható kattintva **oszlopok** > **ügyfélalkalmazás**.
1. Szűrés **ügyfélalkalmazás** > **egyéb ügyfelek** kattintson **alkalmaz**.

Szűrés lesz show jelentkezik be, amely megpróbálja csak az örökölt hitelesítési protokollok által indított. Minden egyes bejelentkezési kísérlet kattintva megtudhatja, további részleteket. A **ügyfélalkalmazás** eleménél a **alapszintű adatainak** lapon használt örökölt hitelesítési protokollt jelzi.

Ezek a naplók fogja jelezni, mely felhasználók továbbra is a régi authentication attól függően, és hogy mely alkalmazások örökölt protokoll használata hitelesítési kérelmeket. Feltételes hozzáférési szabályzat megvalósítása felhasználók számára, nem jelennek meg ezek a naplók és megerősítik, hogy nem használja az örökölt hitelesítés, vagy engedélyezze a **alapvető házirendet: block örökölt hitelesítési** csak ezeknek a felhasználóknak.

## <a name="moving-away-from-legacy-authentication"></a>Erről az örökölt hitelesítési áthelyezése

Ha már rendelkezik egy jobb képet használó örökölt hitelesítés a címtárban, és mely alkalmazások használják, a következő lépés a modern hitelesítés nélküli felhasználók frissíti. Modern hitelesítést a módszer az Identitáskezelés, amely biztosít a biztonságosabb felhasználók hitelesítéséhez és engedélyezéséhez. Ha egy többtényezős hitelesítési szabályzat helyen található a címtárban, a modern hitelesítés biztosítja, hogy a multi-factor Authentication szükség esetén kéri a felhasználót. A biztonságosabb alternatívája lehet az örökölt hitelesítési protokollok.

Ez a szakasz egy részletes áttekintést nyújt a modern hitelesítés a környezet frissítése. Olvassa el az alábbi lépéseket egy régebbi hitelesítési házirendet a szervezetében blokkolja engedélyezése előtt.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>1\. lépés: A címtárban a modern hitelesítés engedélyezése

Az első lépés a modern hitelesítés engedélyezése, hogy így, hogy a címtár támogatja a modern hitelesítést. A címtárak vagy 2017. augusztus 1. után létrehozott alapértelmezés szerint engedélyezve van a modern hitelesítést. Ha a címtár dátum előtt jött létre, szüksége manuálisan az az alábbi lépéseket követve a címtár számára a modern hitelesítés engedélyezése:

1. Ellenőrizze, hogy ha a címtár már támogatja a modern hitelesítés nélküli futtatásával `Get-CsOAuthConfiguration` származó a [Skype for Business Online PowerShell-modul](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell).
1. Ha a parancs visszaadja egy üres `OAuthServers` tulajdonsága, a Modern hitelesítés le van tiltva. A beállítás engedélyezése a modern hitelesítést használó frissítse `Set-CsOAuthConfiguration`. Ha a `OAuthServers` tulajdonság olyan bejegyzést tartalmaz, akkor készen.

Ügyeljen arra, hogy ez a lépés befejezése előtt. Rendkívül fontos, hogy a könyvtár-konfigurációk változnak először mert azok szabályozzák az összes Office-ügyfelek által használt protokoll. Akkor is, ha az Office-ügyfelek, amelyek támogatják a modern hitelesítést használja, akkor alapértelmezés szerint örökölt protokollok használatával, ha a modern hitelesítés le van tiltva, a címtárban.

### <a name="step-2-office-applications"></a>2\. lépés: Office-alkalmazások

Miután engedélyezte a modern hitelesítést a címtárban, elkezdheti a modern hitelesítéssel az Office-ügyfelek engedélyezésével alkalmazások frissítése. Office 2016 vagy újabb ügyfelek alapértelmezés szerint támogatja a modern hitelesítést. Nincsenek további lépések szükségesek.

Ha használja az Office 2013 Windows-ügyfelek vagy a régebbi, javasoljuk, hogy az Office 2016 vagy újabb frissítése. A címtárban a modern hitelesítés engedélyezése, az előző lépés befejezése után a régebbi Office-alkalmazások továbbra is az örökölt hitelesítési protokollok használatára. Ha az ügyfelek Office 2013-at használ, és nem lehet azonnal frissítse az Office 2016 vagy újabb, kövesse az alábbi cikkben [Office 2013 Windows-eszközökön a Modern hitelesítés engedélyezése](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication). Az örökölt hitelesítés használata során a fiókja védelme érdekében azt javasoljuk az erős jelszavak a címtárban. Tekintse meg [az Azure AD jelszóvédelem](../authentication/concept-password-ban-bad.md) letiltani a gyenge jelszavakat között a címtárban.

Office 2010-et nem támogatja a modern hitelesítést. Szüksége lesz a felhasználók Office 2010-zel frissítsen egy újabb verziója az Office. Javasoljuk, hogy az Office 2016 vagy újabb, frissítése, alapértelmezés szerint az örökölt hitelesítési blokkol.

Ha MacOS használ, javasoljuk, Office for Mac 2016 vagy újabb verzióra. A natív levelezőprogramján használja, ha szüksége lesz a MacOS 10.14 verzió vagy újabb minden eszköz rendelkezik.

### <a name="step-3-exchange-and-sharepoint"></a>3\. lépés: Az Exchange és SharePoint

Windows-alapú Outlook-felhasználók számára a modern hitelesítést használnak, az Exchange online-hoz kell lennie a modern hitelesítés engedélyezése is. Ha a modern hitelesítés le van tiltva, az Exchange Online, Outlook Windows-alapú ügyfelek támogató modern authentication (Outlook 2013-as vagy újabb) alapszintű hitelesítés fogja használni az Exchange Online-postaládákhoz való kapcsolódáshoz.

A SharePoint Online engedélyezve van a modern hitelesítés alapértelmezett. 2017\. augusztus 1. után létrehozott címtárakat a modern hitelesítés alapértelmezés szerint engedélyezve van az Exchange online-ban. Azonban ha korábban rendelkezett letiltott modern hitelesítést, vagy ez a dátum előtt létrehozott egy könyvtárat használja, kövesse az alábbi cikkben leírtak [modern hitelesítés engedélyezése az Exchange Online-ban](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

### <a name="step-4-skype-for-business"></a>4\. lépés: Skype Vállalati verzió

Skype vállalati verzió által az örökölt hitelesítési kérelmek megelőzése érdekében elengedhetetlen a Skype vállalati online a modern hitelesítés engedélyezése. 2017\. augusztus 1. után létrehozott címtárakat modern hitelesítést a Skype vállalati verzió alapértelmezés szerint engedélyezve van.

Ahhoz, hogy a modern hitelesítést a Skype vállalati verzió, javasoljuk, hogy Microsoft Teams, amely támogatja a modern hitelesítést alapértelmezés szerint való áttérés. Azonban ha nem tudja tr jelenleg, meg fog engedélyeznie kell a modern hitelesítést a Skype vállalati Online úgy, hogy a Skype vállalati ügyfelek számára a modern hitelesítést használó. Kövesse az alábbi lépéseket a cikk [Skype for Business topológiákat támogatja a Modern hitelesítést használó](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported), a Modern hitelesítés engedélyezése a Skype for Business lépéseket.

Egyrészt a modern hitelesítést a Skype vállalati Online, javasoljuk, hogy a modern hitelesítési engedélyezhető az Exchange online-hoz, modern hitelesítést a Skype vállalati verzió engedélyezésekor. Ez a folyamat segít szinkronizálása a modern hitelesítés a Exchange Online és Skype vállalati online állapotát, és megakadályozza, hogy több bejelentkezési kérések a Skype vállalati ügyfelek számára.

### <a name="step-5-using-mobile-devices"></a>5\. lépés: Mobil eszközök használatával

A mobileszközön lévő alkalmazások kell, valamint az örökölt hitelesítés letiltása. Outlook Mobile használatát javasoljuk. Outlook Mobile alapértelmezés szerint támogatja a modern hitelesítést, és más MFA alapkonfiguráció alkalmazásvédelmi szabályzatok eleget tesz.

A natív IOS-es mail ügyfél használatához szüksége lesz IOS-es verzió fut 11.0 vagy újabb, annak érdekében, hogy az e-mail ügyfélprogramból frissítve lett, hogy az örökölt hitelesítés letiltása.

### <a name="step-6-on-premises-clients"></a>6\. lépés: A helyi ügyfelek

Ha a helyszíni Exchange Server és a Skype használata a helyszíni hibrid ügyfél, mindkét szolgáltatás kell frissíteni, hogy a modern hitelesítés engedélyezése. Modern hitelesítést használó hibrid környezetben, ha továbbra is az helyszíni felhasználók hitelesítéséhez használt. A történetet, az erőforrások (e-mailek vagy fájlok) módosítások való hozzáférésre.

Mielőtt elkezdhetné a engedélyezése helyszíni modern hitelesítést, mindenképpen theIf megfelel a követelményeknek, most már készen áll helyszíni modern hitelesítés engedélyezése.

Modern hitelesítés nélküli engedélyezésének lépései a következő cikkekben található:

* [Az Exchange Server konfigurálása a helyszíni hibrid, Modern hitelesítést használó](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Skype for Business Modern Authentication (ADAL) használata](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="enable-the-baseline-policy"></a>Az alapkonfiguráció-házirend engedélyezése

A szabályzat **alapvető házirendet: Az örökölt hitelesítés letiltása (előzetes verzió)** előre konfigurálva, és jelennek meg az oldal tetején a feltételes hozzáférés paneljén, az Azure Portalon lépve.

A szabályzat engedélyezéséhez és a szervezet védelme érdekében:

1. Jelentkezzen be a **az Azure portal** globális rendszergazdai, biztonsági rendszergazdai vagy feltételes hozzáférésű rendszergazda.
1. Keresse meg a **az Azure Active Directory** > **feltételes hozzáférési**.
1. A házirendek listájából válassza ki **alapvető házirendet: Az örökölt hitelesítés letiltása (előzetes verzió)** .
1. Állítsa be **házirend engedélyezése** való **a házirend azonnal használható**.
1. Bármely felhasználó kizárások hozzáadása kattintva **felhasználók** > **kizárt felhasználók kiválasztása** majd ki kell zárni a felhasználóknak. Kattintson a **kiválasztása** majd **kész**.
1. Kattintson a **mentése**.

## <a name="next-steps"></a>További lépések

További információkért lásd:

* [Feltételes hozzáférés alapkonfiguráció alkalmazásvédelmi szabályzatok](concept-baseline-protection.md)
* [Öt lépést a személyazonosság-infrastruktúra védelme](../../security/azure-ad-secure-steps.md)
* [Mi az az Azure Active Directory feltételes hozzáférés?](overview.md)
