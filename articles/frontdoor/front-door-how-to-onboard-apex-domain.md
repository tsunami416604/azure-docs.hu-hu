---
title: Gyökér- vagy csúcstartomány fedélzetre ágyazása egy meglévő bejárati ajtóhoz – Azure portal
description: Ismerje meg, hogyan lehet egy gyökér- vagy csúcstartományt egy meglévő bejárati ajtóra beszállni az Azure Portal használatával.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 4b74338f22a82d76ef13126ee0862b841bd89a99
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878884"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Gyökér- vagy csúcstartomány alaplapja a bejárati ajtón
Az Azure Front Door CNAME rekordok használatával ellenőrzi a tartomány tulajdonjogát az egyéni tartományok bevezetéséhez. A Bejárati ajtó nem teszi elérhetővé az előtér-profilhoz társított előtér-IP-címet, így nem tudja leképezni a csúcstartományt egy IP-címre, ha az a szándék, hogy az Azure Front Door-ra vegye fel.

A DNS-protokoll megakadályozza a CNAME rekordok zónacsúcson történő hozzárendelését. Például, ha a `contoso.com`domain; Létrehozhat CNAME rekordokat `somelabel.contoso.com`a; de nem hozhat létre cname-t `contoso.com` magának. Ez a korlátozás problémát jelent az olyan alkalmazástulajdonosok számára, akik az Azure Bejárati ajtajámögött rendelkeznek terheléselosztással rendelkező alkalmazásokkal. Mivel a Bejárati ajtó profil használatához CNAME rekord létrehozása szükséges, a zóna csúcsáról nem lehet a Bejárati ajtó profilra mutatni.

A probléma az Azure DNS aliasrekordjaival oldható meg. A CNAME rekordokkal ellentétben az aliasrekordok a zóna csúcsán jönnek létre, és az alkalmazástulajdonosok használhatják a zónacsúcsrekordjukat egy nyilvános végpontokkal tartalmazó bejárati ajtó profilra. Az alkalmazástulajdonosok ugyanarra a Bejárati ajtó-profilra mutatnak, amelyet a DNS-zónájuk bármely más tartományához használnak. Például, `contoso.com` `www.contoso.com` és rámutathat ugyanarra a bejárati ajtó profilra. 

A csúcs- vagy gyökértartomány hozzárendelése a Bejárati ajtó profiljához alapvetően megköveteli a CNAME összeolvasztását vagy a DNS üldözését, amely egy olyan mechanizmus, amelyben a DNS-szolgáltatóban rekurzívan feloldja a CNAME bejegyzést, amíg el nem éri az IP-címet. Ezt a funkciót az Azure DNS a bejárati ajtó végpontjai támogatja. 

> [!NOTE]
> Vannak más DNS-szolgáltatók is, amelyek támogatják a CNAME összeolvasztását vagy a DNS-üldözést, azonban az Azure Front Door azt javasolja, hogy az Azure DNS-t használják ügyfelei számára a tartományok üzemeltetéséhez.

Az Azure Portal segítségével a bejárati ajtaján egy csúcstartományt hozhat létre, és https-t engedélyezhet rajta, ha a TLS-megszüntetési tanúsítványt társítja. Apex domainis nevezik root vagy meztelen domain.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A bejárati ajtó profiljára mutató aliasrekord létrehozása
> * A gyökértartomány hozzáadása a bejárati ajtóhoz
> * HTTPS beállítása a gyökértartományon

> [!NOTE]
> Ez az oktatóanyag megköveteli, hogy már létrehozott egy Bejárati ajtó profilt. Tekintse át a többi oktatóanyagot, [például a rövid útmutatót: Hozzon létre egy bejárati ajtót,](./quickstart-create-front-door.md) vagy [hozzon létre egy bejárati ajtót HTTP-vel HTTPS-átirányítással](./front-door-how-to-redirect-https.md) az első lépésekhez.

## <a name="create-an-alias-record-for-zone-apex"></a>Aliasrekord létrehozása a zónacsúcshoz

1. Nyissa meg az **Azure DNS-konfigurációját** a berakandó tartományhoz.
2. A zónacsúcs rekordjának létrehozása vagy szerkesztése.
3. Jelölje ki a **rekordtípust** _A_ rekordként, majd válassza az _Igen_ az **Alias rekordkészlethez**lehetőséget. **Az aliastípust** _az Azure-erőforrásra_kell állítani.
4. Válassza ki azt az Azure-előfizetést, ahol a Bejárati ajtajának profilja található, majd válassza ki a Bejárati ajtajának erőforrást az **Azure-erőforrás** legördülő menüből.
5. A módosítások elküldéséhez kattintson az **OK** gombra.

    ![Aliasrekord a zóna csúcsa](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. A fenti lépés létrehoz egy zóna csúcsrekordot, amely a bejárati ajtó erőforrásra mutat, valamint `afdverify.contosonews.com`egy `afdverify.<name>.azurefd.net` CNAME rekordleképezést (például - ), amelyhez a rendszer a tartomány bevezetéséhez lesz használható a Bejárati ajtó profilban.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Az egyéni tartomány bedeszkázása a bejárati ajtón

1. Új egyéni tartomány hozzáadásához kattintson a Frontend hosts szakasz "+" ikonjára a Bejárati ablak tervezője lapon.
2. Írja be a gyökér- vagy csúcstartománynevet az `contosonews.com`egyéni állomásnév mezőbe, például .
3. Miután a CNAME leképezés a tartományból a bejárati ajtó, kattintson a **Hozzáadás** az egyéni tartomány hozzáadásához.
4. A módosítások elküldéséhez kattintson a **Mentés** gombra.

![Egyéni tartomány menü](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>HTTPS engedélyezése az egyéni tartományban

1. Kattintson a hozzáadott egyéni tartományra, és az **Egyéni tartomány HTTPS**szakaszában módosítsa az állapotot **Engedélyezve értékre.**
2. A _"Saját tanúsítvány használata"_ lehetőséget válassza a **Tanúsítványkezelés típust.**

> [!WARNING]
> A Bejárati ajtó által felügyelt tanúsítványkezelési típus jelenleg nem támogatott a csúcs- vagy gyökértartományokban. Az egyetlen lehetőség, amely a HTTPS engedélyezése egy csúcs- vagy gyökértartomány ban bejárati ajtaját a saját egyéni TLS/SSL tanúsítvány azure Key Vault üzemeltetett.

3. Győződjön meg arról, hogy a következő lépés hez való továbblépés előtt győződjön meg arról, hogy rendelkezik a bejárati ajtó megfelelő engedélyekkel a felhasználói felületen jelzett kulcstároló eléréséhez.
4. Válasszon egy **Key Vault-fiókot** az aktuális előfizetéséből, majd válassza ki a megfelelő **titkos** és **titkos verziót** a megfelelő tanúsítványhoz való hozzárendeléshez.
5. A kijelölés mentéséhez kattintson a **Frissítés** gombra, majd a **Mentés gombra.**
6. Kattintson **a Frissítés** néhány perc múlva, majd kattintson az egyéni tartomány ismét a tanúsítványkiépítés előrehaladásának megtekintéséhez. 

> [!WARNING]
> Győződjön meg arról, hogy megfelelő útválasztási szabályokat hozott létre a csúcstartományhoz, vagy hozzáadta a tartományt a meglévő útválasztási szabályokhoz.

## <a name="next-steps"></a>További lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.