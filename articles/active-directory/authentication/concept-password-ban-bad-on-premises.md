---
title: Azure AD jelszavas védelem – Azure Active Directory
description: Gyenge jelszavak betiltása a helyszíni Active Directory az Azure AD jelszavas védelme használatával
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b9d07099f8de996181948921330ef6744b302a8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74848646"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Jelszavas Azure AD-védelem kikényszerítése Windows Server Active Directoryhoz

Az Azure AD jelszavas védelme egy olyan szolgáltatás, amely egy szervezet jelszavas házirendjeit javítja ki. A jelszavas védelem helyszíni telepítése az Azure AD-ben tárolt globális és egyéni tiltott jelszavak listáját is használja. Ugyanazokat a helyszíni ellenőrzéseket végzi el, mint az Azure AD a felhőalapú változtatásokhoz. Ezeket az ellenőrzéseket a rendszer a jelszó módosítása és a jelszó-visszaállítási forgatókönyvek során hajtja végre.

## <a name="design-principles"></a>Tervezési alapelvek

Az Azure AD jelszavas védelmét az alábbi alapelvek alapján tervezték meg:

* A tartományvezérlőknek soha nem kell közvetlenül kommunikálnia az internettel.
* Nincsenek új hálózati portok megnyitva a tartományvezérlőkön.
* Nincs szükség Active Directory séma módosítására. A szoftver a meglévő Active Directory **tároló** -és **serviceConnectionPoint** séma-objektumokat használja.
* Nincs szükség minimális Active Directory tartományra vagy erdő működési szintjére (DFL/FFL).
* A szoftver nem hoz létre vagy igényel fiókokat az általa védett Active Directory-tartományokban.
* Felhasználó tiszta – a szöveges jelszavak soha nem hagyják el a tartományvezérlőt a jelszó-érvényesítési műveletek során vagy bármilyen más időpontban.
* A szoftver nem függ más Azure AD-funkcióktól; például az Azure AD jelszó-kivonat szinkronizálása nem kapcsolódik, és nem szükséges ahhoz, hogy az Azure AD jelszavas védelem működni lehessen.
* A növekményes üzembe helyezés támogatott, azonban a jelszóházirend csak abban az esetben kényszerítve van, ha a tartományvezérlő ügynöke telepítve van. További részletekért tekintse meg a következő témakört.

## <a name="incremental-deployment"></a>Növekményes üzembe helyezés

Az Azure AD jelszavas védelme támogatja a tartományvezérlők közötti növekményes üzembe helyezést egy Active Directory tartományban, de fontos tisztában lennie azzal, hogy mit jelent ez valójában, és mi a kompromisszumok.

Az Azure AD jelszavas védelem DC-ügynök szoftvere csak akkor tudja érvényesíteni a jelszavakat, ha a tartományvezérlőre van telepítve, és csak az adott tartományvezérlőnek eljuttatott jelszó-módosításokra. A felhasználói jelszó módosításának feldolgozásához nem lehet szabályozni, hogy mely tartományvezérlőket választja ki a Windows-ügyfélszámítógépek. A konzisztens működés és az univerzális jelszavas védelem biztonságának biztosítása érdekében a tartományvezérlői ügynök szoftverét a tartomány összes tartományvezérlőjén telepíteni kell.

Számos szervezet szeretne körültekintően tesztelni az Azure AD jelszavas védelmét a tartományvezérlők egy részhalmazán a teljes üzembe helyezés előtt. Az Azure AD jelszavas védelme támogatja a részleges üzembe helyezést, azaz az adott TARTOMÁNYVEZÉRLŐn a DC Agent szoftver aktívan érvényesíti a jelszavakat, még akkor is, ha a tartomány más tartományvezérlői nem rendelkeznek a DC Agent szoftverrel. Az ilyen típusú részleges központi telepítések nem biztonságosak, és nem ajánlott tesztelési célokra.

## <a name="architectural-diagram"></a>Építészeti diagram

Az Azure AD jelszavas védelem helyszíni Active Directory környezetben való üzembe helyezése előtt fontos megérteni a mögöttes tervezési és működési fogalmakat. Az alábbi ábra bemutatja, hogyan működik együtt a jelszavas védelem összetevői:

