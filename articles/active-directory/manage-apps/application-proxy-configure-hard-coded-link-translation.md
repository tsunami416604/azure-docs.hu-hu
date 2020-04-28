---
title: Hivatkozások és URL-címek lefordítása Azure AD alkalmazás proxyn | Microsoft Docs
description: Az Azure AD Application Proxy-összekötők alapjaira terjed ki.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa0dc2081aff5a24fb830b756131cccd5c6ce810
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "69533703"
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Hardcoded-hivatkozások átirányítása az Azure AD Application Proxy-ban közzétett alkalmazásokra

Az Azure AD Application Proxy elérhetővé teszi a helyszíni alkalmazásokat a távoli vagy a saját eszközökön lévő felhasználók számára. Egyes alkalmazások azonban a HTML-ben beágyazott helyi hivatkozásokkal lettek kialakítva. Ezek a hivatkozások nem működnek megfelelően, ha az alkalmazást távolról használják. Ha több helyszíni alkalmazás is mutat egymásra, a felhasználók elvárják, hogy a hivatkozások továbbra is működőképesek maradjanak, ha nem az irodában vannak. 

A legjobb módszer annak biztosítására, hogy a kapcsolatok ugyanúgy működjenek, mint a vállalati hálózaton belül és kívül is, hogy az alkalmazások külső URL-címei megegyezzenek a belső URL-címekkel. [Egyéni tartományokkal](application-proxy-configure-custom-domain.md) konfigurálhatja a külső URL-címeket úgy, hogy az alapértelmezett alkalmazásproxy-tartomány helyett a vállalati tartománynevet használják.


Ha nem használhat egyéni tartományokat a bérlőben, több további lehetőség is rendelkezésre áll a funkció biztosításához. Ezek mindegyike az egyéni tartományokkal és egymással is kompatibilis, így szükség esetén egyéni tartományokat és egyéb megoldásokat is beállíthat.

> [!NOTE]
> A hivatkozás fordítása nem támogatott a JavaScript használatával generált, rögzített belső URL-címek esetében.

**1. lehetőség: a Managed Browser vagy a Microsoft Edge használata** – ez a megoldás csak akkor alkalmazható, ha azt tervezi, hogy a felhasználók a Intune Managed Browser vagy a Microsoft Edge böngésző használatával érik el az alkalmazást. A szolgáltatás minden közzétett URL-címet kezelni fog. 

**2. lehetőség: a MyApps bővítmény használata** – ehhez a megoldáshoz a felhasználóknak ügyféloldali böngésző-bővítményt kell telepíteniük, de az összes közzétett URL-címet kezelni fogják, és a legnépszerűbb böngészőkkel működnek. 

**3. lehetőség: a hivatkozás fordítási beállításának használata** – ez egy rendszergazdai oldal, amely láthatatlan a felhasználók számára. Azonban csak HTML-és CSS-URL-címeket fog kezelni.   

