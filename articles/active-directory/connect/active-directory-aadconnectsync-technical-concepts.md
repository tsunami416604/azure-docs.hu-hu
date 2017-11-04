---
title: "Azure AD Connect szinkronizálása: technikai fogalmak |} Microsoft Docs"
description: "Ismerteti az Azure AD Connect szinkronizálási szolgáltatás műszaki elveit."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 731cfeb3-beaf-4d02-aef4-b02a8f99fd11
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi;andkjell
ms.openlocfilehash: e178c5fa453c753aabdc38449273f46a90e1194a
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2017
---
# <a name="azure-ad-connect-sync-technical-concepts"></a>Az Azure AD Connect szinkronizálása: technikai kulcsfogalmak
Ez a cikk, a következő témakörben összegzését [ismertetése architektúra](active-directory-aadconnectsync-technical-concepts.md).

Az Azure AD Connect szinkronizálási szolgáltatás egy teli metakönyvtár szinkronizálási platformra épít.
Az alábbi szakaszok a metakönyvtár szinkronizálási fogalmak vezethet.
MIIS ILM és FIM épít, az Azure Active Directory szinkronizálási szolgáltatások csatlakozás az adatforrásokhoz, adatforrások, valamint a kiépítés közötti szinkronizálása és identitások megszüntetés a következő platformot biztosít.

![Műszaki fogalmak](./media/active-directory-aadconnectsync-technical-concepts/scenario.png)

Az alábbi szakaszokban további információt a FIM szinkronizálási szolgáltatás a következő szempontokat:

* összekötő
* Attribútumfolyam
* Összekötőtér
* Metaverzum
* Kiépítés

## <a name="connector"></a>összekötő
A csatlakoztatott címtárhoz folytatott kommunikációhoz használt kódmodulok nevezzük összekötők (korábbi nevén kezelőügynökök (MAs)).

Azon a számítógépen, amelyen az Azure AD Connect szinkronizálási szolgáltatás van telepítve. Az összekötők a távoli rendszer protokollok használatával speciális ügynökök telepítése ahelyett Serverhez ügynök nélküli lehetőséget nyújtanak. Ez azt jelenti, csökkentheti kockázat és üzembe helyezési idők, különösen akkor, ha kritikus fontosságú alkalmazások és rendszerek foglalkoznak.

A fenti kép az összekötő azonos a a kapcsolódási térbe, de a külső rendszer összes kommunikációt magában foglalja.

Az összekötő feladata az összes importálási és funkció exportálja a rendszer, és felszabadítja a fejlesztők annak megértése, hogyan kapcsolódni minden rendszer natív módon használatakor deklaratív kiépítés adatátalakítást testreszabása nem szükséges.

Importálja és exportálja csak fordulhat elő, ha ütemezve, lehetővé teszi a további szigetelése a változások a rendszer, mivel a módosítások nem automatikus terjesztése a csatlakoztatott adatforráshoz. Ezenkívül a fejlesztők is hozzon létre saját összekötőket szinte bármilyen adatforráshoz való kapcsolódáshoz.

## <a name="attribute-flow"></a>Attribútumfolyam
A metaverse az összevont nézet az összes csatlakoztatott identitások a szomszédos összekötőterek. A fenti ábrán Attribútumfolyam kapcsolaton keresztül a bejövő és kimenő áramlás nyílhegy írja le. Attribútumfolyam másolással vagy egy másikra az rendszer adatok átalakítása során a rendszer, és az összes attribútum adatfolyamok (bejövő vagy kimenő).

Attribútumfolyam esetén a kapcsolódási térbe és a metaverzum közötti kétirányúan belülre ütemezett szinkronizálási (teljes vagy különbözeti) műveletek.

Attribútumfolyam csak akkor következik be, ezeket a szinkronizálások futtatásakor. Szinkronizálási szabályok attribútumfolyamok vannak definiálva. Ezek lehetnek bejövő (ISR a fenti kép) vagy kimenő (OSR a fenti ábrán).

