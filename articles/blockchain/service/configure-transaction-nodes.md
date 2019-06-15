---
title: Az Azure Blockchain-szolgáltatás tranzakció csomópontok konfigurálása
description: Az Azure Blockchain-szolgáltatás tranzakció csomópontok konfigurálása
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: dffeb81ae1eb244c38639a1241c0581e6fcdf94a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027960"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Az Azure Blockchain-szolgáltatás tranzakció csomópontok konfigurálása

Együttműködhet az Azure Blockchain-szolgáltatás, mégis így keresztül csatlakozik a tranzakció egy vagy több csomópontján az a blockchain tag.  Annak érdekében, hogy a tranzakció csomópontok kommunikáljanak, szüksége lesz a csomópontok hozzáférés konfigurálásához.

## <a name="prerequisites"></a>Előfeltételek

* [Az Azure Blockchain tag létrehozása](create-member.md)

## <a name="transaction-node-overview"></a>Tranzakció csomópont áttekintése

Tranzakció csomópontok blockchain tranzakciók küldeni az Azure Blockchain-szolgáltatás egy nyilvános végpontot szolgálnak. Az alapértelmezett tranzakció csomópont tartalmazza az Ethereum-fiók regisztrálva van a blockchain a titkos kulcsot, és emiatt nem lehet törölni.

Az alapértelmezett tranzakció csomópont részleteinek megtekintéséhez:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg az Azure Blockchain-szolgáltatás tag. Válassza ki **tranzakció csomópontok**.

    ![Válassza ki az alapértelmezett tranzakció csomópont](./media/configure-transaction-nodes/nodes.png)

    Áttekintése részleteinek nyilvános végpontcímeket és a nyilvános kulcsot tartalmazza.

## <a name="create-transaction-node"></a>Tranzakció csomópont létrehozása

Adhat hozzá további tranzakciós kilenc csomópontokat akár a blockchain tag, összesen 10 tranzakció csomópontok. Tranzakció csomópontok hozzáadásával bővítése érdekében, vagy a terhelés elosztása. Például lehet egy másik ügyfélalkalmazások tranzakció csomópont végpontot.

Egy tranzakció csomópont hozzáadása:

1. Az Azure Portalon keresse meg az Azure Blockchain-szolgáltatás tagot, és válassza **tranzakció csomópontok > Hozzáadás**.
1. Végezze el az új tranzakció csomópont beállításait.

    ![Tranzakció csomópont hozzáadása](./media/configure-transaction-nodes/add-node.png)

    | Beállítás | Leírás |
    |---------|-------------|
    | Name (Név) | Tranzakció csomópont neve. A név szolgál a DNS-címe, a tranzakciós csomópont-végpont létrehozása. Például: `newnode-myblockchainmember.blockchain.azure.com`. A csomópont neve a létrehozásuk után nem módosítható. |
    | Jelszó | Állítson be egy erős jelszót. Használja a jelszót egyszerű hitelesítést a tranzakció csomópont-végpontot.