Ez a három funkció tárolja a hivatkozásokat, függetlenül attól, hogy a felhasználók hol vannak. Ha olyan alkalmazásokkal rendelkezik, amelyek közvetlenül a belső végpontokra vagy portokra mutatnak, ezeket a belső URL-címeket a közzétett külső alkalmazásproxy URL-címeire képezheti le. 

 
> [!NOTE]
> Az utolsó lehetőség csak olyan bérlők esetében használható, amelyek bármilyen okból nem használhatják az egyéni tartományokat az alkalmazásokhoz tartozó belső és külső URL-címekkel. A szolgáltatás engedélyezése előtt tekintse meg, hogy az [Azure ad Application proxy-beli egyéni tartományok](application-proxy-configure-custom-domain.md) működhetnek-e. 
> 
> Vagy ha a hivatkozás fordításával konfigurálni kívánt alkalmazás SharePoint rendszerű, tekintse meg a következő témakört: [alternatív hozzáférés-leképezések konfigurálása a sharepoint 2013](https://technet.microsoft.com/library/cc263208.aspx) -hoz a hivatkozások leképezésének másik megközelítéséhez. 

 
### <a name="option-1-intune-managed-browser-and-microsoft-edge-integration"></a>1. lehetőség: a Intune Managed Browser és a Microsoft Edge integrációja 

Az alkalmazás és a tartalom további védelemmel való ellátásához a Intune Managed Browser vagy a Microsoft Edge használható. Ennek a megoldásnak a használatához meg kell követelnie/javasolnia kell, hogy a felhasználók hozzáférhessenek az alkalmazáshoz a Intune Managed Browseron keresztül. A Managed Browser felismeri az alkalmazásproxy által közzétett összes belső URL-címet, és átirányítja a megfelelő külső URL-címre. Ez biztosítja, hogy a rögzített belső URL-címek működnek, és ha a felhasználó a böngészőbe lép, és közvetlenül a belső URL-címet írja be, akkor is működik, ha a felhasználó távoli.  

Ha többet szeretne megtudni, beleértve a beállítás konfigurálásának módját, tekintse meg a [Managed Browser](https://docs.microsoft.com/intune/app-configuration-managed-browser) dokumentációját.  

### <a name="option-2-myapps-browser-extension"></a>2. lehetőség: MyApps böngésző bővítmény 

A MyApps böngésző bővítménnyel a bővítmény felismeri az alkalmazásproxy által közzétett összes belső URL-címet, és átirányítja a megfelelő külső URL-címre. Ez biztosítja, hogy a rögzített belső URL-címek működnek, és ha a felhasználó a böngésző címsorába kerül, és közvetlenül a belső URL-címet írja be, akkor is működik, ha a felhasználó távoli.  

A szolgáltatás használatához a felhasználónak le kell töltenie a bővítményt, és be kell jelentkeznie. A rendszergazdák vagy a felhasználók számára nincs szükség további konfigurálásra. 

Ha többet szeretne megtudni, beleértve a beállítás konfigurálásának módját, tekintse meg a [MyApps böngésző bővítmény](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access#download-and-install-the-my-apps-secure-sign-in-extension) dokumentációját.

### <a name="option-3-link-translation-setting"></a>3. lehetőség: fordítási beállítás csatolása 

Ha a hivatkozás fordítása engedélyezve van, az Application proxy szolgáltatás HTML-és CSS-keresést végez a közzétett belső hivatkozásokon, és lefordítja őket, hogy a felhasználók zavartalan élményt kapjanak. A MyApps böngésző bővítmény használata ajánlott a link Translation beállításhoz, mivel a felhasználók számára nagyobb teljesítményű felhasználói élményt nyújt.

> [!NOTE]
> Ha a 2. vagy a 3. lehetőséget használja, egyszerre csak az egyiket kell engedélyezni.

## <a name="how-link-translation-works"></a>A link Translation működése

A hitelesítés után, amikor a proxykiszolgáló átadja az alkalmazásadatok a felhasználónak, az alkalmazásproxy megkeresi az alkalmazást az hardcoded-hivatkozásokra, és lecseréli azokat a megfelelő, közzétett külső URL-címekre.

Az alkalmazásproxy feltételezi, hogy az alkalmazások UTF-8-ban vannak kódolva. Ha ez nem így van, adja meg a kódolás típusát egy http-válasz fejlécében, `Content-Type:text/html;charset=utf-8`például:.

### <a name="which-links-are-affected"></a>Mely hivatkozások érintik?

A hivatkozás fordítási funkciója csak az alkalmazás törzsében lévő kódelemekben található hivatkozásokat keresi. Az alkalmazásproxy külön funkcióval rendelkezik a cookie-k és URL-címek lefordításához a fejlécekben. 

A helyszíni alkalmazásokban két általános típusú belső kapcsolat létezik:

- **Relatív belső hivatkozások** , amelyek egy megosztott erőforrásra mutatnak egy helyi fájlmegosztás, `/claims/claims.html`például:. Ezek a hivatkozások automatikusan működnek az Application proxyn keresztül közzétett alkalmazásokban, és a kapcsolat fordításával vagy anélkül is dolgozhatnak. 
- A **hardcoded belső hivatkozásokat** más helyszíni alkalmazásokra, `http://expenses` például a (z `http://expenses/logo.jpg`) vagy a közzétett fájlokra. A hivatkozás fordítási funkciója hardcoded belső hivatkozásokon működik, és úgy módosítja őket, hogy azok a külső URL-címekre mutassanak, amelyeket a távoli felhasználóknak el kell végezniük.

Az Application proxy által támogatott HTML-kódelemek teljes listája a következőkhöz:
* a
* hang
* base
* gombra
* div
* beágyaz
* űrlap
* keret
* fej
* html
* iframe
* kép
* bemenet
* hivatkozás
* MENUITEM
* meta
* objektum
* szkriptet.
* source
* követés
* Videó

Emellett a CSS-en belül az URL-attribútum is le van fordítva.

### <a name="how-do-apps-link-to-each-other"></a>Hogyan hivatkoznak az alkalmazások egymásra?

A link Translation minden alkalmazás esetében engedélyezve van, így a felhasználói élmény az alkalmazáson belüli szinten szabályozható. Kapcsolja be a hivatkozás fordítását egy alkalmazáshoz, ha az *alkalmazás hivatkozásait* le szeretné fordítani, és nem *hivatkozik erre az* alkalmazásra. 

Tegyük fel például, hogy három, az Application proxyn keresztül közzétett alkalmazással rendelkezik, amelyek mindegyike kapcsolódik egymáshoz: előnyök, költségek és utazás. Van egy negyedik alkalmazás, visszajelzés, amely nincs közzétéve az alkalmazásproxy használatával.

Ha engedélyezi a kapcsolat fordítását az előnyök alkalmazáshoz, a költségekre és az utazásra mutató hivatkozások átirányítva lesznek az alkalmazások külső URL-címeire, de a visszajelzésre mutató hivatkozás nem lesz átirányítva, mert nincs külső URL-cím. A költségekre mutató hivatkozások és az előnyök visszautazása nem működik, mert a hivatkozás fordítása nincs engedélyezve a két alkalmazás esetében.

![Az előnyöktől más alkalmazásokra mutató hivatkozások a kapcsolat fordításának engedélyezésekor](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Mely hivatkozások nem fordíthatók le?

A teljesítmény és a biztonság javítása érdekében egyes hivatkozások nem fordíthatók le:

- A kódelemeken kívüli hivatkozások. 
- HTML-vagy CSS-hivatkozások nem. 
- URL-kódolású formátumú hivatkozások
- Más programokból megnyitott belső hivatkozások. Az e-mailben vagy csevegésen keresztül küldött hivatkozások nem lesznek lefordítva. A felhasználóknak ismerniük kell a külső URL-címet.

Ha a két forgatókönyv egyikét kell támogatnia, használja a kapcsolat fordítása helyett ugyanazt a belső és külső URL-címet.  

## <a name="enable-link-translation"></a>Hivatkozás fordításának engedélyezése

A hivatkozás fordításának első lépései olyan egyszerűek, mint a gombra kattintva:

1. Jelentkezzen be rendszergazdaként a [Azure Portalba](https://portal.azure.com) .
2. Lépjen **Azure Active Directory** > **vállalati alkalmazások** > **minden alkalmazás** lehetőségre > válassza ki azt az alkalmazást, amelyet > **alkalmazásproxy**kezelésére szeretne használni.
3. Az **alkalmazás törzsében lévő URL-címek fordítása** **Igen**.

   ![Válassza az Igen lehetőséget az URL-címek lefordításához az alkalmazás törzsében](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. A módosítások alkalmazásához kattintson a **Mentés** gombra.

Most, amikor a felhasználók hozzáférnek az alkalmazáshoz, a proxy automatikusan ellenőrzi azokat a belső URL-címeket, amelyek a bérlőn keresztül lettek közzétéve az Application proxyn keresztül.

## <a name="send-feedback"></a>Visszajelzés küldése

Szeretnénk, ha segítségre van szüksége a funkció működéséhez az összes alkalmazás számára. Több mint 30 címkét keresünk HTML-ben és CSS-ben. Ha például olyan generált hivatkozásokat tartalmaz, amelyek nincsenek lefordítva, küldjön egy kódrészletet az [alkalmazásproxy visszajelzéséhez](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>További lépések
[Egyéni tartományok használata az Azure ad Application proxy](application-proxy-configure-custom-domain.md) ugyanazzal a belső és külső URL-címmel

[Alternatív hozzáférés-leképezések konfigurálása a SharePoint 2013-hoz](https://technet.microsoft.com/library/cc263208.aspx)