## <a name="connected-system"></a>Csatlakoztatott rendszer
Csatlakoztatott rendszer (más néven csatlakoztatott címtárhoz) van a távoli rendszer szinkronizálási csatlakozik-e az Azure AD Connect hivatkozó és adatok írásakor vagy olvasásakor identitás irányuló és onnan.

## <a name="connector-space"></a>Összekötőtér
Minden csatlakoztatott adatforrás egy jelenik meg az objektumokat és attribútumokat a kapcsolódási térbe szűrt részhalmazát.
Ez lehetővé teszi a szinkronizálási szolgáltatás kizárólag a helyi anélkül, hogy az objektumok szinkronizálásakor, lépjen kapcsolatba a távoli rendszer, és korlátozza az importáláshoz műveletet, és csak exportálja.

Ha az adatforrás és az összekötő képes sorolja fel azokat a változásokat (különbözeti importálás), majd a működési hatékonyság jelentősen megnő, csak a módosítás megtörténik a legutolsó lekérdezési ciklus óta. A kapcsolódási térbe insulates a csatlakoztatott adatforrás alapján, hogy az összekötő ütemezéséhez import és export automatikusan propagálása változások. A hozzáadott biztosítási biztosít, nyugodt maradhat afelől, tesztelési, megtekintés, vagy erősítse meg a következő frissítés közben.

## <a name="metaverse"></a>Metaverzum
A metaverse az összevont nézet az összes csatlakoztatott identitások a szomszédos összekötőterek.

Identitások egymáshoz kapcsolódó és hatóság különböző attribútumok Attribútumfolyam-megfeleltetéseket importálási keresztül van hozzárendelve, a központi metaverzum-objektum elkezdi összesített adatát több rendszerekből. Az az objektum Attribútumfolyam leképezései hajthat kimenő információt.

Objektumok jönnek létre, amikor egy mérvadó rendszer projektek azokat a metaverzumba. Amint az összes kapcsolat törlődik, a metaverzum-objektum törlődik.

A metaverzumban található objektumok közvetlenül nem szerkeszthető. Az objektum összes adatot kell hozzájárult, Attribútumfolyam keresztül. A metaverse tart fenn minden kapcsolódási térbe állandó összekötőt. Az összekötők újrakiértékelési nem igényel minden egyes szinkronizálás futtatása. Ez azt jelenti, hogy az Azure AD Connect szinkronizálási szolgáltatás nem minden alkalommal, amikor a megfelelő távoli objektum kereséséhez. Ezzel elkerülhető, hogy megakadályozza a módosítását az attribútumok, amelyek általában felelős válaszoknak az összekapcsolása a objektumok költséges ügynökök szükség.

Amikor, amelyek elérésű, korábban létező objektumot kell kezelni, új adatforrások felfedezésére, az Azure AD Connect szinkronizálási szolgáltatás illesztési szabály nevezett folyamat segítségével értékelje ki a lehetséges jelöltek, amellyel kapcsolatot.
Ha a kapcsolat létrejött, az értékelés indítást, és normál Attribútumfolyam távoli csatlakoztatott adatforrás és a metaverzum kerül sor.

## <a name="provisioning"></a>Kiépítés
Ha egy mérvadó forrás projektek a metaverzumba összekötő terület új objektumot új objektumot képviselő egy alsóbb rétegbeli csatlakoztatott adatforrás egy másik összekötő is létrehozható.

Ez az eredendően kapcsolatot hoz létre, és Attribútumfolyam kétirányúan lépne.

Amikor egy szabály határozza meg, hogy egy új összekötő terület objektumot kell létrehozni, kiépítés nevezik. Azonban ez a művelet csak akkor történik meg a kapcsolódási térbe belül, mert az nem jelenik meg a csatlakoztatott adatforráshoz való mindaddig, amíg az exportálás történik.

## <a name="additional-resources"></a>További források
* [Az Azure AD Connect-szinkronizálás: Szinkronizálási beállítások testreszabása](active-directory-aadconnectsync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