![Hogyan működnek együtt az Azure AD jelszavas védelmi összetevői?](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Az Azure AD jelszavas védelem proxy szolgáltatása az aktuális Active Directory erdőben található bármely tartományhoz csatlakoztatott gépen fut. Elsődleges célja, hogy továbbítsa a jelszóházirend letöltési kérelmeit a tartományvezérlők és az Azure AD között. Ezután visszaadja az Azure AD válaszait a tartományvezérlőnek.
* A DC-ügynök jelszavas szűrő DLL-je fogadja a felhasználói jelszó-érvényesítési kérelmeket az operációs rendszertől. Továbbítja őket a tartományvezérlőn helyileg futó DC Agent szolgáltatásnak.
* A jelszavas védelem DC Agent szolgáltatása a tartományvezérlő ügynökének jelszavas szűrő DLL-fájljában fogadja a jelszó-ellenőrzési kérelmeket. Ezeket az aktuális (helyileg elérhető) jelszóházirend használatával dolgozza fel, és visszaadja az eredményt: *pass* vagy *Fail*.

## <a name="how-password-protection-works"></a>A jelszavas védelem működése

Minden egyes Azure AD-beli jelszavas védelmi proxy szolgáltatási példány az erdőben lévő tartományvezérlőkre hirdeti magát egy **serviceConnectionPoint** objektum létrehozásával Active Directoryban.

A jelszavas védelemhez használt összes tartományvezérlő ügynök-szolgáltatás egy **serviceConnectionPoint** objektumot is létrehoz Active Directoryban. Ez az objektum elsősorban jelentéskészítésre és diagnosztika használatára szolgál.

A DC Agent szolgáltatás feladata, hogy kezdeményezzen egy új jelszóházirend letöltését az Azure AD-ből. Az első lépés egy Azure AD-beli jelszavas védelmi proxy szolgáltatás megkeresése az erdő **serviceConnectionPoint** -objektumok számára történő lekérdezésével. Ha talál rendelkezésre álló proxy szolgáltatást, a DC ügynök egy jelszóházirend letöltési kérelmét küldi el a proxy szolgáltatásnak. A proxy szolgáltatás pedig elküldi a kérést az Azure AD-nek. A proxy szolgáltatás ezután visszaadja a DC Agent szolgáltatásnak küldött választ.

Miután a DC Agent szolgáltatás új jelszót kapott az Azure AD-től, a szolgáltatás a tartomány *SYSVOL* mappa-megosztásának gyökerében lévő dedikált mappában tárolja a szabályzatot. A DC Agent szolgáltatás a mappában is figyeli ezt a mappát abban az esetben, ha újabb házirendek replikálódnak a tartomány más DC-ügynök szolgáltatásaiból.

A DC Agent szolgáltatás mindig új házirendet kér a szolgáltatás indításakor. A DC Agent szolgáltatás elindítása után óránként ellenőrzi a jelenlegi helyileg elérhető házirend korát. Ha a házirend egy óránál régebbi, a tartományvezérlő ügynöke új szabályzatot kér az Azure AD-től a proxy szolgáltatáson keresztül a korábban leírtak szerint. Ha az aktuális házirend nem régebbi, mint egy óra, a tartományvezérlő ügynök továbbra is ezt a házirendet használja.

Amikor az Azure AD jelszavas védelemre vonatkozó jelszóházirend le van töltve, a szabályzat egy bérlőre vonatkozik. Más szóval a jelszó-szabályzatok mindig a Microsoft globális betiltott jelszavas listájának és a bérlői egyéni tiltott jelszó-lista kombinációja.

A tartományvezérlő ügynök a proxy szolgáltatással kommunikál a TCP-n keresztüli RPC protokollon keresztül. A proxy szolgáltatás a konfigurációtól függően egy dinamikus vagy statikus RPC-porton figyeli ezeket a hívásokat.

A DC-ügynök soha nem figyeli a hálózatról elérhető portot.

A proxy szolgáltatás soha nem hívja meg a DC Agent szolgáltatást.

A proxy szolgáltatás állapota állapot nélküli. Soha nem gyorsítótárazza a házirendeket vagy az Azure-ból letöltött más állapotot.

A DC Agent szolgáltatás mindig a legutóbbi helyileg elérhető jelszóházirend használatával értékeli ki a felhasználó jelszavát. Ha a helyi TARTOMÁNYVEZÉRLŐn nem érhető el jelszóházirend, a rendszer automatikusan elfogadja a jelszót. Ha ez történik, a rendszer egy eseményt naplóz, hogy figyelmeztesse a rendszergazdát.

Az Azure AD jelszavas védelme nem valós idejű házirend-alkalmazás motorja. A jelszó-házirend konfigurációjának módosítása az Azure AD-ben, illetve a változás elérésekor és az összes tartományvezérlőn való betartatása között eltelt idő lehet.

Az Azure AD jelszavas védelme kiegészíti a meglévő Active Directory jelszóházirend-szabályzatokat, nem pedig helyettesíti azokat. Ebbe beletartozik minden más, a külső féltől származó jelszó-szűrési dll-fájl, amely telepíthető lehet. Active Directory mindig megköveteli, hogy az összes jelszó-ellenőrzési összetevő elfogadja a jelszó elfogadását.

## <a name="foresttenant-binding-for-password-protection"></a>A jelszavas védelemhez szükséges erdő/bérlői kötés

Az Azure AD jelszavas védelem Active Directory erdőben való üzembe helyezéséhez az adott erdőnek az Azure AD-vel való regisztrációja szükséges. Minden központilag telepített proxy szolgáltatásnak is regisztrálva kell lennie az Azure AD-ben. Ezek az erdő-és proxy-regisztrációk egy adott Azure AD-bérlőhöz vannak társítva, amely implicit módon azonosítható a regisztráció során használt hitelesítő adatokkal.

Az erdőben lévő Active Directory erdőt és az összes telepített proxy szolgáltatást ugyanahhoz a bérlőhöz kell regisztrálni. Nem támogatott, hogy Active Directory erdőben vagy bármely proxy szolgáltatásban legyen regisztrálva az adott erdőben különböző Azure AD-bérlők számára. Az ilyen helytelenül konfigurált központi telepítés tünetei közé tartozik a jelszóházirendek letöltésének képtelensége.

## <a name="download"></a>Letöltés

Az Azure AD jelszavas védelem két szükséges ügynök-telepítője a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=57071)érhető el.

## <a name="next-steps"></a>További lépések
[Azure AD jelszóvédelem üzembe helyezése](howto-password-ban-bad-on-premises-deploy.md)
