---
title: Gyökér-vagy APEX-tartomány beléptetése meglévő bejárati ajtóra – Azure Portal
description: Megtudhatja, hogyan lehet bevezetni egy gyökér-vagy APEX-tartományt egy meglévő bevezető ajtajához a Azure Portal használatával.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: bb1042e15d4366923174996388eeb2fb99aef429
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184616"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Egy gyökér-vagy APEX-tartomány beléptetése az előtérben
Az Azure bevezető ajtaja CNAME rekordokat használ a tartomány tulajdonjogának ellenőrzéséhez az egyéni tartományok bevezetéséhez. Emellett a bevezető ajtó nem teszi elérhetővé az előtér-profilhoz társított előtérbeli IP-címet, így az APEX tartománya nem rendelhető hozzá IP-címhez, ha a cél az, hogy bemutassa az Azure-ba.

A DNS protokoll megakadályozza a CNAME rekordok hozzárendelését a zóna csúcsán. Ha például a tartománya `contoso.com`; a `somelabel.contoso.com`hoz CNAME rekordokat is létrehozhat. a CNAME nem hozható létre `contoso.com` önmagához. Ez a korlátozás olyan alkalmazás-tulajdonosoknak nyújt problémát, akik elosztott terhelésű alkalmazásokat vezetnek be az Azure bejárati ajtó mögött. Mivel a bejárati ajtó profiljának használata szükséges egy CNAME rekord létrehozásához, nem lehet az első ajtón lévő profilra mutatni a zóna csúcspontján.

Ezt a problémát a Azure DNS található alias-rekordok használatával oldják meg. A CNAME rekordoktól eltérően az alias-rekordok a zóna csúcsán jönnek létre, és az alkalmazás tulajdonosai használhatják a zóna csúcs-rekordját egy nyilvános végpontokkal rendelkező bejárati profilra. Az alkalmazás tulajdonosai a DNS-zónán belüli bármely más tartományhoz tartozó bejárati ajtós profilra mutatnak. A `contoso.com` és a `www.contoso.com` például ugyanarra az előtérben lévő profilra mutathat. 

A csúcspont vagy a gyökértartomány a bejárati ajtó profiljához való leképezése alapvetően a CNAME-összeolvasztás vagy a DNS-cizellálás megadását igényli, ami egy olyan mechanizmus, amelyben a DNS-szolgáltató rekurzív módon oldja fel a CNAME-bejegyzést, amíg az IP-címet nem talál. Ezt a funkciót a Azure DNS a bejárati ajtó végpontok számára támogatja. 

> [!NOTE]
> Vannak más DNS-szolgáltatók is, amelyek támogatják a CNAME-összeolvasztást vagy a DNS-t, azonban az Azure bejárati ajtó azt ajánlja, hogy az ügyfelek a tartományuk üzemeltetéséhez Azure DNS használjanak.

A Azure Portal használatával egy APEX-tartományt helyezhet üzembe a bevezető ajtaján, és engedélyezheti a HTTPS használatát, ha társítja azt egy SSL-leállításhoz szükséges tanúsítvánnyal. Az APEX-tartományokat root vagy Naked tartománynak is nevezzük.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Hozzon létre egy olyan alias rekordot, amely az előtérben lévő profilra mutat
> * A legfelső szintű tartomány hozzáadása a bejárati ajtóhoz
> * A HTTPS beállítása a legfelső szintű tartományban

> [!NOTE]
> Ehhez az oktatóanyaghoz létre kell hoznia egy előtérben létrehozott profilt. Tekintse meg az egyéb oktatóanyagokat, például a gyors üzembe helyezést [: hozzon létre egy](./quickstart-create-front-door.md) bevezető ajtót [, vagy hozzon létre egy bevezető ajtót http használatával HTTPS-átirányítás](./front-door-how-to-redirect-https.md)

## <a name="create-an-alias-record-for-zone-apex"></a>Alias-rekord létrehozása a zóna csúcsához

1. Nyissa meg **Azure DNS** konfigurációt a bekészíteni kívánt tartományhoz.
2. Hozza létre vagy szerkessze a zóna csúcspontjának rekordját.
3. Adja meg a rekord típusát _rekordként_ , majd válassza az _Igen_ lehetőséget az **alias-rekordhoz**. Az **alias típusát** az _Azure Resource_értékre kell beállítani.
4. Válassza ki azt az Azure-előfizetést, amelybe bezárta a bejárati profilt, majd válassza ki a bejárati ajtót az **Azure erőforrás** legördülő menüből.
5. A módosítások elküldéséhez kattintson **az OK** gombra.

    ![Alias record for zone apex](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. A fenti lépés egy zóna csúcspont-rekordot hoz létre, amely az előtérben lévő erőforrásra mutat, valamint egy CNAME rekordot, amely a "afdverify" (példa-`afdverify.contosonews.com`) nevű CNAME rekord hozzárendelésével `afdverify.<name>.azurefd.net`, amelyet a tartomány bevezetéséhez fog használni a bevezető ajtó profiljában.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Az egyéni tartomány előkészítése a bejárati ajtón

1. Az új egyéni tartomány hozzáadásához az előtér-Tervező lapon kattintson a "+" ikonra a frontend gazdagépek szakaszban.
2. Adja meg a gyökér vagy a csúcspont tartománynevét az egyéni Állomásnév mezőben, például `contosonews.com`.
3. Ha a tartományból a bejárati ajtóhoz tartozó CNAME-leképezést érvényesíti, kattintson a **Hozzáadás** gombra az egyéni tartomány hozzáadásához.
4. Kattintson a **Save (Mentés** ) gombra a módosítások elküldéséhez.

![Egyéni tartomány menü](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>HTTPS engedélyezése az egyéni tartományon

1. Kattintson a hozzáadott egyéni tartományra, és az **egyéni tartomány HTTPS**szakasza alatt módosítsa az állapotot **engedélyezve**értékre.
2. Válassza ki a **tanúsítványkezelő típusát** a _saját tanúsítvány használata_lehetőségnél.

> [!WARNING]
> Az előtérben felügyelt tanúsítvány-felügyeleti típus jelenleg nem támogatott az APEX-vagy a gyökérszintű tartományokban. Az egyetlen lehetőség, hogy a HTTPS-t egy csúcson vagy gyökértartományen engedélyezze a bejárati ajtón, a saját, Azure Key Vault üzemeltetett SSL-tanúsítványát használja.

3. Mielőtt továbblép a következő lépésre, győződjön meg arról, hogy a megfelelő engedélyekkel rendelkezik a bejárati ajtóhoz a kulcstartóhoz való hozzáféréshez.
4. Válasszon egy **Key Vault fiókot** a jelenlegi előfizetésből, majd válassza ki a megfelelő **titkos** és **titkos verziót** , hogy a megfelelő tanúsítványhoz lehessen rendelni.
5. Kattintson a **frissítés** elemre a kijelölés mentéséhez, majd kattintson a **Mentés**gombra.
6. Néhány perc elteltével kattintson a **frissítés** elemre, majd kattintson ismét az egyéni tartományra a tanúsítvány kiépítés állapotának megtekintéséhez. 

> [!WARNING]
> Győződjön meg arról, hogy megfelelő útválasztási szabályokat hozott létre az APEX-tartományhoz, vagy adja hozzá a tartományt a meglévő útválasztási szabályokhoz.

## <a name="next-steps"></a>Következő lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.