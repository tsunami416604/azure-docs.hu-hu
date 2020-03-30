---
title: Az Azure Active Directory-hozzáférés konfigurálása – Azure Blockchain szolgáltatás
description: Az Azure Blockchain szolgáltatás konfigurálása az Azure Active Directory-hozzáféréssel
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 682ab282036fcd592e66942d08a84cdce46d8915
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455872"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>Az Azure Active Directory-hozzáférés konfigurálása az Azure Blockchain szolgáltatáshoz

Ebben a cikkben megtudhatja, hogyan adhat hozzáférést, és csatlakozhat az Azure Blockchain szolgáltatás csomópontjaihoz az Azure Active Directory (Azure AD) felhasználói, csoport- vagy alkalmazásazonosítóihasználatával.

Az Azure AD felhőalapú identitáskezelést biztosít, és lehetővé teszi, hogy egyetlen identitást használjon a teljes vállalaton belül, és hozzáférjen az Azure-beli alkalmazásokhoz. Az Azure Blockchain Szolgáltatás integrálva van az Azure AD-vel, és olyan előnyöket kínál, mint az azonosító-összevonás, az egyszeri bejelentkezés és a többtényezős hitelesítés.

## <a name="prerequisites"></a>Előfeltételek

* [Blokklánc-tag létrehozása az Azure Portal használatával](create-member.md)

## <a name="grant-access"></a>Hozzáférés biztosítása

A hozzáférést a tag és a csomópont szintjén is megadhatja. A hozzáférési jogok tagszintű megadása viszont hozzáférést biztosít a tag minden csomópontjához.

### <a name="grant-member-level-access"></a>Tagszintű hozzáférés megadása

Hozzáférési engedély megadása tagszinten.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Keresse meg a **Hozzáférés-vezérlés (IAM) > add > Add szerepkör-hozzárendelés**.
1. Válassza ki a **Blockchain tagnode access (előzetes verzió)** szerepkört, és adja hozzá az Azure AD ID objektumot, amelyhez hozzáférést kíván adni. Az Azure AD ID objektum a következő lehet:

    | Azure AD-objektum | Példa |
    |-----------------|---------|
    | Azure AD-felhasználó   | `kim@contoso.onmicrosoft.com` |
    | Azure AD-csoport  | `sales@contoso.onmicrosoft.com` |
    | Alkalmazásazonosító  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Szerepkör-hozzárendelés hozzáadása](./media/configure-aad/add-role-assignment.png)

1. Kattintson a **Mentés** gombra.

### <a name="grant-node-level-access"></a>Csomópontszintű hozzáférés megadása

A csomópont szintjén hozzáférést adhat, ha a csomópont biztonságára navigál, és rákattint a hozzáférést megadó csomópontnévre.

Válassza ki a Blockchain tagnode access (előzetes verzió) szerepkört, és adja hozzá az Azure AD ID objektumot, amelyhez hozzáférést kíván adni.

További információ: [Configure Azure Blockchain Service transaction nodes](configure-transaction-nodes.md#azure-active-directory-access-control).

## <a name="connect-using-azure-blockchain-connector"></a>Csatlakozás az Azure Blockchain Connector használatával

Töltse le vagy klónozza az [Azure Blockchain Connectort a GitHubról.](https://github.com/Microsoft/azure-blockchain-connector/)

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

A rövid útmutató szakasz a **readme** az összekötő létrehozásához a forráskódból.

### <a name="connect-using-an-azure-ad-user-account"></a>Csatlakozás Azure AD-felhasználói fiókkal

1. Futtassa a következő parancsot az Azure AD felhasználói fiók használatával történő hitelesítéshez. Cserélje \<le a\> myAADDirectoryt egy Azure AD-tartományra. Például: `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Az Azure AD hitelesítő adatokat kér.
1. Jelentkezzen be a felhasználónevével és jelszavával.
1. Sikeres hitelesítés esetén a helyi proxy csatlakozik a blokklánc-csomóponthoz. Most már csatolhatja a Geth-ügyfél a helyi végponthoz.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Csatlakozás alkalmazásazonosító használatával

Számos alkalmazás hitelesíti az Azure AD-t egy Azure AD-felhasználói fiók helyett egy alkalmazásazonosító használatával.

Ha alkalmazásazonosítóval szeretne csatlakozni a csomóponthoz, cserélje le **az aadauthcode-ot** **az aadclient**ra.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Paraméter | Leírás |
|-----------|-------------|
| bérlő-azonosító | Azure AD tartomány, Például,`yourdomain.onmicrosoft.com`
| ügyfél-azonosító | A regisztrált alkalmazás ügyfélazonosítója az Azure AD-ben
| ügyféltitok | A regisztrált alkalmazás ügyféltka-tka az Azure AD-ben

Ha többet szeretne tudni arról, hogyan regisztrálhat egy alkalmazást az Azure AD-ben, olvassa [el a Hogyan: A portál használata azure AD-alkalmazás és az erőforrásokeléréséhez hozzáférő egyszerű szolgáltatás létrehozásáról](../../active-directory/develop/howto-create-service-principal-portal.md) című témakörben talál további információt.

### <a name="connect-a-mobile-device-or-text-browser"></a>Mobileszköz vagy szöveges böngésző csatlakoztatása

Egy mobileszköz vagy szöveges alapú böngésző, ahol az Azure AD-hitelesítés előugró kijelző nem lehetséges, az Azure AD létrehoz egy egyszeri jelszót. Másolhatja a jelszót, és folytathatja az Azure AD-hitelesítést egy másik környezetben.

A jelkód létrehozásához cserélje le az **aadauthcode-ot** **az aaddevice -ra.** Cserélje \<le a\> myAADDirectoryt egy Azure AD-tartományra. Például: `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>További lépések

Az Azure Blockchain Szolgáltatás adatbiztonságáról az Azure Blockchain Szolgáltatás biztonságáról az [Azure Blockchain Szolgáltatás biztonsága című](data-security.md)témakörben talál további információt.