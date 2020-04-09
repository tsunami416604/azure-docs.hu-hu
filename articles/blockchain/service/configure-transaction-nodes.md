---
title: Az Azure Blockchain szolgáltatás tranzakciós csomópontjainak konfigurálása
description: Az Azure Blockchain szolgáltatás tranzakciós csomópontjainak konfigurálása
ms.date: 11/20/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 8fa18496d0c1aa59beb55569e731967d5ebea427
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876929"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Az Azure Blockchain szolgáltatás tranzakciós csomópontjainak konfigurálása

A tranzakciócsomópontok segítségével blockchain-tranzakciókat küldhet az Azure Blockchain Service-nek egy nyilvános végponton keresztül. Az alapértelmezett tranzakciócsomópont a blokkláncon regisztrált Ethereum-fiók titkos kulcsát tartalmazza, ezért nem törölhető.

Az alapértelmezett tranzakciócsomópont részleteinek megtekintése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg az Azure Blockchain szolgáltatás tagját. Válassza **a Tranzakciócsomópontok lehetőséget.**

    ![Alapértelmezett tranzakciócsomópont kiválasztása](./media/configure-transaction-nodes/nodes.png)

    Az áttekintő részletek közé tartoznak a nyilvános végpontcímek és a nyilvános kulcs.

## <a name="create-transaction-node"></a>Tranzakciócsomópont létrehozása

Legfeljebb kilenc további tranzakciócsomópontot adhat hozzá a blokklánc-taghoz, összesen 10 tranzakciócsomóponthoz. Tranzakciócsomópontok hozzáadásával növelheti a méretezhetőséget, vagy terjesztheti a terhelést. Például lehet egy tranzakciós csomópont végpontja a különböző ügyfélalkalmazásokhoz.

Tranzakciócsomópont hozzáadása:

1. Az Azure Portalon keresse meg az Azure Blockchain szolgáltatás tagját, és válassza **a Tranzakciócsomópontok lehetőséget > hozzáadás.**
1. Töltse ki az új tranzakciócsomópont beállításait.

    ![Tranzakciócsomópont hozzáadása](./media/configure-transaction-nodes/add-node.png)

    | Beállítás | Leírás |
    |---------|-------------|
    | Név | Tranzakciócsomópont neve. A név a tranzakciócsomópont végpontjának DNS-címének létrehozásához használható. Például: `newnode-myblockchainmember.blockchain.azure.com`. A csomópont neve nem módosítható a létrehozása után. |
    | Jelszó | Állítson be erős jelszót. A jelszó segítségével érheti el a tranzakciócsomópont-végpontot alapfokú hitelesítéssel.