1. Kattintson a **Létrehozás** gombra.

    Új tranzakció csomópont kiépítése körülbelül 10 percet vesz igénybe. További tranzakciós csomópontok használatáért számítunk fel. A költségek további információkért lásd: [az Azure díjszabása](https://aka.ms/ABSPricing).

## <a name="endpoints"></a>Végpontok

Tranzakció csomópontok egy egyedi DNS-nevet és a nyilvános végpontokkal rendelkezik.

Egy tranzakció csomópont végpont részleteinek megtekintéséhez:

1. Az Azure Portalon keresse meg az Azure Blockchain-szolgáltatás tag tranzakció csomópontok egyikére, és válassza **áttekintése**.

    ![Végpontok](./media/configure-transaction-nodes/endpoints.png)

Tranzakció csomópont végpontok biztonságosak, és a hitelesítésre van szükség. A tranzakció végpont használata az Azure AD-hitelesítés, alapszintű HTTPS-hitelesítést, és a hozzáférési kulcs használatával a HTTPS vagy a Websocket SSL-en keresztül csatlakozhat.

### <a name="azure-active-directory-access-control"></a>Azure Active Directory hozzáférés-vezérlés

Az Azure Blockchain tranzakció csomópont Szolgáltatásvégpontok Azure Active Directory (Azure AD-) hitelesítést támogatja. Az Azure AD-felhasználói, csoport- és szolgáltatásnév hozzáférhessen a végpontra megadásához.

Biztosítania az Azure AD-hozzáférés-vezérlés a végpontra:

1. Az Azure Portalon keresse meg az Azure Blockchain-szolgáltatás tagot, és válassza ki **tranzakció csomópontok > hozzáférés-vezérlés (IAM) > Hozzáadás > szerepkör-hozzárendelés hozzáadása**.
1. Hozzon létre egy új szerepkör-hozzárendelés egy felhasználó, csoport vagy egyszerű szolgáltatás (alkalmazás-szerepkörök).

    ![IAM-szerepkör hozzáadása](./media/configure-transaction-nodes/add-role.png)

    | Beállítás | Műveletek |
    |---------|-------------|
    | Szerepkör | Válassza ki **tulajdonosa**, **közreműködői**, vagy **olvasó**.
    | Hozzáférés hozzárendelése | Válassza ki **az Azure AD-felhasználó, csoport vagy szolgáltatásnév**.
    | Válassza ezt: | Keresse meg a felhasználó, csoport vagy egyszerű szolgáltatást szeretne hozzáadni.

1. Válassza ki **mentése** a szerepkör-hozzárendelés hozzáadása.

A hozzáférés-vezérlés az Azure AD további információkért lásd: [rbac-RÓL és az Azure portal segítségével Azure-erőforrásokhoz való hozzáférés kezelése](../../role-based-access-control/role-assignments-portal.md)

A csatlakozás az Azure AD-hitelesítés használatával kapcsolatos részletekért lásd: [az AAD-hitelesítéssel a csomóponthoz csatlakozás](configure-aad.md).

### <a name="basic-authentication"></a>Alapszintű hitelesítés

HTTPS alapszintű hitelesítés felhasználói nevét és jelszavát a hitelesítő adatok továbbítódnak a HTTPS a kérelem fejlécében a végpontot.

Egy tranzakció csomópont az egyszerű hitelesítés részletei az Azure Portalon tekintheti meg. Keresse meg az Azure Blockchain-szolgáltatás tag tranzakció csomópontjának egyikét, és válassza **alapszintű hitelesítés** beállításaiban.

![Alapszintű hitelesítés](./media/configure-transaction-nodes/basic.png)

A felhasználó neve a csomópont nevét, és nem módosítható.

Az URL-cím használatához cserélje le a \<jelszó\> jelszót beállítani, amikor a csomópont lett üzembe helyezve. A jelszó kiválasztásával frissítheti **jelszó alaphelyzetbe állítása**.

### <a name="access-keys"></a>Elérési kulcs

A hozzáférési kulcs alapú hitelesítés esetén a hozzáférési kulcsot a végponti URL-cím szerepel. A tranzakció csomópontra helyezésekor két kulcsot jönnek létre. Mindkét hozzáférési kulcsot a hitelesítéshez használható. Két kulcs engedélyezése módosítja, és kulcsainak rotálása.

Egy tranzakció csomópont hozzáférési kulcs részleteinek megtekintése, és másolja a hozzáférési kulcsokat tartalmazó végpontcímeket. Keresse meg az Azure Blockchain-szolgáltatás tag tranzakció csomópontjának egyikét, és válassza **Tárelérési kulcsok** beállításaiban.

### <a name="firewall-rules"></a>Tűzfalszabályok

Tűzfalszabályok lehetővé teszik, hogy korlátozza az IP-címeket is megpróbálnak hitelesíteni a tranzakció csomópontra.  Ha nincs tűzfalszabály konfigurálva van a tranzakciós csomópontjához, nem érhető el bármelyik fél.  

Tranzakció a csomópont a tűzfalszabályok megtekintéséhez keresse meg az Azure Blockchain-szolgáltatás tagcsomópontok tranzakció egyikét, és válassza **tűzfalszabályok** beállításaiban.

Szabály név beírásával is hozzáadhat tűzfalszabályokat, kezdő IP címe, és egy záró IP-cím az az **tűzfalszabályok** rács.

![Tűzfalszabályok](./media/configure-transaction-nodes/firewall-rules.png)

Engedélyezése:

* **Egyetlen IP-cím:** Azonos IP-címét a kezdő és záró IP-címek konfigurálásához.
* **IP-címtartomány:** Állítsa be a kezdő és záró IP-címtartományt. Például: 10.221.34.0 kezdési és befejezési 10.221.34.255, számos lehetővé teszik a teljes 10.221.34.xxx alhálózat.
* **Az összes IP-címek engedélyezése:** Konfigurálja a 0.0.0.0 kezdő IP-cím és a 255.255.255.255 a záró IP-cím.

## <a name="connection-strings"></a>Kapcsolati sztringek

Kapcsolati karakterlánc szintaxisát a tranzakció csomópont biztosítunk alapszintű hitelesítés vagy a hozzáférési kulcsok használatával. Kapcsolati karakterláncok, többek között a tárelérési kulcsok HTTPS és WebSockets keresztül biztosított.

Egy tranzakció csomópont kapcsolati karakterláncok megtekintése, és másolja a szolgáltatásvégpont-címeit. Keresse meg az Azure Blockchain-szolgáltatás tag tranzakció csomópontjának egyikét, és válassza **kapcsolati karakterláncok** beállításaiban.

![Kapcsolati sztringek](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Mintakód

Mintakód, amelyekkel gyorsan kapcsolódni a tranzakció csomóponthoz weben 3, Nethereum, Web3js és Truffle keresztül biztosított.

Egy tranzakció csomóponti kapcsolat kódmintát, és másolja ki, hogy használja a népszerű fejlesztői eszközökkel. Keresse meg az Azure Blockchain-szolgáltatás tag tranzakció csomópontjának egyikét, és válassza **mintakód** beállításaiban.

Válassza ki a használni kívánt kódminta megtekintése a weben 3 vagy Nethereum fülre.

![Mintakód](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure CLI-vel tranzakció csomópontok konfigurálása](manage-cli.md)
