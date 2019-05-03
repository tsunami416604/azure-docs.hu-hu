---
title: Az Azure Active Directory-hozzáférés konfigurálása
description: Az Azure Blockchain-szolgáltatás konfigurálása az Azure Active Directory-hozzáférés
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: seal
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: 616e342f1d52179c40c225c5dafc9de13ce85e06
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028215"
---
# <a name="how-to-configure-azure-active-directory-access"></a>Az Azure Active Directory-hozzáférés konfigurálása

Ebből a cikkből megismerheti, hogyan csatlakozhat az Azure Blockchain-szolgáltatás csomópontokhoz használatával az Azure Active Directory (Azure AD) felhasználó, csoport vagy Alkalmazásazonosítók és hozzáférést biztosít.

Az Azure AD felhőalapú identitáskezelést nyújt, és lehetővé teszi, hogy egyetlen identitással egy teljes vállalati és a hozzáférési alkalmazásokban az Azure-ban. Az Azure Blockchain-szolgáltatás az Azure ad-vel integrált, és előnyökkel, például az azonosító összevonási, egyszeri bejelentkezést és a multi-factor Authentication hitelesítést.

## <a name="prerequisites"></a>Előfeltételek

* [Az Azure portal használatával a blockchain-tag létrehozása](create-member.md)

## <a name="grant-access"></a>Hozzáférés biztosítása

A tag szintje és a csomópont szintjén egyaránt lehet hozzáférést. A tag szintjén hozzáférési jogokat adjon viszont minden csomópont alatt a tag hozzáférést.

### <a name="grant-member-level-access"></a>Tag szintű hozzáférés biztosítása

A hozzáférési engedélyt a tag szintjén.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Navigáljon a **hozzáférés-vezérlés (IAM) > Hozzáadás > szerepkör-hozzárendelés hozzáadása**.
1. Válassza ki a **Blockchain tag csomópont hozzáférés (előzetes verzió)** szerepkört, és adja hozzá az Azure AD-azonosítója objektum kíván hozzáférést biztosítani. Az Azure AD-Azonosítójú objektum lehet:

    | Az Azure AD-objektum | Példa |
    |-----------------|---------|
    | Azure AD-felhasználó   | `frank@contoso.onmicrosoft.com` |
    | Az Azure AD-csoport  | `sales@contoso.onmicrosoft.com` |
    | Alkalmazásazonosító  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Szerepkör-hozzárendelés hozzáadása](./media/configure-aad/add-role-assignment.png)

1. Kattintson a **Mentés** gombra.

### <a name="grant-node-level-access"></a>Csomópont szintű hozzáférés biztosítása

1. Hozzáférést biztosít a csomópont szintjén csomópont biztonsági megnyitásával, és kattintson a csomópont nevét, amelyet szeretne hozzáférést biztosítani a.
1. Válassza ki a Blockchain tag csomópont hozzáférés (előzetes verzió) szerepkört, és adja hozzá az Azure AD-azonosítója objektum kíván hozzáférést biztosítani. 

## <a name="connect-using-azure-blockchain-connector"></a>Csatlakozás az Azure Blockchain-összekötő használatával

Töltse le vagy klónozza a [Azure Blockchain-összekötő a Githubról](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

A következő szakasz a rövid útmutatóban a **információs** hozhat létre az összekötő a forráskódban.

### <a name="connect-using-an-azure-ad-user-account"></a>Csatlakozás az Azure AD felhasználói fiók használatával

1. Futtassa a következő parancsot egy Azure AD felhasználói fiók használatával történő hitelesítéshez. Cserélje le \<myAADDirectory\> az Azure AD-tartomány. Például: `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Az Azure AD hitelesítő adatok megadását kéri.
1. Jelentkezzen be a felhasználónevet és jelszót.
1. Sikeres hitelesítést követően a helyi proxy a blockchain-csomópont csatlakozik. Most már a Geth ügyfél, és a helyi végpont is csatlakoztatható.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Csatlakozás egy Alkalmazásazonosítót használatával

Számos alkalmazás hitelesítése az Azure AD szolgáltatással egy Alkalmazásazonosítót az Azure AD felhasználói fiók helyett.

Ha csatlakozni szeretne a csomópont-alkalmazás azonosítójával, cserélje le **aadauthcode** a **aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Paraméter | Leírás |
|-----------|-------------|
| tenant-id | Az Azure AD-tartományát, például `yourdomain.onmicrosoft.com`
| ügyfél-azonosító | Az Azure AD-ben regisztrált alkalmazás Ügyfélazonosítója
| client-secret | Az Azure AD-ben regisztrált alkalmazás titkos Ügyfélkód

Hogyan kell regisztrálni egy alkalmazást az Azure AD további információkért lásd: [hogyan: Az Azure AD-alkalmazás és -erőforrások elérésére képes egyszerű szolgáltatás létrehozása a portál használatával](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Csatlakozás egy mobil eszköz vagy szöveges böngészőben

A mobileszközök és szöveges böngésző, ha az Azure AD-hitelesítési előugró ablakban nem lehetséges az Azure AD egyszeri PIN-kódot generál. Másolja a PIN-kódot, és a egy másik környezetben az Azure AD-hitelesítés folytatásához.

Cserélje le a jelszó generálásához, **aadauthcode** a **aaddevice**. Cserélje le \<myAADDirectory\> az Azure AD-tartomány. Például: `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>További lépések

Az Azure Blockchain-szolgáltatás által nyújtott adatbiztonság kapcsolatos további információkért lásd:

> [!div class="nextstepaction"]
> [Az Azure Blockchain-szolgáltatás biztonsága](data-security.md)