1. Kattintson a **Létrehozás** gombra.

    Egy új tranzakciócsomópont kiépítése körülbelül 10 percet vesz igénybe. További tranzakciócsomópontok költsége. A költségekről további információt az [Azure díjszabása](https://aka.ms/ABSPricing)című témakörben talál.

## <a name="endpoints"></a>Végpontok

A tranzakciócsomópontok egyedi DNS-névvel és nyilvános végponttal rendelkeznek.

A tranzakciócsomópont végpontjának részleteinek megtekintése:

1. Az Azure Portalon keresse meg az Azure Blockchain szolgáltatás egyik tagtranzakció-csomópontját, és válassza **az Áttekintés lehetőséget.**

    ![Végpontok](./media/configure-transaction-nodes/endpoints.png)

A tranzakciós csomópont végpontjai biztonságosak és hitelesítést igényelnek. Csatlakozhat egy tranzakcióvégponthoz az Azure AD-hitelesítés, https-alapfokú hitelesítés, és egy hozzáférési kulcs https-en vagy Websocket TLS-en keresztül használatával.

### <a name="azure-active-directory-access-control"></a>Az Azure Active Directory hozzáférés-vezérlése

Az Azure Blockchain Service tranzakciós végpontjai támogatják az Azure Active Directory (Azure AD) hitelesítését. Az Azure AD-felhasználó, -csoport és egyszerű szolgáltatás hozzáférést biztosíthat a végponthoz.

Az Azure AD hozzáférés-vezérlési szolgáltatásának megadása a végponthoz:

1. Az Azure Portalon keresse meg az Azure Blockchain szolgáltatás tagját, és válassza **a Tranzakciócsomópontok > hozzáférés-vezérlési (IAM) lehetőséget, > adja hozzá > szerepkör-hozzárendelés hozzáadása**lehetőséget.
1. Hozzon létre egy új szerepkör-hozzárendelést egy felhasználóhoz, csoporthoz vagy egyszerű szolgáltatáshoz (alkalmazásszerepkörökhöz).

    ![IAM-szerepkör hozzáadása](./media/configure-transaction-nodes/add-role.png)

    | Beállítás | Műveletek |
    |---------|-------------|
    | Szerepkör | Válassza **a Tulajdonos,** **a Közreműködő**vagy **az Olvasó**lehetőséget.
    | Hozzáférés hozzárendelése ehhez | Válassza az **Azure AD felhasználó, csoport vagy egyszerű szolgáltatás lehetőséget.**
    | Válassza ezt: | Keresse meg a hozzáadni kívánt felhasználót, csoportot vagy egyszerű szolgáltatást.

1. A **szerepkör-hozzárendelés** hozzáadásához válassza a Mentés lehetőséget.

Az Azure AD hozzáférés-vezérléssel kapcsolatos további információkért olvassa [el az Azure-erőforrásokhoz való hozzáférés kezelése az RBAC és az Azure Portal használatával című témakört.](../../role-based-access-control/role-assignments-portal.md)

Az Azure AD-hitelesítés használatával való csatlakozásról az [AAD-hitelesítéshasználatával a csomóponthoz való csatlakozás című](configure-aad.md)témakörben talál.

### <a name="basic-authentication"></a>Alapszintű hitelesítés

A HTTPS-alapfokú hitelesítéshez a felhasználónév és a jelszó hitelesítő adatai a kérelem HTTPS-fejlécében kerülnek átadásra a végpontnak.

Megtekintheti a tranzakciócsomópontok alapszintű hitelesítési végpontjának részleteit az Azure Portalon. Keresse meg az Azure Blockchain szolgáltatás egyik tagtranzakció-csomópontját, és válassza **az Alapfokú hitelesítés** lehetőséget a beállításokban.

![Alapszintű hitelesítés](./media/configure-transaction-nodes/basic.png)

A felhasználónév a csomópont neve, és nem módosítható.

Az URL-cím \<használatához\> cserélje le a jelszót a csomópont kiépítésekor beállított jelszóval. A jelszó frissítéséhez válassza **a Jelszó alaphelyzetbe állítása**lehetőséget.

### <a name="access-keys"></a>Elérési kulcs

A hozzáférési kulcs hitelesítése esetén a hozzáférési kulcs szerepel a végpont URL-címében. A tranzakciós csomópont kiépítésekor két hozzáférési kulcs jön létre. Mindkét hozzáférési kulcs használható a hitelesítéshez. A két billentyű lehetővé teszi a billentyűk módosítását és elforgatását.

Megtekintheti a tranzakciócsomópont hozzáférési kulcsának részleteit, és átmásolhatja a hozzáférési kulcsokat tartalmazó végpontcímeket. Keresse meg az Azure Blockchain szolgáltatás egyik tagtranzakció-csomópontját, és válassza **a Hozzáférési kulcsok** lehetőséget a beállításokban.

### <a name="firewall-rules"></a>Tűzfalszabályok

A tűzfalszabályok lehetővé teszik, hogy korlátozza azokat az IP-címeket, amelyek megpróbálhatják hitelesíteni magukat a tranzakciós csomóponton.  Ha nincs tűzfalszabály konfigurálva a tranzakciócsomóponthoz, akkor azt egyik fél sem érheti el.  

Egy tranzakciócsomópont tűzfalszabályainak megtekintéséhez keresse meg az Azure Blockchain szolgáltatás egyik tagtranzakció-csomópontját, és válassza **a tűzfalszabályokat** a beállításokban.

Tűzfalszabályokat úgy adhat hozzá, hogy megad egy szabálynevet, elindítja az IP-címet és egy végződő IP-címet a **tűzfalszabályok** rácsában.

![Tűzfalszabályok](./media/configure-transaction-nodes/firewall-rules.png)

Az engedélyezéshez:

* **Egyetlen IP-cím:** Adja meg ugyanazt az IP-címet a kezdő és záró IP-címekhez.
* **IP-címtartomány:** Konfigurálja a kezdő és záró IP-címtartományt. Ha például egy 10.221.34.0-tól 10.221.34.255-ig kezdődő és 10.221.34.255-tel kezdődő tartomány lehetővé teszi a teljes 10.221.34.xxx alhálózatot.
* **Az összes IP-cím engedélyezése:** Állítsa be a kezdő IP-címet 0.0.0.0-ra és a záró IP-címet 255.255.255.255-re.

## <a name="connection-strings"></a>Kapcsolati sztringek

A tranzakciós csomópont csatlakozási karakterláncszint-szintaxisa egyszerű hitelesítéshez vagy hozzáférési kulcsok hoz. A kapcsolati karakterláncok, beleértve a HTTPS-en és websocketeken keresztüli hozzáférési kulcsokat is, rendelkezésre állnak.

Megtekintheti a tranzakciócsomópont kapcsolati karakterláncait, és átmásolhatja a végpontcímeket. Keresse meg az Azure Blockchain szolgáltatás egyik tagtranzakció-csomópontját, és válassza **a Kapcsolati karakterláncok** lehetőséget a beállításokban.

![Kapcsolati sztringek](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Mintakód

Mintakód áll rendelkezésre, hogy gyorsan lehetővé tegye a tranzakciós csomóponthoz való csatlakozást a Web3, a Nethereum, a Web3js és a Szarvasgomba segítségével.

Megtekintheti a tranzakciócsomópontok mintakapcsolati kódját, és átmásolhatja a népszerű fejlesztői eszközökkel való használatra. Nyissa meg az Azure Blockchain szolgáltatás egyik tagtranzakció-csomópontját, és válassza **a Mintakód lehetőséget** a beállításokban.

A használni kívánt kódminta megtekintéséhez válassza a Web3, nethereum, szarvasgomba vagy Web3j lapot.

![Mintakód](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tranzakciócsomópontok konfigurálása az Azure CLI használatával](manage-cli.md)
